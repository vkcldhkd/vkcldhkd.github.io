---
title: CollectionViewLayout
date: 2025-09-01 14:21:02
category:
- iOS
tags:
- CollectionViewLayout
- CompositionalLayout
- FlowLayout
---

# CompositionalLayout vs FlowLayout

`UICollectionViewFlowLayout`(이하 **FlowLayout**)과 `UICollectionViewCompositionalLayout`(이하 **CompositionalLayout**)은 iOS 컬렉션 뷰 레이아웃을 구성하는 두 가지 대표 방식입니다. 이 문서는 두 레이아웃의 개념/기능 차이와 장단점, 선택 가이드, 실무 팁을 간단히 정리합니다.

---

## 개념 비교
- **FlowLayout**
  - 행/열 규칙에 따라 자동 배치되는 **단순 리스트/그리드 전용** 레이아웃.
  - 전역 속성(아이템 크기, 간격, 인셋) 중심. 섹션별로 다른 형태를 만들려면 서브클래싱/오버라이드가 필요.
- **CompositionalLayout**
  - **Item → Group → Section**을 선언적으로 조립하는 **범용 레이아웃 엔진**.
  - 섹션별 상이한 구조(카루셀, 모자이크, 배너+그리드 등)를 **서브클래싱 없이** 구성 가능.

---

## 기능 비교 요약

| 항목               | FlowLayout                                | CompositionalLayout                |
|------------------|-------------------------------------------|------------------------------------|
| 섹션별 상이한 레이아웃     | 제한적(커스텀 필요)                               | **매우 용이**                      |
| 가로 캐러셀(carousel) | 사실상 불가(중첩 CV 필요)                          | **내장**                         |
| 핀 헤더(Sticky)     | `sectionHeadersPinToVisibleBounds = true` | `pinToVisibleBounds`               |
| 섹션 배경/데코레이션      | 서브클래싱 + 등록 필요                             | `NSCollectionLayoutDecorationItem` |
| Self-Sizing      | `estimatedItemSize`                       | `.estimated(...)`(아이템/그룹 단위)       |
| 러닝커브             | **낮음**                                | **높지만 강력**                     |
| iOS 지원           | iOS 6+                                    | **iOS 13+**                    |

---

### FlowLayout
**장점**
- 설정 단순, 러닝커브 낮음.
- 단순 리스트/격자에서 오버헤드 최소.
- 구(iOS 12 이하)까지 폭넓은 호환성.

**단점**
- 섹션마다 전혀 다른 레이아웃을 만들기 어렵다.
- 가로 카루셀/모자이크 등 복합 배치에 한계.

### CompositionalLayout
**장점**
- 섹션 단위로 **완전히 다른 레이아웃**을 선언형으로 구성.
- 환경(`NSCollectionLayoutEnvironment`)에 따라 **동적 대응**(열 수 변경 등)이 쉬움.
- 중첩 컬렉션 뷰 없이 복합 피드를 성능/접근성면에서 깔끔히 구현.

**단점**
- API가 길고 개념 학습 필요.
- 복잡한 Self-Sizing/중첩 그룹은 계산 비용이 커질 수 있음.
- iOS 13+ 요구.

---

## 언제 무엇을 쓰는게 좋을까?
- **단순 리스트/그리드**(뉴스 피드, 3열 격자 등): **FlowLayout** **(또는* `*UICollectionLayoutListConfiguration*` *고려)**
- **복합 피드**(배너 + 가로 카루셀 + 그리드 + 핀 헤더 + 섹션 배경): **CompositionalLayout**
- **가로 스크롤 섹션을 섞고 싶다**: CompositionalLayout의 `orthogonalScrollingBehavior` 채택
- **iOS 12까지 지원** 필요: FlowLayout(또는 커스텀 레이아웃) 유지

---

## 성능 팁
- **Self-Sizing**는 필요한 곳에만, `estimated` 값을 적절히(너무 작지도 크지도 않게) 설정.
- 카루셀 섹션은 아이템/그룹 크기를 **절대값 또는 고정 비율**로 두면 계산이 단순해져 부드러움.
- 큰 **cornerRadius/마스크/그림자**는 오프스크린 렌더링 유발 → 컨테이너 분리(바깥 그림자, 안쪽 모서리), 재설정 최소화.
- 프리페칭/백그라운드 이미지 디코딩·리사이즈, 셀 재사용 최적화 필수.
