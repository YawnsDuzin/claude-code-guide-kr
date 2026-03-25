# Claude Code 메모리 시스템 (CLAUDE.md)

## 개요

Claude Code의 **메모리 시스템**은 프로젝트에 대한 중요한 정보를 영구적으로 저장하여, 새로운 세션을 시작할 때마다 같은 내용을 반복 설명할 필요가 없도록 해줍니다. 이 메모리는 `CLAUDE.md`라는 특별한 파일에 저장됩니다.

---

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

## 요약

| 항목 | 설명 |
|------|------|
| **CLAUDE.md란** | Claude Code가 자동으로 읽는 프로젝트 메모리 파일 |
| **레벨** | 사용자, 프로젝트, 하위 디렉토리 (3단계) |
| **편집 방법** | 직접 파일 편집 또는 `/memory` 명령어 |
| **포함 내용** | 컨벤션, 아키텍처, 명령어, 스타일 가이드, 주의사항 |
| **핵심 원칙** | 간결하고, 구체적이며, 실행 가능한 규칙 위주로 작성 |

CLAUDE.md를 잘 작성해두면 Claude Code가 프로젝트를 깊이 이해하고, 일관성 있는 코드를 생성할 수 있습니다. 프로젝트를 시작할 때 가장 먼저 CLAUDE.md를 작성하는 것을 권장합니다.
