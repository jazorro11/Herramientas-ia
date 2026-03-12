# CLAUDE.md — AI Assistant Guide for Herramientas-ia

This file provides context, conventions, and workflows for AI assistants (Claude, Copilot, etc.) working in this repository. Read it before making any changes.

---

## Project Overview

**Herramientas-ia** (Spanish: "AI Tools") is a collection of utilities, integrations, and experiments built around AI/LLM capabilities. The project is in its initial stages; structure and tooling will evolve as features are added.

---

## Repository Structure (Intended)

```
Herramientas-ia/
├── CLAUDE.md              # This file
├── README.md              # Human-facing project documentation
├── .gitignore             # Git ignore rules
├── src/                   # Source code
│   ├── tools/             # Individual AI tool implementations
│   ├── utils/             # Shared utilities
│   └── integrations/      # Third-party service integrations
├── tests/                 # Test files mirroring src/ structure
├── docs/                  # Extended documentation
└── scripts/               # Build, dev, and utility scripts
```

> As the project grows, update this section to reflect the actual structure.

---

## Development Setup

### Prerequisites

Document prerequisites here as the stack is decided. Common examples:
- Node.js >= 20 / Python >= 3.11 / etc.
- API keys (e.g., `ANTHROPIC_API_KEY`)

### Environment Variables

Copy `.env.example` to `.env.local` and fill in values:

```bash
cp .env.example .env.local
```

Never commit `.env.local` or any file containing real secrets.

### Install Dependencies

```bash
# Node.js projects
npm install

# Python projects
pip install -r requirements.txt
```

### Run the Project

```bash
# Update this command once an entry point exists
npm run dev   # or: python main.py
```

---

## Git Workflow

### Branches

- `main` — stable, production-ready code
- `dev` or `develop` — integration branch for features
- `claude/<task-id>` — branches created by AI assistants for specific tasks
- `feature/<name>` — human-created feature branches
- `fix/<name>` — bug fix branches

### Commit Messages

Use the Conventional Commits format:

```
<type>(<scope>): <short description>

[optional body]
```

**Types:** `feat`, `fix`, `docs`, `refactor`, `test`, `chore`, `ci`

**Examples:**
```
feat(tools): add summarization tool using Claude API
fix(utils): handle empty input in text splitter
docs: update setup instructions in README
```

### Pull Requests

- Keep PRs focused on a single concern.
- Include a summary of what changed and why.
- Reference related issues with `Closes #<issue>`.

---

## Code Conventions

### General

- Prefer clear, readable code over clever one-liners.
- Do not add comments to self-evident code; only comment non-obvious logic.
- Keep functions small and single-purpose.
- Validate inputs at system boundaries (user input, external API responses). Trust internal contracts.

### Naming

- Files/directories: `kebab-case` (e.g., `text-splitter.ts`)
- Functions/variables: `camelCase` (JS/TS) or `snake_case` (Python)
- Classes/types: `PascalCase`
- Constants: `UPPER_SNAKE_CASE`

### Error Handling

- Surface errors early; avoid silent failures.
- Use structured error types rather than plain strings.
- Log errors with enough context to diagnose the problem.

### Security

- Never hardcode API keys, passwords, or secrets in source files.
- Sanitize and validate all external input.
- Avoid `eval()` and dynamic code execution with user-supplied data.
- Use parameterized queries for any database interactions.

---

## Testing

- Place tests alongside or mirroring source files in `tests/`.
- Name test files `<module>.test.ts` (JS/TS) or `test_<module>.py` (Python).
- Each public function should have at least one test.
- Run tests before committing: `npm test` or `pytest`.

---

## Working with the Claude (Anthropic) API

When integrating Anthropic's API:

- Use the official SDK (`@anthropic-ai/sdk` for JS/TS, `anthropic` for Python).
- Store `ANTHROPIC_API_KEY` in environment variables, never in code.
- Default to the latest capable model unless a specific version is needed.
  - Current recommended: `claude-sonnet-4-6` (Sonnet) or `claude-opus-4-6` (Opus)
- Always handle API errors and rate limits gracefully.
- Keep system prompts in dedicated files under `src/prompts/` for easy review.

---

## AI Assistant Instructions

When working in this repository as an AI assistant:

1. **Read before writing.** Always read existing files before modifying them.
2. **Minimal changes.** Only change what is necessary for the task. Do not refactor unrelated code.
3. **No speculative features.** Do not add error handling, flags, or abstractions for hypothetical future requirements.
4. **Update this file.** If you add a new tool, integration, or workflow that future assistants should know about, update the relevant section of this CLAUDE.md.
5. **Branch discipline.** Work on the designated `claude/<task-id>` branch. Never push directly to `main`.
6. **Commit clarity.** Write descriptive commit messages using the Conventional Commits format above.
7. **Security first.** Never include real credentials, tokens, or secrets in any committed file.
8. **Ask, don't assume.** If the task is ambiguous or the approach unclear, surface the question rather than guessing.

---

## Useful Commands Reference

| Task | Command |
|------|---------|
| Install deps | `npm install` / `pip install -r requirements.txt` |
| Run dev server | `npm run dev` |
| Run tests | `npm test` / `pytest` |
| Lint | `npm run lint` / `flake8 .` |
| Format | `npm run format` / `black .` |
| Build | `npm run build` |

> Update this table as scripts are added to `package.json` or `scripts/`.

---

## Documentation

- Keep `README.md` updated with setup steps and usage examples.
- Add detailed documentation to `docs/` for complex features.
- Use JSDoc / docstrings for public APIs.

---

*Last updated: 2026-03-12. Update this date and relevant sections whenever the project structure or conventions change.*
