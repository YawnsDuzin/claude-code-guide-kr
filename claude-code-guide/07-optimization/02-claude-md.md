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

> **다음 단계:** [성능 최적화](./03-performance.md)로 이동하여 Claude Code의 효율을 높이세요.
