---
title: HTTP Basic Access denied
date: 2022-11-25 14:01:28
category:
 - Git
tags: 
- Access
- Http
---

계정 비밀번호를 변경한것을 깜빡하고 git push 를 진행하던 도중 아래와 같은 에러가 발생했다.

```
remote: HTTP Basic: Access denied. The provided password or token is incorrect or your account has 2FA enabled and you must use a personal access token instead of a password.
```

git config –local –unset credential.helper
email과 password를 다시 입력
git push
성공 :)
