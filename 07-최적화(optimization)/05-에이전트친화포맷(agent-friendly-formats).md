# 07. 에이전트 친화 포맷

> "Markdown + Mermaid 조합으로 모든 문서 요구를 덮을 수 있다." — 에이전트가 읽고 쓰고 diff까지 할 수 있는 포맷만 쓴다는 원칙.

이 문서는 두 부분으로 구성됩니다.

1. **포맷 일반 원칙** — 어떤 포맷을 쓰고 어떤 것을 피하는가, Mermaid 스니펫 모음
2. **문서 종류별 매칭표** — 14종 문서 × 1순위/2순위/금지 포맷 결정표

---

# Part 1. 포맷 일반 원칙

## 한 줄 결론

| 상황 | 쓸 것 |
|------|-------|
| 규칙 / 설명 / 체크리스트 | **Markdown** |
| 시스템/컴포넌트 구조 | **Mermaid flowchart** |
| 시퀀스 / 상호작용 | **Mermaid sequenceDiagram** |
| DB 스키마 | **Mermaid erDiagram** |
| 상태 전이 | **Mermaid stateDiagram-v2** |
| UI 레이아웃 | **텍스트 와이어프레임 (박스 문자)** |
| 표 형태 데이터 | **Markdown table** |

다른 건 거의 필요 없습니다.

---

## 포맷 비교 표

| 포맷 | 사람 가독성 | AI 이해도 | Diff 용이성 | 도구 의존 | 추천도 |
|------|-----------|----------|------------|----------|--------|
| **Markdown** | ★★★★★ | ★★★★★ | ★★★★★ | 없음 | ★★★★★ |
| **Mermaid** | ★★★★☆ | ★★★★★ | ★★★★★ | 렌더러 | ★★★★★ |
| PlantUML | ★★★★☆ | ★★★★☆ | ★★★★☆ | Java 설치 | ★★★☆☆ |
| draw.io (.drawio) | ★★★★★ | ★★☆☆☆ | ★☆☆☆☆ | GUI | ★★☆☆☆ |
| Figma | ★★★★★ | ★★★☆☆ | ★☆☆☆☆ | 계정/브라우저 | UI에만 |
| Confluence/Notion 독점 포맷 | ★★★★☆ | ★★☆☆☆ | ★☆☆☆☆ | 플랫폼 | ★☆☆☆☆ |
| PDF | ★★★★★ | ★★★☆☆ | ★☆☆☆☆ | 리더 | ★☆☆☆☆ |

### 선택 기준 3가지

1. **git diff가 의미 있게 나오는가** — 변경 이력을 코드처럼 다루려면 텍스트 기반이어야 함
2. **AI가 파싱할 수 있는가** — 바이너리/이미지는 에이전트가 추정에 의존
3. **외부 도구 없이 보이는가** — IDE/GitHub만으로 열리는가

Markdown + Mermaid는 세 기준을 모두 만족하는 유일한 조합에 가깝습니다.

---

## Mermaid 실전 스니펫

### 시스템 구조 (flowchart)
```mermaid
flowchart LR
    A[User] --> B[Web]
    B --> C[API]
    C --> D[(DB)]
```

### 시퀀스 (sequenceDiagram)
```mermaid
sequenceDiagram
    autonumber
    User->>Web: click
    Web->>API: POST /x
    API->>DB: INSERT
    DB-->>API: ok
    API-->>Web: 200
    Web-->>User: toast
```

### ER (erDiagram)
```mermaid
erDiagram
    USER ||--o{ POST : writes
    USER {
        uuid id PK
        string email UK
    }
    POST {
        uuid id PK
        uuid user_id FK
        string title
    }
```

### 상태 전이 (stateDiagram-v2)
```mermaid
stateDiagram-v2
    [*] --> Pending
    Pending --> Paid: pay()
    Paid --> Shipped: ship()
    Shipped --> Delivered
    Pending --> Canceled: cancel()
    Paid --> Refunded: refund()
```

### 간트 (gantt)
```mermaid
gantt
    title Roadmap
    dateFormat  YYYY-MM-DD
    section P0
    Favorites        :a1, 2026-04-01, 10d
    Search           :a2, after a1, 7d
```

---

## 텍스트 와이어프레임 규칙

1. 박스는 `┌ ─ ┐ │ └ ┘`로 (ASCII 대체: `+ - | +`)
2. 버튼/입력은 `[...]`, 링크는 `[text]`
3. 상태 표기는 박스 아래에 별도 블록
4. 반응형은 "sm/md/lg: ..." 문구로

예시: [11-UI구현흐름](../04-워크플로우(workflows)/11-UI구현흐름(ui-flow).md) 또는 [08-바이브코딩/02-프롬프트템플릿/05-UI구현](../08-바이브코딩(vibe-coding)/02-프롬프트템플릿(prompts)/05-UI구현(ui).md)

---

## Markdown 스타일 규칙 (agent-friendly)

### 좋음
- 제목 레벨 2~3까지만 (깊어지면 AI가 맥락을 놓침)
- 표는 작게 (10행 넘으면 리스트로)
- 코드 블록은 언어 태그 포함 (` ```ts`)
- 링크는 상대 경로
- 파일/경로는 백틱으로 감싸기

### 피함
- HTML 임베드 (AI가 파싱 우회)
- 장식 이미지 (대신 Mermaid)
- 매우 긴 표 (30행+)
- 섹션 번호 5단계 이상 (`5.3.2.1.4`)

---

## 예외: 언제 Mermaid로 부족한가

- **픽셀 단위 UI 디자인**: Figma가 맞지만, 에이전트에겐 **텍스트 와이어프레임을 병기**.
- **복잡한 데이터 시각화**: 실제 차트는 코드로 생성 (예: Observable, matplotlib 스크립트).
- **UML 전체 14종**: Mermaid는 일부만 지원. 대부분 프로젝트엔 Mermaid로 충분.

---

# Part 2. 문서 종류별 최적 포맷 매칭

> "어떤 문서를 어떤 포맷으로 적을지"의 결정표. Part 1의 일반론을 **문서 종류별로** 구체화.

## 한 장 결정표

| 문서 종류 | 1순위 포맷 | 2순위 / 보조 | 절대 쓰지 마라 |
|----------|----------|-------------|--------------|
| **CLAUDE.md** (규칙) | Markdown (명령형) | 표 (정책 비교) | HTML, 이미지 |
| **PRD.md** (요구사항) | Markdown 리스트 + 표 | Mermaid gantt (로드맵) | Word/PDF, 슬라이드 |
| **architecture.md** | **Mermaid flowchart** + Markdown 설명 | sequenceDiagram (주요 플로우) | draw.io, Visio |
| **erd.md** | **Mermaid erDiagram** | Markdown 표 (컬럼 상세) | DBeaver export, PNG |
| **feature-spec** | Markdown + 체크박스 + Mermaid | sequenceDiagram (시나리오) | 이미지 와이어프레임 단독 |
| **ADR** | Markdown (Context/Decision/Consequences) | - | 긴 산문 |
| **handoff 노트** | Markdown 체크리스트 + 코드블록 | git log 발췌 | - |
| **API 계약** | TypeScript / Zod 코드블록 | Mermaid sequenceDiagram | 수동 OpenAPI YAML |
| **상태 머신** | **Mermaid stateDiagram-v2** | - | 산문 설명만 |
| **UI 와이어프레임** | **텍스트 박스 ASCII** | Markdown 컴포넌트 트리 | Figma 단독 (병기 OK) |
| **데이터 흐름** | Mermaid flowchart LR | sequenceDiagram | 산문만 |
| **로드맵 / 일정** | Mermaid gantt | Markdown 표 | Excel, MS Project |
| **트러블슈팅 가이드** | Markdown 표 (증상→원인→해결) | - | FAQ 산문 |
| **테스트 시나리오** | Markdown Given/When/Then | - | Excel 시나리오 |

---

## 매칭 근거

### 1. CLAUDE.md → Markdown 명령형

**왜**: 에이전트가 줄단위로 파싱. 명령형 ("~하지 마라") 이 의도 전달이 가장 강함.
**어떻게**: 섹션 헤더 + 불릿 + 코드 블록 (빌드 명령). 산문 금지.

```markdown
## 금지 사항
- `console.log` 커밋 금지
- `as any` 금지
```

### 2. PRD.md → Markdown 리스트 + 표

**왜**: 우선순위 (P0/P1/P2) 가 시각적으로 분리되어야 함. 체크박스로 진행 상태 표시.
**보조**: 로드맵이 필요하면 Mermaid `gantt`.

```markdown
### P0
- [ ] F-001 ...
- [x] F-002 ...
```

### 3. architecture.md → Mermaid flowchart + sequenceDiagram

**왜**: 시스템 구조는 노드/엣지 그래프. 주요 플로우는 시간 축. 두 다이어그램이 80% 커버.
**경계**: flowchart 한 장이 30 노드를 넘으면 분할.

### 4. erd.md → Mermaid erDiagram

**왜**: ER 다이어그램은 표준 표기. 에이전트가 직접 파싱해서 ORM 코드 생성에 사용.
**보조**: 컬럼 타입/제약은 별도 Markdown 표.

```mermaid
erDiagram
    USER ||--o{ POST : writes
```

### 5. feature-spec → Markdown 체크박스

**왜**: 수용 기준이 **검증 가능한 단위**여야 함. 체크박스가 강제.
**보조**: 시나리오는 sequenceDiagram, UI는 텍스트 와이어프레임.

### 6. ADR → Markdown Context/Decision/Consequences

**왜**: ADR 표준 구조. 짧고 고정. 변경 안 됨 (Superseded만 가능).
**금지**: 긴 산문 — 5분 안에 읽어야 함.

### 7. UI 와이어프레임 → 텍스트 ASCII 박스

**왜**: 에이전트 이해도 ★★★★★. Figma 이미지 ★★★☆☆.
**보조**: Figma가 있어도 **반드시 텍스트 와이어프레임 병기**.

```
┌──────────┐
│ [Logo]   │
│  [Btn]   │
└──────────┘
```

### 8. API 계약 → TypeScript / Zod 코드블록

**왜**: 수동 OpenAPI는 코드와 즉시 어긋남. 코드 자체가 명세.
**예외**: 외부 API 공개 시 자동 생성된 OpenAPI. 수작성 금지.

```ts
const Favorite = z.object({
  productId: z.string().uuid(),
});
```

---

## 포맷별 사용 가이드

### Markdown (모든 문서의 뼈대)
- 헤더: `##`, `###`까지만 (`####` 이상은 컨텍스트 분실)
- 표: 10행 이하 권장
- 코드 블록: 언어 태그 필수 (` ```ts`, ` ```sql`, ` ```bash`)
- 링크: 상대 경로
- 강조: `**볼드**`만 사용 (`*이탤릭*`은 가독성 ↓)

### Mermaid (구조/관계/시간)

| 다이어그램 | 용도 | 노드 한도 |
|-----------|------|----------|
| flowchart LR/TD | 시스템 구조, 데이터 흐름 | ~30 |
| sequenceDiagram | 시간순 상호작용 | ~10 actors |
| erDiagram | DB 스키마 | ~15 entities |
| stateDiagram-v2 | 상태 머신 | ~10 states |
| gantt | 일정/로드맵 | ~20 tasks |
| classDiagram | (사용 비추천 — 코드와 어긋남) | - |

### 텍스트 와이어프레임 (UI 전용)
- 박스: `┌ ─ ┐ │ └ ┘` (또는 ASCII `+ - |`)
- 인터랙티브: `[버튼]`, `[입력_필드]`
- 상태: 박스 아래 "States:" 블록
- 반응형: "sm/md/lg: ..." 줄

---

## 안티 매칭 (자주 보는 잘못된 조합)

| 잘못된 조합 | 왜 안 되는가 | 옳은 조합 |
|------------|------------|---------|
| 아키텍처를 산문으로만 | 영향 범위 추론 불가 | + Mermaid flowchart |
| ERD를 dbdiagram.io 이미지로 | 버전 관리/diff 불가 | Mermaid erDiagram |
| UI를 Figma 이미지만 | 에이전트가 픽셀 추측 | + 텍스트 와이어프레임 |
| API 명세를 수동 OpenAPI로 | 코드와 어긋남 | TS/Zod 코드 자체가 명세 |
| 로드맵을 Excel로 | 에이전트 못 읽음 | Mermaid gantt |
| 트러블슈팅을 산문 FAQ로 | 검색성 ↓ | "증상 / 원인 / 해결" 표 |
| ADR을 5페이지로 | 안 읽힘 | 1페이지 미만 |

---

## 도구 의존성 매트릭스

> "이 포맷을 사람이 보려면 / AI가 보려면 / git diff로 보려면 무엇이 필요한가"

| 포맷 | 사람 | AI 에이전트 | git diff | 추천도 |
|------|-----|-----------|---------|--------|
| Markdown | 에디터 | ✅ 직접 | ✅ 줄단위 | ★★★★★ |
| Mermaid (in MD) | GitHub/IDE 렌더러 | ✅ 직접 (텍스트) | ✅ 줄단위 | ★★★★★ |
| ASCII 와이어 | 에디터 | ✅ 직접 | ✅ | ★★★★★ |
| PlantUML | Java + 렌더러 | ✅ 텍스트 | ✅ | ★★★☆☆ |
| draw.io (.drawio) | draw.io 앱 | ❌ XML 추정 | △ XML diff | ★★☆☆☆ |
| Figma | 브라우저 + 계정 | ❌ 비전 의존 | ❌ | ★★☆☆☆ (UI에만) |
| Word/PDF | 전용 리더 | △ 추출 의존 | ❌ | ★☆☆☆☆ |
| Confluence | 플랫폼 + 계정 | ❌ | ❌ | ☆☆☆☆☆ |

**선택 원칙**: 세 열 (사람/AI/diff) 모두 ✅인 것만 1순위.

---

## 빠른 의사결정 흐름도

```mermaid
flowchart TD
    Start([문서를 만들거나 그릴 때]) --> Q1{무엇을 표현?}
    Q1 -- 규칙/정책/금지 --> A1[Markdown 명령형]
    Q1 -- 시스템 구조/관계 --> A2[Mermaid flowchart]
    Q1 -- 시간 순 흐름 --> A3[Mermaid sequenceDiagram]
    Q1 -- DB 스키마 --> A4[Mermaid erDiagram]
    Q1 -- 상태 전이 --> A5[Mermaid stateDiagram-v2]
    Q1 -- UI 레이아웃 --> A6[텍스트 와이어프레임]
    Q1 -- API 계약 --> A7[TypeScript/Zod 코드블록]
    Q1 -- 의사결정 기록 --> A8[Markdown ADR 양식]
    Q1 -- 일정/로드맵 --> A9[Mermaid gantt]
    Q1 -- 트러블슈팅 --> A10[Markdown 증상/원인/해결 표]
```

---

## 관련 문서

- [04-문서종류분류](./04-문서종류분류(document-types).md) — 어떤 문서를 만들고 어떤 걸 만들지 않을지
- [02-CLAUDE-MD작성법](./02-CLAUDE-MD작성법(claude-md).md) — CLAUDE.md 작성 가이드 + 프로젝트별 템플릿
- [08-바이브코딩/03-문서템플릿](../08-바이브코딩(vibe-coding)/03-문서템플릿(templates)/) — 복사용 문서 템플릿
