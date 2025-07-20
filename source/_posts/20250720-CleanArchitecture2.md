---
title: CleanArchitecture + ReactorKit
date: 2025-07-20 21:43:03
category: iOS
tags: 
- CleanArchitecture
- ReactorKit
- Presentation
- Domain
- Data
- Entity
---

# CleanArchitecture2

---

## 1. 전체 구조

클린 아키텍처는 계층 간 의존성을 분리하여, 비즈니스 로직이 외부 UI나 데이터 저장 방식에 영향을 받지 않도록 한다.

```
<외부>
Presentation
  ↕
Domain
  ↕
Data
<내부>
```

---

## 2. 계층별 설명

### Entity
- 비즈니스 규칙을 표현하는 핵심 모델이다.
- 단순한 데이터 구조가 아니라, 도메인의 상태와 로직을 담는다.

---

### Domain
- 순수한 비즈니스 로직 계층이다.
- 외부와는 무관하게 동작한다.
- UseCase와 Repository Interface로 구성된다.

---

### Data
- 데이터 저장소 접근을 담당하는 계층이다.
- 외부 API, DB, UserDefaults와 연결된다.
- Repository 인터페이스의 실제 구현체가 위치한다.

---

### Presentation
- 사용자와의 상호작용을 처리하는 계층이다.
- 상태 관리와 액션 처리를 담당한다.
- ViewController 또는 View, 그리고 Reactor로 구성된다.

---

## 3. 의존성 방향

```
Presentation → Domain (← Interface)
Data → Domain Interface
```

- Domain은 외부를 몰라야 한다.
- Data는 Domain의 Interface를 구현한다.
- Presentation은 UseCase를 사용하여 Domain 로직을 호출한다.

---

## 4. 요약 표

| 계층 | 구성 요소 | 책임 | 비고 |
|------|-----------|------|------|
| Entity | Model | 도메인 모델 정의 | 상태와 규칙 포함 |
| Domain | UseCase, Repository Interface | 비즈니스 로직 | 외부 독립적 |
| Data | Repository 구현, API, DB | 저장소 처리 | Domain Interface 구현 |
| Presentation | ViewController, View, Reactor | UI와 사용자 입력 처리 | Domain 호출 |

---

ReactorKit, RxSwift 구조에서 적용해보니, Reactor도 가벼워지고, 책임이 명확해지고 테스트도 편해져서 장점이 훨씬 많은 것 같다!
RIBs 쓸거아니면 꼭 유의해서 써야겠다 :) 
