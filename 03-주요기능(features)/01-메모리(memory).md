# Claude Code 메모리 시스템 (CLAUDE.md)

## 개요

Claude Code의 **메모리 시스템**은 프로젝트에 대한 중요한 정보를 영구적으로 저장하여, 새로운 세션을 시작할 때마다 같은 내용을 반복 설명할 필요가 없도록 해줍니다.

메모리 시스템은 두 가지 축으로 구성됩니다.

| 구분 | CLAUDE.md | Auto Memory |
|------|-----------|-------------|
| **누가 작성** | 사용자가 직접 작성 | Claude가 대화 중 자동 저장 |
| **무엇을 담는가** | 프로젝트 규칙, 컨벤션, 지시사항 | 학습한 패턴, 사용자 선호, 맥락 |
| **저장 위치** | 프로젝트 루트 또는 `~/.claude/` | `~/.claude/projects/<프로젝트ID>/memory/` |
| **Git 커밋** | 보통 커밋 (팀 공유) | 커밋하지 않음 (개인용) |
| **로딩 방식** | 전체 내용 로드 | MEMORY.md 첫 200줄만 로드, 나머지는 필요 시 |

쉽게 말해, **CLAUDE.md는 사용자가 Claude에게 주는 규칙서**이고, **Auto Memory는 Claude가 스스로 작성하는 학습 노트**입니다.

---

---

# Part 1: CLAUDE.md (사용자 작성 메모리)

## CLAUDE.md란?

`CLAUDE.md`는 Claude Code가 세션을 시작할 때 자동으로 읽는 마크다운 파일입니다. 프로젝트의 규칙, 컨벤션, 아키텍처 정보 등을 담아두면 Claude가 이를 참고하여 더 정확한 답변과 코드를 생성합니다.

쉽게 말해, **Claude에게 주는 프로젝트 설명서**라고 생각하면 됩니다.

---

## 메모리의 3가지 레벨

CLAUDE.md는 세 가지 위치에 둘 수 있으며, 각각 다른 범위에 적용됩니다.

### 1. 프로젝트 레벨 (가장 많이 사용)

프로젝트 루트 디렉토리에 위치합니다.

```
my-project/
├── CLAUDE.md          # 프로젝트 루트에 위치
├── .claude/
│   └── CLAUDE.md      # 또는 .claude 디렉토리 안에 위치
├── src/
└── package.json
```

- **적용 범위**: 해당 프로젝트에서 Claude Code를 실행할 때만 적용
- **공유**: Git에 커밋하면 팀 전체가 공유 가능
- **용도**: 프로젝트별 규칙, 코딩 컨벤션, 빌드 명령어 등

### 2. 사용자 레벨

홈 디렉토리의 `.claude` 폴더에 위치합니다.

```
~/.claude/CLAUDE.md
```

- **적용 범위**: 모든 프로젝트에서 공통으로 적용
- **공유**: 개인 설정이므로 공유되지 않음
- **용도**: 개인 선호 코딩 스타일, 자주 사용하는 패턴 등

### 3. 하위 디렉토리 레벨

프로젝트 내 특정 하위 디렉토리에 위치합니다.

```
my-project/
├── CLAUDE.md              # 프로젝트 전체 규칙
├── frontend/
│   └── CLAUDE.md          # 프론트엔드 전용 규칙
└── backend/
    └── CLAUDE.md          # 백엔드 전용 규칙
```

- **적용 범위**: 해당 디렉토리와 하위 디렉토리에서 작업할 때 적용
- **용도**: 모듈별, 패키지별 특화된 규칙

---

## 메모리 우선순위 (Hierarchy)

여러 레벨의 CLAUDE.md가 존재할 경우, 다음 순서로 로드되며 모두 적용됩니다.

```
1. 사용자 레벨     (~/.claude/CLAUDE.md)        - 가장 먼저 로드
2. 프로젝트 레벨   (프로젝트루트/CLAUDE.md)      - 다음으로 로드
3. 하위 디렉토리   (하위폴더/CLAUDE.md)          - 마지막으로 로드
```

중요한 점은 이 파일들이 서로 **덮어쓰지 않고 합쳐진다**는 것입니다. 모든 레벨의 내용이 Claude의 컨텍스트에 포함됩니다. 단, 충돌하는 규칙이 있을 경우 더 구체적인(하위) 레벨이 우선합니다.

---

## CLAUDE.md에 포함할 내용

### 프로젝트 컨벤션

```markdown
## 코딩 컨벤션
- TypeScript 사용, JavaScript 금지
- 함수는 화살표 함수로 작성
- 변수명은 camelCase, 상수는 UPPER_SNAKE_CASE
- import 순서: 외부 라이브러리 → 내부 모듈 → 상대 경로
```

### 아키텍처 개요

```markdown
## 프로젝트 아키텍처
- 모노레포 구조 (turborepo 사용)
- packages/frontend: Next.js 14 App Router
- packages/backend: Express.js + Prisma
- packages/shared: 공유 타입 및 유틸리티
```

### 자주 사용하는 명령어

```markdown
## 주요 명령어
- 개발 서버: `npm run dev`
- 테스트: `npm run test`
- 빌드: `npm run build`
- 린트: `npm run lint`
- DB 마이그레이션: `npx prisma migrate dev`
```

### 스타일 가이드

```markdown
## 스타일 가이드
- CSS: Tailwind CSS 사용, 인라인 스타일 금지
- 컴포넌트: 함수형 컴포넌트만 사용
- 상태관리: Zustand 사용 (Redux 사용하지 않음)
- API 호출: React Query (TanStack Query) 사용
```

### 주의사항 및 금지 규칙

```markdown
## 주의사항
- `.env` 파일을 절대 커밋하지 말 것
- main 브랜치에 직접 push 금지
- API 키를 코드에 하드코딩하지 말 것
- console.log 대신 logger 유틸리티 사용
```

---

## /memory 명령어

대화 중에 `/memory` 명령어를 사용하면 CLAUDE.md를 직접 편집할 수 있습니다.

```
사용자: /memory
```

이 명령어를 실행하면 Claude가 현재 프로젝트의 CLAUDE.md 파일을 열어 편집할 수 있게 해줍니다. 대화 중 발견한 중요한 정보를 바로 메모리에 저장할 때 유용합니다.

### 활용 예시

```
사용자: 이 프로젝트에서 테스트는 항상 vitest를 사용해야 해. 이걸 기억해줘.

Claude: 알겠습니다. CLAUDE.md에 추가하겠습니다.

사용자: /memory
# Claude가 CLAUDE.md를 편집하여 vitest 규칙을 추가합니다
```

---

## 효과적인 CLAUDE.md 작성 모범 사례

### 1. 간결하고 명확하게 작성

```markdown
# 좋은 예
## 테스트
- 테스트 프레임워크: vitest
- 테스트 파일 위치: `__tests__/` 디렉토리
- 네이밍: `*.test.ts`

# 나쁜 예 (너무 장황함)
## 테스트에 대해서
우리 프로젝트에서는 테스트를 작성할 때 vitest라는 프레임워크를 사용합니다.
vitest는 Vite 기반의 테스트 프레임워크로서 매우 빠르고...
(불필요하게 긴 설명)
```

### 2. 실행 가능한 규칙 위주로 작성

Claude가 바로 따를 수 있는 구체적인 규칙을 작성하세요.

```markdown
# 좋은 예
- 새 API 엔드포인트는 `src/routes/` 아래에 생성
- 에러 처리는 반드시 `AppError` 클래스 사용
- 데이터베이스 쿼리는 raw SQL 대신 Prisma ORM 사용

# 나쁜 예 (추상적)
- 깔끔한 코드를 작성하세요
- 좋은 네이밍을 사용하세요
```

### 3. 구조화하여 작성

마크다운 헤더와 리스트를 활용하여 정리하세요.

```markdown
## 프론트엔드
- 프레임워크: Next.js 14
- 스타일링: Tailwind CSS
- 상태관리: Zustand

## 백엔드
- 프레임워크: Express.js
- ORM: Prisma
- DB: PostgreSQL

## 공통
- 언어: TypeScript (strict mode)
- 패키지 매니저: pnpm
- Node.js 버전: 20.x
```

### 4. 필요에 따라 업데이트

프로젝트가 발전하면 CLAUDE.md도 함께 업데이트하세요. 오래된 정보는 혼란을 줄 수 있습니다.

---

## 프로젝트 유형별 CLAUDE.md 예시

### React/Next.js 프로젝트

```markdown
# 프로젝트: My Web App

## 기술 스택
- Next.js 14 (App Router)
- TypeScript (strict mode)
- Tailwind CSS
- Prisma + PostgreSQL
- NextAuth.js (인증)

## 디렉토리 구조
- `src/app/`: 페이지 및 라우트
- `src/components/`: 재사용 컴포넌트
- `src/lib/`: 유틸리티 및 헬퍼
- `src/types/`: TypeScript 타입 정의
- `prisma/`: 데이터베이스 스키마

## 컨벤션
- 컴포넌트 파일명: PascalCase (예: `UserProfile.tsx`)
- 유틸리티 파일명: camelCase (예: `formatDate.ts`)
- 서버 컴포넌트가 기본, 클라이언트 컴포넌트는 `'use client'` 명시
- API Route는 Route Handler 사용 (`route.ts`)

## 명령어
- `pnpm dev`: 개발 서버 (포트 3000)
- `pnpm build`: 프로덕션 빌드
- `pnpm test`: 테스트 실행
- `pnpm db:push`: DB 스키마 푸시
- `pnpm db:studio`: Prisma Studio 실행

## 주의사항
- 새 페이지는 반드시 `loading.tsx`와 `error.tsx` 포함
- 이미지는 `next/image` 컴포넌트 사용 필수
- 환경변수는 `.env.local`에 정의 (커밋 금지)
```

### Python/FastAPI 백엔드 프로젝트

```markdown
# 프로젝트: API Server

## 기술 스택
- Python 3.12
- FastAPI
- SQLAlchemy 2.0 (async)
- PostgreSQL
- Redis (캐싱)
- pytest (테스트)

## 디렉토리 구조
- `app/api/`: API 라우터
- `app/models/`: SQLAlchemy 모델
- `app/schemas/`: Pydantic 스키마
- `app/services/`: 비즈니스 로직
- `app/core/`: 설정, 보안, 의존성
- `tests/`: 테스트 코드
- `alembic/`: DB 마이그레이션

## 컨벤션
- 타입 힌트 필수 (mypy strict 적용)
- docstring은 Google 스타일
- import 정렬: isort 사용
- 포맷터: black (line-length 88)
- 비동기 함수 우선 사용 (async/await)

## 명령어
- `uvicorn app.main:app --reload`: 개발 서버
- `pytest`: 테스트 실행
- `pytest --cov`: 커버리지 포함 테스트
- `alembic upgrade head`: DB 마이그레이션 적용
- `alembic revision --autogenerate -m "설명"`: 마이그레이션 생성

## API 규칙
- 응답은 반드시 Pydantic 스키마로 정의
- 에러는 HTTPException 사용
- 인증: JWT Bearer 토큰
- 페이지네이션: cursor 기반
```

### 모노레포 프로젝트

```markdown
# 프로젝트: Platform Monorepo

## 구조
- turborepo 기반 모노레포
- `apps/web`: Next.js 프론트엔드
- `apps/api`: NestJS 백엔드
- `apps/admin`: 관리자 대시보드
- `packages/ui`: 공유 UI 컴포넌트 라이브러리
- `packages/types`: 공유 TypeScript 타입
- `packages/utils`: 공유 유틸리티 함수

## 명령어
- `pnpm dev`: 모든 앱 동시 실행
- `pnpm dev --filter=web`: web 앱만 실행
- `pnpm build`: 전체 빌드
- `pnpm test`: 전체 테스트
- `pnpm lint`: 전체 린트

## 패키지 간 의존성
- `@platform/ui`: UI 컴포넌트 (apps에서 import)
- `@platform/types`: 공유 타입 (모든 패키지에서 import)
- `@platform/utils`: 유틸리티 (모든 패키지에서 import)

## 주의사항
- 패키지 추가 시 반드시 해당 workspace에서 `pnpm add` 실행
- 공유 타입 변경 시 모든 의존 패키지 확인 필요
- PR은 영향받는 앱의 빌드 통과 필수
```

---

# Part 2: Auto Memory (Claude 자동 메모리)

## Auto Memory란?

Auto Memory는 Claude Code v2.1.59부터 기본 활성화된 기능으로, **대화 중 학습한 정보를 파일로 저장하여 다음 세션에서도 기억**하는 시스템입니다. CLAUDE.md가 "사용자가 작성하는 규칙서"라면, Auto Memory는 "Claude가 작성하는 학습 노트"입니다.

```
사용자: 이 프로젝트에서 테스트는 반드시 실제 DB를 사용해야 해. mock 금지.

Claude: 알겠습니다. 이 규칙을 기억해두겠습니다.
        → [자동으로 memory/feedback_testing.md에 저장]
```

다음 세션에서 Claude는 이 정보를 자동으로 참조하여 테스트 코드를 작성할 때 실제 DB를 사용합니다.

---

## Auto Memory 저장 위치와 구조

Auto Memory는 **사용자 홈 디렉토리**에 프로젝트별로 저장됩니다 (프로젝트 루트가 아님).

```
~/.claude/projects/<프로젝트ID>/memory/
├── MEMORY.md                 # 인덱스 (목차 역할, 세션 시작 시 로드)
├── user_role.md              # 사용자 역할/선호도
├── feedback_testing.md       # 테스트 관련 피드백
├── project_migration.md      # 마이그레이션 진행 상황
└── reference_tracking.md     # 외부 리소스 참조
```

- **같은 Git 저장소**의 모든 worktree와 하위 디렉토리는 **하나의 memory 디렉토리를 공유**합니다
- Git 저장소가 아닌 경우 프로젝트 루트 경로를 기준으로 생성됩니다
- 머신 로컬 저장이므로 **다른 머신이나 클라우드와 공유되지 않습니다**

### MEMORY.md (인덱스 파일)

`MEMORY.md`는 메모리의 목차 역할을 합니다. 세션 시작 시 **첫 200줄(또는 25KB)**만 자동 로드되므로, 간결하게 유지하는 것이 중요합니다.

```markdown
- [사용자 역할](user_role.md) — 시니어 백엔드 개발자, Python 전문
- [테스트 규칙](feedback_testing.md) — DB mock 금지, 실제 DB 사용 필수
- [마이그레이션 진행](project_migration.md) — Phase 2 진행 중, 4/20 마감
- [버그 트래커](reference_tracking.md) — Linear INGEST 프로젝트에서 관리
```

> **주의**: MEMORY.md 자체에 상세 내용을 쓰지 않습니다. 한 줄짜리 요약 + 개별 파일 링크 형태를 유지합니다.

### 개별 메모리 파일 형식

각 메모리 파일은 YAML frontmatter를 가집니다.

```markdown
---
name: testing-strategy
description: 단위 테스트 규칙과 fixture 관리 방법 — 테스트 작성 시 참조
type: feedback
---

통합 테스트는 반드시 실제 DB를 사용, mock 금지.
**Why:** 이전 분기에 mock 테스트는 통과했지만 프로덕션 마이그레이션이 실패한 사고 발생
**How to apply:** 테스트 코드 작성 시 DB 연결 fixture 사용, mock 라이브러리 import 금지
```

| 필드 | 역할 | 비고 |
|------|------|------|
| **name** | 메모리의 식별자 | 파일명과 일치시키면 관리 용이 |
| **description** | 한 줄 설명 | Claude가 이 설명을 보고 해당 메모리를 로드할지 판단 |
| **type** | 메모리 유형 | `user`, `feedback`, `project`, `reference` 중 택 1 |

---

## 메모리 유형 4가지

### 1. User (사용자 정보)

사용자의 역할, 기술 수준, 선호도를 기록합니다. Claude가 응답 수준과 설명 방식을 조절하는 데 사용됩니다.

```markdown
---
name: user-profile
description: 사용자 역할과 기술 수준 — 응답 깊이 조절에 참조
type: user
---

시니어 백엔드 개발자. Go 10년 경력, React는 처음.
프론트엔드 설명 시 백엔드 개념에 비유하면 이해가 빠름.
```

**저장 시점**: 사용자가 역할, 선호도, 기술 수준을 언급할 때

### 2. Feedback (작업 방식 교정)

사용자가 Claude의 작업 방식을 교정하거나, 특정 접근법이 잘 동작함을 확인했을 때 저장됩니다.

```markdown
---
name: feedback-response-style
description: 응답 스타일 피드백 — 간결한 응답, 요약 금지
type: feedback
---

응답 끝에 요약을 달지 않는다. 사용자가 diff를 직접 읽을 수 있다.
**Why:** 사용자가 "매번 요약하지 마"라고 교정
**How to apply:** 모든 응답에서 후행 요약 제거
```

**저장 시점**: 사용자가 교정(`"그렇게 하지 마"`)하거나 확인(`"맞아, 그렇게 해"`)할 때

### 3. Project (프로젝트 상황)

진행 중인 작업, 의사결정, 마감 등 프로젝트 상태를 기록합니다.

```markdown
---
name: project-merge-freeze
description: 모바일 릴리스 브랜치 커팅으로 인한 merge freeze 일정
type: project
---

2026-04-20부터 비핵심 PR merge freeze.
**Why:** 모바일 팀 릴리스 브랜치 커팅
**How to apply:** 이 날짜 이후 예정된 비핵심 PR 작업 시 경고 표시
```

**저장 시점**: 프로젝트 진행 상황, 제약사항, 의사결정이 언급될 때. 상대 날짜(`"목요일"`)는 절대 날짜(`2026-04-20`)로 변환하여 저장

### 4. Reference (외부 리소스 참조)

외부 시스템에서 정보를 찾을 수 있는 위치를 기록합니다.

```markdown
---
name: reference-bug-tracker
description: 버그 트래킹 도구 위치 — 이슈 조회 시 참조
type: reference
---

파이프라인 버그는 Linear 프로젝트 "INGEST"에서 관리.
온콜 대시보드: grafana.internal/d/api-latency — 요청 처리 코드 수정 시 확인 필요.
```

**저장 시점**: 외부 시스템의 리소스와 그 용도를 파악했을 때

---

## Auto Memory에 저장하지 않는 것

| 저장하지 않는 항목 | 이유 |
|-------------------|------|
| 코드 패턴, 아키텍처, 파일 경로 | 코드를 읽으면 알 수 있음 |
| Git 히스토리, 최근 변경사항 | `git log` / `git blame`으로 확인 |
| 디버깅 해결 방법 | 수정은 코드에, 맥락은 커밋 메시지에 존재 |
| CLAUDE.md에 이미 있는 내용 | 중복 |
| 현재 세션에서만 유효한 임시 상태 | 다음 세션에서 의미 없음 |

> **판단 기준**: 코드베이스에서 추론 가능하면 저장 불필요. 추론 불가능하고 반복적으로 필요한 정보만 저장 대상.

---

## Auto Memory 사용 방법

### 자연어로 기억 요청

대화 중 자연어로 Claude에게 기억을 요청할 수 있습니다.

```
사용자: 이 프로젝트에서는 pnpm을 사용해. npm 쓰지 마. 기억해둬.
Claude: 알겠습니다. pnpm 사용 규칙을 기억해두겠습니다.

사용자: API 테스트에 로컬 Redis가 필요하다는 걸 메모해둬.
Claude: Redis 의존성 정보를 저장했습니다.
```

### /memory 명령어

```
사용자: /memory
```

`/memory` 명령어로 현재 세션에 로드된 메모리를 확인하고 관리할 수 있습니다.

- 로드된 CLAUDE.md, CLAUDE.local.md, rules 파일 목록 확인
- Auto Memory 켜기/끄기 토글
- memory 폴더를 파일 탐색기에서 열기
- 개별 메모리 파일을 에디터에서 열어 편집

### 기억 삭제 요청

```
사용자: pnpm 사용 규칙을 잊어줘.
Claude: 해당 메모리를 삭제했습니다.
```

### Auto Memory 비활성화

필요한 경우 Auto Memory를 끌 수 있습니다.

```bash
# /memory 명령어에서 토글

# 또는 settings.json에서 설정
{
  "autoMemoryEnabled": false
}

# 또는 환경 변수
CLAUDE_CODE_DISABLE_AUTO_MEMORY=1
```

---

## Auto Dream (메모리 정리)

### Auto Dream이란?

Auto Dream은 Claude의 **메모리 정리 기능**입니다. 수면 중 기억을 정리하는 것처럼, 세션 사이에 자동으로 메모리를 검토하고 정리합니다.

### 정리 과정 (4단계)

```
Phase 1: Orient (파악)
  └─ MEMORY.md와 모든 토픽 파일을 스캔하여 현재 상태 파악

Phase 2: Gather Signal (수집)
  └─ 모든 메모리 파일의 전체 내용을 읽고 유효성 평가

Phase 3: Consolidation (통합)
  └─ 상대 날짜 → 절대 날짜 변환
  └─ 모순되는 정보 삭제
  └─ 삭제된 파일/해결된 이슈 참조 제거
  └─ 겹치는 항목 병합

Phase 4: Index Update (인덱스 갱신)
  └─ MEMORY.md를 현재 상태에 맞게 업데이트
  └─ 삭제된 토픽 파일의 링크 제거
  └─ 새 토픽 파일 링크 추가
```

### 실행 조건

- **실행 주체**: 사용자가 아닌 **Claude Code가 자동 실행** (사용자 개입 불필요)
- **실행 시점**: 활성 세션 중에는 실행되지 않음. **세션과 세션 사이**에 백그라운드 실행
- **실행 조건**: 마지막 정리 후 **24시간 경과** + **5세션 이상** 진행 시 자동 트리거
- **수동 실행**: 대규모 리팩토링(파일명 대량 변경, 프레임워크 교체 등) 직후에는 사용자가 정리를 요청할 수 있음

### 실행 흐름 예시

```
[세션 1] 작업 → 메모리 축적
[세션 2] 작업 → 메모리 축적
[세션 3] 작업 → 메모리 축적
[세션 4] 작업 → 메모리 축적
[세션 5] 작업 → 메모리 축적
        ↓
  (24시간 + 5세션 조건 충족)
        ↓
[세션 사이] Auto Dream 자동 실행
  → 오래된 메모리 삭제, 중복 병합, 인덱스 갱신
        ↓
[세션 6] 정리된 메모리로 시작
```

### Auto Dream이 필요한 이유

- MEMORY.md를 200줄 이내로 유지하여 세션 시작 시 전체 로드 보장
- 모순되거나 오래된 정보가 쌓이는 것을 방지
- 현재 프로젝트 상태를 반영한 깨끗한 메모리 유지

---

## CLAUDE.md vs Auto Memory 활용 전략

### 언제 CLAUDE.md에 쓰는가?

**규칙, 강제사항, 팀 공유 정보**는 CLAUDE.md에 작성합니다.

```markdown
# CLAUDE.md에 적합한 내용
## 코딩 규칙
- TypeScript strict mode 필수
- console.log 대신 logger 유틸리티 사용
- main 브랜치 직접 push 금지
```

### 언제 Auto Memory에 맡기는가?

**작업 중 자연스럽게 학습되는 패턴**은 Auto Memory에 맡깁니다.

```
# Auto Memory에 적합한 내용
- 사용자가 간결한 응답을 선호한다는 것
- 이 프로젝트의 빌드가 8분 걸린다는 것
- 특정 모듈의 테스트가 Redis에 의존한다는 것
- 버그 트래킹이 Linear에서 이루어진다는 것
```

### 3계층 메모리 전략

```
┌─────────────────────────────────────────────┐
│  CLAUDE.md  (사용자 작성, Git 커밋, 팀 공유)  │  ← 규칙 강제
├─────────────────────────────────────────────┤
│  Auto Memory (Claude 작성, 로컬, 개인)        │  ← 패턴 학습
├─────────────────────────────────────────────┤
│  Auto Dream  (자동 정리, 세션 사이 실행)       │  ← 메모리 유지보수
└─────────────────────────────────────────────┘
```

### 실전 워크플로우 예시

```
[세션 1]
사용자: 이 프로젝트에서 DB mock 쓰지 마. 지난번에 mock 테스트 통과했는데
       프로덕션 마이그레이션 실패했거든.
Claude: 알겠습니다. 실제 DB를 사용하는 테스트 규칙을 기억하겠습니다.
        → feedback_testing.md 저장

[세션 2]
사용자: 주문 서비스 테스트 코드 작성해줘.
Claude: (feedback_testing.md를 자동 로드)
        실제 DB 연결을 사용하는 테스트를 작성하겠습니다...
        → mock 없이 실제 DB fixture로 테스트 코드 생성

[세션 5 이후 — Auto Dream 실행]
Claude: (메모리 정리)
        - feedback_testing.md: 여전히 유효, 유지
        - project_sprint_deadline.md: 마감일 지남, 삭제
        - MEMORY.md: 삭제된 파일 링크 제거, 인덱스 갱신
```

---

## 요약

| 항목 | 설명 |
|------|------|
| **CLAUDE.md** | 사용자가 작성하는 프로젝트 규칙서. 세션 시작 시 전체 로드 |
| **CLAUDE.md 레벨** | 사용자(`~/.claude/`), 프로젝트(루트), 하위 디렉토리 (3단계) |
| **Auto Memory** | Claude가 자동 저장하는 학습 노트. `~/.claude/projects/` 하위 저장 |
| **메모리 유형** | user(사용자), feedback(교정), project(상황), reference(참조) 4종 |
| **Auto Dream** | 세션 사이에 자동 실행되는 메모리 정리 (24시간 + 5세션 조건) |
| **편집 방법** | CLAUDE.md: 직접 편집 / Auto Memory: 자연어 요청 또는 `/memory` |
| **핵심 전략** | CLAUDE.md = 규칙 강제, Auto Memory = 패턴 학습, Auto Dream = 유지보수 |

CLAUDE.md를 잘 작성해두면 Claude Code가 프로젝트 규칙을 정확히 따르고, Auto Memory를 통해 대화에서 학습한 맥락을 다음 세션에서도 유지합니다. 프로젝트를 시작할 때 가장 먼저 CLAUDE.md를 작성하고, 이후 작업 중 교정과 피드백은 Auto Memory에 맡기는 것을 권장합니다.
