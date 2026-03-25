# Claude Code 가이드: 개요 및 빠른 시작

## Claude Code란?

Claude Code는 **Anthropic이 공식 개발한 에이전틱(agentic) 코딩 CLI 도구**입니다. 터미널에서 직접 실행되며, 여러분의 코드베이스를 이해하고, 파일을 편집하고, 명령어를 실행하며, 복잡한 프로그래밍 작업을 자율적으로 수행합니다.

기존의 코드 자동완성 도구와 달리, Claude Code는 **에이전트(agent)** 방식으로 동작합니다. 단순히 코드 한 줄을 제안하는 것이 아니라, 프로젝트 전체를 파악하고 여러 파일에 걸친 변경사항을 스스로 계획하고 실행합니다.

```bash
# 터미널에서 바로 대화하듯 코딩할 수 있습니다
$ claude
> 이 프로젝트의 구조를 설명해줘

# Claude가 프로젝트를 분석하고 상세히 설명합니다
```

---

## 주요 기능 요약

### 1. 에이전틱 코딩 (Agentic Coding)
- 자연어로 지시하면 Claude가 **스스로 계획을 세우고 코드를 작성**합니다
- 여러 단계에 걸친 복잡한 작업도 자율적으로 처리합니다
- 오류가 발생하면 스스로 디버깅하고 수정합니다

### 2. 멀티 파일 편집 (Multi-file Editing)
- 한 번의 요청으로 **여러 파일을 동시에 수정**할 수 있습니다
- 프로젝트 구조를 이해하고 관련 파일을 자동으로 찾아냅니다
- 리팩토링, 기능 추가, 버그 수정 시 영향을 받는 모든 파일을 업데이트합니다

### 3. Git 통합 (Git Integration)
- 커밋 메시지 자동 생성
- 브랜치 생성 및 관리
- PR(Pull Request) 작성 지원
- 코드 리뷰 및 변경사항 요약

### 4. 터미널 접근 (Terminal Access)
- 셸 명령어를 직접 실행할 수 있습니다
- 테스트 실행, 빌드, 린팅 등 개발 워크플로우를 자동화합니다
- 명령어 실행 결과를 분석하고 다음 단계를 결정합니다

### 5. 코드베이스 이해 (Codebase Understanding)
- 프로젝트 전체 구조를 빠르게 파악합니다
- 함수 간의 의존 관계, 데이터 흐름을 분석합니다
- 새로운 프로젝트에 합류할 때 온보딩 도우미 역할을 합니다

---

## 빠른 시작: 3단계로 시작하기

### Step 1: 설치

```bash
# Node.js 18 이상이 설치되어 있어야 합니다
npm install -g @anthropic-ai/claude-code
```

### Step 2: 인증

```bash
# 프로젝트 디렉토리에서 Claude Code 실행
cd your-project
claude

# 처음 실행 시 인증 과정이 자동으로 시작됩니다
# - Anthropic API 키 입력, 또는
# - Claude 구독(Max, Team, Enterprise)으로 OAuth 로그인
```

### Step 3: 코딩 시작

```bash
# 자연어로 원하는 작업을 요청합니다
> 이 프로젝트의 구조를 설명해줘
> login 함수에 입력값 검증을 추가해줘
> 최근 커밋의 변경사항을 요약하고 커밋 메시지를 작성해줘
```

> **팁**: 처음에는 간단한 질문(프로젝트 구조 설명, 함수 역할 설명 등)부터 시작하세요. Claude Code의 동작 방식에 익숙해진 후 복잡한 작업을 요청하면 더 효과적입니다.

---

## 이 가이드의 대상

이 가이드는 다음과 같은 분들을 위해 작성되었습니다:

| 대상 | 이 가이드에서 얻을 수 있는 것 |
|------|-------------------------------|
| **AI 코딩 도구 입문자** | Claude Code의 기본 개념부터 실전 활용까지 단계별 학습 |
| **기존 IDE AI 도구 사용자** | CLI 기반 에이전틱 코딩의 장점과 활용법 |
| **팀 리더 / 매니저** | Claude Code 도입 시 고려사항, 팀 워크플로우 설계 |
| **숙련된 개발자** | 고급 최적화 기법, 자동화 워크플로우 구축 |

### 사전 지식

- **필수**: 터미널(명령줄) 기본 사용법, Git 기초
- **권장**: JavaScript/Python 등 프로그래밍 언어 경험
- **선택**: CI/CD, Docker 등 DevOps 기초 지식

---

## 가이드 구성 안내

이 가이드는 점진적으로 난이도가 올라가는 구조로 설계되었습니다:

```
claude-code-guide/
├── 00-overview.md              ← 현재 문서 (개요 및 빠른 시작)
├── 01-installation/            ← 설치 및 초기 설정
│   ├── requirements.md         시스템 요구사항
│   ├── install-guide.md        OS별 설치 방법
│   └── first-run.md            첫 실행 및 초기 설정
├── 02-core-concepts/           ← 핵심 개념
│   ├── how-claude-code-works.md
│   ├── context-and-memory.md
│   └── permission-model.md
├── 03-features/                ← 주요 기능 상세
│   ├── code-editing.md
│   ├── git-integration.md
│   ├── shell-commands.md
│   └── slash-commands.md
├── 04-workflows/               ← 실전 워크플로우
│   ├── debugging.md
│   ├── refactoring.md
│   ├── testing.md
│   └── code-review.md
├── 05-tech-stacks/             ← 기술 스택별 활용
│   ├── frontend.md
│   ├── backend.md
│   └── data-science.md
├── 06-comparison/              ← 다른 도구와의 비교
├── 07-optimization/            ← 고급 최적화
└── 99-appendix/                ← 부록 (명령어 사전, FAQ 등)
```

### 추천 학습 순서

1. **입문자**: `00` → `01` → `02` → `03` 순서대로 읽기
2. **경험자**: `00` 훑어보기 → `03` 또는 `04`부터 시작
3. **특정 주제 탐색**: 목차에서 필요한 섹션으로 바로 이동

---

## 핵심 용어 빠른 참조

| 용어 | 설명 |
|------|------|
| **에이전틱 코딩 (Agentic Coding)** | AI가 자율적으로 판단하고 여러 단계를 거쳐 코딩 작업을 수행하는 방식. 단순 자동완성과 다름 |
| **CLI (Command Line Interface)** | 터미널/명령줄에서 텍스트 명령어로 상호작용하는 인터페이스 |
| **프롬프트 (Prompt)** | Claude에게 전달하는 지시 또는 요청 텍스트 |
| **컨텍스트 (Context)** | Claude가 현재 대화에서 참조할 수 있는 정보의 범위. 파일 내용, 이전 대화 등 포함 |
| **컨텍스트 윈도우 (Context Window)** | Claude가 한 번에 처리할 수 있는 최대 정보량. 토큰 수로 측정됨 |
| **CLAUDE.md** | 프로젝트 루트에 놓는 설정 파일. Claude Code에게 프로젝트 규칙, 코딩 스타일 등을 알려줌 |
| **슬래시 명령어 (Slash Command)** | `/help`, `/clear` 등 `/`로 시작하는 Claude Code 내장 명령어 |
| **권한 모드 (Permission Mode)** | Claude Code가 파일 수정, 명령어 실행 시 사용자 승인을 요구하는 수준 설정 |
| **토큰 (Token)** | AI 모델이 텍스트를 처리하는 최소 단위. 대략 한국어 1글자 ≈ 2-3 토큰, 영어 1단어 ≈ 1 토큰 |
| **OAuth** | 외부 서비스(여기서는 Anthropic)를 통한 안전한 인증 방식 |
| **MCP (Model Context Protocol)** | 외부 도구 및 데이터 소스를 Claude에 연결하는 프로토콜 |
| **멀티턴 (Multi-turn)** | 여러 번의 대화를 주고받으며 작업을 진행하는 방식 |

---

## 다음 단계

설치를 시작할 준비가 되셨나요?

→ [시스템 요구사항 확인하기](./01-installation/requirements.md)
→ [설치 가이드](./01-installation/install-guide.md)
→ [첫 실행 및 초기 설정](./01-installation/first-run.md)
