---
title: Clean Architecture
date: 2025-07-05 23:16:05
category:
 - Swift
tags: 
- Clean Architecture
- ReactorKit
- DI
- 관심사분리
---

# 클린 아키텍처 (Clean Architecture):

클린 아키텍처는 소프트웨어 아키텍처 설계 원칙 중 하나로, **의존성 역전\(Dependency Inversion\)**을 핵심으로 하여 **비즈니스 로직과 외부 요소(UI, DB 등)**를 철저히 분리한다고한다.
 MVC, MVI와 어떤부분이 크게 다른지 알아보고 더 공부하기위해 작성!

---

## 개념
클린 아키텍처는 **계층별로 관심사(Responsibility)를 나누고**, 각 계층은 **안쪽 계층(도메인)**에만 의존하도록 구성

### 계층 구조
```
+------------------------+  
|        UI Layer        |  ← ViewController, View
+------------------------+  
|   Presentation Layer   |  ← Presenter, ViewModel
+------------------------+  
|     Domain Layer       |  ← UseCase, Entity
+------------------------+  
|     Data Layer         |  ← Repository, API, DB
+------------------------+
```

### 의존성 방향
- 모든 의존성은 **안쪽(Domain)**으로 향한다.
- 외부(UI, DB, 프레임워크)는 언제든지 교체 가능!

---

## 장점
- 테스트 용이 (도메인 계층이 UI/프레임워크에 독립적)
- 유지보수성, 확장성 향상
- 관심사 분리 (SRP - 단일 책임 원칙 적용 가능)

---

## MVC, MVI와의 비교

| 항목              | MVC                                | MVI                                           | Clean Architecture                         |
|-------------------|-------------------------------------|-----------------------------------------------|--------------------------------------------|
| 구조 단순성       | ✅ 단순함                          | ❌ 상대적으로 복잡                          | ❌ 더 많은 계층 구성 필요                 |
| 관심사 분리       | ❌ Controller가 비대해지기 쉬움   | ✅ 단방향 흐름으로 명확한 책임               | ✅ 계층 기반 분리                          |
| 테스트 용이성     | ❌ UI 의존적                        | ✅ State 기반 테스트 가능                    | ✅ 비즈니스 로직 단위로 테스트 용이       |
| 프레임워크 의존성 | ⛔ UIKit에 강한 결합               | ⛔ Rx/MVI 기반 의존                           | ✅ 도메인 레이어는 프레임워크에 독립적    |
| 방향성            | 양방향                              | 단방향 (Intent → Model → View)              | 의존성 역전 (Data → Domain ← UI)          |

---

## 예시 (Search)

- **UI Layer**: `SearchViewController`
- **Presentation Layer**: `SearchViewModel`, `SearchPresenter`
- **Domain Layer**: `SearchUseCase`, `SearchEntity`
- **Data Layer**: `SearchRepository`, `SearchAPI`

---

## ReactorKit과 클린 아키텍처를 같이 사용하고싶다면?
> `Reactor`는 **클린 아키텍처의 Presentation Layer(ViewModel 자리)**를 충분히 대체 가능할 것 같다!
> `View ↔ Reactor ↔ UseCase` 흐름으로 단방향 데이터 흐름을 구현 가능 할 것 같다..

## Reactor가 ViewModel을 대체할 수 있는 이유
| 역할         | 설명 |
|--------------|------|
| **입력 처리**    | `Action`으로 사용자 이벤트 전달 |
| **상태 변환**    | `Mutate` → `Reduce` 패턴으로 상태 변화 처리 |
| **상태 제공**    | `State`로 UI 업데이트 상태 제공 |
| **의존성 주입**  | `UseCase`, `Service` 등 외부 주입 가능 |

→ Reactor는 ViewModel처럼 동작하면서도 **단방향 흐름을 명확히 구현**할 수 있지않을까?

---

## 주의할 점

- **Reactor가 비대해지지 않도록**, 복잡한 비즈니스 로직은 `UseCase`로 위임하는것이 좋다고한다.
- `Reactor`는 단순히 액션을 받아서 상태를 변경하고, 상태를 View로 전달하는 역할에 집중하는 것이 이상적!

---

## 예시

```swift
final class MyReactor: Reactor {
    enum Action {
        case fetchTitle
    }
    enum Mutation {
        case setTitle(String)
    }

    struct State {
        var title: String = ""
    }

    let useCase: MyUseCase

    init(useCase: MyUseCase) {
        self.useCase = useCase
    }

    func mutate(action: Action) -> Observable<Mutation> {
        switch action {
        case .fetchTitle:
            let setTitle: Observable<Mutation> = 
                useCase.fetchTitle().map { Mutation.setTitle($0) }

            return .concat([setTitle])
        }
    }

    func reduce(state: State, mutation: Mutation) -> State {
        switch mutation {
        case let .setTitle(title):
            var newState = state
            newState.title = title
            return newState
        }
    }
}
```

# ReactorKit 사용 시, 계층구조(?)
아직 해본 부분이 아니라 예측으로 작성..! 맞는지 확인필요!

```
+------------------------+  
|        UI Layer        | ← ViewController, View
| 사용자 인터페이스, UI 렌더링, 유저 액션 전달
+------------------------+  
|   Presentation Layer   | ← Presenter, Reactor
| 상태/액션 처리, 유저 액션을 도메인 계층에 전달
+------------------------+  
|     Domain Layer       | ← UseCase, Entity
| 핵심 비즈니스 로직, 애플리케이션 규칙
+------------------------+  
|     Data Layer         | ← Repository, API, DB
| 외부 리소스 (네트워크, DB) 처리
+------------------------+
```
