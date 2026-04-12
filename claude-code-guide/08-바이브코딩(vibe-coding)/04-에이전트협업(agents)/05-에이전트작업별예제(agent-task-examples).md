# 05. 에이전트 작업별 실전 예제

> [04-인기에이전트레포](./04-인기에이전트레포(popular-agent-repos).md)에서 소개한 8개 레포를 **6가지 작업 시나리오**에 직접 투입하는 복붙용 예제 모음.

이 문서는 "어떤 레포가 좋은가"가 아니라 **"오늘 당장 어떻게 호출하는가"** 를 다룹니다.
레포 성격·규모·장단점은 [04](./04-인기에이전트레포(popular-agent-repos).md)를 참고하세요.

---

## 사용법

1. 자신이 쓰는 레포 섹션으로 이동
2. 6가지 시나리오 중 오늘 할 작업 찾기
3. 환경 설정을 1회 실행 (이미 설치돼 있으면 건너뜀)
4. **실전 프롬프트**를 복사해 `[대괄호 플레이스홀더]` 를 채운 뒤 Claude Code 세션에 붙여넣기
5. **예상 산출물** / **주의사항** 으로 결과 검증

### 6가지 작업 시나리오

| # | 시나리오 | 언제 |
|---|---------|------|
| 1 | 프로젝트 설계 | 아키텍처 산출, PRD 작성 |
| 2 | 코드 분석 | 기존 코드베이스 파악, 의존성 분석 |
| 3 | 기능 코딩 | 신규 기능 구현 (TDD 포함) |
| 4 | 코드 리뷰 | 품질/보안 검토 |
| 5 | 테스트 자동화 | 단위/통합 테스트 생성 및 실행 |
| 6 | 멀티 에이전트 협업 | Subagent / Agent Teams 조합 병렬 처리 |

### 대상 레포 8개

1. [wshobson/agents](#1-wshobsonagents)
2. [VoltAgent/awesome-claude-code-subagents](#2-voltagentawesome-claude-code-subagents)
3. [0xfurai/claude-code-subagents](#3-0xfuraiclaude-code-subagents)
4. [davepoon/buildwithclaude](#4-davepoonbuildwithclaude)
5. [lst97/claude-code-sub-agents](#5-lst97claude-code-sub-agents)
6. [SuperClaude-Org/SuperClaude_Framework](#6-superclaude-orgsuperclaude_framework)
7. [anthropics/skills](#7-anthropicsskills)
8. [hesreallyhim/awesome-claude-code](#8-hesreallyhimawesome-claude-code)

> **멀티 에이전트 주의**: 시나리오 6은 실험 기능인 Agent Teams 를 요구합니다. 세션 시작 전
> `export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` 을 반드시 설정하세요.

---

## 1. wshobson/agents

프로덕션 규모. 플러그인 마켓 기반 설치. 16개의 오케스트레이터가 강점.

### 프로젝트 설계 — wshobson/agents

**사용 에이전트:** `backend-architect` (도메인 분해 + API/DB 설계를 한 문서로 산출)

**환경 설정:**
```
/plugin marketplace add wshobson/agents
/plugin install backend-architect@claude-code-workflows
```

**CLAUDE.md 설정:**
```markdown
## 설계 규칙
- 모든 아키텍처 결정은 ADR 포맷으로 `docs/adr/` 에 기록한다
- 외부 의존성은 인터페이스 뒤로 숨긴다 (hexagonal)
- API 스펙은 Zod/TypeScript 타입을 단일 진실 공급원으로 사용
```

**실전 프롬프트:**
```
@backend-architect

목적: [한 줄 — 예: "사용자별 읽기 리스트를 저장하는 MVP 서비스"]

제약:
- 스택: [예: Next.js 15 + Postgres 16 + Drizzle]
- 배포: [예: Vercel + Supabase]
- 예상 트래픽: [예: DAU 1,000, 피크 RPS 20]
- 보안: [예: 로그인 필요, 타 유저 데이터 격리]

다음을 순서대로 산출:
1. 컨텍스트 다이어그램 (Mermaid flowchart)
2. 도메인 엔티티 + ERD (Mermaid erDiagram)
3. 핵심 API 5개 (엔드포인트 + Zod 입출력 타입)
4. 리스크 Top 3 + 완화책
5. ADR 2개 (DB 선택, 인증 방식)

파일 수정은 하지 말고 텍스트로만 출력해라.
```

**예상 산출물:** Mermaid 2장, ADR 2개, API 5개 명세, 리스크 표. 파일은 건드리지 않음.

**주의사항:**
- 읽기 전용 지시로 시작해야 에이전트가 임의로 `docs/adr/`에 파일을 만들지 않음.
- 트래픽 수치가 없으면 과도한 캐싱·샤딩 설계가 끼어들 수 있음. 구체 수치 명시 권장.

---

### 코드 분석 — wshobson/agents

**사용 에이전트:** `codebase-analyzer` + `dependency-mapper` (레거시 이해와 의존 그래프 추출)

**환경 설정:**
```
/plugin install code-analysis@claude-code-workflows
```

**실전 프롬프트:**
```
@codebase-analyzer

읽기 전용 모드로 다음을 조사한다. 파일 수정 금지.

대상: `src/services/` 전체
목표: [예: "주문 서비스를 리팩토링하기 전 현재 구조를 파악"]

출력:
1. 디렉토리 트리 (5~10줄)
2. 핵심 진입점 5개 (파일:심볼)
3. 공개 API (다른 모듈이 import 하는 심볼 목록)
4. 외부 의존 (npm/pip/go mod)
5. 데이터 흐름 (Mermaid flowchart, 5~7 노드)
6. 위험 지점 Top 5 (순환 의존 / 거대 파일 / 테스트 미비)
7. 내가 모를 것 같은 "조용한 가정" 3개
```

**예상 산출물:** 요약 분석 리포트 + Mermaid 1장. 약 600~1,000 토큰.

**주의사항:**
- "읽기 전용" 을 명시하지 않으면 자동 위임이 `refactorer` 를 호출해 수정을 시작할 수 있음.
- 초거대 레포에선 대상 디렉토리를 명시적으로 좁혀야 컨텍스트 초과를 피함.

---

### 기능 코딩 — wshobson/agents

**사용 에이전트:** `test-engineer` → `typescript-pro` (TDD: 테스트 먼저, 구현 다음)

**환경 설정:**
```
/plugin install languages-typescript@claude-code-workflows
/plugin install quality-testing@claude-code-workflows
```

**실전 프롬프트 (1단계 — 테스트 먼저):**
```
@test-engineer

목표: [예: "POST /api/favorites 엔드포인트 TDD로 구현"]

참조:
- docs/features/favorites.md (수용 기준 AC-1 ~ AC-6)
- docs/erd.md §favorite
- src/server/routes/  (기존 라우트 스타일 참고)

지시:
1. 아직 구현 없이 **실패하는 테스트**만 먼저 작성한다
2. Vitest + supertest 사용
3. AC-1 ~ AC-6 각각 최소 1개 테스트
4. 멱등성 / 비로그인 401 / 타 유저 403 엣지 케이스 포함
5. 작성한 테스트 파일을 실행해서 모두 빨간 상태인지 확인

다음 단계(구현)는 내가 승인 후에 `@typescript-pro` 에게 넘긴다.
```

**실전 프롬프트 (2단계 — 구현):**
```
@typescript-pro

planning/favorites-test.md 의 테스트가 모두 통과하도록 구현해라.

제약:
- src/server/services/favorite-service.ts 신규
- src/server/routes/favorites.ts 신규
- 기존 테스트를 수정하지 마라 (테스트는 스펙이다)
- 한 번에 한 테스트만 초록으로 바꾸는 방식으로 진행
```

**예상 산출물:** 1단계 — 5~8개 실패 테스트. 2단계 — 최소 구현 + 모든 테스트 통과.

**주의사항:**
- 두 에이전트를 **별도 세션**에 돌려야 "테스트를 구현 편의로 고치는" 오염이 없음.
- `@typescript-pro` 는 언어 특화라 프레임워크(Next.js) 관례는 CLAUDE.md 로 별도 보강 필요.

---

### 코드 리뷰 — wshobson/agents

**사용 에이전트:** `code-reviewer` + `security-auditor` (parallel code review teams 오케스트레이터 활용)

**환경 설정:**
```
/plugin install parallel-code-review-teams@claude-code-workflows
```

**실전 프롬프트:**
```
다음 변경을 parallel code review teams 오케스트레이터로 검토한다.

diff:
```
[git diff main...HEAD 결과 붙여넣기]
```

리뷰어 구성 (병렬):
1. @code-reviewer — 가독성/네이밍/로직
2. @security-auditor — OWASP Top 10 + 인증/인가
3. @performance-reviewer — N+1, 불필요한 재렌더, 큰 루프

출력 형식:
- 각 리뷰어: "OK" 또는 "문제: <한 줄> + 수정 제안"
- 마지막: **머지 가능 / 조건부 / 반려** + 이유
- 절대 칭찬하지 마라
```

**예상 산출물:** 3명의 리뷰어 코멘트 + 최종 판정. 세 관점이 독립 실행되어 사각지대 감소.

**주의사항:**
- diff 를 통째로 넣으면 큰 PR에서 컨텍스트 초과 가능. 파일별로 쪼개 두 라운드 권장.
- `security-auditor` 는 false-positive 비율이 약간 높음. 지적을 그대로 적용하지 말고 검증할 것.

---

### 테스트 자동화 — wshobson/agents

**사용 에이전트:** `unit-test-expert` + `e2e-test-engineer`

**환경 설정:**
```
/plugin install quality-testing@claude-code-workflows
```

**실전 프롬프트:**
```
@unit-test-expert

다음 파일의 단위 테스트 커버리지를 80% 이상으로 올려라.

대상: src/server/services/favorite-service.ts
테스트 위치: src/server/services/__tests__/favorite-service.test.ts
프레임워크: Vitest + in-memory Postgres (testcontainers 아님)

제약:
- 모킹은 최소 (외부 HTTP / 타임스탬프만 모킹)
- 기존 테스트를 약화시키지 마라
- 새 테스트는 AAA(Arrange-Act-Assert) 구조
- 에지 케이스: 빈 입력 / null / 중복 삽입 / 동시 삽입 (멱등)

실행:
1. 테스트 작성
2. `pnpm test favorite-service` 로 실행
3. 커버리지 리포트 출력
4. 커버되지 않은 라인이 있으면 이유를 1줄씩 설명
```

**예상 산출물:** 8~15개 테스트 + `pnpm test` 실행 결과 + 커버리지 수치.

**주의사항:**
- "커버리지 80%" 같은 수치 목표가 없으면 에이전트는 "적당히" 만 작성하고 끝냄.
- testcontainers 사용 허용 여부를 반드시 명시 (CI 환경 제약 때문).

---

### 멀티 에이전트 협업 — wshobson/agents

**사용 에이전트:** `full-stack-feature-development` 오케스트레이터 (DB → API → UI 순차 + 병렬 검증)

**환경 설정:**
```bash
export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1
```
```
/plugin install full-stack-feature-development@claude-code-workflows
```

**실전 프롬프트:**
```
full-stack-feature-development 오케스트레이터로 [즐겨찾기 기능] 을 끝까지 구현한다.

입력 문서:
- docs/PRD.md §3.2
- docs/features/favorites.md (AC-1 ~ AC-10)
- docs/erd.md (favorite 엔티티 추가됨)

단계 (오케스트레이터가 자동 진행):
1. database-engineer — favorite 마이그레이션 (Expand-Contract 중 Expand)
2. backend-architect + typescript-pro — API 3개 (POST/DELETE/GET)
3. frontend-architect + react-specialist — <FavoriteButton>, 프로필 탭 (병렬)
4. test-engineer — 단위 + 통합 테스트
5. code-reviewer + security-auditor — 병렬 리뷰
6. 마지막: 수용 기준 10개 체크

각 단계 종료마다 diff 를 보여주고 내 승인을 기다려라.
단계 간 파일은 planning/step-N.md 로 남겨라.
```

**예상 산출물:** 6단계 완료 + 각 단계별 planning 파일 + 최종 수용 기준 체크리스트.

**주의사항:**
- 각 단계 승인 지점에서 멈추게 해야 "달려가서 10파일 수정" 사고 방지.
- Agent Teams 실험 기능은 릴리즈에 따라 명령/환경변수가 바뀔 수 있음. 공식 문서 우선.
- 토큰 소모가 일반 세션의 3~5배. 작은 작업엔 과잉.

---
