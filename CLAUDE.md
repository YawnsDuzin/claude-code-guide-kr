# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A comprehensive Korean-language guide to Claude Code — covering installation, concepts, features, workflows, optimization, and a practical "vibe coding" methodology with reusable templates and prompts integrated directly into workflow documents. This is a **documentation-only** repository with no build system, tests, or application code.

## Repository Structure

All content is organized into numbered sections at the project root:

- **01-07**: Reference material + practical playbooks (installation, core concepts, features, workflows with prompt templates, tech stacks, tool comparisons, optimization with document templates)
- **99-부록(appendix)**: CLI reference, troubleshooting, glossary, FAQ, tips, agent repos, agent examples

### Merged/Deleted Files

The following files were merged and no longer exist as separate files:
- `07-최적화/05-CLAUDE-MD템플릿` → merged into `07-최적화/02-CLAUDE-MD작성법(claude-md).md` §7 (2026-04-12)
- `07-최적화/04-비용관리` → merged into `07-최적화/03-성능최적화(performance).md` Part 2 (2026-04-12)
- `07-최적화/06,07` → renumbered to `04,05` (2026-04-13)
- `08-바이브코딩/00-개요(overview).md` → merged into `08-바이브코딩/README.md` (2026-04-13)
- `08-바이브코딩(vibe-coding)/` 전체 폴더 해체 (2026-04-14):
  - 프롬프트 템플릿 7종 → `04-워크플로우/` 각 흐름 문서에 병합
  - 범용 템플릿 + 6대 원칙 → `07-최적화/01-프롬프트엔지니어링`
  - CLAUDE.template → `07-최적화/02-CLAUDE-MD작성법` §7
  - 문서 템플릿 4종 (PRD/arch/erd/feature-spec) → `07-최적화/04-문서종류분류`
  - 에이전트 패턴 + 역할 기반 + 멀티에이전트 예제 → `03-주요기능/05-서브에이전트`
  - 크로스 에이전트 핸드오프 → `06-비교/05-크로스도구핸드오프` (신규)
  - 인기 에이전트 레포 → `99-부록/06-에이전트레포` (신규)
  - 에이전트 작업별 예제 → `99-부록/07-에이전트예제` (신규)
  - 빠른 시작 → `04-워크플로우/00-프로젝트초기화` (신규)
  - 기능 개발 예제 → `04-워크플로우/07-기능개발흐름`에 병합

## Writing Conventions

- All documentation is written in **Korean**
- File and directory names use the pattern `NN-한글명(english-slug).md`
- Diagrams use **Mermaid** syntax (agent-friendly, renders in GitHub)
- Document templates are embedded as collapsible `<details>` blocks in `07-최적화/04-문서종류분류`
- Imperative tone for instructions; structured with clear headings and tables

## Navigation Model

The guide supports two reading paths (defined in README.md):
1. **Reference path** (sections 01-07): "What is this feature?"
2. **Playbook path** (`04-워크플로우/07~11`): "What do I do at work tomorrow?" — each flow doc includes flowchart + prompt template + examples

## Key Architectural Concepts

The "vibe coding" methodology follows a core equation:
```
Good Vibe Coding = Good Documentation + Good Prompt Structure + Proper Task Division
```

Five task types have dedicated workflow playbooks (in `04-워크플로우/`): feature development, bug fixes, refactoring, schema changes, and UI implementation. Each includes its own prompt template directly in the document.

## Single Source of Truth Policy

To prevent duplication, each major topic has a designated "primary document." Other documents that mention the topic should link to it rather than repeating the content:

| Topic | Primary Document |
|-------|-----------------|
| CLAUDE.md 개념/기능 | `03-주요기능/01-메모리(memory).md` |
| CLAUDE.md 작성법/템플릿 | `07-최적화/02-CLAUDE-MD작성법(claude-md).md` |
| 성능 + 비용 최적화 | `07-최적화/03-성능최적화(performance).md` |
| 컨텍스트 윈도우 관리 | `02-핵심개념/04-컨텍스트관리(context-management).md` |
| 슬래시 명령어 | `03-주요기능/02-스킬(skills).md` |
| 트러블슈팅 | `99-부록/02-문제해결(troubleshooting).md` |
| 도구 비교/선택 | `06-비교/04-선택가이드(selection-guide).md` |
| 크로스 도구 핸드오프 | `06-비교/05-크로스도구핸드오프(cross-tool-handoff).md` |
| Git Claude Code 활용 | `04-워크플로우/05-Git워크플로우(git-workflow).md` |
| Git 기본 개념/명령어 | `04-워크플로우/06-Git기본사용법(git-basics).md` |
| 프롬프트 엔지니어링 | `07-최적화/01-프롬프트엔지니어링(prompt-engineering).md` |
| 문서 종류/템플릿 | `07-최적화/04-문서종류분류(document-types).md` |
| 서브에이전트/멀티에이전트 | `03-주요기능/05-서브에이전트(subagents).md` |

When adding or editing content, check if the topic already has a primary document above. If so, write only a brief mention (1-3 lines) and link to the primary document instead of duplicating.

## Common Tasks

```bash
# No build/test/lint — this is a docs-only repo
# Preview locally with any Markdown viewer that supports Mermaid

# Check for broken internal links (relative paths between .md files)
git diff --name-only  # after renames, verify cross-references still resolve
```

## Content Guidelines

- When adding new sections, follow the existing `NN-한글명(english-slug).md` naming convention
- Update the README.md table of contents when adding or restructuring sections
- Cross-reference related sections using relative Markdown links
- **04-워크플로우 (07~11)** are self-contained playbooks: flowchart + prompt template + examples in one file
- **04-워크플로우/00-프로젝트초기화** is the entry point for new projects (30-min bootstrap)
- **05-기술스택** files should contain only key points (~5 lines) for CLAUDE.md examples, linking to `07-최적화/02-CLAUDE-MD작성법` for full templates
- **06-비교** individual comparison files focus on the comparison partner's unique traits; common Claude Code descriptions are kept minimal and link to `04-선택가이드` for cross-tool analysis
- **07-최적화/04-문서종류분류** contains document templates as collapsible `<details>` blocks — edit templates there, not in separate files
