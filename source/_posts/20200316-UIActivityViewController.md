---
title: UIActivityViewController 정리
date: 2020-03-16 13:25:37
category:
 - iOS
tags: 
- UIActivityViewController
---

iOS에서 공유하기 버튼을 누를경우 하단에서 뷰가 올라오는것이 있는데, 그것이 UIActivityViewController이다.
UIActivityViewController에 대해서 옵션을 설정해보면서 알게된 내용 정리!

```
ActivityType

@available(iOS 6.0, *)
public static let postToFacebook: UIActivity.ActivityType // 페이스북

@available(iOS 6.0, *)
public static let postToTwitter: UIActivity.ActivityType // 트위터

@available(iOS 6.0, *)
public static let postToWeibo: UIActivity.ActivityType // 웨이보

@available(iOS 6.0, *)
public static let message: UIActivity.ActivityType // 메시지

@available(iOS 6.0, *)
public static let mail: UIActivity.ActivityType // 메일

@available(iOS 6.0, *)
public static let print: UIActivity.ActivityType // 프린트

@available(iOS 6.0, *)
public static let copyToPasteboard: UIActivity.ActivityType // 복사하기

@available(iOS 6.0, *)
public static let assignToContact: UIActivity.ActivityType // 전화번호부에 저장

@available(iOS 6.0, *)
public static let saveToCameraRoll: UIActivity.ActivityType // 사진첩에 저장

@available(iOS 7.0, *)
public static let addToReadingList: UIActivity.ActivityType // 읽기목록에 추가

@available(iOS 7.0, *)
public static let postToFlickr: UIActivity.ActivityType // 플리커(사진공유 커뮤니티)

@available(iOS 7.0, *)
public static let postToVimeo: UIActivity.ActivityType // 비메오

@available(iOS 7.0, *)
public static let postToTencentWeibo: UIActivity.ActivityType // 텐센트웨이보

@available(iOS 7.0, *)
public static let airDrop: UIActivity.ActivityType // 에어드랍

@available(iOS 9.0, *)
public static let openInIBooks: UIActivity.ActivityType // 아이북스

@available(iOS 11.0, *)
public static let markupAsPDF: UIActivity.ActivityType // PDF로 저장
```
