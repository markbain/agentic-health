# Python Type Hints Compatibility

Use `from __future__ import annotations` at the top of all Python files.

## Why

This enables modern type hint syntax (`list[str]`, `dict[str, int]`) on Python 3.8.
Without it, requires Python 3.9+.

## Standard

```python
#!/usr/bin/env python3

from __future__ import annotations

import argparse
import sys
...
```

Required for compatibility with systems that ship Python 3.8 as default.
