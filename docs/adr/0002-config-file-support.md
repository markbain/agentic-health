# ADR-0002: Configuration File Locations and Precedence

## Status
Accepted

## Context
The auditor needs to support customization of scanning parameters (root directories, max depth, file thresholds) without hardcoding them. Users should be able to configure globally and override via CLI.

## Decision
Implement a three-tier configuration system with clear precedence:

**1. Hardcoded defaults** (lowest priority)
- `DEFAULT_ROOTS = ["~/code", "~/Dropbox"]`
- `DEFAULT_MAX_DEPTH = 2`
- `DEFAULT_MIN_FILES = 3`

**2. Config file** (if present)
- `~/.project-health.json` (checked first)
- `~/.config/project-health/config.json` (checked second)
- Returns defaults if neither exists
- JSON format for simplicity (stdlib compatible)

**3. CLI parameters** (highest priority)
- `--roots` override (comma-separated paths)
- `--depth` override
- `--config` to specify custom config file
- Positional `path` parameter overrides roots

**Precedence Order (highest to lowest):**
1. CLI parameters
2. Config file values
3. Hardcoded defaults

## Rationale

### Why ~/.project-health.json first?
- Simple, flat location in home directory
- Easy to find and edit
- Follows Unix convention (e.g., ~/.bashrc, ~/.gitconfig)

### Why ~/.config/project-health/ second?
- XDG Base Directory Specification compliant
- Keeps home directory cleaner for users with many dotfiles
- Professional projects use this pattern

### Why JSON?
- No external dependencies (stdlib json module)
- Human-readable and editable
- Industry standard
- Simpler than YAML/TOML for this use case

### Why three-tier system?
- **Hardcoded defaults:** Tool works out-of-the-box for majority use cases
- **Config file:** Global customization without CLI flags every time
- **CLI parameters:** One-off overrides for specific runs

## Consequences

### Positive
- No setup required (works with defaults immediately)
- Personal customization via config file
- Flexible per-run overrides
- Clear, predictable precedence

### Negative
- Two config file locations to check (slightly more complex)
- Users need to create config manually (not auto-generated)
- Potential confusion if both config files exist

### Mitigations
- Document both locations clearly
- Use first config found (no ambiguity)
- Plan for future v2 feature: `project-health --init` to generate config template

## Alternatives Considered

1. **Single config location only (~/.project-health.json)**
   - Rejected: XDG spec is better practice for modern tools

2. **Auto-generate config on first run**
   - Rejected: Tool should work without setup; too much magic

3. **Environment variables only (PROJECT_HEALTH_ROOTS, etc.)**
   - Rejected: Less discoverable, harder to maintain multiple settings

4. **Config in current directory (.project-health.json)**
   - Rejected: Projects shouldn't define auditor config; auditor is external observer

## Future Enhancements
- `project-health --init`: Generate config template at correct location
- `project-health --show-config`: Display current effective configuration
- YAML support (optional, if users request)
