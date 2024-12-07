---
title: Fastlane env
date: 2023-03-13 14:10:14
tags: Fastlane
---

얼마전부터 fastlane 키체인 관련해서 계속 로그인이 풀리고, 앱암호를 넣었는데도 불구하고 계속 패스워드를 다시 입력하라고 해서 찾다가 성공한 방법 메모!

1. fastlane 폴더에 .env 파일 생성

2. 아래의 정보들을 넣은 후 저장
APPLE_ID=이메일주소
FASTLANE_PASSWORD=비밀번호
FASTLANE_APPLE_APPLICATION_SPECIFIC_PASSWORD=앱암호(애플에서 발급받은 abcd-dsad-fdsa 형태)

3. git status 후 .env파일이 업로드 안올라가는지 확인