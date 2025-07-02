---
title: Protocol과 Delegate
date: 2025-07-02 21:07:15
category:
 - RxSwift
tags: 
- debounce
- throttle
---

# Swift:
Protocol vs Delegate 차이점 정리

Swift에서 `Protocol`과 `Delegate`는 밀접한 관계가 있으며, 객체 간 통신 및 책임 분리의 핵심 개념!
하지만 정확히 말하면 **Delegate는 Protocol을 사용하는 하나의 디자인 패턴**이라고 한다. 
자세히 알기위해 포스트 작성!!

---

## Protocol 이란?
- **기능** 을 정의하는 타입
- 구현을 강제하지 않음 (선언만 함)
- 클래스, 구조체, 열거형 등에서 채택 가능
```swift
protocol SomeProtocol {
    func doSomething()
}
```

---

## Delegate 이란?
- **Delegation Pattern** 을 의미
- 하나의 객체가 특정 작업을 **다른 객체에 위임**할 때 사용
- 위임 대상은 보통 Protocol을 채택
- iOS에서 많이 사용하는 설계 방식 (`UITableViewDelegate`, `UITextFieldDelegate` 등)
```swift
protocol MyDelegate: AnyObject {
    func didTapButton()
}

class ChildViewController {
    weak var delegate: MyDelegate?

    func buttonTapped() {
        delegate?.didTapButton()
    }
}
```

---

## 차이점

| 항목       | Protocol                     | Delegate                                |
|------------|------------------------------|------------------------------------------|
| 정의       | 기능의 명세                   | Protocol을 활용한 설계 패턴             |
| 개념       | 추상화                        | 위임 (Delegation)                        |
| 역할       | 채택자가 구현할 인터페이스     | 특정 역할을 다른 객체에 위임            |
| 사용 대상  | 모든 타입                     | 보통 클래스 간 통신                     |
| 메모리 관리 | 상관없음                      | 보통 `weak`으로 선언 (순환 참조 방지)   |

---

## 정리

- `Protocol`은 단독으로도 사용 가능하지만,`Delegate`는 항상 `Protocol`을 기반으로 구성된다.
- 즉, **Delegate는 Protocol의 응용 사례** !!

