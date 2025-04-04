---
title: HashMapAndHashTable
date: 2025-04-04 16:07:11
tags: iOS
---

HashMap에 대해서 공부하던 중, 자세히 알지 못했던 부분들에 대해서 남겨놓기


---
# **해시맵(HashMap)과 해시테이블(HashTable)의 차이점**

**해시맵(HashMap)과 해시테이블(HashTable)은 기본적으로 동일한 개념을 공유하지만, 구현 방식과 특징에서 차이가 있다.**


**해시맵(HashMap)이란?**

- **해시맵(HashMap)은 동기화(Synchronization)를 지원하지 않는 해시 기반의 Key-Value 저장소**
- 멀티스레드 환경에서 사용할 경우, 여러 스레드가 동시에 접근하면 데이터 무결성이 깨질 수 있다.
- 📌 **Swift에서 Dictionary<Key, Value>는 해시맵의 동작 방식을 따른다.**


**3. 해시테이블(HashTable)이란?**

- **해시테이블(HashTable)은 해시맵과 유사하지만, 멀티스레드 환경에서도 안전하게 동작하도록 설계**
- 내부적으로 **락(Lock) 또는 동기화 메커니즘**을 사용하여 여러 스레드가 동시에 접근할 때 데이터를 보호
- 📌 **Swift에서는 NSMutableDictionary, NSCache 등을 통해 동기화된 해시테이블을 사용할 수 있다**



---

- **해시맵(HashMap)** 은 **동기화(Synchronization) 기능이 없고 빠르며, iOS에서는 Dictionary로 사용**
- **해시테이블(HashTable)** 은 **멀티스레드 환경에서 안전하게 동작하며, iOS에서는 NSCache, NSMutableDictionary로 구현 가능**
- **멀티스레드 환경에서 성능을 고려한다면 NSCache가 가장 적절한 해시 테이블 대안**

