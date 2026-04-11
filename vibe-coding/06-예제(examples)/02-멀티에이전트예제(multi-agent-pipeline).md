# 02. 멀티 에이전트 파이프라인 예제

> 큰 리팩토링을 Planner → Coder → Reviewer → Tester 4-role로 나눠 실행한 사례.

## 설정

- 작업: `src/services/order-service.ts` (847줄, 4개 관심사 혼재)를 4개 서비스로 분리
- 도구:
  - **Planner**: Claude Code 세션 A (읽기만)
  - **Coder**: Claude Code 세션 B (쓰기)
  - **Reviewer**: Claude Code 세션 C (diff만 본다)
  - **Tester**: Claude Code 세션 D (테스트 파일만)
- 리스크: 높음 (결제 경로 포함)

---

## 단계 1: Planner 세션 (세션 A)

### 투입 프롬프트

[02-역할기반협업 Planner 템플릿](../04-에이전트(agents)/02-역할기반협업(role-based).md#planner-프롬프트)을 그대로 복사해서 채움.

```markdown
## 역할
너는 Planner다. 코드를 수정하지 않는다. 계획만 만든다.

## 참조
- CLAUDE.md
- docs/architecture.md §주문 플로우
- src/services/order-service.ts
- src/services/__tests__/order-service.spec.ts

## 작업
"order-service를 4개 서비스로 분리" 계획 수립.

## 출력
planning/order-refactor.md 생성.
...
```

### 산출물: `planning/order-refactor.md`

```markdown
# Plan: order-service 리팩토링

## 목표
847줄 order-service를 4개 서비스로 분리. 공개 API 유지.

## 현재 진단
- 4개 관심사: OrderCreation(320줄) / Payment(180줄) / Shipping(220줄) / Notification(127줄)
- 테스트는 `spec.ts` 580줄, 통과 중
- 모킹 22개 (리팩토링 후 10개 이하 목표)

## 영향 파일
- 신규:
  - src/services/order-creation-service.ts
  - src/services/payment-service.ts
  - src/services/shipping-service.ts
  - src/services/notification-service.ts
  - src/services/__tests__/characterization.spec.ts
- 수정:
  - src/services/order-service.ts (단계별로 축소)
  - src/api/routes/orders.ts (의존성만 재배선, 시그니처 유지)

## 단계
### Step 1: 특성화 테스트 추가
- 기존 spec.ts 외에, 주문 생성부터 알림까지 full roundtrip을 테스트하는 characterization test 5개 추가
- 모킹 사용 금지 (실제 DB + 인메모리 Mail fake)
- **공개 API 시그니처를 프리즈** 하는 역할

### Step 2: PaymentService 추출
- Payment 관련 180줄을 `payment-service.ts`로 이동
- order-service는 PaymentService 주입받아 위임
- 기존 테스트 + characterization 테스트 모두 통과 확인

### Step 3: ShippingService 추출
### Step 4: NotificationService 추출
### Step 5: OrderCreationService 추출
### Step 6: order-service.ts 제거 (또는 얇은 facade만)

## 위험
- HIGH: 결제 경로 변경. 회귀 시 유저 돈 손실 위험.
- MED: 알림 중복 발송 (idempotency key 누락 시)
- LOW: 로그 포맷 변화

## 보존 규칙
- POST /api/orders 요청/응답 스키마 불변
- 에러 코드 불변 (`ORDER_*`)
- 기존 spec.ts 수정 금지 (새 테스트만 추가)
- DB 스키마 변경 금지

## 완료 기준
- [ ] 6개 단계 모두 통과
- [ ] characterization 테스트 5개 추가
- [ ] 기존 spec.ts 유지 + 전부 통과
- [ ] 모킹 수 22 → 10 이하
- [ ] order-service.ts 100줄 이하 또는 제거
```

Planner 세션 종료. 계획 파일 커밋:
```
docs(plan): order-service refactor plan
```

---

## 단계 2: Coder 세션 - Step 1 실행 (세션 B)

### 투입 프롬프트

```markdown
## 역할
Coder다. planning/order-refactor.md를 따른다. 계획에서 벗어나지 마라.

## 참조
- planning/order-refactor.md (필독)
- src/services/order-service.ts
- src/services/__tests__/order-service.spec.ts
- src/db/ (읽기)

## 작업
Step 1만 수행: characterization 테스트 5개 추가.

## 제약
- 프로덕션 코드 0줄 변경
- 테스트 파일 외 건드리지 마라
- 모킹 없이 실제 DB + in-memory fake 사용

## 출력
1. 새 파일: src/services/__tests__/characterization.spec.ts
2. diff
3. 실행 명령과 결과
```

### Coder 응답

```typescript
// src/services/__tests__/characterization.spec.ts
describe('order-service characterization', () => {
  // 1. successful full flow
  // 2. payment failure rollback
  // 3. shipping failure notification
  // 4. duplicate order request idempotency
  // 5. partial refund path
  // ... 구체 코드
});
```

`pnpm test characterization` 5개 통과. 커밋:
```
test(order): add characterization tests before refactor
```

세션 B 종료.

---

## 단계 3: Coder 세션 - Step 2 (세션 B-2, 새 세션)

> 중요: 같은 세션을 재사용하지 않는다. Step 간 컨텍스트 분리.

### 투입 프롬프트

```markdown
## 역할
Coder. Step 2만 수행.

## 참조
- planning/order-refactor.md §Step 2
- src/services/order-service.ts
- 이전 커밋: <hash of characterization>

## 작업
PaymentService 추출:
- payment-service.ts 생성
- order-service는 주입받아 위임 (공개 API 그대로)

## 제약
- characterization 테스트 + 기존 spec.ts 모두 통과해야 한다
- 공개 API 시그니처 불변
- Payment 관련 메서드만 이동. 다른 거 건드리지 마라.

## 출력
1. 계획: 어떤 메서드를 어떻게 이동시킬지 (코드 전에)
2. 내 승인 대기
3. 승인 후 diff
4. 테스트 결과
```

### 계획 응답

```
이동 대상:
- processPayment() [L120-160]
- refundPayment() [L161-200]
- verifyPaymentStatus() [L201-220]
- 3개의 private helper

남기는 것:
- order-service.processPayment는 PaymentService 주입 받아 호출만

새 파일:
- src/services/payment-service.ts

공개 API:
- class PaymentService { process(ctx), refund(ctx), verifyStatus(id) }

Waiting for approval.
```

"OK" → 코드 작성 → `pnpm test` 통과 → 세션 B-2 종료.

커밋:
```
refactor(order): extract PaymentService (step 2/6)
```

---

## 단계 4: Reviewer 세션 (세션 C)

> 별도 세션. Planner/Coder가 뭘 했는지 모르는 상태.

### 투입 프롬프트

```markdown
## 역할
독립 Reviewer. 외부 사람이다. 이 프로젝트 맥락 모른다.
칭찬 금지.

## 참조
- CLAUDE.md

## 입력
다음 diff만 본다.

```diff
<git show HEAD의 diff 붙여넣기>
```

## 점검
1. CLAUDE.md 규칙 위반?
2. 엣지 케이스 누락?
3. 테스트 커버리지 충분?
4. 공개 API 변경? (의도된 것?)
5. 보안/권한 체크?
6. 성능 이상?

## 출력
각 항목: OK 또는 "문제: ..." + 수정 제안
마지막: **머지 가능 / 조건부 / 반려**
```

### Reviewer 응답 (실제 사례)

```
1. CLAUDE.md: OK
2. 엣지 케이스: 문제 — payment-service.process() 에서 timeout 시 retry 없음.
   기존 order-service.processPayment는 3회 retry. 이동 과정에서 사라진 듯.
3. 테스트: 문제 — PaymentService 단위 테스트가 0개. 
   characterization으로 간접 커버되나 직접 단위 테스트 필요.
4. 공개 API: OK (변경 없음)
5. 보안: OK
6. 성능: OK

판정: **조건부** — retry 로직 복구 + PaymentService 단위 테스트 5개 추가 후 재리뷰.
```

중요한 회귀를 잡음. Coder 세션 B-2로 돌아가 수정.

---

## 단계 5: Coder 재수정 (세션 B-3)

```markdown
## 역할
Coder. Reviewer의 피드백 2개를 반영한다.

## 피드백
1. PaymentService.process()에 retry 3회 복구 (기존 order-service 로직 참조)
2. PaymentService 단위 테스트 5개 추가

## 참조
- planning/order-refactor.md
- 기존 order-service에서 사라진 retry 로직 복원용

## 제약
- 계획에 없는 변경 금지
- 기존 테스트 유지
```

수정 → 테스트 통과 → 다시 Reviewer 세션 C에 diff 투입 → "머지 가능" 판정.

커밋:
```
fix(order): restore payment retry and add unit tests (step 2 review fix)
```

---

## 단계 6: Tester 세션 (세션 D)

Step 2가 머지 가능해진 후, 테스트 보강 전담 세션.

### 투입 프롬프트

```markdown
## 역할
Tester. 프로덕션 코드 수정 금지. 테스트만.

## 참조
- src/services/payment-service.ts
- src/services/payment-service.test.ts (방금 추가됨)

## 작업
PaymentService의 실패 경로 테스트를 3개 더 추가:
1. DB 연결 실패
2. 결제 게이트웨이 500 응답
3. 중복 결제 시도 (동일 idempotency key)

## 제약
- 모킹 최소화
- 프로덕션 코드 수정 금지
```

테스트 3개 추가 → 통과 → 커밋:
```
test(payment): add failure path tests (step 2 hardening)
```

---

## Step 3~6 반복

같은 패턴으로 ShippingService / NotificationService / OrderCreationService 추출.
각 단계마다:
1. Coder 세션 (새로운)
2. Reviewer 세션 (새로운)
3. 필요 시 Coder 재수정
4. Tester 세션

---

## 결과

| 지표 | Before | After |
|------|--------|-------|
| order-service.ts 줄수 | 847 | 52 (facade) |
| 모킹 수 | 22 | 8 |
| 관심사 분리 | 없음 | 4개 서비스 |
| 기존 테스트 통과 | ✅ | ✅ (하나도 수정 안 함) |
| 신규 단위 테스트 | 0 | 35 |
| characterization 테스트 | 0 | 5 |
| 공개 API 변경 | - | 0 |
| 결제 회귀 | - | 0 (retry 복구 덕분) |
| 총 세션 수 | - | 14개 (각 역할 3~4회) |

---

## 교훈

1. **Planner 분리로 범위 고정** — Coder가 다른 파일 못 건드림
2. **Reviewer 분리로 회귀 발견** — 같은 세션이었다면 retry 누락을 놓쳤을 것
3. **새 세션마다 컨텍스트 초기화** — planning/ 파일이 컨텍스트 핸드오프 역할
4. **각 단계 = 머지 가능한 그린 상태** — 중간에 멈춰도 손해 없음
5. **전체 소요 시간은 단일 세션 대비 길지만**, 회귀 없이 끝난 게 더 큰 이득

---

## 이 패턴을 언제 쓰는가

- 변경 파일 10개 이상
- 고위험 영역 (결제 / 인증 / 데이터 마이그레이션)
- 기존 테스트가 약한 곳
- "그냥 정리" 아닌 구조 변경

작은 버그 수정에 이 패턴 쓰면 오버엔지니어링입니다. [01-멀티에이전트패턴의 선택표](../04-에이전트(agents)/01-멀티에이전트패턴(multi-agent-patterns).md#패턴-선택표) 참고.
