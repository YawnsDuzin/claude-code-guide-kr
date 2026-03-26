# 코딩 워크플로우

Claude Code를 활용하여 새로운 기능 구현, API 개발, 테스트 작성 등 일상적인 코딩 작업을 효율적으로 수행하는 방법을 소개합니다.

---

## 1. 새 기능 구현 (Step by Step)

새로운 기능을 체계적으로 구현하는 워크플로우입니다.

### 1단계: 요구사항 정리

```
다음 기능을 구현해야 해:
- 사용자가 게시글에 댓글을 달 수 있는 기능
- 대댓글(중첩 댓글) 지원
- 댓글 수정/삭제 가능
- 작성자만 수정/삭제 가능

현재 프로젝트 구조를 파악하고, 이 기능을 구현하기 위한 계획을 세워줘.
```

### 2단계: 구현 계획 확인 후 실행

```
계획이 좋아. 다음 순서로 구현해줘:
1. 데이터베이스 스키마 (Comment 모델)
2. API 엔드포인트 (CRUD)
3. 서비스 로직
4. 프론트엔드 컴포넌트
5. 테스트 코드
```

### Claude의 예상 동작

- 기존 코드의 패턴과 컨벤션을 분석
- 일관된 스타일로 새 코드를 생성
- 기존 모듈과의 연동을 고려한 구현
- 각 단계마다 구현 내용을 설명

### 핵심 팁

> **중요**: 한 번에 모든 것을 구현하라고 하기보다, 단계별로 나누어 요청하면 품질이 더 높아집니다. 각 단계의 결과를 확인한 후 다음 단계로 진행하세요.

---

## 2. API 엔드포인트 생성

RESTful API 엔드포인트를 생성하는 워크플로우입니다.

### 프롬프트 예시

```
다음 스펙에 맞는 REST API 엔드포인트를 만들어줘:

엔드포인트: POST /api/v1/orders
기능: 새 주문 생성
인증: Bearer 토큰 필요
요청 본문:
{
  "items": [{"productId": "string", "quantity": number}],
  "shippingAddress": {"street": "string", "city": "string", "zipCode": "string"},
  "paymentMethod": "card" | "bank_transfer"
}
응답: 201 Created with 주문 정보
에러: 400 (유효성 검사 실패), 401 (인증 실패), 409 (재고 부족)

기존 프로젝트의 패턴을 따라서 구현해줘.
```

### 예상 결과물

Claude가 생성하는 코드 구조:

```
src/
├── routes/orders.ts          # 라우트 정의
├── controllers/orderController.ts  # 컨트롤러 로직
├── services/orderService.ts       # 비즈니스 로직
├── validators/orderValidator.ts   # 입력값 검증
├── types/order.ts                 # 타입 정의
└── tests/orders.test.ts           # 테스트
```

### 핵심 팁

```
# 기존 패턴을 참고하도록 유도
"기존 /api/v1/products 엔드포인트의 패턴을 참고해서 만들어줘"

# 에러 처리를 명시적으로 요청
"에러 케이스별로 적절한 HTTP 상태 코드와 에러 메시지를 반환하도록 해줘"

# 미들웨어 연동
"기존 인증 미들웨어를 적용하고, 필요하면 권한 검사 미들웨어도 추가해줘"
```

---

## 3. 테스트 코드 작성

단위 테스트와 통합 테스트를 작성하는 워크플로우입니다.

### 단위 테스트 (Unit Test)

```
src/services/orderService.ts의 단위 테스트를 작성해줘.
다음 케이스를 포함해줘:
1. 정상적인 주문 생성
2. 재고 부족 시 에러
3. 유효하지 않은 상품 ID
4. 빈 주문 항목
5. 결제 방법별 처리

외부 의존성(DB, 외부 API)은 모킹해줘.
```

### 통합 테스트 (Integration Test)

```
주문 API의 통합 테스트를 작성해줘:
1. POST /api/v1/orders - 주문 생성 성공
2. POST /api/v1/orders - 인증 없이 요청 시 401
3. POST /api/v1/orders - 잘못된 요청 본문으로 400
4. GET /api/v1/orders/:id - 주문 조회
5. GET /api/v1/orders/:id - 다른 사용자의 주문 조회 시 403

테스트 DB를 사용하고, 각 테스트 전에 시드 데이터를 넣어줘.
```

### 기존 코드에 테스트 추가

```
src/utils/dateHelper.ts 파일에 대한 테스트가 없어.
이 파일의 모든 exported 함수에 대한 테스트를 작성해줘.
엣지 케이스(null, undefined, 빈 문자열, 경계값)도 포함해줘.
```

### Claude의 예상 동작

- 프로젝트의 테스트 프레임워크(Jest, Vitest, pytest 등)를 자동 감지
- 기존 테스트 파일의 패턴과 컨벤션을 따르기
- describe/it 구조로 체계적인 테스트 구성
- 적절한 모킹과 fixture 설정

### 핵심 팁

> **중요**: "테스트를 작성해줘"라고만 하면 해피 패스(happy path)만 테스트할 수 있습니다. 에러 케이스와 엣지 케이스를 명시적으로 요청하세요.

---

## 4. 타입 정의와 인터페이스 생성

TypeScript 타입이나 인터페이스를 체계적으로 정의합니다.

### 프롬프트 예시

```
다음 API 응답을 기반으로 TypeScript 타입을 정의해줘:

{
  "user": {
    "id": "uuid",
    "name": "홍길동",
    "email": "hong@example.com",
    "role": "admin" | "user" | "moderator",
    "profile": {
      "avatar": "url 또는 null",
      "bio": "string 또는 null",
      "socialLinks": [{"platform": "github", "url": "..."}]
    },
    "createdAt": "ISO 8601 날짜"
  }
}

요구사항:
- strict 모드에 맞게 nullable 필드 처리
- 재사용 가능하도록 분리 (User, UserProfile, SocialLink 등)
- API 요청/응답용 타입도 함께 생성
```

### 예상 결과물

```typescript
// types/user.ts
export interface SocialLink {
  platform: 'github' | 'twitter' | 'linkedin';
  url: string;
}

export interface UserProfile {
  avatar: string | null;
  bio: string | null;
  socialLinks: SocialLink[];
}

export interface User {
  id: string;
  name: string;
  email: string;
  role: 'admin' | 'user' | 'moderator';
  profile: UserProfile;
  createdAt: string;
}

// API 요청/응답 타입
export interface CreateUserRequest {
  name: string;
  email: string;
  role?: User['role'];
  profile?: Partial<UserProfile>;
}

export interface UserResponse {
  user: User;
}
```

### 핵심 팁

```
# 기존 타입에서 파생
"기존 User 타입을 기반으로 UserUpdateRequest 타입을 만들어줘. 모든 필드를 optional로"

# JSON에서 타입 생성
"이 JSON 응답을 붙여넣을게. 여기서 TypeScript 인터페이스를 추출해줘"

# Zod 스키마와 타입 동시 생성
"Zod 스키마로 유효성 검사와 타입 추론이 동시에 가능하도록 만들어줘"
```

---

## 5. 데이터베이스 마이그레이션 작성

데이터베이스 스키마 변경을 위한 마이그레이션을 작성합니다.

### 프롬프트 예시

```
다음 요구사항에 맞는 DB 마이그레이션을 작성해줘:

1. comments 테이블 생성
   - id: UUID, 기본키
   - post_id: posts 테이블 외래키
   - author_id: users 테이블 외래키
   - parent_id: 자기 참조 외래키 (대댓글용, nullable)
   - content: TEXT, not null
   - created_at, updated_at: 타임스탬프

2. 인덱스: post_id, author_id, parent_id
3. 소프트 삭제를 위한 deleted_at 컬럼

기존 마이그레이션 파일의 패턴을 따라줘.
```

### Claude의 예상 동작

- 프로젝트의 ORM(Prisma, TypeORM, Knex, Alembic 등)을 감지
- 기존 마이그레이션 파일의 형식을 따르기
- Up/Down 마이그레이션 모두 생성
- 인덱스와 제약 조건을 적절히 설정

### 핵심 팁

```
# Prisma 스키마 수정
"Prisma 스키마에 Comment 모델을 추가하고 npx prisma migrate dev를 실행해줘"

# 기존 테이블 수정
"users 테이블에 phone_number 컬럼을 추가하는 마이그레이션을 만들어줘.
기존 데이터를 고려해서 nullable로 설정하고, 향후 필수로 변경할 계획이야"

# 데이터 마이그레이션 포함
"role 컬럼을 문자열에서 enum으로 변경하는 마이그레이션을 만들어줘.
기존 데이터를 새 enum 값으로 변환하는 로직도 포함해줘"
```

---

## 6. 프론트엔드 컴포넌트 생성

재사용 가능한 UI 컴포넌트를 생성합니다.

### 프롬프트 예시

```
재사용 가능한 Modal 컴포넌트를 만들어줘:

요구사항:
- 제목, 본문, 액션 버튼을 props로 받기
- ESC 키로 닫기
- 백드롭 클릭으로 닫기
- 애니메이션 (fade in/out)
- 접근성: aria 속성, 포커스 트랩
- 포탈(Portal) 사용
- 기존 프로젝트의 디자인 시스템/스타일링 방식을 따르기
```

### 예상 결과물

Claude가 프로젝트를 분석하여 적절한 스타일링 방식(CSS Modules, Tailwind, styled-components 등)으로 구현:

```
src/components/
├── Modal/
│   ├── Modal.tsx          # 메인 컴포넌트
│   ├── Modal.test.tsx     # 테스트
│   ├── Modal.stories.tsx  # Storybook (있는 경우)
│   └── index.ts           # 배럴 export
```

### 핵심 팁

```
# 기존 컴포넌트를 참고
"기존 Dialog 컴포넌트와 같은 패턴으로 ConfirmModal 컴포넌트를 만들어줘"

# 상태 관리 포함
"검색 필터 컴포넌트를 만들어줘. 필터 상태를 URL 쿼리 파라미터와 동기화해줘"

# 데이터 페칭 포함
"사용자 목록을 보여주는 UserTable 컴포넌트를 만들어줘.
페이지네이션, 정렬, 검색을 지원하고 React Query로 데이터를 가져와줘"

# 폼 컴포넌트
"React Hook Form과 Zod를 사용한 회원가입 폼 컴포넌트를 만들어줘.
실시간 유효성 검사와 에러 메시지를 포함해줘"
```

---

## 코딩 워크플로우 모범 사례

### 1. 점진적 구현

한 번에 전체를 구현하지 말고 단계별로 진행합니다:

```
# 좋은 방식 - 단계별 요청
"먼저 데이터 모델만 만들어줘"
→ 확인 후
"이제 이 모델을 사용하는 서비스 로직을 구현해줘"
→ 확인 후
"API 엔드포인트를 추가해줘"
```

### 2. 컨텍스트 제공

기존 코드 패턴을 명시적으로 알려줍니다:

```
# 좋은 방식 - 참고 파일 지정
"src/services/userService.ts와 같은 패턴으로 orderService.ts를 만들어줘"

# 좋은 방식 - 프로젝트 컨벤션 명시
"이 프로젝트는 에러를 custom Error 클래스로 처리해. AppError를 확인하고 같은 방식으로 해줘"
```

### 3. 검증 요청

구현 후 검증을 요청합니다:

```
"방금 만든 코드를 빌드해서 에러가 없는지 확인해줘"
"타입 에러가 없는지 tsc --noEmit으로 확인해줘"
"테스트를 실행해서 모두 통과하는지 확인해줘"
```

---

## 핵심 정리

| 작업 유형 | 핵심 프롬프트 | 필수 포함 정보 |
|-----------|-------------|---------------|
| 기능 구현 | "다음 기능을 구현해줘" | 요구사항, 참고 패턴 |
| API 생성 | "API 엔드포인트를 만들어줘" | 스펙 (HTTP 메서드, 경로, 요청/응답) |
| 테스트 작성 | "테스트를 작성해줘" | 테스트 케이스 목록, 엣지 케이스 |
| 타입 정의 | "타입을 정의해줘" | JSON 예시 또는 스펙 |
| DB 마이그레이션 | "마이그레이션을 작성해줘" | 스키마 변경 사항 |
| 컴포넌트 생성 | "컴포넌트를 만들어줘" | 요구사항, 디자인 참고 |

> **팁**: 항상 "기존 프로젝트의 패턴을 따라줘"를 포함하면 Claude가 프로젝트 컨벤션에 맞는 코드를 생성합니다.
