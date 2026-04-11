# Vibe Coding with Claude (바이브 코딩 실전 가이드)

> "최소 문서, 최대 효율" — 에이전트가 읽기 좋은 문서와 재사용 가능한 프롬프트로, 혼자서도 팀 수준의 개발 흐름을 만든다.

이 디렉토리는 `claude-code-guide/`(개념/기능 중심 가이드)를 보완하는 **실전 워크플로우 · 프롬프트 · 템플릿** 모음입니다.
개념은 `claude-code-guide/`를, 실제로 붙여 쓰는 자산은 `vibe-coding/`을 참조하세요.

---

## 이 폴더는 무엇인가?

`claude-code-guide/`가 "Claude Code 사용법을 설명"하는 레퍼런스라면,
`vibe-coding/`은 **"그걸로 실제 하루 개발을 어떻게 굴릴 것인가"** 를 다루는 실행 키트입니다.

| 구분 | claude-code-guide | vibe-coding |
|------|-------------------|-------------|
| 성격 | 레퍼런스 / 개념 설명 | 실전 자산 / 재사용 템플릿 |
| 독자 | "Claude Code가 뭔지 배우고 싶다" | "내일 출근해서 바로 쓰고 싶다" |
| 형식 | 서술형 가이드 | 체크리스트, 템플릿, 프롬프트 |
| 업데이트 주기 | 기능 릴리즈 기준 | 프로젝트 회고 기준 |

---

## 구조 한눈에 보기

```
vibe-coding/
├── README.md                          ← (여기)
├── 01-워크플로우(workflow)/            작업 유형별 실행 루프
│   ├── 00-개요(overview).md
│   ├── 01-빠른시작(quickstart).md      CLAUDE.md → PRD → arch → ERD → dev
│   ├── 02-기능개발흐름(feature-flow).md
│   ├── 03-버그수정흐름(bugfix-flow).md
│   ├── 04-리팩토링흐름(refactoring-flow).md
│   ├── 05-스키마변경흐름(schema-flow).md
│   └── 06-UI구현흐름(ui-flow).md
├── 02-프롬프트(prompts)/               재사용 프롬프트 템플릿
│   ├── README.md
│   ├── 00-범용템플릿(universal).md
│   ├── 01-기능개발(feature).md
│   ├── 02-버그수정(bugfix).md
│   ├── 03-리팩토링(refactoring).md
│   ├── 04-스키마마이그레이션(schema).md
│   ├── 05-UI구현(ui).md
│   ├── 06-코드리뷰(review).md
│   └── 07-탐색분석(exploration).md
├── 03-문서템플릿(templates)/           프로젝트에 복붙할 문서 원본
│   ├── README.md
│   ├── 00-문서종류분류(document-types).md   필수4/권장3/불필요5 분류
│   ├── CLAUDE.template.md
│   ├── PRD.template.md
│   ├── architecture.template.md
│   ├── erd.template.md
│   └── feature-spec.template.md
├── 04-에이전트(agents)/                멀티 에이전트 / 역할 분담 / 핸드오프
│   ├── README.md
│   ├── 01-멀티에이전트패턴(multi-agent-patterns).md
│   ├── 02-역할기반협업(role-based).md
│   └── 03-크로스에이전트핸드오프(cross-agent-handoff.md
├── 05-포맷(formats)/                   에이전트 친화 포맷 선택 가이드
│   ├── README.md                       포맷 일반 원칙 + Mermaid 스니펫
│   └── 01-문서종류별포맷매칭(format-matching).md   문서 종류 × 포맷 결정표
└── 06-예제(examples)/                  엔드투엔드 워크스루
    ├── README.md
    ├── 01-기능개발예제(feature-walkthrough).md
    └── 02-멀티에이전트예제(multi-agent-pipeline).md
```

---

## 시작 경로 (5분)

1. **[01-워크플로우/01-빠른시작](./01-워크플로우(workflow)/01-빠른시작(quickstart).md)** 을 읽고 5단계를 그대로 따라 한다.
2. **[03-문서템플릿/CLAUDE.template.md](./03-문서템플릿(templates)/CLAUDE.template.md)** 을 내 프로젝트 루트에 `CLAUDE.md`로 복사·채운다.
3. **[02-프롬프트/00-범용템플릿](./02-프롬프트(prompts)/00-범용템플릿(universal).md)** 의 구조를 익힌다.
4. 내 오늘 작업이 "기능/버그/리팩토링/스키마/UI" 중 무엇인지 정하고, **[02-프롬프트](./02-프롬프트(prompts)/)** 의 해당 템플릿을 복사해 채운다.
5. 복잡한 작업이라면 **[04-에이전트/01-멀티에이전트패턴](./04-에이전트(agents)/01-멀티에이전트패턴(multi-agent-patterns).md)** 을 읽고 역할을 나눈다.

---

## 설계 원칙

1. **역할 기반 분리** — 워크플로우(언제/왜) / 프롬프트(어떻게 묻나) / 템플릿(무엇을 남기나) / 에이전트(누가 하나) 로 역할을 분리한다.
2. **재사용 우선** — 모든 문서는 복붙해서 바로 채울 수 있는 상태로 둔다. 설명보다 구조를 남긴다.
3. **재현 가능성** — 같은 입력이면 같은 결과가 나오도록 프롬프트 구조를 고정한다(§02-프롬프트/00-범용템플릿).
4. **에이전트 친화** — 문서는 Markdown + Mermaid로만 작성한다(사람·AI 양쪽이 diff/파싱 가능).
5. **확장성** — 새 에이전트 / 자동화 / 훅 추가 시 `04-에이전트/` 아래에 파일 하나만 추가하면 되도록 한다.

---

## `claude-code-guide/`와의 관계

- Claude Code의 "기능"이 궁금하면 → `claude-code-guide/03-주요기능/`
- 그 기능을 "언제 어떻게 쓸지"가 궁금하면 → `vibe-coding/01-워크플로우/`
- 특정 상황에서 "뭐라고 물어볼지"가 궁금하면 → `vibe-coding/02-프롬프트/`
- 프로젝트 루트에 "어떤 문서를 남길지"가 궁금하면 → `vibe-coding/03-문서템플릿/`
- 작업을 "여러 에이전트로 나누고 싶다"면 → `vibe-coding/04-에이전트/`

두 디렉토리는 대체 관계가 아니라 **레퍼런스 ↔ 플레이북** 의 보완 관계입니다.
