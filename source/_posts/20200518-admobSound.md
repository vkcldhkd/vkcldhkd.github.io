---
title: 애드몹 동영상광고
date: 2020-05-18 13:34:56
category:
 - iOS
tags: 
- Admob
- AudioSession
---

컬렉션뷰에 기존 아이템들과 광고 아이템을 섞어서 노출하던 중, 음악재생 중에 동영상 광고를 지나가게되면 노래가 꺼지는 버그가 있었다. 아무리 생각해도 오디오 세션 관련된 버그 같았는데 공식홈페이지에 이런 내용이 있었다.
하 이거때문에 엄청 검색하다가 겨우 찾음…. 하지만 조금 더 테스트가 필요하다ㅠ

```
https://developers.google.com/admob/ios/global-settings?hl=ko

오디오 세션 변경
오디오 세션을 통해 앱의 오디오 동작에 대한 개발자의 의도를 시스템에 반영할 수 있습니다. 오디오 세션에 대한 자세한 내용은 Apple의 오디오 세션 프로그래밍 가이드를 참조하세요. Google 모바일 광고 SDK 오디오를 관리하는 데 사용할 수 있는 옵션은 audioVideoManager 속성을 사용하는 것입니다.

앱에서 오디오를 사용하지 않으면 이러한 API를 사용할 필요가 없습니다. Google 모바일 광고 SDK가 오디오를 재생할 때 오디오 세션 카테고리를 자동으로 관리합니다. 앱에서 오디오를 재생하고 Google 모바일 광고 SDK가 오디오를 재생하는 방법과 시기를 더욱 정밀하게 제어하려는 경우 이러한 API가 도움이 될 수 있습니다.

오디오 동영상 관리자에서 직접 오디오 세션 카테고리를 관리해야 하는 경우 audioSessionIsApplicationManaged 속성을 YES로 설정하세요.

오디오 세션 카테고리를 관리하려면 광고 동영상 및 오디오 재생 이벤트를 알리도록 GADAudioVideoManagerDelegate를 구현하고 오디오 동영상 관리자에서 delegate 속성을 설정해야 합니다. 그런 다음 위에 연결된 Apple의 오디오 세션 프로그래밍 가이드에 따라 오디오 세션 카테고리를 관련 카테고리로 변경하세요.

참고: 기본적으로 모바일 광고 SDK는 광고를 음소거 상태로 재생할 때 오디오 세션 카테고리를 AVAudioSessionCategoryAmbient로 설정합니다. 이 경우에 앱에서 AVAudioSessionCategoryOptionDuckOthers를 사용하게 하려면 GADAudioVideoManagerDelegate 프로토콜을 구현하고 오디오 동영상 관리자 audioSessionIsApplicationManaged를 YES로 설정해야 합니다.
다음은 앱이 위의 API를 사용하여 음악을 재생할 때 권장하는 코드의 간단한 예입니다.


func setUp() {
    GADMobileAds.sharedInstance().audioVideoManager.delegate = self
    GADMobileAds.sharedInstance().audioVideoManager.audioSessionIsApplicationManaged = false
}
func myAppWillStartPlayingMusic() {
    // Mutes all Google video ads.
    GADMobileAds.sharedInstance().audioVideoManager.audioSessionIsApplicationManaged = true
    GADMobileAds.sharedInstance().applicationMuted = true
}
func myAppDidStopPlayingMusic() {
    // Un-mutes all of our video ads.
    GADMobileAds.sharedInstance().audioVideoManager.audioSessionIsApplicationManaged = false
    GADMobileAds.sharedInstance().applicationMuted = false
}

// MARK: - GADAudioVideoManagerDelegate
func audioVideoManagerWillPlayAudio(_ audioVideoManager: GADAudioVideoManager) {
    // The mobile ads SDK is notifying your app that it will play audio. You
    // could optionally pause music depending on your apps design.
    MyAppObject.sharedInstance().pauseAllMusic()
}

func audioVideoManagerDidStopPlayingAudio(_ audioVideoManager: GADAudioVideoManager) {
    // The mobile ads SDK is notifying your app that it has stopped playing
    // audio. Depending on your design, you could resume music here.
    MyAppObject.sharedInstance().resumeAllMusic()
}
```
