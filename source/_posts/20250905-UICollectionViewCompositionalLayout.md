---
title: 20250905-UICollectionViewCompositionalLayout
date: 2025-09-05 23:25:20
category:
- iOS
tags:
- UICollectionViewLayout
- CompositionalLayout
---

# UICollectionViewCompositionalLayout

iOS 13+에서 도입된 ****CompositionalLayout****은 `Item → Group → Section`을 선언적으로 조립하여, 섹션마다 완전히 다른 레이아웃(캐러셀, 그리드, 모자이크, 배경, 핀 헤더 등)을 구현할 수 있는 강력한 레이아웃 엔진이다.

---

## 1) 핵심 구성 요소
- ****NSCollectionLayoutItem****: 셀 1개의 규격(사이즈, 인셋)
- ****NSCollectionLayoutGroup****: 아이템들을 가로/세로/커스텀으로 묶는 컨테이너
- ****NSCollectionLayoutSection****: 그룹을 배치하고, 스크롤/헤더/푸터/배경/간격 등을 제어
- ****Supplementary****: 헤더/푸터. `pinToVisibleBounds = true`로 고정(Sticky) 가능
- ****Decoration****: 섹션 배경 등 장식 요소(데이터와 무관)
- ****Environment****: `sectionProvider`에서 전달되는 `NSCollectionLayoutEnvironment`로 화면폭/Size Class에 따라 ****열 수/간격/사이즈****를 동적으로 조정

---

## 2) 사이징 규칙(중요)
- ****.fractionalWidth/Height****: 부모 대비 비율
- ****.absolute****: 고정 크기
- ****.estimated****: 셀 자체가 Auto Layout으로 크기를 정함(셀프 사이징)
> 과도한 `estimated`와 깊은 중첩 그룹은 레이아웃 계산 비용 증가 → 캐러셀은 ****absolute****, 그리드는 ****fractional + 적절한 높이**** 권장

---

## 3) 자주 쓰는 옵션
- `section.orthogonalScrollingBehavior`: 세로 피드 안에서 ****가로 캐러셀****
  - `.continuous`, `.groupPaging`, `.groupPagingCentered`, `.paging`
- `section.boundarySupplementaryItems`: 헤더/푸터 + `pinToVisibleBounds = true`
- `NSCollectionLayoutDecorationItem.background(elementKind:)`: 섹션 배경 등록

---

## 4) 기본 스켈레톤
```swift
let layout = UICollectionViewCompositionalLayout { sectionIndex, env in
    switch sectionIndex {
    case 0: return carouselSection()
    case 1:
        let columns = env.container.effectiveContentSize.width > 600 ? 3 : 2
        return gridSection(columns: columns)
    default: return listSection()
    }
}
```

### (A) 캐러셀 섹션(가로 스크롤)
```swift
func carouselSection() -> NSCollectionLayoutSection {
    let item = NSCollectionLayoutItem(
        layoutSize: .init(widthDimension: .absolute(280), heightDimension: .absolute(160))
    )
    item.contentInsets = .init(top: 0, leading: 8, bottom: 0, trailing: 8)

    let group = NSCollectionLayoutGroup.horizontal(
        layoutSize: .init(widthDimension: .absolute(280), heightDimension: .absolute(160)),
        subitems: [item]
    )

    let section = NSCollectionLayoutSection(group: group)
    section.orthogonalScrollingBehavior = .groupPagingCentered
    section.contentInsets = .init(top: 16, leading: 16, bottom: 16, trailing: 16)
    return section
}
```

### (B) 반응형 그리드 섹션(2~3열)
```swift
func gridSection(columns: Int) -> NSCollectionLayoutSection {
    let fraction = 1.0 / CGFloat(columns)

    let item = NSCollectionLayoutItem(
        layoutSize: .init(widthDimension: .fractionalWidth(fraction),
                          heightDimension: .estimated(220))
    )
    item.contentInsets = .init(top: 8, leading: 8, bottom: 8, trailing: 8)

    let group = NSCollectionLayoutGroup.horizontal(
        layoutSize: .init(widthDimension: .fractionalWidth(1.0),
                          heightDimension: .estimated(220)),
        subitems: Array(repeating: item, count: columns)
    )

    let section = NSCollectionLayoutSection(group: group)
    section.contentInsets = .init(top: 16, leading: 16, bottom: 16, trailing: 16)
    return section
}
```

### (C) 리스트 섹션(간단/내장 형태)
```swift
func listSection() -> NSCollectionLayoutSection {
    var config = UICollectionLayoutListConfiguration(appearance: .insetGrouped)
    config.headerMode = .supplementary
    return NSCollectionLayoutSection.list(using: config, layoutEnvironment: .init())
}
```

### (D) 핀 헤더 & 섹션 배경
```swift
// 헤더
let header = NSCollectionLayoutBoundarySupplementaryItem(
    layoutSize: .init(widthDimension: .fractionalWidth(1.0), heightDimension: .absolute(44)),
    elementKind: UICollectionView.elementKindSectionHeader,
    alignment: .top
)
header.pinToVisibleBounds = true
section.boundarySupplementaryItems = [header]

// 배경
let bg = NSCollectionLayoutDecorationItem.background(elementKind: "SectionBackground")
section.decorationItems = [bg]
// 사용 전: layout.register(BackgroundView.self, forDecorationViewOfKind: "SectionBackground")
```

---

## 5) Pinterest(워터폴) 스타일
- ****iOS 16+****: `NSCollectionLayoutGroupCustom`으로 ****진짜 워터폴**** 구현 가능(아이템별 frame 직접 계산)
- ****iOS 13~15****: 완전한 워터폴은 어렵기 때문에 ****패턴 반복****(큰+작은 카드 조합)으로 근사하거나 ****커스텀 레이아웃**** 사용

간단 샘플(iOS 16+ 핵심만):
```swift
if #available(iOS 16.0, *) {
    let custom = NSCollectionLayoutGroup.custom(
        layoutSize: .init(widthDimension: .fractionalWidth(1.0), heightDimension: .estimated(1))
    ) { env in
        var frames: [NSCollectionLayoutGroupCustomItem] = []
        let width = env.container.effectiveContentSize.width
        let spacing: CGFloat = 16
        let colWidth = (width - spacing) / 2

        var colHeights: (left: CGFloat, right: CGFloat) = (0, 0)
        for idx in 0..<env.subitemCount {
            let isLeft = colHeights.left <= colHeights.right
            let x = isLeft ? 0 : colWidth + spacing
            let y = isLeft ? colHeights.left : colHeights.right
            let h = /* 사전 계산 or 추정 높이 */ CGFloat(220)

            let frame = CGRect(x: x, y: y, width: colWidth, height: h)
            frames.append(.init(frame: frame))

            if isLeft { colHeights.left += h + spacing }
            else { colHeights.right += h + spacing }
        }

        env.contentSize = CGSize(width: width, height: max(colHeights.left, colHeights.right))
        return frames
    }

    let section = NSCollectionLayoutSection(group: custom)
    section.contentInsets = .init(top: 16, leading: 16, bottom: 16, trailing: 16)
    section.interGroupSpacing = 16
}
```

---

## 6) 성능/품질 팁
- ****Self-Sizing는 필요한 곳에만****. `estimated` 값은 너무 작지도/크지도 않게 설정
- ****캐러셀****: 아이템/그룹을 `absolute`로 고정하여 계산 단순화 + 스냅 느낌은 `.groupPagingCentered`
- ****핀 헤더/배경****: 높이/인셋을 고정하면 재계산 비용 감소
- 큰 ****cornerRadius/마스크/그림자****는 오프스크린 렌더링 → 컨테이너 분리, 재설정 최소화
- ****Environment 활용****: `env.container.effectiveContentSize` 기반으로 열 수/간격 동적 조절(아이패드 대응)
- `visibleItemsInvalidationHandler`로 패럴랙스/스케일 등 효과 가능(과용 주의)

---

## 7) 선택 가이드
- iOS 13+만 타깃이고 섹션별로 다른 레이아웃이 필요 → ****CompositionalLayout****
- 단순 리스트/그리드, 레거시(iOS 12↓) 호환 중요 → ****FlowLayout**** 또는 `UICollectionLayoutListConfiguration`
- 가로 캐러셀/섹션 배경/핀 헤더/복합 배치 → ****CompositionalLayout****

---

## 체크리스트하면 좋은 것들!!
- [ ] 섹션별 목적을 정의했는가? (배너/카테고리/피드/리스트)
- [ ] 사이즈는 fractional/absolute/estimated 중 어떤 기준이 적합한가?
- [ ] iPad/가로모드에서 열 수가 자연스럽게 변하는가? (`Environment` 반영)
- [ ] 헤더/배경/핀 고정이 필요한가?
- [ ] Self-Sizing이 필요한 셀만 `estimated`를 쓰는가?
- [ ] 스크롤 성능 측정(레벨/오프스크린/이미지 디코딩) 완료했는가?
