# Discovery Depth Limit

Discovery must never exceed 2 levels from root directories (~/code, ~/Dropbox).

## Why This Matters

- Prevents recursing into `node_modules`, `vendor`, `.git` subdirectories
- Prevents auditing auto-generated or cache directories
- Keeps scan time predictable and consistent

## Implementation

Enforce in `discover_projects()`:
```python
if depth > MAX_DEPTH:
    return
```

Never allow configuration to set depth > 2 without careful consideration of performance impact.
