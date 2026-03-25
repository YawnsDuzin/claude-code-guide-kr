# IDE 연동 (IDE Integration)

## 개요

Claude Code는 CLI(명령줄) 도구이지만, **VS Code**와 **JetBrains IDE**에 확장 프로그램을 설치하여 IDE 안에서 직접 사용할 수 있습니다. 터미널과 에디터를 오가는 번거로움 없이, 코드를 보면서 바로 Claude와 대화할 수 있습니다.

쉽게 말해, **IDE 안에 Claude Code를 내장하여 더 편리하게 사용하는 것**입니다.

---

## VS Code 확장 프로그램

### 설치 방법

#### 1단계: 확장 프로그램 설치

1. VS Code를 엽니다
2. 사이드바에서 **Extensions** 아이콘을 클릭 (또는 `Ctrl+Shift+X` / `Cmd+Shift+X`)
3. 검색창에 **"Claude Code"** 입력
4. **Anthropic**에서 제공하는 공식 확장 프로그램을 찾아 **Install** 클릭

또는 터미널에서 설치:

```bash
code --install-extension anthropic.claude-code
```

#### 2단계: Claude Code CLI 확인

VS Code 확장 프로그램은 Claude Code CLI가 설치되어 있어야 합니다.

```bash
# Claude Code CLI가 설치되어 있는지 확인
claude --version

# 설치되어 있지 않다면
npm install -g @anthropic-ai/claude-code
```

#### 3단계: 인증 확인

```bash
# 이미 CLI에서 인증이 완료되어 있다면 자동으로 연동됩니다
claude
```

### VS Code에서 사용하기

#### Claude Code 패널 열기

설치 후 VS Code에서 Claude Code를 여는 방법은 여러 가지입니다.

1. **사이드바 아이콘** 클릭
2. **명령 팔레트** (`Ctrl+Shift+P` / `Cmd+Shift+P`) 에서 "Claude Code" 검색
3. **키보드 단축키** 사용 (아래 참조)

#### 기본 사용법

VS Code 내 Claude Code 패널에서 CLI와 동일하게 대화할 수 있습니다.

```
Claude Code 패널에서:

사용자: 이 파일에 에러 처리를 추가해줘
Claude: 현재 열려 있는 파일을 분석하겠습니다...
```

#### 코드 선택 후 질문

에디터에서 코드를 선택한 후 Claude에게 질문할 수 있습니다.

1. 에디터에서 코드 블록을 **드래그하여 선택**
2. 우클릭 → **"Ask Claude about selection"** 선택
3. 또는 단축키로 선택한 코드를 Claude에게 전달

```
# 코드를 선택하고 질문
사용자: 이 코드의 버그를 찾아줘
사용자: 이 함수를 최적화해줘
사용자: 이 로직을 설명해줘
```

#### 인라인 편집

Claude가 제안한 코드 변경 사항을 VS Code의 diff 뷰어로 확인하고 적용할 수 있습니다.

```
1. Claude가 파일 수정을 제안
2. VS Code에서 변경 전/후를 나란히 비교 (diff view)
3. "Accept" 또는 "Reject"로 변경 적용/거부
```

### VS Code 전용 기능

VS Code 확장 프로그램에서는 CLI에 없는 추가 기능을 사용할 수 있습니다.

| 기능 | 설명 |
|------|------|
| **현재 파일 인식** | 에디터에서 열린 파일을 자동으로 컨텍스트에 포함 |
| **코드 선택 전달** | 선택한 코드 블록을 Claude에게 직접 전달 |
| **인라인 diff** | 변경 사항을 에디터에서 바로 확인 |
| **터미널 통합** | VS Code 내장 터미널에서 Claude Code CLI 실행 가능 |
| **작업 영역 인식** | 열린 프로젝트/워크스페이스를 자동 인식 |

---

## JetBrains 플러그인

### 설치 방법

#### 1단계: 플러그인 설치

1. JetBrains IDE (IntelliJ, WebStorm, PyCharm 등)를 엽니다
2. **Settings** (`Ctrl+Alt+S` / `Cmd+,`) 열기
3. **Plugins** → **Marketplace** 탭 선택
4. **"Claude Code"** 검색
5. **Install** 클릭 후 IDE 재시작

#### 2단계: Claude Code CLI 확인

```bash
# JetBrains 플러그인도 CLI가 필요합니다
claude --version
```

#### 3단계: 설정 확인

1. **Settings** → **Tools** → **Claude Code**
2. CLI 경로가 올바르게 설정되어 있는지 확인
3. 필요한 경우 경로를 수동으로 지정

### JetBrains에서 사용하기

#### Claude Code 도구 창 열기

1. **View** → **Tool Windows** → **Claude Code**
2. 또는 단축키로 도구 창 토글

#### 코드 선택 후 질문

1. 에디터에서 코드를 선택
2. 우클릭 → **Claude Code** 메뉴 선택
3. 또는 단축키 사용

#### 지원하는 JetBrains IDE

| IDE | 지원 여부 | 주요 대상 언어 |
|-----|----------|---------------|
| IntelliJ IDEA | 지원 | Java, Kotlin |
| WebStorm | 지원 | JavaScript, TypeScript |
| PyCharm | 지원 | Python |
| GoLand | 지원 | Go |
| PhpStorm | 지원 | PHP |
| RubyMine | 지원 | Ruby |
| CLion | 지원 | C, C++ |
| Rider | 지원 | C#, .NET |

---

## IDE vs CLI 기능 비교

| 기능 | CLI | VS Code | JetBrains |
|------|-----|---------|-----------|
| 대화형 작업 | O | O | O |
| 파일 읽기/수정 | O | O | O |
| 셸 명령어 실행 | O | O | O |
| 슬래시 명령어 | O | O | O |
| 메모리 (CLAUDE.md) | O | O | O |
| MCP 서버 연동 | O | O | O |
| 현재 파일 자동 인식 | X | O | O |
| 코드 선택 전달 | X | O | O |
| 인라인 diff 뷰어 | X | O | O |
| 비대화형 모드 (`-p`) | O | X | X |
| 파이프 입력 | O | X | X |
| CI/CD 연동 | O | X | X |

**정리**: IDE 확장은 시각적 편의성이 뛰어나고, CLI는 자동화와 파이프라인 활용에 강점이 있습니다.

---

## 터미널 통합

IDE 내장 터미널에서 Claude Code CLI를 직접 실행하는 것도 좋은 방법입니다.

### VS Code 통합 터미널

```bash
# VS Code의 내장 터미널 열기: Ctrl+` (백틱)
# 터미널에서 Claude Code 실행
claude
```

**장점**:
- CLI의 모든 기능 사용 가능
- IDE 에디터와 나란히 작업 가능
- 터미널 분할로 여러 Claude 세션 운영 가능

### JetBrains 통합 터미널

```bash
# JetBrains의 내장 터미널 열기: Alt+F12
# 터미널에서 Claude Code 실행
claude
```

### 터미널 분할 활용

```
┌─────────────────────────────────────┐
│              에디터                   │
│  (코드를 보면서)                      │
├──────────────────┬──────────────────┤
│   터미널 1       │   터미널 2        │
│   Claude Code    │   개발 서버       │
│   (AI 대화)      │   (npm run dev)  │
└──────────────────┴──────────────────┘
```

---

## 나란히 작업하기 (Side-by-side Workflow)

IDE와 Claude Code를 함께 사용하는 효과적인 워크플로우를 소개합니다.

### 워크플로우 1: 코드 작성 + 리뷰

```
1. 에디터에서 코드 작성
2. 작성한 코드를 선택
3. Claude에게 리뷰 요청
4. 피드백에 따라 수정
5. 반복
```

### 워크플로우 2: 디버깅

```
1. 에디터에서 에러가 발생하는 코드 확인
2. 에러 메시지와 관련 코드를 Claude에게 전달
3. Claude의 분석과 수정 제안 확인
4. 제안된 수정 사항을 에디터에서 적용
5. 테스트 실행하여 확인
```

### 워크플로우 3: 탐색 + 이해

```
1. 새로운 코드베이스를 에디터에서 열기
2. Claude에게 프로젝트 구조 설명 요청
3. 궁금한 파일이나 함수를 선택하여 설명 요청
4. 전체 아키텍처 이해
```

### 워크플로우 4: 테스트 주도 개발 (TDD)

```
1. Claude에게 테스트 코드 작성 요청
2. 에디터에서 테스트 코드 확인
3. 터미널에서 테스트 실행 (실패 확인)
4. Claude에게 구현 코드 작성 요청
5. 테스트 통과 확인
6. 리팩토링
```

---

## 키보드 단축키

### VS Code 단축키

| 동작 | Windows/Linux | macOS |
|------|--------------|-------|
| Claude Code 패널 열기/닫기 | `Ctrl+Shift+P` → "Claude" | `Cmd+Shift+P` → "Claude" |
| 선택 코드 Claude에게 전달 | `Ctrl+L` | `Cmd+L` |
| 통합 터미널 열기 | `` Ctrl+` `` | `` Cmd+` `` |
| 명령 팔레트 | `Ctrl+Shift+P` | `Cmd+Shift+P` |
| 사이드바 토글 | `Ctrl+B` | `Cmd+B` |
| 터미널 분할 | `Ctrl+Shift+5` | `Cmd+Shift+5` |

### JetBrains 단축키

| 동작 | Windows/Linux | macOS |
|------|--------------|-------|
| Claude Code 도구 창 | `Alt+C` (커스텀) | `Option+C` (커스텀) |
| 통합 터미널 열기 | `Alt+F12` | `Option+F12` |
| 설정 열기 | `Ctrl+Alt+S` | `Cmd+,` |
| 코드 선택 확장 | `Ctrl+W` | `Option+Up` |

**참고**: 단축키는 IDE 버전과 설정에 따라 다를 수 있습니다. Settings > Keymap에서 커스터마이징 가능합니다.

---

## 권장 IDE 설정

### VS Code 권장 설정

`settings.json`에 다음 설정을 추가하면 Claude Code와의 작업이 더 편리합니다.

```json
{
  // 자동 저장 (Claude가 수정한 내용이 바로 반영)
  "files.autoSave": "afterDelay",
  "files.autoSaveDelay": 1000,

  // 터미널 설정
  "terminal.integrated.defaultProfile.linux": "bash",
  "terminal.integrated.fontSize": 14,

  // 에디터 설정
  "editor.formatOnSave": true,
  "editor.wordWrap": "on",

  // diff 에디터 설정
  "diffEditor.renderSideBySide": true,
  "diffEditor.ignoreTrimWhitespace": false,

  // 파일 탐색기에서 .claude 디렉토리 표시
  "files.exclude": {
    // .claude 디렉토리는 숨기지 않음 (CLAUDE.md 편집을 위해)
  }
}
```

### JetBrains 권장 설정

1. **자동 저장 활성화**
   - Settings → Appearance & Behavior → System Settings
   - "Save files on frame deactivation" 체크

2. **터미널 설정**
   - Settings → Tools → Terminal
   - Shell path 확인
   - 폰트 크기 조절

3. **코드 스타일 동기화**
   - Settings → Editor → Code Style
   - 프로젝트의 `.editorconfig` 또는 코드 스타일 설정과 일치시키기

---

## 효과적인 사용 팁

### 1. IDE 확장 + CLI 병행 사용

IDE 확장과 CLI를 상황에 맞게 병행하세요.

```
# IDE 확장이 좋은 경우
- 코드를 보면서 대화할 때
- 특정 코드 블록에 대해 질문할 때
- diff를 시각적으로 확인할 때

# CLI가 좋은 경우
- 자동화 스크립트에서 사용할 때
- 비대화형 모드가 필요할 때
- SSH 원격 서버에서 작업할 때
```

### 2. 작업 영역(Workspace) 설정

멀티 루트 워크스페이스를 사용하면 여러 프로젝트를 동시에 관리할 수 있습니다.

```json
// my-workspace.code-workspace
{
  "folders": [
    { "path": "./frontend" },
    { "path": "./backend" },
    { "path": "./shared" }
  ]
}
```

### 3. 확장 프로그램 조합

Claude Code와 함께 사용하면 좋은 VS Code 확장 프로그램:

| 확장 프로그램 | 용도 |
|-------------|------|
| **GitLens** | Git 히스토리와 blame 정보 |
| **Error Lens** | 에러를 인라인으로 표시 |
| **Todo Tree** | TODO 코멘트 관리 |
| **Prettier** | 코드 포맷팅 |
| **ESLint** | 코드 린팅 |

### 4. 멀티 모니터 활용

```
모니터 1: VS Code (에디터 + Claude Code 패널)
모니터 2: 브라우저 (문서, 디자인, API 테스트)
```

또는:

```
모니터 1: VS Code 에디터
모니터 2: 터미널 (Claude Code CLI + 개발 서버)
```

---

## 문제 해결

### VS Code 확장이 작동하지 않을 때

1. **Claude Code CLI 확인**
   ```bash
   claude --version
   # CLI가 설치되어 있고 인증이 완료되어 있어야 합니다
   ```

2. **확장 프로그램 재설치**
   - Extensions에서 Claude Code 제거 후 재설치
   - VS Code 재시작

3. **출력 패널 확인**
   - View → Output → 드롭다운에서 "Claude Code" 선택
   - 에러 메시지 확인

### JetBrains 플러그인이 작동하지 않을 때

1. **CLI 경로 확인**
   ```bash
   which claude
   # 이 경로를 플러그인 설정에 입력
   ```

2. **IDE 버전 호환성 확인**
   - 플러그인이 현재 IDE 버전을 지원하는지 확인

3. **로그 확인**
   - Help → Show Log in Explorer/Finder
   - 에러 메시지 확인

### 공통 문제

| 증상 | 해결 방법 |
|------|----------|
| Claude 응답 없음 | 인터넷 연결 확인, API 키 유효성 확인 |
| 파일 수정이 반영 안 됨 | 자동 저장 설정 확인, 파일 새로고침 |
| 확장이 느림 | Claude Code 버전 업데이트, IDE 재시작 |
| 인증 오류 | `claude` CLI에서 재인증 |

---

## 요약

| 항목 | 설명 |
|------|------|
| **VS Code** | 공식 확장 프로그램으로 IDE 내에서 사용 |
| **JetBrains** | 플러그인으로 IntelliJ, WebStorm 등에서 사용 |
| **핵심 장점** | 코드 선택 전달, 인라인 diff, 현재 파일 인식 |
| **CLI 병행** | IDE에서 시각적 작업, CLI에서 자동화 |
| **터미널 통합** | IDE 내장 터미널에서 CLI 직접 사용 가능 |
| **권장 설정** | 자동 저장, diff 에디터, 포맷팅 설정 |

IDE와 Claude Code를 함께 사용하면 개발 효율이 크게 향상됩니다. 처음에는 VS Code 확장 프로그램부터 설치하여 사용해보고, 자신에게 맞는 워크플로우를 만들어가세요.
