# Git 커밋/PR 워크플로우

Claude Code를 활용하여 Git 관련 작업을 효율적으로 수행하는 방법을 소개합니다. 커밋 메시지 작성부터 PR 생성, 코드 리뷰, 충돌 해결까지 다룹니다.

---

## 1. 커밋 메시지 작성 (/commit)

Claude Code의 `/commit` 명령어를 사용하여 의미 있는 커밋 메시지를 자동 생성합니다.

### 기본 사용법

```bash
# Claude Code에서 /commit 명령어 사용
/commit
```

Claude가 스테이징된 변경사항을 분석하여 적절한 커밋 메시지를 생성합니다.

### 상세 요청

```
현재 변경사항을 분석하고 Conventional Commits 형식으로 커밋 메시지를 작성해줘.
변경 사항:
- 사용자 프로필 API 추가
- 프로필 이미지 업로드 기능
- 관련 테스트 코드

본문에 주요 변경 내용을 요약해줘.
```

### Claude의 예상 동작

1. `git diff --staged`를 확인하여 변경 내용 분석
2. 변경 범위와 성격을 파악 (기능 추가, 버그 수정, 리팩토링 등)
3. Conventional Commits 형식의 메시지 생성
4. 사용자 확인 후 커밋 실행

### 예상 결과물

```
feat(profile): add user profile API with image upload

- Add GET/PUT /api/v1/profile endpoints
- Implement image upload with S3 integration
- Add file size and type validation
- Add unit tests for profile service
- Add integration tests for profile API
```

---

## 2. 브랜치 생성과 관리

작업 목적에 맞는 브랜치를 생성하고 관리합니다.

### 프롬프트 예시

```
다음 기능을 개발하기 위한 브랜치를 만들어줘:
- 기능: 사용자 알림 설정 페이지
- 이 프로젝트의 브랜치 네이밍 컨벤션을 따라줘
```

### Claude의 예상 동작

- 기존 브랜치 이름 패턴을 분석 (`feature/`, `feat/`, `fix/` 등)
- 컨벤션에 맞는 브랜치 이름을 생성
- 최신 main/develop 브랜치에서 새 브랜치를 생성

```bash
# Claude가 실행하는 명령어 예시
git checkout main
git pull origin main
git checkout -b feature/notification-settings
```

### 브랜치 관리 프롬프트

```
# 오래된 브랜치 정리
"머지된 브랜치 중 삭제해도 되는 것을 찾아줘"

# 브랜치 상태 확인
"현재 브랜치가 main과 얼마나 차이나는지 확인해줘"

# 브랜치 전략 확인
"이 프로젝트의 브랜치 전략을 분석해줘 (Git Flow, GitHub Flow 등)"
```

---

## 3. Pull Request 생성

체계적인 PR을 작성합니다.

### 프롬프트 예시

```
현재 브랜치의 변경사항으로 PR을 만들어줘.

다음을 포함해줘:
- 변경 내용 요약
- 주요 변경 파일 설명
- 테스트 방법
- 관련 이슈 번호: #42
- 스크린샷이 필요한 UI 변경이 있으면 표시
```

### Claude의 예상 동작

1. `git log main..HEAD`로 커밋 히스토리 확인
2. `git diff main...HEAD`로 전체 변경사항 분석
3. 프로젝트의 PR 템플릿이 있으면 해당 형식 사용
4. `gh pr create`로 PR 생성

### 예상 결과물

```markdown
## Summary
사용자 알림 설정 기능을 추가합니다.

## Changes
- **알림 설정 API**: GET/PUT /api/v1/notification-settings
- **설정 페이지 UI**: NotificationSettings 컴포넌트 추가
- **이메일 알림**: 알림 설정에 따른 이메일 발송 로직
- **테스트**: 서비스 단위 테스트 및 API 통합 테스트

## Test Plan
- [ ] 알림 설정 조회 API 동작 확인
- [ ] 알림 설정 변경 후 저장 확인
- [ ] 설정에 따른 이메일 발송 확인
- [ ] 기존 알림 기능에 영향 없음 확인

Closes #42
```

### PR 생성 팁

```
# 프로젝트 PR 템플릿 활용
"이 프로젝트의 PR 템플릿을 확인하고 그 형식에 맞춰서 작성해줘"

# 변경 범위가 큰 경우
"이 PR의 변경사항을 리뷰어가 이해하기 쉽도록 커밋별로 설명해줘"

# Draft PR
"아직 작업 중이니까 Draft PR로 만들어줘"
```

---

## 4. 코드 리뷰

Claude를 활용하여 PR을 리뷰합니다.

### 다른 사람의 PR 리뷰

```
PR #123을 리뷰해줘. 다음 관점에서 검토해줘:

1. 코드 품질과 가독성
2. 잠재적 버그
3. 성능 영향
4. 보안 문제
5. 테스트 충분성
6. 프로젝트 컨벤션 준수
```

### 내 PR 자체 리뷰

```
내가 작성한 PR을 머지하기 전에 자체 리뷰해줘.
놓친 부분이 있는지 확인해줘:
1. console.log나 디버깅 코드가 남아있는지
2. TODO 주석이 있는지
3. 하드코딩된 값이 있는지
4. 에러 처리가 누락된 곳이 있는지
5. 타입 안전성이 확보되었는지
```

### Claude의 예상 동작

- PR의 diff를 분석
- 파일별로 주요 변경사항을 검토
- 문제가 되는 부분을 구체적으로 지적
- 개선 제안을 코드 예시와 함께 제공
- 전반적인 평가와 승인/수정 요청 의견을 제시

### 리뷰 관련 팁

```
# 특정 파일에 집중
"이 PR에서 src/services/ 디렉토리의 변경사항만 자세히 리뷰해줘"

# 리뷰 코멘트 작성
"리뷰 결과를 GitHub PR 코멘트로 작성해줘"

# 리뷰 피드백 반영
"리뷰에서 받은 피드백을 반영해줘: [피드백 내용]"
```

---

## 5. 머지 충돌 해결

Git 머지 충돌을 체계적으로 해결합니다.

### 프롬프트 예시

```
main 브랜치를 머지하려는데 충돌이 발생했어. 해결해줘.

충돌 해결 시 다음 원칙을 따라줘:
1. 두 변경의 의도를 모두 이해하고 반영
2. 단순히 한쪽을 선택하지 말고 적절히 병합
3. 충돌 해결 후 코드가 정상 동작하는지 확인
4. 테스트가 통과하는지 확인
```

### Claude의 예상 동작

1. `git status`로 충돌 파일 확인
2. 각 충돌 파일을 열어 양쪽 변경사항을 이해
3. 논리적으로 올바른 병합 수행
4. 충돌 마커(`<<<<<<<`, `=======`, `>>>>>>>`) 제거 확인
5. 빌드와 테스트 실행으로 검증

### 실전 예시

```
# 복잡한 충돌 해결
"이 충돌에서 main 브랜치는 함수 시그니처를 변경했고,
내 브랜치는 함수 본문을 변경했어.
두 변경을 모두 반영해줘"

# 자동 해결 후 검토
"git merge main을 실행하고,
자동으로 해결된 부분도 검토해서 논리적으로 맞는지 확인해줘"

# 리베이스 중 충돌
"rebase 중에 충돌이 발생했어. 각 커밋별로 충돌을 해결해줘"
```

---

## 6. Git 히스토리 분석

커밋 히스토리를 분석하여 유용한 인사이트를 얻습니다.

### 프롬프트 예시

```
이 파일의 변경 히스토리를 분석해줘: src/services/orderService.ts

다음을 알려줘:
1. 최근 한 달간 변경 빈도와 내용
2. 주요 변경을 한 작성자
3. 자주 함께 변경되는 파일들
4. 변경이 집중되는 함수/영역
```

### 특정 버그의 도입 시점 추적

```
이 버그가 언제 도입되었는지 찾아줘.

현상: calculateDiscount 함수가 음수 값을 반환하는 경우가 있음
파일: src/utils/pricing.ts

git bisect나 git log를 활용해서 문제가 시작된 커밋을 찾아줘.
```

### Claude의 예상 동작

```bash
# Claude가 실행할 수 있는 명령어들
git log --oneline --since="1 month ago" -- src/services/orderService.ts
git log --follow -p src/services/orderService.ts
git blame src/utils/pricing.ts
git log --all --oneline -- src/utils/pricing.ts
```

### 히스토리 분석 팁

```
# 릴리스 간 변경사항 요약
"v1.2.0과 v1.3.0 사이의 모든 변경사항을 요약해줘"

# 특정 기간 변경 분석
"지난 2주간 src/api/ 디렉토리의 변경사항을 분석해줘"

# 기여자 분석
"이 프로젝트의 최근 기여 현황을 분석해줘"
```

---

## 7. Conventional Commits

일관된 커밋 메시지 형식을 사용합니다.

### 형식 설명

```
<type>(<scope>): <description>

[optional body]

[optional footer]
```

### 주요 타입

| 타입 | 설명 | 예시 |
|-----|------|------|
| `feat` | 새 기능 | `feat(auth): add social login` |
| `fix` | 버그 수정 | `fix(cart): resolve quantity calculation error` |
| `docs` | 문서 변경 | `docs(api): update endpoint documentation` |
| `style` | 코드 스타일 | `style: apply prettier formatting` |
| `refactor` | 리팩토링 | `refactor(user): extract validation logic` |
| `perf` | 성능 개선 | `perf(query): add index for user search` |
| `test` | 테스트 | `test(order): add integration tests` |
| `chore` | 빌드/도구 | `chore(deps): update dependencies` |
| `ci` | CI 설정 | `ci: add GitHub Actions workflow` |

### Claude에게 컨벤션 적용 요청

```
이 프로젝트에서 Conventional Commits를 사용하고 있어.
앞으로 커밋 메시지를 작성할 때 이 형식을 따라줘.

추가 규칙:
- scope는 변경된 모듈명 (auth, user, order 등)
- description은 영어, 소문자로 시작, 마침표 없음
- body에 한국어로 상세 설명 가능
- Breaking Change가 있으면 footer에 명시
```

### Breaking Change 예시

```
feat(api)!: change user response format

사용자 API 응답 형식을 변경합니다.
기존 flat 구조에서 nested 구조로 변경되었습니다.

BREAKING CHANGE: GET /api/users 응답의 profile 필드가
객체로 변경되었습니다. 기존: { name, avatar_url }
변경: { profile: { name, avatarUrl } }
```

---

## 8. PR 템플릿 활용

프로젝트의 PR 템플릿을 활용하여 일관된 PR을 작성합니다.

### PR 템플릿 생성 요청

```
이 프로젝트에 맞는 PR 템플릿을 만들어줘.

포함할 섹션:
1. 변경 요약 (Summary)
2. 변경 유형 (체크박스: 기능, 버그 수정, 리팩토링 등)
3. 변경 내용 상세 (Changes)
4. 테스트 방법 (Test Plan)
5. 스크린샷 (UI 변경 시)
6. 체크리스트 (리뷰 전 확인사항)
7. 관련 이슈

.github/pull_request_template.md에 저장해줘.
```

### 템플릿에 맞춘 PR 작성

```
현재 변경사항으로 PR을 작성해줘.
.github/pull_request_template.md 형식을 따르고,
모든 섹션을 빠짐없이 채워줘.
```

### 실전 체크리스트 예시

```markdown
## Checklist
- [ ] 셀프 리뷰를 완료했습니다
- [ ] 새로운 코드에 주석을 추가했습니다 (필요한 경우)
- [ ] 변경사항에 대한 테스트를 추가했습니다
- [ ] 기존 테스트가 모두 통과합니다
- [ ] lint 에러가 없습니다
- [ ] 타입 에러가 없습니다
- [ ] 마이그레이션이 필요한 경우 추가했습니다
- [ ] 환경 변수가 추가된 경우 .env.example을 업데이트했습니다
```

---

## Git 워크플로우 통합 예시

하나의 기능 개발 전체 과정을 Claude와 함께 진행하는 예시입니다.

### 전체 흐름

```bash
# 1. 브랜치 생성
"이슈 #42 (알림 설정 기능)를 위한 브랜치를 만들어줘"

# 2. 기능 구현 (여러 커밋)
"알림 설정 모델을 만들어줘"
"/commit"  # 첫 번째 커밋

"알림 설정 API를 만들어줘"
"/commit"  # 두 번째 커밋

"테스트를 작성해줘"
"/commit"  # 세 번째 커밋

# 3. 머지 전 최신화
"main 브랜치를 현재 브랜치에 머지해줘"

# 4. PR 생성
"PR을 만들어줘. 이슈 #42를 닫도록 설정해줘"

# 5. 리뷰 피드백 반영
"리뷰에서 에러 처리를 추가하라는 피드백을 받았어. 반영해줘"
"/commit"

# 6. 최종 확인
"모든 테스트가 통과하는지 확인해줘"
```

---

## 핵심 정리

| 작업 유형 | 핵심 명령/프롬프트 | 핵심 포인트 |
|-----------|------------------|-----------|
| 커밋 메시지 | `/commit` | Conventional Commits 형식 |
| 브랜치 관리 | "브랜치를 만들어줘" | 프로젝트 네이밍 컨벤션 준수 |
| PR 생성 | "PR을 만들어줘" | 템플릿 활용, 변경사항 요약 |
| 코드 리뷰 | "PR을 리뷰해줘" | 다양한 관점에서 검토 |
| 충돌 해결 | "충돌을 해결해줘" | 양쪽 의도를 모두 반영 |
| 히스토리 분석 | "변경 히스토리를 분석해줘" | git log, blame 활용 |
| 커밋 형식 | Conventional Commits | type(scope): description |
| PR 템플릿 | pull_request_template.md | 일관된 PR 품질 유지 |

> **팁**: `/commit` 명령어를 적극 활용하세요. Claude가 변경사항을 자동으로 분석하여 적절한 커밋 메시지를 생성해주므로, 커밋 메시지 작성에 소요되는 시간을 크게 줄일 수 있습니다.
