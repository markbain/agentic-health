# Project Health Auditor — Build Plan

## Overview

A Python CLI script that walks `~/code` and `~/Dropbox`, finds project directories, and audits each one for agent-readiness. Outputs a terminal report with per-project scores and actionable failures.

---

## Discovery

- Walk both roots: `~/code` and `~/Dropbox`
- A directory is treated as a **project** if it contains a `.git` folder, OR is a direct child of either root with at least 3 files/folders in it
- Do not recurse deeper than 2 levels from either root (avoids auditing nested `node_modules`, `vendor`, sub-directories etc.)
- Skip hidden directories, `node_modules`, `vendor`, `.git` itself

---

## Checks and Scoring

Each check resolves to **pass / warn / fail** with a weight. Final score is 0-100.

### Git

| Check | Result |
|---|---|
| `.git` present | fail if missing; skip secret checks if so |
| `.gitignore` present and non-empty | warn if missing |
| `.env` exists AND is tracked by git | fail (secret leak risk) |
| Committed text files contain secret patterns (see below) | fail if found |

**Secret patterns to scan for:**

- AWS key format (`AKIA[0-9A-Z]{16}`)
- OpenAI key format (`sk-[a-zA-Z0-9]{32,}`)
- PEM headers (`-----BEGIN RSA PRIVATE KEY-----` etc.)
- `.env` values that look like tokens (long hex or base64 strings assigned to a key)
- `Bearer ` followed by a long string

Use a short pragmatic list. Do not use an exhaustive regex library.

### README

| Check | Result |
|---|---|
| `README.md`, `README`, or `README.txt` present | warn if missing |
| Word count > 100 | warn if stub |

### CLAUDE.md

| Check | Result |
|---|---|
| `CLAUDE.md` present | warn if missing |
| Word count > 150 | warn if stub |
| Contains *what is this* signal: keywords `purpose`, `about`, `overview`, `this project` | warn if absent |
| Contains *conventions/rules* signal: keywords `convention`, `rule`, `always`, `never`, `don't`, `prefix` | warn if absent |
| Contains *agent caution* signal: keywords `avoid`, `do not`, `careful`, `warning`, `important` | warn if absent |

### Other Agent Config Files (informational only, not scored)

Detect and note presence of: `.cursorrules`, `copilot-instructions.md`, `.aider.conf`, `AGENTS.md`

---

## Scoring Weights

Assign weights so that the git secret check and CLAUDE.md presence have the most impact. README and CLAUDE.md quality checks are lower weight. Suggested distribution (adjust as needed):

| Check | Weight |
|---|---|
| `.git` present | 15 |
| `.gitignore` present | 5 |
| No `.env` tracked | 20 |
| No secret patterns | 20 |
| README present | 5 |
| README substantive | 5 |
| CLAUDE.md present | 15 |
| CLAUDE.md substantive | 5 |
| CLAUDE.md: what-is-this | 4 |
| CLAUDE.md: conventions | 3 |
| CLAUDE.md: agent caution | 3 |
| **Total** | **100** |

---

## Output Format

Terminal output using colour via `colorama` (graceful fallback to no-colour if not installed).

```
~/code/bff-project          ████████░░  78/100  2 warnings
~/code/ggsa                 ██████████  95/100
~/Dropbox/beato-properties  ████░░░░░░  42/100  3 failures, 1 warning
...

FAILURES AND WARNINGS
~/Dropbox/beato-properties
  ✗ .env is tracked by git
  ✗ CLAUDE.md missing
  ⚠ README is a stub (< 100 words)
```

The `--verbose` flag shows all checks (including passes) for every project.

The `--markdown` flag writes the same report to `~/project-health-report.md`.

---

## CLI Interface

```bash
project-health                  # audit both roots (~/code and ~/Dropbox)
project-health ~/code/bff       # audit a single specific project
project-health --markdown       # also write report to ~/project-health-report.md
project-health --verbose        # show all checks, not just failures and warnings
```

---

## Installation

- Single file: `~/bin/project-health` (no extension)
- Shebang: `#!/usr/bin/env python3`
- No dependencies beyond stdlib + optional `colorama`
- Make executable: `chmod +x ~/bin/project-health`
- Ensure `~/bin` is on `$PATH`

No config files. No install step beyond chmod.

---

## File Structure

```
~/bin/project-health    # single executable script
```
