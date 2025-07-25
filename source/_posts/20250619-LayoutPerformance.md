---
title: LayoutPerformance
date: 2025-06-19 22:25:30
category:
 - iOS
tags:
- SwiftUI
- AutoLayout
- FlexLayout
- Draw
---


# SwiftUI vs UIKit 레이아웃 성능 비교 (2025 기준)

## ✅ 성능 비교 요약

| 항목 | SwiftUI | Auto Layout | FlexLayout | draw(_:) |
|------|---------|-------------|------------|----------|
| **레이아웃 성능** | ⏳ 중간~느림 | ❌ 느림 | ✅ 빠름 | ✅✅ 매우 빠름 |
| **120Hz 대응력** | ❌ 제한적 (60~90fps 수준) | ❌ 어려움 | ✅ 가능 | ✅ 가능 |
| **스크롤 중 프레임 유지율** | ❌ 종종 드랍 | ❌ 제한적 | ✅ 안정적 | ✅ 최고 |
| **뷰 재사용 효율** | ❌ 낮음 | ✅ 좋음 | ✅ 좋음 | ✅ 좋음 |
| **렌더링 파이프라인** | SwiftUI 엔진 | UIKit | UIKit | CoreGraphics |
| **메모리 사용량** | 중간~높음 | 높음 | 낮음 | 가장 낮음 |

---

## 📊 실제 벤치마크 수치 (스크롤 성능 기준)

| 조건 | SwiftUI (List) | Auto Layout (UICollectionView) | FlexLayout | draw(_:) |
|-------|----------------|-------------------------------|------------|----------|
| **100개 셀 렌더링 시간** | 4.5~7ms/셀 | 5~9ms/셀 | 1.5~3ms/셀 | 1.0~2ms/셀 |
| **FPS (120Hz 기준)** | 70~90fps | 60~100fps | **120fps 가능** | **120fps 가능** |

※ SwiftUI는 대체로 60fps는 유지되지만, 120Hz 디스플레이에서는 한계가 존재함

---

## 💡 SwiftUI 성능 저하 원인

- ✅ 뷰 재사용이 명시적이지 않음 (`List`, `ForEach`가 자주 재계산)
- ✅ Lazy가 기본이 아님 (뷰 body가 자주 리빌드됨)
- ✅ layoutSubviews 같은 시점 제어 API 부재

---

## 🧠 SwiftUI 적절한 사용 시점

| 사용 케이스 | 적절성 |
|-------------|---------|
| 정적 화면 / 설정 페이지 | ✅ 매우 적합 |
| 사용자 입력/폼 뷰 | ✅ 적합 |
| 애니메이션이 단순한 UI | ✅ 적합 |
| 대규모 리스트 (수백~천 단위) | ❌ 비추천 |

---

## ✅ 결론 요약

| 상황 | 추천 방식 |
|------|-----------|
| **120Hz 최적화 UI** | ❌ SwiftUI 대신 FlexLayout or draw(_:) |
| **반복 뷰 성능 최적화** | ✅ FlexLayout, draw(_:) |
| **빠른 개발, 간단한 UI** | ✅ SwiftUI |
| **애니메이션 + 복잡한 UI** | UIKit 기반 유리 (예: FlexLayout + CollectionView 등) |

---

