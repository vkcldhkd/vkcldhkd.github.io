---
title: Transition과 Animation
date: 2020-07-21 13:44:10
category:
 - iOS
tags: 
- Transition
- Animation
---

transition 속성과 animation 속성의 가장 큰 차이는 transition 속성은 요소의 상태가 바뀌어야 바뀌는 상태를 애니메이션으로 표현하지만, animation 속성은 요소의 상태 변화와 상관 없이 애니메이션을 실행한다.

그리고 UIView.animate를 할 때,transition으로 시작하는 옵션값들을 넣으면 아무 효과 없이, cureveEaseInOut으로 동작하게된다.

transition 속성을 이용하려면 UIView.transition으로 해야 제대로 동작한다. 이럴거면 UIView.AnimationOptions 를 나눠두는게 낫지않나싶은데… 애플의 큰뜻이 있겠지 ㅠ
