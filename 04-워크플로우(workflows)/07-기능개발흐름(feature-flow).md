# 02. 기능 개발 흐름 (Feature Development Flow)

> 새 기능을 추가할 때 쓰는 플레이북. 가장 자주 쓰는 흐름.

## 적용 조건

- 기존에 없던 화면/엔드포인트/동작을 추가한다
- PRD에 있는 P0/P1 항목을 구현한다
- "이런 거 되면 좋겠는데" 수준의 아이디어는 먼저 PRD에 넣고 시작한다

## 흐름도

```mermaid
flowchart TD
    Start([시작]) --> S1[1. PRD에서 항목 선택]
    S1 --> S2[2. 영향 범위 식별]
    S2 --> S3{스키마 변경?}
    S3 -- Yes --> S4[스키마변경흐름 먼저 실행]
    S3 -- No --> S5
    S4 --> S5[3. feature-spec 작성]
    S5 --> S6[4. 프롬프트 템플릿 로드]
    S6 --> S7[5. 작업 크기 판단]
    S7 --> S8{3파일 이하?}
    S8 -- Yes --> S9[단일 프롬프트]
    S8 -- No --> S10[단계별 분할]
    S9 --> S11[6. 실행 + 검증]
    S10 --> S11
    S11 --> S12[7. 문서 동기화]
    S12 --> S13[8. 커밋]
    S13 --> End([종료])
```

---

## 단계별 체크리스트

### 1. PRD에서 항목 선택

- `docs/PRD.md`를 연다
- 오늘 구현할 항목 1개를 고른다 (복수 X)
- 그 항목에 `[진행 중]` 표시

### 2. 영향 범위 식별

다음 5가지 중 어디에 영향을 주는지 판단:

```
[ ] DB 스키마
[ ] API / 서버 로직
[ ] 프론트엔드 컴포넌트
[ ] 외부 통합 (결제/메일/푸시 등)
[ ] 설정 / 환경 변수
```

영향 범위가 `DB + API + FE`처럼 3개 이상이면 **반드시 단계별 분할**로 간다.

### 3. feature-spec 작성 (5분)

`docs/features/<feature-name>.md`를 만들고, [07-최적화/04-문서종류분류](../07-최적화(optimization)/04-문서종류분류(document-types).md)의 feature-spec 템플릿을 복사해서 채웁니다.

최소 채울 항목:
- 목적 1줄
- 사용자 시나리오 2~3줄
- 수용 기준(Acceptance Criteria) 3~5개
- 영향 파일 목록

### 4. 프롬프트 템플릿 로드

아래 [프롬프트 템플릿](#프롬프트-템플릿) 섹션을 복사합니다.

### 5. 작업 크기 판단

| 변경 파일 수 | 접근 | 이유 |
|-------------|------|------|
| 1~2개 | 단일 프롬프트 | 컨텍스트가 작아 한 번에 가능 |
| 3~5개 | FE/BE 분할 | 관심사 분리로 검증 쉬움 |
| 6개 이상 | 순차 4단계: DB → API → 로직 → UI | 한 번에 하면 롤백 어려움 |

### 6. 실행 + 검증

- 프롬프트 투입
- 에이전트가 만든 변경 사항을 `git diff`로 직접 본다 (자동 수락 금지)
- 수용 기준을 **하나씩** 확인한다

### 7. 문서 동기화

```
[ ] PRD.md — 항목 상태 [완료]로 변경
[ ] erd.md — 스키마 변경 시 업데이트
[ ] architecture.md — 새 컴포넌트 추가 시 다이어그램 갱신
[ ] CLAUDE.md — 새 패턴/규칙이 생겼으면 추가
```

### 8. 커밋

```
feat(<scope>): <한 줄 요약>

- <바뀐 것 1>
- <바뀐 것 2>

docs: update PRD/erd/architecture
```

문서 변경은 **같은 커밋 또는 같은 PR**에 포함합니다. 분리하면 다음 세션이 오염됩니다.

---

## 안티패턴

1. **스코프 확장** — "어차피 여기 만진 김에"로 다른 기능을 건드리면 PR 리뷰가 불가능해집니다.
2. **수용 기준 없이 시작** — "되게 해줘"로 시작하면 언제 끝났는지 모릅니다.
3. **문서 후일 업데이트** — "나중에 한꺼번에" 하면 아무도 안 합니다.

---

## 프롬프트 템플릿

> 아래 흐름도의 "6. 실행" 단계에서 사용하는 복사용 프롬프트.

```markdown
## 1. 역할
너는 <프로젝트명>의 시니어 풀스택 개발자다.
`CLAUDE.md`와 `docs/PRD.md`의 규칙이 절대 기준이다.

## 2. 참조
- CLAUDE.md
- docs/PRD.md  §<기능 이름>
- docs/architecture.md  §<영향 영역>
- docs/erd.md  (스키마 접근 시)
- docs/features/<feature-name>.md   ← 이 기능의 feature-spec
- 관련 소스:
  - src/<관련 파일 1>
  - src/<관련 파일 2>

## 3. 목적
<feature-spec의 "목적 1줄" 그대로>

## 4. 범위
### 포함
- 백엔드: <엔드포인트/서비스>
- 프론트엔드: <컴포넌트/페이지>
- 테스트: 단위 + 해당 통합 테스트

### 제외
- 다른 기능 리팩토링
- 디자인 시스템 확장
- 문서 외 커밋에 포함 금지

## 5. 완료 기준
feature-spec의 수용 기준을 그대로 복사:
- [ ] <AC-1>
- [ ] <AC-2>
- [ ] <AC-3>
- [ ] 단위 테스트 추가
- [ ] 통합 테스트 1건 이상
- [ ] PRD에서 이 항목 상태 [완료]로 변경
- [ ] architecture.md / erd.md 영향 시 동기화

## 6. 제약
- 금지: <CLAUDE.md의 금지 목록>
- 기존 공개 API 시그니처 유지
- 신규 npm/pip 의존성 도입 시 사전 승인 필요 (이 대화에서 먼저 질문)
- 성능: p95 응답 <200ms (해당 엔드포인트)

## 7. 출력 형식
순서대로:
1. 이해한 내용 2~3줄 요약
2. 변경/신규 파일 트리 + 각 파일 역할
3. 대표 API/함수 시그니처 (코드 작성 전)
4. 위 3단계가 맞다고 하면 → 코드 작성
5. 수용 기준 체크리스트에 체크 결과와 근거(어느 테스트가 덮는지)
```

### 사용 예 (Before/After)

**Before (나쁜 예)**
```
사용자 프로필 페이지에 즐겨찾기 기능 추가해줘
```

**After (좋은 예)**
```
## 1. 역할
너는 shop-web 프로젝트의 시니어 풀스택이다.

## 2. 참조
- CLAUDE.md
- docs/PRD.md §3.2 즐겨찾기
- docs/architecture.md §user-service
- docs/erd.md  (favorite 엔티티 신규)
- src/app/profile/page.tsx
- src/server/user-service.ts

## 3. 목적
로그인 유저가 상품을 즐겨찾기로 저장/해제할 수 있어야 한다 (PRD P0).

## 4. 범위
### 포함
- POST /api/favorites, DELETE /api/favorites/:productId
- GET /api/users/me/favorites (페이지네이션)
- ProfilePage 상단에 즐겨찾기 탭
- favorite 테이블 신규 (Expand 단계)

### 제외
- 즐겨찾기 공유/공개 기능 (PRD P2)
- 이메일 알림
- 모바일 앱

## 5. 완료 기준
- [ ] 로그인 유저가 상품 카드의 하트 아이콘으로 토글 가능
- [ ] 프로필 페이지 "즐겨찾기" 탭에 목록 표시
- [ ] 페이지네이션 20개 단위
- [ ] 비로그인 유저가 토글 시 로그인 모달
- [ ] 단위 테스트 (서비스, 훅)
- [ ] 통합 테스트 1건 (API E2E)
- [ ] PRD §3.2 상태 [완료]
- [ ] erd.md에 favorite 엔티티 반영

## 6. 제약
- any 타입 금지 (CLAUDE.md)
- 신규 의존성 금지
- /api/favorites 에 동일 요청 멱등(idempotent) 처리

## 7. 출력 형식
1. 이해 요약
2. 변경 파일 트리
3. API 시그니처 + Zod 스키마
4. 내 승인 대기
5. 승인 후 코드 작성
```

### 프롬프트 사전 체크리스트

```
[ ] feature-spec 파일이 존재한다
[ ] PRD에 이 항목이 있다 (없으면 먼저 추가)
[ ] 참조 파일 경로가 실제로 존재한다
[ ] 범위 "제외" 섹션이 비어있지 않다
[ ] 완료 기준이 모두 검증 가능한 조건이다
```

---

## 실전 예제 — 즐겨찾기 기능

> PRD 항목 1개가 실제 코드까지 가는 전체 경로를 단계별로 본다. 각 단계의 입력/출력을 그대로 보여준다.

### 설정

- 프로젝트: Next.js 14 + Drizzle + Postgres
- 팀 규모: 솔로
- 주 에이전트: Claude Code
- 이미 있는 문서: `CLAUDE.md`, `docs/PRD.md`, `docs/architecture.md`, `docs/erd.md`

### 0단계: PRD에 항목이 있는가 확인

`docs/PRD.md`의 §2를 열어 확인.

```markdown
### P0
- [ ] F-001 로그인 유저는 상품을 즐겨찾기로 저장/해제할 수 있다
- [ ] F-002 프로필 페이지에서 즐겨찾기 목록을 볼 수 있다
- [ ] F-003 즐겨찾기한 상품이 품절되면 목록에서 품절 표시가 보인다
```

→ 있다. `F-001~F-003`을 이번 세션에 끝낸다.

### 1단계: feature-spec 작성 (5분)

`docs/features/favorites.md`를 만들고 템플릿 복사.

채운 핵심:
- AC-1 ~ AC-10 (10개 수용 기준)
- 영향 파일 목록 (7개 신규 + 3개 수정)
- ER 다이어그램
- API 계약 3개

여기까지가 **에이전트를 부르기 전** 사람이 할 일. 5분이면 충분합니다.

### 2단계: 스키마 변경 먼저 (Expand)

ERD가 바뀌므로 [스키마변경흐름](10-스키마변경흐름(schema-flow).md)을 먼저 실행.

#### 에이전트에 투입한 프롬프트

```markdown
## 변경 이유
즐겨찾기 기능 (PRD F-001). 유저-상품 N:M 관계 테이블 신규.

## 현재 ERD
(erd.md §1 다이어그램 그대로)

## 목표 ERD
(favorite 엔티티 추가한 다이어그램)

## 변경 요약
| 테이블 | 변경 | 호환성 |
|--------|------|--------|
| favorite | 신규 (NOT NULL FK, UNIQUE 복합키) | 호환 (신규) |

## 이 요청은 Expand 단계
(Contract 없음 — 순수 추가)

## 완료 기준
- [ ] up/down 마이그레이션
- [ ] drizzle 스키마 파일
- [ ] erd.md 업데이트
```

#### 에이전트 응답 (요약)

```sql
-- src/db/migrations/0005_add_favorite.up.sql
CREATE TABLE favorite (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES "user"(id) ON DELETE CASCADE,
    product_id UUID NOT NULL REFERENCES product(id) ON DELETE CASCADE,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    UNIQUE(user_id, product_id)
);

CREATE INDEX favorite_user_created_idx
    ON favorite (user_id, created_at DESC);
```

```sql
-- src/db/migrations/0005_add_favorite.down.sql
DROP TABLE IF EXISTS favorite;
```

#### 검증
```bash
pnpm db:migrate:up
pnpm dev  # 앱 기동 확인
pnpm db:migrate:down
pnpm db:migrate:up
```

통과. 여기서 **즉시** 커밋:
```
feat(db): add favorite table (PRD F-001 expand)
```

### 3단계: 서버 로직 (Coder 세션)

별도 세션. 위 프롬프트 템플릿 사용.

#### §4 범위에 명시

```markdown
### 포함
- src/server/services/favorite-service.ts (신규)
- src/server/api/routers/favorite.ts (신규)

### 제외
- 프론트엔드 (다음 단계)
- 다른 API 수정
```

#### 에이전트의 첫 응답 — "변경 계획"

```
1. 이해: favorite_service는 toggle/list/exists 3개 함수. 
   API는 POST/DELETE/GET 3개 엔드포인트.
2. 변경 파일:
   - src/server/services/favorite-service.ts (신규)
   - src/server/api/routers/favorite.ts (신규)
3. 시그니처:
   - toggle(userId, productId) → { state: 'added' | 'removed' }
   - list(userId, { page, limit }) → { items, total }
   - exists(userId, productId[]) → Set<string>  (카드에서 배치 조회용)
4. 내 승인 대기
```

→ "OK 진행해" 응답. 에이전트가 코드 + 단위 테스트 생성.

#### 검증
```bash
pnpm test src/server/services/favorite-service.test.ts
# ✓ toggle adds
# ✓ toggle removes (second call)
# ✓ toggle is idempotent on rapid calls
# ✓ list paginates
```

통과. 커밋:
```
feat(api): favorites service and routes (PRD F-001)
```

### 4단계: 프론트엔드

별도 세션 (컨텍스트 오염 방지). [UI구현흐름](11-UI구현흐름(ui-flow).md)의 프롬프트 사용.

#### 텍스트 와이어프레임

```
<ProductCard>
┌────────────────┐
│ [Image]    ♡/♥│  ← 하트 토글, 우상단
│                │
│ Title          │
│ ₩12,000        │
│                │
│ [장바구니 담기]│
└────────────────┘

<ProfilePage - 즐겨찾기 탭>
┌──────────────────────────────┐
│ [프로필] [주문내역] [즐겨찾기]│
├──────────────────────────────┤
│  3 columns grid              │
│  ...20 cards per page...     │
│  [< 1 2 3 >]                 │
└──────────────────────────────┘

States:
- loading: skeleton 20개
- empty: "즐겨찾기한 상품이 없습니다"
- error: 인라인 에러 + 재시도
```

#### 에이전트의 계획

```
신규 파일:
- src/components/FavoriteButton.tsx
- src/hooks/use-favorite.ts
- src/app/profile/favorites/page.tsx

수정:
- src/components/ProductCard.tsx — FavoriteButton 삽입

Props:
- <FavoriteButton productId: string initialState?: boolean />
- useFavorite(productId): { isFavorited, toggle, isPending }
```

→ 승인 → 코드 생성 → 브라우저에서 확인 → 키보드 조작 확인.

#### 검증
```bash
pnpm test
pnpm test:e2e tests/e2e/favorites.spec.ts
```

통과. 커밋:
```
feat(ui): favorite button and profile tab (PRD F-001~F-003)
```

### 5단계: 문서 동기화

feature-spec의 체크리스트 10개 중 모두 확인.

`PRD.md` 업데이트:
```diff
- [ ] F-001 로그인 유저는 상품을 즐겨찾기로 저장/해제할 수 있다
+ [x] F-001 로그인 유저는 상품을 즐겨찾기로 저장/해제할 수 있다
```

최종 커밋:
```
docs: mark F-001~F-003 as done, sync feature spec
```

### 결과 요약

| 단계 | 소요 | 커밋 |
|------|------|------|
| 0. PRD 확인 | 1분 | - |
| 1. feature-spec 작성 | 5분 | - |
| 2. 스키마 (Expand) | 15분 | `feat(db): ...` |
| 3. 서버 로직 | 25분 | `feat(api): ...` |
| 4. 프론트엔드 | 30분 | `feat(ui): ...` |
| 5. 문서 동기화 | 5분 | `docs: ...` |
| **총합** | **~80분** | 4 commits |

### 관찰 포인트

- **에이전트를 부르기 전 문서 작업이 5분** — 이게 에이전트 세션을 50분에서 30분으로 줄인다
- **각 단계마다 다른 세션** — 스키마/서버/프론트 세 세션으로 분리. 컨텍스트 오염 없음.
- **항상 "계획 → 승인 → 코드"** — 즉시 코드 작성 금지. 잘못된 방향을 일찍 잡음.
- **문서 동기화는 같은 커밋** — 분리하면 영원히 뒤처짐.

### 만약 실패했다면?

- 3단계에서 `toggle is idempotent` 테스트가 실패 → [버그수정흐름](08-버그수정흐름(bugfix-flow).md)의 2단계 프롬프트 사용. "원인 먼저" 요청.
- 4단계에서 브라우저 확인 시 모바일 레이아웃 깨짐 → 에이전트에 "Chrome DevTools 375px 폭에서 sidebar가 스크롤에 끼어있다" 같이 **구체적 증상**으로 지시. "예쁘게 해줘" 금지.
