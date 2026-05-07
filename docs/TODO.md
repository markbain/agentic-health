# Project Roadmap

## MVP (Current Release ✅)

- [x] Core audit engine with gate pattern (`.claude`, `CLAUDE.md`, `README.md`)
- [x] Post-gate checks (git, CLAUDE.md quality signals)
- [x] Colored terminal output with progress bars
- [x] Config file support (`~/.project-health.json`)
- [x] CLI parameter overrides (`--roots`, `--depth`, `--config`)
- [x] Markdown report export (`--markdown`)
- [x] Verbose mode (`--verbose`)
- [x] GitHub deployment
- [x] CLAUDE.md documentation

## v1.1 (Testing & Polish)

- [ ] Unit tests for each check (gate, post-gate, scoring)
- [ ] Integration tests with fixture projects
- [ ] Test discovery logic (depth limits, symlinks, permissions)
- [ ] Setup/installation script
- [ ] Better error messages for common issues
- [ ] Config file example/template

## v1.2 (Features & Ergonomics)

- [ ] `--filter` flag for project name filtering
- [ ] `--min-score` flag to only show failing projects
- [ ] JSON output format (`--json`)
- [ ] Quiet mode (`--quiet`)
- [ ] Color disable flag (`--no-color`)
- [ ] Update check (notify if newer version available)

## v2 (Future Enhancements)

- [ ] Doc folder structure validation
- [ ] Makefile/script detection (build, test, lint commands)
- [ ] CI/CD config detection (.github/workflows, etc.)
- [ ] License file check
- [ ] Contributing guidelines check
- [ ] Optional: Secret pattern scanning (deferred from MVP)
- [ ] Caching to avoid re-scanning unchanged projects
- [ ] Parallel project scanning for speed

## Known Limitations / Deferred

- Secret scanning (removed from MVP, complex & risky)
- Doc folder structure (deferred - need to establish best practices first)
- Performance optimization for very large directories (1000+ projects)

## Done, Not Yet Integrated

- Created `/docs/TODO.md` ✓
