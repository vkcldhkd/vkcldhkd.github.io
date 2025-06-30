---
title: DisposeBag, Dispose()
date: 2025-06-29 22:31:22
category:
 - RxSwift
tags: 
- DisposeBag
- Dispose
---

# RxSwift: 
`DisposeBag`의 역할과 `dispose()`와의 차이

---

## DisposeBag이란?
- `DisposeBag`은 RxSwift에서 ****Disposable 객체들을 담아두는 바구니**** 역할
- 구독(Subscribe)이 끝날 때 자동으로 ****모든 리소스를 정리(dispose)****
```swift
let disposeBag = DisposeBag()

Observable.of(1, 2, 3)
    .subscribe(onNext: { print($0) })
    .disposed(by: disposeBag)
```

### 역할
- `deinit` 시 내부의 모든 Disposable을 자동으로 해제
- 수동으로 `dispose()`를 호출하지 않아도 됨
- 보통 ****ViewController의 프로퍼티로 보관**** 

---

## dispose()란?
- Rx의 구독은 Disposable을 반환하며, 이 객체에 대해 수동으로 `dispose()`를 호출하면 ****스트림을 해제****할 수 있음
```swift
let subscription = Observable.of("A", "B", "C")
    .subscribe(onNext: { print($0) })

subscription.dispose() // 수동으로 메모리 해제
```

---

## DisposeBag vs dispose()

| 항목           | `DisposeBag`                        | `dispose()`                           |
|----------------|-------------------------------------|----------------------------------------|
| 사용 방식      | `.disposed(by: disposeBag)`         | `.dispose()` 직접 호출                |
| 메모리 해제 시점 | `DisposeBag`가 deinit 될 때 자동     | 호출한 시점에 즉시 해제               |
| 주 사용처      | ViewController, 클래스 전체 관리용   | 임시 Disposable 수동 제어 필요 시     |
| 반복 사용      | 여러 Disposable을 모아서 처리 가능    | 단건 처리에 적합                       |

---

## 추천 방식
| 상황 | 추천 방식 |
|------|-----------|
| ViewController에서 구독 관리 | ✅ `DisposeBag` |
| 구독을 수동으로 해제해야 할 때 | ✅ `dispose()` |
| 타이머, 일회성 작업 제어 등 | ✅ `dispose()` |

---

## 결론
- `DisposeBag`은 메모리 누수 방지를 위한 ****자동 관리 도구****
- `dispose()`는 ****직접 수동 해제****가 필요한 경우 사용
- 대부분의 실무에서는 `DisposeBag`을 사용하여 ****명확하고 안전하게 메모리를 관리****
