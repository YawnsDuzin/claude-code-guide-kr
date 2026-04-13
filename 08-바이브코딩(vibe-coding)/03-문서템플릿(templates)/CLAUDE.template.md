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

```
<프로젝트 루트>
├── src/
│   ├── app/           # Next.js 라우트
│   ├── components/    # 재사용 컴포넌트
│   ├── lib/           # 순수 유틸
│   ├── server/        # 서버 로직
│   └── db/            # 스키마, 마이그레이션
├── tests/
└── docs/              # PRD, architecture, erd
```

## 빌드 / 테스트 / 실행 명령

```bash
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
```

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
