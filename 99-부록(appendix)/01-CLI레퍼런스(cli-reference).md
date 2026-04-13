# 부록 A: CLI 레퍼런스

Claude Code의 모든 명령어, 옵션, 슬래시 명령어, 환경 변수, 종료 코드를 종합 정리한 레퍼런스입니다.

---

## 1. 기본 실행 명령어

### `claude` - 대화형 모드

터미널에서 대화형 REPL 세션을 시작합니다.

```bash
# 기본 실행
claude

# 초기 프롬프트와 함께 실행
claude "이 프로젝트의 구조를 설명해줘"

# 특정 디렉터리에서 실행
cd /my/project && claude
```

대화형 모드에서는 자유롭게 메시지를 입력하고, 슬래시 명령어를 사용하며, Claude와 실시간으로 상호작용할 수 있습니다.

### `claude -p` (또는 `--print`) - 비대화형 모드

단일 프롬프트를 실행하고 결과를 출력한 뒤 즉시 종료합니다. 스크립트나 CI/CD 파이프라인에 적합합니다.

```bash
# 단일 질문
claude -p "이 함수의 시간 복잡도는?"

# 파이프와 함께 사용
cat error.log | claude -p "이 에러 로그를 분석해줘"

# 파일 내용 전달
claude -p "이 코드를 리뷰해줘" < main.py

# JSON 출력
claude -p "package.json의 의존성을 나열해줘" --output-format json
```

### `claude -c` (또는 `--continue`) - 이전 대화 이어가기

가장 최근 대화를 이어서 진행합니다.

```bash
# 마지막 대화 이어가기
claude -c

# 마지막 대화에 메시지 추가
claude -c "아까 만든 함수에 에러 핸들링을 추가해줘"

# 비대화형에서도 사용 가능
claude -p -c "이전 결과를 표로 정리해줘"
```

### `claude -r` (또는 `--resume`) - 특정 대화 재개

세션 ID를 지정하여 특정 대화를 재개합니다.

```bash
# 세션 ID로 재개
claude -r abc123def456

# 메시지와 함께 재개
claude -r abc123def456 "그 작업을 계속해줘"
```

---

## 2. 모델 선택

### `--model` 플래그

사용할 모델을 지정합니다.

```bash
# 특정 모델 지정
claude --model claude-sonnet-4-20250514

# Opus 모델 사용
claude --model claude-opus-4-20250514

# 비대화형에서 모델 지정
claude -p --model claude-sonnet-4-20250514 "코드를 최적화해줘"
```

**사용 가능한 모델 예시:**

| 모델 ID | 설명 |
|---------|------|
| `claude-sonnet-4-20250514` | Sonnet 4 - 균형 잡힌 성능 |
| `claude-opus-4-20250514` | Opus 4 - 최고 성능 |
| `claude-haiku-3-5-20241022` | Haiku 3.5 - 빠른 응답 |

---

## 3. 구성 관리: `claude config`

Claude Code의 설정을 관리합니다.

```bash
# 설정 값 조회
claude config get <key>

# 설정 값 변경
claude config set <key> <value>

# 설정 목록 보기
claude config list

# 설정 삭제
claude config remove <key>
```

### 주요 설정 키

| 키 | 설명 | 예시 값 |
|----|------|---------|
| `model` | 기본 모델 | `claude-sonnet-4-20250514` |
| `permissions` | 권한 모드 | `default`, `allowAll` |
| `autoCompact` | 자동 컴팩션 | `true`, `false` |
| `preferredNotifChannel` | 알림 채널 | `terminal`, `iterm2` |

### 스코프 지정

```bash
# 프로젝트 로컬 설정
claude config set --project model claude-sonnet-4-20250514

# 전역 설정
claude config set --global model claude-opus-4-20250514
```

---

## 4. MCP 서버 관리: `claude mcp`

Model Context Protocol 서버를 추가, 제거, 관리합니다.

```bash
# MCP 서버 추가
claude mcp add <name> <command> [args...]

# 예시: GitHub MCP 서버 추가
claude mcp add github npx -y @modelcontextprotocol/server-github

# stdio 타입으로 추가 (기본값)
claude mcp add myserver -t stdio -- node /path/to/server.js

# SSE 타입으로 추가
claude mcp add remote-server -t sse https://example.com/mcp

# 환경 변수와 함께 추가
claude mcp add github -e GITHUB_TOKEN=ghp_xxxx -- npx -y @modelcontextprotocol/server-github

# MCP 서버 목록 보기
claude mcp list

# MCP 서버 상세 정보
claude mcp get <name>

# MCP 서버 제거
claude mcp remove <name>

# 스코프 지정 (프로젝트/글로벌)
claude mcp add --scope project myserver node server.js
claude mcp add --scope global myserver node server.js
```

---

## 5. 진단: `claude doctor`

설치 상태, 인증, 환경 구성을 진단합니다.

```bash
claude doctor
```

**진단 항목:**
- Node.js 버전 확인
- 인증 상태 (API 키 또는 OAuth)
- 네트워크 연결
- MCP 서버 상태
- 권한 설정
- 최신 버전 확인

---

## 6. 슬래시 명령어

대화형 모드에서 `/`로 시작하는 명령어를 사용할 수 있습니다.

| 명령어 | 설명 | 사용 예시 |
|--------|------|-----------|
| `/help` | 사용 가능한 명령어 및 도움말 표시 | `/help` |
| `/clear` | 대화 내역 초기화 | `/clear` |
| `/compact` | 대화를 요약하여 컨텍스트 절약 | `/compact` 또는 `/compact 핵심 로직 위주로` |
| `/exit` | Claude Code 종료 | `/exit` |
| `/memory` | CLAUDE.md 메모리 파일 편집 | `/memory` |
| `/config` | 설정 보기 및 변경 | `/config` |
| `/cost` | 현재 세션의 토큰 사용량 및 비용 표시 | `/cost` |
| `/doctor` | 환경 진단 실행 | `/doctor` |
| `/model` | 사용 중인 모델 확인 및 변경 | `/model claude-opus-4-20250514` |
| `/permissions` | 권한 설정 보기 및 변경 | `/permissions` |
| `/diff` | 마지막 변경 사항의 diff 표시 | `/diff` |
| `/review` | 코드 리뷰 요청 | `/review` |
| `/undo` | 마지막 파일 변경 되돌리기 | `/undo` |
| `/bug` | 버그 리포트 작성 | `/bug` |
| `/login` | 인증 로그인 | `/login` |
| `/logout` | 인증 로그아웃 | `/logout` |
| `/pr-comments` | PR 코멘트 확인 | `/pr-comments` |
| `/terminal-setup` | 터미널 키 바인딩 설정 | `/terminal-setup` |

### 슬래시 명령어 상세

#### `/compact` - 컨텍스트 압축

컨텍스트 윈도우가 부족해질 때 대화를 요약합니다.

```
/compact
/compact API 관련 내용 위주로 요약해줘
```

자동 컴팩션을 활성화하면 컨텍스트 한도에 도달할 때 자동으로 실행됩니다.

```bash
claude config set autoCompact true
```

#### `/memory` - 메모리 관리

프로젝트의 CLAUDE.md 파일을 편집하여 Claude가 기억할 내용을 관리합니다.

```
/memory
```

이 명령어는 `CLAUDE.md` 파일을 에디터에서 열어줍니다. 여기에 프로젝트 규칙, 코딩 스타일, 자주 사용하는 명령어 등을 기록할 수 있습니다.

---

## 7. 플래그 및 옵션 종합 테이블

| 플래그 | 단축 | 설명 |
|--------|------|------|
| `--print` | `-p` | 비대화형 모드 (결과 출력 후 종료) |
| `--continue` | `-c` | 마지막 대화 이어가기 |
| `--resume` | `-r` | 특정 세션 ID로 대화 재개 |
| `--model` | | 사용할 모델 지정 |
| `--output-format` | | 출력 형식 (`text`, `json`, `stream-json`) |
| `--max-turns` | | 에이전트 루프 최대 턴 수 제한 |
| `--system-prompt` | | 시스템 프롬프트 지정 (비대화형 전용) |
| `--append-system-prompt` | | 기존 시스템 프롬프트에 추가 |
| `--allowedTools` | | 허용할 도구 목록 지정 |
| `--disallowedTools` | | 차단할 도구 목록 지정 |
| `--permission-mode` | | 권한 모드 설정 |
| `--input-format` | | 입력 형식 (`text`, `stream-json`) |
| `--verbose` | | 상세 로그 출력 |
| `--no-cache` | | 프롬프트 캐싱 비활성화 |
| `--version` | `-v` | 버전 정보 표시 |
| `--help` | `-h` | 도움말 표시 |
| `--dangerously-skip-permissions` | | 모든 권한 확인 건너뛰기 (주의) |

### `--output-format` 상세

```bash
# 일반 텍스트 (기본값)
claude -p "안녕" --output-format text

# JSON 형식 (메타데이터 포함)
claude -p "안녕" --output-format json

# 스트리밍 JSON (실시간 토큰 출력)
claude -p "안녕" --output-format stream-json
```

**JSON 출력 예시:**
```json
{
  "type": "result",
  "result": "안녕하세요! 무엇을 도와드릴까요?",
  "cost_usd": 0.003,
  "duration_ms": 1200,
  "num_turns": 1,
  "session_id": "abc123"
}
```

### `--max-turns` 사용

```bash
# 최대 3턴으로 제한
claude -p --max-turns 3 "테스트를 작성하고 실행해줘"
```

### `--permission-mode` 옵션

| 모드 | 설명 |
|------|------|
| `default` | 위험한 작업에 대해 확인 요청 |
| `plan` | 읽기 전용 (파일 수정/명령 실행 불가) |
| `bypassPermissions` | 모든 권한 자동 허용 (위험) |

---

## 8. 환경 변수

Claude Code의 동작을 환경 변수로 제어할 수 있습니다.

| 환경 변수 | 설명 | 예시 |
|-----------|------|------|
| `ANTHROPIC_API_KEY` | Anthropic API 키 | `sk-ant-...` |
| `CLAUDE_CODE_USE_BEDROCK` | AWS Bedrock 사용 | `1` |
| `CLAUDE_CODE_USE_VERTEX` | Google Vertex AI 사용 | `1` |
| `ANTHROPIC_MODEL` | 기본 모델 지정 | `claude-sonnet-4-20250514` |
| `CLAUDE_CODE_MAX_OUTPUT_TOKENS` | 최대 출력 토큰 수 | `16384` |
| `CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC` | 텔레메트리 비활성화 | `1` |
| `HTTP_PROXY` / `HTTPS_PROXY` | 프록시 서버 설정 | `http://proxy:8080` |
| `NO_PROXY` | 프록시 예외 주소 | `localhost,127.0.0.1` |
| `CLAUDE_CODE_API_TIMEOUT` | API 타임아웃 (밀리초) | `30000` |

### AWS Bedrock 설정

```bash
export CLAUDE_CODE_USE_BEDROCK=1
export AWS_REGION=us-east-1
export AWS_ACCESS_KEY_ID=AKIA...
export AWS_SECRET_ACCESS_KEY=...
claude
```

### Google Vertex AI 설정

```bash
export CLAUDE_CODE_USE_VERTEX=1
export CLOUD_ML_REGION=us-east5
export ANTHROPIC_VERTEX_PROJECT_ID=my-project-id
claude
```

---

## 9. 종료 코드

스크립트에서 Claude Code의 실행 결과를 판별할 때 사용합니다.

| 종료 코드 | 의미 |
|-----------|------|
| `0` | 정상 완료 |
| `1` | 일반 오류 |
| `2` | 인증 오류 또는 API 키 문제 |

```bash
# 스크립트에서 종료 코드 활용
claude -p "테스트를 실행해줘"
if [ $? -eq 0 ]; then
    echo "성공"
else
    echo "실패 (종료 코드: $?)"
fi
```

---

## 10. 파이프라인 활용 예시

### CI/CD 통합

```bash
# PR 리뷰 자동화
git diff main...HEAD | claude -p "이 변경 사항을 리뷰해줘. 버그나 보안 이슈가 있는지 확인해줘"

# 커밋 메시지 생성
git diff --staged | claude -p "이 변경 사항에 적합한 커밋 메시지를 작성해줘" --output-format text

# 코드 품질 검사
claude -p "src/ 디렉터리의 코드 품질을 분석하고 개선점을 알려줘" --max-turns 5
```

### 복합 파이프라인

```bash
# 에러 로그 분석 후 수정
cat error.log | claude -p "이 에러의 원인을 분석하고 해결 방법을 알려줘"

# 여러 파일 분석
find . -name "*.test.js" -exec cat {} + | claude -p "테스트 커버리지 개선 방안을 제안해줘"
```

---

## 요약 퀵 레퍼런스

```bash
# 대화형 세션
claude

# 비대화형 질문
claude -p "질문"

# 이전 대화 이어가기
claude -c

# 모델 지정
claude --model claude-opus-4-20250514

# JSON 출력
claude -p --output-format json "질문"

# MCP 서버 추가
claude mcp add <name> <command>

# 환경 진단
claude doctor

# 설정 변경
claude config set <key> <value>
```
