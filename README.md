# Claude Code 완벽 가이드 (한국어)

Claude Code의 설치/개념/기능/워크플로우/최적화 + **실전 바이브 코딩 플레이북**까지, 한국어로 작성된 포괄적인 가이드입니다.

> 모든 문서는 [`claude-code-guide/`](./claude-code-guide/) 디렉토리에 있습니다.

이 가이드는 두 가지 사용 방식을 모두 지원합니다.

| 사용 방식 | 보는 곳 |
|---------|--------|
| "이 기능이 뭔지" 알고 싶다 | 01~07 (레퍼런스) |
| "내일 출근해서 뭐부터 할지" 알고 싶다 | **08-바이브코딩** (플레이북, 복붙용 자산) |

---

## 목차

### [00. 개요 및 빠른 시작](./claude-code-guide/00-개요(overview).md)
Claude Code 소개, 주요 기능 요약, 3단계 빠른 시작 가이드

### 01. 설치
- [시스템 요구사항](./claude-code-guide/01-설치(installation)/01-시스템요구사항(requirements).md) - OS, Node.js, 하드웨어, 네트워크, 인증
- [OS별 설치 가이드](./claude-code-guide/01-설치(installation)/02-설치가이드(install-guide).md) - macOS, Linux, Windows 설치 방법
- [첫 실행 및 초기 설정](./claude-code-guide/01-설치(installation)/03-첫실행(first-run).md) - 인증, 기본 설정, 첫 대화

### 02. 핵심 개념
- [대화와 세션](./claude-code-guide/02-핵심개념(core-concepts)/01-대화와세션(conversation).md) - 대화 흐름, 세션 관리, 컨텍스트
- [도구 시스템](./claude-code-guide/02-핵심개념(core-concepts)/02-도구시스템(tools).md) - 파일 편집, Bash 실행, 검색 도구
- [권한 시스템](./claude-code-guide/02-핵심개념(core-concepts)/03-권한시스템(permissions).md) - 권한 모드, 허용 목록, 보안 설정
- [컨텍스트 관리](./claude-code-guide/02-핵심개념(core-concepts)/04-컨텍스트관리(context-management).md) - 토큰 관리, 컴팩션, 최적화
- [시스템 프롬프트](./claude-code-guide/02-핵심개념(core-concepts)/05-시스템프롬프트(system-prompt).md) - 시스템 프롬프트 구조, 커스터마이징

### 03. 주요 기능
- [메모리 (CLAUDE.md)](./claude-code-guide/03-주요기능(features)/01-메모리(memory).md) - 프로젝트 설정, 계층 구조, 작성법
- [스킬 (Skills)](./claude-code-guide/03-주요기능(features)/02-스킬(skills).md) - 슬래시 명령어, 커스텀 스킬
- [훅 (Hooks)](./claude-code-guide/03-주요기능(features)/03-훅(hooks).md) - 이벤트 기반 자동화, 커스텀 훅
- [MCP 서버](./claude-code-guide/03-주요기능(features)/04-MCP서버(mcp-servers).md) - 외부 도구 연동, MCP 설정
- [서브에이전트](./claude-code-guide/03-주요기능(features)/05-서브에이전트(subagents).md) - 병렬 처리, 멀티 에이전트
- [IDE 통합](./claude-code-guide/03-주요기능(features)/06-IDE통합(ide-integration).md) - VS Code, JetBrains 연동
- [Web & Desktop](./claude-code-guide/03-주요기능(features)/07-웹데스크톱(web-desktop).md) - Web, Desktop 앱, VS Code 확장 사용법

### 04. 워크플로우
**개념 (Claude Code의 워크플로우 기초)**
- [코드 분석](./claude-code-guide/04-워크플로우(workflows)/01-코드분석(analysis).md) - 코드베이스 이해, 구조 분석
- [코딩](./claude-code-guide/04-워크플로우(workflows)/02-코딩(coding).md) - 기능 개발, 코드 생성
- [디버깅](./claude-code-guide/04-워크플로우(workflows)/03-디버깅(debugging).md) - 버그 추적, 오류 해결
- [리팩토링](./claude-code-guide/04-워크플로우(workflows)/04-리팩토링(refactoring).md) - 코드 개선, 구조 변경
- [Git 워크플로우](./claude-code-guide/04-워크플로우(workflows)/05-Git워크플로우(git-workflow).md) - 커밋, PR, 코드 리뷰
- [Git 기본사용법](./claude-code-guide/04-워크플로우(workflows)/06-Git기본사용법(git-basics).md) - Git 기초, 명령어, 상황별 Git Flow

**실전 플레이북 (단계 체크리스트 + Mermaid 흐름도)**
- [기능 개발 흐름](./claude-code-guide/04-워크플로우(workflows)/07-기능개발흐름(feature-flow).md) - 8단계, 작업 크기별 분할
- [버그 수정 흐름](./claude-code-guide/04-워크플로우(workflows)/08-버그수정흐름(bugfix-flow).md) - 재현→원인→최소 패치→회귀 테스트
- [리팩토링 흐름](./claude-code-guide/04-워크플로우(workflows)/09-리팩토링흐름(refactoring-flow).md) - 보존 규칙 + 단계 분할
- [스키마 변경 흐름](./claude-code-guide/04-워크플로우(workflows)/10-스키마변경흐름(schema-flow).md) - Expand-Contract 3단계
- [UI 구현 흐름](./claude-code-guide/04-워크플로우(workflows)/11-UI구현흐름(ui-flow).md) - 텍스트 와이어프레임 우선

### 05. 기술 스택별 활용
- [프론트엔드](./claude-code-guide/05-기술스택(tech-stacks)/01-프론트엔드(frontend).md) - React, Vue, Angular, Next.js
- [백엔드](./claude-code-guide/05-기술스택(tech-stacks)/02-백엔드(backend).md) - Node.js, Python, Java, Go
- [모바일](./claude-code-guide/05-기술스택(tech-stacks)/03-모바일(mobile).md) - React Native, Flutter, Swift, Kotlin
- [인프라](./claude-code-guide/05-기술스택(tech-stacks)/04-인프라(infra).md) - Docker, Kubernetes, Terraform, CI/CD

### 06. AI 코딩 도구 비교
- [vs GitHub Copilot](./claude-code-guide/06-비교(comparison)/01-GitHub-Copilot비교(vs-github-copilot).md)
- [vs Cursor](./claude-code-guide/06-비교(comparison)/02-Cursor비교(vs-cursor).md)
- [vs Windsurf](./claude-code-guide/06-비교(comparison)/03-Windsurf비교(vs-windsurf).md)
- [도구 선택 가이드](./claude-code-guide/06-비교(comparison)/04-선택가이드(selection-guide).md)

### 07. 최적화
- [프롬프트 엔지니어링](./claude-code-guide/07-최적화(optimization)/01-프롬프트엔지니어링(prompt-engineering).md) - 효과적인 지시 방법
- [CLAUDE.md 작성법](./claude-code-guide/07-최적화(optimization)/02-CLAUDE-MD작성법(claude-md).md) - 프로젝트 설정 최적화
- [성능 최적화](./claude-code-guide/07-최적화(optimization)/03-성능최적화(performance).md) - 속도, 정확도 개선
- [비용 관리](./claude-code-guide/07-최적화(optimization)/04-비용관리(cost-management).md) - 토큰 절약, 비용 절감
- [CLAUDE.md 템플릿](./claude-code-guide/07-최적화(optimization)/05-CLAUDE-MD템플릿(claude-md-templates).md) - React, Python, Java 등 프로젝트별 템플릿
- [문서 종류 분류](./claude-code-guide/07-최적화(optimization)/06-문서종류분류(document-types).md) - 필수 4종 / 권장 3종 / 불필요 5종
- [에이전트 친화 포맷](./claude-code-guide/07-최적화(optimization)/07-에이전트친화포맷(agent-friendly-formats).md) - Markdown + Mermaid 원칙 + 문서 종류별 포맷 매칭표

### [08. 바이브 코딩 (Vibe Coding) — 실전 자산](./claude-code-guide/08-바이브코딩(vibe-coding)/README.md)
"문서/프롬프트/템플릿을 복붙해서 바로 쓰는" 실행 키트입니다. 01~07이 이론/레퍼런스라면, 08은 손에 쥐는 자산입니다.

- [00. 개요](./claude-code-guide/08-바이브코딩(vibe-coding)/00-개요(overview).md) - 바이브 코딩 등식 + 8단계 공통 루프
- [01. 빠른 시작](./claude-code-guide/08-바이브코딩(vibe-coding)/01-빠른시작(quickstart).md) - 5단계 초기화 (CLAUDE.md → PRD → arch → ERD → dev)
- [02. 프롬프트 템플릿](./claude-code-guide/08-바이브코딩(vibe-coding)/02-프롬프트템플릿(prompts)/) - 범용 + 7가지 작업 유형별 재사용 템플릿
- [03. 문서 템플릿](./claude-code-guide/08-바이브코딩(vibe-coding)/03-문서템플릿(templates)/) - CLAUDE.md / PRD / architecture / erd / feature-spec
- [04. 에이전트 협업](./claude-code-guide/08-바이브코딩(vibe-coding)/04-에이전트협업(agents)/) - 멀티 에이전트 패턴, 4-role 협업, 크로스 에이전트 핸드오프
- [05. 엔드투엔드 예제](./claude-code-guide/08-바이브코딩(vibe-coding)/05-예제(examples)/) - 기능 개발 + 멀티 에이전트 파이프라인 실사례

→ 시작하려면 **[08-바이브코딩/01-빠른시작](./claude-code-guide/08-바이브코딩(vibe-coding)/01-빠른시작(quickstart).md)** 5분 가이드.

### 99. 부록
- [CLI 레퍼런스](./claude-code-guide/99-부록(appendix)/01-CLI레퍼런스(cli-reference).md) - 명령어, 플래그, 환경변수 전체 목록
- [문제 해결](./claude-code-guide/99-부록(appendix)/02-문제해결(troubleshooting).md) - 자주 발생하는 오류와 해결법
- [용어집](./claude-code-guide/99-부록(appendix)/03-용어집(glossary).md) - Claude Code 관련 용어 정리
- [FAQ](./claude-code-guide/99-부록(appendix)/04-FAQ(faq).md) - 자주 묻는 질문과 답변
- [팁 30선](./claude-code-guide/99-부록(appendix)/05-팁모음(tips).md) - 알아두면 좋은 실전 팁 모음

---

## Claude Code 구독 요구사항

Claude Free/Pro 구독으로도 Claude Code를 사용할 수 있습니다.

| 접근 방식 | Free | Pro | Max |
|-----------|:----:|:---:|:---:|
| Terminal CLI | ✅ | ✅ | ✅ |
| VS Code 확장 | ✅ | ✅ | ✅ |
| Desktop 앱 | ❌ | ✅ | ✅ |
| Web (claude.ai/code) | ❌ | ✅ | ✅ |

### 핵심 포인트

1. **무료 사용자**: Terminal CLI와 VS Code 확장에서 사용 가능
2. **Pro 구독자** ($20/월): 모든 기능 사용 가능 (Desktop, Web 포함)
3. **API 키 방식**: Claude 구독과 별개로 API 키로도 사용 가능 (이 경우 토큰당 비용 청구, 평균 $100-200/월)

> Claude Pro 구독이 있으면 별도 API 비용 없이 Claude Code를 사용할 수 있습니다.

---

## 빠른 시작

```bash
# 1. 설치
npm install -g @anthropic-ai/claude-code

# 2. 프로젝트 디렉토리로 이동
cd your-project

# 3. Claude Code 실행
claude
```

---

## 대상 독자

- Claude Code를 처음 사용하는 개발자
- AI 코딩 도구를 비교하고 선택하려는 개발자
- Claude Code를 더 효율적으로 활용하고 싶은 개발자
- 한국어로 된 체계적인 가이드가 필요한 개발자

---

## 기여하기

오류 수정, 내용 추가, 번역 개선 등 모든 기여를 환영합니다. Issue나 Pull Request를 통해 참여해주세요.

---

## 라이선스

이 가이드는 자유롭게 참고하고 활용할 수 있습니다.
