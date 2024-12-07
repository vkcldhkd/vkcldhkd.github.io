---
title: AppStore 수출 규정 준수
date: 2020-03-20 13:32:20
tags: iOS
---

테스트플라이트에서 앱을 테스트 하기위해 앱을 올렸는데 수출규정준수 관련한 문구가 떠있어서 테스트가 불가능했다.
기존 암호화 구성이 변경되지 않은경우, info.plist에 아래의 정보를 넣으면 수출규정준수에 허용할 필요 없이 바로 가능하다.

```
<key>ITSAppUsesNonExemptEncryption</key><false/>
```