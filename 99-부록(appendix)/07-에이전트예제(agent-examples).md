# 07. 에이전트 작업별 예제

> [04-인기에이전트레포](06-에이전트레포(agent-repos).md)에서 소개한 8개 레포를 **6가지 작업 시나리오**에 직접 투입하는 복붙용 예제 모음.

이 문서는 "어떤 레포가 좋은가"가 아니라 **"오늘 당장 어떻게 호출하는가"** 를 다룹니다.
레포 성격·규모·장단점은 [04](06-에이전트레포(agent-repos).md)를 참고하세요.

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

### 대상 레포 비교

| # | 레포 | 에이전트 규모 | 오케스트레이터 | 설치 방식 | 추천 대상 |
|---|------|-------------|-------------|---------|---------|
| 1 | [wshobson/agents](#1-wshobsonagents) | 16 오케스트레이터 | ✅ 전용 오케스트레이터 | 수동 복사 | 프로덕션 팀, 체계적 파이프라인 |
| 2 | [VoltAgent](#2-voltagentawesome-claude-code-subagents) | 100+ 전문가 | ✅ coordinator | 스크립트/수동 복사 | 도메인 전문가 다수 필요 시 |
| 3 | [0xfurai](#3-0xfuraiclaude-code-subagents) | 중간 | ❌ 수동 파이프라인 | 수동 복사 | 최소 의존, 직접 제어 |
| 4 | [davepoon](#4-davepoonbuildwithclaude) | agents+commands+hooks | ✅ orchestrator-router | 수동 복사 | 통합 허브, Next.js 중심 |
| 5 | [lst97](#5-lst97claude-code-sub-agents) | 33 + 메타 1 | ✅ agent-organizer | 수동 복사 | 솔로 풀스택, 과잉 설계 방지 |
| 6 | [SuperClaude](#6-superclaude-orgsuperclaude_framework) | 16 specialist + 14 persona | ✅ /sc:workflow | pip install | 프레임워크 통합, auto-activation |
| 7 | [anthropics/skills](#7-anthropicsskills) | 스킬 단위 (에이전트 아님) | ❌ 순차 활용 | 수동 복사 | 공식 템플릿/체크리스트 기반 |
| 8 | [hesreallyhim](#8-hesreallyhimawesome-claude-code) | — (메타 큐레이션) | — | 설치 없음 | 도구/에이전트 발견용 |

### 시나리오별 빠른 찾기

| 시나리오 | wshobson | VoltAgent | 0xfurai | davepoon | lst97 | SuperClaude | anthropics | hesreallyhim |
|---------|----------|-----------|---------|----------|-------|-------------|------------|-------------|
| 설계 | [→](#프로젝트-설계--wshobsonagents) | [→](#프로젝트-설계--voltagent) | [→](#프로젝트-설계--0xfurai) | [→](#프로젝트-설계--davepoon) | [→](#프로젝트-설계--lst97) | [→](#프로젝트-설계--superclaude) | [→](#프로젝트-설계--anthropicsskills) | [→](#프로젝트-설계--hesreallyhim) |
| 분석 | [→](#코드-분석--wshobsonagents) | [→](#코드-분석--voltagent) | [→](#코드-분석--0xfurai) | [→](#코드-분석--davepoon) | [→](#코드-분석--lst97) | [→](#코드-분석--superclaude) | [→](#코드-분석--anthropicsskills) | [→](#코드-분석--hesreallyhim) |
| 코딩 | [→](#기능-코딩--wshobsonagents) | [→](#기능-코딩--voltagent) | [→](#기능-코딩--0xfurai) | [→](#기능-코딩--davepoon) | [→](#기능-코딩--lst97) | [→](#기능-코딩--superclaude) | [→](#기능-코딩--anthropicsskills) | [→](#기능-코딩--hesreallyhim) |
| 리뷰 | [→](#코드-리뷰--wshobsonagents) | [→](#코드-리뷰--voltagent) | [→](#코드-리뷰--0xfurai) | [→](#코드-리뷰--davepoon) | [→](#코드-리뷰--lst97) | [→](#코드-리뷰--superclaude) | [→](#코드-리뷰--anthropicsskills) | [→](#코드-리뷰--hesreallyhim) |
| 테스트 | [→](#테스트-자동화--wshobsonagents) | [→](#테스트-자동화--voltagent) | [→](#테스트-자동화--0xfurai) | [→](#테스트-자동화--davepoon) | [→](#테스트-자동화--lst97) | [→](#테스트-자동화--superclaude) | [→](#테스트-자동화--anthropicsskills) | [→](#테스트-자동화--hesreallyhim) |
| 멀티 | [→](#멀티-에이전트-협업--wshobsonagents) | [→](#멀티-에이전트-협업--voltagent) | [→](#멀티-에이전트-협업--0xfurai) | [→](#멀티-에이전트-협업--davepoon) | [→](#멀티-에이전트-협업--lst97) | [→](#멀티-에이전트-협업--superclaude) | [→](#멀티-에이전트-협업--anthropicsskills) | [→](#멀티-에이전트-협업--hesreallyhim) |

> **⚠️ 멀티 에이전트(시나리오 6) 주의사항**
>
> Agent Teams 는 **실험 기능**입니다. 세션 시작 전 `export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`을 반드시 설정하세요.
> - 이 환경변수는 향후 릴리즈에서 이름이 변경되거나 제거될 수 있습니다. 적용 전 [공식 문서](https://docs.anthropic.com)를 확인하세요.
> - 토큰 소모가 일반 세션의 **3~5배**입니다. 작은 작업에는 과잉일 수 있습니다.

---

## 1. wshobson/agents

프로덕션 규모. 16개의 오케스트레이터가 강점.

### 프로젝트 설계 — wshobson/agents

**사용 에이전트:** `backend-architect` (도메인 분해 + API/DB 설계를 한 문서로 산출)

**환경 설정:**
```bash
git clone https://github.com/wshobson/agents.git /tmp/wshobson
mkdir -p ~/.claude/agents
cp /tmp/wshobson/agents/backend-architect.md ~/.claude/agents/
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
```bash
# 최초 클론은 1회만 (이미 했으면 건너뜀)
cp /tmp/wshobson/agents/codebase-analyzer.md ~/.claude/agents/
cp /tmp/wshobson/agents/dependency-mapper.md ~/.claude/agents/
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

**예상 산출물:** 요약 분석 리포트 + Mermaid 1장.

**주의사항:**
- "읽기 전용" 을 명시하지 않으면 자동 위임이 `refactorer` 를 호출해 수정을 시작할 수 있음.
- 초거대 레포에선 대상 디렉토리를 명시적으로 좁혀야 컨텍스트 초과를 피함.

---

### 기능 코딩 — wshobson/agents

**사용 에이전트:** `test-engineer` → `typescript-pro` (TDD: 테스트 먼저, 구현 다음)

**환경 설정:**
```bash
cp /tmp/wshobson/agents/test-engineer.md ~/.claude/agents/
cp /tmp/wshobson/agents/typescript-pro.md ~/.claude/agents/
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
```bash
cp /tmp/wshobson/agents/code-reviewer.md ~/.claude/agents/
cp /tmp/wshobson/agents/security-auditor.md ~/.claude/agents/
cp /tmp/wshobson/agents/performance-reviewer.md ~/.claude/agents/
```

**실전 프롬프트:**
````
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
````

**예상 산출물:** 3명의 리뷰어 코멘트 + 최종 판정. 세 관점이 독립 실행되어 사각지대 감소.

**주의사항:**
- diff 를 통째로 넣으면 큰 PR에서 컨텍스트 초과 가능. 파일별로 쪼개 두 라운드 권장.
- `security-auditor` 는 false-positive 비율이 약간 높음. 지적을 그대로 적용하지 말고 검증할 것.

---

### 테스트 자동화 — wshobson/agents

**사용 에이전트:** `unit-test-expert` + `e2e-test-engineer`

**환경 설정:**
```bash
cp /tmp/wshobson/agents/unit-test-expert.md ~/.claude/agents/
cp /tmp/wshobson/agents/e2e-test-engineer.md ~/.claude/agents/
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

# 오케스트레이터 + 관련 에이전트 복사
for a in full-stack-feature-development database-engineer backend-architect \
         typescript-pro frontend-architect react-specialist \
         test-engineer code-reviewer security-auditor; do
  cp /tmp/wshobson/agents/$a.md ~/.claude/agents/
done
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

## 2. VoltAgent/awesome-claude-code-subagents

언어/인프라/보안 도메인 전문가 100+ 명.

### 프로젝트 설계 — VoltAgent

**사용 에이전트:** `api-designer` (REST/GraphQL/tRPC 스펙을 Zod/OpenAPI 로 산출)

**환경 설정:**
```bash
# 설치 스크립트 (선별 설치)
git clone https://github.com/VoltAgent/awesome-claude-code-subagents.git /tmp/voltagent
cd /tmp/voltagent && ./install-agents.sh
# 프롬프트에서 "core-development" 카테고리 선택

# 또는 수동 복사
mkdir -p ~/.claude/agents
cp /tmp/voltagent/agents/api-designer.md ~/.claude/agents/
```

**실전 프롬프트:**
```
@api-designer

목적: [예: "Mobile + Web 클라이언트 공용 API 스펙 설계"]

스택/제약:
- 프로토콜: tRPC (내부) + REST (외부 공개)
- 인증: Supabase JWT
- 페이지네이션: cursor-based
- 에러 포맷: { ok: false, code, message, details? }

다음 리소스 5개의 API 스펙을 작성:
- [리소스 1: 예 User]
- [리소스 2: 예 Product]
- [리소스 3: 예 Order]
- [리소스 4: 예 Favorite]
- [리소스 5: 예 Review]

각 리소스마다:
1. tRPC procedure 시그니처 (Zod input/output)
2. 등가 REST 엔드포인트 (method, path, body, response)
3. 권한 행렬 (게스트/유저/관리자)
4. 에러 케이스 3개 이상

마지막에 OpenAPI 3.1 조각 1개를 예시로 출력.
```

**예상 산출물:** 5개 리소스의 tRPC + REST 이중 스펙 + 권한 행렬 + OpenAPI 샘플.

**주의사항:**
- VoltAgent 의 `api-designer` 는 GraphQL 편향이 있을 수 있음. "tRPC 우선" 명시 필수.
- 생성된 Zod 스키마를 그대로 `src/server/schemas/` 에 넣기 전에 기존 네이밍 컨벤션과 대조.

---

### 코드 분석 — VoltAgent

**사용 에이전트:** `code-archaeologist` (레거시 코드 고고학)

**환경 설정:**
```bash
./install-agents.sh   # "developer-experience" 카테고리 선택
# 또는 수동
cp /tmp/voltagent/agents/code-archaeologist.md ~/.claude/agents/
```

**실전 프롬프트:**
```
@code-archaeologist

3년된 레거시 모듈을 조사한다. 수정 금지.

대상: `src/legacy/billing/` (약 4,500 LOC)
맥락: [예: "원 저자 퇴사, 문서 없음, 테스트 23%"]

다음을 조사해라:
1. 이 모듈이 해결하는 비즈니스 문제 (코드에서 역추론)
2. 숨은 결합 (hidden coupling) — 어느 파일이 의외의 다른 파일을 참조하는가
3. "건드리면 위험한" 코드 경로 Top 5 (예: 사이드 이펙트, 글로벌 상태)
4. 죽은 코드 후보 (호출되지 않는 export)
5. 이 모듈을 건드리기 전 확인해야 할 외부 시스템 (DB 테이블, 큐, API)
6. 리팩토링 시작점 추천 3개 (작은 → 큰 순)

각 항목은 파일:라인 근거 필수.
```

**예상 산출물:** 6개 항목의 "고고학 리포트" + 파일 레퍼런스.

**주의사항:**
- "수정 금지"를 명시하지 않으면 자동 위임이 `refactorer` 를 활성화해 즉시 파일을 건드리기 시작.
- 대상 폴더가 너무 넓으면 컨텍스트 초과. 5,000 LOC 를 넘기면 하위 폴더별로 나눠 실행.

---

### 기능 코딩 — VoltAgent

**사용 에이전트:** `typescript-pro` + `react-specialist` (Language Specialists 28명 중)

**환경 설정:**
```bash
./install-agents.sh   # "language-specialists" 선택
```

**실전 프롬프트:**
```
@react-specialist

기능: [예: "상품 카드에 낙관적 업데이트로 즐겨찾기 하트 토글"]

참조:
- src/components/ProductCard.tsx (수정 대상)
- src/hooks/use-favorite.ts (존재하지 않음 — 신규 작성)
- docs/features/favorites.md (AC-1 ~ AC-10)

제약 (CLAUDE.md 준수):
- React Query v5 (+ 서스펜스 모드 아님)
- Tailwind + shadcn/ui 의 <Button> 만 사용
- 아이콘은 lucide-react
- any / @ts-ignore 금지
- 낙관적 업데이트 실패 시 롤백 + 토스트

출력 순서:
1. 신규 훅 `useFavorite(productId)` 시그니처 (코드 작성 전)
2. ProductCard 변경 부분 요약
3. 내가 승인 후 → 코드 작성
4. 작성 후 `pnpm typecheck && pnpm test ProductCard` 실행
```

**예상 산출물:** 훅 시그니처 → 승인 → 구현 + 타입체크/테스트 통과.

**주의사항:**
- VoltAgent 의 언어 스페셜리스트는 프레임워크 편향이 있을 수 있음. Next.js 라면 server component / client component 구분을 명시.
- React Query 버전 표기 없으면 v4 관례가 섞일 수 있음.

---

### 코드 리뷰 — VoltAgent

**사용 에이전트:** `security-auditor` + `code-reviewer` (Quality & Security 카테고리)

**환경 설정:**
```bash
./install-agents.sh   # "quality-security" 선택
```

**실전 프롬프트:**
````
2단계 리뷰를 수행한다.

## 단계 1: @security-auditor
대상 diff:
```
[git diff origin/main...HEAD 붙여넣기]
```

점검:
- OWASP Top 10 (A01 Broken Access Control 부터 A10 SSRF 까지)
- 사용자 입력 검증 (Zod 스키마 존재 여부)
- 인증 쿠키/토큰 취급 (HttpOnly, SameSite, Secure)
- SQL 주입 / NoSQL 주입 / 템플릿 주입
- 에러 메시지를 통한 정보 유출

## 단계 2: @code-reviewer
단계 1 결과 + 다음 관점 추가:
- 가독성 (이름, 함수 크기, 책임 분리)
- 테스트 존재 여부
- CLAUDE.md 규칙 위반

## 출력
- 각 항목 "OK" 또는 "문제: ..."
- 마지막: 머지 가능 / 조건부 / 반려
- 칭찬 금지
````

**예상 산출물:** 단계 1(보안) + 단계 2(품질) 각각의 점검표 + 최종 판정.

**주의사항:**
- VoltAgent 의 `security-auditor` 는 OWASP 에 특화. 규제(HIPAA/PCI-DSS) 점검은 별도 `compliance-*` 에이전트 필요.
- 2단계로 나누지 않으면 단일 세션에서 보안과 품질 논점이 섞여 결정 피로 증가.

---

### 테스트 자동화 — VoltAgent

**사용 에이전트:** `qa-automation` (Quality & Security 카테고리)

**환경 설정:**
```bash
./install-agents.sh   # "quality-security" 에서 qa-* 선택
```

**실전 프롬프트:**
```
@qa-automation

테스트 피라미드 관점에서 다음 기능을 덮어라.

기능: [예: "POST /api/favorites 멱등 저장"]
현재 상태:
- 단위 테스트 0개
- 통합 테스트 0개
- E2E 테스트 0개

목표 피라미드:
- 단위: 8~12개 (서비스 함수 레벨)
- 통합: 3개 (라우트 → DB 왕복)
- E2E: 1개 (Playwright, 성공 경로만)

제약:
- 단위는 Vitest, 통합은 supertest, E2E 는 Playwright
- 외부 네트워크 호출 금지 (fetch 모킹)
- testcontainers 사용 허용
- 각 테스트 이름은 "should ..." 형식

진행:
1. 테스트 계획 표 (레벨, 시나리오, 파일 경로)
2. 내 승인
3. 테스트 파일 생성
4. 전체 실행 (`pnpm test && pnpm test:e2e`)
5. 실패가 있으면 원인 1줄씩
```

**예상 산출물:** 3레벨 피라미드 12~16개 테스트 + 실행 결과.

**주의사항:**
- E2E 1개만 요구하는 게 핵심. 많으면 CI 시간 폭발.
- `qa-automation` 이 Cypress 를 선호할 수 있음. Playwright 명시 필수.

---

### 멀티 에이전트 협업 — VoltAgent

**사용 에이전트:** `multi-agent-coordinator` (Meta & Orchestration 카테고리의 메타 에이전트)

**환경 설정:**
```bash
export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1
./install-agents.sh   # "meta-orchestration" 선택
```

**실전 프롬프트:**
```
@multi-agent-coordinator

작업: [예: "사용자 프로필 페이지를 새 디자인 시스템으로 마이그레이션"]

팀 구성 (병렬 실행):
- @frontend-architect — 컴포넌트 트리 재설계 + 마이그레이션 순서
- @react-specialist — 페이지/컴포넌트 코드 변환
- @accessibility-auditor — WCAG 2.1 AA 점검
- @code-reviewer — 리뷰 (병합 가부)

공유 산출물:
- planning/profile-migration.md (coordinator 가 관리)
- diff 는 각 에이전트가 만들고 coordinator 가 취합

진행 규칙:
1. coordinator 가 먼저 단계별 계획 수립
2. 각 에이전트는 자기 책임 영역만 수정 (경계 넘지 않음)
3. 단계 종료 시 coordinator 가 충돌/중복 검사
4. 최종 merge diff 를 내게 보여주고 승인 대기
```

**예상 산출물:** coordinator 주도 planning 파일 + 4개 에이전트의 역할별 기여 + 병합된 최종 diff.

**주의사항:**
- VoltAgent 의 coordinator 는 wshobson 의 전용 오케스트레이터보다 느슨함. 단계별 승인 지점을 프롬프트에 명시해야 폭주 방지.
- 4명 이상 병렬은 토큰 소모가 크고 충돌도 잦음. 팀 인원은 4명 이하로 유지.

---

## 3. 0xfurai/claude-code-subagents

레포 클론 후 `~/.claude/agents/` 에 직접 복사. 일관된 포맷, 모델 자동 매핑.

### 프로젝트 설계 — 0xfurai

**사용 에이전트:** `system-architect` (신규 시스템 설계 전담)

**환경 설정:**
```bash
git clone https://github.com/0xfurai/claude-code-subagents.git /tmp/0xfurai
mkdir -p ~/.claude/agents
cp /tmp/0xfurai/agents/system-architect.md ~/.claude/agents/
cp /tmp/0xfurai/agents/database-architect.md ~/.claude/agents/
```

**CLAUDE.md 설정:**
```markdown
## 아키텍처 결정 원칙
- 3rd-party 선택은 ADR 로 기록
- 모든 외부 통합은 어댑터 패턴 (교체 가능성 확보)
```

**실전 프롬프트:**
```
@system-architect

새 서비스 설계 요청.

목적: [예: "실시간 알림 허브 — 웹/모바일/이메일 3채널 팬아웃"]

제약:
- 언어: Go
- 배포: AWS ECS Fargate + RDS Postgres + SQS
- 예상 QPS: 500 (피크), 평균 50
- SLO: 알림 생성 → 발송 p95 < 3초
- 장애 허용: 단일 AZ 다운 시에도 동작

산출물:
1. 고수준 아키텍처 (Mermaid flowchart, 6~10 노드)
2. 핵심 데이터 흐름 2개 (알림 생성 / 읽음 처리)
3. 큐 토폴로지 + DLQ 전략
4. 단일 장애점 Top 3 + 완화책
5. 운영 지표 (RED: Rate/Errors/Duration)
6. 코드는 작성하지 말 것
```

**예상 산출물:** 설계 문서 1건 (Mermaid 2장 + 토폴로지 + 지표 목록).

**주의사항:**
- 0xfurai 는 **모델 자동 매핑** 으로 설계 같은 복잡 작업에 상위 모델을 쓰려 함. 토큰 예산 주의.
- 에이전트 파일을 열어서 system prompt 하단의 "Output Guidelines" 를 확인하면 산출물 형식 예측 가능.

---

### 코드 분석 — 0xfurai

**사용 에이전트:** `code-analyzer` + `dependency-analyst`

**환경 설정:**
```bash
cp /tmp/0xfurai/agents/code-analyzer.md ~/.claude/agents/
cp /tmp/0xfurai/agents/dependency-analyst.md ~/.claude/agents/
```

**실전 프롬프트:**
```
@code-analyzer

읽기 전용으로 `src/` 전체 구조를 파악한다.

출력:
1. 레이어 검증 — Presentation / Domain / Repository 가 일관되게 분리돼 있는가? 위반 5개까지
2. 순환 의존 — import 그래프에서 사이클 탐지 (파일:파일 쌍)
3. God object 후보 — 한 파일에 3개 이상의 책임을 가진 것 Top 5
4. 테스트 미비 영역 — 테스트 없는 공개 함수 Top 10
5. 각 항목은 파일:라인 근거 필수

수정 금지. 파일을 만들지도 마라.
```

**예상 산출물:** 구조 진단 리포트 (4개 섹션 + 근거).

**주의사항:**
- 0xfurai 에이전트는 "quality checklist" 섹션을 내장해서 출력이 일관적. 결과를 `docs/audit/*.md` 로 바로 보존하기 좋음.
- 의존성 그래프를 Mermaid 로 그려주지 않을 수 있음. "Mermaid 로 표현" 명시 권장.

---

### 기능 코딩 — 0xfurai

**사용 에이전트:** `typescript-expert` + `postgres-specialist`

**환경 설정:**
```bash
cp /tmp/0xfurai/agents/typescript-expert.md ~/.claude/agents/
cp /tmp/0xfurai/agents/postgres-specialist.md ~/.claude/agents/
```

**실전 프롬프트:**
```
@postgres-specialist → @typescript-expert 순서로 진행.

## 단계 1: @postgres-specialist
스키마 변경: `notification` 테이블 추가
- id uuid PK, user_id FK, channel enum, payload jsonb, status enum, created_at, sent_at
- 인덱스: (user_id, created_at desc), (status, created_at) partial where status='pending'
- up/down 마이그레이션 쌍
- Drizzle 스키마 파일

## 단계 2: @typescript-expert
단계 1 완료 후:
- NotificationService (create, markSent, listForUser)
- 각 메서드에 Zod 입력 스키마
- 실패 테스트를 먼저 작성 (5개 이상) → 구현 → 통과 확인

제약:
- any / @ts-ignore 금지
- 트랜잭션 필요한 곳은 명시
- 실행: `pnpm db:migrate && pnpm test notification`
```

**예상 산출물:** 마이그레이션 up/down + Drizzle 스키마 + 서비스 + 테스트 + 실행 결과.

**주의사항:**
- 0xfurai 의 언어/DB 전문가는 서로 다른 세션으로 분리해도 되지만, 같은 세션이면 단계 사이에 명시적 "다음 단계로 가자" 가 필요.
- jsonb payload 에 대한 타입 안정성은 에이전트가 종종 잊음. Zod + Drizzle `$type` 명시 필수.

---

### 코드 리뷰 — 0xfurai

**사용 에이전트:** `code-reviewer` (self-review 용이)

**환경 설정:**
```bash
cp /tmp/0xfurai/agents/code-reviewer.md ~/.claude/agents/
```

**실전 프롬프트:**
````
@code-reviewer

커밋 전 셀프 리뷰. 칭찬 금지.

diff:
```
[git diff --staged 결과]
```

다음을 순서대로 점검:
1. 정확성 — null / undefined / 경계값 / 에러 경로
2. CLAUDE.md 금지 규칙 위반
3. 타입 안정성 — any 없나, unknown 은 좁혀졌나
4. 테스트 — 이 변경을 커버하는 테스트가 diff 안에 있나
5. 로그/관측성 — 새 실패 경로가 보이는가
6. 보안 — 사용자 입력 이스케이프, 권한 체크
7. 가독성 — 2주 후 내가 읽을 수 있나

각 항목: "OK" 또는 "문제: <한 줄>" + 수정 제안 1개
마지막: 머지 가능 / 불가 + 고쳐야 할 목록
````

**예상 산출물:** 7개 항목 점검표 + 최종 판정 + 고칠 목록.

**주의사항:**
- 0xfurai `code-reviewer` 는 지시를 잘 따름. 단 "칭찬 금지" 를 빼면 "잘 작성하셨습니다" 같은 쓸모 없는 말이 추가됨.
- 큰 diff 에서는 세션 컨텍스트 초과 가능. 파일 그룹별로 2~3 라운드 권장.

---

### 테스트 자동화 — 0xfurai

**사용 에이전트:** `testing-jest` + `testing-playwright`

**환경 설정:**
```bash
cp /tmp/0xfurai/agents/testing-jest.md ~/.claude/agents/
cp /tmp/0xfurai/agents/testing-playwright.md ~/.claude/agents/
```

**실전 프롬프트:**
```
@testing-jest

다음 파일에 단위 테스트를 추가한다.

대상: src/services/notification-service.ts
테스트 파일: src/services/__tests__/notification-service.test.ts
스택: Vitest (Jest 호환 API) + in-memory fake repository

제약:
- AAA 구조
- 시나리오 범주:
  - 성공 경로 (채널별 3개)
  - 실패 경로 (DB 에러, payload 검증 실패, 권한 없음)
  - 동시성 (동일 user 에 중복 알림 — 멱등)
- 모킹: 시간(vi.useFakeTimers), 외부 HTTP 만
- 커버리지: branch 85% 이상

실행:
1. 테스트 작성
2. `pnpm test notification-service` 실행
3. 미커버 라인 있으면 1줄씩 이유

이어서 @testing-playwright 로 E2E 스모크 1개를 내가 별도 세션에서 요청하겠다.
```

**예상 산출물:** Vitest 단위 테스트 15~20개 + 실행 리포트 + 커버리지.

**주의사항:**
- 0xfurai `testing-jest` 가 진짜 Jest 전제로 작성할 수 있음. Vitest 임을 명시해야 imports / matchers 가 맞음.
- `testing-playwright` 는 별도 세션 권장 (환경/의존성이 다름).

---

### 멀티 에이전트 협업 — 0xfurai

**사용 에이전트:** 여러 에이전트를 **사람이 직접 순차 호출**. 0xfurai 는 오케스트레이터 에이전트가 없음.

**환경 설정:**
```bash
export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1

# 필요한 에이전트 일괄 복사
for a in system-architect postgres-specialist typescript-expert react-developer \
         testing-jest code-reviewer security-auditor; do
  cp /tmp/0xfurai/agents/$a.md ~/.claude/agents/
done
```

**실전 프롬프트 (수동 Pipeline 패턴):**
```
기능: [예: "알림 읽음 상태 저장 + 목록 조회 API + 프론트 배지"]

다음 순서로 **한 번에 한 에이전트만** 호출한다. 단계 완료 시 내가 다음 에이전트를 부른다.

Step 1: @system-architect
- 영향 파일 목록 + 계획 `planning/notification-read.md` 생성
- 코드 수정 금지

Step 2: @postgres-specialist
- planning/notification-read.md 읽기
- notification.read_at 컬럼 추가 마이그레이션 (Expand)

Step 3: @typescript-expert
- markAsRead 서비스 + API 엔드포인트

Step 4: @react-developer
- 배지 컴포넌트 + 훅

Step 5: @testing-jest
- 단위 + 통합 테스트

Step 6: @code-reviewer
- 전체 diff 리뷰 + 머지 가부 판정

⚠️ Step 을 건너뛰지 마라. 이전 단계의 planning 파일을 먼저 읽은 뒤 진행.
```

**예상 산출물:** 6단계 파이프라인 완료 + planning 파일 + 최종 리뷰 리포트.

**주의사항:**
- 0xfurai 는 **자체 오케스트레이터가 없음**. 사람이 단계를 밀어야 함.
- "한 번에 한 에이전트만" 을 빼면 자동 위임이 섞여 혼선.
- 각 단계를 **새 세션** 에서 여는 것이 가장 안전 (컨텍스트 분리).

---

## 4. davepoon/buildwithclaude

커뮤니티 허브. 웹 UI(buildwithclaude.com)에서 검색 가능. agents + commands + hooks + skills 다 있음.

### 프로젝트 설계 — davepoon

**사용 에이전트:** `agents-system-design-architect` (+ 보조 `commands-documentation-adr`)

**환경 설정:**
```bash
git clone https://github.com/davepoon/buildwithclaude.git /tmp/davepoon
mkdir -p ~/.claude/agents ~/.claude/commands
cp /tmp/davepoon/agents/system-design-architect.md ~/.claude/agents/
cp /tmp/davepoon/commands/documentation-adr.md ~/.claude/commands/
```

**실전 프롬프트:**
```
@agents-system-design-architect

새 서비스 설계. 코드 수정 금지.

목적: [예: "멀티테넌트 SaaS 대시보드 MVP"]
테넌트 격리: [예: "schema-per-tenant"]
스택: Next.js 15 + tRPC + Postgres 16 + Clerk auth
초기 테넌트 수: 50
테넌트당 유저: 평균 20

산출물:
1. 컨텍스트 다이어그램 + 배포 다이어그램 (Mermaid)
2. 테넌트 격리 전략 상세 (DB/캐시/파일/백그라운드잡)
3. 온보딩 플로우 시퀀스 (Mermaid sequenceDiagram)
4. 요금제 기반 기능 플래그 설계
5. 리스크 5개 + 완화책

이어서:
/adr-new "테넌트 격리 전략 schema-per-tenant 채택"
/adr-new "인증 공급자 Clerk 채택 이유"
```

**예상 산출물:** 설계 문서 1건 + ADR 2개 (슬래시 커맨드가 템플릿 생성).

**주의사항:**
- buildwithclaude 의 파일은 **카테고리 접두어** (`agents-*`, `commands-*`, `hooks-*`) 가 있어 이름이 길다.
- 설계 단계에서 `hooks-*` 는 켜지 말 것 (파일 편집 트리거가 잘못 걸릴 수 있음).

---

### 코드 분석 — davepoon

**사용 에이전트:** `agents-codebase-explorer` + `/codebase-map` 커맨드

**환경 설정:**
```bash
cp /tmp/davepoon/agents/codebase-explorer.md ~/.claude/agents/
cp /tmp/davepoon/commands/codebase-map.md ~/.claude/commands/
cp /tmp/davepoon/commands/code-analysis.md ~/.claude/commands/
```

**실전 프롬프트:**
```
/codebase-map src/

이어서:

@agents-codebase-explorer

위 맵을 기반으로 다음을 조사한다.

1. 가장 자주 import 되는 모듈 Top 10 (변경 시 파급 큰 곳)
2. 테스트 커버리지가 낮은 공개 모듈 Top 5
3. 순환 의존 존재 여부
4. 파일당 평균 줄수 / 500줄 초과 파일 목록
5. 이 레포에 처음 합류한 개발자가 2시간 안에 이해해야 할 핵심 파일 10개

출력은 Markdown 표 + 근거 경로 포함. 파일 수정 금지.
```

**예상 산출물:** 코드베이스 지도 + 5개 분석 섹션. 온보딩 문서로 재사용 가능.

**주의사항:**
- `/codebase-map` 커맨드는 첫 실행 시 프로젝트 전체를 스캔 → 큰 레포는 시간·토큰 소모 큼. 최초 1회만.
- 결과를 `docs/onboarding.md` 로 저장하는 훅(`hooks-save-analysis`)이 있으면 자동 보존 가능.

---

### 기능 코딩 — davepoon

**사용 에이전트:** `agents-fullstack-feature-developer`

**환경 설정:**
```bash
cp /tmp/davepoon/agents/fullstack-feature-developer.md ~/.claude/agents/
cp /tmp/davepoon/commands/git-workflow.md ~/.claude/commands/
```

**실전 프롬프트:**
```
@agents-fullstack-feature-developer

기능: [예: "팀 초대 링크 발급 + 수락 플로우"]

참조:
- docs/PRD.md §4.1
- docs/features/team-invite.md (AC 8개)
- src/server/ (tRPC 라우터 위치)
- src/app/(dashboard)/team/ (프론트)

제약:
- 트랜잭션: 초대 생성 + 이메일 발송을 outbox 패턴으로
- 이메일: Resend API (src/lib/email.ts 기존)
- 만료: 초대 링크 48시간
- 멱등: 같은 이메일로 중복 초대 불가

출력 순서:
1. 영향 파일 목록 (신규/수정)
2. tRPC procedure 시그니처 + DB 스키마 변경
3. 내 승인 → 코드 작성
4. 작성 후 `/commit-with-conventional-message feat` 로 커밋
```

**예상 산출물:** 풀스택 변경 + Conventional Commit 자동 생성.

**주의사항:**
- davepoon 의 `fullstack-feature-developer` 는 Next.js/tRPC 편향. 다른 스택(Remix/SolidStart)이면 스택을 명시적으로 적어야 함.
- `commands-git-workflow` 는 커밋 메시지를 자동 생성하므로 팀 컨벤션과 맞는지 먼저 검증.

---

### 코드 리뷰 — davepoon

**사용 에이전트:** `agents-code-reviewer-strict` + `/security-scan` 커맨드 + `hooks-block-todo-comments`

**환경 설정:**
```bash
cp /tmp/davepoon/agents/code-reviewer-strict.md ~/.claude/agents/
cp /tmp/davepoon/commands/security-scan.md ~/.claude/commands/
# 훅은 settings.json 에 등록 (레포 README 참조)
cp /tmp/davepoon/hooks/block-todo-comments.sh ~/.claude/hooks/
```

**실전 프롬프트:**
```
/security-scan --staged

이어서:

@agents-code-reviewer-strict

위 security-scan 결과 + 스테이징된 diff 를 리뷰한다.

점검:
1. security-scan 경고 — 각각 true-positive 인가? 근거 1줄
2. 로직 정확성 — 경계값/에러 경로
3. 테스트 — 존재 여부와 충분성
4. CLAUDE.md 규칙
5. 성능 — 명백한 N+1, 거대 payload

출력:
- security-scan 경고: 각 라인 + 판정 (TP/FP)
- diff 점검: OK/문제
- 머지 가능/조건부/반려
```

**예상 산출물:** 보안 스캔 결과 + 리뷰 코멘트 + 판정. `hooks-block-todo-comments` 가 커밋 단계에서 TODO 자동 차단.

**주의사항:**
- 훅을 켠 뒤엔 의도한 TODO(예: `// TODO(#123)`)까지 차단될 수 있음. 훅 설정에 화이트리스트 정규식 필수.
- `/security-scan` 은 내부적으로 여러 스캐너를 호출. 토큰 소모가 상대적으로 크다.

---

### 테스트 자동화 — davepoon

**사용 에이전트:** `agents-test-writer-comprehensive` + `/test-gen` 커맨드

**환경 설정:**
```bash
cp /tmp/davepoon/agents/test-writer-comprehensive.md ~/.claude/agents/
cp /tmp/davepoon/commands/test-gen.md ~/.claude/commands/
```

**실전 프롬프트:**
```
/test-gen src/services/invite-service.ts

이어서:

@agents-test-writer-comprehensive

위 자동 생성 테스트를 기반으로 다음을 수행:

1. 누락 시나리오 추가 (경계값, 에러 경로, 동시성)
2. 테스트 이름을 "should ..." 형식으로 정리
3. 픽스처를 `tests/fixtures/invite.ts` 로 추출 (중복 제거)
4. 통합 테스트 1개 추가 (tRPC caller 레벨)
5. `pnpm test invite` 실행 + 결과 요약

제약:
- 목표 커버리지: 90% (statement)
- 외부 HTTP 는 msw 로 모킹
- 테스트당 실행 시간 200ms 이하
```

**예상 산출물:** 자동 생성 + 보강된 테스트 세트 + 픽스처 추출 + 실행 결과.

**주의사항:**
- `/test-gen` 은 "일단 돌아가는 수준" 의 뼈대만 생성. 보강 에이전트 호출은 필수.
- 픽스처 위치를 명시하지 않으면 에이전트가 테스트 파일 내부에 중복 작성.

---

### 멀티 에이전트 협업 — davepoon

**사용 에이전트:** `agents-orchestrator-router` + 역할별 에이전트 3~4명

**환경 설정:**
```bash
export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1

for a in orchestrator-router backend-engineer frontend-engineer \
         test-writer-comprehensive code-reviewer-strict; do
  cp /tmp/davepoon/agents/$a.md ~/.claude/agents/
done
```

**실전 프롬프트:**
```
@agents-orchestrator-router

작업: [예: "결제 실패 시 자동 재시도 로직 추가"]

팀:
- @agents-backend-engineer — 재시도 큐 + 지수 백오프
- @agents-frontend-engineer — 사용자 알림 UI
- @agents-test-writer-comprehensive — 테스트 3레벨
- @agents-code-reviewer-strict — 최종 리뷰

규칙:
1. 각 에이전트는 자기 영역만 수정
2. orchestrator 가 단계별 충돌 검사
3. 단계 종료 시 diff 보여주고 내 승인 대기
4. 실패 경로(결제 게이트웨이 타임아웃)는 별도 테스트 필수
5. 최종 PR 설명은 `/pr-description` 커맨드로 생성

산출물:
- planning/payment-retry.md
- 각 에이전트 diff 취합본
- 테스트 실행 결과
- PR 설명 초안
```

**예상 산출물:** 4인 팀 병렬/순차 협업 완료 + PR 초안.

**주의사항:**
- buildwithclaude 의 orchestrator 는 비교적 최근 추가. 릴리즈에 따라 동작 차이가 있을 수 있음.
- 4개 에이전트를 동시에 복사하면 이름 충돌 가능. `ls ~/.claude/agents/` 로 기존 에이전트를 확인하고 시작.

---

## 5. lst97/claude-code-sub-agents

33개 + 1 메타 오케스트레이터(`agent-organizer`). 솔로 풀스택 (React/Next.js/Python/Go/TS/Postgres) 중심.

### 프로젝트 설계 — lst97

**사용 에이전트:** `solution-architect`

**환경 설정:**
```bash
git clone https://github.com/lst97/claude-code-sub-agents.git /tmp/lst97
mkdir -p ~/.claude/agents
cp /tmp/lst97/agents/*.md ~/.claude/agents/   # 33개 전체 (선별도 가능)

# 프로젝트에 CLAUDE.md 샘플 복사 (선택)
cp /tmp/lst97/CLAUDE.md ./CLAUDE.md.sample
```

**실전 프롬프트:**
```
@solution-architect

목적: [예: "개인 독서 기록 앱 — 책 검색/저장/리뷰/하이라이트"]

제약:
- 팀: 솔로
- 스택: Next.js 15 App Router + Postgres + Drizzle + Clerk
- 배포: Vercel + Supabase
- 예산: 월 $30 이하
- 개발 기간: 4주

산출물:
1. 최소 기능 세트 (P0만 7개 이하)
2. 데이터 모델 (Mermaid erDiagram, 엔티티 4~6개)
3. 핵심 API 5개
4. 외부 서비스 의존 (도서 메타데이터는 어디서?)
5. 4주 로드맵 (주차별 목표)

파일 수정 금지. 설계만.
```

**예상 산출물:** 솔로 개발자 기준의 "실제로 완성 가능한" 작은 설계.

**주의사항:**
- lst97 은 솔로 중심이라 **과잉 설계를 스스로 경계**함. 이 점이 장점.
- 엔터프라이즈 규모 요건을 주면 에이전트가 당황할 수 있음 — 솔로 범위에서만 쓸 것.

---

### 코드 분석 — lst97

**사용 에이전트:** `agent-organizer` (메타) → 개별 에이전트에게 위임

**환경 설정:** 위와 동일.

**실전 프롬프트:**
```
@agent-organizer

작업: 이 레포의 현재 상태 파악. 읽기 전용.

필요한 하위 조사:
1. 구조 — @code-analyzer 가 담당
2. 의존성 — @dependency-auditor 가 담당
3. 테스트 현황 — @test-engineer 가 담당
4. 기술 부채 지점 — @refactoring-specialist 가 담당 (제안만)

너(agent-organizer)는:
- 위 4개 하위 조사를 **순차로** 호출
- 각 결과를 planning/audit.md 로 취합
- 마지막에 "오늘 1시간 안에 할 수 있는 개선 3개" 제안

⚠️ 어느 하위 에이전트도 코드를 수정하지 마라.
```

**예상 산출물:** `planning/audit.md` 1개 + 개선 제안 3개 (작은 것부터).

**주의사항:**
- `agent-organizer` 는 lst97 의 가장 큰 특징. 직접 역할 분배를 하지 않아도 메타 에이전트가 처리.
- 하위 에이전트가 파일을 만들 수 있으므로 "코드 수정 금지" 를 최상위에서 박아야 함.

---

### 기능 코딩 — lst97

**사용 에이전트:** `nextjs-developer` + `drizzle-orm-expert`

**환경 설정:**
```bash
cp /tmp/lst97/agents/nextjs-developer.md ~/.claude/agents/
cp /tmp/lst97/agents/drizzle-orm-expert.md ~/.claude/agents/
```

**실전 프롬프트:**
```
@nextjs-developer

기능: [예: "책 검색 결과 페이지 — Google Books API 프록시 + 서버 컴포넌트"]

참조:
- src/app/(books)/ (기존 레이아웃)
- src/lib/google-books.ts (미작성 — 신규)
- docs/features/book-search.md (AC 6개)

제약:
- App Router + RSC + Server Actions
- 이미지는 next/image 로 (외부 도메인 next.config.js 에 추가)
- 에러 바운더리 + loading.tsx + not-found.tsx
- 무한 스크롤 금지 (pagination 20개/페이지)

출력:
1. 파일 트리 (신규/수정)
2. 각 파일의 핵심 시그니처
3. 내 승인 → 코드 작성
4. 타입체크 + 빌드 실행
```

**예상 산출물:** 책 검색 페이지 전체 + next.config.js 업데이트 + 빌드 성공.

**주의사항:**
- lst97 의 `nextjs-developer` 는 App Router 전제. Pages Router 프로젝트엔 부적합.
- drizzle 전문가와 next.js 전문가를 같이 쓰면 DB 접근이 서버 컴포넌트에서 자연스럽게 연결됨.

---

### 코드 리뷰 — lst97

**사용 에이전트:** `code-reviewer` + `quality-engineer`

**환경 설정:**
```bash
cp /tmp/lst97/agents/code-reviewer.md ~/.claude/agents/
cp /tmp/lst97/agents/quality-engineer.md ~/.claude/agents/
```

**실전 프롬프트:**
````
@code-reviewer 가 1차, @quality-engineer 가 2차로 검토한다.

## 1차: @code-reviewer
diff:
```
[git diff main...HEAD]
```
- 로직 정확성, 타입 안정성, 네이밍, CLAUDE.md 위반만
- 보안/성능은 1차에서 다루지 마라

## 2차: @quality-engineer
1차 결과 + 다음 추가 점검:
- 관측성 — 로그/메트릭 충분한가
- 장애 복구 — 재시도/타임아웃/회로 차단
- 운영 — 배포 가능 상태인가
- 롤백 가능성

## 최종
- 각 리뷰어의 코멘트 요약
- 머지 가능/조건부/반려
- 조건부라면 **고쳐야 할 것** 3개 이하로 압축
````

**예상 산출물:** 2단계 리뷰 리포트 + 최종 판정.

**주의사항:**
- lst97 의 `quality-engineer` 는 solo/small team 관점이라 **"가능한 것만"** 요구. 과잉 점검 없음.
- 반대로 대규모 팀의 SRE 수준은 아님 (운영 관점이 얕음).

---

### 테스트 자동화 — lst97

**사용 에이전트:** `test-engineer` (33개 중 QA 전담)

**환경 설정:**
```bash
cp /tmp/lst97/agents/test-engineer.md ~/.claude/agents/
```

**실전 프롬프트:**
```
@test-engineer

다음 기능에 테스트를 추가한다.

기능: [예: "책 검색 결과 페이지 (lst97/nextjs-developer 가 방금 구현)"]

목표:
- 단위 6~10개 (순수 함수 + 훅)
- 통합 2개 (서버 액션 레벨, in-memory Postgres)
- 스냅샷은 금지 (대신 data-testid 기반 DOM assertion)

제약:
- Vitest + @testing-library/react
- msw 로 Google Books API 모킹
- 커버리지 목표: statement 80%
- 각 테스트 이름은 "should ..."

진행:
1. 테스트 계획 표 (레벨, 시나리오)
2. 내 승인
3. 테스트 작성
4. `pnpm test book-search` 실행 + 결과
```

**예상 산출물:** 8~12개 테스트 + msw 핸들러 + 실행 결과.

**주의사항:**
- lst97 `test-engineer` 는 스냅샷 테스트를 선호하는 경향이 있음. 금지 명시 필수.
- `@testing-library/react` 버전이 오래되면 `waitFor` API 차이로 실패. 최신 버전 확인.

---

### 멀티 에이전트 협업 — lst97

**사용 에이전트:** `agent-organizer` 한 명이 다른 에이전트를 오케스트레이션 (lst97 의 핵심 기능)

**환경 설정:**
```bash
export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1
cp /tmp/lst97/agents/*.md ~/.claude/agents/   # 33개 전체
```

**실전 프롬프트:**
```
@agent-organizer

기능: [예: "책 하이라이트 공유 — 공개 URL 생성 + OG 이미지 자동 생성"]

너는 아래 에이전트들을 **순차 호출** 한다.
각 단계가 끝나면 내게 승인을 요청한다.

팀 구성:
1. @solution-architect — 플로우 설계 + 영향 파일 목록
2. @drizzle-orm-expert — 공유 테이블 + 토큰 컬럼 마이그레이션
3. @nextjs-developer — /share/[token] 라우트 + OG 이미지 API
4. @tailwind-artist — OG 이미지 레이아웃
5. @test-engineer — 단위 + 통합 + 1 E2E
6. @code-reviewer — 최종 리뷰

규칙:
- 각 에이전트는 자기 영역 외 파일 수정 금지
- 단계 종료 시 planning/share-link.md 에 결과 기록
- 충돌이 있으면 너(organizer)가 중재
- 최종: `pnpm build && pnpm test` 성공 확인
```

**예상 산출물:** 6단계 파이프라인 완료 + planning 파일 + 빌드/테스트 성공.

**주의사항:**
- `agent-organizer` 는 lst97 의 가장 큰 장점. 단, 6명 이상 팀 구성은 권하지 않음 (메타 에이전트의 컨텍스트 한계).
- 단계별 승인을 빼면 organizer 가 한 번에 달려가서 6단계를 연속 실행. "승인 요청" 명시 필수.

---

## 6. SuperClaude-Org/SuperClaude_Framework

프레임워크. 16개 specialist agent + 14개 cognitive persona + 30개 슬래시 커맨드(`/sc:*`). **auto-activation** 이 핵심.

### 프로젝트 설계 — SuperClaude

**사용 에이전트:** `architect` persona + `/sc:design` 커맨드

**환경 설정:**
```bash
pip install SuperClaude
python -m SuperClaude install   # Claude Code 에 설정 주입
# 재시작 후 /sc:* 커맨드 활성화
```

**CLAUDE.md 설정 (SuperClaude 설치가 생성함 — 필요 시 수정):**
```markdown
## SuperClaude Persona 정책
- 기본 모드: architect (설계), frontend (UI), backend (서버)
- 자동 활성화 허용 (키워드 기반)
- 페르소나 전환 시 사용자에게 알림
```

**실전 프롬프트:**
```
/sc:design "[예: 워크아웃 기록 앱 — 운동 로그 + 주간 리포트 + 친구와 공유]"

요구 산출물:
1. 컨텍스트 다이어그램 (Mermaid)
2. 주요 유스케이스 3개 + 시나리오
3. 데이터 모델 (Mermaid erDiagram)
4. 단계별 MVP → v1 → v2 로드맵
5. 비기능 요구 (성능/보안/접근성) 각 1~2줄

제약:
- 솔로 개발, 4주 내 MVP
- 스택은 네가 추천 (이유 포함)
- 파일 수정 금지
```

**예상 산출물:** architect persona 가 자동 활성화된 상태의 설계 문서.

**주의사항:**
- `/sc:design` 은 내부적으로 architect + analyzer persona 를 묶어 호출. 토큰 소모가 일반 프롬프트의 1.5~2배.
- SuperClaude 설치가 CLAUDE.md 를 수정하므로 **설치 전 백업**. 기존 프로젝트의 CLAUDE.md 와 병합 주의.

---

### 코드 분석 — SuperClaude

**사용 에이전트:** `analyzer` persona + `/sc:analyze` 커맨드

**환경 설정:** 위와 동일.

**실전 프롬프트:**
```
/sc:analyze src/

modes:
- structure — 디렉토리 · 레이어
- dependency — 의존 그래프
- hotspot — 자주 변경되는 파일
- risk — 테스트 부재 + 복잡도

출력:
- Markdown 리포트 + Mermaid 의존성 그래프
- 발견한 각 이슈에 대해 제안된 후속 persona (예: refactorer, qa) 추천
- 코드 수정 금지 (analyzer 고정)

⚠️ auto-activation 이 refactorer 를 켜려 하면 거부해라. 이 세션은 분석 전용.
```

**예상 산출물:** 분석 리포트 + 후속 작업 추천 (persona 이름 단위).

**주의사항:**
- SuperClaude 의 auto-activation 은 "리팩토링" 같은 키워드에 반응. 분석 세션에서는 **명시적으로 거부** 해야 세션 오염 방지.
- `/sc:analyze` 는 내부적으로 파일 샘플링을 하지만, 초거대 레포에선 하위 폴더를 명시 권장.

---

### 기능 코딩 — SuperClaude

**사용 에이전트:** `frontend` persona + `/sc:build` 커맨드

**환경 설정:** 위와 동일.

**실전 프롬프트:**
```
/sc:build "주간 운동 리포트 카드 컴포넌트"

persona: frontend (명시)
참조:
- src/components/ (디자인 시스템 위치)
- docs/features/weekly-report.md (AC 5개)

단계:
1. 컴포넌트 트리 설계 (WeeklyReportCard > {Header, StatsGrid, Chart, Footer})
2. Props 타입 + 샘플 데이터
3. 각 서브 컴포넌트 개별 구현
4. 차트는 recharts 사용, 접근성 라벨 필수
5. Storybook 스토리 1개

제약:
- auto-activation 의 qa persona 호출 금지 (테스트는 다음 세션에서 qa 페르소나로 따로)
- iterative mode 활성화 — 각 단계 후 내 피드백 기다려라
```

**예상 산출물:** 컴포넌트 + Storybook + iterative 단계별 확인.

**주의사항:**
- `/sc:build` 는 "구현 + 자동 린팅 + 자동 테스트" 를 묶어 실행할 수 있음. iterative 를 켜지 않으면 한 번에 달려감.
- persona 간 충돌(예: frontend vs performance)이 생기면 사용자가 중재 필요.

---

### 코드 리뷰 — SuperClaude

**사용 에이전트:** `qa` + `security` persona 조합 + `/sc:review` 커맨드

**환경 설정:** 위와 동일.

**실전 프롬프트:**
```
/sc:review --target=staged

personas to activate:
- qa — 테스트/품질
- security — 보안
- performance — 성능

점검 범위:
- staged diff 만 (워킹트리 전체 아님)
- CLAUDE.md 의 금지 규칙
- 3개 persona 각각 독립 의견 후 합의

출력:
- 각 persona 별 섹션
- 충돌하는 의견이 있으면 충돌 지점 명시
- 머지 가능/조건부/반려 + 근거
- 칭찬 금지 (전역 규칙)
```

**예상 산출물:** 3개 persona 의 독립 리뷰 + 합의 판정. 일반 에이전트 리뷰보다 다층적.

**주의사항:**
- 3개 persona 가 동시에 활성화되면 응답이 길어지고 토큰 소모 큼. 작은 PR 에서는 1~2개로 줄이는 게 효율적.
- persona 간 "합의" 는 SuperClaude 의 meta 레이어가 처리. 문제 생기면 persona 를 하나씩 끄며 디버그.

---

### 테스트 자동화 — SuperClaude

**사용 에이전트:** `qa` persona + `/sc:test` 커맨드

**환경 설정:** 위와 동일.

**실전 프롬프트:**
```
/sc:test src/services/workout-service.ts

persona: qa (명시)

요구:
1. 단위 테스트 (Vitest) — 커버리지 85% statement
2. 엣지 케이스 표 작성 후 테스트 변환
3. 테스트 실행 + 실패 원인 수정 (자동 반복 최대 3회)
4. 미커버 라인을 각각 이유와 함께 설명

제약:
- 외부 API(Strava, Apple Health)는 msw 로 모킹
- iterative mode 켜라 — 3회 실패 후엔 멈추고 내 판단 요청
- refactorer persona 자동 활성화 금지 (코드 수정 최소)
```

**예상 산출물:** 테스트 세트 + 자동 반복 실행 로그 + 최종 통과 상태.

**주의사항:**
- `/sc:test` 의 자동 반복(auto-retry) 기능은 편리하지만, 3회로 제한하지 않으면 무한 루프에 빠질 수 있음.
- qa persona 가 refactorer 를 자동 호출해 "테스트 용이성을 위해 코드 수정" 을 시작할 수 있음. 명시적 금지 필요.

---

### 멀티 에이전트 협업 — SuperClaude

**사용 에이전트:** 여러 persona 를 `/sc:workflow` 커맨드로 조합

**환경 설정:**
```bash
export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1
```

**실전 프롬프트:**
```
/sc:workflow "친구 초대 기능 엔드투엔드"

workflow stages (순차):
1. architect — 설계 + 영향 파일
2. backend — API + DB
3. frontend — UI + 훅
4. qa — 테스트 3레벨
5. security — OWASP 점검
6. reviewer — 머지 가부

persona transitions:
- 각 단계 종료 시 사용자 승인 필수
- 이전 단계 산출물은 planning/invite-feature.md 에 기록
- 단계 간 consistency 검증 (SuperClaude 내장)

제약:
- auto-activation 끄기 — 사용자가 명시 전환
- iterative mode ON
- 최종 merge 전 /sc:review 1회
```

**예상 산출물:** 6단계 workflow 완료 + planning 파일 + 최종 리뷰 판정.

**주의사항:**
- `/sc:workflow` 는 SuperClaude 의 가장 고수준 기능. 안정화가 덜 된 부분이 있어 단계 누락/중복이 발생할 수 있음.
- auto-activation 을 끄지 않으면 workflow 가 persona 를 건너뛸 수 있음. `persona transitions` 를 명시.

---

## 7. anthropics/skills

공식 Anthropic 스킬. 서브에이전트가 아니라 **스킬(SKILL.md + 리소스)** 단위. 문서/개발/엔터프라이즈/디자인 4개 카테고리.

> 이 레포는 "에이전트 호출" 이 아니라 "스킬 로드" 패턴입니다. 설치 후엔 Claude Code 가 대화 중 자동으로 스킬을 활성화합니다.

### 프로젝트 설계 — anthropics/skills

**사용 스킬:** `mcp-server-creator` (Development & Technical 카테고리) + 직접 프롬프트

**환경 설정:**
```bash
git clone https://github.com/anthropics/skills.git /tmp/anthropics-skills
mkdir -p ~/.claude/skills
cp -r /tmp/anthropics-skills/skills/development/mcp-server-creator ~/.claude/skills/
```

**실전 프롬프트:**
```
내 프로젝트에 MCP 서버를 붙이고 싶어. 설계 단계.

맥락:
- 프로젝트: [예: "내부 Notion DB 를 조회하는 MCP 서버"]
- 언어: Node.js + TypeScript
- 도구: Notion API + 자체 캐시

mcp-server-creator 스킬을 참조해서 다음을 작성:
1. MCP 서버의 역할 + 노출할 도구 목록 (tool names + schemas)
2. 인증 방식 (Notion token 보관)
3. 캐시 전략 (TTL, 무효화)
4. 에러/재시도 정책
5. 로컬 개발 + 배포(Systemd 또는 Docker) 단계

파일 생성은 하지 말고 설계만.
```

**예상 산출물:** MCP 서버 설계 문서 1건 (도구 목록 + 인증 + 캐시 + 운영).

**주의사항:**
- 스킬은 SKILL.md 의 `description` 을 바탕으로 Claude 가 **자동 활성화** 함. 설계/분석 단계에서 활성화가 안 되면 프롬프트에 "mcp-server-creator 스킬 참조" 를 명시.
- Document skills (docx/pdf/pptx/xlsx) 는 **source-available** — 사내 배포 전에 라이선스 조건을 확인.

---

### 코드 분석 — anthropics/skills

**사용 스킬:** `webapp-testing` (Development & Technical 카테고리, 웹앱 품질 점검 템플릿)

**환경 설정:**
```bash
cp -r /tmp/anthropics-skills/skills/development/webapp-testing ~/.claude/skills/
```

**실전 프롬프트:**
```
`webapp-testing` 스킬을 참조해서 이 웹앱의 현재 품질 상태를 분석한다.

프로젝트: [예: Next.js 15 + tRPC + Postgres]
관심 영역:
- 핵심 플로우: 로그인 → 대시보드 → 설정
- 기존 테스트: e2e 3건, 단위 47건 (커버리지 미측정)

요구:
1. webapp-testing 스킬의 체크리스트에 따라 미검증 항목 식별
2. 우선순위 Top 5 (비즈니스 임팩트 기준)
3. 각 항목에 대해 구현해야 할 테스트 유형 (단위/통합/E2E/접근성/성능)
4. 4주 내 달성 가능한 목표 수치 (커버리지, E2E 수, lighthouse 점수)
5. 파일 수정 금지 (진단만)
```

**예상 산출물:** 품질 진단 리포트 + Top 5 우선순위 + 4주 목표.

**주의사항:**
- anthropics/skills 의 스킬은 대부분 **템플릿·체크리스트 기반**. 직접 코드를 쓰지 않으므로 분석/진단에 강함.
- 스킬이 자동 활성화되지 않으면 첫 줄에 스킬 이름을 명시하면 강제 활성화.

---

### 기능 코딩 — anthropics/skills

**사용 스킬:** 직접 코딩보다 **문서 스킬** 로 산출물 생성 (`docx`, `xlsx`) — 코딩 자체는 일반 세션에서

**환경 설정:**
```bash
cp -r /tmp/anthropics-skills/skills/document/docx ~/.claude/skills/
cp -r /tmp/anthropics-skills/skills/document/xlsx ~/.claude/skills/
```

**실전 프롬프트:**
```
나는 [예: "주간 리포트 자동 생성 기능"] 을 구현하려 한다.

Claude Code 의 docx 와 xlsx 스킬을 활용해:

1. 리포트 템플릿 `.docx` 를 프로젝트에 추가 (docs/templates/weekly-report.template.docx)
2. 동적 데이터를 채우는 Node.js 코드 (src/lib/report-generator.ts)
3. 테스트 데이터로 실제 생성 → `tmp/sample-report.docx` 저장
4. xlsx 로도 같은 데이터를 내보내기 (src/lib/report-generator-xlsx.ts)
5. 사용 예: 단위 테스트 1개 + CLI 커맨드 예시

제약:
- docx/xlsx 스킬의 라이선스는 source-available — 재배포 코드에 포함하지 않는다 (스킬을 호출하는 코드만 작성).
- 생성 로직은 순수 함수로 유지 (의존성 주입).
```

**예상 산출물:** 템플릿 파일 + 생성기 코드 + 샘플 파일 + 테스트.

**주의사항:**
- docx/xlsx 스킬은 **source-available** 로 재배포 금지. 프로젝트에서 스킬 자체를 벤더링하지 말고 **Claude Code 세션에서만** 사용.
- 생성 결과물(파일 자체)은 자신의 저작물이므로 라이선스 제약 없음.

---

### 코드 리뷰 — anthropics/skills

**사용 스킬:** 공식 `review-pr` 또는 유사 스킬 + 직접 프롬프트

**환경 설정:**
```bash
# 리뷰용 스킬 이름은 버전에 따라 다름 — 레포 확인
ls /tmp/anthropics-skills/skills/development/
cp -r /tmp/anthropics-skills/skills/development/<review-*> ~/.claude/skills/
```

**실전 프롬프트:**
````
공식 리뷰 스킬을 참조해서 스테이징된 diff 를 점검한다.

diff:
```
[git diff --staged 결과]
```

점검:
1. Anthropic 의 공식 권장 패턴 (스킬 내부 체크리스트)
2. 보안 — 사용자 입력 검증, 인증/인가
3. 관측성 — 실패 경로 로깅
4. 안전성 — 실수로 사용자 데이터에 영향 주는 변경이 있는가
5. CLAUDE.md 규칙

출력:
- 섹션별 OK/문제
- 머지 가능/조건부/반려
- 조건부면 고쳐야 할 것 3개
````

**예상 산출물:** 공식 스킬 기준의 리뷰 리포트.

**주의사항:**
- 공식 스킬은 **보수적** — false-positive 가 적지만 커버리지도 제한적. 보안 전담 에이전트와 병행 권장.
- 스킬 이름은 레포 업데이트에 따라 변할 수 있으니 `ls` 로 실제 이름 확인.

---

### 테스트 자동화 — anthropics/skills

**사용 스킬:** `webapp-testing`

**환경 설정:** 위 "코드 분석" 과 동일.

**실전 프롬프트:**
```
`webapp-testing` 스킬의 테스트 카탈로그를 기반으로,
다음 페이지에 E2E 테스트 3개를 추가한다.

대상: /app/(dashboard)/settings/page.tsx
기존 E2E: 없음
도구: Playwright

webapp-testing 카탈로그에서 다음에 해당하는 항목 선택:
- Happy path (성공 경로)
- Form validation (필드 검증)
- Authorization (권한 없는 접근 시 리다이렉트)

각 테스트:
- data-testid 기반 selector 사용 (텍스트 아님)
- 각 테스트 독립 (전역 상태 오염 없음)
- 실행 시간 5초 이하
- `pnpm test:e2e settings` 로 실행 후 결과 출력
```

**예상 산출물:** Playwright 테스트 3개 + 실행 결과.

**주의사항:**
- `webapp-testing` 스킬은 카탈로그 제공이 핵심. **사용자가 항목을 고르지 않으면** 스킬이 모든 항목을 나열만 하고 끝날 수 있음.
- 공식 스킬은 **도구 선택을 강요하지 않음** — Playwright / Cypress / Puppeteer 중 사용할 것을 명시 필수.

---

### 멀티 에이전트 협업 — anthropics/skills

**사용 스킬:** 여러 스킬을 **하나의 세션에서 순차 활성화**. 별도 오케스트레이터 없음.

**환경 설정:**
```bash
export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1
cp -r /tmp/anthropics-skills/skills/development/mcp-server-creator ~/.claude/skills/
cp -r /tmp/anthropics-skills/skills/development/webapp-testing ~/.claude/skills/
cp -r /tmp/anthropics-skills/skills/document/docx ~/.claude/skills/
```

**실전 프롬프트:**
```
작업: [예: "내부 Notion MCP 서버 구축 + 품질 리포트 자동 생성"]

3개 스킬을 순차 활용한다:

Phase 1 — mcp-server-creator
- 설계 문서 + 코드 생성 (src/mcp-notion/)
- 내 승인 후 다음 단계

Phase 2 — webapp-testing
- Phase 1 산출물에 대한 테스트 카탈로그 적용
- 단위 + E2E 테스트 작성

Phase 3 — docx
- Phase 1~2 전체 내용을 `docs/reports/mcp-rollout.docx` 로 리포트 생성
- 섹션: 요약 / 아키텍처 / 테스트 / 운영 / 오픈 이슈

규칙:
- 각 phase 는 별도의 "컨텍스트" — 이전 phase 의 파일만 참조
- 스킬이 동시에 활성화되지 않도록 세션 상태를 분리
- phase 종료 시 `planning/mcp-rollout.md` 에 진행 상태 기록
```

**예상 산출물:** 3단계 산출물 (코드 + 테스트 + docx 리포트) + planning 파일.

**주의사항:**
- 스킬 기반 "멀티 에이전트" 는 엄밀히는 **멀티 스킬 순차 활용**. 진짜 병렬 처리는 wshobson/VoltAgent 에 비해 약함.
- docx 스킬은 source-available 이므로 프로덕션 코드에 벤더링하지 말 것.

---

# Part B: 메타 큐레이션 (도구 발견용)

> 아래 레포는 직접 설치하는 에이전트가 아니라, 에이전트/스킬/가이드를 **찾는 허브**입니다.
> "실전 프롬프트" 는 모두 **탐색·선택용**이며, 최종 설치·실행은 발견한 레포의 방식을 따르세요.

---

## 8. hesreallyhim/awesome-claude-code

**메타 큐레이션 리스트**. 레포 자체를 설치하지 않음. 다른 레포를 발견/선택하는 용도.

> 이 섹션의 "실전 프롬프트" 는 모두 **선택·탐색용** 입니다. 최종 설치·실행은 발견한 레포의 방식을 따르세요.

### 프로젝트 설계 — hesreallyhim

**사용 방식:** 리스트 탐색 + 선택

**환경 설정:** 설치 없음. 브라우저로 리스트 열람.

**실전 프롬프트 (Claude Code 세션에서 리스트 읽기):**
```
다음 파일을 읽고 "설계 단계에 쓸 만한 에이전트/스킬" 을 5개 추려라.

파일: https://raw.githubusercontent.com/hesreallyhim/awesome-claude-code/main/README.md

선정 기준:
- "architecture / design / PRD / ADR" 키워드
- 최근 업데이트 (1년 이내)
- MIT/Apache 라이선스 (사내 반입 가능)

출력:
| 이름 | 소속 레포 | 간단 설명 | 설치 방법 | 예상 활용 |
|------|---------|---------|---------|---------|
| ... | ... | ... | ... | ... |

내가 선택하면 그때 설치 방법을 상세히 알려줘.
```

**예상 산출물:** 5개 후보 표 + 각각의 한 줄 설명.

**주의사항:**
- 리스트는 **품질 편차** 가 있음. 반드시 각 레포의 README + 마지막 커밋 날짜 확인.
- WebFetch 도구를 Claude Code 가 쓸 수 있어야 함 (네트워크 허용).

---

### 코드 분석 — hesreallyhim

**사용 방식:** "Tooling" 섹션에서 코드 분석/시각화 도구 탐색

**실전 프롬프트:**
```
hesreallyhim/awesome-claude-code 의 "Tooling" 섹션을 조회하고,
다음 요건에 맞는 3개 도구를 추천한다.

요건:
- 대형 레포(10만 LOC+)의 구조를 시각화할 수 있는 것
- 타입스크립트 + Python 혼합 프로젝트 지원
- CLI 또는 VS Code 플러그인
- 오픈소스

출력:
- 각 도구의 이름, 출처, 설치 방법, 사용 예
- 3개 중 1순위 추천 + 이유
- 설치 전 주의사항
```

**예상 산출물:** 3개 도구 비교 + 1순위 추천.

**주의사항:**
- awesome 리스트의 "Tooling" 섹션은 "범용 유틸" 과 "Claude 전용" 이 섞여 있음. 선정 기준을 Claude 와 무관한 것까지 넓히면 혼선.

---

### 기능 코딩 — hesreallyhim

**사용 방식:** "CLAUDE.md Files" 섹션에서 언어·도메인 특화 템플릿 찾기

**실전 프롬프트:**
```
나는 Rust + Axum + Postgres 프로젝트를 시작한다.

hesreallyhim/awesome-claude-code 의 "CLAUDE.md Files" 섹션을 조회해:
1. Rust 또는 Axum 관련 CLAUDE.md 샘플을 찾아라
2. 가장 잘 쓰인 것 2개를 선정 (명령형 어투 + 금지 규칙 포함)
3. 그 중 하나를 내 프로젝트용으로 수정 (스택 맞춤)
4. CLAUDE.md 생성 후 `cargo check` 와 `cargo test` 명령 섹션 추가
5. 최종본을 프로젝트 루트에 저장

제약:
- 원본 저작권 표기 유지
- 내 프로젝트 특성(모노레포/싱글크레이트 등)을 반영
```

**예상 산출물:** 커뮤니티 CLAUDE.md 기반의 맞춤 버전.

**주의사항:**
- 커뮤니티 템플릿은 라이선스가 다양. 수정·재배포 전 원본 라이선스 확인.
- 스택이 최신이 아니면 (예: `tokio 0.2`) 현재 버전에 맞게 명령을 업데이트.

---

### 코드 리뷰 — hesreallyhim

**사용 방식:** "Workflows & Knowledge Guides" → 리뷰 관련 가이드 찾기

**실전 프롬프트:**
```
awesome-claude-code 의 "Workflows & Knowledge Guides" 섹션에서
"code review" 관련 가이드 3개를 찾아 비교한다.

각 가이드에 대해:
1. 어떤 리뷰 접근(체크리스트/자동/AI-first)을 제안하는가
2. 의존하는 도구나 에이전트 (wshobson/VoltAgent 등 어디 기반인가)
3. 내 프로젝트(Next.js + Postgres, 2인 팀)에 맞는가
4. 바로 적용 가능한 체크리스트 3개 문장

추천:
- 3개 중 1순위 채택 이유
- 내 팀에 맞춘 5분 버전으로 축약
```

**예상 산출물:** 3개 가이드 비교 + 팀 맞춤 축약본.

**주의사항:**
- Workflows 섹션은 개인 블로그/Gist 링크가 많음. 링크 사라짐(link rot) 주의.
- 채택 전 **원문을 반드시 읽기** — 요약만으로 판단하면 누락되는 맥락 많음.

---

### 테스트 자동화 — hesreallyhim

**사용 방식:** "Slash-Commands" 섹션에서 테스트 관련 커맨드 탐색

**실전 프롬프트:**
```
awesome-claude-code 의 "Slash-Commands" 섹션에서
테스트 자동화 관련 커맨드 5개를 추려라.

요건:
- 각 커맨드가 어느 레포에 있고 어떻게 설치하는지
- Vitest / Jest / Playwright 중 어느 것을 지원하는가
- 설치 후 프로젝트에 영향(다른 훅/에이전트와 충돌)
- "테스트 생성 → 실행 → 커버리지 리포트" 전 과정을 한 커맨드로 처리하는 게 있다면 우선

출력:
| 커맨드 | 레포 | 지원 도구 | 설치 | 장점 | 위험 |

1순위 선정 후 설치 단계와 초기 사용 예시 제공.
```

**예상 산출물:** 5개 커맨드 비교 표 + 1순위 추천 + 초기 사용 가이드.

**주의사항:**
- 슬래시 커맨드는 프로젝트마다 **충돌 가능성**이 있음 (같은 이름, 다른 동작). 설치 전 `ls ~/.claude/commands/` 와 `.claude/commands/` 확인.
- 커맨드가 내부에서 임의 코드 실행을 시도할 수 있으므로 설치 전 **소스 코드 리뷰 필수**.

---

### 멀티 에이전트 협업 — hesreallyhim

**사용 방식:** "Workflows & Knowledge Guides → Teams" 섹션에서 팀 구성 패턴 찾기

**환경 설정:**
```bash
export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1
```

**실전 프롬프트:**
```
awesome-claude-code 의 "Workflows > Teams" 하위에서
"multi-agent orchestration" 예시를 2개 찾아라.

각 예시에 대해:
1. 어떤 에이전트들을 팀으로 묶는가 (역할 목록)
2. 통신 방식 (파일 기반 / 메모리 / 오케스트레이터)
3. 장점 / 한계
4. 내 프로젝트에 적용 가능한가
   - 내 스택: Next.js + Postgres
   - 내 팀 크기: 솔로
   - 내 작업: [예: "결제 모듈 리라이트"]

출력:
- 2개 예시 비교
- 내 상황에 맞는 1개 채택
- 채택한 예시의 팀 구성을 내 에이전트 세트(예: wshobson/lst97)로 매핑
- 각 에이전트 호출 순서 + 역할
```

**예상 산출물:** 팀 패턴 비교 + 매핑 + 내 환경 적용 가이드.

**주의사항:**
- awesome-claude-code 에서 찾은 팀 패턴은 **원본 저자 환경 전제** 로 작성된 경우가 많음. 그대로 복사하면 내 환경에서 작동 안 할 수 있음.
- 매핑 단계에서 에이전트 이름/역할이 서로 다를 수 있으므로 **나의 기존 에이전트 목록** 을 프롬프트에 먼저 제공.

---

