# Python Pattern Matching (PEP 634 / PEP 636)

> Requires Python 3.10+

Structural pattern matching lets you branch on the **shape and content** of data
using `match`/`case`. It is far more powerful than a chain of `if`/`elif`.

---

## Table of Contents

1. [Literal Patterns](#1-literal-patterns)
2. [Capture Patterns](#2-capture-patterns)
3. [Wildcard Pattern](#3-wildcard-pattern)
4. [OR Patterns](#4-or-patterns)
5. [Sequence Patterns](#5-sequence-patterns)
6. [Mapping Patterns](#6-mapping-patterns)
7. [Class Patterns](#7-class-patterns)
8. [Guards](#8-guards)
9. [AS Patterns](#9-as-patterns)
10. [Dotted Names vs Captures](#10-dotted-names-vs-captures)
11. [Nested Patterns](#11-nested-patterns)
12. [Real-World Examples](#12-real-world-examples)

---

## 1. Literal Patterns

Match exact values: strings, ints, floats, booleans, `None`.

```python
def http_status(code):
    match code:
        case 200:
            return "OK"
        case 201:
            return "Created"
        case 404:
            return "Not Found"
        case 500:
            return "Internal Server Error"
        case _:
            return f"Unknown ({code})"

print(http_status(200))   # OK
print(http_status(418))   # Unknown (418)
```

```python
def check_flag(flag):
    match flag:
        case True:
            print("enabled")
        case False:
            print("disabled")
        case None:
            print("not set")

check_flag(True)   # enabled
check_flag(None)   # not set
```

---

## 2. Capture Patterns

A bare name in a `case` always **captures** the matched value into that variable.

```python
def describe(value):
    match value:
        case 0:
            print("zero")
        case n:                    # captures anything into n
            print(f"got {n}")

describe(0)    # zero
describe(42)   # got 42
describe("hi") # got hi
```

> **Rule:** bare names are captures, not comparisons.
> Use dotted names (`Status.OK`) to compare against existing values.

---

## 3. Wildcard Pattern

`_` is the universal catch-all. It matches everything but **never binds** a name.

```python
match command:
    case "quit":
        quit()
    case "help":
        show_help()
    case _:
        print(f"unrecognized command")
```

Use `_` inside nested patterns too:

```python
match point:
    case (_, 0):   # any x, y must be 0
        print("on x-axis")
    case (0, _):   # x must be 0, any y
        print("on y-axis")
```

---

## 4. OR Patterns

Combine alternatives with `|`. All alternatives must bind the **same names**.

```python
def classify_status(code):
    match code:
        case 200 | 201 | 202:
            return "success"
        case 301 | 302:
            return "redirect"
        case 400 | 401 | 403 | 404:
            return "client error"
        case 500 | 502 | 503:
            return "server error"
        case _:
            return "unknown"
```

```python
match command:
    case "q" | "quit" | "exit":
        sys.exit(0)
    case "h" | "help" | "?":
        print_help()
```

---

## 5. Sequence Patterns

Match lists and tuples by structure. Works with any sequence (not dicts/strings).

```python
def handle_command(tokens):
    match tokens:
        case []:
            print("empty input")
        case ["go", direction]:
            print(f"going {direction}")
        case ["go", direction, speed]:
            print(f"going {direction} at {speed}")
        case ["pick", "up", item]:
            print(f"picking up {item}")
        case ["drop", *items]:          # *rest captures remaining
            print(f"dropping {items}")
        case [cmd, *_]:
            print(f"unknown command: {cmd}")

handle_command(["go", "north"])           # going north
handle_command(["go", "south", "fast"])   # going south at fast
handle_command(["drop", "sword", "key"])  # dropping ['sword', 'key']
```

### Splat (`*`) rules

```python
match items:
    case [first, *rest]:          # at least one element
        ...
    case [first, *_, last]:       # first and last, ignore middle
        ...
    case [*_]:                    # any sequence (including empty)
        ...
```

---

## 6. Mapping Patterns

Match dictionaries by key-value structure. Extra keys are **ignored** by default.

```python
def handle_event(event):
    match event:
        case {"type": "click", "button": btn, "x": x, "y": y}:
            print(f"click {btn} at ({x},{y})")
        case {"type": "keypress", "key": k}:
            print(f"key pressed: {k}")
        case {"type": "scroll", "delta": d}:
            print(f"scroll delta: {d}")
        case {"type": t}:
            print(f"unhandled event type: {t}")
        case {}:
            print("empty event")

handle_event({"type": "keypress", "key": "Enter", "modifiers": []})
# key pressed: Enter  (extra 'modifiers' key is ignored)
```

### Capture remaining keys with `**rest`

```python
match config:
    case {"host": host, "port": port, **rest}:
        connect(host, port)
        print(f"extra config: {rest}")
```

---

## 7. Class Patterns

Match instances by type and attributes. Works with any class.

### Using `__match_args__`

Dataclasses and named tuples set `__match_args__` automatically, enabling
positional matching:

```python
from dataclasses import dataclass

@dataclass
class Point:
    x: float
    y: float

@dataclass
class Circle:
    center: Point
    radius: float

@dataclass
class Rectangle:
    top_left: Point
    bottom_right: Point

def describe_shape(shape):
    match shape:
        case Circle(center=Point(x=0, y=0), radius=r):
            print(f"circle at origin, radius={r}")
        case Circle(center=Point(x=cx, y=cy), radius=r):
            print(f"circle at ({cx},{cy}), radius={r}")
        case Rectangle(top_left=Point(x=x1, y=y1),
                       bottom_right=Point(x=x2, y=y2)):
            w, h = abs(x2 - x1), abs(y2 - y1)
            print(f"rectangle {w}x{h}")

describe_shape(Circle(Point(0, 0), 5))        # circle at origin, radius=5
describe_shape(Rectangle(Point(0, 4), Point(3, 0)))  # rectangle 3x4
```

### Positional matching via `__match_args__`

```python
@dataclass
class Point:
    x: float
    y: float
    # __match_args__ = ("x", "y") is added automatically by @dataclass

match Point(1, 2):
    case Point(0, 0):
        print("origin")
    case Point(x, 0):          # positional: x=x, y=0
        print(f"x-axis at {x}")
    case Point(x, y):
        print(f"point at ({x},{y})")
```

### Manual `__match_args__` for regular classes

```python
class Color:
    __match_args__ = ("r", "g", "b")

    def __init__(self, r, g, b):
        self.r, self.g, self.b = r, g, b

match Color(255, 0, 0):
    case Color(255, 0, 0):
        print("red")
    case Color(r, g, b) if r == g == b:
        print(f"grey ({r})")
    case Color(r, g, b):
        print(f"color ({r},{g},{b})")
```

---

## 8. Guards

Add an `if` condition after a pattern. The case only matches if the pattern
matches **and** the guard is truthy.

```python
def categorize(value):
    match value:
        case int(n) if n < 0:
            return "negative int"
        case int(n) if n == 0:
            return "zero"
        case int(n):
            return "positive int"
        case float(f) if f != f:   # NaN check
            return "NaN"
        case float(f):
            return "float"
        case str(s) if len(s) == 0:
            return "empty string"
        case str(s):
            return f"string of length {len(s)}"

print(categorize(-3))    # negative int
print(categorize(0))     # zero
print(categorize(3.14))  # float
print(categorize(""))    # empty string
```

```python
match point:
    case Point(x, y) if x == y:
        print("on y=x diagonal")
    case Point(x, y) if x == -y:
        print("on y=-x diagonal")
    case Point(x, y):
        print("off diagonal")
```

---

## 9. AS Patterns

Bind the matched value to a name **while** also checking the pattern.
Syntax: `pattern as name`

```python
def process(data):
    match data:
        case [int(x), int(y)] as pair:
            print(f"int pair {pair}, sum={x+y}")
        case {"x": int(x), "y": int(y)} as point:
            print(f"point dict {point}")
        case str() as s if s.startswith("http"):
            print(f"URL: {s}")

process([3, 4])                   # int pair [3, 4], sum=7
process({"x": 1, "y": 2, "z": 3}) # point dict {'x': 1, 'y': 2, 'z': 3}
process("https://example.com")    # URL: https://example.com
```

---

## 10. Dotted Names vs Captures

This is the most common gotcha.

```python
# BAD: 'red' is treated as a capture variable, not the value Color.red
match color:
    case red:        # captures ANYTHING into 'red' — always matches!
        ...

# GOOD: dotted name is looked up as a value
from enum import Enum

class Color(Enum):
    RED = 1
    GREEN = 2
    BLUE = 3

match color:
    case Color.RED:      # compares against Color.RED
        print("red")
    case Color.GREEN:
        print("green")
    case Color.BLUE:
        print("blue")
```

```python
# Also works with module-level constants via dotted access
import http

match response.status:
    case http.HTTPStatus.OK:
        ...
    case http.HTTPStatus.NOT_FOUND:
        ...
```

---

## 11. Nested Patterns

Patterns compose freely — any pattern can be nested inside another.

```python
from dataclasses import dataclass
from typing import Optional

@dataclass
class User:
    name: str
    role: str
    address: Optional[dict]

def greet(user):
    match user:
        case User(name=n, role="admin", address={"city": city}):
            print(f"Welcome, admin {n} from {city}")
        case User(name=n, role="admin"):
            print(f"Welcome, admin {n} (no address)")
        case User(name=n, role=r, address={"country": "US"}):
            print(f"US user {n} ({r})")
        case User(name=n, role=r):
            print(f"User {n} ({r})")
```

```python
# Nested sequences + mappings
match message:
    case {"headers": [first, *_], "body": {"data": [int(x), *_]}}:
        print(f"first header={first}, first data item={x}")
```

---

## 12. Real-World Examples

### A. CLI Command Parser

```python
import sys

def run_cli(argv):
    match argv[1:]:
        case []:
            print("Usage: tool <command> [args]")
        case ["--help"] | ["-h"]:
            print("Help text...")
        case ["--version"] | ["-v"]:
            print("v1.0.0")
        case ["add", *files] if files:
            for f in files:
                add_file(f)
        case ["remove", filename]:
            remove_file(filename)
        case ["config", "set", key, value]:
            set_config(key, value)
        case ["config", "get", key]:
            print(get_config(key))
        case ["config", *_]:
            print("Usage: tool config [set|get] ...")
        case [unknown, *_]:
            print(f"Unknown command: {unknown}", file=sys.stderr)
            sys.exit(1)

run_cli(["tool", "config", "set", "timeout", "30"])
```

---

### B. JSON / API Response Handler

```python
def handle_api_response(response):
    match response:
        case {"status": "ok", "data": {"users": [*users]}}:
            return [process_user(u) for u in users]

        case {"status": "ok", "data": data}:
            return data

        case {"status": "error", "code": 401 | 403, "message": msg}:
            raise PermissionError(msg)

        case {"status": "error", "code": 429, "retry_after": int(seconds)}:
            import time
            time.sleep(seconds)
            return None   # caller should retry

        case {"status": "error", "message": msg}:
            raise RuntimeError(f"API error: {msg}")

        case _:
            raise ValueError(f"Unexpected response shape: {response}")
```

---

### C. Simple Expression Evaluator (AST walker)

```python
# AST node types
from dataclasses import dataclass
from typing import Union

@dataclass
class Num:
    value: float

@dataclass
class BinOp:
    op: str
    left: "Expr"
    right: "Expr"

@dataclass
class Neg:
    operand: "Expr"

Expr = Union[Num, BinOp, Neg]

def evaluate(expr: Expr) -> float:
    match expr:
        case Num(value=v):
            return v
        case Neg(operand=e):
            return -evaluate(e)
        case BinOp(op="+", left=l, right=r):
            return evaluate(l) + evaluate(r)
        case BinOp(op="-", left=l, right=r):
            return evaluate(l) - evaluate(r)
        case BinOp(op="*", left=l, right=r):
            return evaluate(l) * evaluate(r)
        case BinOp(op="/", left=l, right=r):
            rval = evaluate(r)
            if rval == 0:
                raise ZeroDivisionError
            return evaluate(l) / rval
        case BinOp(op=op):
            raise ValueError(f"Unknown operator: {op}")

# (2 + 3) * -4  =>  -20
expr = BinOp("*", BinOp("+", Num(2), Num(3)), Neg(Num(4)))
print(evaluate(expr))   # -20.0
```

---

### D. State Machine

```python
from dataclasses import dataclass
from enum import Enum, auto

class State(Enum):
    IDLE    = auto()
    RUNNING = auto()
    PAUSED  = auto()
    STOPPED = auto()

@dataclass
class Event:
    kind: str
    payload: dict = None

def transition(state: State, event: Event) -> State:
    match (state, event):
        case (State.IDLE, Event(kind="start")):
            print("starting...")
            return State.RUNNING
        case (State.RUNNING, Event(kind="pause")):
            print("pausing...")
            return State.PAUSED
        case (State.RUNNING, Event(kind="stop")):
            print("stopping...")
            return State.STOPPED
        case (State.PAUSED, Event(kind="resume")):
            print("resuming...")
            return State.RUNNING
        case (State.PAUSED, Event(kind="stop")):
            print("stopping from pause...")
            return State.STOPPED
        case (State.STOPPED, _):
            print("already stopped, ignoring event")
            return State.STOPPED
        case (s, Event(kind=k)):
            print(f"invalid transition: {s} + {k}")
            return state

state = State.IDLE
state = transition(state, Event("start"))    # starting...
state = transition(state, Event("pause"))    # pausing...
state = transition(state, Event("resume"))   # resuming...
state = transition(state, Event("stop"))     # stopping...
```

---

## Quick Reference

| Pattern | Example | What it does |
|---|---|---|
| Literal | `case 42:` | Exact value match |
| Capture | `case x:` | Binds matched value to `x` |
| Wildcard | `case _:` | Matches anything, binds nothing |
| OR | `case a \| b:` | Matches either alternative |
| Sequence | `case [a, b]:` | Matches 2-element sequence |
| Sequence splat | `case [first, *rest]:` | Head + tail capture |
| Mapping | `case {"k": v}:` | Dict with key "k", value to `v` |
| Mapping rest | `case {"k": v, **r}:` | Remaining keys to `r` |
| Class | `case Foo(x=v):` | Instance of Foo, attr x to v |
| Guard | `case x if x > 0:` | Pattern + boolean condition |
| AS | `case [a, b] as pair:` | Pattern + bind whole match |
| Dotted | `case Color.RED:` | Value comparison, not capture |

---

## Gotchas Summary

- **Bare names capture.** `case x` always matches — it's not a comparison.
- **Use dotted names for constants:** `case Status.OK`, not `case OK`.
- **No fall-through.** Only the first matching case runs.
- **Guards don't affect variable binding.** Variables from the pattern are
  bound even if the guard fails (but the case body won't execute).
- **Mapping patterns are partial.** Extra dict keys don't cause a mismatch.
- **Sequence patterns require a sequence.** Strings and dicts won't match
  `case [a, b]`.
