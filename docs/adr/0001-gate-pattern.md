# ADR-0001: Gate Pattern for Project-Readiness Checks

## Status
Accepted

## Context
The auditor needs to check multiple aspects of project readiness (git setup, documentation, configuration). Some checks are expensive (file scanning, word counting) while others are fast (directory existence).

We want to provide quick feedback to users on the most critical issues without wasting time on projects that clearly aren't ready for agent collaboration.

## Decision
Implement a **gate pattern** where projects must pass three critical checks before proceeding to optional checks:

**Gate Checks (fail = stop immediately):**
1. `.claude` directory exists
2. `CLAUDE.md` file exists
3. `README.md` file exists

**Post-Gate Checks (optional, don't stop audit):**
- Git files (`.git`, `.gitignore`)
- CLAUDE.md quality signals (word count, keywords)

If any gate check fails, the project fails with a score proportional to how far it got, and remaining checks are skipped.

## Rationale
- **User experience:** Prioritizes the most important signals (agent configuration & documentation)
- **Performance:** Avoids expensive checks on projects that clearly aren't configured
- **Clear feedback:** Users see immediately what's blocking them (missing `.claude` or CLAUDE.md)
- **Flexibility:** Post-gate checks warn but don't fail, allowing projects with missing git to still score well

## Consequences
- **Positive:** Fast feedback, clear priority, better UX
- **Negative:** Projects might stop auditing and miss other issues (e.g., README.md fails before we check git setup)
- **Acceptable trade-off:** Users can run `--verbose` to see what would have failed if gates had passed

## Alternatives Considered
1. **All-or-nothing scoring:** Check everything, penalize all missing items equally
   - Rejected: Users wouldn't know what's most critical to fix first

2. **Soft gating (warnings only):** Check everything but highlight critical issues
   - Rejected: Unclear prioritization, longer scan times, less satisfying UX

3. **Configurable gates:** Let users define which checks gate
   - Rejected: Over-engineered for MVP, can be added in v2 if needed
