---
title: Google DynamicLinks
date: 2020-03-17 13:33:11
category:
 - iOS
tags: 
- DynamicLinks
---

DynamicLinks 쓸일이 생겨서 정리!
https://firebase.google.com/docs/dynamic-links/create-manually?hl=ko

```
ibi : 링크를 여는 데 사용할 iOS 앱의 번들 ID입니다. Firebase 콘솔의 개요 페이지에서 앱을 프로젝트에 연결해야 합니다. 동적 링크를 통해 iOS 앱을 여는 데 필요합니다.

ifl : 앱이 설치되지 않은 경우에 열리는 링크입니다. 앱이 설치되지 않았을 때 App Store에서 앱을 설치하는 것 이외에 콘텐츠의 모바일 웹 버전 열기, 앱 홍보 페이지 표시 등의 다른 작업을 수행하려는 경우 이 매개변수를 지정합니다.

ius : 앱의 번들 ID와 다른 값으로 정의하면 앱의 맞춤 URL 스키마로 사용됩니다.

ipfl : 앱이 설치되지 않은 경우 iPad에서 열리는 링크입니다. 앱이 설치되지 않았을 때 App Store에서 앱을 설치하는 것 이외에 콘텐츠의 웹 버전 열기, 앱 홍보 페이지 표시 등의 다른 작업을 수행하려면 이 매개변수를 지정합니다.

ipbi : iPad에서 링크를 여는 데 사용할 iOS 앱의 번들 ID입니다. Firebase 콘솔의 개요 페이지에서 앱을 프로젝트에 연결해야 합니다.

isi : 앱이 설치되지 않았다면 사용자를 App Store로 보내는 데 사용되는 앱의 App Store ID입니다.

imv : 링크를 열 수 있는 앱의 최소 버전 번호입니다. 이 플래그는 앱이 열렸을 때 앱에 전달되며 앱에서 수행할 작업을 결정해야 합니다.
```
