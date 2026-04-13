# CLAUDE.md 최적화 가이드

> CLAUDE.md는 Claude Code의 프로젝트 메모리입니다.
> 잘 작성된 CLAUDE.md는 매 세션마다 반복 설명 없이 일관된 결과를 얻게 해줍니다.

---

## 1. CLAUDE.md란?

CLAUDE.md는 Claude Code가 세션 시작 시 자동으로 읽는 설정 파일입니다. 프로젝트의 규칙, 구조, 명령어 등을 담아 Claude에게 **지속적인 컨텍스트**를 제공합니다.

### 파일 위치와 우선순위

| 위치 | 범위 | 용도 |
|------|------|------|
| `~/.claude/CLAUDE.md` | 전역 (모든 프로젝트) | 개인 선호, 공통 규칙 |
| `프로젝트루트/CLAUDE.md` | 프로젝트 전체 | 프로젝트 규칙, 팀 공유 |
| `프로젝트루트/.claude/CLAUDE.md` | 프로젝트 전체 | 위와 동일 (숨김 파일) |
| `하위디렉토리/CLAUDE.md` | 해당 디렉토리 | 디렉토리별 특수 규칙 |

**우선순위:** 하위 디렉토리 > 프로젝트 루트 > 전역

---

## 2. 구조 템플릿

```markdown
# 프로젝트명

## 프로젝트 개요
[1-2줄 설명]

## 기술 스택
- [언어/프레임워크]
- [주요 라이브러리]
- [DB/인프라]

## 프로젝트 구조
[디렉토리 트리]

## 개발 명령어
- 설치: [명령어]
- 개발: [명령어]
- 빌드: [명령어]
- 테스트: [명령어]
- 린트: [명령어]

## 코딩 규칙
- [규칙 1]
- [규칙 2]
- [규칙 3]

## 아키텍처 결정
- [패턴/규칙과 그 이유]

## 주의사항
- [절대 하면 안 되는 것]
- [특별히 신경 써야 할 것]
```

---

## 3. 프로젝트 유형별 예시

> 아래 예시는 상세 설명이 포함된 버전입니다. 바로 복사해서 쓸 수 있는 간결한 템플릿은 [§7. 실전 복사용 템플릿](#7-실전-복사용-템플릿-모음)을 참조하세요.

### React + TypeScript 앱

```markdown
# MyApp 프론트엔드

## 개요
B2B SaaS 대시보드 프론트엔드. React 18 + TypeScript.

## 기술 스택
- React 18, TypeScript 5.4
- 상태 관리: Zustand (전역), React Query (서버 상태)
- 스타일: Tailwind CSS + shadcn/ui
- 폼: React Hook Form + Zod
- 테스트: Vitest + Testing Library
- 빌드: Vite 5

## 구조
src/
├── app/             # 앱 진입점, 라우터, 프로바이더
├── components/
│   ├── ui/          # shadcn/ui 기반 공통 컴포넌트
│   └── features/    # 도메인별 컴포넌트
├── hooks/           # 커스텀 훅
├── lib/             # 유틸리티, API 클라이언트
├── stores/          # Zustand 스토어
├── types/           # 전역 타입 정의
└── test/            # 테스트 유틸, 목업

## 명령어
- npm run dev         → Vite 개발 서버 (localhost:5173)
- npm run build       → 프로덕션 빌드
- npm run test        → Vitest 실행
- npm run test:ui     → Vitest UI 모드
- npm run lint        → ESLint + Prettier 검사
- npm run typecheck   → tsc --noEmit

## 코딩 규칙
- 함수형 컴포넌트만 사용, export function 형태 (export default 금지)
- Props 인터페이스: ComponentNameProps로 명명
- 파일명: PascalCase (컴포넌트), camelCase (유틸/훅)
- API 호출은 반드시 lib/api/ 통해서만
- 새 페이지 추가 시 app/router.tsx에 라우트 등록
- CSS: Tailwind 유틸리티 클래스 사용, 인라인 style 금지
- 이벤트 핸들러: handle 접두사 (handleClick, handleSubmit)

## 주의사항
- node_modules, .env 파일 절대 수정 금지
- shadcn/ui 컴포넌트는 직접 수정하지 말고 래핑해서 사용
- API 응답 타입은 types/api.ts에서 관리
```

### Python FastAPI 서버

```markdown
# OrderService API

## 개요
주문 처리 마이크로서비스. FastAPI + SQLAlchemy.

## 기술 스택
- Python 3.12, FastAPI 0.110
- SQLAlchemy 2.0 (async) + Alembic
- PostgreSQL 16
- Redis (캐싱, 세션)
- Celery (비동기 작업)
- pytest + httpx

## 구조
app/
├── api/
│   ├── v1/routes/     # 버전별 API 엔드포인트
│   └── deps.py        # 공통 의존성 (DB 세션, 인증)
├── core/
│   ├── config.py      # 환경 설정 (Pydantic Settings)
│   └── security.py    # JWT, 비밀번호 해싱
├── models/            # SQLAlchemy 모델
├── schemas/           # Pydantic 스키마 (요청/응답)
├── services/          # 비즈니스 로직
├── repositories/      # DB 접근 계층
└── tasks/             # Celery 비동기 태스크

## 명령어
- uvicorn app.main:app --reload     → 개발 서버
- pytest -v                          → 전체 테스트
- pytest -v -k "test_order"         → 특정 테스트
- alembic revision --autogenerate -m "msg"  → 마이그레이션 생성
- alembic upgrade head               → 마이그레이션 적용
- celery -A app.tasks worker --loglevel=info → 워커 실행

## 코딩 규칙
- 타입 힌트 100% 필수
- Pydantic 모델로 모든 입출력 검증
- DB 접근은 반드시 Repository 계층을 통해서만
- 비즈니스 로직은 Service 계층에 위치 (Route 핸들러에 직접 X)
- async def 기본 사용
- 에러: HTTPException 대신 커스텀 AppException 사용

## API 버전 관리
- 현재: v1 (app/api/v1/)
- 새 엔드포인트는 v1에 추가
- 호환성 깨지는 변경은 v2 디렉토리 생성
```

### 모노레포

```markdown
# MyOrg 모노레포

## 개요
Turborepo 기반 모노레포. 프론트엔드 + 백엔드 + 공유 패키지.

## 구조
├── apps/
│   ├── web/           # Next.js 프론트엔드
│   ├── api/           # Express API 서버
│   └── admin/         # 관리자 대시보드
├── packages/
│   ├── ui/            # 공유 UI 컴포넌트
│   ├── types/         # 공유 TypeScript 타입
│   ├── utils/         # 공유 유틸리티
│   └── config/        # 공유 설정 (ESLint, TS)
└── turbo.json

## 명령어
- pnpm install                → 전체 의존성 설치
- pnpm dev                    → 모든 앱 동시 실행
- pnpm dev --filter=web       → web 앱만 실행
- pnpm build                  → 전체 빌드
- pnpm test                   → 전체 테스트
- pnpm lint                   → 전체 린트

## 규칙
- 앱 간 직접 import 금지, 반드시 packages/ 통해서
- 새 공유 패키지: packages/ 아래에 생성, package.json의 name은 @myorg/ 접두사
- 타입 공유: packages/types에서 관리
- 각 앱의 .env는 해당 앱 디렉토리에 위치

## 주의사항
- pnpm workspace 사용 (npm/yarn 금지)
- turbo.json의 pipeline 수정 시 팀 리뷰 필요
```

---

## 4. 작성 베스트 프랙티스

### DO (권장)

- **200줄 이하**로 유지 (초과 시 컨텍스트 비용 증가)
- **구체적인 예시** 포함 (추상적 규칙보다 효과적)
- **명령어는 실행 가능한 형태**로 (복사-붙여넣기 가능)
- **변경 시 즉시 업데이트** (오래된 정보는 해로움)
- **팀원과 공유** (Git으로 버전 관리)

### DON'T (지양)

- 당연한 것 적지 않기 ("코드를 잘 작성하라" 같은 것)
- 민감 정보 포함하지 않기 (API 키, 비밀번호)
- 너무 세세한 규칙 나열하지 않기 (ESLint가 할 일은 ESLint에게)
- 파일 전체 내용 복사하지 않기 (경로 참조로 대체)

---

## 5. /memory 명령어 활용

세션 중 발견한 규칙을 즉시 CLAUDE.md에 추가할 수 있습니다:

```
/memory "API 응답은 항상 { data, error, meta } 형식을 사용한다"
```

Claude가 자동으로 적절한 CLAUDE.md 파일에 추가합니다.

---

## 6. 팀 협업 팁

| 방법 | 설명 |
|------|------|
| Git 추적 | CLAUDE.md를 커밋하여 팀 전체 공유 |
| PR 리뷰 | CLAUDE.md 변경도 코드 리뷰 대상에 포함 |
| 온보딩 | 새 팀원이 CLAUDE.md를 읽으면 프로젝트 이해 가속 |
| 점진적 개선 | 세션마다 발견한 규칙을 하나씩 추가 |

> **다음 단계:** [성능 및 비용 최적화](./03-성능최적화(performance).md)로 이동하여 Claude Code의 효율을 높이세요.

---

## 7. 실전 복사용 템플릿 모음

프로젝트 유형별로 바로 복사해서 사용할 수 있는 간결한 템플릿입니다.

### 공통 기본 템플릿

모든 프로젝트에 적용할 수 있는 기본 구조입니다.

```markdown
# 프로젝트 개요
- [프로젝트 이름]: [한 줄 설명]
- 주요 기술: [기술 스택 나열]

# 코딩 규칙
- 변수/함수명은 camelCase 사용
- 커밋 메시지는 Conventional Commits 형식 (feat:, fix:, docs: 등)
- 한국어 주석 사용

# 프로젝트 구조
- src/ - 소스 코드
- tests/ - 테스트 코드
- docs/ - 문서

# 명령어
- 빌드: npm run build
- 테스트: npm test
- 린트: npm run lint
```

### Node.js / Express 프로젝트

```markdown
# 프로젝트 개요
- Express.js REST API 서버
- TypeScript, Prisma ORM 사용

# 코딩 규칙
- ESM 모듈 사용 (import/export)
- async/await 사용 (콜백, .then() 금지)
- 에러 처리: 커스텀 AppError 클래스 사용
- 환경 변수: .env 파일 + zod 스키마로 검증

# 프로젝트 구조
- src/routes/ - Express 라우터
- src/controllers/ - 요청/응답 처리
- src/services/ - 비즈니스 로직
- src/middlewares/ - Express 미들웨어
- src/utils/ - 유틸리티 함수
- prisma/schema.prisma - DB 스키마

# 명령어
- 개발 서버: npm run dev
- 빌드: npm run build
- 테스트: npm test
- DB 마이그레이션: npx prisma migrate dev
- DB 시드: npx prisma db seed
- Prisma Studio: npx prisma studio
```

### React Native / Expo 프로젝트

```markdown
# 프로젝트 개요
- Expo SDK 51 기반 모바일 앱
- TypeScript, React Navigation 사용

# 코딩 규칙
- 함수형 컴포넌트 + 커스텀 훅 패턴
- 스타일: StyleSheet.create() 사용
- 플랫폼 분기: Platform.select() 또는 .ios.tsx/.android.tsx
- 네비게이션: Expo Router (파일 기반 라우팅)

# 프로젝트 구조
- app/ - Expo Router 화면
- src/components/ - 공유 컴포넌트
- src/hooks/ - 커스텀 훅
- src/services/ - API 통신
- src/stores/ - 상태 관리
- src/constants/ - 색상, 크기 등 상수
- assets/ - 이미지, 폰트

# 명령어
- 개발 서버: npx expo start
- iOS: npx expo run:ios
- Android: npx expo run:android
- 테스트: npm test
- 린트: npm run lint
```

### Spring Boot / Java 프로젝트

```markdown
# 프로젝트 개요
- Spring Boot 3.x REST API
- Java 17, Gradle 빌드

# 코딩 규칙
- Google Java Style 준수
- 롬복(Lombok) 사용: @Getter, @Builder, @RequiredArgsConstructor
- Record 타입: DTO에 Java Record 사용
- Optional: null 반환 대신 Optional 사용

# 프로젝트 구조
- src/main/java/com/example/
  - controller/ - REST 컨트롤러
  - service/ - 서비스 레이어
  - repository/ - JPA 리포지토리
  - entity/ - JPA 엔티티
  - dto/ - 요청/응답 DTO
  - config/ - 설정 클래스
  - exception/ - 커스텀 예외
- src/main/resources/application.yml - 설정
- src/test/ - 테스트 코드

# 명령어
- 빌드: ./gradlew build
- 실행: ./gradlew bootRun
- 테스트: ./gradlew test
- 클린 빌드: ./gradlew clean build
```

### Flutter / Dart 프로젝트

```markdown
# 프로젝트 개요
- Flutter 3.x 크로스플랫폼 모바일 앱
- Dart 3.x, Riverpod 상태 관리

# 코딩 규칙
- Effective Dart 스타일 준수
- 클래스명: PascalCase, 파일명: snake_case
- 상태 관리: Riverpod + Code Generation
- 라우팅: GoRouter 사용
- 불변 객체: freezed 패키지 사용

# 프로젝트 구조
- lib/
  - main.dart - 앱 진입점
  - app/ - 앱 설정, 라우터
  - features/ - 기능별 모듈 (화면, 위젯, 로직)
  - shared/ - 공유 위젯, 유틸
  - models/ - 데이터 모델
  - services/ - API 서비스

# 명령어
- 실행: flutter run
- 빌드 (Android): flutter build apk
- 빌드 (iOS): flutter build ios
- 테스트: flutter test
- 코드 생성: dart run build_runner build
- 린트: flutter analyze
```

### DevOps / Terraform 프로젝트

```markdown
# 프로젝트 개요
- AWS 인프라를 Terraform으로 관리
- Terraform 1.5+

# 코딩 규칙
- 리소스명: snake_case
- 변수에는 description과 type 필수
- 민감 정보: AWS Secrets Manager 또는 변수의 sensitive = true
- 모듈화: 재사용 가능한 인프라는 modules/에 분리

# 프로젝트 구조
- environments/
  - dev/ - 개발 환경
  - staging/ - 스테이징 환경
  - prod/ - 프로덕션 환경
- modules/ - 재사용 가능한 Terraform 모듈
- scripts/ - 배포 스크립트

# 명령어
- 초기화: terraform init
- 플랜: terraform plan
- 적용: terraform apply
- 상태 확인: terraform state list
- 포맷: terraform fmt -recursive
```

### 전체 CLAUDE.md 템플릿 (명령형, 11섹션)

아래 템플릿은 실무에서 바로 복사해서 사용할 수 있는 **명령형 스타일**의 완성형 CLAUDE.md입니다. `<예시>` 부분을 프로젝트에 맞게 수정하세요.

<details><summary>📋 전체 CLAUDE.md 템플릿</summary>

```markdown
# CLAUDE.md

> 이 파일은 Claude Code가 세션 시작 시 자동으로 읽습니다.
> **명령형 어투**로 규칙을 적으세요. 설명이 아니라 **지시**입니다.

## 프로젝트 한 줄 요약

<예시> 개인 블로그 엔진. Next.js + Markdown 파일 기반. 댓글/방문자 통계 없음.

## 기술 스택

- 언어: <예시: TypeScript 5.x>
- 런타임: <예시: Node.js 20>
- 프레임워크: <예시: Next.js 14 (App Router)>
- DB: <예시: SQLite + Drizzle ORM>
- 스타일: <예시: Tailwind CSS, shadcn/ui>
- 테스트: <예시: Vitest, Playwright>
- 린터: <예시: Biome>
- 패키지 매니저: <예시: pnpm>

## 디렉토리 구조

<프로젝트 루트>
├── src/
│   ├── app/           # Next.js 라우트
│   ├── components/    # 재사용 컴포넌트
│   ├── lib/           # 순수 유틸
│   ├── server/        # 서버 로직
│   └── db/            # 스키마, 마이그레이션
├── tests/
└── docs/              # PRD, architecture, erd

## 빌드 / 테스트 / 실행 명령

# 의존성 설치
pnpm install

# 개발 서버
pnpm dev

# 타입 체크
pnpm typecheck

# 테스트
pnpm test              # 단위
pnpm test:e2e          # E2E

# 빌드
pnpm build

# 린트 + 포맷
pnpm lint
pnpm format

## 참조 문서

작업 전 반드시 다음 문서를 읽어라.

- [docs/PRD.md](./docs/PRD.md) — 제품 요구사항, 기능 우선순위
- [docs/architecture.md](./docs/architecture.md) — 시스템 구조
- [docs/erd.md](./docs/erd.md) — 데이터 모델
- [docs/features/](./docs/features/) — 기능별 상세 스펙

## 코딩 규칙

### 타입
- `any` 금지. 불확실하면 `unknown` 후 좁히기.
- 공개 함수는 명시적 리턴 타입.
- `interface` 대신 `type` (팀 컨벤션).

### 파일/이름
- 컴포넌트 파일: `PascalCase.tsx`
- 훅: `useXxx.ts`
- 유틸: `kebab-case.ts`
- 테스트: `<원본>.test.ts` 또는 `<원본>.spec.ts`

### React
- 함수형 컴포넌트만.
- `useEffect` 최소화. 데이터 패칭은 React Query 훅으로.
- Props는 항상 타입 선언.
- `children` 외엔 destructure.

### 서버
- 요청 검증은 `zod`.
- 공개 API 응답은 `{ ok: true, data }` 또는 `{ ok: false, error }` 형태.
- DB 쿼리는 서비스 레이어에서만. 라우트 핸들러에서 직접 호출 금지.

## 금지 사항

**다음은 절대 하지 마라.**

- `console.log` 커밋 금지 (디버깅용은 커밋 전 제거)
- `// @ts-ignore` / `// @ts-expect-error` 금지
- `as any` 금지
- 새 npm 의존성 추가 시 **사전 승인 필수** (이 대화에서 질문)
- `src/` 바깥에 코드 파일 생성 금지
- 시크릿/키/토큰을 코드에 하드코딩 금지
- 기존 공개 API (`POST /api/*`) 시그니처 변경 금지 (별도 협의 없이)

## 테스트 정책

- 새 기능: 단위 테스트 필수 + 가능하면 E2E 1건
- 버그 수정: 회귀 테스트 필수
- 커버리지 목표: `src/server/*` 80% 이상
- 스냅샷 테스트는 작은 컴포넌트에만

## Git / 커밋

- 브랜치: `feat/<scope>-<short>`, `fix/<scope>-<short>`, `refactor/<scope>`
- 커밋: Conventional Commits (`feat:`, `fix:`, `refactor:`, `docs:`, `test:`, `chore:`)
- 커밋 1개 = 논리적 변경 1개
- 문서 변경은 관련 코드 변경과 **같은 커밋**

## 에이전트 출력 규칙

1. 파일을 수정하기 전에 **변경 계획**을 먼저 말한다.
2. 변경 파일이 3개 이상이면 사용자에게 계속 진행할지 확인한다.
3. 완료 후 **완료 기준 체크리스트**를 재확인한다.
4. `CLAUDE.md`에 없는 규칙이 필요해졌다면 먼저 업데이트를 제안한다.

---

<!-- 프로젝트 진행하며 이 파일을 계속 업데이트하세요. 살아있는 문서입니다. -->
```

</details>

---

## 관련 문서

- **어떤 문서를 만들지**: [04-문서종류분류](./04-문서종류분류(document-types).md) — 필수 4종 / 권장 3종 / 불필요 5종
