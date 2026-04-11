# 04. 에이전트 협업

> 혼자서도 "여러 에이전트를 역할별로 나눠" 팀처럼 굴릴 수 있다. 이 폴더는 그 방법을 다룬다.

## 왜 멀티 에이전트인가

단일 에이전트 한 세션이 할 수 있는 일에는 한계가 있습니다.

- **컨텍스트 오염**: 한 에이전트가 탐색/구현/리뷰/테스트를 다 하면 역할 전환마다 이전 실수에 묶임
- **토큰 낭비**: 큰 세션은 반복 컴팩션
- **교차 검증 불가**: 자기 코드를 자기가 리뷰하면 같은 사각지대

역할별 에이전트 분리 → 각 역할이 최소 문맥만 가짐 → 교차 검증 가능.

---

## 파일 목록

| 파일 | 내용 |
|------|------|
| [01-멀티에이전트패턴(multi-agent-patterns).md](./01-멀티에이전트패턴(multi-agent-patterns).md) | Orchestrator-Worker / Pipeline / Parallel / Debate 4가지 패턴 |
| [02-역할기반협업(role-based).md](./02-역할기반협업(role-based).md) | Planner / Coder / Reviewer / Tester 역할 정의 + 프롬프트 |
| [03-크로스에이전트핸드오프(cross-agent-handoff).md](./03-크로스에이전트핸드오프(cross-agent-handoff).md) | Claude Code ↔ Codex ↔ Cursor ↔ 사람 간 컨텍스트 전달 프로토콜 |

## 시작 경로

1. 혼자 작은 작업: 단일 에이전트 + [02-프롬프트](../02-프롬프트(prompts)/)의 템플릿
2. 3~5파일 작업: [01-멀티에이전트패턴](./01-멀티에이전트패턴(multi-agent-patterns).md) §Pipeline
3. 낯선 코드베이스 + 고위험 변경: [02-역할기반협업](./02-역할기반협업(role-based).md)의 4-role
4. 여러 도구 병용: [03-크로스에이전트핸드오프](./03-크로스에이전트핸드오프(cross-agent-handoff).md)의 핸드오프 프로토콜

## 관련 기능

- Claude Code의 서브에이전트: `claude-code-guide/03-주요기능/05-서브에이전트(subagents).md`
- 이 폴더는 **개념/실전**, 저 문서는 **Claude Code 기능 설명**이다.
