# 시스템 요구사항

Claude Code를 설치하기 전에 아래 요구사항을 확인하세요. 모든 항목을 충족해야 안정적으로 사용할 수 있습니다.

---

## 운영체제 (OS)

Claude Code는 다음 운영체제를 지원합니다:

| OS | 최소 버전 | 비고 |
|----|-----------|------|
| **macOS** | 10.15 (Catalina) 이상 | Intel 및 Apple Silicon(M1/M2/M3/M4) 모두 지원 |
| **Ubuntu** | 20.04 LTS 이상 | 가장 많이 테스트된 Linux 배포판 |
| **Debian** | 10 (Buster) 이상 | Ubuntu와 동일한 방식으로 설치 |
| **Windows** | WSL2를 통한 사용 | 네이티브 Windows는 지원하지 않음 |

> **주의**: Windows 사용자는 반드시 **WSL2(Windows Subsystem for Linux 2)**를 통해 사용해야 합니다. PowerShell이나 CMD에서는 직접 실행할 수 없습니다. WSL2 설정 방법은 [설치 가이드](./install-guide.md)에서 자세히 설명합니다.

### OS 버전 확인 방법

```bash
# macOS 버전 확인
sw_vers

# Ubuntu/Debian 버전 확인
lsb_release -a
# 또는
cat /etc/os-release

# Windows에서 WSL2 버전 확인 (PowerShell에서 실행)
wsl --version
```

---

## Node.js

Claude Code는 Node.js 기반으로 동작하므로 **Node.js 18 이상**이 필수입니다.

| 항목 | 요구사항 |
|------|----------|
| **최소 버전** | Node.js 18.0.0 |
| **권장 버전** | Node.js 20 LTS 또는 22 LTS |
| **패키지 관리자** | npm (Node.js와 함께 설치됨) |

### Node.js 설치 여부 및 버전 확인

```bash
# Node.js 버전 확인
node --version
# 출력 예시: v20.11.0  (18 이상이면 OK)

# npm 버전 확인
npm --version
# 출력 예시: 10.2.4
```

### Node.js가 설치되어 있지 않다면

Node.js가 없거나 버전이 낮다면, [설치 가이드](./install-guide.md)에서 OS별 설치 방법을 참고하세요. 간단한 요약:

```bash
# macOS (Homebrew 사용)
brew install node@20

# Ubuntu/Debian (NodeSource 저장소)
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs

# nvm 사용 (모든 OS 공통, 권장)
nvm install 20
nvm use 20
```

> **팁**: 여러 프로젝트에서 서로 다른 Node.js 버전을 사용한다면 **nvm(Node Version Manager)**을 사용하는 것을 강력히 권장합니다. 프로젝트별로 Node.js 버전을 쉽게 전환할 수 있습니다.

---

## 하드웨어 사양

### 메모리 (RAM)

| 구분 | 용량 | 설명 |
|------|------|------|
| **최소** | 4GB | 기본적인 동작 가능, 대규모 프로젝트에서 느려질 수 있음 |
| **권장** | 8GB 이상 | 쾌적한 사용 경험, 대부분의 프로젝트에 적합 |

Claude Code 자체의 메모리 사용량은 크지 않지만, Node.js 런타임과 함께 다른 개발 도구(IDE, 브라우저, Docker 등)를 동시에 사용하는 상황을 고려해야 합니다.

### 디스크 공간

| 항목 | 필요 용량 |
|------|-----------|
| **Claude Code 설치** | 약 500MB |
| **Node.js + npm** | 약 200-300MB |
| **총 권장 여유 공간** | 1GB 이상 |

```bash
# 현재 디스크 여유 공간 확인
df -h /
```

### CPU

특별한 CPU 요구사항은 없습니다. Claude Code는 주로 API 통신을 통해 작업하므로, 일반적인 개발 환경의 CPU라면 충분합니다.

---

## 네트워크

Claude Code는 **인터넷 연결이 필수**입니다. 모든 AI 처리는 Anthropic의 클라우드 서버에서 이루어집니다.

| 항목 | 요구사항 |
|------|----------|
| **인터넷 연결** | 필수 (오프라인 사용 불가) |
| **속도** | 최소 1Mbps 이상 권장 |
| **프록시** | HTTP/HTTPS 프록시 지원 |
| **방화벽** | `api.anthropic.com` 접근 허용 필요 |

### 네트워크 연결 확인

```bash
# Anthropic API 서버 접근 가능 여부 확인
curl -s -o /dev/null -w "%{http_code}" https://api.anthropic.com/v1/messages
# 401이 나오면 정상 (인증 없이 접근했으므로 401은 서버에 도달했다는 의미)
```

### 프록시 환경에서 사용

회사 네트워크 등 프록시를 사용하는 환경에서는 환경 변수를 설정해야 합니다:

```bash
# 프록시 설정 (필요한 경우)
export HTTP_PROXY=http://proxy.company.com:8080
export HTTPS_PROXY=http://proxy.company.com:8080

# .bashrc 또는 .zshrc에 추가하면 영구 적용됩니다
echo 'export HTTP_PROXY=http://proxy.company.com:8080' >> ~/.bashrc
echo 'export HTTPS_PROXY=http://proxy.company.com:8080' >> ~/.bashrc
```

---

## 인증: API 키 또는 Claude 구독

Claude Code를 사용하려면 다음 중 하나의 인증 수단이 필요합니다:

### 옵션 1: Anthropic API 키

| 항목 | 내용 |
|------|------|
| **발급 방법** | [console.anthropic.com](https://console.anthropic.com)에서 발급 |
| **비용** | 사용량 기반 과금 (토큰당 비용) |
| **적합한 사용자** | 개인 개발자, 사용량을 직접 관리하고 싶은 경우 |

```bash
# API 키 설정 방법
export ANTHROPIC_API_KEY=sk-ant-xxxxxxxxxxxxx

# 영구 설정 (.bashrc 또는 .zshrc에 추가)
echo 'export ANTHROPIC_API_KEY=sk-ant-xxxxxxxxxxxxx' >> ~/.bashrc
source ~/.bashrc
```

> **보안 주의**: API 키는 절대 Git에 커밋하거나 공개 저장소에 노출하지 마세요. `.bashrc` 파일 권한을 `600`으로 설정하는 것을 권장합니다.

### 옵션 2: Claude 구독 (OAuth 로그인)

| 구독 플랜 | Claude Code 사용 | 설명 |
|-----------|-------------------|------|
| **Claude Max** | 가능 | 개인 사용자용 구독 플랜 |
| **Claude Team** | 가능 | 소규모 팀용 구독 플랜 |
| **Claude Enterprise** | 가능 | 대규모 조직용 구독 플랜 |
| Claude Free / Pro | 불가 | Claude Code 미포함 |

구독 플랜을 사용하면 API 키 없이 **OAuth 로그인**으로 인증할 수 있습니다. 첫 실행 시 브라우저가 열리며 로그인 과정을 안내합니다.

---

## 요구사항 한눈에 보기 (체크리스트)

설치 전에 아래 항목을 모두 확인하세요:

```
[ ] macOS 10.15+ / Ubuntu 20.04+ / Debian 10+ / Windows WSL2
[ ] Node.js 18 이상 설치됨
[ ] npm 사용 가능
[ ] RAM 4GB 이상 (8GB 권장)
[ ] 디스크 여유 공간 1GB 이상
[ ] 인터넷 연결 가능
[ ] api.anthropic.com 접근 가능
[ ] Anthropic API 키 또는 Claude 구독(Max/Team/Enterprise) 보유
```

### 빠른 확인 스크립트

아래 명령어를 터미널에 복사해서 실행하면 주요 항목을 한 번에 확인할 수 있습니다:

```bash
echo "=== Claude Code 설치 요구사항 확인 ==="
echo ""
echo "1. OS 정보:"
uname -a
echo ""
echo "2. Node.js 버전:"
node --version 2>/dev/null || echo "  Node.js가 설치되어 있지 않습니다"
echo ""
echo "3. npm 버전:"
npm --version 2>/dev/null || echo "  npm이 설치되어 있지 않습니다"
echo ""
echo "4. 메모리:"
free -h 2>/dev/null || sysctl -n hw.memsize 2>/dev/null | awk '{print $0/1024/1024/1024 " GB"}'
echo ""
echo "5. 디스크 여유 공간:"
df -h / | tail -1
echo ""
echo "6. API 서버 연결:"
curl -s -o /dev/null -w "  HTTP 상태 코드: %{http_code} (401=정상)\n" https://api.anthropic.com/v1/messages 2>/dev/null || echo "  연결 실패"
echo ""
echo "7. API 키 설정:"
if [ -n "$ANTHROPIC_API_KEY" ]; then echo "  설정됨"; else echo "  미설정 (OAuth 로그인 사용 가능)"; fi
echo ""
echo "=== 확인 완료 ==="
```

---

## 다음 단계

모든 요구사항을 충족했다면 설치를 진행합니다.

→ [OS별 설치 가이드](./install-guide.md)
