# 스킬과 슬래시 명령어 (Skills & Slash Commands)

## 개요

Claude Code의 **스킬(Skills)**은 슬래시 명령어(`/command`) 형태로 실행되는 특수 기능입니다. 자주 반복하는 작업을 하나의 명령어로 간편하게 실행할 수 있으며, 내장 스킬 외에도 프로젝트에 맞는 커스텀 스킬을 직접 만들 수 있습니다.

쉽게 말해, **자주 쓰는 프롬프트를 저장해두고 한 번에 실행하는 단축키**라고 생각하면 됩니다.

---

## 슬래시 명령어란?

Claude Code 대화창에서 `/`로 시작하는 명령어를 입력하면, 미리 정의된 작업이 자동으로 실행됩니다.

```
사용자: /help
# Claude Code의 도움말이 표시됩니다

사용자: /commit
# 변경 사항을 분석하고 커밋 메시지를 생성합니다
```

슬래시를 입력하면 사용 가능한 명령어 목록이 자동완성으로 표시되므로, 정확한 이름을 외울 필요가 없습니다.

---

## 내장 스킬 (Built-in Skills)

Claude Code에는 기본적으로 제공되는 내장 스킬이 있습니다.

### /help - 도움말

```
사용자: /help
```

Claude Code의 사용법, 사용 가능한 명령어, 키보드 단축키 등을 보여줍니다. 처음 사용할 때 한 번 실행해보는 것을 권장합니다.

### /clear - 대화 초기화

```
사용자: /clear
```

현재 대화 내용을 모두 지우고 새로운 세션을 시작합니다. 컨텍스트가 너무 길어졌거나 완전히 다른 작업을 시작할 때 유용합니다.

**주의**: 대화 내용만 초기화되며, 파일에 대한 변경 사항은 유지됩니다.

### /compact - 대화 압축

```
사용자: /compact
```

현재까지의 대화 내용을 요약하여 컨텍스트 크기를 줄입니다. 긴 작업 도중 토큰이 부족할 때 유용합니다.

```
사용자: /compact 프론트엔드 관련 내용만 유지해줘
```

선택적으로 어떤 내용을 유지할지 지시할 수도 있습니다.

### /exit - 종료

```
사용자: /exit
```

Claude Code 세션을 종료합니다. `Ctrl+C`를 두 번 누르는 것과 동일합니다.

### /memory - 메모리 편집

```
사용자: /memory
```

프로젝트의 `CLAUDE.md` 파일을 편집할 수 있게 해줍니다. 대화 중 발견한 중요한 규칙이나 정보를 영구적으로 저장할 때 사용합니다.

```
사용자: 이 프로젝트에서는 항상 vitest를 써야 해. 이걸 기억해줘.
Claude: 네, CLAUDE.md에 기록하겠습니다.
사용자: /memory
```

### /config - 설정 관리

```
사용자: /config
```

Claude Code의 설정을 확인하고 변경할 수 있습니다. API 키, 모델 설정, 권한 등을 관리합니다.

### /cost - 비용 확인

```
사용자: /cost
```

현재 세션에서 사용한 토큰 수와 예상 비용을 보여줍니다. 비용 관리에 유용합니다.

### /doctor - 진단

```
사용자: /doctor
```

Claude Code의 설치 상태와 설정을 진단합니다. 문제가 발생했을 때 가장 먼저 실행해보면 좋습니다.

---

## 내장 스킬 요약표

| 명령어 | 기능 | 사용 시점 |
|--------|------|----------|
| `/help` | 도움말 표시 | 사용법이 궁금할 때 |
| `/clear` | 대화 초기화 | 새 작업을 시작할 때 |
| `/compact` | 대화 압축 | 컨텍스트가 길어졌을 때 |
| `/exit` | 세션 종료 | 작업을 마칠 때 |
| `/memory` | 메모리 편집 | 프로젝트 규칙을 저장할 때 |
| `/config` | 설정 관리 | 설정을 변경할 때 |
| `/cost` | 비용 확인 | 사용량을 확인할 때 |
| `/doctor` | 상태 진단 | 문제를 해결할 때 |

---

## 커스텀 스킬 만들기

내장 스킬 외에도 프로젝트에 맞는 **커스텀 스킬**을 직접 만들 수 있습니다. 이것이 스킬 시스템의 진정한 강점입니다.

### 기본 구조

커스텀 스킬은 `.claude/commands/` 디렉토리에 마크다운(`.md`) 파일로 생성합니다.

```
my-project/
├── .claude/
│   └── commands/
│       ├── deploy.md         # /project:deploy 로 실행
│       ├── review.md         # /project:review 로 실행
│       └── test-feature.md   # /project:test-feature 로 실행
├── src/
└── package.json
```

파일 이름이 곧 명령어 이름이 됩니다. `deploy.md`를 만들면 `/project:deploy`로 실행할 수 있습니다.

### 첫 번째 커스텀 스킬 만들기

#### 예시 1: 코드 리뷰 스킬

`.claude/commands/review.md` 파일을 생성합니다:

```markdown
현재 브랜치의 변경 사항을 리뷰해주세요.

1. `git diff main...HEAD`를 실행하여 변경 사항을 확인
2. 다음 관점에서 리뷰:
   - 코드 품질 및 가독성
   - 잠재적 버그
   - 성능 이슈
   - 보안 취약점
3. 각 파일별로 구체적인 피드백 제공
4. 전체 요약과 개선 제안 포함
```

실행 방법:
```
사용자: /project:review
```

#### 예시 2: 인자를 받는 스킬

`$ARGUMENTS` 플레이스홀더를 사용하면 명령어에 인자를 전달할 수 있습니다.

`.claude/commands/explain.md`:

```markdown
다음 파일 또는 코드를 초보자도 이해할 수 있도록 설명해주세요: $ARGUMENTS

설명할 때 다음을 포함해주세요:
1. 이 코드가 하는 일 (한 줄 요약)
2. 핵심 로직 단계별 설명
3. 사용된 주요 패턴이나 개념
4. 관련 파일이나 의존성
```

실행 방법:
```
사용자: /project:explain src/auth/middleware.ts
# $ARGUMENTS가 "src/auth/middleware.ts"로 치환됩니다
```

#### 예시 3: 커밋 스킬

`.claude/commands/commit.md`:

```markdown
현재 변경 사항을 분석하고 커밋을 생성해주세요.

1. `git status`와 `git diff`로 변경 사항을 파악
2. Conventional Commits 형식으로 커밋 메시지 작성:
   - feat: 새 기능
   - fix: 버그 수정
   - refactor: 리팩토링
   - docs: 문서 변경
   - test: 테스트 추가/수정
   - chore: 빌드/설정 변경
3. 커밋 메시지는 한국어로 작성
4. 본문에 주요 변경 사항을 bullet point로 나열
5. 커밋 실행
```

실행 방법:
```
사용자: /project:commit
```

#### 예시 4: 배포 스킬

`.claude/commands/deploy.md`:

```markdown
$ARGUMENTS 환경으로 배포를 준비해주세요.

1. 현재 브랜치가 최신 상태인지 확인 (`git pull`)
2. 테스트 실행 (`npm test`)
3. 빌드 실행 (`npm run build`)
4. 빌드 결과물 확인
5. 배포 환경별 안내:
   - staging: `npm run deploy:staging`
   - production: `npm run deploy:production` (main 브랜치에서만 허용)
6. 배포 전 체크리스트:
   - [ ] 모든 테스트 통과
   - [ ] 빌드 성공
   - [ ] 환경변수 확인
   - [ ] 마이그레이션 필요 여부 확인
```

실행 방법:
```
사용자: /project:deploy staging
사용자: /project:deploy production
```

---

## 프로젝트 레벨 vs 사용자 레벨 스킬

커스텀 스킬은 두 가지 레벨로 만들 수 있습니다.

### 프로젝트 레벨 스킬

팀 전체가 공유하는 프로젝트 전용 스킬입니다.

```
my-project/
└── .claude/
    └── commands/
        ├── review.md      # /project:review
        ├── deploy.md      # /project:deploy
        └── db-migrate.md  # /project:db-migrate
```

- **위치**: 프로젝트의 `.claude/commands/` 디렉토리
- **접두사**: `/project:명령어이름`으로 실행
- **공유**: Git에 커밋하여 팀원과 공유 가능
- **용도**: 프로젝트 고유의 워크플로우

### 사용자 레벨 스킬

개인적으로 모든 프로젝트에서 사용하는 스킬입니다.

```
~/.claude/
└── commands/
    ├── my-review.md     # /user:my-review
    ├── daily-log.md     # /user:daily-log
    └── quick-test.md    # /user:quick-test
```

- **위치**: 홈 디렉토리의 `~/.claude/commands/`
- **접두사**: `/user:명령어이름`으로 실행
- **공유**: 개인 설정이므로 공유되지 않음
- **용도**: 개인적인 작업 자동화

### 구분 예시

```
사용자: /project:deploy staging
# 프로젝트에 정의된 배포 스킬 실행 (팀 공유)

사용자: /user:daily-log
# 개인적으로 정의한 일일 로그 스킬 실행 (개인 전용)
```

---

## 팀과 스킬 공유하기

프로젝트 레벨 스킬은 Git으로 쉽게 공유할 수 있습니다.

### 1단계: 스킬 디렉토리 생성

```bash
mkdir -p .claude/commands
```

### 2단계: 스킬 파일 작성

```bash
# 각 스킬을 마크다운 파일로 작성
```

### 3단계: Git에 커밋

```bash
git add .claude/commands/
git commit -m "feat: Claude Code 커스텀 스킬 추가"
git push
```

### 4단계: 팀원이 Pull

팀원이 `git pull`을 하면 자동으로 스킬을 사용할 수 있습니다. 별도의 설치나 설정이 필요 없습니다.

### 팀 스킬 관리 팁

```
.claude/
└── commands/
    ├── README.md           # 스킬 목록과 설명 (선택사항)
    ├── review.md           # 코드 리뷰
    ├── commit.md           # 커밋 생성
    ├── deploy.md           # 배포
    ├── create-feature.md   # 기능 생성 템플릿
    └── hotfix.md           # 핫픽스 워크플로우
```

---

## 고급 스킬 작성 팁

### 1. 구체적인 지시 포함

```markdown
<!-- 나쁜 예: 너무 모호함 -->
테스트를 작성해주세요.

<!-- 좋은 예: 구체적인 지시 -->
$ARGUMENTS에 대한 단위 테스트를 작성해주세요.

규칙:
- 테스트 프레임워크: vitest
- 파일 위치: 원본 파일 옆에 `*.test.ts`로 생성
- 각 공개 함수에 대해 최소 3개의 테스트 케이스:
  1. 정상 동작 (happy path)
  2. 엣지 케이스
  3. 에러 케이스
- describe/it 구문 사용
- 한국어 설명문 작성
```

### 2. 단계별 워크플로우 정의

```markdown
PR을 생성해주세요.

순서:
1. `git status`로 현재 상태 확인
2. 변경 사항이 있으면 커밋
3. 원격에 브랜치 push
4. `gh pr create`로 PR 생성
   - 제목: Conventional Commits 형식
   - 본문: 변경 사항 요약, 테스트 계획 포함
5. PR URL 출력
```

### 3. 조건부 로직 포함

```markdown
$ARGUMENTS 컴포넌트를 생성해주세요.

파일 구조:
- `src/components/$ARGUMENTS/$ARGUMENTS.tsx` - 메인 컴포넌트
- `src/components/$ARGUMENTS/$ARGUMENTS.test.tsx` - 테스트
- `src/components/$ARGUMENTS/$ARGUMENTS.stories.tsx` - Storybook (있는 경우)
- `src/components/$ARGUMENTS/index.ts` - barrel export

참고:
- Storybook이 프로젝트에 설치되어 있는 경우에만 stories 파일 생성
- 기존 컴포넌트 패턴을 참고하여 일관된 스타일 유지
```

---

## 실전 활용 시나리오

### 시나리오 1: 새 기능 개발 워크플로우

```
사용자: /project:create-feature user-profile
# 브랜치 생성, 기본 파일 구조 셋업, 테스트 파일 생성

# ... 개발 진행 ...

사용자: /project:review
# 코드 리뷰 수행

사용자: /project:commit
# 변경 사항 커밋

사용자: /project:deploy staging
# 스테이징 배포
```

### 시나리오 2: 버그 수정 워크플로우

```
사용자: /project:hotfix 로그인 실패 버그
# hotfix 브랜치 생성, 관련 코드 탐색

# ... 수정 진행 ...

사용자: /project:commit
사용자: /project:deploy production
```

---

## 요약

| 항목 | 설명 |
|------|------|
| **스킬이란** | 슬래시 명령어로 실행하는 자동화된 작업 |
| **내장 스킬** | `/help`, `/clear`, `/compact`, `/memory` 등 |
| **커스텀 스킬** | `.claude/commands/`에 마크다운 파일로 생성 |
| **인자 전달** | `$ARGUMENTS` 플레이스홀더 사용 |
| **프로젝트 스킬** | `/project:이름` - 팀과 공유 가능 |
| **사용자 스킬** | `/user:이름` - 개인 전용 |
| **공유 방법** | Git으로 `.claude/commands/` 커밋 |

커스텀 스킬을 활용하면 반복적인 작업을 자동화하고, 팀 전체의 워크플로우를 표준화할 수 있습니다. 프로젝트에서 자주 수행하는 작업을 스킬로 만들어보세요.
