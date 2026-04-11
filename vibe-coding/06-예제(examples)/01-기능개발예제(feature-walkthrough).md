# 01. 기능 개발 예제 — 즐겨찾기 기능

> PRD 항목 1개가 실제 코드까지 가는 전체 경로를 단계별로 본다. 각 단계의 입력/출력을 그대로 보여준다.

## 설정

- 프로젝트: Next.js 14 + Drizzle + Postgres
- 팀 규모: 솔로
- 주 에이전트: Claude Code
- 이미 있는 문서: `CLAUDE.md`, `docs/PRD.md`, `docs/architecture.md`, `docs/erd.md`

---

## 0단계: PRD에 항목이 있는가 확인

`docs/PRD.md`의 §2를 열어 확인.

```markdown
### P0
- [ ] F-001 로그인 유저는 상품을 즐겨찾기로 저장/해제할 수 있다
- [ ] F-002 프로필 페이지에서 즐겨찾기 목록을 볼 수 있다
- [ ] F-003 즐겨찾기한 상품이 품절되면 목록에서 품절 표시가 보인다
```

→ 있다. `F-001~F-003`을 이번 세션에 끝낸다.

---

## 1단계: feature-spec 작성 (5분)

`docs/features/favorites.md`를 만들고 [템플릿](../03-문서템플릿(templates)/feature-spec.template.md) 복사.

채운 핵심:
- AC-1 ~ AC-10 (10개 수용 기준)
- 영향 파일 목록 (7개 신규 + 3개 수정)
- ER 다이어그램
- API 계약 3개

여기까지가 **에이전트를 부르기 전** 사람이 할 일. 5분이면 충분합니다.

---

## 2단계: 스키마 변경 먼저 (Expand)

ERD가 바뀌므로 [스키마변경흐름](../01-워크플로우(workflow)/05-스키마변경흐름(schema-flow).md)을 먼저 실행.

### 에이전트에 투입한 프롬프트

[04-스키마마이그레이션 템플릿](../02-프롬프트(prompts)/04-스키마마이그레이션(schema).md)을 채움:

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

### 에이전트 응답 (요약)

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

그리고 `src/db/schema/favorite.ts`와 `erd.md` 업데이트 diff.

### 검증
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

---

## 3단계: 서버 로직 (Coder 세션)

이제 기능 개발. [01-기능개발 프롬프트](../02-프롬프트(prompts)/01-기능개발(feature).md) 사용.

### 프롬프트의 §4 범위에 명시

```markdown
### 포함
- src/server/services/favorite-service.ts (신규)
- src/server/api/routers/favorite.ts (신규)

### 제외
- 프론트엔드 (다음 단계)
- 다른 API 수정
```

### 에이전트의 첫 응답 — "변경 계획"

출력 형식 §7을 따라 **코드 전에 계획**:

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

### 검증
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

---

## 4단계: 프론트엔드

별도 세션 (컨텍스트 오염 방지). [05-UI구현 프롬프트](../02-프롬프트(prompts)/05-UI구현(ui).md).

### §4 텍스트 와이어프레임

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

### 에이전트의 계획

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

### 검증
```bash
pnpm test
pnpm test:e2e tests/e2e/favorites.spec.ts
```

통과. 커밋:
```
feat(ui): favorite button and profile tab (PRD F-001~F-003)
```

---

## 5단계: 문서 동기화

feature-spec의 체크리스트 10개 중 모두 ✅ 확인.

`PRD.md` 업데이트:
```diff
- [ ] F-001 로그인 유저는 상품을 즐겨찾기로 저장/해제할 수 있다
+ [x] F-001 로그인 유저는 상품을 즐겨찾기로 저장/해제할 수 있다
```

`erd.md`는 2단계에서 이미 업데이트됨. `architecture.md`는 새 컴포넌트 없으므로 변경 없음.

최종 커밋:
```
docs: mark F-001~F-003 as done, sync feature spec
```

---

## 결과 요약

| 단계 | 소요 | 커밋 |
|------|------|------|
| 0. PRD 확인 | 1분 | - |
| 1. feature-spec 작성 | 5분 | - |
| 2. 스키마 (Expand) | 15분 | `feat(db): ...` |
| 3. 서버 로직 | 25분 | `feat(api): ...` |
| 4. 프론트엔드 | 30분 | `feat(ui): ...` |
| 5. 문서 동기화 | 5분 | `docs: ...` |
| **총합** | **~80분** | 4 commits |

---

## 관찰 포인트

- **에이전트를 부르기 전 문서 작업이 5분** — 이게 에이전트 세션을 50분에서 30분으로 줄인다
- **각 단계마다 다른 세션** — 스키마/서버/프론트 세 세션으로 분리. 컨텍스트 오염 없음.
- **항상 "계획 → 승인 → 코드"** — 즉시 코드 작성 금지. 잘못된 방향을 일찍 잡음.
- **문서 동기화는 같은 커밋** — 분리하면 영원히 뒤처짐.

---

## 만약 실패했다면?

- 3단계에서 `toggle is idempotent` 테스트가 실패 → [02-버그수정 프롬프트](../02-프롬프트(prompts)/02-버그수정(bugfix).md)로 2단계 프롬프트 사용. "원인 먼저" 요청.
- 4단계에서 브라우저 확인 시 모바일 레이아웃 깨짐 → 에이전트에 "Chrome DevTools 375px 폭에서 sidebar가 스크롤에 끼어있다" 같이 **구체적 증상**으로 지시. "예쁘게 해줘" 금지.
