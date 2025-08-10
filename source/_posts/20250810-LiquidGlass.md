---
title: Liquid Glass 대비 체크리스트
date: 2025-08-10 23:31:10
category: 
- iOS
tags:
- Liquid Glass
- UIKit
---

# Liquid Glass 대비 체크리스트
리퀴드 글래스 업데이트를 대비하기 전, 확인해봐야할 내용들을 GPT 통해서 남겨두기!
테스트를 해본것은 아니라서, 추후 테스트 필요!!!

**요지**: iOS 전반의 시각 언어가 더 “재질(material)·투명도·동적 애니메이션” 중심으로 진화할 때, **UIKit 기반 앱**은 큰 개편 없이도 준비할 수 있습니다. 핵심은 **시스템 재질·의미론적 색상·적응형 타이포그래피·바(Bar) 외형 API·접근성·퍼포먼스**를 올바르게 사용하는 것입니다.

## 1) 디자인 원칙 리셋
* **플랫 색상 하드코딩 금지** → UIColor의 **의미론적 색상** 사용 (.label, .systemBackground, .secondarySystemGroupedBackground 등).
* **이미지형 블러/그라데이션 제거** → **시스템 재질(Material)**·**블러**로 치환 (UIBlurEffect, UIVibrancyEffect, UICellConfiguration 계열).
* **고정 불투명 바 금지** → UINavigationBarAppearance·UITabBarAppearance로 **투명/반투명/불투명** 모드 전환.
* **자체 애니메이션 최소화** → UIViewPropertyAnimator·UISpringTimingParameters 사용, **감쇠(damping)**·**접근성 모션 감축** 대응.

⠀
## 2) 테마·색상 체계 정리
### ✅ 해야 할 일
* 전역 팔레트에서 **의미론적 색상으로 매핑**: 커스텀 색→UIColor(named:) + **다크 모드/하이 콘트라스트 변형** 제공.
* **SF Symbols** 우선 사용: 두께·스케일을 **Dynamic Type**과 함께 조정.

⠀💡 샘플
```swift
// 의미론적 색상 사용
view.backgroundColor = .systemBackground
titleLabel.textColor = .label

// 심볼 가변 두께/크기
let config = UIImage.SymbolConfiguration(textStyle: .body, scale: .medium)
iconView.image = UIImage(systemName: "paperplane.fill", withConfiguration: config)
```

## 3) 바(Bar)·툴바 외형: Appearance API 통일
### ✅ 해야 할 일
* UINavigationBarAppearance, UITabBarAppearance, UIToolbarAppearance로 **스크롤 경계/표준/컴팩트** 상태 별 외형 설정.
* 투과 효과가 필요한 화면은 **투명 + 재질 배경** 조합을 사용.
* 스크롤에 따라 **표준→불투명** 전환이 부드럽게 이뤄지도록 구성.

⠀💡 샘플
```swift
let nav = UINavigationBarAppearance()
nav.configureWithTransparentBackground() // 투명 기반
nav.backgroundEffect = UIBlurEffect(style: .systemMaterial) // 재질 적용
nav.backgroundColor = UIColor.systemBackground.withAlphaComponent(0.6)
nav.titleTextAttributes = [.foregroundColor: UIColor.label]

let scrollEdge = UINavigationBarAppearance()
scrollEdge.configureWithDefaultBackground() // 스크롤 엣지에서 불투명 전환

let bar = navigationController?.navigationBar
bar?.standardAppearance = nav
bar?.scrollEdgeAppearance = scrollEdge
bar?.compactAppearance = nav
bar?.tintColor = .label
```

## 4) 리스트/그룹 화면: 시스템 구성 활용
* **UITableView/UICollectionView**는 **시스템 그룹 배경**과 **셀 구성(Compositional Configurations)** 사용.
* UIListContentConfiguration, UICellAccessory로 텍스트·부제목·보조 아이콘·배지를 시스템 룩으로.

```swift
var content = UIListContentConfiguration.subtitleCell()
content.text = "알림"
content.secondaryText = "새 메시지 3개"
cell.contentConfiguration = content
cell.backgroundConfiguration = UIBackgroundConfiguration.listGroupedCell()
```

## 5) 배경·블러·투명도 지침
* **블러**는 UIVisualEffectView 사용, 투명 PNG/그라데이션 대체.
* **레이어 수 최소화**: 과도한 반투명 뷰 중첩은 **오버드로우**↑·**오프스크린 렌더링**↑.
* **대비 확보**: 블러 뒤 텍스트는 vibrancy 혹은 세컨더리 색상이 아닌 **.label** 사용.

```swift
let blur = UIBlurEffect(style: .systemMaterial)
let blurView = UIVisualEffectView(effect: blur)
blurView.frame = view.bounds
blurView.autoresizingMask = [.flexibleWidth, .flexibleHeight]
view.addSubview(blurView)

let vibrant = UIVibrancyEffect(blurEffect: blur)
let vibrantView = UIVisualEffectView(effect: vibrant)
blurView.contentView.addSubview(vibrantView)
```

## 6) 타이포그래피 & 동적 크기
* UIFont.preferredFont(forTextStyle:) + **Dynamic Type**.
* **줄바꿈·트렁케이션**을 텍스트 스타일별로 재검토.
* **언어 확장** 대비: 긴 문자열, 다국어 줄바꿈(한·일·영).

```swift
titleLabel.font = .preferredFont(forTextStyle: .title2)
titleLabel.adjustsFontForContentSizeCategory = true
```

## 7) 애니메이션·전이(Transition) 가이드
* **유저 주도 상호작용** 위주: UICubicTimingParameters, UISpringTimingParameters.
* **Reduce Motion** 대응: UIAccessibility.isReduceMotionEnabled 분기.
* **시트 전환**은 UISheetPresentationController.Detent 활용(중간/큰/커스텀).

```swift
let params = UISpringTimingParameters(dampingRatio: 0.85)
let animator = UIViewPropertyAnimator(duration: 0.35, timingParameters: params)
animator.addAnimations { self.panel.transform = .identity }
animator.startAnimation()
```

## 8) 접근성·가독성
* **명암비**: UIColor 의미론 색 + 다크 모드 대비 확인.
* **모션 감축**: 파티클·패럴랙스 비활성화.
* **버튼 영역**: 최소 44×44pt.
* **VoiceOver**: 핵심 흐름 라벨링, 동적 콘텐츠 UIAccessibility.post(notification:).

```swift
if UIAccessibility.isReduceMotionEnabled {
    // 저모션용 단순 전환
    view.layer.removeAllAnimations()
}
```

## 9) 성능·품질 측정 (Instruments/디버그 HUD)
* **Core Animation**: 레이어 리빌·오버드로우(빨/녹/보) 확인.
* **Time Profiler**: 셀 구성·이미지 디코딩 병목.
* **메모리 그래프**: 재질/블러 중첩로 인한 뷰 누수.
* **GPU Frame Capture (Xcode)**: 반투명 중첩로 인한 드로우 콜 증가.

⠀체크리스트
* 스크롤 120Hz 환경에서 **프레임 드랍 없음**(iPhone Pro).
* 첫 화면 표시 TTI(초) 목표치 충족.
* **크래시 없는 다크 모드 전환**(설정 토글 실시간 테스트).
* 상하 스크롤 시 바(Bar) 외형 전이 **티어링 없음**.

⠀
## 10) 마이그레이션 전략
1. **UIAppearance 전역 치환** → **Appearance API 개별 스크린 설정**으로 이동.
2. **색상/폰트 하드코딩 제거** → 의미론·텍스트 스타일 적용.
3. **이미지형 블러/그라데이션 제거** → 시스템 재질/효과로 대체.
4. **바 외형 통합**: 내비/탭/툴바 전이 일관화.
5. **접근성·성능 점검**: Reduce Motion/Contrast, 오버드로우 최소화.

⠀
## 11) 리스크 관리 & 롤아웃
* **Feature Flag**로 화면 단위 단계적 적용(신규 홈→디테일→설정).
* **A/B**: 불투명 vs 반투명 상단바 유지시간/이탈률 비교.
* **Fallback**: 구형 디바이스(iPhone 11 이하)에는 블러 강도 축소.

⠀
## 12) 점검용 PR 템플릿 (발췌)
* 의미론적 색상/텍스트 스타일만 사용
* Appearance API로 바 외형 정의(표준/스크롤엣지)
* Reduce Motion/Contrast 검증 스크린샷 첨부
* Instruments 캡처(Overdraw, CA Layers) 첨부
* 다국어 레이아웃 캡처(ko/ja/en) 첨부

⠀
## 부록) 흔한 안티패턴
* ❌ 캔버스 전체에 알파 0.6 오버레이 → **오버드로우 폭증**
* ❌ PNG 섀도우/글로우 → **CALayer.shadowPath**·**shadowOpacity**로 대체
* ❌ 상태별 전역 tintColor 강제 → **컴포넌트 단위**로 지정
* ❌ 커스텀 네비게이션 바 → 시스템 전이를 깨뜨림

⠀
### 결론
UIKit 앱은 **“시스템이 주도하는 재질·색·타이포그래피·전이”**만 제대로 따르면 큰 리스크 없이 차세대 iOS 룩앤필을 얻습니다. 먼저 **색/폰트 하드코딩 제거 → Appearance 통일 → 블러/재질 도입 → 접근성/성능 튜닝** 순으로 점진적 적용을 추천합니다.
