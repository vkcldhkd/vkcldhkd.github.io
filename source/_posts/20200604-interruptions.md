---
title: Audio Session Interruptions
date: 2020-06-04 13:37:17
tags: iOS
---

클래식 매니저를 개발중에 버그리포트가 들어왔다.
내용은 바로 음악 재생중에 전화를 걸고 난 후 통화가 종료되었을 때 다른앱들은 노래가 나오는데, 왜 클래식매니저는 나오지 않느냐 라는 리포트였다.

맨 처음에는 AppDelegate LifeCycle쪽을 의심했었는데 얻을 수 있는 단서가 없었다 ㅠ….
위의 리포트 내용을 보고 곰곰히 생각해보니, 위와 같은 상황은 앱단에서 체크하는것이 아닌, OS단에서 체크를 해서 앱에 전달해줘야하지않을까??? 라는 생각이 뽷 들었다ㅋㅋㅋㅋ

그래서 애플 공식홈페이지에서 [AudioSession](https://developer.apple.com/documentation/avfoundation/avaudiosession)쪽을 보던 중 [Audio Session Interruptions](https://developer.apple.com/documentation/avfaudio/handling-audio-interruptions)을 발견했다.

```
func setupNotifications() {
    // Get the default notification center instance.
    let nc = NotificationCenter.default
    nc.addObserver(self,
                   selector: #selector(handleInterruption),
                   name: AVAudioSession.interruptionNotification,
                   object: nil)
}

@objc func handleInterruption(notification: Notification) {
    // To be implemented.
}
```


위의 코드를 이용해서 로그를 찍어본 결과

```
재생 중이고, 전화왔을 때
notification :name = AVAudioSessionInterruptionNotification, object = Optional(<AVAudioSession: 0x283023550>), userInfo = Optional([AnyHashable("AVAudioSessionInterruptionTypeKey"): 1])

재생 멈춘 상태이고 전화 끊어졌을 때
notification :name = AVAudioSessionInterruptionNotification, object = Optional(<AVAudioSession: 0x283023550>), userInfo = Optional([AnyHashable("AVAudioSessionInterruptionTypeKey"): 0, AnyHashable("AVAudioSessionInterruptionOptionKey"): 1])
```
이런 값들로 들어왔다. 그래서 아래와 같이 마무리를 했더니 잘 된다 :)

```
@objc func handleInterruption(notification: Notification) {        
    guard let userinfo = notification.userInfo,
        let interruptionOptionKey = userinfo[AVAudioSessionInterruptionOptionKey] as? Int,
        interruptionOptionKey == 1 else { return }
        
        ModernPlayerManager.shared.play()
}
```

++++++
아래의 코드로 개선했다.

```
@objc func handleInterruption(notification: Notification) {        
    LogHelper.printLog("handleInterruption")
    guard let value = (notification.userInfo?[AVAudioSessionInterruptionTypeKey] as? NSNumber)?.uintValue,
        let interruptionType =  AVAudioSession.InterruptionType(rawValue: value) else {
            LogHelper.printLog("AVAudioSessionInterruptionTypeKey : \(String(describing: notification.userInfo?[AVAudioSessionInterruptionTypeKey]))")
            return
    }

    switch interruptionType {
    case .began:
        LogHelper.printLog("began")
        ModernPlayerManager.shared.pause()
        LogHelper.printLog("audioPlayer.playing \(ModernPlayerManager.shared.player.state == .playing)")
        
    default :
        LogHelper.printLog("ended")
        if let optionValue = (notification.userInfo?[AVAudioSessionInterruptionOptionKey] as? NSNumber)?.uintValue,
            AVAudioSession.InterruptionOptions(rawValue: optionValue) == .shouldResume {
            LogHelper.printLog("should resume")
            ModernPlayerManager.shared.play()
        }
    }
}
```