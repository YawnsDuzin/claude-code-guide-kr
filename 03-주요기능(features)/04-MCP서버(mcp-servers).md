# MCP 서버 연동 (Model Context Protocol)

## 개요

**MCP(Model Context Protocol)**는 Claude Code가 외부 서비스 및 데이터 소스와 연결할 수 있게 해주는 프로토콜입니다. MCP 서버를 통해 GitHub, Slack, 데이터베이스 등 다양한 서비스에 직접 접근하여 작업할 수 있습니다.

쉽게 말해, **Claude Code에 플러그인을 추가하는 것**과 같습니다. MCP 서버를 연결하면 Claude가 사용할 수 있는 도구가 늘어납니다.

---

## MCP란 무엇인가?

### 핵심 개념

MCP(Model Context Protocol)는 AI 모델이 외부 도구와 데이터에 접근하기 위한 표준 프로토콜입니다.

```
Claude Code ←→ MCP 프로토콜 ←→ MCP 서버 ←→ 외부 서비스
                                    │
                                    ├── GitHub API
                                    ├── Slack API
                                    ├── 데이터베이스
                                    └── 기타 서비스
```

### MCP의 구성 요소

| 구성 요소 | 역할 | 예시 |
|-----------|------|------|
| **MCP 클라이언트** | Claude Code가 MCP 서버에 연결 | Claude Code 내장 |
| **MCP 서버** | 외부 서비스를 도구로 제공 | GitHub MCP 서버 |
| **도구(Tools)** | MCP 서버가 제공하는 개별 기능 | `create_pull_request` |
| **리소스(Resources)** | MCP 서버가 제공하는 데이터 | 이슈 목록, PR 내용 |

### MCP를 사용하면 좋은 이유

- **직접 연동**: Claude가 GitHub PR을 직접 생성하고 관리
- **실시간 데이터**: 최신 이슈, PR 상태 등을 실시간으로 확인
- **자동화**: 여러 서비스를 연결하여 복잡한 워크플로우 자동화
- **확장성**: 새로운 MCP 서버를 추가하여 기능 확장

---

## Claude Code에서 MCP 서버 사용하기

### MCP 서버가 연결되면 달라지는 점

MCP 서버를 연결하기 전과 후를 비교해보겠습니다.

**MCP 서버 없이**:
```
사용자: GitHub에 PR을 만들어줘
Claude: gh pr create 명령어를 실행하겠습니다.
# Bash 도구로 gh CLI 명령어를 실행
```

**GitHub MCP 서버 연결 후**:
```
사용자: GitHub에 PR을 만들어줘
Claude: GitHub MCP 도구를 사용하여 PR을 생성하겠습니다.
# mcp__github__create_pull_request 도구를 직접 호출
# 더 풍부한 데이터 접근과 안정적인 연동 가능
```

---

## MCP 서버 설정 방법

### 설정 파일 위치

MCP 서버는 Claude Code의 설정 파일에서 구성합니다.

```
# 프로젝트 레벨 (프로젝트 전용)
.claude/settings.json

# 사용자 레벨 (모든 프로젝트에 적용)
~/.claude/settings.json
```

### 기본 설정 구조

```json
{
  "mcpServers": {
    "서버이름": {
      "command": "실행 명령어",
      "args": ["인자1", "인자2"],
      "env": {
        "환경변수": "값"
      }
    }
  }
}
```

### 설정 필드 설명

| 필드 | 설명 | 필수 여부 |
|------|------|----------|
| `command` | MCP 서버를 시작하는 명령어 | 필수 |
| `args` | 명령어에 전달할 인자 배열 | 선택 |
| `env` | 환경 변수 설정 | 선택 |

---

## 인기 MCP 서버

### 1. GitHub MCP 서버

GitHub의 이슈, PR, 리포지토리를 직접 관리합니다.

**제공 도구 예시**:
- `create_pull_request` - PR 생성
- `list_issues` - 이슈 목록 조회
- `add_issue_comment` - 이슈에 코멘트 추가
- `merge_pull_request` - PR 머지
- `search_code` - 코드 검색

**설정 예시**:

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "ghp_your_token_here"
      }
    }
  }
}
```

### 2. Slack MCP 서버

Slack 채널에 메시지를 보내거나 읽습니다.

**제공 도구 예시**:
- 메시지 전송
- 채널 목록 조회
- 메시지 검색

**설정 예시**:

```json
{
  "mcpServers": {
    "slack": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-slack"],
      "env": {
        "SLACK_BOT_TOKEN": "xoxb-your-token-here"
      }
    }
  }
}
```

### 3. 데이터베이스 MCP 서버 (PostgreSQL)

데이터베이스에 직접 쿼리하고 스키마를 조회합니다.

**설정 예시**:

```json
{
  "mcpServers": {
    "postgres": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-postgres",
        "postgresql://user:password@localhost:5432/mydb"
      ]
    }
  }
}
```

### 4. 파일시스템 MCP 서버

특정 디렉토리의 파일을 관리합니다.

**설정 예시**:

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "/path/to/allowed/directory"
      ]
    }
  }
}
```

### 5. 웹 검색 MCP 서버 (Brave Search)

웹 검색 기능을 추가합니다.

**설정 예시**:

```json
{
  "mcpServers": {
    "brave-search": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-brave-search"],
      "env": {
        "BRAVE_API_KEY": "your-api-key"
      }
    }
  }
}
```

---

## GitHub MCP 서버 상세 설정 가이드

GitHub MCP 서버는 가장 많이 사용되는 MCP 서버입니다. 단계별로 설정해보겠습니다.

### 1단계: GitHub Personal Access Token 생성

1. GitHub에서 **Settings > Developer settings > Personal access tokens > Tokens (classic)** 이동
2. **Generate new token (classic)** 클릭
3. 필요한 권한(scope) 선택:
   - `repo` - 리포지토리 전체 접근
   - `read:org` - 조직 정보 읽기
   - `read:user` - 사용자 정보 읽기
4. 토큰 생성 및 복사

### 2단계: 설정 파일에 추가

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "ghp_xxxxxxxxxxxxxxxxxxxx"
      }
    }
  }
}
```

**보안 팁**: 토큰을 설정 파일에 직접 넣는 대신 환경 변수를 참조할 수 있습니다.

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_TOKEN}"
      }
    }
  }
}
```

### 3단계: Claude Code 재시작

설정 후 Claude Code를 재시작하면 GitHub 관련 도구가 활성화됩니다.

### 4단계: 사용 확인

```
사용자: 이 리포지토리의 열린 이슈를 보여줘
Claude: GitHub MCP 서버를 통해 이슈 목록을 조회하겠습니다.
# mcp__github__list_issues 도구 사용
```

### 활용 예시

```
# PR 생성
사용자: 현재 변경 사항으로 PR을 만들어줘. 제목은 "로그인 기능 추가"로 해줘.

# 이슈 관리
사용자: #42 이슈에 "수정 완료했습니다"라고 코멘트 달아줘.

# 코드 검색
사용자: 이 조직의 다른 리포지토리에서 비슷한 인증 코드를 찾아줘.

# PR 리뷰
사용자: PR #15의 변경 사항을 리뷰해줘.
```

---

## 커스텀 MCP 서버 만들기

필요한 기능이 기존 MCP 서버에 없다면 직접 만들 수 있습니다.

### 기본 구조 (Node.js)

```javascript
// my-mcp-server.js
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";

const server = new Server(
  { name: "my-custom-server", version: "1.0.0" },
  { capabilities: { tools: {} } }
);

// 도구 목록 정의
server.setRequestHandler("tools/list", async () => ({
  tools: [
    {
      name: "get_weather",
      description: "도시의 현재 날씨를 조회합니다",
      inputSchema: {
        type: "object",
        properties: {
          city: { type: "string", description: "도시 이름" }
        },
        required: ["city"]
      }
    }
  ]
}));

// 도구 실행 핸들러
server.setRequestHandler("tools/call", async (request) => {
  if (request.params.name === "get_weather") {
    const city = request.params.arguments.city;
    // 실제 API 호출 로직
    return {
      content: [
        { type: "text", text: `${city}의 현재 날씨: 맑음, 22도` }
      ]
    };
  }
});

// 서버 시작
const transport = new StdioServerTransport();
await server.connect(transport);
```

### 기본 구조 (Python)

```python
# my_mcp_server.py
from mcp.server import Server
from mcp.server.stdio import stdio_server
from mcp.types import Tool, TextContent

app = Server("my-custom-server")

@app.list_tools()
async def list_tools():
    return [
        Tool(
            name="get_weather",
            description="도시의 현재 날씨를 조회합니다",
            inputSchema={
                "type": "object",
                "properties": {
                    "city": {"type": "string", "description": "도시 이름"}
                },
                "required": ["city"]
            }
        )
    ]

@app.call_tool()
async def call_tool(name: str, arguments: dict):
    if name == "get_weather":
        city = arguments["city"]
        return [TextContent(type="text", text=f"{city}의 현재 날씨: 맑음, 22도")]

async def main():
    async with stdio_server() as (read_stream, write_stream):
        await app.run(read_stream, write_stream)

if __name__ == "__main__":
    import asyncio
    asyncio.run(main())
```

### 커스텀 서버 등록

```json
{
  "mcpServers": {
    "my-server": {
      "command": "node",
      "args": ["./tools/my-mcp-server.js"]
    }
  }
}
```

또는 Python 서버의 경우:

```json
{
  "mcpServers": {
    "my-server": {
      "command": "python",
      "args": ["./tools/my_mcp_server.py"]
    }
  }
}
```

---

## MCP 연결 문제 해결 (Troubleshooting)

### 1. MCP 서버가 연결되지 않을 때

**증상**: Claude Code에서 MCP 도구가 보이지 않음

**확인 사항**:
```bash
# MCP 서버 패키지가 설치되어 있는지 확인
npx -y @modelcontextprotocol/server-github --version

# Node.js 버전 확인 (18 이상 필요)
node --version
```

### 2. 인증 오류

**증상**: "Authentication failed" 또는 "401 Unauthorized"

**해결 방법**:
```bash
# 토큰이 유효한지 확인 (GitHub 예시)
curl -H "Authorization: token ghp_your_token" https://api.github.com/user
```

- 토큰이 만료되지 않았는지 확인
- 필요한 권한(scope)이 부여되었는지 확인
- 토큰 값에 공백이나 줄바꿈이 포함되지 않았는지 확인

### 3. 서버 시작 실패

**증상**: "Failed to start MCP server"

**해결 방법**:
```bash
# 서버를 직접 실행하여 에러 메시지 확인
npx -y @modelcontextprotocol/server-github

# 필요한 환경 변수가 설정되어 있는지 확인
echo $GITHUB_PERSONAL_ACCESS_TOKEN
```

### 4. 설정 파일 오류

**증상**: 설정이 적용되지 않음

**확인 사항**:
```bash
# JSON 문법 검증
cat .claude/settings.json | jq .

# 설정 파일 위치 확인
ls -la .claude/settings.json
ls -la ~/.claude/settings.json
```

### 5. 타임아웃 오류

**증상**: MCP 도구 호출 시 시간 초과

**해결 방법**:
- 네트워크 연결 상태 확인
- 외부 API 서비스가 정상 운영 중인지 확인
- MCP 서버 재시작 (Claude Code 재시작)

### 일반적인 디버깅 순서

```
1. Claude Code 재시작
2. 설정 파일 JSON 문법 확인
3. 토큰/인증 정보 유효성 확인
4. MCP 서버 패키지 재설치
5. MCP 서버를 터미널에서 직접 실행하여 에러 확인
6. Node.js/Python 버전 확인
```

---

## 여러 MCP 서버 동시 사용

여러 MCP 서버를 동시에 연결하여 강력한 워크플로우를 구성할 수 있습니다.

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "ghp_xxx"
      }
    },
    "slack": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-slack"],
      "env": {
        "SLACK_BOT_TOKEN": "xoxb-xxx"
      }
    },
    "postgres": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-postgres",
        "postgresql://localhost:5432/mydb"
      ]
    }
  }
}
```

이렇게 설정하면 다음과 같은 복합 작업이 가능합니다:

```
사용자: 데이터베이스에서 최근 에러 로그를 확인하고,
       관련 GitHub 이슈를 찾아서,
       Slack #dev 채널에 요약을 보내줘.
```

---

## 보안 모범 사례

### 1. 토큰 관리

```bash
# 환경 변수로 토큰 관리 (권장)
export GITHUB_TOKEN="ghp_xxxx"
export SLACK_TOKEN="xoxb-xxxx"
```

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_TOKEN}"
      }
    }
  }
}
```

### 2. 최소 권한 원칙

토큰에 필요한 최소한의 권한만 부여하세요.

- **읽기만 필요한 경우**: `read` 권한만 부여
- **특정 리포지토리만**: Fine-grained 토큰 사용
- **공개 리포지토리만**: `public_repo` 스코프만 부여

### 3. 설정 파일을 Git에서 제외

토큰이 포함된 설정 파일은 `.gitignore`에 추가하세요.

```gitignore
# .gitignore
.claude/settings.local.json
```

---

## 요약

| 항목 | 설명 |
|------|------|
| **MCP란** | AI가 외부 서비스와 연결하는 표준 프로토콜 |
| **역할** | Claude Code에 플러그인처럼 기능을 추가 |
| **설정 위치** | `.claude/settings.json` 또는 `~/.claude/settings.json` |
| **인기 서버** | GitHub, Slack, PostgreSQL, Filesystem, Web Search |
| **커스텀 서버** | Node.js 또는 Python으로 직접 제작 가능 |
| **보안** | 환경 변수로 토큰 관리, 최소 권한 원칙 |

MCP 서버를 활용하면 Claude Code의 기능을 크게 확장할 수 있습니다. GitHub MCP 서버부터 시작하여 필요에 따라 다른 서비스를 연결해보세요.
