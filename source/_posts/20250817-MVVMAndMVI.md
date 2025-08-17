---
title: MVVM vs MVI (iOS 관점 비교)
date: 2025-08-17 23:47:14
tags:
---

# MVVM vs MVI (iOS 관점 비교)

## 1) 한줄 요약
- **MVVM**: 뷰-모델의 양방향 바인딩으로 상태 변화에 즉시 반응. 비교적 **유연**하고 **익숙**하며 UIKit/SwiftUI 어디서나 활용 쉬움.
- **MVI**: **단방향 데이터 흐름(UDI: User Intent → Reducer → State)**으로 상태를 **단일 소스**에서 관리. **예측 가능성**과 **디버깅 용이성**이 강점.

---

## 2) 핵심 구성 요소

### MVVM
- **View**: UIKit/SwiftUI 화면, 사용자 입력/렌더링 담당
- **ViewModel**: 비즈니스 로직, 상태/이벤트 바인딩(RxSwift/Combine), 네트워크/DB 호출 조정
- **Model**: 도메인/DTO/엔티티

**흐름(일반적)**  
`View (input) ⇄ ViewModel (transform) → Model(data)`  
`ViewModel (outputs) → View (render)`

### MVI
- **Intent (Action/Event)**: 사용자의 의도(버튼 탭, 스크롤 등) 혹은 시스템 이벤트
- **Reducer**: 순수 함수로 **이전 State + Intent → 새 State**
- **State (Single Source of Truth)**: 화면을 그리는 **불변 상태**
- **Effect/Side-Effect**: 네트워크/DB 등 비순수 작업

**흐름(단방향 UDF/UDFI)**  
`Intent → Reducer → New State → View (render)`  
`(필요 시) Effect → Intent(결과) → Reducer …`

---

## 3) iOS 구현 관점 메모

- **MVVM**
  - RxSwift/Combine으로 입력·출력을 바인딩 (Input/Output 패턴)
  - SwiftUI는 @StateObject/@ObservedObject, `state → view` 바인딩이 자연스러움
  - UIKit은 `ViewController ⇄ ViewModel` 의존을 최소화(테스트 용이성 위해 View 로직 분리)

- **MVI**
  - ReactorKit, The Composable Architecture(TCA), Uniflow-like, Point-Free 스타일 등
  - `State`는 **불변 struct**, `Reducer`는 순수 함수 유지
  - 사이드이펙트는 `Effect`/`Middleware`/`SideEffect` 레이어로 분리

---

## 4) 장단점 비교

| 구분 | MVVM | MVI |
|---|---|---|
| 학습 곡선 | 낮음~중간 (iOS 개발자에게 익숙) | 중간~높음 (상태/리듀서/이펙트 개념 필요) |
| 상태 관리 | 분산될 수 있음(뷰모델 내부 상태 다수) | **단일 상태**로 일관성↑, 리그레션 버그↓ |
| 데이터 흐름 | 양방향(바인딩) → 유연하지만 흐름 추적 어려울 수 있음 | **단방향** → 예측 가능, 디버깅/리플레이 용이 |
| 테스트 용이성 | 뷰모델 단위 테스트 쉬움 | 리듀서가 순수 함수 → **테스트 최강** |
| 모듈성/스케일 | 팀별 자유도↑ (하지만 일관성 저하 위험) | 패턴이 강함 → **대규모 팀 일관성** 확보 |
| 퍼포먼스 | 바인딩 남용 시 불필요한 업데이트 발생 가능 | 상태 변경 지점이 명확, 메모리/업데이트 제어 용이 |
| 보일러플레이트 | 적음~보통 | **다소 많음**(Intent/State/Reducer/Effect 정의) |
| 디버깅 | 바인딩 체인 추적 난이도 | **타임트래블/로그 재생** 등 강력한 디버깅 패턴 가능 |
| SwiftUI 적합성 | 자연스럽게 맞음 | SwiftUI와의 궁합도 좋음(TCA 등으로 광범위 사용) |
| UIKit 적합성 | 널리 검증됨 | ReactorKit/TCA-UIKit 바인딩으로 충분히 가능 |
