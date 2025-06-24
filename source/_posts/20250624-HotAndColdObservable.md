---
title: Hot Observable, Cold Observable
date: 2025-06-24 14:09:26
tags: Rx, RxSwift, Observable
---

# RxSwift에서 Hot vs Cold Observable 차이

RxSwift에서 **Hot Observable**과 **Cold Observable**의 차이는 **구독 시점에 따라 데이터가 생성되고 전파되는 방식**

---

## 🔵 Cold Observable (콜드 옵저버블)

- **구독이 발생할 때마다** 새롭게 데이터를 생성
- **구독자마다 독립적인 데이터 흐름**을 가짐
- 비유: 영화 DVD → 각자 플레이할 때 영화가 시작됨.

### 📌 특징

- 데이터를 **지연 생성**함.
- 여러 구독자가 있으면 **각자 처음부터 시작**.
- 대표 예: `Observable.create`, `Observable.from`, `Observable.just`

```swift
let cold = Observable<Int>.create { observer in
    print("SUNG9...")
    observer.onNext(Int.random(in: 0...100))
    observer.onCompleted()
    return Disposables.create()
}

cold.subscribe(onNext: { print("Subscriber 1: \($0)") })
cold.subscribe(onNext: { print("Subscriber 2: \($0)") })

// 출력 예:
// SUNG9...
// Subscriber 1: 52
// SUNG9...
// Subscriber 2: 13
```

---

## 🔴 Hot Observable (핫 옵저버블)

- **데이터는 이미 생성되고 있고**, 구독자는 흐름에 **중간부터 참여**
- **모든 구독자가 동일한 데이터 스트림**을 공유
- 비유: 생중계 방송 → 중간에 들어오면 그 시점부터 시청함.

### 📌 특징

- 데이터를 **즉시 생성**하고 유지.
- 구독 시점에 따라 **이전 데이터는 못 봄**.
- 대표 예: `Subject`, `share()`, `PublishSubject`, `ReplaySubject`, `BehaviorSubject`

```swift
let subject = PublishSubject<String>()

subject.subscribe(onNext: { print("Subscriber 1: \($0)") })

subject.onNext("🔥 First Event")

subject.subscribe(onNext: { print("Subscriber 2: \($0)") })

subject.onNext("🔥 Second Event")

// 출력:
// Subscriber 1: 🔥 First Event
// Subscriber 1: 🔥 Second Event
// Subscriber 2: 🔥 Second Event
```

---

## ✅ 요약 비교

| 특징                     | Cold Observable                     | Hot Observable                     |
|--------------------------|--------------------------------------|------------------------------------|
| 데이터 생성 시점         | 구독 시                             | 구독 전에도 시작 가능             |
| 구독자 간 데이터 흐름    | 독립적                               | 공유됨                             |
| 예시                     | `Observable.create`, `just`, `from` | `Subject`, `share()`, `PublishRelay` 등 |
| 사용 시점               | API 요청, 파일 읽기 등               | 버튼 클릭, 알림, 타이머 등        |
