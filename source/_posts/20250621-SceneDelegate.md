---
title: SceneDelegate
date: 2025-06-21 12:57:55
tags: SceneDelegate, AppDelegate, iOS
---

### 📘 AppDelegate vs SceneDelegate Lifecycle 정보 (최신 기준 반영)

---

#### 1. AppDelegate란?

- 앱의 전체 수명 주기를 관리하는 진입점
- 앱 시작, 종료, 푸시 등록, 백그라운드 전환 등 **전역 이벤트 처리**

**주요 메서드**
```swift
func application(_:didFinishLaunchingWithOptions:)
func applicationDidBecomeActive(_:)
func applicationWillTerminate(_:)
func application(_:didRegisterForRemoteNotificationsWithDeviceToken:)
```

---

#### 2. SceneDelegate란?

- iOS 13부터 도입된 **멀티 윈도우 (Scene)** 지원 구조
- 하나의 앱에서 **여러 화면(Scene)을 동시에 구성** 가능
- UI 구성, 화면 전환, 인스턴스 개체를 관리

**주요 메서드**
```swift
func scene(_:willConnectTo:options:)
func sceneDidBecomeActive(_:)
func sceneWillResignActive(_:)
func sceneDidDisconnect(_:)
```

---

#### AppDelegate vs SceneDelegate 비교

| 항목           | AppDelegate         | SceneDelegate                        |
|----------------|----------------------|--------------------------------------|
| 도입 시점      | iOS 초기부터         | iOS 13 이상                          |
| 역할           | 앱 전체 수명 주기 관리 | 화면(윈도우) 단위 수명 주기 관리       |
| UI 구성 위치   | `didFinishLaunching` | `scene(_:willConnectTo:)`           |
| 멀티 윈도우    | ✖️                   | ✅                                   |
| 필수 여부       | ✅ 항상 존재          | ✅ (Info.plist에 `SceneManifest` 필요) |

---

#### iOS 버전 대응 구조

- **iOS 12 이하**: `AppDelegate` 단독 사용 (기존 방식)
- **iOS 13 이상**: `SceneDelegate` 기반 구조가 기본값
- **iOS 14~17**: SceneDelegate가 기본으로 사용되며, AppDelegate만 사용하는 구조는 사실상 deprecated

> ⚠️ Info.plist의 `UIApplicationSceneManifest` 값이 존재하지 않으면 SceneDelegate는 실행되지 않음.

---

#### AppDelegate만 사용하는 구조는 deprecated 추세

| 항목       | 설명 |
|------------|------|
| 기존 구조   | AppDelegate에서 `window`를 설정하고 `rootViewController`를 직접 설정 |
| 현재 구조   | iOS 13+부터는 SceneDelegate가 UI 구성 및 수명 주기를 담당 |
| 주의 사항   | AppDelegate에서 UI를 설정해도 적용되지 않거나 crash가 발생할 수 있음 |

---

#### 핵심 요약

> `AppDelegate`는 앱의 전체 생명 주기를,  
> `SceneDelegate`는 iOS 13 이상에서 화면(윈도우)의 생명 주기를 관리.  
> iOS 14 이후부터는 SceneDelegate 기반 구조가 기본이며,  
> AppDelegate만 사용하는 구조는 사실상 deprecated 방향으로 전환되고 있는 추세.
