# Claude Orchestrator Template

Bộ template để setup hệ thống AI orchestrator với Claude Code cho bất kỳ project nào.

## Bao gồm gì?

```
template/
├── .claude/
│   ├── agents/          # PM, Coder, Explorer, Reviewer, Debugger, Doc-writer, Solution Architect
│   ├── commands/        # /bootstrap, /resume, /handoff, /learn, /commit, /pr, /review, ...
│   ├── skills/          # orchestrate, wrap-up, write-*, explain, refactor, frontend-design, gitnexus
│   └── settings.json    # Permissions, hooks, model env vars
├── .agents/
│   └── skills/
│       └── frontend-design/   # Skill cho Codex, Gemini CLI, GitHub Copilot
├── .project-info/
│   ├── architecture_template.md
│   ├── conventions_template.md
│   ├── stack_template.md
│   ├── onboarding_template.md
│   └── patterns_template.md
├── .project-manager/
│   ├── README.md
│   ├── status.md
│   ├── tasks/           # backlog, in-progress, done
│   ├── sessions/        # latest handoff note
│   ├── logs/
│   └── knowledge/       # decisions, blockers, context
├── CLAUDE.md            # Project instructions (edit this!)
└── AGENTS.md            # Mirror cho Codex/other tools (keep in sync)
```

## Cách dùng

### Bước 1: Copy vào project

```bash
cp -r template/. /path/to/your-project/
```

> Hoặc copy từng folder nếu project đã có `.claude/`.

### Bước 2: Điền thông tin vào CLAUDE.md

Mở `CLAUDE.md` và điền:
- Project name, type, status
- Tech stack thực tế
- Project structure
- Coding conventions của team

### Bước 3: Bootstrap project intelligence

Mở Claude Code trong project, chạy:
```
/bootstrap
```

Lệnh này sẽ phân tích codebase và tạo ra:
- `.project-info/architecture.md` — kiến trúc project
- `.project-info/stack.md` — tech stack chi tiết
- `.project-info/conventions/` — conventions theo domain
- `.project-info/onboarding.md` — hướng dẫn onboarding

### Bước 4: Bắt đầu làm việc

```
@pm [yêu cầu của bạn]
```

PM sẽ tự routing đến agent phù hợp.

---

## Workflow cơ bản

| Tình huống | Lệnh |
|------------|------|
| Mở session mới | `/resume` |
| Implement feature | `@pm Implement [feature]` |
| Fix bug | `@pm Fix [bug description]` |
| Review code | `@pm Review recent changes` |
| Commit | `/commit` |
| Tạo PR | `/pr` |
| Kết thúc session | `/handoff` |

## Agents

| Agent | Khi nào dùng |
|-------|--------------|
| `@pm` | Mọi lúc — đây là entry point duy nhất |
| `@explorer` | PM tự gọi khi cần hiểu codebase |
| `@coder` | PM tự gọi khi implement |
| `@reviewer` | PM tự gọi khi review |
| `@debugger` | PM tự gọi khi debug |

## Yêu cầu

- Claude Code (claude.ai/code)
- (Optional) GitNexus: `npx gitnexus analyze` để index codebase cho graph search

## MCP Tools

File `.claude/settings.json` bật `.mcp.json` auto-discovery. Nếu dùng GitNexus:

```json
// .mcp.json
{
  "mcpServers": {
    "gitnexus": {
      "command": "npx",
      "args": ["gitnexus", "mcp"]
    }
  }
}
```
