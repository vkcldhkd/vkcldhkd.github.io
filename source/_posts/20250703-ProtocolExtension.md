---
title: Protocol Extension
date: 2025-07-03 23:20:52
category:
 - Swift
tags: 
- Protocol
- Extension
---

# Swift:
Protocol Extension의 장점

Swift의 Protocol Extension은 매우 강력한 기능으로, 프로토콜에 기본 구현을 제공하여 코드 재사용성과 유연성을 극대화할 수 있게 해준다.

---

## 1. 코드 재사용 (Code Reuse)
- 여러 타입에 걸쳐 중복되는 로직을 한 곳에서 정의 가능
- 기본 구현을 통해 타입별 구현 부담 감소
```swift
protocol Greetable {
    func greet()
}

extension Greetable {
    func greet() {
        print("Hello!")
    }
}

struct Person: Greetable {}
Person().greet() // Hello!
```

---

## 2. 타입 확장 없이 기능 확장 가능
- 기존 타입을 변경하지 않고도 기능을 추가할 수 있음
- 구조적 결합도를 낮춰 유연한 설계 가능
```swift
protocol Describable {
    func describe() -> String
}

extension Describable {
    func describe() -> String {
        return "Default description"
    }
}
```

---

## 3. 조건부 구현 (where절)
- 특정 조건을 만족하는 타입에만 기능을 제공할 수 있음
```swift
extension Collection where Element: Equatable {
    func containsDuplicate() -> Bool {
        return self.count != Set(self).count
    }
}
```

---

## 4. 기본 구현 제공으로 선택적 구현 가능
- 클래스에서 `@objc optional` 없이도 선택적으로 오버라이드 가능
- 사용자는 필요한 경우만 커스터마이징 가능

---

## 5. POP (Protocol-Oriented Programming) 지향
- Swift는 POP 기반 언어
- Protocol Extension은 객체지향보다 더 유연하고 재사용성 높은 코드 설계를 가능하게 함

---
