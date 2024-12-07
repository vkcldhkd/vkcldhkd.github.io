---
title: ReorderImageView TintColor
date: 2020-03-09 13:22:27
tags: iOS
---

TableView Editing Mode 를 변경해서 사용하는 도중, CustomCell의 백그라운드색상이 흰색일 경우 테이블뷰에서 Reorder 부분이 잘 안보이는 이슈가 있었다. 이래저래 방법을 찾던 도중 아래와같은 방법으로 해결했다! 후 ㅂㄷㅂㄷ…

```
self.detailTableView.rx.willDisplayCell
    .subscribe(onNext: { tuple in
        let cell = tuple.cell
        let imageView = cell.subviews.first(where: { $0.description.contains("Reorder") })?.subviews.first(where: { $0 is UIImageView }) as? UIImageView
        imageView?.image = imageView?.image?.withRenderingMode(.alwaysTemplate)
        imageView?.tintColor = ColorHelper.C33_33_33.getColor()
    })
    .disposed(by: self.disposeBag)
```