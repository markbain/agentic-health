# Configuration Precedence

Always maintain this order (highest to lowest priority):

1. **CLI parameters** (`--roots`, `--depth`, `--config`)
2. **Config file values** (`~/.project-health.json` or `~/.config/project-health/config.json`)
3. **Hardcoded defaults** (`DEFAULT_ROOTS`, `DEFAULT_MAX_DEPTH`, `DEFAULT_MIN_FILES`)

## Pattern

```python
# CLI (highest priority)
if args.depth is not None:
    max_depth = args.depth
# Config file (middle priority)
elif "max_depth" in config:
    max_depth = config["max_depth"]
# Hardcoded default (lowest priority)
else:
    max_depth = DEFAULT_MAX_DEPTH
```

Never invert this order or allow lower-priority sources to override higher ones. This ensures CLI can always override config, and config can always override defaults.
