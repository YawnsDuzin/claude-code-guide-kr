# CLAUDE.md 실전 템플릿 모음

프로젝트 유형별로 바로 복사해서 사용할 수 있는 CLAUDE.md 템플릿입니다.

---

## 공통 기본 템플릿

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

---

## React / Next.js 프로젝트

```markdown
# 프로젝트 개요
- Next.js 14 App Router 기반 웹 애플리케이션
- TypeScript strict 모드 사용

# 코딩 규칙
- 함수형 컴포넌트만 사용 (class 컴포넌트 금지)
- 스타일: Tailwind CSS 사용 (CSS 파일 생성 금지)
- 상태 관리: Zustand 사용
- 데이터 페칭: React Query (TanStack Query) 사용
- 컴포넌트 파일명: PascalCase (예: UserProfile.tsx)
- 유틸 함수 파일명: camelCase (예: formatDate.ts)

# 프로젝트 구조
- src/app/ - Next.js App Router 페이지
- src/components/ - 재사용 가능한 컴포넌트
- src/components/ui/ - 기본 UI 컴포넌트 (Button, Input 등)
- src/hooks/ - 커스텀 훅
- src/lib/ - 유틸리티 함수
- src/types/ - TypeScript 타입 정의
- src/stores/ - Zustand 스토어

# 컴포넌트 작성 규칙
- Props는 interface로 정의 (type 대신)
- 컴포넌트 export는 named export 사용 (default export 금지)
- 'use client'는 필요한 컴포넌트에만 추가

# 명령어
- 개발 서버: npm run dev
- 빌드: npm run build
- 린트: npm run lint
- 타입 체크: npx tsc --noEmit
```

---

## Python / FastAPI 프로젝트

```markdown
# 프로젝트 개요
- FastAPI 기반 REST API 서버
- Python 3.11+, 패키지 관리: Poetry

# 코딩 규칙
- PEP 8 스타일 가이드 준수
- Type Hint 필수 (모든 함수 파라미터와 반환값)
- 변수/함수명: snake_case
- 클래스명: PascalCase
- 상수: UPPER_SNAKE_CASE
- f-string 사용 (.format() 금지)

# 프로젝트 구조
- app/main.py - FastAPI 앱 진입점
- app/api/ - API 라우터
- app/models/ - SQLAlchemy/Pydantic 모델
- app/schemas/ - Pydantic 스키마 (요청/응답)
- app/services/ - 비즈니스 로직
- app/core/ - 설정, 보안, 의존성
- tests/ - pytest 테스트 코드
- alembic/ - DB 마이그레이션

# API 작성 규칙
- 응답은 항상 Pydantic 스키마로 정의
- 에러는 HTTPException 사용
- DB 세션은 의존성 주입으로 관리

# 명령어
- 개발 서버: uvicorn app.main:app --reload
- 테스트: pytest -v
- 린트: ruff check .
- 포맷: ruff format .
- 마이그레이션 생성: alembic revision --autogenerate -m "설명"
- 마이그레이션 적용: alembic upgrade head
```

---

## Node.js / Express 프로젝트

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

---

## React Native / Expo 프로젝트

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

---

## Spring Boot / Java 프로젝트

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

---

## Flutter / Dart 프로젝트

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

---

## DevOps / Terraform 프로젝트

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

---

## 활용 팁

1. **프로젝트 루트에 저장**: `CLAUDE.md` 파일을 프로젝트 루트에 놓으면 Claude Code가 자동으로 읽습니다
2. **팀 공유**: Git에 커밋하여 팀원 모두 같은 규칙을 적용할 수 있습니다
3. **점진적 추가**: 처음부터 완벽하게 작성하지 않아도 됩니다. 작업하면서 규칙을 추가하세요
4. **간결하게 유지**: 너무 길면 컨텍스트를 낭비합니다. 핵심만 담으세요
5. **하위 폴더용**: `src/components/CLAUDE.md`처럼 하위 폴더에도 추가 가능합니다

---

## 다음 단계

-> [CLAUDE.md 작성법 상세 가이드](./02-CLAUDE-MD작성법(claude-md).md)

---

## 관련 문서

- **명령형 CLAUDE.md 골격 (복사용)**: [08-바이브코딩/03-문서템플릿/CLAUDE.template.md](../08-바이브코딩(vibe-coding)/03-문서템플릿(templates)/CLAUDE.template.md) — 11섹션, 빈 칸 채워 사용
- **함께 쓰는 문서 템플릿**: [PRD](../08-바이브코딩(vibe-coding)/03-문서템플릿(templates)/PRD.template.md) / [architecture](../08-바이브코딩(vibe-coding)/03-문서템플릿(templates)/architecture.template.md) / [erd](../08-바이브코딩(vibe-coding)/03-문서템플릿(templates)/erd.template.md) / [feature-spec](../08-바이브코딩(vibe-coding)/03-문서템플릿(templates)/feature-spec.template.md)
