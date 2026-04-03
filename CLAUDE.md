# Project Instructions

## Project Overview

**Project Name:** [Tên project]
**Type:** [Web App / API / Mobile / Library]
**Status:** Active

## Tech Stack

- **Language:** [TypeScript / Python / Java / ...]
- **Framework:** [Next.js / FastAPI / Spring / ...]
- **Database:** [PostgreSQL / MongoDB / N/A]
- **Testing:** [Jest / Pytest / JUnit / ...]
- **Package Manager:** [npm / yarn / pnpm / pip / ...]

## Project Structure

```
src/
  ├── [folder]/    # [mô tả]
  ├── [folder]/    # [mô tả]
  └── [folder]/    # [mô tả]
```

## Coding Conventions

### General
- [Ngôn ngữ và strict mode]
- [Functional vs OOP preference]
- [Naming conventions: camelCase / snake_case / ...]
- [File naming: kebab-case / PascalCase / ...]

### Functions
- Hàm nhỏ, làm một việc duy nhất (Single Responsibility)
- Pure functions khi có thể
- Tên hàm phải nói rõ ý định: `getUserById`, `formatCurrency`

### Error Handling
- Luôn handle errors ở boundaries (API routes, event handlers)
- Dùng custom error types cho domain errors
- Log errors với context đầy đủ

### Comments
- Chỉ comment khi logic không tự giải thích được
- Không comment code đã xóa — dùng git history

## Git Workflow

### Branch Naming
- `feat/[ticket-id]-short-description` — Feature mới
- `fix/[ticket-id]-short-description` — Bug fix
- `chore/description` — Maintenance
- `docs/description` — Documentation

### Commit Convention (Conventional Commits)
```
<type>(<scope>): <description>

feat(auth): add OAuth2 login
fix(cart): prevent duplicate items
chore(deps): upgrade dependencies
```

Types: `feat`, `fix`, `chore`, `docs`, `test`, `refactor`, `perf`, `ci`

### PR Rules
- PR title theo Conventional Commits format
- Description giải thích WHY (không chỉ WHAT)
- Pass tất cả CI checks trước khi merge
- Cần ít nhất 1 reviewer approve

## Testing

- Unit tests cho business logic
- Integration tests cho API endpoints
- E2E tests cho critical user flows
- Chạy test suite trước khi commit

## Environment Variables

```bash
# Required
# DATABASE_URL=
# API_KEY=

# Optional
# DEBUG=false
# LOG_LEVEL=info
```

## Important Notes

- [Lưu ý quan trọng 1]
- [Lưu ý quan trọng 2]

---

## Agent System

### Default Agent: PM

**Dùng `@pm [yêu cầu]`** cho mọi task — PM là điểm giao tiếp duy nhất với user, tự routing đến sub-agent phù hợp.

### Agents

| Agent | Model | Vai trò |
|-------|-------|---------|
| `@pm` | Sonnet | **Default** — nhận mọi yêu cầu, routing, orchestrate |
| `@explorer` | Haiku | Đọc/hiểu codebase, không edit |
| `@coder` | Haiku | Implement feature, viết code |
| `@spec-reviewer` | Haiku | Kiểm tra code có đúng spec không |
| `@reviewer` | Sonnet | Review code quality |
| `@debugger` | Sonnet | Debug khó, deep reasoning |
| `@doc-writer` | Haiku | Viết/cập nhật documentation |
| `@solution-architect` | Sonnet | Tư vấn kiến trúc, unblock escalations |

### Commands quan trọng

| Command | Dùng khi |
|---------|----------|
| `/bootstrap` | **Lần đầu** — phân tích project, tạo `.project-info/` |
| `/resume` | **Mở session mới** — đọc lại context và tiếp tục |
| `/handoff` | **Kết thúc session** — lưu lại để session sau tiếp tục |
| `/learn` | Cuối session — trích xuất patterns vào `.project-info/patterns.md` |
| `/parallel [tasks]` | Nhiều tasks độc lập cần chạy đồng thời |
| `/new-standard` | Thêm standard mới vào project |

### Project Manager

Thư mục `.project-manager/` lưu **toàn bộ trạng thái và lịch sử project**:

```
.project-manager/
├── status.md              ← Trạng thái tổng thể (đọc đầu tiên)
├── sessions/latest.md     ← Context session trước (đọc khi resume)
├── tasks/                 ← backlog / in-progress / done
├── logs/                  ← Activity logs theo ngày
└── knowledge/             ← decisions, blockers, context
```

**Quy tắc bắt buộc cho agents:**
- Khi nhận task → ghi vào `tasks/in-progress.md`
- Khi xong task → move sang `tasks/done.md`, cập nhật `status.md`
- Khi có quyết định kỹ thuật quan trọng → ghi vào `knowledge/decisions.md`
- Khi gặp blocker → ghi vào `knowledge/blockers.md`
- Cuối session → chạy `/handoff`

### Tools & Guides

- **GitNexus MCP**: xem `.claude/skills/gitnexus/gitnexus-guide/SKILL.md` — graph search, impact analysis, rename
- **Learned patterns**: xem `.project-info/patterns.md` — áp dụng khi implement

---

*Template version: 1.0 | Cập nhật file này khi project thay đổi*
