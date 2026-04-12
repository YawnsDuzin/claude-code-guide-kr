# 04. 인기 Claude Code 에이전트 레포지토리 가이드

> 커뮤니티가 만든 서브에이전트 / 스킬 / 플러그인 컬렉션은 수십 개에 이른다. 이 문서는 **무엇을 / 왜 / 어떻게 설치할지** 를 결정하기 위한 실전 참조 가이드다.

**마지막 검증**: 2026-04 (주요 레포 최신 상태 기준)

---

## 0. 먼저: 용어 5가지 정리

레포지토리를 고르기 전에 각 용어가 무엇을 말하는지 구분해야 합니다. 뭉쳐 쓰면 잘못된 레포를 설치하게 됩니다.

| 용어 | 무엇 | 어디 저장 | 예시 |
|------|-----|----------|------|
| **Subagent** | 특정 도메인 전문 보조 에이전트. 자체 컨텍스트로 실행 | `~/.claude/agents/` 또는 `.claude/agents/` | `backend-architect`, `security-auditor` |
| **Skill** | 동적으로 로드되는 지시/스크립트 묶음 (Agent Skills 스펙) | `~/.claude/skills/` 또는 프로젝트 `.claude/skills/` | `pdf`, `review-pr`, `commit` |
| **Slash Command** | `/xxx` 형태 사용자 호출 명령 | `.claude/commands/` | `/commit`, `/test` |
| **Hook** | 이벤트(예: PreToolUse, PostToolUse)에 따른 자동 실행 스크립트 | `settings.json`의 `hooks` 필드 | pre-commit 포맷, 알림 |
| **Plugin** | 위 4가지를 한 단위로 묶어 배포하는 마켓플레이스 단위 | `/plugin marketplace add ...` | `voltagent-lang`, `agents@wshobson` |

**핵심 구분**:
- Subagent는 **에이전트가 호출하는 보조 세션**
- Skill은 **필요할 때 로드되는 지시서**
- Command는 **사용자가 타이핑하는 단축**
- Hook은 **이벤트 기반 자동 실행**
- Plugin은 **위 4가지의 배포 포장재**

대부분의 "awesome-*" 레포는 이 중 하나 이상을 제공합니다.

---

## 1. 한 장 비교표

아래 8개 레포가 현재 커뮤니티에서 가장 활발합니다. 각각의 상세는 다음 섹션을 참고.

| # | 레포 | 주 내용 | 규모 | 설치 방식 | 라이선스 | 누가 쓰면 좋은가 |
|---|------|--------|------|----------|---------|-----------------|
| 1 | **wshobson/agents** | subagent + plugin + skill + command | 182 agents / 149 skills / 96 commands / 77 plugins / 16 orchestrator | `/plugin marketplace add` | MIT | 프로덕션 규모, 멀티 에이전트 오케스트레이션 필요 |
| 2 | **VoltAgent/awesome-claude-code-subagents** | subagent | 100+ (카테고리 9~10개) | 플러그인 마켓 / 설치 스크립트 / 수동 | (오픈) | 언어/인프라/보안 등 도메인 전문 에이전트가 필요한 풀스택 |
| 3 | **0xfurai/claude-code-subagents** | subagent | 100+ (16 카테고리) | 클론 → `~/.claude/agents/` | MIT | 일관된 포맷, 비용 의식적 (모델 자동 매핑) |
| 4 | **davepoon/buildwithclaude** | agent + command + hook + skill + plugin (메타) | 117 agents / 175 commands / 28 hooks / 26 skills / 50 plugins | 플러그인 마켓 + 웹 UI 검색 | MIT | 한 곳에서 다양한 자산을 검색해서 선택 |
| 5 | **lst97/claude-code-sub-agents** | subagent (풀스택 중심) | 33 + orchestrator | 클론 → `~/.claude/agents/` | 오픈 | 솔로 풀스택 (React/Next.js/Python/Go) |
| 6 | **SuperClaude-Org/SuperClaude_Framework** | framework + persona + command + agent | 16 specialist agents / 30 commands / 14 personas | `pip install SuperClaude` 등 | MIT | "가이드된 방법론"이 필요, 페르소나 기반 |
| 7 | **anthropics/skills** (공식) | skill | 문서/코드/디자인 스킬 | 플러그인 또는 복사 | Apache 2.0 (일부 source-available) | 공식 품질을 신뢰, 문서 조작 등 표준 작업 |
| 8 | **hesreallyhim/awesome-claude-code** | 메타 큐레이션 리스트 | 수백 개 링크 | - (레포가 자산이 아님) | - | "무엇이 있는지" 둘러보고 싶을 때 |

> **메타 리스트 주의**: 6번, 8번은 레포 자체를 설치하는 게 아닙니다. 링크 모음이라 읽기만 합니다. 1~5, 7번만 "설치" 대상입니다.

---

## 2. 레포별 상세

### 2-1. wshobson/agents — 프로덕션 규모 올인원

- **링크**: https://github.com/wshobson/agents
- **규모**: 182 에이전트 / 149 스킬 / 96 커맨드 / 77 플러그인 / 16 오케스트레이터
- **카테고리(24개 일부)**: Development / Languages / Infra & DevOps / Security / AI·ML / Docs / Quality & Testing / Database / Operations / Workflows / Payments / Blockchain / Gaming / Accessibility / ...
- **설치**:
  ```
  /plugin marketplace add wshobson/agents
  /plugin install <plugin-name>@claude-code-workflows
  ```
  플러그인 단위로 쪼개져 있어 **필요한 것만** 설치 가능.
- **라이선스**: MIT

**장점**
1. **규모 · 커버리지**: 거의 모든 스택/도메인 대응.
2. **멀티 에이전트 오케스트레이터 16개** — "전체 기능 개발", "보안 강화", "병렬 코드 리뷰 팀" 등 이미 설계된 워크플로우.
3. **플러그인 세분화 (평균 3.6 컴포넌트/플러그인)** — 토큰 절약, 필요한 것만 로드.
4. **프로덕션 지향 설계** — 관측성, 인시던트 대응, 컴플라이언스 포함.

**단점 / 주의**
1. **학습 곡선 큼** — 에이전트 182개 중 무엇을 언제 쓸지 파악에 시간 소요.
2. **과도한 설치 유혹** — "전부 깔기"는 컨텍스트 충돌·토큰 낭비 원인.
3. 일부 도메인(블록체인/게이밍)은 범용 프로젝트에서 불필요.

**적합한 경우**
- 팀/스타트업/중견, 프로덕션 서비스 운영
- 여러 스택 병용 (백엔드 + 프론트 + 인프라 + 보안)
- 멀티 에이전트 파이프라인을 **이미 설계된 대로** 쓰고 싶을 때

**피해야 할 경우**
- "Hello World" 수준의 학습용 프로젝트
- 엄격한 플러그인 화이트리스트가 필요한 환경

---

### 2-2. VoltAgent/awesome-claude-code-subagents — 도메인 특화 언어 지향

- **링크**: https://github.com/VoltAgent/awesome-claude-code-subagents
- **규모**: 100+ (문서에 따라 130+ 까지). 9~10개 카테고리.
- **카테고리**:
  - Core Development (API/front/back/full-stack) — 11
  - Language Specialists (TS/Python/Go/Rust/Java 등) — 28
  - Infrastructure (DevOps/K8s/Terraform/클라우드) — 16
  - Quality & Security (테스트/리뷰/펜테스트) — 15
  - Data & AI — 13
  - Developer Experience — 14
  - Specialized Domains (블록체인/IoT/게임/핀테크) — 12
  - Business & Product — 11
  - Meta & Orchestration — 13
  - Research & Analysis — 3
- **설치** (4가지):
  1. 플러그인 마켓: `claude plugin marketplace add VoltAgent/awesome-claude-code-subagents`
  2. 스크립트: `./install-agents.sh` (클론 후)
  3. curl 단발 설치
  4. 수동 복사 → `~/.claude/agents/`
- **라이선스**: 오픈(저장소에 LICENSE 존재)

**장점**
1. **언어/프레임워크 전문가 28명** — `typescript-pro`, `python-expert`, `rust-ownership-guru` 등 도메인 언어 수준의 세밀한 조언.
2. **설치 옵션 다양** — 팀 단위 일괄 설치부터 개인 선별 설치까지.
3. **VoltAgent 생태계**와 연결 — `awesome-agent-skills` (스킬), `voltagent-lang`, `voltagent-infra` 등 별도 컬렉션과 호환.

**단점**
1. 에이전트 간 **명명 규칙 일관성이 레포 내부에서도 조금씩 다름**.
2. 오케스트레이션 예시가 wshobson 대비 얕음.
3. "Research & Analysis" 등 일부 카테고리는 코드 작업과 거리가 있음.

**적합한 경우**
- **특정 언어/프레임워크**가 주력인 팀 (예: TypeScript 전담, Go 전담)
- 인프라·보안 전문 에이전트 스태프를 구성하고 싶을 때
- 이미 VoltAgent 생태계(예: Voltagent 프레임워크)를 쓰는 경우

---

### 2-3. 0xfurai/claude-code-subagents — 일관 포맷 · 비용 최적

- **링크**: https://github.com/0xfurai/claude-code-subagents
- **규모**: 100+ / 16 카테고리
- **카테고리**: 언어&프레임워크(23) / 웹&프론트(26) / 모바일&데스크톱(8) / DB&데이터(15) / ORM(5) / Infra&DevOps(9) / Services(7) / 메시징(10) / QA(10) / DS·ML(6) / 관측성(5) / 보안(3) / 빌드 툴(2) / 마이그레이션(3) / Job Queue(3) / 런타임·패키지(3)
- **설치**: 레포를 클론해 `~/.claude/agents/` 로 복사. 배치되면 자동 인식.
- **라이선스**: MIT

**장점**
1. **표준화된 마크다운 포맷**: 모든 에이전트가 동일 구조(Focus / Instructions / Quality Checklist / Output Guidelines)로 작성되어 **커스터마이즈가 쉽다**.
2. **모델 자동 매핑 (비용 최적화)**: 간단한 작업엔 저렴 모델, 복잡한 작업엔 고성능 모델을 사용하도록 에이전트 단위로 지정.
3. **자동 위임(auto-delegation)** + **명시 호출** 둘 다 지원.
4. 명시적 라이선스(MIT) — 사내 반입 결정이 쉽다.

**단점**
1. **플러그인 마켓 미지원** — 수동 복사만. 대규모 팀 배포에 번거로움.
2. 오케스트레이터 / 워크플로우가 없음. 역할별 에이전트만 제공.
3. 업데이트를 받으려면 직접 `git pull` 해야 함.

**적합한 경우**
- "내가 쓸 에이전트만 몇 개 골라 커스터마이즈"하고 싶은 개인
- 토큰 비용에 민감한 환경
- **플러그인 마켓을 사용하지 않는** 보수적 설정 환경 (사내 보안 정책 등)

---

### 2-4. davepoon/buildwithclaude — 통합 마켓플레이스 허브

- **링크**: https://github.com/davepoon/buildwithclaude (웹 UI: buildwithclaude.com)
- **규모**: 117 agents / 175 commands / 28 hooks / 26 skills / 50 번들 플러그인
- **카테고리**: 11 (agents), 22 (commands), 8 (hooks) 등
- **설치**:
  ```
  /plugin marketplace add davepoon/buildwithclaude
  /plugin install <plugin-name>@buildwithclaude
  ```
  또는 레포 수동 클론 후 복사.
- **라이선스**: MIT

**장점**
1. **웹 UI로 검색·필터·브라우징** — 플러그인 쇼핑몰 같은 경험.
2. **agent / command / hook / skill 전부** 를 한 마켓에서.
3. 외부 마켓플레이스(20k+ 커뮤니티 플러그인, 4,500+ MCP 서버, 1,100+ 마켓플레이스)도 인덱싱.
4. "One-click install command" 복사 지원 — 온보딩 문서에 붙여넣기 용이.

**단점**
1. 허브 성격이라 **개별 에이전트의 품질은 기여자 편차** 가 있음 — 쓰기 전에 각 에이전트 내용을 꼭 확인.
2. 명령/훅/스킬이 섞여 있어 **개념 혼동** 위험 (§0 용어 정리 필수).
3. wshobson 대비 멀티 에이전트 오케스트레이션이 약함.

**적합한 경우**
- 여러 소스에서 자산을 **한 번에 둘러보고** 고르고 싶을 때
- 팀 온보딩 문서에 "이 명령들 설치" 식으로 링크를 줄 때
- 단일 마켓플레이스로 **거버넌스** 를 두고 싶은 팀
