---
title: Protocol에서 let var
date: 2025-07-22 23:39:33
category: 
- Swift
tags: 
- protocol
---

# Swift Protocol:

Swift의 프로토콜에서는 `let`으로 속성을 선언할 수 **없다**.  
대신, **읽기 전용 속성**을 선언할 때는 `var`에 `get`만 붙여서 사용한다.

---

## 이유
Swift의 `protocol`은 **구현이 아니라 요구사항만 명시하는 타입**이다.  
`let`은 구현체 내부에서 상수로 지정하겠다는 의미지만,  
프로토콜은 이 속성이 상수인지 변수인지 미리 알 수 없기 때문에 `let`을 사용할 수 없다.

---

## 올바른 방법: 읽기 전용 속성 선언

```swift
protocol MyProtocol {
    var name: String { get }         // 읽기 전용
    var age: Int { get set }         // 읽고 쓸 수 있음
}
```

### 구현 예시:

```swift
struct Person: MyProtocol {
    let name: String     // ✅ 읽기 전용 요구사항 만족 (let 가능)
    var age: Int         // ✅ 읽기/쓰기 가능
}
```

- `name`은 `let`으로 선언했지만 `get`만 있으므로 프로토콜 요구사항을 만족한다.
- 프로토콜에서는 `let` 대신 `var name: String { get }`으로 선언하는 것이 핵심이다.


---

## 정리

| 질문 | 답변 |
|------|------|
| 프로토콜에서 `let` 사용 가능? | ❌ 불가능 |
| 읽기 전용 속성 정의하려면? | ✅ `var property: Type { get }` 사용 |
| 구현체에서 `let` 사용 가능? | ✅ 읽기 전용 속성이면 가능 |

---

