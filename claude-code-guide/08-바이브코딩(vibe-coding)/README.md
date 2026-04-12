# 08. 바이브 코딩 (Vibe Coding) — 실전 자산

> "최소 문서, 최대 효율" — 에이전트가 읽기 좋은 문서와 재사용 가능한 프롬프트로, 혼자서도 팀 수준의 개발 흐름을 만든다.

이 섹션은 가이드의 다른 섹션(01~07)이 **개념/레퍼런스**를 다룬다면, 여기서는 **복사해서 바로 쓰는 자산** — 프롬프트, 문서 템플릿, 협업 패턴, 예제 — 을 모았습니다.

| 다른 섹션 | 이 섹션 (08) |
|----------|-------------|
| "Claude Code가 뭔지" | "그걸로 내일 출근해서 뭘 할지" |
| 서술형 가이드 | 체크리스트 / 템플릿 / 프롬프트 |
| 기능 릴리즈 기준 갱신 | 프로젝트 회고 기준 갱신 |

---

## 구조

```
08-바이브코딩(vibe-coding)/
├── README.md                              ← (여기)
├── 00-개요(overview).md                   바이브 코딩 등식과 8단계 공통 루프
├── 01-빠른시작(quickstart).md             5단계 초기화 (CLAUDE.md → PRD → arch → ERD → dev)
├── 02-프롬프트템플릿(prompts)/             재사용 프롬프트 9종
│   ├── README.md
│   ├── 00-범용템플릿(universal).md         7-section 구조 (모든 템플릿의 부모)
│   ├── 01-기능개발(feature).md
│   ├── 02-버그수정(bugfix).md             2단계(원인→패치) 분리 패턴
│   ├── 03-리팩토링(refactoring).md
│   ├── 04-스키마마이그레이션(schema).md
│   ├── 05-UI구현(ui).md
│   ├── 06-코드리뷰(review).md
│   └── 07-탐색분석(exploration).md
├── 03-문서템플릿(templates)/               프로젝트에 복붙할 문서 원본
│   ├── README.md
│   ├── CLAUDE.template.md                  → 프로젝트 루트의 CLAUDE.md
│   ├── PRD.template.md                     → docs/PRD.md
│   ├── architecture.template.md            → docs/architecture.md
│   ├── erd.template.md                     → docs/erd.md
│   └── feature-spec.template.md            → docs/features/<name>.md
├── 04-에이전트협업(agents)/                멀티 에이전트 / 역할 분담 / 핸드오프
│   ├── README.md
│   ├── 01-멀티에이전트패턴(multi-agent-patterns).md   Orchestrator/Pipeline/Parallel/Debate
│   ├── 02-역할기반협업(role-based).md      Planner/Coder/Reviewer/Tester
│   ├── 03-크로스에이전트핸드오프(cross-agent-handoff).md
│   └── 04-인기에이전트레포(popular-agent-repos).md   wshobson/VoltAgent/0xfurai/... 비교 + 선택 가이드
└── 05-예제(examples)/                     엔드투엔드 워크스루
    ├── README.md
    ├── 01-기능개발예제(feature-walkthrough).md     기능 1개를 80분 4커밋으로
    └── 02-멀티에이전트예제(multi-agent-pipeline).md  847줄 리팩토링 14세션 사례
```

---

## 시작 경로 (5분)

1. **[01-빠른시작](./01-빠른시작(quickstart).md)** 을 읽고 5단계를 그대로 따라 한다.
2. **[03-문서템플릿/CLAUDE.template.md](./03-문서템플릿(templates)/CLAUDE.template.md)** 를 내 프로젝트 루트에 `CLAUDE.md`로 복사·채운다.
3. **[02-프롬프트템플릿/00-범용템플릿](./02-프롬프트템플릿(prompts)/00-범용템플릿(universal).md)** 의 구조를 익힌다.
4. 내 오늘 작업이 "기능/버그/리팩토링/스키마/UI" 중 무엇인지 정하고, **[02-프롬프트템플릿](./02-프롬프트템플릿(prompts)/)** 의 해당 템플릿을 복사해 채운다.
5. 복잡한 작업이라면 **[04-에이전트협업/01-멀티에이전트패턴](./04-에이전트협업(agents)/01-멀티에이전트패턴(multi-agent-patterns).md)** 을 읽고 역할을 나눈다.

---

## 가이드의 다른 섹션과의 연결

이 섹션은 가이드의 다른 부분과 다음과 같이 연결됩니다.

| 궁금한 것 | 보내는 곳 |
|----------|---------|
| Claude Code의 "기능 자체" | [03-주요기능/](../03-주요기능(features)/) |
| 워크플로우 "개념"  (코드 분석, 디버깅, 리팩토링 등) | [04-워크플로우/01~06](../04-워크플로우(workflows)/) |
| 워크플로우 "실전 흐름"  (기능/버그/리팩/스키마/UI) | [04-워크플로우/07~11](../04-워크플로우(workflows)/) |
| 프롬프트 엔지니어링 "원칙" | [07-최적화/01-프롬프트엔지니어링](../07-최적화(optimization)/01-프롬프트엔지니어링(prompt-engineering).md) |
| 프롬프트 "재사용 템플릿" | [02-프롬프트템플릿/](./02-프롬프트템플릿(prompts)/) (이 섹션) |
| CLAUDE.md "작성 방법" | [07-최적화/02-CLAUDE-MD작성법](../07-최적화(optimization)/02-CLAUDE-MD작성법(claude-md).md) |
| CLAUDE.md "복사용 템플릿" | [03-문서템플릿/CLAUDE.template.md](./03-문서템플릿(templates)/CLAUDE.template.md) (이 섹션) |
| 어떤 "문서를 만들지/안 만들지" | [07-최적화/06-문서종류분류](../07-최적화(optimization)/06-문서종류분류(document-types).md) |
| "에이전트 친화 포맷" 선택 | [07-최적화/07-에이전트친화포맷](../07-최적화(optimization)/07-에이전트친화포맷(agent-friendly-formats).md) |
| Claude Code "서브에이전트 기능" | [03-주요기능/05-서브에이전트](../03-주요기능(features)/05-서브에이전트(subagents).md) |
| 멀티 에이전트 "협업 패턴" | [04-에이전트협업/](./04-에이전트협업(agents)/) (이 섹션) |

---

## 설계 원칙

1. **역할 기반 분리** — 워크플로우(언제/왜) / 프롬프트(어떻게 묻나) / 템플릿(무엇을 남기나) / 에이전트(누가 하나) 로 역할 분리.
2. **재사용 우선** — 모든 문서는 복붙해서 바로 채울 수 있는 상태. 설명보다 구조를 남긴다.
3. **재현 가능성** — 같은 입력이면 같은 결과가 나오도록 프롬프트 구조를 고정 (§02-프롬프트템플릿/00-범용템플릿).
4. **에이전트 친화** — 문서는 Markdown + Mermaid만 (→ [07-최적화/07-에이전트친화포맷](../07-최적화(optimization)/07-에이전트친화포맷(agent-friendly-formats).md)).
5. **확장성** — 새 에이전트 / 자동화 / 훅 추가 시 `04-에이전트협업/` 아래에 파일 하나만 추가하면 되도록.
