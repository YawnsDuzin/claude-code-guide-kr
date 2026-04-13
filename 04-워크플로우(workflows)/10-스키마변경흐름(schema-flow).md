# 05. 스키마 변경 흐름 (Schema Change Flow)

> DB 구조 변경은 바이브 코딩에서 가장 위험한 작업. **ERD 문서가 없으면 시작 금지**.

## 전제 조건

- `docs/erd.md`가 존재하고 현재 상태를 정확히 반영한다
- 프로덕션이면 마이그레이션 롤백 계획이 있다
- 백업이 있다 (프로덕션이면)

---

## 흐름도

```mermaid
flowchart TD
    A[1. 변경 이유 명시] --> B[2. 현재 ERD 확인]
    B --> C[3. 목표 ERD 작성]
    C --> D[4. 호환성 판단]
    D --> E{호환 가능?}
    E -- 깨짐 --> F[확장 단계 분할]
    E -- 호환 --> G[5. 마이그레이션 작성]
    F --> G
    G --> H[6. 롤백 스크립트 작성]
    H --> I[7. 로컬 DB에서 검증]
    I --> J[8. 코드 모델 업데이트]
    J --> K[9. erd.md 업데이트]
    K --> L[10. 커밋 + 리뷰]
```

---

## Before / After ERD 비교

프롬프트에 **반드시** 현재(Before)와 목표(After) ERD 다이어그램을 함께 넣고, 컬럼별 변경 유형을 요약합니다. 두 상태를 나란히 보여줘야 에이전트가 정확한 마이그레이션을 생성합니다.

---

## 호환성 3단계 마이그레이션 (Expand-Contract)

호환 안 되는 변경은 **반드시** 3단계로 나눕니다.

1. **Expand** — 새 컬럼/테이블을 추가하고 기존과 병존시킵니다.
2. **Migrate** — 기존 데이터를 백필하고 새 쪽에서만 읽도록 전환합니다.
3. **Contract** — 기존 컬럼/테이블을 제거합니다.

단일 배포로 스키마와 코드를 동시에 바꾸면, 롤백 시 데이터 손실이 발생합니다.

---

## 롤백 스크립트 필수 템플릿

```sql
-- migrations/20260411_add_legal_name.up.sql
ALTER TABLE users ADD COLUMN legal_name VARCHAR(255);

-- migrations/20260411_add_legal_name.down.sql
ALTER TABLE users DROP COLUMN legal_name;
```

up / down 쌍이 없는 마이그레이션은 머지 금지.

---

## 체크리스트

```
[ ] 현재 erd.md 확인
[ ] 목표 erd.md 작성 (같은 파일 한 섹션 아래)
[ ] 변경 테이블 요약
[ ] 호환성 판단 (호환 or Expand-Contract 필요)
[ ] up/down 마이그레이션
[ ] 로컬 DB에서 up → 앱 동작 → down 검증
[ ] 코드 모델 업데이트
[ ] erd.md에 "After"를 현재 상태로 반영
[ ] 같은 PR에 마이그레이션 + 코드 + 문서 포함
```

---

## 프롬프트 템플릿

> DB 스키마 변경은 가장 되돌리기 어려운 작업. ERD before/after + 롤백 스크립트가 없으면 시작 금지.

```markdown
## 1. 역할
너는 이 프로젝트의 시니어 개발자 + DBA다.
지금은 **Expand-Contract 패턴을 준수하는 스키마 변경**을 작성한다.

## 2. 참조
- CLAUDE.md
- docs/erd.md (현재 상태)
- docs/architecture.md §데이터 레이어
- src/db/migrations/ (마이그레이션 툴: <Flyway / Prisma / TypeORM / Alembic ...>)
- src/models/<관련 모델>

## 3. 변경 이유
<한 문장: 왜 스키마가 바뀌어야 하는가>

## 4. 현재 ERD (Before)
```mermaid
erDiagram
    <현재 상태>
```

## 5. 목표 ERD (After)
```mermaid
erDiagram
    <목표 상태>
```

## 6. 변경 요약
| 테이블/컬럼 | 변경 유형 | 호환성 | 비고 |
|------------|----------|--------|------|
| users.email | UNIQUE 추가 | 깨짐 | 중복 데이터 선제 정리 필요 |
| users.name → display_name | 이름 변경 | 깨짐 | 앱 전역 사용 |
| users.legal_name | 신규 NULLable | 호환 | - |
| users.deleted_at | 신규 NULLable | 호환 | 소프트 삭제 |

## 7. Expand-Contract 단계
- **Expand (PR 1)**: 호환 유지하며 새 컬럼/테이블 추가. 코드는 양쪽 읽기, 새 쪽 쓰기.
- **Migrate (PR 2)**: 백필 스크립트 실행. 코드는 새 쪽만 읽기.
- **Contract (PR 3)**: 기존 컬럼/테이블 제거.

→ 이 요청은 **PR 1 (Expand)** 단계의 작업이다.

## 8. 완료 기준
- [ ] up 마이그레이션 스크립트
- [ ] down 마이그레이션 스크립트 (up의 완전한 역연산)
- [ ] 로컬 DB에서 up → 앱 동작 확인 → down → 원상복구 검증
- [ ] 모델/엔티티 코드 업데이트 (양쪽 호환 읽기)
- [ ] erd.md의 "After" 섹션을 현재 상태로 반영
- [ ] PR 2/PR 3 TODO를 이슈로 생성 (또는 docs/migrations-pending.md에 추가)
- [ ] 기존 테스트 전부 통과

## 9. 제약
- 한 번의 마이그레이션에 호환/비호환 변경 혼합 금지
- up/down 쌍 없는 마이그레이션 금지
- 데이터 삭제 금지 (Contract 단계에서만)
- 프로덕션 DB에 직접 DDL 금지 (마이그레이션 툴만)
- 긴 테이블(100만 행+)에 기본값 있는 NOT NULL 컬럼 추가 금지 (락 유발)

## 10. 출력 형식
1. 호환성 판단: 각 변경이 호환인지 / Expand-Contract가 필요한지
2. up.sql
3. down.sql
4. 모델/엔티티 diff
5. 백필 스크립트 (PR 2에서 쓸 것. 이번 PR에는 포함하지 않음)
6. 검증 절차 (로컬에서 실행할 명령)
7. erd.md 업데이트 diff
```

### "깨짐 / 호환" 빠른 판단표

| 변경 | 호환성 | Expand-Contract 필요? |
|------|--------|-----|
| 컬럼 추가 (NULLable) | 호환 | No |
| 컬럼 추가 (NOT NULL + default) | 작은 테이블에선 호환, 큰 테이블에선 락 | 큰 테이블에선 필요 |
| 컬럼 제거 | 깨짐 | Yes |
| 컬럼 이름 변경 | 깨짐 | Yes (신규 추가 → 백필 → 기존 제거) |
| 컬럼 타입 변경 (축소) | 깨짐 | Yes |
| 컬럼 타입 변경 (확장, e.g. VARCHAR 100→255) | 대부분 호환 | No |
| UNIQUE 제약 추가 | 깨짐 (기존 중복 시) | Yes (먼저 중복 정리) |
| NOT NULL 제약 추가 | 깨짐 (기존 NULL 시) | Yes |
| 인덱스 추가 | 호환 (CONCURRENTLY 사용 시) | No |
| 테이블 이름 변경 | 깨짐 | Yes |

### 프롬프트 사전 체크리스트

```
[ ] erd.md Before/After가 프롬프트에 포함됨
[ ] 변경 요약 표가 호환성 열을 포함함
[ ] Expand-Contract 중 어느 PR인지 명시
[ ] down 스크립트가 up의 완전한 역연산
[ ] 큰 테이블 DDL에 락 유발 변경 없음
```
