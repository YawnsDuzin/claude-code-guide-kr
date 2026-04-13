# 권한 모드와 보안(Permissions & Security)

Claude Code는 파일 수정, 명령어 실행 등 시스템에 영향을 줄 수 있는 작업을 수행합니다. 안전한 사용을 위해 **권한 시스템**을 통해 어떤 작업을 허용하고 차단할지 세밀하게 제어할 수 있습니다.

---

## 세 가지 권한 모드

Claude Code는 세 가지 권한 모드를 제공합니다. 상황에 맞는 모드를 선택하여 보안과 편의성 사이의 균형을 맞출 수 있습니다.

### 1. Default (Ask) 모드 - 기본값

**가장 안전한 사용 모드**입니다. 파일 수정이나 명령어 실행 전에 항상 사용자의 승인을 요청합니다.

```bash
# 기본 모드로 실행 (별도 설정 불필요)
claude
```

#### 승인이 필요 없는 작업 (자동 허용)

- 파일 읽기 (`Read`)
- 파일 검색 (`Glob`, `Grep`)
- 디렉터리 목록 확인

#### 승인이 필요한 작업

- 파일 수정 (`Edit`)
- 파일 생성 (`Write`)
- 터미널 명령어 실행 (`Bash`)
- 웹 접근 (`WebFetch`, `WebSearch`)

#### 실제 동작 예시

```
> src/config.ts에서 포트를 8080으로 바꿔줘

Claude: src/config.ts 파일을 수정하겠습니다.

  Edit: src/config.ts
    old: const PORT = 3000
    new: const PORT = 8080

  이 변경을 허용하시겠습니까? [Y/n/e]
```

- **Y (Yes)**: 이 변경을 승인
- **n (No)**: 이 변경을 거부
- **e (Edit)**: 제안된 변경 내용을 편집

### 2. Relaxed (Yolo) 모드

**편의성 우선 모드**입니다. 대부분의 작업을 자동으로 승인하며, 빠른 작업 진행이 필요할 때 유용합니다.

```bash
# Relaxed 모드로 실행
claude --dangerously-skip-permissions
```

> **경고**: 이 모드는 파일 수정과 명령어 실행을 자동으로 승인합니다. 신뢰할 수 있는 프로젝트에서만 사용하세요.

#### 자동 허용되는 작업

- 파일 읽기, 검색
- 파일 수정 및 생성
- 대부분의 터미널 명령어

#### 여전히 차단되는 작업

Relaxed 모드에서도 일부 위험한 명령어는 차단됩니다:

- `curl` 등으로 외부에서 스크립트를 받아 실행하는 패턴
- 시스템 전체에 영향을 줄 수 있는 명령어

### 3. Strict 모드

**최고 보안 모드**입니다. 기본 모드보다 더 엄격하게 권한을 제한합니다.

#### 특징

- 읽기 전용 작업도 프로젝트 범위 내로 제한 가능
- 허용 목록(allowlist)에 있는 작업만 수행 가능
- 기업 환경이나 민감한 프로젝트에 적합

---

## 도구별 권한 설정

각 도구에 대해 개별적으로 권한을 설정할 수 있습니다.

### 권한 수준

| 수준 | 설명 |
|------|------|
| **Allow** | 항상 자동 승인 |
| **Ask** | 매번 사용자에게 확인 |
| **Deny** | 항상 차단 |

### 설정 방법

권한은 설정 파일이나 대화형 프롬프트에서 설정할 수 있습니다.

#### 대화 중 설정

도구 사용 승인 시 옵션을 선택할 수 있습니다:

```
이 도구 사용을 허용하시겠습니까?
  (y) 이번만 허용
  (a) 이 세션에서 항상 허용
  (d) 항상 허용 (설정에 저장)
  (n) 거부
```

#### 설정 파일로 관리

`~/.claude/settings.json` 또는 프로젝트의 `.claude/settings.json`에서 권한을 설정합니다.

```json
{
  "permissions": {
    "allow": [
      "Bash(npm test)",
      "Bash(npm run lint)",
      "Bash(git log*)",
      "Bash(git diff*)",
      "Bash(git status)"
    ],
    "deny": [
      "Bash(rm -rf*)",
      "Bash(sudo*)"
    ]
  }
}
```

---

## 명령어 허용 목록(Allowlist)

특정 Bash 명령어를 미리 허용 목록에 등록하면, 해당 명령어 실행 시 매번 승인할 필요가 없습니다.

### 허용 목록 설정

```json
{
  "permissions": {
    "allow": [
      "Bash(npm test)",
      "Bash(npm run *)",
      "Bash(npx prettier *)",
      "Bash(git status)",
      "Bash(git log*)",
      "Bash(git diff*)",
      "Bash(git branch*)",
      "Bash(ls *)",
      "Bash(cat *)",
      "Bash(pwd)"
    ]
  }
}
```

### 와일드카드 패턴

- `*`를 사용하여 다양한 변형을 허용할 수 있습니다.
- `Bash(npm run *)`: `npm run test`, `npm run build` 등 모두 허용
- `Bash(git log*)`: `git log`, `git log --oneline -5` 등 모두 허용

### 자주 허용하는 명령어 목록

| 카테고리 | 명령어 | 설명 |
|----------|--------|------|
| 패키지 관리 | `npm test` | 테스트 실행 |
| | `npm run *` | NPM 스크립트 실행 |
| | `npm install` | 의존성 설치 |
| Git | `git status` | 상태 확인 |
| | `git log*` | 커밋 이력 |
| | `git diff*` | 변경사항 비교 |
| | `git branch*` | 브랜치 관리 |
| 파일 확인 | `ls *` | 디렉터리 목록 |
| | `pwd` | 현재 경로 |
| 빌드 | `npm run build` | 프로젝트 빌드 |
| 린트 | `npx eslint *` | 코드 린트 |

---

## 보안 모범 사례(Best Practices)

### 1. 항상 Git이 있는 프로젝트에서 사용하기

Claude Code가 파일을 잘못 수정하더라도 Git으로 복원할 수 있습니다.

```bash
# 작업 전 커밋
git add -A && git commit -m "before claude changes"

# Claude가 잘못 수정한 경우 복원
git checkout -- <파일>
# 또는 전체 복원
git stash
```

### 2. 민감한 파일은 보호하기

`.env`, 비밀키 파일, 인증 정보 파일 등은 Claude가 접근하지 못하도록 해야 합니다.

프로젝트 루트에 `.claudeignore` 파일을 생성합니다 (`.gitignore`와 동일한 문법):

```
# .claudeignore
.env
.env.local
.env.production
*.pem
*.key
credentials.json
secrets/
```

### 3. Relaxed 모드는 신뢰할 수 있는 환경에서만 사용

```bash
# 개인 프로젝트, 학습용 - Relaxed 모드 OK
claude --dangerously-skip-permissions

# 프로덕션 코드, 팀 프로젝트 - Default 모드 권장
claude
```

### 4. 허용 목록은 최소한으로 유지

필요한 명령어만 허용하고, 가능하면 와일드카드 사용을 최소화합니다.

```json
// 좋은 예: 구체적인 명령어
{
  "allow": [
    "Bash(npm test)",
    "Bash(npm run build)",
    "Bash(git status)"
  ]
}

// 나쁜 예: 너무 넓은 허용
{
  "allow": [
    "Bash(*)"
  ]
}
```

### 5. 승인 전 항상 내용 확인하기

Claude가 도구 사용을 요청할 때, 실행될 내용을 꼼꼼히 확인하세요.

```
Claude: 다음 명령어를 실행하겠습니다:
  Bash: rm -rf node_modules && npm install

  허용하시겠습니까? [Y/n]
```

특히 `rm`, `mv`, `git push`, `git reset` 등의 명령어는 주의 깊게 확인해야 합니다.

### 6. 프로젝트별 설정 분리

전역 설정과 프로젝트별 설정을 분리하여 관리합니다.

```
~/.claude/settings.json          # 전역 설정 (모든 프로젝트)
./project/.claude/settings.json   # 프로젝트별 설정 (해당 프로젝트만)
```

프로젝트별 설정이 전역 설정보다 우선합니다.

---

## 프로젝트 유형별 권한 가이드

### 개인 학습/실험 프로젝트

보안 위험이 낮으므로 편의성을 우선합니다.

```bash
# Relaxed 모드 사용 가능
claude --dangerously-skip-permissions
```

또는 자주 사용하는 명령어를 넉넉하게 허용:

```json
{
  "permissions": {
    "allow": [
      "Bash(npm *)",
      "Bash(git *)",
      "Bash(ls *)",
      "Bash(cat *)"
    ]
  }
}
```

### 팀 프로젝트

Default 모드를 사용하되, 안전한 명령어를 허용 목록에 추가합니다.

```json
{
  "permissions": {
    "allow": [
      "Bash(npm test)",
      "Bash(npm run lint)",
      "Bash(npm run build)",
      "Bash(git status)",
      "Bash(git diff*)",
      "Bash(git log*)"
    ],
    "deny": [
      "Bash(git push --force*)",
      "Bash(git reset --hard*)",
      "Bash(rm -rf *)"
    ]
  }
}
```

### 프로덕션/민감한 프로젝트

엄격한 권한 관리가 필요합니다.

```json
{
  "permissions": {
    "allow": [
      "Bash(git status)",
      "Bash(git diff*)",
      "Bash(npm test)"
    ],
    "deny": [
      "Bash(git push*)",
      "Bash(git reset*)",
      "Bash(rm *)",
      "Bash(sudo *)",
      "Bash(curl *)",
      "Bash(wget *)"
    ]
  }
}
```

---

## 자주 묻는 질문(FAQ)

### Q: Claude가 승인 없이 파일을 수정할 수 있나요?

**A**: Default 모드에서는 불가능합니다. 모든 파일 수정은 사용자 승인이 필요합니다. Relaxed 모드에서는 자동으로 승인됩니다.

### Q: 실수로 승인한 변경을 되돌리려면?

**A**: Git을 사용하세요:
```bash
git checkout -- <파일경로>  # 특정 파일 복원
git stash                   # 모든 변경사항 임시 저장
```

### Q: 허용 목록 설정은 어디에 저장되나요?

**A**: 두 곳에 저장할 수 있습니다:
- 전역: `~/.claude/settings.json`
- 프로젝트별: `.claude/settings.json` (프로젝트 루트)

### Q: 네트워크 접근도 제어할 수 있나요?

**A**: `WebFetch`와 `WebSearch` 도구의 사용 권한을 설정하거나, `curl`, `wget` 등의 Bash 명령어를 차단 목록에 추가할 수 있습니다.

---

## 요약

| 모드 | 보안 수준 | 편의성 | 적합한 상황 |
|------|-----------|--------|-------------|
| **Default (Ask)** | 높음 | 보통 | 일반적인 개발 작업 |
| **Relaxed (Yolo)** | 낮음 | 높음 | 개인 프로젝트, 학습 |
| **Strict** | 매우 높음 | 낮음 | 민감한 프로젝트, 기업 환경 |

- 기본적으로 **Default 모드**를 사용하세요.
- 자주 사용하는 안전한 명령어는 **허용 목록**에 등록하세요.
- Git을 활용하여 항상 **되돌릴 수 있는 안전망**을 유지하세요.
- 민감한 파일은 `.claudeignore`로 **보호**하세요.
