---
title: Observable과 Subject
date: 2025-06-29 22:52:09
category:
 - RxSwift
tags:
- Observable
- Subject
- PublishSubject
- BehaviorSubject
- ReplaySubject
- AsyncSubject
---

# RxSwift: 
Observable과 Subject 및 Subject 하위 타입 비교

RxSwift에서 핵심요소인  `Observable`과 `Subject`에 대해서 공부하고, 사용처를 더 명확히 구분하기 위해 포스팅!!

---

## Observable이란?

| 항목        | 설명 |
|-------------|------|
| 정의        | 데이터를 **방출(produce)** 하는 단방향 스트림 |
| 특징        | 구독자가 있어야 동작 시작, 값을 외부에서 주입 불가 |
| 예시        | `Observable.of(...)`, `Observable.create(...)` |
| 사용 목적   | 정적 데이터 스트림, 비동기 이벤트 흐름 |

---

## Subject란?

| 항목    | 설명                                                  |
|-------|-----------------------------------------------------|
| 정의    | `Observable` + `Observer` 역할을 모두 수행                 |
| 특징    | 외부에서 `onNext`, `onError`, `onCompleted`로 이벤트를 주입 가능 |
| 사용 목적 | UI 이벤트 바인딩, 외부 이벤트 수동 트리거 등                         |

---

## Subject의 하위 타입 비교

| 타입               | 초기 값 | 마지막 값 유지 | 이벤트 재방출 | 구독 시 수신되는 값 | 비고 |
|--------------------|---------|----------------|----------------|----------------------|------|
| `PublishSubject`   | ❌ 없음 | ❌ 없음         | ❌ 없음         | **구독 후 발생한 이벤트만** | 가장 기본적인 Subject |
| `BehaviorSubject`  | ✅ 필요 | ✅ 유지         | ❌ 없음         | **가장 최근 값 + 이후 이벤트** | 항상 최근 상태 유지 |
| `ReplaySubject`    | ❌ 선택 | ✅ 가능         | ✅ 가능         | **버퍼 내 모든 이벤트**       | N개까지 기억 |
| `AsyncSubject`     | ❌ 없음 | ✅ 유지 (최종)  | ❌ 없음         | **완료 시 마지막 값 1개만**   | 오직 마지막 결과 전달 |

---

## 예제 

### PublishSubject
```swift
let subject = PublishSubject<String>()
subject.onNext("Before")  // 구독 전 → 무시됨

subject.subscribe(onNext: { print("A:", $0) })
subject.onNext("After")   // 출력: A: After
```

### BehaviorSubject
```swift
let subject = BehaviorSubject(value: "Initial")
subject.onNext("First")

subject.subscribe(onNext: { print("B:", $0) })
// 출력: B: First (구독 시 가장 최근 값 전달)
```

### ReplaySubject
```swift
let subject = ReplaySubject<String>.create(bufferSize: 2)
subject.onNext("1st")
subject.onNext("2nd")
subject.onNext("3rd")

subject.subscribe(onNext: { print("C:", $0) })
// 출력: C: 2nd, C: 3rd (최근 2개 재방출)
```

### AsyncSubject
```swift
let subject = AsyncSubject<String>()
subject.onNext("A")
subject.onNext("B")

subject.subscribe(onNext: { print("D:", $0) })
subject.onNext("C")
subject.onCompleted()
// 출력: D: C (완료 시 마지막 값 전달)
```

---

## 상황에 따른 추천 Subject

| 상황                             | 추천 Subject      |
|----------------------------------|-------------------|
| 단순 이벤트 알림                 | `PublishSubject`  |
| 상태 값을 계속 유지해야 할 때     | `BehaviorSubject` |
| 히스토리 이벤트를 공유해야 할 때 | `ReplaySubject`   |
| 오직 마지막 결과만 필요할 때     | `AsyncSubject`    |

---

## 정리
- `Observable`: 외부 제어 없이 데이터만 방출
- `Subject`: 외부에서 이벤트를 주입할 수 있는 Observable
- 하위 Subject는 이벤트를 **어떻게 저장/공유/전달**할지에 따라 구분됨
- 실무에서는 보통 `PublishSubject`, `BehaviorSubject`, `ReplaySubject` 순으로 많이 사용됨

---

적절한 Subject를 사용하는 것은 **상태 관리, UI 반응성, 메모리 최적화**에 큰 영향을 준다고한다.
기존에는 `BehaviorSubject` 위주로 사용했었는데 더 목적에 따라 맞게 사용할 수 있도록 해야겠다 :)
