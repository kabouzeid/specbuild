# specbuild

[![PyPI version](https://img.shields.io/pypi/v/specbuild.svg)](https://pypi.org/project/specbuild/)

Registry-based object builder for nested configuration dictionaries.

Docs: https://kabouzeid.github.io/specbuild/

## Install

```bash
pip install specbuild
```

## Quick start

```python
from specbuild import register, build

@register()
class Encoder:
    def __init__(self, channels: int):
        self.channels = channels

cfg = {"type": "Encoder", "channels": 64}
model = build(cfg)
```

Works well with [`cfgx`](https://github.com/kabouzeid/cfgx) for loading config dictionaries.
