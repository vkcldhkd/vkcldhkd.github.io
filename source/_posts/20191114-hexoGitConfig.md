---
title: Hexo GitConfig
date: 2019-11-14 13:59:35
category:
- Hexo
tags:
- Git
- Config
---

Hexo deploy안에 name과 email을 설정해도 글로벌으로 설정되어있지 않다면 깃헙에 잔디가 심어지지않았다 (name,email이 설정되지 않았다는 뜻)

.deploy_git 폴더로 이동 후 아래의 명령어를 추가했다.

```
git config user.name vkcldhkd
git config user.email vkcldhkd@gmail.com
```

잔디 성공!
