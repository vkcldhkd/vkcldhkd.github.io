---
title: RxSwift → Swift Concurrency/Combine 전환 시 주의사항
date: 2025-09-08 23:59:29
category: 
- iOS
tags:
- RxSwift 
- Concurrency
- Combine
---

# RxSwift → Swift Concurrency/Combine 전환 시 주의사항 (ReactorKit 관점, iOS 15 기준)

## 개요
기존 RxSwift 기반 ReactorKit 코드는 비동기 처리와 스트림 관리에 **Observable/Subject**를 사용하지만, iOS 15부터는 **Swift Concurrency(async/await, Task) **와 **Combine(Publisher) **로 대체할 수 있다. 전환 시 주의해야 할 점들을 기록하기 위해 작성!

------------------------------------------------------------------------

## 주의할 점

1. **취소(Cancellation)**
   - Rx: dispose
   - Combine: cancel
   - Concurrency: `Task` 취소가 상위 → 하위로 전파됨.
2. **스케줄링(MainActor hop)**
   - UI 업데이트는 반드시 `@MainActor`에서 수행.
3. **리플레이/상태 유지**
   - Rx: `BehaviorSubject(value:)`
   - Combine: `CurrentValueSubject`
   - Concurrency: `@Published` 또는 `actor` 상태 보관.
4. **에러 처리**
   - Rx/Combine: 스트림 오류와 값이 같은 레벨.
   - Concurrency: `async throws`로 모델링, `do-catch` 필요.
5. **테스트**
   - Rx: `TestScheduler`
   - Combine: `CombineSchedulers`, `XCTest`
   - Concurrency: XCTest의 async 지원, `clock` 모킹.

------------------------------------------------------------------------

## 마이그레이션 전략 (ReactorKit)

1. **Infra 계층**: 네트워크/DB 호출은 `async/await`로 변경.
2. **Reactor 계층**: Action → Mutation → State 파이프라인을 유지하되,
   - Action 처리: `Task` 기반 비동기 호출
   - State 반영: `@MainActor` 보장
3. **View 계층**: UIKit은 Combine(`@Published`, `assign`)으로 바인딩, SwiftUI는 `@StateObject`/`@ObservedObject` 활용.

------------------------------------------------------------------------

## 치환 치트시트 (ReactorKit 관점)

- `Observable<Action>` → `AsyncStream<Action>` 또는 `PassthroughSubject<Action, Never>`
- `Observable<Mutation>` → `AsyncStream<Mutation>` 또는 `Publisher<Mutation, Never>`
- `BehaviorSubject<State>` → `@Published var state`
- `flatMapLatest` → Combine의 `switchToLatest` / Concurrency의 Task 취소 제어
- `share(replay: 1)` → `CurrentValueSubject` or 상태 저장

------------------------------------------------------------------------

## 결론
- **단발 비동기(네트워크, DB)**: Swift Concurrency(`async/await`) 권장
- **지속 스트림(UI 입력, 이벤트)**: Combine 권장
- **ReactorKit 스타일 유지**: Action-Mutation-State 파이프라인을 유지하면서, Rx 의존성을 줄이고 iOS 15 이상의 언어/런타임 기능으로 현대화 가능

------------------------------------------------------------------------
