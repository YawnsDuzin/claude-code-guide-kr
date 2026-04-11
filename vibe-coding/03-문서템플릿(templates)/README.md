# 03. 문서 템플릿

> 프로젝트 루트에 **그대로 복사**해서 쓰는 문서 템플릿.
> 이 파일들을 수정하는 건 메타 작업(템플릿 자체 개선). 프로젝트에 적용할 땐 복사본만 수정하세요.

## 파일 목록

| 템플릿 | 복사 위치 | 목적 |
|--------|----------|------|
| [CLAUDE.template.md](./CLAUDE.template.md) | `<repo>/CLAUDE.md` | 에이전트가 세션 시작 시 자동으로 읽는 프로젝트 규칙 |
| [PRD.template.md](./PRD.template.md) | `<repo>/docs/PRD.md` | 제품 요구사항 — 무엇을 왜 만드는가 |
| [architecture.template.md](./architecture.template.md) | `<repo>/docs/architecture.md` | 시스템 구조 — Mermaid 다이어그램 중심 |
| [erd.template.md](./erd.template.md) | `<repo>/docs/erd.md` | 데이터 모델 — Mermaid ER 다이어그램 |
| [feature-spec.template.md](./feature-spec.template.md) | `<repo>/docs/features/<name>.md` | 단일 기능의 수용 기준 + 범위 |

## 복사 방법

```bash
# CLAUDE.md
cp vibe-coding/03-문서템플릿\(templates\)/CLAUDE.template.md ./CLAUDE.md

# docs/
mkdir -p docs docs/features
cp vibe-coding/03-문서템플릿\(templates\)/PRD.template.md docs/PRD.md
cp vibe-coding/03-문서템플릿\(templates\)/architecture.template.md docs/architecture.md
cp vibe-coding/03-문서템플릿\(templates\)/erd.template.md docs/erd.md
```

## 템플릿 원칙

1. **빈 칸은 `<...>` 로** — 채워야 할 자리를 시각적으로 표시
2. **"삭제해야 할 예시"는 `(예시)` 표시** — 실제 내용을 쓸 때 지우라는 신호
3. **Mermaid는 필수** — 에이전트가 그림 대신 파싱 가능한 구조로 읽음
4. **각 섹션은 독립적** — 다른 섹션을 안 읽어도 의미가 있어야 함
5. **명령형 어투** — "~한다", "~하지 않는다" (설명형 금지)

## 업데이트 주기

| 문서 | 업데이트 트리거 |
|------|----------------|
| CLAUDE.md | 새 규칙/금지 사항이 생길 때마다 |
| PRD.md | 스프린트 시작 시 / 기능 완료 시 상태 변경 |
| architecture.md | 컴포넌트 추가/제거 시 |
| erd.md | 스키마 변경과 **같은 커밋** |
| feature-spec | 기능 종료 시 `[완료]` 표시 |
