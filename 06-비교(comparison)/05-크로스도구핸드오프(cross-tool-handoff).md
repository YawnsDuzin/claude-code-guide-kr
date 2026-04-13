# 05. 크로스 도구 핸드오프 (Cross-Tool Handoff)

> Claude Code만 쓰는 경우는 드물다. 실무에선 Cursor / GPT Codex / Aider / Windsurf 와 병용한다. 도구가 바뀌어도 **컨텍스트가 보존**되어야 한다.

## 핵심 문제

각 도구는 서로 다른 시스템 프롬프트와 메모리 모델을 가진다.

| 도구 | 자동 로드 | 특성 |
|------|----------|------|
| Claude Code | `CLAUDE.md`, `.claude/` | 파일 시스템 접근, 긴 세션 |
| Cursor | `.cursorrules`, `.cursor/` | IDE 통합, 인라인 편집 |
| GPT Codex | `AGENTS.md` | 웹/터미널, 작업 단위 실행 |
| Aider | `CONVENTIONS.md` | CLI 커밋 중심 |

그래서 한 문서를 여기저기 복제하면 **동기화 지옥**이 생깁니다.

---

## 해결: 단일 소스 + 링크

### 권장 구조

```
<repo>/
├── CLAUDE.md                 # 단일 진실 공급원 (SSOT)
├── .cursorrules → CLAUDE.md  # 심볼릭 링크 (지원 시) 또는 재내보내기
├── AGENTS.md → CLAUDE.md
├── CONVENTIONS.md → CLAUDE.md
└── docs/
    ├── PRD.md
    ├── architecture.md
    ├── erd.md
    └── handoff/              # 세션 간 핸드오프 노트
        └── 2026-04-11-favorites.md
```

심볼릭 링크가 불가능하면 각 파일을 **자동 생성 스텁**으로 둡니다.

```markdown
<!-- .cursorrules -->
이 파일은 자동 생성됩니다. 실제 규칙은 CLAUDE.md를 보세요.
`scripts/sync-agent-rules.sh`로 동기화됩니다.

(아래는 CLAUDE.md의 복사본)
...
```

동기화 스크립트:
```bash
#!/bin/bash
# scripts/sync-agent-rules.sh
cp CLAUDE.md .cursorrules
cp CLAUDE.md AGENTS.md
cp CLAUDE.md CONVENTIONS.md
```

pre-commit 훅에 걸면 드리프트 방지.

> **CLAUDE.md 작성법 상세**는 [CLAUDE.MD 작성법](../07-최적화(optimization)/02-CLAUDE-MD작성법(claude-md).md)을 참고하세요.

---

## 세션 간 핸드오프 노트

"작업 중간에 다른 도구로 넘길 때" 쓰는 프로토콜.

### 파일 위치
```
docs/handoff/<YYYY-MM-DD>-<slug>.md
```

### 템플릿

```markdown
# Handoff: <작업 제목>

**From**: <도구 — 예: Claude Code>
**To**: <도구 — 예: Cursor>
**Date**: <YYYY-MM-DD HH:MM>
**Status**: 진행 중 / 리뷰 대기 / 블록됨

## 지금까지 한 것
- <변경한 파일 목록>
- <완료된 단계 / 체크리스트>

## 현재 상태
- 빌드: ✅ / ❌
- 테스트: ✅ 전체 통과 / ❌ <실패 이름>
- 린트: ✅ / ❌

## 다음 할 일
1. <구체적 단계 1>
2. <구체적 단계 2>

## 알아야 할 결정
- <예: "Redis 캐시 대신 in-memory LRU 선택 — 이유: ...">

## 블로커
- <있으면>

## 참조 문서
- docs/PRD.md §2.3
- docs/features/favorites.md

## 관련 diff
`git diff main...HEAD` 또는 브랜치 `feat/favorites`
```

> **문서 템플릿 종류 분류**는 [문서종류분류](../07-최적화(optimization)/04-문서종류분류(document-types).md)를 참고하세요.

### 사용법
1. 한 도구에서 작업 마무리할 때 이 파일 작성
2. 커밋에 함께 포함
3. 다음 도구 열면 해당 세션 시작 시 이 파일을 먼저 읽으라고 지시

---

## 핸드오프 지시 프롬프트

새 도구 세션을 열 때 첫 메시지로:

```
다음 순서로 컨텍스트를 로드해.

1. CLAUDE.md를 읽어라
2. docs/handoff/<최신 파일>.md를 읽어라
3. 거기 적힌 "다음 할 일"을 보고, 내 확인 없이는 작업 시작하지 마라
4. 현재 브랜치와 git status를 확인해라

그 다음 대기해라. 내가 구체적 지시를 준다.
```

이 4단계만 있어도 도구 전환의 "맥락 손실 20분"이 사라집니다.

---

## 도구별 모델 특성 — 역할 배분 참고

| 도구 | 강점 | 약점 | 추천 역할 |
|------|------|------|----------|
| Claude Code | 긴 탐색, 멀티 파일 계획, 도구 사용 | 빠른 인라인 편집은 IDE 대비 느림 | Planner, Reviewer |
| Cursor | 인라인 편집, tab autocomplete | 긴 체인 사고, 대규모 리팩토링 | Coder (소규모 수정) |
| Codex (GPT) | 독립 작업 단위, 터미널 실행 | 프로젝트 전역 맥락 | Coder (격리된 작업), Tester |
| Aider | 커밋 단위 작업, 명확한 diff | UI 없음 | Coder (CLI 선호 시) |

**고정 배분은 아닙니다.** 같은 도구로도 세션을 나누면 역할 분리 가능.

> **역할 기반 에이전트 분업 패턴**은 [서브에이전트](../03-주요기능(features)/05-서브에이전트(subagents).md)를 참고하세요.

> **도구별 비교 및 선택 기준**은 [선택가이드](04-선택가이드(selection-guide).md)를 참고하세요.

---

## 안티패턴

| 하지 마라 | 왜 |
|----------|---|
| 각 도구마다 다른 규칙 문서 | 동기화 실패 → 모순 |
| 핸드오프 노트 없이 도구 전환 | 컨텍스트 증발 |
| "기억나? 우리 어제 얘기한" | 세션 메모리 없음, 낭비 |
| 한 도구에 모든 것 몰아주기 | 도구별 강점 활용 못 함 |
| 심볼릭 링크 없이 수동 복붙 | 드리프트 발생 |
