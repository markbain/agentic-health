# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Agentic Health Monitor** — A Python CLI tool (`project-health`) that walks `~/code` and `~/Dropbox` directories, discovers projects, and audits each one for agent-readiness. Outputs a colored terminal report with per-project scores (0-100) and actionable failure/warning summaries.

The tool helps ensure projects are properly configured for AI agent collaboration: they have git tracking, `.gitignore`, a README.md, a CLAUDE.md file, and don't leak secrets.

## Key Architecture

### Discovery Phase
- Walk two root directories: `~/code` and `~/Dropbox`
- Treat a directory as a **project** if:
  - It contains a `.git` folder, OR
  - It's a direct child of either root with 3+ files/folders
- Never recurse deeper than 2 levels from roots (skip nested `node_modules`, `vendor`, etc.)
- Skip hidden directories (`.git`, `.github`), `node_modules`, `vendor`

### Audit Phase — Checks & Scoring

Each project gets a 0-100 score based on weighted checks:

**Git Checks** (weight: 55 total)
- `.git` present (15 pts) — fail if missing
- `.gitignore` present & non-empty (5 pts) — warn if missing
- `.env` not tracked (20 pts) — fail if tracked
- No secret patterns in committed files (20 pts) — fail if found

**Documentation** (weight: 45 total)
- README present (5 pts) — warn if missing
- README substantive: >100 words (5 pts) — warn if stub
- CLAUDE.md present (15 pts) — warn if missing
- CLAUDE.md substantive: >150 words (5 pts) — warn if stub
- CLAUDE.md contains *what-is-this* signals (keywords: `purpose`, `about`, `overview`, `this project`) (4 pts) — warn if absent
- CLAUDE.md contains *conventions/rules* signals (keywords: `convention`, `rule`, `always`, `never`, `don't`, `prefix`) (3 pts) — warn if absent
- CLAUDE.md contains *agent caution* signals (keywords: `avoid`, `do not`, `careful`, `warning`, `important`) (3 pts) — warn if absent

**Secret Pattern Detection**
Scan committed files for:
- AWS key format: `AKIA[0-9A-Z]{16}`
- OpenAI key format: `sk-[a-zA-Z0-9]{32,}`
- PEM headers: `-----BEGIN RSA PRIVATE KEY-----`, etc.
- `.env` values that look like tokens (long hex/base64 strings)
- `Bearer ` followed by a long string

Use pragmatic patterns, not an exhaustive regex library.

**Informational Detections** (not scored)
- Presence of `.cursorrules`, `copilot-instructions.md`, `.aider.conf`, `AGENTS.md`

### Output

Terminal output with progress bars and color (via `colorama`, graceful fallback):
```
~/code/bff-project          ████████░░  78/100  2 warnings
~/code/ggsa                 ██████████  95/100
~/Dropbox/beato-properties  ████░░░░░░  42/100  3 failures, 1 warning
```

Summary section lists all failures and warnings by project. The `--verbose` flag shows all checks. The `--markdown` flag writes the report to `~/project-health-report.md`.

## Commands

```bash
# Audit both roots (~/code and ~/Dropbox)
project-health

# Audit a single project
project-health ~/code/bff

# Write markdown report to ~/project-health-report.md
project-health --markdown

# Show all checks (including passes)
project-health --verbose

# Combined
project-health ~/code --markdown --verbose
```

## Development & Testing

Once implementation begins, include:
- Unit tests for each check (git, readme, claude.md, secrets)
- Test fixtures with projects in various states (missing git, stub README, leaked secrets, etc.)
- Integration test that audits a temporary test directory
- Test the discovery logic against nested structures (ensure depth limit is respected)

## Installation

The tool is distributed as:
- Single executable file: `~/bin/project-health`
- Shebang: `#!/usr/bin/env python3`
- No external dependencies beyond stdlib; `colorama` is optional for color output
- Installation: `chmod +x ~/bin/project-health` and ensure `~/bin` is on `$PATH`

No config files or setup step beyond chmod.

## Important Notes for Agents

- **Avoid false positives on secrets**: Use the short pragmatic patterns list, not a security library. `.env` files are only flagged if tracked by git; untracked `.env` files are not audited.
- **Don't recurse infinitely**: The 2-level depth limit is critical to avoid auditing auto-generated files (node_modules, vendor, .git subtrees). Enforce this strictly.
- **CLAUDE.md keyword detection is soft**: Keywords are case-insensitive hints. A project with substantial content but no keywords should not fail — only warn.
- **Be mindful of performance**: Walking and scanning two large directories (~/code, ~/Dropbox) could take time if they contain deep nesting or large files. Consider progress indication for the discovery phase.
