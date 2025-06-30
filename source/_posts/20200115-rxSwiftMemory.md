---
title: RxSwift 메모리 누수
date: 2020-01-15 13:08:34
category:
 - RxSwift
tags: 
- MemoryIssue
- weak self
- Binder
---

이직한 회사에서 TagListView에 Rx를 extension 하다가 문득 바인더에서 메모리 누수에 대해 궁금했다
보통 Subscribe나 filter, map 의 경우에서만 [weak self] 를 통해 메모리 누수를 방지했는데 조금 더 자세히 알고 사용하기 위해 구글링을 시작했다.

[링크](https://medium.com/@M0rtyMerr/guarantee-rx-memory-leaks-absence-3a90636ec49e)를 보니 딱 궁금한것들이 정리되어있었다.

결론은 바인더에서는 기본적으로 약한참조를 하기때문에 신경쓰지않아도되서 아래와 같은 코드로 변경하였다.

```
import TagListView
import RxCocoa

extension Reactive where Base: TagListView{
    private var addTags: Binder<[String]>{
        return Binder(self.base) { (tagListView, tags) in
            tagListView.addTags(tags)
        }
    }
}
```
