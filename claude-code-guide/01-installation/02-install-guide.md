# OS별 설치 가이드

이 문서에서는 운영체제별 Claude Code 설치 방법을 단계별로 설명합니다. [시스템 요구사항](./requirements.md)을 먼저 확인한 후 진행하세요.

---

## 공통: Claude Code 설치 명령어

모든 운영체제에서 Node.js가 준비되어 있다면 아래 한 줄로 설치할 수 있습니다:

```bash
npm install -g @anthropic-ai/claude-code
```

> **참고**: `-g` 플래그는 **전역(global) 설치**를 의미합니다. 이렇게 설치하면 어떤 디렉토리에서든 `claude` 명령어를 사용할 수 있습니다.

---

## macOS 설치

### 1단계: Node.js 설치

macOS에서는 **Homebrew**를 사용하여 Node.js를 설치하는 것이 가장 간편합니다.

#### Homebrew가 없는 경우

```bash
# Homebrew 설치
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

#### Homebrew로 Node.js 설치

```bash
# Node.js 20 LTS 설치
brew install node@20

# 설치 확인
node --version   # v20.x.x 이상이면 OK
npm --version    # 10.x.x
```

#### nvm으로 Node.js 설치 (권장)

여러 Node.js 버전을 관리해야 한다면 nvm을 사용하세요:

```bash
# nvm 설치
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash

# 터미널 재시작 또는 설정 파일 다시 로드
source ~/.zshrc   # zsh 사용 시
source ~/.bashrc  # bash 사용 시

# Node.js 20 LTS 설치 및 사용
nvm install 20
nvm use 20
nvm alias default 20   # 기본 버전으로 설정

# 확인
node --version
```

### 2단계: Claude Code 설치

```bash
npm install -g @anthropic-ai/claude-code
```

### 3단계: 설치 확인

```bash
claude --version
```

버전 번호가 출력되면 설치가 성공한 것입니다.

### macOS 관련 참고사항

- **Apple Silicon(M1/M2/M3/M4)**: 특별한 추가 설정 없이 네이티브로 동작합니다
- **권한 오류 발생 시**: `sudo npm install -g @anthropic-ai/claude-code`를 사용하거나, npm 전역 경로 권한을 설정하세요 (아래 [문제 해결](#문제-해결) 참조)
- **Homebrew Node.js 경로 문제**: `brew link node@20`이 필요할 수 있습니다

---

## Linux (Ubuntu/Debian) 설치

### 1단계: Node.js 설치

Ubuntu/Debian에서는 여러 방법으로 Node.js를 설치할 수 있습니다.

#### 방법 A: NodeSource 저장소 사용 (간단)

```bash
# NodeSource 저장소 추가 및 Node.js 20 설치
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs

# 설치 확인
node --version
npm --version
```

#### 방법 B: nvm 사용 (권장)

```bash
# nvm 설치
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash

# 터미널 재시작 또는 설정 파일 다시 로드
source ~/.bashrc

# Node.js 20 LTS 설치
nvm install 20
nvm use 20
nvm alias default 20

# 확인
node --version
npm --version
```

#### 방법 C: 시스템 패키지 관리자 (비권장)

```bash
# Ubuntu/Debian 기본 저장소의 Node.js는 버전이 낮을 수 있습니다
# 버전이 18 미만이면 위의 방법 A 또는 B를 사용하세요
sudo apt update
sudo apt install nodejs npm
node --version   # 18 이상인지 확인
```

### 2단계: Claude Code 설치

```bash
npm install -g @anthropic-ai/claude-code
```

### 3단계: 설치 확인

```bash
claude --version
```

### Linux 관련 참고사항

- **EACCES 권한 오류**: nvm을 사용하면 이 문제가 발생하지 않습니다. NodeSource로 설치한 경우 아래 [문제 해결](#문제-해결)을 참조하세요
- **빌드 도구 필요 시**: 일부 npm 패키지는 네이티브 컴파일이 필요할 수 있습니다
  ```bash
  sudo apt install build-essential
  ```

---

## Windows (WSL2) 설치

Claude Code는 Windows에서 직접 실행되지 않습니다. **WSL2(Windows Subsystem for Linux 2)**를 통해 Linux 환경에서 사용해야 합니다.

### 1단계: WSL2 설치

PowerShell을 **관리자 권한**으로 열고 실행합니다:

```powershell
# WSL2 설치 (Ubuntu가 기본 배포판으로 설치됩니다)
wsl --install

# 설치 후 컴퓨터를 재시작하세요
```

재시작 후 WSL Ubuntu 터미널이 자동으로 열리며, Linux 사용자 이름과 비밀번호를 설정합니다.

### 2단계: WSL2 내에서 Node.js 설치

WSL Ubuntu 터미널에서 실행합니다:

```bash
# 시스템 업데이트
sudo apt update && sudo apt upgrade -y

# nvm 설치 (권장)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
source ~/.bashrc

# Node.js 20 설치
nvm install 20
nvm use 20
nvm alias default 20

# 확인
node --version
npm --version
```

### 3단계: Claude Code 설치

WSL 터미널에서 실행합니다:

```bash
npm install -g @anthropic-ai/claude-code
```

### 4단계: 설치 확인

```bash
claude --version
```

### WSL2 관련 참고사항

- **Windows 터미널 사용 권장**: Windows Terminal(Microsoft Store에서 설치)을 사용하면 WSL 환경이 더 쾌적합니다
- **파일 시스템 성능**: WSL 내부 파일 시스템(`/home/username/`)에서 프로젝트를 관리하세요. Windows 파일 시스템(`/mnt/c/`)에서 작업하면 I/O 성능이 크게 저하됩니다
- **VS Code 연동**: VS Code의 "Remote - WSL" 확장을 설치하면 WSL 환경의 파일을 VS Code에서 편집할 수 있습니다
- **OAuth 인증 시 브라우저**: WSL에서 `claude`를 실행하면 Windows 브라우저가 자동으로 열려 인증을 진행합니다

---

## 버전 업데이트

Claude Code는 활발하게 개발되고 있으며, 새로운 기능과 성능 개선이 자주 릴리스됩니다. 최신 버전으로 업데이트하려면:

```bash
# 최신 버전으로 업데이트
npm update -g @anthropic-ai/claude-code

# 현재 설치된 버전 확인
claude --version
```

### 업데이트 알림

Claude Code는 새 버전이 있을 때 실행 시 알림을 표시합니다. 이 알림이 보이면 위 명령어로 업데이트하세요.

### 특정 버전 설치

특정 버전이 필요한 경우:

```bash
# 특정 버전 설치
npm install -g @anthropic-ai/claude-code@1.0.0

# 사용 가능한 버전 목록 확인
npm view @anthropic-ai/claude-code versions
```

---

## 설치 확인

설치가 완료되었다면 아래 명령어들이 모두 정상 동작하는지 확인합니다:

```bash
# 1. 버전 확인
claude --version

# 2. 도움말 확인
claude --help

# 3. Claude Code 실행 (인증이 필요할 수 있음)
claude
```

---

## 문제 해결

### EACCES 권한 오류

npm 전역 설치 시 권한 오류가 발생하는 경우:

```
npm ERR! Error: EACCES: permission denied
```

**해결 방법 1: nvm 사용 (권장)**

nvm으로 Node.js를 설치하면 전역 패키지가 사용자 디렉토리에 설치되므로 권한 문제가 없습니다.

```bash
# nvm 설치 후 Node.js 재설치
nvm install 20
npm install -g @anthropic-ai/claude-code
```

**해결 방법 2: npm 전역 디렉토리 변경**

```bash
# npm 전역 디렉토리를 사용자 홈으로 변경
mkdir -p ~/.npm-global
npm config set prefix '~/.npm-global'

# PATH에 추가
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
source ~/.bashrc

# Claude Code 재설치
npm install -g @anthropic-ai/claude-code
```

**해결 방법 3: sudo 사용 (비권장)**

```bash
sudo npm install -g @anthropic-ai/claude-code
```

> **참고**: `sudo`를 사용한 npm 전역 설치는 보안 및 권한 문제를 일으킬 수 있으므로 가급적 방법 1 또는 2를 사용하세요.

### Node.js 버전 충돌

시스템에 여러 버전의 Node.js가 설치된 경우:

```bash
# 현재 사용 중인 Node.js 경로 확인
which node
# /home/username/.nvm/versions/node/v20.11.0/bin/node

# nvm으로 버전 전환
nvm use 20
```

### 네트워크/프록시 문제

npm이 패키지를 다운로드하지 못하는 경우:

```bash
# npm 프록시 설정
npm config set proxy http://proxy.company.com:8080
npm config set https-proxy http://proxy.company.com:8080

# npm 레지스트리 변경 (사내 미러가 있는 경우)
npm config set registry https://registry.company.com/npm/

# SSL 인증서 문제 (자체 서명 인증서 사용 환경)
npm config set strict-ssl false   # 보안상 임시로만 사용하세요
```

### 설치 후 `claude` 명령어를 찾을 수 없는 경우

```bash
# npm 전역 bin 경로 확인
npm config get prefix
# 출력 경로/bin 이 PATH에 포함되어 있는지 확인

# PATH 확인
echo $PATH

# 필요시 PATH에 추가 (예시)
echo 'export PATH=$(npm config get prefix)/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

---

## 완전 제거 (Uninstall)

Claude Code를 제거해야 하는 경우:

```bash
# Claude Code 제거
npm uninstall -g @anthropic-ai/claude-code

# 설정 파일 제거 (선택)
rm -rf ~/.claude
```

---

## 다음 단계

설치가 완료되었다면 첫 실행 및 초기 설정을 진행합니다.

→ [첫 실행 및 초기 설정](./first-run.md)
