---
title: ImageTintColor
date: 2020-03-19 13:29:38
category:
 - iOS
tags: 
- ImageTintColor
---

가끔 이미지 하나로 UIColor를 입혀서 사용해야할 때가있다.
자꾸 까먹어서 아예 포스팅!

```
let button = UIButton()
let image = UIImage(named: "image_name")?.withRenderingMode(.alwaysTemplate)
button.setImage(image, for: .normal)
button.tintColor = UIColor.red
```
