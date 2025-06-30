---
title: Button.rx.tap Merge
date: 2020-02-11 13:14:09
category:
 - RxSwift
tags: 
- RxSwift
- ButtonEvent
- Merge
---
여러개의 버튼에 하나의 Action, Bind로 할수 있을까 해서 삽질하다가 성공한 코드!
조금만 더 깔끔하게 짤 수 있었으면 좋겠다 ㅎ…

```
Observable.merge(
    self.playlistEditButton.rx.tap.map{ $0 },
    self.editDoneButton.rx.tap.map{ $0 }
    )
    .throttle(.milliseconds(500), scheduler: MainScheduler.asyncInstance)
    .map { Reactor.Action.updatePlaylistEditing }
    .bind(to: reactor.action)
    .disposed(by: self.disposeBag)
```
