---
description: Khởi tạo project intelligence — phân tích architecture, extract conventions, populate .project-info/
allowed-tools: Read, Write, Edit, Glob, Grep, Bash(git *), Bash(ls *), Bash(cat *), Bash(npm *), Bash(gitnexus *)
---

# Bootstrap — Project Intelligence Setup

Phân tích project từ high-level đến atomic level, tạo ra bộ tài liệu trong `.project-info/` để tất cả agents làm việc đúng chuẩn.

---

## PHASE 0 — DETECT STATE

### Bước 1: Đã bootstrap chưa?

```bash
cat .project-info/meta.md 2>/dev/null
```

**Nếu `meta.md` TỒN TẠI** → project đã được bootstrap trước đó. Đọc nội dung và hỏi user:

```
Project này đã được bootstrap lần cuối vào [date từ meta.md].
Các thông tin hiện có:
  - architecture.md  ✓
  - conventions.md   ✓
  - stack.md         ✓
  - onboarding.md    ✓

Bạn muốn làm gì?
  [F] Full re-analysis   — phân tích lại toàn bộ (~3-5 phút)
  [P] Chỉ update patterns — cập nhật patterns.md từ code mới (~1 phút)
  [S] Skip               — dùng thông tin hiện có
```

- Chọn **S** → DỪNG, không làm gì thêm.
- Chọn **P** → bỏ qua Phases 1-4, chỉ chạy Phase 3 cho patterns.
- Chọn **F** → tiếp tục toàn bộ pipeline.

**Nếu `meta.md` KHÔNG TỒN TẠI** → chưa bootstrap, tiếp tục.

---

### Bước 2: Project có code chưa?

Chạy song song:
```bash
ls -la
cat README.md 2>/dev/null
cat package.json 2>/dev/null
git log --oneline -5 2>/dev/null
```

- **EMPTY** (không có `src/`, `app/`, hay code files) → thông báo "Project trống, bootstrap sẽ chạy lại sau khi có code", DỪNG.
- **HAS CODE** → tiếp tục.

---

### Bước 3: GitNexus status

```bash
gitnexus status
```

- **Indexed** → dùng GitNexus MCP tools cho Phase 1
- **Not indexed** → hỏi user:
  > "GitNexus chưa index. Chạy `gitnexus analyze` để phân tích tốt hơn? (Y/n)"
  - **Y**: chạy `gitnexus analyze` background, tiến hành Phase 1 bằng Glob+Read song song trong lúc chờ
  - **N**: dùng Glob + Read + Grep

---

## PHASE 1 — HIGH-LEVEL ARCHITECTURE ANALYSIS

> Đọc template tại `.project-info/architecture_template.md` để biết format cần tạo.

### 1a. Scan structure
```
Glob("src/**/*", "app/**/*", "lib/**/*", "packages/**/*")
```
Map ra:
- Top-level modules và trách nhiệm
- Layer separation (api, services, models, utils...)
- Entry points
- Config files quan trọng

### 1b. Identify architecture pattern
- **Monolith / Modular monolith / Microservices**
- **MVC / Clean Architecture / Hexagonal / Feature-based**
- **Frontend: Pages Router / App Router / SPA / SSR**

Nếu GitNexus đã indexed, dùng thêm:
```
gitnexus_query({ query: "main entry points and module structure" })
gitnexus_cypher({ query: "MATCH (m:Module) RETURN m.name, m.type LIMIT 20" })
```

### 1c. Trace data flow
Đọc entry point + route/handler files:
- Request đến từ đâu?
- Đi qua layers nào?
- Data lưu ở đâu?

### 1d. External integrations
Tìm: fetch, axios, SDK calls → liệt kê external services.

### 1e. Module Organization — detect entry point archetype

Detect cách project tổ chức modules và expose public API. Chạy theo ngôn ngữ detect được ở Phase 2:

**TypeScript / JavaScript:**
```bash
find src/ -name "index.ts" -o -name "index.js" 2>/dev/null | head -20
grep -r "export \* from\|export {" src/ --include="index.ts" --include="index.js" -l | head -5
# Đọc 2-3 file index.ts để hiểu pattern thực tế
```

**Python:**
```bash
find . -name "__init__.py" | head -20
grep -rn "^from \.\|^from \.\." $(find . -name "__init__.py" | head -10) 2>/dev/null | head -10
```

**Java / Kotlin:**
```bash
find src/ -name "*.java" -o -name "*.kt" | xargs grep -l "^package " | head -10
# Check xem có pattern facade class hay package-info không
find src/ -name "package-info.java" | head -5
```

**Xác định archetype:**

| Pattern tìm thấy | Archetype |
|---|---|
| Nhiều `index.ts` với `export * from` | **Barrel Entry Point** |
| `__init__.py` với re-exports | **Package Init** |
| Public facade class ở root package | **Package Root Class** |
| Import trực tiếp vào file, không có entry point | **Direct Import** (no barrel) |

Ghi rõ:
- Archetype name
- Entry point file (`index.ts` / `__init__.py` / ...)
- Import rule (import từ entry point hay direct?)
- Ví dụ thực tế từ codebase

**→ Tạo `.project-info/architecture.md` từ template, điền nội dung thực tế**

---

## PHASE 2 — STACK ANALYSIS

> Đọc template tại `.project-info/stack_template.md` để biết format cần tạo.

Đọc song song:
- `package.json` / `pyproject.toml` / `go.mod` / `Cargo.toml`
- `tsconfig.json` / `.eslintrc*` / `prettier.config.*`
- `Dockerfile` / `docker-compose.yml`
- `.env.example` / `.env-branch`
- `README.md`

Extract: runtime versions, framework, key dependencies, build tools, env vars.

**→ Tạo `.project-info/stack.md` từ template, điền nội dung thực tế**

---

## PHASE 3 — CONVENTION EXTRACTION (4-tier)

> Đọc template tại `.project-info/conventions_template.md` để biết format cần tạo.

### Tier 0 — Team Standards (highest priority, override mọi tier khác)

Kiểm tra `STANDARD.md` ở root project:
```bash
cat STANDARD.md 2>/dev/null
```

- **Nếu tồn tại** → đọc toàn bộ, extract tất cả rules, label `[STANDARD - từ team doc]`
- Tier này là **ground truth tuyệt đối** — nếu mâu thuẫn với tier thấp hơn, Tier 0 thắng
- **Nếu không tồn tại** → bỏ qua, tiếp tục từ Tier 1

### Tier 1 — Config files (ground truth, auto)

Đọc song song tất cả config files:
```
.eslintrc* / eslint.config.*   → naming rules, code style enforced
prettier.config.* / .prettierrc → formatting
tsconfig.json                   → strictness, path aliases
commitlint.config.*             → commit convention
```
Ghi với label `[AUTO - từ config]`

### Tier 2 — Frequency analysis (semi-auto)

Detect framework từ Phase 2, sau đó chạy grep counts:

**Next.js / React detected:**
```bash
grep -r "use client" src/ --include="*.tsx" -l | wc -l
grep -r "export default function" src/ --include="*.tsx" -l | wc -l
grep -r "export const.*=.*(" src/ --include="*.tsx" -l | wc -l
grep -rn "^export function use" src/ --include="*.ts" -l | wc -l
```

**TypeScript:**
```bash
grep -rn "^interface " src/ --include="*.ts" | wc -l
grep -rn "^type " src/ --include="*.ts" | wc -l
grep -rn "=> {" src/ --include="*.ts" | wc -l
grep -rn "^function " src/ --include="*.ts" | wc -l
```

Kết quả nhiều hơn = convention được dùng phổ biến hơn.
Ghi với label `[INFERRED - X/Y files]`

### Tier 3 — Code samples (targeted)

Lấy 5 files được sửa gần nhất:
```bash
git log --name-only --pretty="" -30 | grep -E "\.(ts|tsx|js|jsx|py)$" | sort -u | head -5
```

Đọc từng file, copy **ví dụ thực tế** cho:
- Cách đặt tên function/variable/component
- Import/export style
- Error handling pattern
- State management pattern (nếu frontend)

Ghi với label `[EXAMPLE từ code thực]`

### Tier 4 — Interview (unavoidable)

Hỏi user 3 câu quan trọng nhất mà config/code không thể tự nói lên:
1. "Business logic được đặt ở layer nào? (service / hook / component / ...)"
2. "Có conventions nào quan trọng mà new dev hay mắc lỗi không?"
3. "Có quy tắc nào về state management / data fetching không?"

Ghi với label `[CONFIRMED BY TEAM]`

### Output — Atomic convention files

Sau khi có đủ data từ Tier 0-4, tạo **hai loại output**:

**1. Summary file** (index, ngắn gọn):
```
→ Tạo .project-info/conventions.md
   Chỉ ghi key rules quan trọng nhất của mỗi domain (3-5 dòng/domain)
   Dùng như quick reference cho agents
```

**2. Atomic files** (chi tiết theo domain):
```
→ Tạo .project-info/conventions/components.md  — folder structure, naming, single responsibility
→ Tạo .project-info/conventions/hooks.md        — hook types, orchestration pattern, state management
→ Tạo .project-info/conventions/stores.md       — state library, store structure, action naming
→ Tạo .project-info/conventions/selectors.md    — selector naming, memoization pattern
→ Tạo .project-info/conventions/services.md     — HTTP client, auth, error handling, realtime
→ Tạo .project-info/conventions/testing.md      — test runner, mock pattern, file location, naming
→ Tạo .project-info/conventions/api.md          — response format, status codes, versioning
→ Tạo .project-info/conventions/design.md       — typography, color system, spacing, animation (xem hướng dẫn Phase 3.5 bên dưới)
```

Chỉ tạo file nào có đủ data (ít nhất 2 rules có evidence). Bỏ qua domain không áp dụng cho project này.

Mỗi rule trong atomic file phải ghi rõ nguồn: `[STANDARD]` / `[AUTO]` / `[INFERRED]` / `[EXAMPLE]` / `[CONFIRMED]`

---

## PHASE 3.5 — DESIGN SYSTEM EXTRACTION

> Chỉ thực hiện nếu project là frontend (React/Next.js/Vue detected ở Phase 2).
> Đọc `.claude/skills/frontend-design/SKILL.md` để biết các categories cần extract.
> Output: `.project-info/conventions/design.md`

### Nguồn dữ liệu (đọc song song)

```bash
# CSS tokens / theme
cat src/app/shadcn-theme.css 2>/dev/null    # Shadcn theme variables
cat tailwind.config.* 2>/dev/null           # Tailwind custom theme
grep -r "css\|style\|theme" src/ --include="*.css" -l | head -5

# Design system config
cat components.json 2>/dev/null             # Shadcn config
grep -r "@font-face\|font-family\|--font" src/ --include="*.css" | head -20
grep -r "fontSize\|fontFamily\|colors\|spacing" tailwind.config.* 2>/dev/null
```

### Categories cần extract (theo frontend-design skill framework)

**Typography:**
- Font families đang dùng (display font, body font, mono font)
- Font size scale (text-sm, text-base, text-lg... hay custom tokens)
- Font weight conventions (headings, body, labels)

**Color System:**
- Primary / secondary / accent colors (từ CSS variables)
- Background / foreground / muted / card / destructive...
- Dark mode support? (`.dark` class hay `data-theme`?)
- Tên biến CSS thực tế (`--primary`, `--background`...)

**Spacing & Sizing:**
- Spacing scale (Tailwind default hay custom?)
- Border radius conventions (`--radius` token)
- Container / max-width patterns

**Motion & Animation:**
- Animation library đang dùng (Framer Motion, CSS only, Motion?)
- Transition patterns thường gặp trong code
- Duration standards

**Component Library:**
- UI library đang dùng (Shadcn, Radix, custom?)
- Component import pattern (`@/components/ui/button`)
- Icon library (lucide-react, heroicons...)

### Output format cho `conventions/design.md`

```markdown
# Design System Conventions

> Extracted by /bootstrap. Agents đọc file này trước khi viết UI.
> Kết hợp với .claude/skills/frontend-design/SKILL.md để viết UI đúng chuẩn project.

## Typography
- Display font: [font name] — dùng cho headings h1-h2
- Body font: [font name] — dùng cho text thường
- Mono font: [font name] — dùng cho code
- Scale: [tailwind scale / custom tokens]

## Color System
CSS variables (định nghĩa tại [file path]):
- --background: [value]
- --foreground: [value]
- --primary: [value]
- --primary-foreground: [value]
- --muted: [value]
- --accent: [value]
- [các biến quan trọng khác]
Dark mode: [có / không, cách implement]

## Spacing & Radius
- Spacing scale: [Tailwind default / custom]
- Border radius: --radius = [value], classes: rounded-[sm/md/lg]
- Container: [max-width pattern]

## Motion
- Library: [CSS only / Framer Motion / Motion / tw-animate-css]
- Standard transition: [pattern từ code thực]
- Animation classes thường dùng: [list]

## Component Library
- UI Library: [Shadcn UI / Radix / custom]
- Import pattern: import { Button } from "@/components/ui/button"
- Icon library: [lucide-react / heroicons / ...]
- Theme config: [file path]

## Styling Rules
- Styling approach: [Tailwind utility classes]
- Class order convention: [layout → spacing → color → typography → state]
- Conditional classes: [cn() utility từ shadcn]
```

---

## PHASE 4 — ONBOARDING

> Đọc template tại `.project-info/onboarding_template.md` để biết format cần tạo.

Tổng hợp:
1. Setup commands từ README + package.json scripts
2. Entry points quan trọng nhất
3. "Không làm" list từ conventions + gotchas phát hiện ở Phase 3

**→ Tạo `.project-info/onboarding.md` từ template, điền nội dung thực tế**

---

## PHASE 5 — UPDATE CLAUDE.md

Cập nhật section **Project Overview** và **Tech Stack** trong `CLAUDE.md` — thay thế các placeholder `[...]` bằng thông tin thực tế.

---

## PHASE 6 — CREATE meta.md + REPORT

### Tạo `.project-info/meta.md`:

```markdown
# Bootstrap Meta

**Bootstrapped:** YYYY-MM-DD HH:MM
**By:** Claude [model]
**Stack:** [e.g. Next.js 15 + TypeScript + Tailwind]
**Architecture:** [e.g. App Router, Feature-based]
**GitNexus:** [Indexed / Not indexed]

## Files generated
- architecture.md  ✓
- conventions.md   ✓
- stack.md         ✓
- onboarding.md    ✓
- patterns.md      (empty — populated by /learn over time)

## Re-bootstrap
Run `/bootstrap` again to update. Choose:
- [F] Full re-analysis
- [P] Patterns only
- [S] Skip
```

### Báo cáo cho user:

```
## Bootstrap Complete ✓

- Stack:        [Next.js 15 + TypeScript + ...]
- Architecture: [Feature-based App Router]
- GitNexus:     [Indexed — 738 symbols]
- Conventions:  [X auto + Y inferred + Z confirmed]

Files created in .project-info/:
  architecture.md / conventions.md / stack.md / onboarding.md / meta.md

Next: commit .project-info/ vào repo để team dùng chung.
```
