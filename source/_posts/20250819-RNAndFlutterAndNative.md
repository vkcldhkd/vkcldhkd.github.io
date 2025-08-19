---
title: React Native & Flutter vs Native
date: 2025-08-19 23:27:09
category:
- iOS
tags:
- RN
- Flutter
- Native
- iOS
---

# React Native & Flutter vs Native

React Native와 Flutter는 크로스플랫폼 앱 개발을 빠르게 확산시키고 있지만, 아직 네이티브 개발을 완전히 대체할 수 없다고 생각하긴 했는데, 어느정도 까지 대체가 가능하고 어느부분은 대체가 어려운지 명확히 알아보기 위해 글 작성!

---

## 🔹 네이티브 기능이 필요한 프로젝트 유형

1. **고성능 실시간 그래픽/미디어 처리**
   - AR/VR, 게임 엔진, 실시간 영상 스트리밍.
   - 예: ARKit, Metal, SceneKit, Unity.
   - RN/Flutter → 브릿지 오버헤드로 성능 저하 발생.

2. **하드웨어 밀접 제어가 필요한 앱**
   - 블루투스 저전력(BLE), IoT, CarPlay, HealthKit, CoreMotion, CoreML.
   - 예: 의료 기기 연동, 자동차 인포테인먼트, 웨어러블 실시간 데이터.

3. **최신 OS 기능 반영이 중요한 경우**
   - iOS: 위젯, Dynamic Island, Vision Pro.
   - Android: Jetpack Compose 최신 API.
   - 크로스 플랫폼은 SDK 업데이트 지연.

4. **고주사율·저지연 UI/UX**
   - 120Hz 스크롤, 커스텀 제스처, 실시간 카메라 필터.
   - 네이티브는 Metal, Core Animation 등 직접 최적화 가능.

5. **복잡한 백그라운드 작업**
   - 오프라인 데이터 동기화, 백그라운드 네트워킹, 위치 추적, Push Notification 정교한 제어.
   - RN/Flutter → 네이티브 모듈로 작성 필요.

---

## ✅ 장점

### 공통 장점
- 크로스 플랫폼 개발 (iOS/Android 동시 배포).
- Hot Reload / Hot Restart → 빠른 UI 피드백.
- 풍부한 패키지 생태계 (npm, pub.dev).
- 개발/유지 비용 절감.

### React Native 장점
- 웹/JS 생태계 활용 가능.
- 웹 개발자 진입 장벽 낮음.
- 네이티브 UI 브릿지 사용 → 플랫폼스러운 UI 제공.

### Flutter 장점
- 자체 렌더링 엔진(Skia) → 일관된 UI/UX.
- JS 브릿지 없음 → 성능 우위.
- Widget 기반 선언형 UI → 높은 커스터마이징 자유도.

---

## ❌ 단점

### React Native 단점
- JS 브릿지 오버헤드 → 성능 병목 발생.
- 플랫폼별 UI 일관성 부족.
- 고급 기능은 Swift/Kotlin 네이티브 모듈 필요.

### Flutter 단점
- 앱 사이즈 큼 (엔진 포함).
- 네이티브 UI 느낌 부족 가능.
- 최신 OS 기능 지원 지연.

---

## 📌 결론

- **일반적인 CRUD 앱, MVP, 스타트업 서비스** → React Native / Flutter 적합.
- **고성능 멀티미디어, 실시간 시스템, 하드웨어 밀접 기능** → 네이티브 필요.
- **대규모 서비스** → 공통 UI는 RN/Flutter, 성능/네이티브 의존 기능은 Swift/Kotlin 모듈로 하이브리드 전략 사용.
