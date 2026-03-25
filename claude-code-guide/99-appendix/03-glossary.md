# 부록 C: 용어 사전

Claude Code와 AI 에이전트 코딩에서 사용되는 주요 용어를 한국어로 정리한 사전입니다.

---

## A

### Agent (에이전트)
사용자의 목표를 달성하기 위해 스스로 판단하고, 도구를 호출하며, 반복적으로 작업을 수행하는 AI 시스템. Claude Code는 코드 읽기, 수정, 명령어 실행 등의 도구를 자율적으로 사용하는 에이전트입니다.

### Agentic Coding (에이전틱 코딩)
AI 에이전트가 코드를 직접 작성, 수정, 테스트, 디버깅하는 개발 방식. 단순한 코드 자동완성을 넘어 에이전트가 전체 개발 워크플로우를 자율적으로 수행합니다.

### API Key (API 키)
Anthropic API에 접근하기 위한 인증 문자열. `sk-ant-`로 시작하며, 환경 변수 `ANTHROPIC_API_KEY`에 설정합니다. 절대 외부에 노출하지 않도록 관리해야 합니다.

### Append System Prompt (시스템 프롬프트 추가)
기존 시스템 프롬프트 뒤에 추가 지시사항을 덧붙이는 기능. `--append-system-prompt` 플래그로 사용합니다.

### Auto-Compact (자동 컴팩션)
컨텍스트 윈도우가 한도에 근접하면 자동으로 대화를 요약하는 기능. `claude config set autoCompact true`로 활성화합니다.

---

## B

### Bash Tool (Bash 도구)
Claude Code가 터미널 명령어를 실행할 수 있게 해주는 내장 도구. 빌드, 테스트, Git 작업 등 셸 명령어를 수행합니다.

### Bedrock
AWS에서 제공하는 관리형 AI 서비스 플랫폼. `CLAUDE_CODE_USE_BEDROCK=1` 설정으로 AWS Bedrock을 통해 Claude를 사용할 수 있습니다.

---

## C

### CLAUDE.md (클로드 메모리 파일)
프로젝트의 규칙, 코딩 스타일, 아키텍처 정보 등을 기록하는 마크다운 파일. Claude Code가 세션 시작 시 자동으로 읽어들여 컨텍스트로 활용합니다. 프로젝트 루트, 홈 디렉터리 등 여러 위치에 배치 가능합니다.

### CLI (Command Line Interface, 명령줄 인터페이스)
텍스트 기반으로 컴퓨터와 상호작용하는 인터페이스. Claude Code는 CLI 도구로, 터미널에서 `claude` 명령어로 실행합니다.

### Compaction (컴팩션)
긴 대화 내역을 요약하여 컨텍스트 윈도우 사용량을 줄이는 과정. `/compact` 명령어로 수동 실행하거나 자동 컴팩션을 활성화할 수 있습니다. 요약 시 핵심 정보를 보존하면서 중복이나 불필요한 내용을 제거합니다.

### Context Window (컨텍스트 윈도우)
모델이 한 번에 처리할 수 있는 최대 텍스트 양 (토큰 단위). Claude의 컨텍스트 윈도우는 200K 토큰이며, 대화가 길어지면 이 한도에 도달하여 컴팩션이 필요합니다.

### Cost (비용)
API 사용에 따라 발생하는 요금. 입력 토큰과 출력 토큰 수에 따라 계산됩니다. `/cost` 명령어로 현재 세션의 비용을 확인할 수 있습니다.

---

## D

### Dangerously Skip Permissions (권한 확인 건너뛰기)
모든 도구 실행에 대한 권한 확인을 건너뛰는 위험한 플래그. `--dangerously-skip-permissions`로 사용하며, CI/CD 환경에서만 제한적으로 사용해야 합니다.

### Doctor (닥터)
Claude Code의 설치 상태, 인증, 네트워크 연결 등을 종합 진단하는 명령어. `claude doctor`로 실행합니다.

---

## E

### Edit Tool (편집 도구)
Claude Code가 파일의 특정 부분을 찾아 교체하는 내장 도구. 파일 전체를 다시 쓰지 않고 정확한 위치만 수정합니다.

### Environment Variable (환경 변수)
운영체제에서 프로그램에 전달하는 설정 값. Claude Code는 `ANTHROPIC_API_KEY`, `CLAUDE_CODE_USE_BEDROCK` 등의 환경 변수를 사용합니다.

### Exit Code (종료 코드)
프로그램 종료 시 반환하는 숫자 값. `0`은 성공, `0`이 아닌 값은 오류를 의미합니다. 스크립트에서 Claude Code의 실행 결과를 판별하는 데 사용합니다.

### Extended Thinking (확장 사고)
Claude가 복잡한 문제에 대해 단계적으로 깊이 사고하는 기능. 더 정확한 답변을 생성하지만 응답 시간과 토큰 사용량이 증가합니다.

---

## F

### Flag (플래그)
명령어에 추가하는 옵션. `-p` (비대화형), `--model` (모델 지정), `--output-format` (출력 형식) 등이 있습니다.

---

## G

### Git Worktree (Git 워크트리)
하나의 Git 저장소에서 여러 브랜치를 동시에 체크아웃하여 작업할 수 있는 기능. 여러 Claude Code 인스턴스가 각각 다른 작업을 병렬로 수행할 때 유용합니다.

### Glob (글롭)
파일 경로 패턴 매칭 문법. `*.js`(모든 JS 파일), `**/*.ts`(모든 하위 디렉터리의 TS 파일) 등의 패턴을 사용합니다.

---

## H

### Headless Mode (헤드리스 모드)
사용자 인터페이스 없이 프로그래밍 방식으로 Claude Code를 실행하는 모드. CI/CD 파이프라인이나 자동화 스크립트에서 사용합니다.

### Hook (훅)
Claude Code의 도구 실행 전후에 자동으로 실행되는 사용자 정의 명령어. 파일 저장 후 린트 실행, 명령어 실행 전 검증 등의 워크플로우를 자동화합니다.

---

## I

### Input Token (입력 토큰)
API에 전송하는 프롬프트(대화 내역, 시스템 프롬프트, 파일 내용 등)의 토큰 수. 비용 계산의 한 요소입니다.

### Interactive Mode (대화형 모드)
`claude` 명령어로 시작하는 기본 모드. 사용자가 실시간으로 메시지를 입력하고 응답을 받는 REPL 환경입니다.

---

## J

### JSON Output (JSON 출력)
`--output-format json` 플래그로 Claude Code의 응답을 JSON 형식으로 받는 기능. 프로그래밍 방식으로 결과를 파싱할 때 유용합니다.

---

## L

### LLM (Large Language Model, 대규모 언어 모델)
방대한 텍스트 데이터로 학습한 AI 모델. Claude는 Anthropic이 개발한 LLM이며, Claude Code의 핵심 엔진입니다.

---

## M

### Max Turns (최대 턴)
에이전트 루프에서 허용하는 최대 반복 횟수. `--max-turns` 플래그로 제한하여 무한 루프를 방지하고 비용을 통제합니다.

### MCP (Model Context Protocol, 모델 컨텍스트 프로토콜)
LLM이 외부 도구, 데이터 소스, 서비스와 표준화된 방식으로 통신하는 개방형 프로토콜. Claude Code에서 `claude mcp add`로 MCP 서버를 추가하여 GitHub, 데이터베이스, 검색 엔진 등의 외부 기능을 사용할 수 있습니다.

### MCP Server (MCP 서버)
MCP 프로토콜을 구현하여 특정 기능을 제공하는 서버 프로세스. stdio 또는 SSE 전송 방식으로 Claude Code와 통신합니다.

### Memory (메모리)
Claude Code가 세션 간에 유지하는 정보. CLAUDE.md 파일에 저장되며, `/memory` 명령어로 편집합니다.

### Model (모델)
Claude의 특정 버전. Opus(최고 성능), Sonnet(균형), Haiku(빠른 응답) 등의 계열이 있으며, `--model` 플래그로 선택합니다.

---

## N

### Non-Interactive Mode (비대화형 모드)
`claude -p` 명령어로 실행하는 모드. 단일 프롬프트를 처리하고 결과를 출력한 뒤 즉시 종료합니다. 스크립트와 파이프라인에 적합합니다.

---

## O

### OAuth (Open Authorization)
API 키 없이 브라우저 기반 로그인으로 인증하는 방식. `claude login` 명령어로 OAuth 인증을 수행합니다.

### Output Format (출력 형식)
Claude Code의 응답 형식. `text`(일반 텍스트), `json`(구조화된 JSON), `stream-json`(실시간 스트리밍 JSON) 중 선택합니다.

### Output Token (출력 토큰)
모델이 생성하는 응답의 토큰 수. 일반적으로 입력 토큰보다 비용이 높습니다.

---

## P

### Permission (권한)
Claude Code가 파일 수정, 명령어 실행 등의 작업을 수행하기 위해 필요한 승인. 보안을 위해 위험한 작업은 사용자의 명시적 허용이 필요합니다.

### Permission Mode (권한 모드)
도구 실행에 대한 권한 처리 방식. `default`(확인 요청), `plan`(읽기 전용), `bypassPermissions`(모두 허용) 등이 있습니다.

### Pipe (파이프)
유닉스 셸에서 한 명령어의 출력을 다른 명령어의 입력으로 전달하는 기능. `cat file | claude -p "분석해줘"`처럼 사용합니다.

### Prompt (프롬프트)
AI 모델에 전달하는 입력 텍스트. 사용자의 질문이나 지시사항을 포함합니다.

### Prompt Caching (프롬프트 캐싱)
동일한 프롬프트 프리픽스를 재사용할 때 비용을 절감하는 기능. 시스템 프롬프트, CLAUDE.md 등 반복되는 부분이 자동으로 캐시됩니다.

---

## R

### Rate Limit (속도 제한)
API 사용량을 제어하기 위한 제한. 분당 요청 수(RPM), 분당 토큰 수(TPM), 일일 한도 등이 있습니다. 한도 초과 시 `429` 오류가 발생합니다.

### Read Tool (읽기 도구)
Claude Code가 파일 내용을 읽는 내장 도구. 특정 줄 범위를 지정하여 읽을 수도 있습니다.

### REPL (Read-Eval-Print Loop)
입력을 읽고(Read), 처리하고(Eval), 결과를 출력하고(Print), 반복하는(Loop) 대화형 환경. Claude Code의 대화형 모드가 REPL 방식입니다.

### Resume (재개)
이전에 중단된 특정 세션을 세션 ID를 지정하여 다시 시작하는 기능. `claude -r <session_id>`로 사용합니다.

---

## S

### Session (세션)
Claude Code와의 하나의 대화 단위. 고유한 세션 ID가 부여되며, 대화 내역이 유지됩니다.

### Skill (스킬)
Claude Code에서 슬래시 명령어로 호출할 수 있는 특수 기능 모듈. 커밋 생성, PR 리뷰 등 특정 워크플로우를 자동화합니다.

### Slash Command (슬래시 명령어)
대화형 모드에서 `/`로 시작하는 특수 명령어. `/help`, `/compact`, `/clear`, `/cost` 등이 있습니다.

### Streaming (스트리밍)
모델의 응답을 완성될 때까지 기다리지 않고 생성되는 대로 실시간으로 표시하는 방식. 사용자가 응답을 빠르게 확인할 수 있습니다.

### Subagent (서브에이전트)
메인 에이전트가 복잡한 작업을 처리하기 위해 생성하는 하위 에이전트. 독립적인 컨텍스트에서 특정 하위 작업을 수행하고 결과를 메인 에이전트에 반환합니다. 컨텍스트 관리와 병렬 처리에 효과적입니다.

### System Prompt (시스템 프롬프트)
모델의 행동 방식을 지정하는 초기 지시사항. `--system-prompt` 플래그로 비대화형 모드에서 사용자 정의 시스템 프롬프트를 지정할 수 있습니다.

---

## T

### Token (토큰)
LLM이 텍스트를 처리하는 기본 단위. 대략 영문 기준 1 토큰은 약 4글자(또는 0.75단어), 한국어는 1글자당 약 2~3 토큰에 해당합니다. 비용과 컨텍스트 윈도우 크기 모두 토큰 단위로 계산됩니다.

### Tool (도구)
Claude Code가 외부 환경과 상호작용하기 위해 사용하는 기능. Read(파일 읽기), Edit(파일 편집), Write(파일 쓰기), Bash(명령어 실행), Glob(파일 검색), Grep(내용 검색) 등의 내장 도구와 MCP로 추가된 외부 도구가 있습니다.

### Tool Use (도구 사용)
Claude가 응답 생성 중 필요에 따라 도구를 호출하는 동작. 에이전틱 코딩의 핵심 메커니즘입니다.

### Turn (턴)
에이전트 루프에서 사용자 입력 → 모델 응답 → 도구 실행의 한 사이클. `--max-turns`로 최대 턴 수를 제한할 수 있습니다.

---

## V

### Verbose Mode (상세 모드)
`--verbose` 플래그로 활성화하는 디버깅 모드. API 요청, 도구 호출 등의 상세 로그를 출력합니다.

### Vertex AI
Google Cloud에서 제공하는 AI 플랫폼. `CLAUDE_CODE_USE_VERTEX=1` 설정으로 Vertex AI를 통해 Claude를 사용할 수 있습니다.

---

## W

### Worktree (워크트리)
→ Git Worktree 참조. 하나의 저장소에서 여러 작업 디렉터리를 생성하여 병렬 작업을 가능하게 합니다. 각 워크트리에서 독립적인 Claude Code 세션을 실행할 수 있습니다.

### Write Tool (쓰기 도구)
Claude Code가 새 파일을 생성하거나 기존 파일을 완전히 덮어쓰는 내장 도구. 기존 파일의 부분 수정에는 Edit 도구가 더 적합합니다.

---

## 기호 및 숫자

### 200K Context
Claude 모델의 최대 컨텍스트 윈도우 크기인 200,000 토큰. 대략 15만 단어 또는 500페이지 분량의 텍스트에 해당합니다.

### 429 Error
HTTP 상태 코드로 "Too Many Requests"를 의미. Rate Limit 초과 시 발생하며, 잠시 후 재시도하면 해결됩니다.

---

## 관련 용어 비교

### Agent vs Tool
- **Agent**: 자율적으로 판단하고 도구를 조합하여 작업을 수행하는 주체
- **Tool**: Agent가 사용하는 개별 기능 (파일 읽기, 명령어 실행 등)

### Session vs Turn
- **Session**: 하나의 대화 전체 (시작부터 종료까지)
- **Turn**: 대화 내 하나의 요청-응답 사이클

### Compaction vs Clear
- **Compaction** (`/compact`): 대화를 요약하여 유지 (정보 보존)
- **Clear** (`/clear`): 대화 내역 완전 초기화 (정보 삭제)

### Input Token vs Output Token
- **Input Token**: 사용자가 모델에 보내는 텍스트의 토큰 (저렴)
- **Output Token**: 모델이 생성하는 응답의 토큰 (상대적으로 비쌈)

### Interactive vs Non-Interactive
- **Interactive** (`claude`): 실시간 대화, 수동 작업에 적합
- **Non-Interactive** (`claude -p`): 자동화, 스크립트, CI/CD에 적합
