---
title: Xcode에서 debug Flag 관리
date: 2020-07-22 13:44:45
tags: iOS
---

```
#if DEBUG
#else
#endif
```

이런 구문을 자주 사용하게되는데, 이런 플레그는 App Target -> Build Settings -> Other Swift Flags에서 -D DEBUG를 추가하면 된다! 구분 잘 해야함!!!