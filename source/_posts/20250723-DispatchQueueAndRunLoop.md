---
title: DispatchQueue과 RunLoop 차이점
date: 2025-07-23 13:51:32
category:
- iOS
tags:
- DispatchQueue
- RunLoop
---

# Swift에서 DispatchQueue, RunLoop:

## 요약
| 항목 | DispatchQueue | RunLoop |
|------|---------------|---------|
| 목적 | 작업을 비동기/동기적으로 실행 | 이벤트와 타이머 처리 |
| 주 사용처 | 쓰레드 간 작업 분배 | UI 이벤트, 타이머, 입력 감지 |
| 실행 방식 | 큐에 태스크를 넣고 실행 | 계속 루프를 돌면서 이벤트 감지 및 처리 |
| 쓰레드 제어 | 내부적으로 스레드를 관리 | 주로 메인 스레드에서 사용됨 |
| 블로킹 | 보통 블로킹 없이 처리 | `run()`으로 루프를 멈출 수도 있음 |

---

## DispatchQueue란?
- GCD(Grand Central Dispatch)의 핵심 구성 요소 중 하나
- 비동기적으로 작업을 큐에 넣어 처리할 수 있게 해주는 기능
```swift
DispatchQueue.global().async {
    // 백그라운드 작업
    DispatchQueue.main.async {
        // UI 업데이트
    }
}
```
- `main`, `global`, `custom queue` 등 다양한 종류가 있음
- `async` / `sync`로 태스크 실행 방법을 결정

---

## RunLoop란?
- 하나의 쓰레드 안에서 ****이벤트/입력/타이머를 감지하고 처리하는 루프 시스템****
- 주로 메인 쓰레드에서 UI 이벤트, 타이머, 입력 이벤트 등을 처리할 때 사용

```swift
RunLoop.current.run(mode: .default, before: Date.distantFuture)
```

- 앱이 멈추지 않고 계속 실행되는 것도 사실 RunLoop 덕분이라고한다.
- `Timer`, `InputStream`, `Notification` 등을 사용할 때 내부적으로 RunLoop가 돌아간다.

---

## 차이점
| 구분 | 설명 |
|------|------|
| ****DispatchQueue**** | 어떤 일을 "언제" 어떤 쓰레드에서 "시작할지" 결정함 |
| ****RunLoop**** | 실행된 쓰레드가 "계속 돌면서" 이벤트를 감시하고 처리함 |

---

## 비유하면?
- `DispatchQueue`는 작업을 ****할당해주는 사람****이고,  
- `RunLoop`는 그 일을 ****계속 돌면서 처리하는 워커****라고 보면 돼.

---

## 예시
```swift
// DispatchQueue로 비동기 작업 실행
DispatchQueue.global().async {
    print("비동기 작업 실행 중")
}

// RunLoop 돌리기
RunLoop.current.run(until: Date().addingTimeInterval(2))
```

---

## 정리

- `DispatchQueue`는 ****작업을 다른 큐로 보내서 실행****할 때 사용
- `RunLoop`는 ****지속적인 이벤트 감지와 처리****가 필요할 때 사용
- 둘 다 iOS의 비동기 처리에 꼭 필요한 도구니까 차이를 확실히 알아둘것!!
