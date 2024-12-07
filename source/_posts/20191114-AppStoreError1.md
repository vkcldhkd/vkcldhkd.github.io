---
title: 앱스토어 업로드 Token 오류
date: 2019-11-14 12:40:17
category:
 - AppStore
tags:
 - Token Error
 - delivery Error
---

```
cannot proceed with delivery: an existing transporter instance is currently uploading this package
```

위와같은 에러가 날 경우 /Users/유저명/.itmstransporter/UploadTokens/ 경로로 들어가서
토큰값을 지워준 후 다시 시도하면 깔끔하게 성공!
