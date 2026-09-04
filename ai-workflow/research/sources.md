# Source registry

> Checked on: `2026-09-04`  
> Đây là registry của nguồn đã dùng cho planning draft, không phải danh sách đầy đủ.

## Official platform docs

| Source | Relevance |
| --- | --- |
| [OpenAI Learn — Customization](https://learn.chatgpt.com/docs/customization/overview) | Codex guidance, skills, MCP, subagents, memories và thứ tự build |
| [OpenAI Learn — Build skills](https://learn.chatgpt.com/docs/build-skills) | Skill structure, progressive disclosure, discovery và packaging |
| [OpenAI Learn — Hooks](https://learn.chatgpt.com/docs/hooks) | Lifecycle events, matcher, block/rewrite, tool coverage |
| [OpenAI Learn — Subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents) | Custom agents, TOML schema, inheritance và sandbox |
| [OpenAI Learn — Plugins](https://learn.chatgpt.com/docs/plugins) | Plugin catalog, components, Codex CLI và trust/permissions |
| [OpenAI Learn — Memories](https://learn.chatgpt.com/docs/customization/memories) | Local memory và nguyên tắc không coi memory là rule source duy nhất |
| [Claude Code — Extend Claude Code](https://code.claude.com/docs/en/features-overview) | So sánh CLAUDE.md, skills, MCP, hooks, subagents, plugins |
| [Claude Code — Skills](https://code.claude.com/docs/en/slash-commands) | Skill format, invocation và Agent Skills standard |
| [Claude Code — Custom subagents](https://code.claude.com/docs/en/sub-agents) | Agent definition, context isolation và persistent memory |
| [Claude Code — Hooks](https://code.claude.com/docs/en/hooks) | Hook event/action types và scope |
| [Claude Code — Plugins](https://code.claude.com/docs/en/plugins) | Plugin structure và distribution |

## Standards and architecture

| Source | Relevance |
| --- | --- |
| [Agent Skills overview](https://agentskills.io/home) | Portable `SKILL.md` concept và progressive disclosure |
| [Agent Skills specification](https://agentskills.io/specification) | Frontmatter, directory structure, resources và validation |
| [MCP server overview](https://modelcontextprotocol.io/specification/2025-06-18/server/index) | Resources, prompts, tools và control model |
| [MCP tools and safety](https://modelcontextprotocol.io/specification/2024-11-05/server/tools) | Human-in-the-loop và tool authorization considerations |
| [Anthropic — Building effective agents](https://www.anthropic.com/engineering/building-effective-agents) | Workflow vs agent, simple composable patterns, orchestration patterns |
| [Anthropic — Effective context engineering](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) | Context budget, memory, compaction và just-in-time context |
| [Anthropic — Demystifying evals](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents) | Task/trial/grader/transcript/outcome và eval harness |
| [Anthropic — Sandboxing Claude Code](https://www.anthropic.com/engineering/claude-code-sandboxing) | Filesystem/network isolation và prompt injection risk |

## Primary idea source

| Source | Relevance |
| --- | --- |
| [Andrej Karpathy — LLM Wiki gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) | Raw → wiki → schema; ingest/query/lint; index/log; Markdown/Git/Obsidian |

## Candidate implementations to investigate

| Source | Why it is in scope | Evidence level |
| --- | --- | --- |
| [jellydn/tiny-coding-agent](https://github.com/jellydn/tiny-coding-agent) | Small multi-provider coding agent with MCP, skills, plugins, hooks | Community implementation; verify locally before relying on it |
| [tinyagent.page](https://tinyagent.page/) | Persistent shareable UI/data surface controlled through MCP | Product page; behavior and availability need trial |
| [alchemiststudiosDOTai/tinyAgent](https://github.com/alchemiststudiosDOTai/tinyagent) | Lightweight Python agent loop candidate | Community beta; APIs may change |

