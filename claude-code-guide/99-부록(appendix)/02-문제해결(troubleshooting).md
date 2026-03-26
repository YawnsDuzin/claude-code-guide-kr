# 부록 B: 문제 해결 가이드

Claude Code 사용 중 발생할 수 있는 문제와 해결 방법을 체계적으로 정리한 가이드입니다.

---

## 1. 설치 문제

### Node.js 버전 오류

**증상:** `Error: Unsupported Node.js version` 또는 설치 실패

**원인:** Claude Code는 Node.js 18 이상을 요구합니다.

**해결:**

```bash
# 현재 Node.js 버전 확인
node --version

# nvm으로 최신 LTS 설치
nvm install --lts
nvm use --lts

# 또는 직접 설치 (Ubuntu/Debian)
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs

# 설치 후 재시도
npm install -g @anthropic-ai/claude-code
```

### npm 권한 오류

**증상:** `EACCES: permission denied` 또는 `npm ERR! code EACCES`

**해결 방법 1: npm 기본 디렉터리 변경 (권장)**

```bash
mkdir -p ~/.npm-global
npm config set prefix '~/.npm-global'

# ~/.bashrc 또는 ~/.zshrc에 추가
export PATH=~/.npm-global/bin:$PATH

# 적용 후 재설치
source ~/.bashrc
npm install -g @anthropic-ai/claude-code
```

**해결 방법 2: npx로 직접 실행**

```bash
npx @anthropic-ai/claude-code
```

**주의:** `sudo npm install -g`는 보안상 권장하지 않습니다.

### WSL (Windows Subsystem for Linux) 문제

**증상:** WSL 환경에서 설치 또는 실행 실패

**해결:**

```bash
# WSL 버전 확인 (WSL2 권장)
wsl --version

# Node.js가 WSL 내부에 설치되었는지 확인
which node  # /usr/bin/node 또는 /home/user/.nvm/... 이어야 함

# Windows의 Node.js가 아닌 WSL 내부의 Node.js를 사용해야 합니다
# /mnt/c/Program Files/nodejs/node 경로라면 잘못된 것

# WSL 내부에 Node.js 설치
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
source ~/.bashrc
nvm install --lts
```

**네트워크 문제 (WSL):**

```bash
# DNS 문제 해결
sudo sh -c 'echo "nameserver 8.8.8.8" > /etc/resolv.conf'

# 프록시 환경인 경우
export HTTP_PROXY=http://your-proxy:port
export HTTPS_PROXY=http://your-proxy:port
```

---

## 2. 인증 문제

### API 키 오류

**증상:** `Invalid API key`, `Authentication failed`, `401 Unauthorized`

**해결:**

```bash
# API 키 형식 확인 (sk-ant-로 시작해야 함)
echo $ANTHROPIC_API_KEY

# API 키 설정
export ANTHROPIC_API_KEY="sk-ant-api03-..."

# 영구 설정 (~/.bashrc 또는 ~/.zshrc에 추가)
echo 'export ANTHROPIC_API_KEY="sk-ant-api03-..."' >> ~/.bashrc
source ~/.bashrc

# 키 유효성 확인
claude doctor
```

**주의 사항:**
- API 키에 공백이나 줄바꿈이 포함되지 않았는지 확인
- 키가 비활성화되지 않았는지 Anthropic Console에서 확인
- 환경 변수 이름이 정확히 `ANTHROPIC_API_KEY`인지 확인

### OAuth 인증 문제

**증상:** OAuth 로그인 실패 또는 토큰 만료

**해결:**

```bash
# 로그아웃 후 재로그인
claude logout
claude login

# 브라우저가 열리지 않는 경우
# 표시된 URL을 직접 브라우저에 복사하여 접속

# 조직 계정 문제
# Anthropic Console에서 조직 멤버십 확인
```

### Rate Limit (속도 제한) 초과

**증상:** `429 Too Many Requests`, `Rate limit exceeded`, `Overloaded`

**해결:**

```bash
# 잠시 대기 후 재시도 (보통 60초)
# Claude Code가 자동으로 재시도합니다

# 사용량 확인
/cost

# 속도 제한 완화 방법:
# 1. API 플랜 업그레이드
# 2. 요청 간격 늘리기
# 3. 프롬프트 캐싱 활용 (동일 프리픽스 재사용)
# 4. 더 작은 모델 사용 (Haiku)
```

**Rate Limit 유형:**

| 유형 | 설명 | 대응 |
|------|------|------|
| RPM (분당 요청) | 분당 요청 수 초과 | 요청 간격 늘리기 |
| TPM (분당 토큰) | 분당 토큰 수 초과 | 프롬프트 크기 줄이기 |
| Daily Limit | 일일 한도 초과 | 다음 날까지 대기 또는 플랜 업그레이드 |

---

## 3. 권한 오류

### 파일 수정 권한 거부

**증상:** Claude가 파일을 수정하려 하지만 권한 프롬프트에서 거부됨

**해결:**

```bash
# 현재 권한 설정 확인
/permissions

# 프로젝트 단위로 도구 허용
# .claude/settings.json에서 설정
{
  "permissions": {
    "allow": [
      "Edit",
      "Write",
      "Bash(npm test)",
      "Bash(npm run lint)"
    ]
  }
}

# 또는 대화 중 권한 부여
# "Always allow for this project" 선택
```

### Bash 명령어 실행 차단

**증상:** `Permission denied for tool: Bash`

**해결:**

특정 명령어만 허용하는 것이 보안상 안전합니다.

```bash
# 설정에서 특정 명령어 허용
claude config set --project permissions.allow '["Bash(npm test)", "Bash(npm run build)", "Bash(git *)"]'
```

### 위험한 명령어 경고

**증상:** `This command may be dangerous` 경고

Claude Code는 `rm -rf`, `git push --force` 등 위험한 명령어에 대해 경고합니다. 실제로 필요한 경우에만 허용하세요.

---

## 4. 컨텍스트 소진 (Context Exhaustion)

### 컨텍스트 윈도우 초과

**증상:** `Context window full`, 응답 품질 저하, 이전 대화 내용을 기억하지 못함

**해결:**

```bash
# 대화 요약으로 컨텍스트 절약
/compact

# 특정 내용 위주로 요약
/compact API 엔드포인트 구현 관련 내용만 유지해줘

# 자동 컴팩션 활성화
claude config set autoCompact true

# 새로운 세션으로 시작
/clear
```

**예방 방법:**

1. **작업 단위를 작게 유지**: 한 세션에서 하나의 기능에 집중
2. **불필요한 파일 읽기 최소화**: 필요한 파일만 참조하도록 지시
3. **CLAUDE.md 활용**: 반복적인 컨텍스트를 메모리 파일에 기록
4. **정기적 컴팩션**: 긴 작업 중 주기적으로 `/compact` 실행
5. **서브에이전트 활용**: 큰 작업을 여러 서브태스크로 분할

### 컨텍스트 사용량 확인

```bash
# 현재 비용 및 토큰 사용량 확인
/cost
```

---

## 5. 도구 실행 오류

### 파일 편집 실패

**증상:** `Edit failed: old_string not found` 또는 `Edit failed: old_string is not unique`

**원인:**
- 파일 내용이 이미 변경됨
- 동일한 문자열이 여러 곳에 존재
- 들여쓰기(공백/탭)가 일치하지 않음

**해결:**
- Claude에게 파일을 다시 읽도록 요청: "파일을 다시 읽고 수정해줘"
- 더 넓은 컨텍스트를 포함하도록 요청: "주변 코드를 더 포함해서 수정해줘"

### Bash 명령어 타임아웃

**증상:** 명령어가 응답 없이 멈춤

**해결:**

```bash
# 타임아웃이 긴 명령어는 백그라운드로 실행하도록 지시
# Claude에게: "이 명령어를 백그라운드로 실행해줘"

# 또는 타임아웃 값 조정
# 명령어에 자체 타임아웃 설정
timeout 30 npm test
```

### 도구를 찾을 수 없음

**증상:** `Tool not found` 또는 MCP 도구가 목록에 없음

**해결:**

```bash
# MCP 서버 상태 확인
claude mcp list

# MCP 서버 재시작 (제거 후 다시 추가)
claude mcp remove <name>
claude mcp add <name> <command>

# 서버 로그 확인
claude mcp get <name>
```

---

## 6. 네트워크 및 프록시 문제

### 연결 실패

**증상:** `ECONNREFUSED`, `ETIMEDOUT`, `Network error`

**해결:**

```bash
# 기본 연결 테스트
curl -s https://api.anthropic.com/v1/messages -o /dev/null -w "%{http_code}"

# DNS 확인
nslookup api.anthropic.com

# 방화벽 확인 (api.anthropic.com:443 접근 필요)
nc -zv api.anthropic.com 443
```

### 프록시 환경 설정

```bash
# HTTP 프록시 설정
export HTTP_PROXY=http://proxy.company.com:8080
export HTTPS_PROXY=http://proxy.company.com:8080

# 프록시 예외
export NO_PROXY=localhost,127.0.0.1,.company.com

# 프록시 인증이 필요한 경우
export HTTPS_PROXY=http://username:password@proxy.company.com:8080

# npm 프록시 설정 (설치 시)
npm config set proxy http://proxy.company.com:8080
npm config set https-proxy http://proxy.company.com:8080
```

### SSL/TLS 오류

**증상:** `UNABLE_TO_VERIFY_LEAF_SIGNATURE`, `CERT_HAS_EXPIRED`

**해결:**

```bash
# 회사 CA 인증서 설정
export NODE_EXTRA_CA_CERTS=/path/to/company-ca.crt

# 임시 우회 (보안상 비권장)
export NODE_TLS_REJECT_UNAUTHORIZED=0
```

---

## 7. MCP 연결 문제

### MCP 서버 시작 실패

**증상:** `MCP server failed to start`, `Connection refused`

**해결:**

```bash
# 1. 서버 명령어가 올바른지 확인
which npx  # npx 경로 확인
node --version  # Node.js 버전 확인

# 2. 서버를 독립적으로 실행하여 에러 확인
npx -y @modelcontextprotocol/server-github

# 3. 환경 변수 확인
claude mcp get <name>

# 4. 서버 재설정
claude mcp remove <name>
claude mcp add <name> -e KEY=value -- <command>
```

### MCP 도구 호출 실패

**증상:** MCP 도구가 에러를 반환

**해결:**

```bash
# 서버 로그 확인
# 서버의 stderr 출력을 확인합니다

# 타임아웃 문제인 경우
# MCP 서버의 응답 시간이 너무 긴 것이 원인일 수 있음

# 인증 토큰 만료
# 환경 변수의 토큰을 갱신
claude mcp remove <name>
claude mcp add <name> -e GITHUB_TOKEN=new_token -- <command>
```

### MCP 서버 자주 끊김

**원인:** 메모리 부족, 프로세스 충돌, 네트워크 불안정

**해결:**
- 서버 프로세스의 리소스 사용량 모니터링
- 안정적인 네트워크 연결 확인
- 서버 버전 업데이트

---

## 8. 성능 문제

### 응답이 느림

**원인과 해결:**

| 원인 | 해결 방법 |
|------|-----------|
| 컨텍스트 윈도우가 큼 | `/compact`로 압축 |
| 모델 과부하 | 잠시 후 재시도 |
| 네트워크 지연 | 네트워크 상태 확인 |
| 큰 파일 읽기 | 필요한 부분만 읽도록 지시 |

### 토큰 비용이 높음

```bash
# 비용 확인
/cost

# 비용 절감 방법:
# 1. 더 작은 모델 사용
/model claude-haiku-3-5-20241022

# 2. 프롬프트 간결하게 작성

# 3. 자동 컴팩션으로 불필요한 컨텍스트 제거
claude config set autoCompact true

# 4. --max-turns로 턴 수 제한
claude -p --max-turns 3 "작업해줘"
```

### 메모리 사용량 높음

```bash
# Node.js 메모리 제한 늘리기
export NODE_OPTIONS="--max-old-space-size=4096"
claude
```

---

## 9. 자주 발생하는 오류 메시지와 해결

| 오류 메시지 | 원인 | 해결 |
|------------|------|------|
| `command not found: claude` | 설치 안 됨 또는 PATH 미설정 | `npm install -g @anthropic-ai/claude-code` 후 PATH 확인 |
| `ANTHROPIC_API_KEY is not set` | API 키 미설정 | `export ANTHROPIC_API_KEY=sk-ant-...` |
| `Model not found` | 잘못된 모델 ID | 올바른 모델 ID 확인 후 재지정 |
| `Context window exceeded` | 컨텍스트 초과 | `/compact` 또는 `/clear` |
| `Rate limit exceeded` | 속도 제한 초과 | 잠시 대기 후 재시도 |
| `ECONNREFUSED` | API 서버 연결 실패 | 네트워크 및 프록시 설정 확인 |
| `Permission denied` | 파일/도구 권한 없음 | 권한 설정 확인 (`/permissions`) |
| `MCP server not responding` | MCP 서버 다운 | `claude mcp remove` 후 재추가 |
| `ENOMEM` | 메모리 부족 | 다른 프로세스 정리 또는 메모리 늘리기 |
| `ETIMEOUT` | 요청 타임아웃 | 네트워크 확인, 재시도 |
| `Invalid JSON` | 잘못된 설정 파일 | `.claude/settings.json` 문법 확인 |
| `Unexpected token` | 설정 파일 파싱 오류 | JSON 문법 검증기로 확인 |

---

## 10. `claude doctor` 활용

`claude doctor`는 환경 문제를 자동으로 진단합니다.

```bash
claude doctor
```

**진단 결과 예시:**

```
Claude Code Doctor
==================

[PASS] Node.js version: v20.11.0 (>= 18.0.0)
[PASS] npm version: 10.2.4
[PASS] Claude Code version: 1.0.10 (latest)
[PASS] Authentication: API key configured
[PASS] Network: api.anthropic.com reachable
[WARN] MCP server 'github': not responding
[PASS] Permissions: default mode

1 warning found. Run 'claude mcp get github' for details.
```

**결과 해석:**

| 상태 | 의미 | 조치 |
|------|------|------|
| `[PASS]` | 정상 | 없음 |
| `[WARN]` | 경고 (작동은 함) | 권장 조치 따르기 |
| `[FAIL]` | 실패 (작동 불가) | 반드시 해결 필요 |

---

## 11. 도움 요청

### 자체 진단 체크리스트

문제를 보고하기 전에 다음을 확인하세요:

1. **최신 버전인가?**
   ```bash
   npm update -g @anthropic-ai/claude-code
   ```

2. **Node.js 18 이상인가?**
   ```bash
   node --version
   ```

3. **`claude doctor`를 실행했는가?**
   ```bash
   claude doctor
   ```

4. **오류 메시지를 정확히 복사했는가?**

5. **최소 재현 가능한 예시를 준비했는가?**

### GitHub Issues에서 버그 보고

```bash
# 대화형 모드에서 버그 보고
/bug

# 또는 GitHub에서 직접 이슈 생성
# https://github.com/anthropics/claude-code/issues
```

**좋은 버그 보고에 포함할 내용:**

- Claude Code 버전 (`claude --version`)
- Node.js 버전 (`node --version`)
- 운영체제 및 버전
- `claude doctor` 출력
- 정확한 오류 메시지
- 재현 단계
- 예상 동작 vs 실제 동작

### 커뮤니티 지원

- **GitHub Discussions**: 질문 및 토론
- **GitHub Issues**: 버그 보고 및 기능 요청
- **공식 문서**: https://docs.anthropic.com/en/docs/claude-code

---

## 빠른 문제 해결 플로차트

```
문제 발생
    |
    v
claude doctor 실행
    |
    +-- FAIL --> 해당 항목 해결
    |
    +-- PASS --> 계속
         |
         v
    오류 메시지 확인
         |
         +-- 인증 오류 --> API 키/OAuth 확인
         |
         +-- 네트워크 오류 --> 프록시/방화벽 확인
         |
         +-- 권한 오류 --> /permissions 확인
         |
         +-- 컨텍스트 오류 --> /compact 실행
         |
         +-- 기타 --> GitHub Issues 검색/보고
```
