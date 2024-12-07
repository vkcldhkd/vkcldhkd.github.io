---
title: RxSwift Scheduler
date: 2024-03-11 14:12:24
tags: RxSwift
---

| 스케줄러 타입      | 용도 | 특징          |
|:---------|-----:|:-------------:|
| MainScheduler   |   UI 작업 등 메인 스레드에서 실행해야 하는 작업 스케줄링 | MainScheduler.instance는 즉시 실행, MainScheduler.asyncInstance는 항상 비동기적으로 실행         |
| ConcurrentDispatchQueueScheduler   |   병렬 실행 작업 스케줄링 | GCD의 DispatchQueue를 사용, 병렬 실행 가능       |
| SerialDispatchQueueScheduler   |   순차적 실행 작업 스케줄링 |  GCD의 DispatchQueue를 사용, 작업을 순차적으로 실행  |
| OperationQueueScheduler   |   복잡한 비동기 작업 스케줄링 |  OperationQueue를 기반, 작업 의존성 관리 및 동시 실행 제한 가능  |
| ImmediateScheduler   |   즉시 실행해야 하는 작업 스케줄링 |  작업을 스케줄링하는 즉시 실행, 주로 테스트나 단순 동기 작업에 사용  |
| CurrentThreadScheduler   |   현재 스레드에서 작업 실행 |  재귀적 작업을 스케줄링할 때 스택 오버플로우 방지 기능 제공  |