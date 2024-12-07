---
title: 특정 부분에만 CornerRadius 적용하기 
date: 2019-12-04 15:24:54
category:
 - iOS
tags:
 - iOS CornerRadius
 - Layer CornerRadius
 - Cusom  CornerRadius
 - layerMinXMinYCorner
 - layerMinXMaxYCorner
 - layerMaxXMinYCorner
 - layerMaxXMaxYCorner
 - maskedCorners
---
저번에 사용했었는데 구글에 또 검색하고 있어서 아예 포스팅 해버리기!
특정 부분만 제외 하고 CornerRadius를 적용하는 방법은 아래와 같다.

```
containerView.layer.cornerRadius = 16
containerView.layer.maskedCorners = [
    .layerMinXMaxYCorner,
    .layerMaxXMinYCorner,
    .layerMaxXMaxYCorner
]
```

- layerMinXMinYCorner : 상단 왼쪽
- layerMinXMaxYCorner : 상단 오른쪽
- layerMaxXMinYCorner : 하단 왼쪽
- layerMaxXMaxYCorner : 하단 오른쪽
