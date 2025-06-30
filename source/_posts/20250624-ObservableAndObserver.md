---
title: Observable vs Observer
date: 2025-06-24 14:16:18
category:
 - RxSwift
tags: 
- Observable
- Observer
---

# RxSwift에서 Observable vs Observer 차이

RxSwift에서 **Observable**(옵저버블)과 **Observer**(옵저버)는 개념적으로 서로 연결된 존재지만 **역할**이 다르다.
맨날 사용하는건데 용어적인 개념을 이제야 다시 명확하게 하기위해 작성 ㅠㅠ...

---

## 📦 Observable (옵저버블)

> 데이터를 **방출**하는 객체.  
> 이벤트 스트림을 만들고, 구독자가 생기면 데이터를 넣어줌

### 🔹 역할
- `onNext`, `onCompleted`, `onError` 같은 **이벤트를 전달**
- 구독 시점에 따라 데이터 흐름을 조절 (콜드/핫)

### 🔹 예시
```swift
let observable = Observable.of("A", "B", "C")
```

---

## 👂 Observer (옵저버)

> 데이터를 **구독하고 처리**하는 객체.  
> Observable에서 전달되는 이벤트를 받아 **반응**

### 🔹 역할
- `onNext`: 새 데이터가 도착했을 때
- `onError`: 에러 발생 시
- `onCompleted`: 완료되었을 때

### 🔹 예시
```swift
observable.subscribe(
    onNext: { print("값: \($0)") },
    onError: { print("에러 발생: \($0)") },
    onCompleted: { print("완료!") }
)
```

---

## ✅ Observable vs Observer 비교

| 항목          | Observable (옵저버블)         | Observer (옵저버)              |
|---------------|-------------------------------|-------------------------------|
| 역할          | 이벤트를 **발행**함            | 이벤트에 **반응**함           |
| 주도권        | 데이터를 내보내는 쪽           | 데이터를 받아서 처리하는 쪽  |
| 생성 방식     | `Observable.of`, `create()` 등 | `subscribe(...)`로 구현됨    |
| 예시 비유     | 유튜브 채널                    | 유튜브 시청자                 |
