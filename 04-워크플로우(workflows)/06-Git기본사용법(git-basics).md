# Git 기본 사용법과 상황별 Git Flow

Claude Code와 함께 Git을 실전에서 바로 활용하는 방법을 정리합니다.

---

## 1. Git 핵심 개념 한눈에 보기

### Git의 3가지 영역

```
Working Directory     Staging Area      Repository
(작업 폴더)          (준비 영역)        (저장소)
     │                    │                 │
     │── git add ────────>│                 │
     │                    │── git commit ──>│
     │<──────────── git checkout ───────────│
```

- **Working Directory**: 실제 파일을 편집하는 공간
- **Staging Area**: 커밋할 파일을 골라 놓는 대기 공간
- **Repository**: 커밋된 히스토리가 저장되는 공간

### 꼭 알아야 할 용어

| 용어 | 설명 |
|------|------|
| **commit** | 변경사항의 스냅샷 (저장 포인트) |
| **branch** | 독립적인 작업 흐름 (평행 세계) |
| **merge** | 두 브랜치를 합치기 |
| **remote** | 원격 저장소 (GitHub 등) |
| **origin** | 기본 원격 저장소의 별칭 |
| **HEAD** | 현재 작업 중인 위치 |
| **pull** | 원격에서 가져오기 + 합치기 |
| **push** | 로컬 커밋을 원격으로 보내기 |
| **stash** | 작업 중인 변경사항을 임시 저장 |

---

## 2. 기본 명령어 + Claude Code 활용

### 저장소 시작하기

```bash
# 새 프로젝트 시작
git init
git remote add origin https://github.com/user/repo.git

# 기존 프로젝트 가져오기
git clone https://github.com/user/repo.git
```

> Claude Code 활용법은 [05-Git워크플로우](./05-Git워크플로우(git-workflow).md)를 참조하세요.

### 변경사항 확인 & 커밋

```bash
git status                    # 현재 상태 확인
git diff                      # 변경 내용 보기
git add 파일명                 # 특정 파일 스테이징
git add .                     # 모든 변경 스테이징
git commit -m "메시지"         # 커밋
git log --oneline             # 커밋 히스토리 보기
```

> Claude Code 활용법은 [05-Git워크플로우](./05-Git워크플로우(git-workflow).md)를 참조하세요.

### 브랜치 다루기

```bash
git branch                     # 브랜치 목록
git branch feature/login       # 새 브랜치 생성
git switch feature/login       # 브랜치 이동
git switch -c feature/login    # 생성 + 이동 동시에
git branch -d feature/login    # 브랜치 삭제
```

> Claude Code 활용법은 [05-Git워크플로우](./05-Git워크플로우(git-workflow).md)를 참조하세요.

### 합치기: Merge vs Rebase

```
# Merge: 합친 기록이 남음
main:    A─B─────M
              \  /
feature:       C─D

# Rebase: 깔끔한 일직선
main:    A─B
feature:     C'─D'  (C, D를 B 뒤로 옮김)
```

```bash
# Merge 방식
git switch main
git merge feature/login

# Rebase 방식
git switch feature/login
git rebase main
```

> Claude Code 활용법은 [05-Git워크플로우](./05-Git워크플로우(git-workflow).md)를 참조하세요.

### 원격 저장소 작업

```bash
git push origin main           # 원격에 보내기
git push -u origin feature/x   # 새 브랜치를 원격에 등록 + 보내기
git pull origin main           # 원격에서 가져오기
git fetch origin               # 가져오기만 (합치지 않음)
```

> Claude Code 활용법은 [05-Git워크플로우](./05-Git워크플로우(git-workflow).md)를 참조하세요.

### 되돌리기

```bash
# 커밋 전: 파일 변경 취소
git checkout -- 파일명

# 스테이징 취소
git reset HEAD 파일명

# 커밋 되돌리기 (기록 남김, 안전)
git revert HEAD

# 커밋 되돌리기 (기록 삭제, 주의!)
git reset --soft HEAD~1   # 변경사항 유지, 커밋만 취소
git reset --hard HEAD~1   # 변경사항도 삭제 (위험!)
```

> Claude Code 활용법은 [05-Git워크플로우](./05-Git워크플로우(git-workflow).md)를 참조하세요.

### 임시 저장 (Stash)

```bash
git stash                     # 현재 작업 임시 저장
git stash list                # 저장 목록 보기
git stash pop                 # 가장 최근 저장 꺼내기
```

> Claude Code 활용법은 [05-Git워크플로우](./05-Git워크플로우(git-workflow).md)를 참조하세요.

---

## 3. 상황별 Git Flow 전략

### 3-1. GitHub Flow (가장 간단, 소규모 팀 추천)

```
main ────●────●────●────●────●────
          \       /      \       /
feature    ●──●──●        ●──●──●
```

**규칙이 단순합니다:**
1. `main`은 항상 배포 가능한 상태
2. 기능 개발은 `feature/*` 브랜치에서
3. PR(Pull Request)로 리뷰 후 머지
4. 머지 후 바로 배포

**Claude Code 실전 흐름:**

```bash
# 1. 브랜치 생성
"이슈 #15 사용자 검색 기능을 위한 브랜치를 만들어줘"

# 2. 기능 개발
"사용자 검색 API를 만들어줘"
/commit

# 3. PR 생성
"PR을 만들어줘. 이슈 #15를 닫도록 해줘"

# 4. 머지 후 정리
"머지된 브랜치를 삭제해줘"
```

**적합한 경우:** 웹 서비스, 소규모 팀, 지속적 배포

---

### 3-2. Git Flow (체계적, 릴리스 관리가 필요할 때)

```
main     ──●─────────────────●──────── (릴리스 버전)
            \               / \
release      \         ●──●   \        (릴리스 준비)
              \       /        \
develop  ──●───●──●──●──────●───●──── (개발 통합)
              \  /       \  /
feature        ●──●       ●──●         (기능 개발)
```

**5가지 브랜치 역할:**

| 브랜치 | 역할 | 수명 |
|--------|------|------|
| `main` | 릴리스된 안정 버전 | 영구 |
| `develop` | 개발 통합 | 영구 |
| `feature/*` | 기능 개발 | 기능 완료 시 삭제 |
| `release/*` | 릴리스 준비 (버그 수정, 버전 태깅) | 릴리스 후 삭제 |
| `hotfix/*` | 긴급 버그 수정 | 수정 후 삭제 |

**Claude Code 실전 흐름:**

```bash
# 기능 개발
"develop에서 feature/payment 브랜치를 만들어줘"
# ... 개발 ...
"feature/payment을 develop에 머지해줘"

# 릴리스 준비
"develop에서 release/1.2.0 브랜치를 만들어줘"
"package.json 버전을 1.2.0으로 올려줘"
/commit
"release/1.2.0을 main과 develop 양쪽에 머지하고 v1.2.0 태그를 달아줘"

# 긴급 수정
"main에서 hotfix/login-crash 브랜치를 만들어줘"
# ... 수정 ...
"hotfix를 main과 develop 양쪽에 머지해줘"
```

**적합한 경우:** 모바일 앱, 릴리스 버전 관리가 필요한 프로젝트

---

### 3-3. Trunk-Based Development (대규모 팀, 빠른 배포)

```
main ──●──●──●──●──●──●──●──●──●── (메인 트렁크)
        \/ \/ \/                     (수시간 내 머지)
```

**핵심 규칙:**
- 브랜치 수명은 **1~2일 이내**
- 작은 단위로 자주 머지
- Feature Flag로 미완성 기능 숨기기

```bash
# 짧은 브랜치로 빠르게 작업
"main에서 짧은 브랜치를 만들어서 버튼 색상을 변경해줘"
/commit
"바로 main에 머지하고 푸시해줘"
```

**적합한 경우:** 대규모 팀, CI/CD 파이프라인이 잘 갖춰진 환경

---

### 3-4. Forking Workflow (오픈소스 기여)

```
upstream/main ──●──●──●──●──  (원본 저장소)
                      ↑
origin/main   ──●──●──●──●──  (내 Fork)
                   \    /
feature             ●──●       (내 작업)
```

**Claude Code 실전 흐름:**

```bash
# 1. Fork 후 클론
"https://github.com/original/repo.git 을 클론해줘"

# 2. upstream 설정
"원본 저장소를 upstream으로 추가해줘"
# git remote add upstream https://github.com/original/repo.git

# 3. 최신 동기화
"upstream의 최신 변경사항을 가져와서 동기화해줘"
# git fetch upstream && git merge upstream/main

# 4. 기능 개발 후 PR
"브랜치를 만들어서 작업하고 origin에 푸시해줘"
# 이후 GitHub에서 원본 저장소로 PR 생성
```

---

## 4. 실전 상황별 가이드

### 상황 1: 새 프로젝트 시작

```
"이 프로젝트를 git 저장소로 초기화해줘.
Node.js 프로젝트에 맞는 .gitignore도 만들어줘.
첫 번째 커밋을 만들어줘."
```

### 상황 2: 충돌 발생

```
"main을 머지하려는데 충돌이 발생했어.
두 변경사항의 의도를 모두 살려서 해결해줘.
해결 후 테스트도 실행해줘."
```

### 상황 3: 잘못된 커밋 되돌리기

```
# 마지막 커밋만 취소 (변경사항은 유지)
"마지막 커밋을 취소해줘. 변경사항은 남겨둬."

# 특정 커밋의 변경을 되돌리기 (안전)
"abc1234 커밋의 변경사항을 되돌리는 커밋을 만들어줘"

# 실수로 민감 정보를 커밋한 경우
"마지막 커밋에 API 키가 포함됐어. 해당 커밋을 제거해줘"
```

### 상황 4: 긴급 버그 수정 (Hotfix)

```
"현재 작업을 임시 저장하고,
main에서 hotfix 브랜치를 만들어서
로그인 버그를 수정해줘.
수정 후 main에 머지하고 태그를 달아줘."
```

### 상황 5: 릴리스 준비

```
"develop에서 release/2.0.0 브랜치를 만들어줘.
package.json 버전을 2.0.0으로 올리고,
CHANGELOG.md를 업데이트해줘.
테스트가 통과하면 main에 머지하고 v2.0.0 태그를 달아줘."
```

---

## 5. Git 베스트 프랙티스

### 커밋 규칙

| 규칙 | 좋은 예 | 나쁜 예 |
|------|---------|---------|
| 작은 단위로 | `feat: 로그인 폼 추가` | `여러 기능 한꺼번에 커밋` |
| 명확한 메시지 | `fix: 장바구니 수량 음수 방지` | `버그 수정` |
| 타입 접두사 | `docs: API 문서 업데이트` | `문서 고침` |

### 브랜치 네이밍

```
feature/user-login       # 기능 개발
fix/cart-quantity-bug     # 버그 수정
hotfix/critical-crash     # 긴급 수정
release/1.2.0            # 릴리스 준비
docs/api-guide           # 문서 작업
```

### 반드시 .gitignore에 넣을 것

```
node_modules/
.env
.env.local
*.log
dist/
.DS_Store
```

> `/commit` 활용법과 커밋 메시지 자동 생성에 대한 자세한 내용은 [05-Git워크플로우](./05-Git워크플로우(git-workflow).md)를 참조하세요.

---

## 6. 명령어 빠른 참조표

### 일상 작업

| 하고 싶은 것 | 명령어 |
|-------------|--------|
| 상태 확인 | `git status` |
| 변경 내용 보기 | `git diff` |
| 파일 스테이징 | `git add 파일명` |
| 전체 스테이징 | `git add .` |
| 커밋 | `git commit -m "메시지"` |
| 히스토리 보기 | `git log --oneline` |

### 브랜치 작업

| 하고 싶은 것 | 명령어 |
|-------------|--------|
| 브랜치 목록 | `git branch` |
| 브랜치 생성+이동 | `git switch -c 이름` |
| 브랜치 이동 | `git switch 이름` |
| 브랜치 머지 | `git merge 이름` |
| 브랜치 삭제 | `git branch -d 이름` |

### 원격 작업

| 하고 싶은 것 | 명령어 |
|-------------|--------|
| 푸시 | `git push origin 브랜치` |
| 풀 | `git pull origin 브랜치` |
| 새 브랜치 푸시 | `git push -u origin 브랜치` |
| 원격 정보 보기 | `git remote -v` |

### 되돌리기

| 하고 싶은 것 | 명령어 | 안전도 |
|-------------|--------|--------|
| 커밋 되돌리기(기록O) | `git revert HEAD` | 안전 |
| 커밋 취소(변경유지) | `git reset --soft HEAD~1` | 보통 |
| 커밋 취소(변경삭제) | `git reset --hard HEAD~1` | 위험 |
| 파일 변경 취소 | `git checkout -- 파일` | 보통 |
| 임시 저장 | `git stash` | 안전 |

---

## 다음 단계

Git 워크플로우를 Claude Code와 함께 실전에서 활용하는 방법은 다음 문서를 참고하세요.

-> [Git 커밋/PR 워크플로우](./05-Git워크플로우(git-workflow).md)
