# 백엔드 기술스택 가이드

> Claude Code를 활용한 백엔드 개발 가이드입니다.
> Node.js, Python, Go, .NET 등 주요 프레임워크별 활용법을 다룹니다.

---

## 1. Node.js / Express

### CLAUDE.md 설정 예시

```markdown
# Node.js Express API 프로젝트

## 기술 스택
- Node.js 20 + TypeScript
- Express 4
- Prisma ORM + PostgreSQL
- 인증: JWT + Passport
- 검증: Zod
- 테스트: Jest + Supertest

## 프로젝트 구조
src/
├── routes/         # 라우트 정의
├── controllers/    # 요청 처리 로직
├── services/       # 비즈니스 로직
├── models/         # Prisma 스키마 기반 타입
├── middleware/      # 인증, 에러 핸들링 등
├── utils/          # 유틸리티 함수
└── config/         # 환경 설정

## 코딩 규칙
- Controller → Service → Repository 계층 분리
- 에러는 커스텀 AppError 클래스 사용
- 모든 입력은 Zod 스키마로 검증
- async/await 사용, 콜백 금지

## 명령어
- 개발: npm run dev
- 빌드: npm run build
- 테스트: npm test
- 마이그레이션: npx prisma migrate dev
```

### API 엔드포인트 생성 예제

**프롬프트:**
```
사용자 CRUD API를 만들어줘.
- POST /api/users - 회원가입
- GET /api/users/:id - 프로필 조회
- PUT /api/users/:id - 프로필 수정
- DELETE /api/users/:id - 회원 탈퇴
- Zod 검증, JWT 인증 미들웨어 적용
```

**생성되는 코드 예시:**

```ts
// src/routes/user.routes.ts
import { Router } from 'express';
import { UserController } from '../controllers/user.controller';
import { authenticate } from '../middleware/auth';
import { validate } from '../middleware/validate';
import { createUserSchema, updateUserSchema } from '../schemas/user.schema';

const router = Router();
const controller = new UserController();

router.post('/', validate(createUserSchema), controller.create);
router.get('/:id', authenticate, controller.getById);
router.put('/:id', authenticate, validate(updateUserSchema), controller.update);
router.delete('/:id', authenticate, controller.delete);

export default router;
```

```ts
// src/services/user.service.ts
import { prisma } from '../config/database';
import { AppError } from '../utils/errors';
import bcrypt from 'bcrypt';

export class UserService {
  async create(data: { email: string; password: string; name: string }) {
    const existing = await prisma.user.findUnique({ where: { email: data.email } });
    if (existing) throw new AppError('이미 등록된 이메일입니다', 409);

    const hashedPassword = await bcrypt.hash(data.password, 12);
    return prisma.user.create({
      data: { ...data, password: hashedPassword },
      select: { id: true, email: true, name: true, createdAt: true },
    });
  }

  async getById(id: string) {
    const user = await prisma.user.findUnique({
      where: { id },
      select: { id: true, email: true, name: true, createdAt: true },
    });
    if (!user) throw new AppError('사용자를 찾을 수 없습니다', 404);
    return user;
  }
}
```

### 테스트 작성

**프롬프트:**
```
UserService의 통합 테스트를 작성해줘. Supertest로 API 엔드포인트를 테스트해.
```

```ts
import request from 'supertest';
import { app } from '../src/app';
import { prisma } from '../src/config/database';

describe('POST /api/users', () => {
  afterEach(async () => {
    await prisma.user.deleteMany();
  });

  it('유효한 데이터로 사용자를 생성한다', async () => {
    const res = await request(app)
      .post('/api/users')
      .send({ email: 'test@example.com', password: 'Password123!', name: '테스트' });

    expect(res.status).toBe(201);
    expect(res.body.email).toBe('test@example.com');
    expect(res.body).not.toHaveProperty('password');
  });

  it('중복 이메일은 409를 반환한다', async () => {
    await request(app)
      .post('/api/users')
      .send({ email: 'test@example.com', password: 'Password123!', name: '테스트' });

    const res = await request(app)
      .post('/api/users')
      .send({ email: 'test@example.com', password: 'Password456!', name: '테스트2' });

    expect(res.status).toBe(409);
  });
});
```

---

## 2. Python / FastAPI

### CLAUDE.md 설정 예시

```markdown
# FastAPI 프로젝트

## 기술 스택
- Python 3.12 + FastAPI
- SQLAlchemy 2.0 + Alembic (마이그레이션)
- PostgreSQL
- Pydantic v2 (검증/직렬화)
- pytest + httpx (테스트)
- Poetry (패키지 관리)

## 프로젝트 구조
app/
├── api/routes/      # 라우트 엔드포인트
├── core/            # 설정, 보안, 의존성
├── models/          # SQLAlchemy 모델
├── schemas/         # Pydantic 스키마
├── services/        # 비즈니스 로직
└── db/              # DB 연결, 마이그레이션

## 코딩 규칙
- 타입 힌트 필수
- async def 엔드포인트 기본 사용
- Depends()로 의존성 주입
- Pydantic 모델로 요청/응답 스키마 정의

## 명령어
- 개발: uvicorn app.main:app --reload
- 테스트: pytest -v
- 마이그레이션 생성: alembic revision --autogenerate -m "설명"
- 마이그레이션 적용: alembic upgrade head
```

### 엔드포인트 생성 예제

**프롬프트:**
```
상품(Product) CRUD API를 만들어줘.
- FastAPI Router 사용
- Pydantic 스키마로 요청/응답 검증
- SQLAlchemy 모델
- 페이지네이션 지원
```

**생성되는 코드:**

```python
# app/schemas/product.py
from pydantic import BaseModel, Field
from datetime import datetime

class ProductCreate(BaseModel):
    name: str = Field(..., min_length=1, max_length=200)
    price: int = Field(..., ge=0)
    description: str | None = None
    category: str

class ProductResponse(BaseModel):
    id: int
    name: str
    price: int
    description: str | None
    category: str
    created_at: datetime

    model_config = {"from_attributes": True}

class ProductListResponse(BaseModel):
    items: list[ProductResponse]
    total: int
    page: int
    size: int
```

```python
# app/api/routes/products.py
from fastapi import APIRouter, Depends, Query, HTTPException
from sqlalchemy.ext.asyncio import AsyncSession
from app.db.session import get_db
from app.schemas.product import ProductCreate, ProductResponse, ProductListResponse
from app.services.product_service import ProductService

router = APIRouter(prefix="/products", tags=["products"])

@router.get("/", response_model=ProductListResponse)
async def list_products(
    page: int = Query(1, ge=1),
    size: int = Query(20, ge=1, le=100),
    category: str | None = None,
    db: AsyncSession = Depends(get_db),
):
    service = ProductService(db)
    return await service.list_products(page=page, size=size, category=category)

@router.post("/", response_model=ProductResponse, status_code=201)
async def create_product(
    data: ProductCreate,
    db: AsyncSession = Depends(get_db),
):
    service = ProductService(db)
    return await service.create_product(data)
```

### 테스트 작성

```python
import pytest
from httpx import AsyncClient, ASGITransport
from app.main import app

@pytest.mark.asyncio
async def test_create_product():
    async with AsyncClient(transport=ASGITransport(app=app), base_url="http://test") as client:
        response = await client.post("/api/products/", json={
            "name": "테스트 상품",
            "price": 10000,
            "category": "electronics",
        })
    assert response.status_code == 201
    assert response.json()["name"] == "테스트 상품"

@pytest.mark.asyncio
async def test_list_products_pagination():
    async with AsyncClient(transport=ASGITransport(app=app), base_url="http://test") as client:
        response = await client.get("/api/products/?page=1&size=10")
    assert response.status_code == 200
    assert "items" in response.json()
    assert "total" in response.json()
```

---

## 3. Go

### CLAUDE.md 설정 예시

```markdown
# Go API 프로젝트

## 기술 스택
- Go 1.22+
- 라우터: chi (또는 gin)
- DB: sqlx + PostgreSQL
- 마이그레이션: golang-migrate
- 테스트: 표준 testing 패키지 + testify

## 프로젝트 구조
├── cmd/server/      # 메인 엔트리포인트
├── internal/
│   ├── handler/     # HTTP 핸들러
│   ├── service/     # 비즈니스 로직
│   ├── repository/  # DB 접근 계층
│   ├── model/       # 도메인 모델
│   └── middleware/   # HTTP 미들웨어
├── pkg/             # 외부 공개 패키지
└── migrations/      # SQL 마이그레이션

## 코딩 규칙
- 표준 프로젝트 레이아웃 준수
- 에러는 반드시 처리 (_ 할당 금지)
- context.Context 첫 번째 파라미터로 전달
- 인터페이스는 소비자 측에서 정의

## 명령어
- 실행: go run ./cmd/server
- 테스트: go test ./...
- 빌드: go build -o bin/server ./cmd/server
- 린트: golangci-lint run
```

### 핸들러 생성 예제

**프롬프트:**
```
Go chi 라우터로 주문(Order) API를 만들어줘.
- 핸들러, 서비스, 리포지토리 계층 분리
- 컨텍스트 전파
- 에러 핸들링
```

```go
// internal/handler/order.go
package handler

import (
    "encoding/json"
    "net/http"
    "github.com/go-chi/chi/v5"
    "myapp/internal/service"
)

type OrderHandler struct {
    svc service.OrderService
}

func NewOrderHandler(svc service.OrderService) *OrderHandler {
    return &OrderHandler{svc: svc}
}

func (h *OrderHandler) Routes() chi.Router {
    r := chi.NewRouter()
    r.Post("/", h.Create)
    r.Get("/{id}", h.GetByID)
    r.Get("/", h.List)
    return r
}

func (h *OrderHandler) Create(w http.ResponseWriter, r *http.Request) {
    var req CreateOrderRequest
    if err := json.NewDecoder(r.Body).Decode(&req); err != nil {
        http.Error(w, "잘못된 요청입니다", http.StatusBadRequest)
        return
    }

    order, err := h.svc.CreateOrder(r.Context(), req.toModel())
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }

    w.Header().Set("Content-Type", "application/json")
    w.WriteHeader(http.StatusCreated)
    json.NewEncoder(w).Encode(order)
}
```

---

## 4. .NET / C#

### CLAUDE.md 설정 예시

```markdown
# .NET Web API 프로젝트

## 기술 스택
- .NET 8 + C# 12
- ASP.NET Core Web API
- Entity Framework Core + PostgreSQL
- FluentValidation
- xUnit + Moq (테스트)
- MediatR (CQRS 패턴)

## 프로젝트 구조
src/
├── Api/              # Controllers, Middleware
├── Application/      # Commands, Queries, DTOs
├── Domain/           # Entities, Value Objects
└── Infrastructure/   # DB Context, Repositories

## 코딩 규칙
- Clean Architecture 준수
- Minimal API 또는 Controller 사용
- record 타입으로 DTO 정의
- async/await 패턴 일관 사용

## 명령어
- 실행: dotnet run --project src/Api
- 테스트: dotnet test
- 마이그레이션: dotnet ef migrations add "설명"
- DB 업데이트: dotnet ef database update
```

### Controller 생성 예제

**프롬프트:**
```
.NET 8 Minimal API로 상품 CRUD를 구현해줘.
Entity Framework Core, FluentValidation 사용.
```

```csharp
// src/Api/Endpoints/ProductEndpoints.cs
public static class ProductEndpoints
{
    public static void MapProductEndpoints(this IEndpointRouteBuilder app)
    {
        var group = app.MapGroup("/api/products").WithTags("Products");

        group.MapGet("/", async (AppDbContext db, int page = 1, int size = 20) =>
        {
            var products = await db.Products
                .Skip((page - 1) * size)
                .Take(size)
                .ToListAsync();
            var total = await db.Products.CountAsync();
            return Results.Ok(new { items = products, total, page, size });
        });

        group.MapPost("/", async (CreateProductRequest req, AppDbContext db,
            IValidator<CreateProductRequest> validator) =>
        {
            var result = await validator.ValidateAsync(req);
            if (!result.IsValid) return Results.ValidationProblem(result.ToDictionary());

            var product = new Product { Name = req.Name, Price = req.Price };
            db.Products.Add(product);
            await db.SaveChangesAsync();
            return Results.Created($"/api/products/{product.Id}", product);
        });
    }
}
```

---

## 5. 공통 백엔드 프롬프트 패턴

### 데이터베이스 통합

```
이 프로젝트에 Redis 캐싱 레이어를 추가해줘.
- 자주 조회되는 엔드포인트에 캐시 적용
- TTL 설정 가능하게
- 캐시 무효화 전략 포함
```

### API 문서 자동 생성

```
Swagger/OpenAPI 문서를 생성해줘.
- 모든 엔드포인트에 설명 추가
- 요청/응답 예시 포함
- 인증 스킴 정의
```

### 보안 강화

```
이 API의 보안을 점검하고 강화해줘.
- Rate limiting 추가
- CORS 설정 검토
- SQL Injection 방어 확인
- 입력 검증 누락 찾기
```

---

## 6. 팁과 주의사항

| 상황 | 팁 |
|------|-----|
| DB 스키마 변경 | CLAUDE.md에 현재 스키마 설명 포함 |
| 인증 구현 | 보안 관련 코드는 반드시 검토 후 사용 |
| 대규모 리팩토링 | 계층별로 나누어 요청 |
| 에러 핸들링 | 프로젝트의 에러 처리 패턴을 CLAUDE.md에 명시 |
| 성능 최적화 | "이 쿼리의 실행 계획을 분석해줘" 활용 |

> **다음 단계:** [모바일 기술스택 가이드](./03-mobile.md)로 이동하세요.
