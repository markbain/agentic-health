# Architecture Decision Records

This directory contains architecture decision records (ADRs) for the project-health auditor.

## Index

| ADR | Title | Status |
|-----|-------|--------|
| [0001](./0001-gate-pattern.md) | Gate pattern for project-readiness checks | Accepted |
| [0002](./0002-config-file-support.md) | Configuration file locations and precedence | Accepted |
| [0003](./0003-mvp-scope-decisions.md) | MVP scope: secret scanning and doc structure checks | Accepted |

## ADR Template

```markdown
# ADR-NNNN: [Title]

## Status
[Accepted | Proposed | Superseded | Deprecated]

## Context
[Why did we need to make this decision? What forces influenced it?]

## Decision
[What did we decide to do? What is the chosen solution?]

## Rationale
[Why is this the best choice? What trade-offs did we consider?]

## Consequences
[What are the positive and negative consequences of this decision?]

## Alternatives Considered
[What other options were evaluated?]
```

## How to Create a New ADR

1. Create a new file: `NNNN-kebab-case-title.md` (increment the number)
2. Use the template above
3. Add entry to the index table in this README
4. Commit and push

See existing ADRs for examples.
