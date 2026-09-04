# News and community sources

Các nguồn dùng để discovery, theo dõi conversation và phát hiện trend. Community/social content cần được xác minh trước khi trở thành claim hoặc decision.

## Sources

| Source | URL | Type | Status | Role | Caveat |
| --- | --- | --- | --- | --- | --- |
| Hacker News | [news.ycombinator.com](https://news.ycombinator.com/) | `community` | `following` | `discovery`, `opinion` | Comment là tín hiệu, không phải evidence mặc định |
| Reddit | [reddit.com](https://www.reddit.com/) | `community` | `following` | `discovery`, `opinion` | Cần kiểm tra author/source/context |
| daily.dev | [daily.dev/search?q=TLDR+AI](https://daily.dev/search?q=TLDR+AI) | `news` / aggregator | `following` | `discovery`, `learning` | Aggregated content; follow link gốc |
| X | [x.com](https://x.com/) | `social` | `following` | `early-signal`, `discovery` | Dễ thiếu context; verify bằng primary source |

## Query bank theo source

### Hacker News

```text
AI agents
agentic coding
MCP
Claude Code
Codex
LLM memory
AI second brain
AI evals
```

Search engine variants:

```text
site:news.ycombinator.com "AI agents"
site:news.ycombinator.com MCP
site:news.ycombinator.com "Claude Code"
```

### Reddit

```text
AI agents Reddit
Claude Code Reddit
Codex CLI Reddit
MCP servers Reddit
Agent Skills Reddit
AI second brain Reddit
tiny coding agent Reddit
```

Search engine variants:

```text
site:reddit.com "AI agents"
site:reddit.com "MCP server"
site:reddit.com "Claude Code"
site:reddit.com "LLM Wiki"
```

### daily.dev

```text
https://daily.dev/search?q=TLDR+AI
https://daily.dev/search?q=AI+agents
https://daily.dev/search?q=MCP
https://daily.dev/search?q=Claude+Code
https://daily.dev/search?q=AI+coding+agents
```

### X

```text
"AI agents" MCP
"Claude Code" skills
Codex hooks
"Agent Skills" SKILL.md
"LLM Wiki"
"AI second brain"
"agentic coding"
```

## Promotion rule

`community/social/news` → discovery candidate → primary-source check → research note → evidence registry (nếu đủ căn cứ).

