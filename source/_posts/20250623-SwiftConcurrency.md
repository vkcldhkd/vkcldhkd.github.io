---
title: Swift Concurrency
date: 2025-06-23 18:28:03
category:
 - Swift
tags: 
- Concurrency
---

# Swift Concurrency: 
`actor` 와 `@MainActor` 정리

Swift Concurrency에서는 데이터 경쟁을 방지하고 **스레드 안전성**을 보장하기 위해 `actor`와 `@MainActor` 키워드를 제공

---

## 🧱 actor

`actor`는 Swift에서 제공하는 **동시성 안전한 객체 타입**
클래스처럼 참조 타입이지만, 내부 상태에 접근할 때는 **직렬(queue)을 통해 비동기 접근**

### ✅ 특징
- 내부 상태는 **actor 외부에서 직접 접근 불가** (비동기 접근)
- 동시에 여러 Task가 접근하더라도 **데이터 레이스(Data Race) 방지**
- `await`를 통해 안전하게 접근 가능

```
⚠️ 데이터 레이스 (Data Race)란?
두 개 이상의 스레드가 동시에 같은 메모리(변수 등)에 접근할 때, 그 중 적어도 하나가 쓰기(write)를 수행하면 발생하는 문제
```

### 🔸 예시
```swift
actor Counter {
    private var value = 0

    func increment() {
        value += 1
    }

    func getValue() -> Int {
        return value
    }
}

let counter = Counter()

Task {
    await counter.increment()
    print(await counter.getValue())
}
```

---

## 🎯 @MainActor

`@MainActor`는 해당 코드가 **메인 스레드(UI 스레드)** 에서 실행되어야 함을 보장  
**UI 업데이트**나 **UIKit 접근**을 안전하게 처리할 때 유용

### ✅ 특징
- 메인 스레드에서 실행됨을 컴파일러가 보장
- UI 관련 작업 시 충돌이나 크래시 방지
- 클래스, 함수, 변수 등 모든 선언에 사용 가능

### 🔸 예시
```swift
@MainActor
class ViewModel {
    var title: String = ""

    func updateTitle() {
        title = "Updated on Main Thread"
    }
}

let viewModel = ViewModel()

Task {
    await viewModel.updateTitle()
}
```

또는 메서드 단위에도 적용 가능:

```swift
class ViewModel {
    var title: String = ""

    @MainActor
    func updateTitle() {
        title = "Updated on Main Thread"
    }
}
```

---

## ✅ actor vs @MainActor 요약 비교

| 항목          | actor                                | @MainActor                             |
|---------------|---------------------------------------|-----------------------------------------|
| 목적          | 데이터 레이스 방지                    | 메인 스레드에서의 안전한 실행 보장      |
| 스레드        | 내부 직렬 처리 (스레드 추상화됨)      | 무조건 메인 스레드                      |
| 용도          | 동시성 환경에서 상태 관리              | UI 작업, UIKit/SwiftUI 관련 처리         |
| 사용 위치     | 클래스처럼 정의 가능                   | 함수, 클래스, 프로퍼티 등에 어노테이션   |
| 접근 방식     | `await` 필요                          | `await` 필요 (`MainActor-isolated`)     |

---

## 📌 참고
- `actor`는 동시성 안전 객체를 만드는 방식
- `@MainActor`는 해당 코드가 UI 스레드에서 실행되도록 지정


## 공부 더 하자!!!!!!!
```
주요 동시성 키워드 목록
키워드    설명
async    비동기 함수 선언. 호출 시 await 필요
await    async 함수의 결과를 기다릴 때 사용
Task    새로운 비동기 작업(경량 스레드)을 시작
TaskGroup    여러 비동기 작업을 그룹으로 병렬 실행
actor    상태를 보호하는 동시성-safe 객체
@MainActor    메인 스레드에서 실행되도록 보장
@Sendable    스레드 간 안전하게 전달 가능한 클로저 보장
nonisolated    actor 안에서 동기적으로 접근 가능한 함수 선언
DetachedTask    actor나 context와 분리된 비동기 작업 실행
withCheckedContinuation    기존 콜백 기반 코드를 async/await로 바꾸기 위한 브릿지
withTaskGroup    여러 작업을 동시에 실행하고 결과를 모을 때 사용
```
