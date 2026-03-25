# 부록 A: CLI 레퍼런스

Claude Code의 모든 명령어, 옵션, 슬래시 커맨드, 환경 변수를 체계적으로 정리한 레퍼런스 가이드입니다.

---

## 1. 기본 실행 명령어

### `claude` - 대화형(Interactive) 모드

터미널에서 Claude Code를 대화형으로 실행합니다.

```bash
# 기본 실행
claude

# 초기 프롬프트와 함께 실행
claude "프로젝트 구조를 분석해줘"

# 이전 대화 이어서 진행
claude --continue

# 가장 최근 대화를 이어서 진행
claude --resume
```

대화형 모드에서는 자유롭게 질문하고 Claude의 응답을 확인하며, 추가 지시를 내릴 수 있습니다.

### `claude -p` - 비대화형(Non-Interactive) 모드

스크립트, CI/CD 파이프라인, 자동화 작업에 적합한 비대화형 모드입니다.

```bash
# 단일 질문에 대한 답변 출력
claude -p "이 프로젝트의 주요 의존성을 나열해줘"

# 파이프와 함께 사용
cat error.log | claude -p "이 에러 로그를 분석해줘"

# JSON 출력 형식
claude -p "함수 목록을 JSON으로 정리해줘" --output-format json

# 스트리밍 JSON 출력
claude -p "코드를 리팩토링해줘" --output-format stream-json
```

**출력 형식 옵션:**

| 옵션 | 설명 |
|------|------|
| `--output-format text` | 일반 텍스트 출력 (기본값) |
| `--output-format json` | JSON 형식으로 전체 결과 출력 |
| `--output-format stream-json` | 스트리밍 JSON 형식 출력 |

---

## 2. 모델 선택

### `claude --model` / `claude -m`

사용할 모델을 지정합니다.

```bash
# 특정 모델 지정
claude --model claude-sonnet-4-20250514

# 축약 옵션 사용
claude -m claude-sonnet-4-20250514

# 비대화형 모드와 조합
claude -p -m claude-sonnet-4-20250514 "이 코드를 최적화해줘"
```

**사용 가능한 모델 예시:**

| 모델 ID | 설명 |
|---------|------|
| `claude-sonnet-4-20250514` | Claude Sonnet 4 |
| `claude-opus-4-20250514` | Claude Opus 4 |

> **참고:** 사용 가능한 모델은 구독 플랜과 API 설정에 따라 다를 수 있습니다.

---

## 3. 설정 관리

### `claude config`

Claude Code의 설정을 관리합니다.

```bash
# 설정 값 목록 보기
claude config list

# 특정 설정 값 확인
claude config get theme

# 설정 값 변경
claude config set theme dark

# 설정 값 삭제
claude config remove theme
```

**설정 범위(Scope):**

```bash
# 프로젝트 범위 설정 (현재 프로젝트에만 적용)
claude config set --project preferredNotifChannel terminal

# 전역 범위 설정 (모든 프로젝트에 적용)
claude config set --global theme dark
```

| 범위 | 플래그 | 설명 |
|------|--------|------|
| 프로젝트 | `--project` | 현재 프로젝트의 `.claude/` 디렉토리에 저장 |
| 전역 | `--global` | `~/.claude/` 디렉토리에 저장 |

**주요 설정 항목:**

| 설정 키 | 값 예시 | 설명 |
|---------|---------|------|
| `theme` | `dark`, `light`, `light-daltonized`, `dark-daltonized` | 터미널 테마 |
| `preferredNotifChannel` | `terminal`, `iterm2`, `terminal_bell` | 알림 채널 설정 |
| `verbose` | `true`, `false` | 상세 출력 모드 |
| `autoUpdaterStatus` | `on`, `off` | 자동 업데이트 여부 |

---

## 4. MCP 서버 관리

### `claude mcp`

Model Context Protocol(MCP) 서버를 관리합니다.

```bash
# MCP 서버 목록 보기
claude mcp list

# MCP 서버 추가 (stdio 방식)
claude mcp add my-server -- node /path/to/server.js

# MCP 서버 추가 (SSE 방식)
claude mcp add my-sse-server --transport sse https://example.com/mcp

# MCP 서버 상세 정보 확인
claude mcp get my-server

# MCP 서버 제거
claude mcp remove my-server
```

**범위 지정:**

```bash
# 프로젝트 범위 (기본값)
claude mcp add --scope project my-server -- node server.js

# 전역 범위
claude mcp add --scope global my-server -- node server.js
```

**환경 변수와 함께 사용:**

```bash
claude mcp add my-server \
  -e API_KEY=abc123 \
  -e DB_HOST=localhost \
  -- node /path/to/server.js
```

**전송 방식:**

| 옵션 | 설명 |
|------|------|
| `--transport stdio` | 표준 입출력 방식 (기본값) |
| `--transport sse` | Server-Sent Events 방식 |

---

## 5. 진단 도구

### `claude doctor`

Claude Code 설치 환경과 설정을 진단합니다.

```bash
# 전체 진단 실행
claude doctor
```

**진단 항목:**

- Node.js 버전 확인
- 인증 상태 확인 (API 키 또는 OAuth)
- 네트워크 연결 상태
- MCP 서버 연결 상태
- 설정 파일 유효성
- 권한 설정 확인

```
# 출력 예시
Claude Code Doctor
==================
✓ Node.js version: v20.11.0 (>=18.0.0 required)
✓ Authentication: Valid API key configured
✓ Network: Connected to Anthropic API
✓ MCP servers: 2/2 connected
✓ Configuration: Valid
```

---

## 6. 슬래시 커맨드

대화형 모드에서 사용할 수 있는 슬래시 커맨드입니다.

### `/help` - 도움말

사용 가능한 커맨드와 도움말을 표시합니다.

```
> /help
```

### `/clear` - 대화 초기화

현재 대화의 컨텍스트를 완전히 초기화합니다.

```
> /clear
```

> **참고:** `/clear`는 컨텍스트 윈도우를 완전히 비웁니다. 이전 대화 내용을 참조할 수 없게 되므로 주의하세요.

### `/compact` - 대화 압축

현재 대화를 요약하여 컨텍스트 사용량을 줄입니다.

```
# 기본 압축
> /compact

# 사용자 정의 요약 지침과 함께 압축
> /compact 코드 변경 사항과 아직 해결되지 않은 이슈에 집중해서 요약해줘
```

**사용 시점:**
- 컨텍스트 윈도우가 80% 이상 사용되었을 때
- 대화가 길어져 응답 속도가 느려질 때
- 주제를 전환하되 일부 맥락은 유지하고 싶을 때

### `/exit` - 종료

Claude Code 세션을 종료합니다.

```
> /exit
```

`Ctrl+C`를 두 번 누르거나 `Ctrl+D`로도 종료할 수 있습니다.

### `/memory` - 메모리 편집

프로젝트의 CLAUDE.md 메모리 파일을 편집합니다.

```
> /memory
```

CLAUDE.md 파일이 편집기에서 열리며, 프로젝트에 대한 지침과 규칙을 작성할 수 있습니다.

### `/config` - 설정

Claude Code 설정을 대화형으로 관리합니다.

```
> /config
```

### `/cost` - 비용 확인

현재 세션의 토큰 사용량과 예상 비용을 표시합니다.

```
> /cost
```

```
# 출력 예시
Session Cost Summary
====================
Input tokens:  45,230
Output tokens: 12,450
Total cost:    $0.34
```

### `/doctor` - 진단

대화형 모드 내에서 환경 진단을 실행합니다.

```
> /doctor
```

### `/model` - 모델 변경

세션 중 사용 모델을 변경합니다.

```
> /model claude-sonnet-4-20250514

> /model claude-opus-4-20250514
```

### `/permissions` - 권한 관리

도구 사용 권한을 관리합니다.

```
> /permissions
```

허용 또는 거부할 도구와 경로 규칙을 설정할 수 있습니다.

---

## 7. 슬래시 커맨드 요약 표

| 커맨드 | 설명 | 사용 예시 |
|--------|------|-----------|
| `/help` | 도움말 표시 | `/help` |
| `/clear` | 대화 초기화 | `/clear` |
| `/compact` | 대화 압축 | `/compact` 또는 `/compact [지침]` |
| `/exit` | 세션 종료 | `/exit` |
| `/memory` | CLAUDE.md 편집 | `/memory` |
| `/config` | 설정 관리 | `/config` |
| `/cost` | 비용 확인 | `/cost` |
| `/doctor` | 환경 진단 | `/doctor` |
| `/model` | 모델 변경 | `/model [모델ID]` |
| `/permissions` | 권한 관리 | `/permissions` |

---

## 8. 환경 변수

Claude Code 동작을 제어하는 환경 변수입니다.

### 인증 관련

| 환경 변수 | 설명 | 예시 |
|-----------|------|------|
| `ANTHROPIC_API_KEY` | Anthropic API 키 | `sk-ant-api03-...` |
| `CLAUDE_CODE_USE_BEDROCK` | AWS Bedrock 사용 여부 | `1` |
| `CLAUDE_CODE_USE_VERTEX` | Google Vertex AI 사용 여부 | `1` |
| `ANTHROPIC_BASE_URL` | 커스텀 API 엔드포인트 URL | `https://custom.api.com` |

### AWS Bedrock 관련

| 환경 변수 | 설명 | 예시 |
|-----------|------|------|
| `AWS_REGION` | AWS 리전 | `us-east-1` |
| `AWS_ACCESS_KEY_ID` | AWS 액세스 키 ID | `AKIA...` |
| `AWS_SECRET_ACCESS_KEY` | AWS 시크릿 액세스 키 | `wJal...` |
| `AWS_SESSION_TOKEN` | AWS 세션 토큰 (임시 자격증명) | - |
| `ANTHROPIC_MODEL` | 사용할 Bedrock 모델 ID | `us.anthropic.claude-...` |

### Google Vertex AI 관련

| 환경 변수 | 설명 | 예시 |
|-----------|------|------|
| `CLOUD_ML_REGION` | GCP 리전 | `us-east5` |
| `ANTHROPIC_VERTEX_PROJECT_ID` | GCP 프로젝트 ID | `my-project-123` |

### 동작 제어

| 환경 변수 | 설명 | 예시 |
|-----------|------|------|
| `CLAUDE_CODE_MAX_OUTPUT_TOKENS` | 최대 출력 토큰 수 | `16384` |
| `CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC` | 비필수 트래픽 비활성화 | `1` |
| `CLAUDE_CODE_SKIP_OOBE` | 초기 설정 화면 건너뛰기 | `1` |

### 프록시 관련

| 환경 변수 | 설명 | 예시 |
|-----------|------|------|
| `HTTP_PROXY` | HTTP 프록시 URL | `http://proxy:8080` |
| `HTTPS_PROXY` | HTTPS 프록시 URL | `https://proxy:8443` |
| `NO_PROXY` | 프록시 우회 대상 | `localhost,127.0.0.1` |

**환경 변수 설정 예시:**

```bash
# 일회성 설정
ANTHROPIC_API_KEY=sk-ant-api03-xxx claude

# 셸 프로파일에 영구 설정 (~/.bashrc 또는 ~/.zshrc)
export ANTHROPIC_API_KEY="sk-ant-api03-xxx"

# AWS Bedrock 사용
export CLAUDE_CODE_USE_BEDROCK=1
export AWS_REGION=us-east-1
claude
```

---

## 9. 종료 코드 (Exit Codes)

비대화형 모드(`claude -p`)에서 반환되는 종료 코드입니다.

| 종료 코드 | 의미 | 설명 |
|-----------|------|------|
| `0` | 성공 | 정상적으로 완료됨 |
| `1` | 일반 오류 | 실행 중 오류 발생 |
| `2` | 인증 오류 | API 키 또는 인증 문제 |

**스크립트에서의 활용:**

```bash
#!/bin/bash
claude -p "테스트를 실행하고 결과를 알려줘"
EXIT_CODE=$?

if [ $EXIT_CODE -eq 0 ]; then
    echo "성공적으로 완료되었습니다."
elif [ $EXIT_CODE -eq 2 ]; then
    echo "인증 오류: API 키를 확인하세요."
else
    echo "오류가 발생했습니다. (코드: $EXIT_CODE)"
fi
```

---

## 10. 플래그 및 옵션 레퍼런스

### 전체 옵션 표

| 플래그 | 축약 | 설명 | 예시 |
|--------|------|------|------|
| `--print` | `-p` | 비대화형 모드 실행 | `claude -p "질문"` |
| `--model` | `-m` | 모델 지정 | `claude -m claude-sonnet-4-20250514` |
| `--output-format` | | 출력 형식 지정 | `claude -p --output-format json` |
| `--continue` | `-c` | 이전 대화 이어서 진행 | `claude --continue` |
| `--resume` | `-r` | 대화 선택하여 이어가기 | `claude --resume` |
| `--allowedTools` | | 허용 도구 목록 지정 | `claude --allowedTools "Bash,Read"` |
| `--disallowedTools` | | 차단 도구 목록 지정 | `claude --disallowedTools "Write"` |
| `--max-turns` | | 최대 턴 수 제한 | `claude -p --max-turns 10` |
| `--system-prompt` | | 시스템 프롬프트 지정 | `claude -p --system-prompt "..."` |
| `--permission-mode` | | 권한 모드 설정 | `claude --permission-mode plan` |
| `--input-format` | | 입력 형식 지정 | `claude -p --input-format stream-json` |
| `--verbose` | | 상세 출력 활성화 | `claude --verbose` |
| `--version` | `-v` | 버전 정보 출력 | `claude --version` |
| `--help` | `-h` | 도움말 출력 | `claude --help` |
| `--add-dir` | | 추가 작업 디렉토리 지정 | `claude --add-dir /path/to/other` |

### 권한 모드 옵션

| 모드 | 설명 |
|------|------|
| `default` | 위험한 작업 시 사용자 확인 요청 (기본값) |
| `plan` | 파일 읽기와 계획은 허용, 수정은 확인 필요 |
| `bypassPermissions` | 모든 도구를 확인 없이 실행 (주의 필요) |

```bash
# Plan 모드로 실행
claude --permission-mode plan

# 비대화형 모드에서 최대 턴 수 제한
claude -p --max-turns 5 "테스트를 실행해줘"

# 특정 도구만 허용
claude --allowedTools "Read,Grep,Glob"
```

### 파이프라인 조합 예시

```bash
# Git diff를 분석하여 코드 리뷰
git diff HEAD~1 | claude -p "이 변경 사항을 코드 리뷰해줘"

# 로그 파일 분석
cat server.log | claude -p "에러 패턴을 분석하고 원인을 추정해줘"

# 여러 파일을 컨텍스트로 전달
cat src/main.ts src/config.ts | claude -p "이 코드의 아키텍처를 설명해줘"

# 결과를 파일로 저장
claude -p "README 초안을 작성해줘" > README_draft.md

# JSON 출력을 jq로 파싱
claude -p --output-format json "프로젝트 구조 분석" | jq '.result'
```

---

## 11. 빠른 참조 카드

```
# 기본 실행
claude                              # 대화형 모드
claude "질문"                       # 초기 프롬프트와 함께 시작
claude -p "질문"                    # 비대화형 모드

# 대화 관리
claude --continue                   # 마지막 대화 이어가기
claude --resume                     # 대화 선택하여 이어가기

# 설정
claude config list                  # 설정 목록
claude config set key value         # 설정 변경
claude mcp list                     # MCP 서버 목록
claude mcp add name -- cmd args     # MCP 서버 추가

# 진단
claude doctor                       # 환경 진단
claude --version                    # 버전 확인

# 슬래시 커맨드 (대화형 모드 내)
/help    /clear    /compact    /exit
/memory  /config   /cost       /doctor
/model   /permissions
```

---

> **팁:** `claude --help`를 실행하면 항상 최신 옵션 목록을 확인할 수 있습니다. 새로운 버전에서 추가된 옵션이 있을 수 있으므로 정기적으로 확인하세요.
