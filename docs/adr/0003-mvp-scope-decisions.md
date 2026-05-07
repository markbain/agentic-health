# ADR-0003: MVP Scope Decisions - Secret Scanning and Doc Structure

## Status
Accepted

## Context
The original build plan included two significant features:
1. **Secret pattern scanning:** Detect AWS keys, OpenAI tokens, PEM headers, Bearer tokens
2. **Doc folder structure validation:** Ensure projects have organized documentation

During design (grill session), these were evaluated for MVP inclusion.

## Decision
**Remove from MVP, defer to v2:**
- Secret scanning: Removed entirely from initial release
- Doc folder structure: Defer until best practices are established

Keep MVP focused on **three gate checks + CLAUDE.md quality signals**.

## Rationale

### Secret Scanning Removal
- **Complexity:** Requires file content scanning across potentially large repos, risk of false positives
- **False positives:** Pattern matching is imprecise (Bearer tokens appear in legitimate contexts)
- **Risk:** Over-flagging secrets when project isn't actually vulnerable
- **User feedback:** Project owner (Mark) indicated this wasn't a priority for personal tool use
- **Better tools exist:** Dedicated secret scanning tools (git-secrets, truffleHog) are more comprehensive

### Doc Structure Deferral
- **Unknown best practices:** No established convention yet for what "good doc structure" looks like
- **Project-specific:** Different project types (Python lib, Node app, config repo) need different structures
- **Premature:** Better to gather data from real projects first, then formalize checks
- **v2 planning:** Once we see patterns across audited projects, we can codify them

## Consequences

### Positive
- Simpler MVP, faster to implement and test
- Fewer false positives, better UX
- Focused on core signal: is project set up for agent collaboration?
- Leaves room for future extensions without breaking scoring model

### Negative
- Won't catch credential leaks (but users can run dedicated tools)
- No doc structure validation (but projects can still pass if gates are met)
- Secret scanning feature request might come up in v2 (manageable)

## Alternatives Considered

1. **Include secret scanning in MVP with warnings-only mode**
   - Rejected: False positives would hurt trust in tool; warnings-only is confusing

2. **Require users to define doc structure in config**
   - Rejected: Too early, no standard to build against

3. **Scan only committed files (not uncommitted)**
   - Rejected: Still risky and complex for MVP scope

## Follow-up
- v2: Collect data on successful project structures, then codify
- v2: Consider integration with existing secret scanning tools
- v3+: Evaluate optional/experimental checks that can be disabled
