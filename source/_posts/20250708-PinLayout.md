---
title: PinLayout
date: 2025-07-08 22:00:02
category: 
- Swift
tags: 
- Layout
- PinLayout
---

# PinLayout:

PinLayout은 **Auto Layout을 사용하지 않고**, **순수 코드**로 UIView/NSView/CALayer의 레이아웃을 빠르게 구성할 수 있는 경량 라이브러리이다.

## 철학 및 핵심 원칙
1. **Manual layout**: Auto Layout을 사용하지 않고, 수동으로 뷰 프레임을 제어한다.
2. **간단하고 빠름**: 수동 레이아웃과 속도 면에서 유사하며, Auto Layout보다 8\~12배 빠르다.
3. **전체 제어권**: 사용자가 레이아웃 과정을 직접 수행한다. 마법 같은 블랙박스는 없다.
4. **단일 뷰 단위 처리**: 디버깅이 쉽고, 코드 간결성 유지 가능
5. **간결한 구문**: 대부분의 뷰를 한 줄로 설정할 수 있음

### 📦 마법 같은 블랙박스가 무슨말이지?
**Auto Layout (블랙박스 느낌 강함)**
* 제약 조건만 설정하면 시스템이 내부적으로 계산해서 레이아웃을 배치해준다.
* 하지만 왜 그 위치에 배치됐는지, 충돌이 나는 이유가 무엇인지 알기 어려울 수 있다.
* 즉, 내부 동작은 우리가 **직접 제어하거나 관찰하기 어렵다!!!** <<<<

**PinLayout (블랙박스 아님)**
* view.pin.top(10).left(10)처럼 명령어 하나하나로 직접 위치를 지정한다.
* 어떤 순서로 적용되고, 어떤 결과가 나오는지 전부 **내가 의도한 그대로**이다.
* 문제가 생기면 코드만 보면 어디가 잘못됐는지 명확히 알 수 있다.

## 단축 API
- `view.pin.top(10).left(10).bottom(10).right(10)` → 모든 방향에 대해 오프셋 지정
- `view.pin.all(10)` = 네 방향 모두 10pt 오프셋
- `view.pin.`
- `top()`, `left()`, `right()` 기본 기준은 superview
- 중앙 배치: `vCenter()`, `hCenter()`, `topRight()` 등 직관적인 배치 API 제공

## RTL / macOS 지원
- RTL 언어 지원 (오른쪽 → 왼쪽): `start()`, `end()` 등 활용
- macOS의 NSView, NSEdgeInsets도 동일 방식으로 지원

## 퍼포먼스
- 수동 레이아웃과 동일한 수준의 속도
- Auto Layout보다 8\~12배 빠른 성능

---



