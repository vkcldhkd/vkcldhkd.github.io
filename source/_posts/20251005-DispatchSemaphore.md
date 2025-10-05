---
title: DispatchSemaphore
date: 2025-10-05 23:37:02
category:
- iOS
tags:
- DispatchSemaphore
---

# DispatchSemaphore:

## 개념 & 동작 원리

- **카운팅 세마포어**: 내부에 정수 카운터(permit)가 있고, `wait()`는 **카운터 1 감소**, `signal()`은 **카운터 1 증가**.
- **초기값**: `DispatchSemaphore(value: N)`에서 `N`은 **동시에 허용할 작업 수**를 의미  
  - `N = 1` → 사실상 **뮤텍스(직렬 실행)**처럼 동작
  - `N > 1` → **최대 N개 병렬 실행** 허용
- **대기/해제**:
  - `wait()` 시 카운터가 **0 이하**면 **해제(signal)**될 때까지 **블로킹**(대기)
  - `signal()`이 호출되면 대기 중인 스레드 **하나**가 깨워짐 (FIFO 보장 X, **공정성 비보장**)

> ⚠️ 세마포어는 **블로킹 기반**이므로, **메인 스레드에서** `**wait()**` **금지**. 항상 **백그라운드 큐**에서 사용하세요.

---

## 언제 쓰는게 좋을까?

1. **동시 요청 수 제한** (예: 네트워크 요청을 한 번에 3개만)
2. **순차 실행 보장** (`value: 1`로 직렬화)
3. **공유 자원 보호** (DB/파일/소켓 등 임계 구역 보호)
4. **비동기 콜백을 동기 흐름으로 조합**할 때 (단, 메인 스레드 금지)

> iOS 12 지원이 필요할 때 특히 유용하다. iOS 15+에서는 `async/await`(또는 `OperationQueue`)가 더 자연스럽다고한다.

---

## 핵심 패턴별 코드 스니펫

### 1. 동시 네트워크 요청 **3개로 제한** (실패율 ↓ 안정성 ↑)

```swift
let semaphore = DispatchSemaphore(value: 3) // 동시에 3개까지만 허용
let queue = DispatchQueue(label: "com.example.request", qos: .userInitiated, attributes: .concurrent)

for request in requests {
    queue.async {
        semaphore.wait() // 슬롯 확보
        self.session.dataTask(with: request) { data, response, error in
            defer { semaphore.signal() } // 반드시 해제
            if let error = error {
                print("❌ error:", error)
            } else {
                print("✅ success:", (response as? HTTPURLResponse)?.statusCode ?? 0)
            }
        }.resume()
    }
}
```

**효과**: 서버/OS 과부하 방지 → `connection reset`, `too many open files` 같은 실패 감소.

---

### 2. **순차 실행** (value = 1)

```swift
let serialSemaphore = DispatchSemaphore(value: 1)

func sendSequentially(_ requests: [URLRequest]) {
    for req in requests {
        DispatchQueue.global().async {
            serialSemaphore.wait()
            self.session.dataTask(with: req) { _, _, _ in
                defer { serialSemaphore.signal() }
                // 완료 후 자동으로 다음 요청이 시작됨
            }.resume()
        }
    }
}
```

> 동시성은 1개로 제한되어 **요청 순서가 보장**됩니다.

---

### 3. **재시도(Exponential Backoff) + 동시성 제한** 결합

```swift
func shouldRetry(_ code: URLError.Code) -> Bool {
    switch code {
    case .timedOut, .cannotFindHost, .cannotConnectToHost,
         .networkConnectionLost, .dnsLookupFailed,
         .notConnectedToInternet, .internationalRoamingOff,
         .callIsActive, .dataNotAllowed:
        return true
    default: return false
    }
}

func performRequest(_ request: URLRequest,
                    retryCount: Int = 3,
                    delay: TimeInterval = 1,
                    completion: @escaping (Result<Data, Error>) -> Void) {
    let task = self.session.dataTask(with: request) { data, response, error in
        // HTTP 상태코드도 체크
        if let http = response as? HTTPURLResponse, !(200..<300).contains(http.statusCode) {
            let err = NSError(domain: "HTTPError", code: http.statusCode, userInfo: nil)
            return completion(.failure(err))
        }
        if let error = error as? URLError, retryCount > 0, shouldRetry(error.code) {
            let nextDelay = delay * 2
            DispatchQueue.global().asyncAfter(deadline: .now() + nextDelay) {
                self.performRequest(request, retryCount: retryCount - 1, delay: nextDelay, completion: completion)
            }
            return
        }
        if let error = error { completion(.failure(error)) }
        else { completion(.success(data ?? Data())) }
    }
    task.resume()
}

let sem = DispatchSemaphore(value: 3) // 동시 3개 제한
for req in requests {
    DispatchQueue.global().async {
        sem.wait()
        self.performRequest(req) { result in
            defer { sem.signal() }
            print("Result:", result)
        }
    }
}
```

> **네트워크 품질에 민감한 환경**(모바일)에서 실패율을 크게 낮출 수 있는 조합!

---

### 4 **임계 구역 보호** (간단 뮤텍스처럼)

```swift
final class SafeBox<T> {
    private var value: T
    private let sem = DispatchSemaphore(value: 1)

    init(_ v: T) { self.value = v }

    func read() -> T {
        sem.wait(); defer { sem.signal() }
        return value
    }

    func write(_ newValue: T) {
        sem.wait(); value = newValue; sem.signal()
    }
}
```

---

## 설정 팁: URLSession과 함께 쓰면 좋은 세팅

```swift
let config = URLSessionConfiguration.default
config.waitsForConnectivity = true           // 연결 복구 시 자동 재개
config.timeoutIntervalForRequest = 30
config.timeoutIntervalForResource = 60
config.httpMaximumConnectionsPerHost = 3     // 호스트당 동시 연결 수 제한
config.requestCachePolicy = .reloadIgnoringLocalCacheData
let session = URLSession(configuration: config)
```

> `waitsForConnectivity = true`는 **일시 연결 끊김 시 자동 대기 후 재시도**해 실패를 줄일 수 있다.

---

## 주의할 점 & 안티 패턴

1. **메인 스레드에서** `**wait()**` **금지** → UI 프리즈/데드락 위험
2. **항상** `**signal()**` **보장** (`defer { signal() }` 권장)
3. **같은 직렬 큐 안에서 다시** `**wait()`**하면 데드락 가능
4. **무한 대기 방지**: 필요시 `wait(timeout:)` 사용
   ```swift
   if sem.wait(timeout: .now() + 5) == .timedOut {
       print("⏱️ timeout")
   }
   ```
5. **공정성(FIFO) 보장 안 됨** → “순서 보장”이 절대적이면 Serial Queue가 더 적합
6. **복잡한 워크플로 제어**에는 `OperationQueue`가 더 가독성 좋을 수 있음
7. **iOS 15+**: 가능하면 `async/await` + `AsyncSemaphore`(직접 구현) 또는 `OperationQueue` 고려

---

## 다른 도구와 비교

| 도구 | 목적 | 특징 |
|-----|------|------|
| **DispatchSemaphore** | 동시성 제한/동기화 | 블로킹, 가볍고 단순, 신중히 사용 필요 |
| **DispatchGroup** | 여러 비동기 작업 완료 대기 | **동시성 제한 기능 없음** |
| **NSLock** | 임계 구역 보호 | 뮤텍스, 카운팅/슬롯 개념 없음 |
| **OperationQueue** | 작업 스케줄링/의존성/취소 | `maxConcurrentOperationCount`로 제한, 가독성 좋음 |
| **Serial DispatchQueue** | 순차 실행 | 가장 단순한 순서 보장, 동시성 제한(>1) 불가 |
| **async/await** | 구조적 동시성 | iOS 15+, 가독성 최고, 블로킹 없음 |

---

## 요약

- `DispatchSemaphore`는 **간단한 코드로 동시성 제어**를 제공하며,  특히 **대량 네트워크 요청의 실패율을 낮추는 데 매우 효과적**
- 단, **메인 스레드 블로킹 금지**, **signal 누락 금지**, **데드락 주의**가 필수
- iOS 12까지 지원해야 한다면 **세마포어 + 재시도 + 동시성 제한**이 실용적인 베스트
