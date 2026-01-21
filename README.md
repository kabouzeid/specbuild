# specbuild

[![PyPI version](https://img.shields.io/pypi/v/specbuild.svg)](https://pypi.org/project/specbuild/)

Registry-based object builder for nested configuration dictionaries.

Docs: https://karimknaebel.github.io/specbuild/

## Install

```bash
pip install specbuild
```

## Simple example

```python
from specbuild import register, build

@register()
class Encoder:
    def __init__(self, channels: int):
        self.channels = channels

cfg = {"type": "Encoder", "channels": 64}
model = build(cfg)
```

## Advanced example (all features)

```python
from specbuild import Registry, build, register

@register()
class Encoder:
    def __init__(self, channels: int):
        self.channels = channels

@register("Head")
class MLPHead:
    def __init__(self, width: int, out_dim: int):
        self.width = width
        self.out_dim = out_dim

@register()
class Classifier:
    def __init__(self, encoder, head, layers, output_dir):
        self.encoder = encoder
        self.head = head
        self.layers = layers
        self.output_dir = output_dir

@register()
def loss_fn(pred, target, weight):
    return (pred - target) * weight

optim_registry = Registry()

@optim_registry.register()
class ToyOptim:
    def __init__(self, params, lr):
        self.params = params
        self.lr = lr

cfg = {
    "type": "Classifier",
    "encoder": {"type": "Encoder", "channels": 64},
    "head": {"type": "Head", "width": 128, "out_dim": 10},
    "layers": [
        {"type": "Encoder", "channels": 32},
        {"type": "Encoder", "channels": 64},
    ],
    "output_dir": {"type": "pathlib.Path", "*": ["./runs/exp1"]},
}

model = build(cfg)
loss = build({"type": "partial:loss_fn", "weight": 0.5})(pred, target)
optimizer = build(
    {"type": "ToyOptim", "params": [1, 2, 3], "lr": 3e-4},
    registry=optim_registry,
)
logger = build(
    {"type": "logging.getLogger", "*": ["train"]},
    registry=None,
)
```

Works well with [`cfgx`](https://github.com/karimknaebel/cfgx) for loading config dictionaries.
