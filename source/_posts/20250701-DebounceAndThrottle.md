---
title: Debounce와 Throttle
date: 2025-07-01 15:30:17
category:
 - RxSwift
tags: 
- debounce
- throttle
---


# RxSwift :
`RxSwift`에서 `debounce`와 `throttle`은 이벤트의 빈도를 제어하기 위해 사용하는 **시간 기반 연산자**이다. 두 연산자는 비슷해 보일 수 있지만 ****동작 방식과 사용 목적****이 다른데, 이를 보다 자세히 구분하기해서 작성!

---

## 차이점
| 구분 | `debounce` | `throttle` |
|------|------------|------------|
| 의미 | 지정한 시간 동안 ****새로운 이벤트가 없을 때****, 가장 ****마지막 이벤트****만 방출 | 지정한 시간 동안 ****첫 번째 이벤트만**** 방출하고, 이후 이벤트는 무시 |
| 목적 | ****최종 입력****만 반영 | ****이벤트 속도 제한**** |
| 사용 시점 | 사용자 입력이 ****끝났을 때 감지**** | ****연속된 이벤트를 일정 간격으로 제한****하고 싶을 때 |
| 무시 방식 | 시간 내 발생한 ****모든 이벤트 무시****, 마지막 하나만 방출 | 주어진 시간 동안 ****처음 하나만 허용****, 이후는 무시 |
| 대표 사용 예 | 검색 자동완성, 키 입력 감지 | 버튼 연타 방지, 중복 API 호출 방지 |

---

## debounce 예시

```swift
self.textField.rx.text
    .debounce(.milliseconds(500), scheduler: MainScheduler.instance)
    .distinctUntilChanged()
    .subscribe(onNext: { print("검색어: \($0)") })
    .disposed(by: self.disposeBag)
```

- 사용자가 입력을 멈춘 후 0.5초가 지나면 마지막 입력만 전달되는 예시

---

## throttle 예시

```swift
self.downloadButton.rx.tap
    .throttle(.milliseconds(600), scheduler: MainScheduler.asyncInstance)
    .map { Reactor.Action.download }
    .bind(to: reactor.action)
    .disposed(by: self.disposeBag)
```

- 버튼이 연속적으로 눌려도, 0.6초 간격으로 한 번만 이벤트가 발생되는 예시

---
