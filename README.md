# Project Health Auditor

A Python CLI tool that walks your project directories and audits them for agent-readiness. It checks for essential configuration files, documentation, and git setup to ensure projects are ready for AI agent collaboration.

## Features

- Audits projects in `~/code` and `~/Dropbox`
- Scores projects 0-100 based on agent-readiness
- Colored terminal output with progress bars
- Optional markdown report export
- Fast discovery with 2-level depth limit

## Installation

```bash
chmod +x ~/bin/project-health
```

Ensure `~/bin` is on your `$PATH`.

## Usage

```bash
project-health                  # audit both roots
project-health ~/code/myproject # audit single project
project-health --verbose        # show all checks
project-health --markdown       # write report to ~/project-health-report.md
```
