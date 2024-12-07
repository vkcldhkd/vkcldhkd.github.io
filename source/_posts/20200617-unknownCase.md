---
title: Enum Unknown Case
date: 2020-06-17 13:42:35
tags: iOS
---

iOS에서 서버의 데이터를 파싱하는 과정에서 코더블을 많이 사용하게된다.
해당 객체의 타입이 enum 타입일때, 정의한 값 이외에 알 수 없는 값이 오게되면 error가 나게되는데 아래의 방법으로 처리하면 기본값을 설정할 수 있다.

```
protocol UnknownCaseRepresentable: RawRepresentable, CaseIterable where RawValue: Equatable {
    static var unknownCase: Self { get }
}

extension UnknownCaseRepresentable {
    init(rawValue: RawValue) {
        self = Self.allCases.first(where: { $0.rawValue == rawValue }) ?? Self.unknownCase
    }
}
```

아래와 같이 프로토콜을 선언한 뒤, 사용하려는 객체에서 사용하면된다. :)

```
enum AccountClass: String, Codable, Equatable, UnknownCaseRepresentable{
    static let unknownCase: AccountClass = .normal
    
    case normal
    case forshop
}
```
도움 주신 [Danuel](https://github.com/Danue1) 님 감사합니다!