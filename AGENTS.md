# Agent Instructions

> This file mirrors key sections of CLAUDE.md for tools that read AGENTS.md (OpenAI Codex, etc.).
> Keep in sync with CLAUDE.md when agent system changes.

## Project Overview

**Project:** [Tên project]
**Stack:** [e.g. Next.js 15 + TypeScript + Tailwind]

## Coding Conventions

- [Naming: camelCase variables, PascalCase components, kebab-case files]
- [No `any` in TypeScript — use `unknown` if needed]
- [Functional programming preferred, avoid mutation]
- [Small functions, single responsibility]
- [Error handling at boundaries (API routes, event handlers)]

## Git Conventions

Conventional Commits format:
```
feat(scope): description
fix(scope): description
chore(scope): description
```

## Agent Roles

| Agent | Role |
|-------|------|
| PM | Orchestrator — route all requests |
| Explorer | Read/understand codebase only |
| Coder | Implement features |
| Reviewer | Code quality review |
| Debugger | Deep debugging |

## Important Rules

- Run `/bootstrap` first on a new project to generate `.project-info/`
- Always use `@pm` as entry point for tasks
- Read `.project-info/conventions/` before writing code for a domain
- Check `.project-manager/status.md` at session start
