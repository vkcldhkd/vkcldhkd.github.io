---
title: URLSession의 httpMaximumConnectionsPerHost
date: 2025-10-06 21:00:54
category:
- iOS
tags:
- Network
- URLSession
- httpMaximumConnectionsPerHost
---

# URLSession의 httpMaximumConnectionsPerHost:

## 핵심
- 한 호스트(도메인)당 동시에 열 수 있는 HTTP 커넥션 수 제한 옵션임  
- TCP 커넥션 수를 직접 제어할 수 있음  
- 기본값은 6임 (`api.example.com` 기준으로 동시에 6개까지 요청 가능함)

---

## 작동 방식
- 설정값만큼 동시에 연결함  
- 초과된 요청은 큐에 대기시킴  
- 요청이 끝나면 다음 요청이 큐에서 나와 실행됨  
- HTTP/1.1에서는 커넥션당 1요청이므로 이 설정이 직접적으로 영향 줌  
- HTTP/2에서는 1커넥션에 여러 요청 가능해서 영향 적음  
- 그래도 여전히 “동시 스트림 수 제한” 의미로 작용함  

---

## 예시 코드

```swift
let config = URLSessionConfiguration.default
config.httpMaximumConnectionsPerHost = 3
let session = URLSession(configuration: config)

for i in 0..<10 {
    session.dataTask(with: URL(string: "https://api.example.com/data\(i)")!) { _, _, _ in
        print("Response \(i)")
    }.resume()
}
```

- 위 코드면 한 번에 최대 3개만 실제 연결함  
- 나머지 7개는 순차 대기함  

---

## 추천 값

| 시나리오 | 추천값 | 이유 |
|-----------|----------|------|
| 일반 REST API | 3~5 | 적당한 병렬성, 서버 부하 안정 |
| 이벤트 SDK / 배치 전송 | 2~3 | 서버와 클라이언트 모두 안정적 |
| 대용량 파일 업로드 | 1 | 네트워크 대역폭 확보 |
| 내부 테스트 서버 | 6 이상 | 병목 적음, 속도 테스트용 |

---

## 너무 높게 설정 시 문제
- TCP 세션 과도하게 생성됨  
- 메모리, CPU, 배터리 소모 늘어남  
- 서버에서 429(Too Many Requests), 503(Service Unavailable) 가능성 있음  

---

## iOS SDK 설계 시 추천 패턴

```swift
final class APIClient {
    static let shared = APIClient()
    let session: URLSession = {
        let config = URLSessionConfiguration.default
        config.httpMaximumConnectionsPerHost = 3
        config.waitsForConnectivity = true
        return URLSession(configuration: config)
    }()
}
```

- 이렇게 하면 SDK 내부 요청이 과도하게 몰리지 않음  
- **세마포어 안 써도 기본적인 자동 스로틀링 효과 있음**
