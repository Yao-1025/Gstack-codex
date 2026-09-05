# gstack development

This repository originally targeted Claude Code. The Codex port keeps the same workflow ideas, skill names, and browser tooling, but the active project instructions for Codex now live here.

## Commands

```bash
bun install             # install dependencies
bun test                # run free tests (browse + snapshot + skill validation)
bun run test:evals      # run evals: Codex CLI judge + Codex CLI E2E
bun run test:e2e        # run E2E tests only
bun run dev <cmd>       # run CLI in dev mode, e.g. bun run dev goto https://example.com
bun run build           # generate docs + compile binaries
bun run gen:skill-docs  # regenerate SKILL.md files from templates
bun run skill:check     # health dashboard for all skills
bun run dev:skill       # watch mode: auto-regen + validate on change
```

## Project structure

```text
gstack/
├── browse/          # Headless browser CLI (Playwright)
├── scripts/         # Build + DX tooling
├── test/            # Skill validation + eval tests
├── qa/              # QA skill
├── review/          # Code review skill
├── ship/            # Release workflow skill
├── design-consultation/
├── plan-ceo-review/
├── plan-eng-review/
├── plan-design-review/
├── qa-design-review/
├── qa-only/
├── retro/
├── document-release/
├── setup            # Build binary + register skill symlinks
├── SKILL.md         # Generated from SKILL.md.tmpl
├── SKILL.md.tmpl    # Edit templates, then regenerate
└── package.json
```

## Skill workflow

- `SKILL.md` files are generated from `.tmpl` templates.
- Edit the `.tmpl` file, then run `bun run gen:skill-docs`.
- Commit both the template and the generated file together.
- Keep skill bodies concise. Variant-specific detail belongs in helper files or referenced docs, not repeated prose.

## Writing templates

- Treat templates as prompt documents, not shell scripts.
- Keep bash blocks self-contained. Do not rely on state carrying across blocks.
- Prefer plain English control flow over nested bash logic.
- Use dynamic branch detection; do not hardcode `main`.
- Treat legacy `AskUserQuestion` steps as conditional requests for genuinely missing information, not mandatory pauses. Reuse existing answers and authorization; use an available Codex question tool or concise chat only when the unresolved choice matters. Routine parameters can be inferred.

## Browser rules

- Use the bundled `browse` CLI or the `/browse` workflow, not `mcp__claude-in-chrome__*`.
- If you add a browse command, update `browse/src/commands.ts` and rebuild.
- If you add a snapshot flag, update `browse/src/snapshot.ts` and rebuild.

## Local dev mode

- `bin/dev-setup` creates `.codex/skills/gstack` as a symlink to this repo so Codex reads skills directly from the working tree.
- `bin/dev-teardown` removes the local symlinks and falls back to `~/.codex/skills/gstack`.
- During large refactors, avoid half-generated skill files in a live symlinked install.

## Testing and evals

- Tier 1: static validation and unit/integration tests via `bun test`
- Tier 2: E2E via `codex exec --json`
- Tier 3: LLM-as-judge via `codex exec --json`

When an eval fails, do not label it "pre-existing" unless you can reproduce the same failure on the base branch.

## Docs and release notes

- `CHANGELOG.md` is user-facing release notes, not contributor notes.
- Lead with what users can now do.
- Keep internal refactors and contributor-only details compressed.

## Legacy note

`CLAUDE.md` is kept for backward compatibility and historical context. For Codex-driven work in this repository, prefer `AGENTS.md`.
