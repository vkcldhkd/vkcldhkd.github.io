---
title: SwiftData
date: 2025-09-21 23:18:30
category:
- iOS
tags:
- SwiftData
---


# SwiftData 사용 시 주의사항 체크리스트

> 대상: iOS 17+ / macOS 14+ / Xcode 15+ 이상에서 SwiftData를 사용하는 iOS 개발자  
> 목적: 실서비스에 SwiftData를 적용할 때 **데이터 무결성**, **성능**, **마이그레이션/동기화** 관점에서 자주 겪는 함정과 권장 패턴을 한눈에 점검

---

# 1) 모델링 (@Model) 설계

- **엔티티는** `**class**` **+** `**@Model`**: SwiftData 모델은 참조 타입이며 Observation이 내장됨. `ObservableObject`를 **중복 적용하지 않기**.
- **저장 속성만 모델에 두기**: 계산 속성/뷰 전용 상태는 모델 밖(뷰모델/헬퍼)으로 분리.
- **대형 데이터(이미지/동영상/바이너리)는 외부 저장**: 파일로 저장하고 SwiftData에는 **파일 URL/메타데이터만** 보관.
- **무결성 제약**: 식별자/고유 키가 필요하면 `@Attribute(.unique)` 등 제약을 적극적으로 사용(충돌 처리를 염두).
- **인덱싱 고려**: 자주 필터/정렬하는 속성은 인덱스를 두어 조회 성능 개선(초기 설계 단계에서 결정).

```swift
@Model
final class Note {
    @Attribute(.unique) var uuid: String
    var title: String
    var body: String
    var updatedAt: Date
    // 계산/뷰 전용 속성은 여기에 두지 않기!
    init(title: String, body: String) {
        self.uuid = UUID().uuidString
        self.title = title
        self.body = body
        self.updatedAt = .now
    }
}
```

---

# 2) 관계(relationships) 설정

- **양방향 관계는 inverse를 정확히**: 누락 시 동기화/삭제 규칙에서 예기치 못한 동작 가능.
- **삭제 규칙(Delete Rule)**: 보통 `.cascade` 또는 `.nullify`를 사용. 도메인 규칙에 맞게 선택.
- **To‑many 정렬은 보장하지 않음**: 화면 표시용 정렬은 **페치 시점에 명시적으로 정렬**.

```swift
@Model
final class Folder {
    var name: String
    @Relationship(deleteRule: .cascade, inverse: \Note.folder)
    var notes: [Note] = []
}

@Model
final class Note {
    var title: String
    var folder: Folder?
}
```

---

# 3) 컨테이너/컨텍스트 라이프사이클

- **`**ModelContainer**`**는 앱 전체에서 공유**, 화면 전환마다 새로 만들지 않기.
- **`**ModelContext**`**는 스레드(액터) 경계에 안전하지 않음**: 다른 Task/스레드로 **전달하지 말고**, 필요한 위치에서 새로 생성.
- **미리보기/테스트**: `isStoredInMemoryOnly: true`로 **인메모리 컨테이너**를 사용해 빠른 반복.

```swift
let container = try ModelContainer(for: [Folder.self, Note.self])
let context = ModelContext(container)
```

---

# 4) 동시성 & 변경 추적

- **같은 모델 인스턴스를 여러 컨텍스트에서 공유하지 않기**: 객체 정체성/스냅샷 충돌 위험. 필요 시 **ID(예:** `**persistentModelID**`**)로 재조회**.
- **대량 쓰기 작업은 배치로**: 반복 저장 대신 일정 단위로 `save()` 호출하여 오버헤드/락 경합 완화.
- **UI 업데이트는 메인 액터에서**: SwiftUI/`@Query`와의 일관성 유지.

```swift
try context.transaction {
    // 여러 삽입/수정/삭제를 묶어 원자적으로 처리
}
```

---

# 5) 페치(@Query / FetchDescriptor / #Predicate)

- **동적 필터는** `#Predicate` 사용**: 문자열 쿼리 조립 불가. 키패스 기반으로 작성.
- **정렬/페이징 명시**: `sortBy`, `fetchLimit`, `fetchOffset`을 활용해 과도한 로드 방지.
- **계산 속성/컬렉션 크기 등은 직접 조건에 사용할 수 없음**: 저장 속성 기준으로 설계.

```swift
let descriptor = FetchDescriptor<Note>(
    predicate: #Predicate { $0.title.localizedStandardContains("swift") },
    sortBy: [SortDescriptor(\.updatedAt, order: .reverse)],
    fetchLimit: 50
)
let results = try context.fetch(descriptor)
```

SwiftUI와 함께라면:

```swift
struct NoteList: View {
    @Query(filter: #Predicate<Note> { $0.title != "" },
           sort: [SortDescriptor(\Note.updatedAt, order: .reverse)])
    var notes: [Note]
    var body: some View { /* ... */ }
}
```

---

# 6) 저장(save)과 에러 처리

- `context.save()`는 **throws**: 실패 시 사용자 메시지/재시도 경로 설계.
- **충돌(고유 제약/동기화 병합) 대비**: 저장 전 정규화/중복 검사, 저장 실패 시 **사용자 피드백 + 롤백/재시도** 전략.
- **Undo/Redo가 필요하면** `UndoManager` 연동을 고려(편집 화면 단위).

---

# 7) 마이그레이션(스키마 진화)

- **가급적 “추가적(additive)” 변경 위주**: 새 속성 추가(기본값 제공), 새 엔티티 추가 등은 경미한 마이그레이션으로 안전.
- **파괴적 변경(타입 변경/필수→옵셔널 반전/이름 변경)**은 출시 전 신중히.  
  - 이름 변경 시 **`**renamingIdentifier`**(속성/관계 옵션)를 사용해 기존 데이터 매핑.
  - 타입 변경은 **새 속성 추가 → 데이터 이관 → 구속성 제거**의 단계적 전략 권장.
- **커스텀 매핑/스크립트 마이그레이션은 제한적**: 사전 설계로 회피하는 것이 현실적.
- **프로덕션 배포 전** 마이그레이션 시나리오(실데이터 백업→업그레이드→롤백) 리허설 필수.

---

# 8) CloudKit 동기화 사용 시 주의

- 컨테이너 구성 시 `allowsCloudSync: true` 등 **동기화 의도를 명시**.
- **중복/고유 제약 충돌**은 기기 간 경쟁 저장에서 발생할 수 있음 → **서버 수락 정책** 대신 **클라이언트 규칙**(사전 중복 체크/병합 규칙) 설계.
- **대량 변경/초기 동기화** 구간의 UI: 스켈레톤/진행 상태를 제공하고, 최초 구동 시간 증가를 고려.
- 스키마 변경은 **동기화된 사용자 기반**에서 전파 시간이 필요 → 점진적 롤아웃 권장.

---

# 9) 앱 확장(위젯/앱 그룹)과 저장소 경로

- 위젯/앱 간 데이터 공유는 **App Group 컨테이너 URL**로 `ModelConfiguration`을 구성.
- 동일 데이터베이스를 여러 타겟이 사용할 때 **동시 접근 타이밍**에 주의(쓰기 충돌/파일 잠금).

```swift
let config = ModelConfiguration(
    url: FileManager.default.containerURL(forSecurityApplicationGroupIdentifier: "group.app")!
        .appending(path: "AppData.store"),
    allowsCloudSync: true
)
let container = try ModelContainer(for: [Note.self, Folder.self], configurations: config)
```

---

# 10) 성능 최적화 팁

- **N+1 쿼리 방지**: 목록은 **가벼운 요약 필드**로 구성, 상세화면에서 무거운 로딩.
- **인덱스/정렬 키 선정**: 목록/검색 UX에 필요한 최소 키만 정렬/필터.
- **배치 저장/삭제**: 대량 변경 시 트랜잭션/배치 처리, 필요하면 화면 갱신을 지연(Throttle/Debounce).
- **메모리 관리**: 대용량 페치 지양, `fetchLimit`/오프셋/스크롤 기반 페이지네이션.

---

# 11) 테스팅/디버깅 전략

- **인메모리 컨테이너**로 단위 테스트 작성 → 속도/격리 보장.
- **샘플 데이터 시드 유틸리티**를 분리해 UI 테스트/프리뷰 재사용.
- **스토어 리셋 유틸리티** 준비: 손상/스키마 충돌 시 복구 경로 마련(사용자 데이터 백업 후 초기화).

```swift
@MainActor
func makeInMemoryContainer() throws -> ModelContainer {
    try ModelContainer(for: [Note.self, Folder.self],
                       configurations: .init(isStoredInMemoryOnly: true))
}
```

---

# 12) 흔한 함정 요약

- 모델에 **뷰 상태/계산 속성**을 넣어 **의도치 않은 변경 감지**가 발생.
- `ModelContext`를 다른 Task로 **넘겨 쓰다 크래시/데이터 경합**.
- 관계의 `inverse`/`deleteRule` 누락으로 **고아 데이터/삭제 누락**.
- `@Query`에 **계산 속성/지원되지 않는 표현**을 사용해 런타임 에러.
- 동기화 활성화 후 **고유 제약 충돌/중복 병합 정책 부재**.
- 마이그레이션에서 **이름/타입 변경을 한 번에** 수행하다 실패.

---

# # 부록: 추천 기본 설정 스니펫

```swift
@main
struct MyApp: App {
    let container: ModelContainer = {
        let config = ModelConfiguration(allowsCloudSync: false) // 필요 시 true
        return try! ModelContainer(for: [Folder.self, Note.self], configurations: config)
    }()

    var body: some Scene {
        WindowGroup {
            ContentView()
        }
        .modelContainer(container)
    }
}
```
