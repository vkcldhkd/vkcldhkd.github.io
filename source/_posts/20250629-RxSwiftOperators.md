---
title: RxSwift Operators
date: 2025-06-29 14:14:14
tags: RxSwift, merge, concat, from, zip, combineLatest
---


# RxSwift: 
`merge`, `concat`, `from`, `zip`, `combineLatest` 정리

RxSwift에서 Observable을 조합하거나 순차적으로 처리할 때 사용하는 `merge`, `concat`, `from`의 개념과 사용 예시, 차이점을 자세히 알기위해 작성!

---

## `merge`
- 여러 Observable을 ****동시에 구독****하고, ****방출되는 값을 병합****해서 하나의 Observable로 만들어줌
- 각 Observable의 ****이벤트 순서는 유지****, 하지만 ****여러 Observable이 동시에 이벤트를 방출할 수 있음****
```swift
let subject1 = PublishSubject<String>()
let subject2 = PublishSubject<String>()

Observable.merge(subject1, subject2)
    .subscribe(onNext: { print($0) })

subject1.onNext("A")
subject2.onNext("1")
subject1.onNext("B")
subject2.onNext("2")

// 출력: A, 1, B, 2 (순서 보장 없음)
```

---

## `concat`
- 여러 Observable을 ****순차적으로 연결****하여, ****앞 Observable이 완료된 후 다음 Observable을 구독****함
- ****순서 보장****
```swift
let obs1 = Observable.of("A", "B")
let obs2 = Observable.of("1", "2")

Observable.concat(obs1, obs2)
    .subscribe(onNext: { print($0) })

// 출력: A, B, 1, 2 (순서 보장)
```

---

## `from`
- 배열, 시퀀스 등을 Observable로 변환함
- 즉, `Array → Observable`
```swift
let items = [1, 2, 3]

Observable.from(items)
    .subscribe(onNext: { print($0) })

// 출력: 1, 2, 3
```

---

## `zip`
- 두 Observable이 **각각 하나의 값을 방출할 때마다 짝을 지어 하나로 묶음**
- 짝이 안 맞으면 기다림
```swift
let obs1 = Observable.of("A", "B", "C")
let obs2 = Observable.of("1", "2")

Observable.zip(obs1, obs2) { a, b in "\(a)-\(b)" }
    .subscribe(onNext: { print($0) })

// 출력: A-1, B-2 (C는 버려짐)
```

---

## `combineLatest`
- 두 Observable이 **각각 최소 하나의 값을 방출한 후**, 이후 하나라도 새로운 값을 방출하면 **가장 최신 값끼리 조합**
- 최신 상태 유지
```swift
let obs1 = BehaviorSubject(value: "A")
let obs2 = BehaviorSubject(value: "1")

Observable.combineLatest(obs1, obs2) { a, b in "\(a)+\(b)" }
    .subscribe(onNext: { print($0) })

obs1.onNext("B")
obs2.onNext("2")

// 출력: A+1, B+1, B+2
```
---

## 차이점

| 연산자   | 용도                       | 동시에 실행 | 순서 보장 | 사용 예시 |
|----------|----------------------------|---------------|------------|------------|
| `merge`  | 여러 Observable 이벤트를 동시에 방출 | ✅            | ❌         | 실시간 알림, 이벤트 흐름 통합 |
| `concat` | Observable들을 순차적으로 연결 | ❌            | ✅         | 단계별 실행, 체이닝 |
| `from`   | 배열/시퀀스를 Observable로 변환 | -             | ✅         | 배열 → 스트림 변환 |
| `zip`            | 각각의 값을 짝지어 결합               | ✅         | 값 쌍으로 묶음 |
| `combineLatest`  | 가장 최신 값으로 조합 (최소 1개 필요) | ✅         | 최신 상태 결합 |

---

## 언제 사용하면 좋을까?

- `merge`: 실시간 알림, UI 이벤트 통합 등 ****동시 이벤트 처리****
- `concat`: 여러 단계가 ****순차적으로 이루어져야 하는 경우****
- `from`: 단순 배열을 Observable로 바꿔야 할 때
- `zip`: 두 데이터가 동시에 필요 (한 쌍이 갖춰졌을 때만 처리)
- `combineLatest`:  최신 값으로 UI 업데이트

---
