---
title: UIBarButtonItem의 TintColor가 초기화 되는 현상
date: 2020-02-17 13:18:24
category:
 - iOS
tags: 
- TintColor
- UIBarButtonItem
---

NavigationBar에 아래와 같이 설정을 해줬는데도 페이지 이동 시 설정한 UIBarButtonItem의 TintColor가 초기화 되는 이슈가 생겼다 -_____-

```
UINavigationBar.appearance().backIndicatorImage = arrwoImg
UINavigationBar.appearance().backIndicatorTransitionMaskImage = arrwoImg
UINavigationBar.appearance().barTintColor = ColorHelper.C38_38_38.getColor()
UINavigationBar.appearance().tintColor = .white
UINavigationBar.appearance().titleTextAttributes = [NSAttributedString.Key.foregroundColor : UIColor.white]
```
초기화는 기본색상인 파란색? 계열로 되는데 도저히 찾지를 못하다가 아래처럼 barbuttonItem의 then 부분에 tintColor를 설정해주니 정상적으로 잘 됐다 -_-

```
static func getSearchBarButton() -> UIBarButtonItem{
    return UIBarButtonItem().then{
        $0.image = R.image.common_Search()
        $0.style = .plain
        $0.tintColor = .white
    }
}
```
하 진짜 이것때문에 몇시간을 날린건지…………………………..
