# Python Notes

Python is a versatile, high-level programming language that supports multiple paradigms: object-oriented, functional, and procedural. Its emphasis on readability and simplicity makes it ideal for data analysis, scripting, web development, and scientific computing.

## Overview

**Strengths:**
- Clear, readable syntax ("executable pseudocode")
- Rich ecosystem (PyPI has 500,000+ packages)
- Excellent data science tools (pandas, numpy, scipy)
- Strong community and documentation
- REPL for interactive development

**Philosophy:** "There should be one—and preferably only one—obvious way to do it." (Zen of Python)

## Functional Programming Patterns

### Built-in Functions

Python provides several built-in functions for functional-style programming:

```python
# map: Apply function to each element
numbers = [1, 2, 3, 4, 5]
doubled = list(map(lambda x: x * 2, numbers))
# [2, 4, 6, 8, 10]

# filter: Select elements by predicate
evens = list(filter(lambda x: x % 2 == 0, numbers))
# [2, 4]

# zip: Combine sequences
names = ['Alice', 'Bob', 'Carol']
ages = [25, 30, 35]
pairs = list(zip(names, ages))
# [('Alice', 25), ('Bob', 30), ('Carol', 35)]

# enumerate: Index and element pairs
for i, name in enumerate(names):
    print(f"{i}: {name}")

# any / all: Check conditions
has_even = any(x % 2 == 0 for x in numbers)  # True
all_positive = all(x > 0 for x in numbers)   # True

# sorted: Sort with key function
words = ['apple', 'pie', 'zoo', 'a']
by_length = sorted(words, key=len)
# ['a', 'pie', 'zoo', 'apple']
```

### Comprehensions (Pythonic!)

Comprehensions are the preferred Python way to create sequences:

```python
# List comprehension
squares = [x**2 for x in range(10)]

# With condition
even_squares = [x**2 for x in range(10) if x % 2 == 0]

# Multiple iterables
pairs = [(x, y) for x in range(3) for y in range(3)]

# Dict comprehension
word_lengths = {word: len(word) for word in ['cat', 'dog', 'elephant']}
# {'cat': 3, 'dog': 3, 'elephant': 8}

# Set comprehension
unique_lengths = {len(word) for word in ['cat', 'dog', 'bird', 'ant']}
# {3, 4}

# Nested comprehension
matrix = [[i * j for j in range(3)] for i in range(3)]
# [[0, 0, 0], [0, 1, 2], [0, 2, 4]]
```

### Generator Expressions (Lazy Evaluation)

Generators compute values on demand, saving memory:

```python
# Generator expression (uses parentheses)
squares_gen = (x**2 for x in range(1000000))  # Creates generator, no memory used yet

# Use in iteration
for square in squares_gen:
    if square > 100:
        break  # Only computed up to here

# Use with functions that accept iterables
total = sum(x**2 for x in range(100))  # Efficient, no list created
```

### Functional Libraries

#### functools

```python
from functools import reduce, partial, lru_cache, singledispatch

# reduce: Fold/accumulate
product = reduce(lambda acc, x: acc * x, [1, 2, 3, 4], 1)  # 24

# partial: Fix some arguments
from operator import mul
double = partial(mul, 2)
double(5)  # 10

# lru_cache: Memoization
@lru_cache(maxsize=None)
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n-1) + fibonacci(n-2)

# singledispatch: Function overloading by type
@singledispatch
def process(data):
    raise NotImplementedError("Unsupported type")

@process.register(list)
def _(data):
    return sum(data)

@process.register(str)
def _(data):
    return data.upper()
```

#### itertools

Provides efficient iterators for looping:

```python
import itertools as it

# Infinite iterators
count = it.count(10, 2)  # 10, 12, 14, 16, ...
cycle = it.cycle([1, 2, 3])  # 1, 2, 3, 1, 2, 3, ...
repeat = it.repeat(5, 3)  # 5, 5, 5

# Combinatorics
combinations = it.combinations([1, 2, 3], 2)  # (1,2), (1,3), (2,3)
permutations = it.permutations([1, 2, 3], 2)  # (1,2), (1,3), (2,1), ...
product = it.product([1, 2], ['a', 'b'])  # (1,'a'), (1,'b'), (2,'a'), (2,'b')

# Useful utilities
chain = it.chain([1, 2], [3, 4])  # 1, 2, 3, 4
groupby = it.groupby('AAABBBCC')  # Groups consecutive equal elements
islice = it.islice(range(10), 2, 8, 2)  # Like [2:8:2]
takewhile = it.takewhile(lambda x: x < 5, [1, 2, 3, 6, 7])  # 1, 2, 3
accumulate = it.accumulate([1, 2, 3, 4], lambda acc, x: acc + x)  # 1, 3, 6, 10
```

#### toolz (Advanced FP)

```python
from toolz import compose, pipe, curry, thread_first

# compose: Right-to-left function composition
process = compose(sum, list, filter, lambda x: x > 0)
# Equivalent to: sum(list(filter(lambda x: x > 0, data)))

# pipe: Left-to-right (more readable)
from toolz import pipe
result = pipe(
    data,
    lambda x: filter(lambda y: y > 0, x),
    list,
    sum
)

# curry: Transform function to curried form
@curry
def add(x, y):
    return x + y

add5 = add(5)
add5(3)  # 8

# thread_first: Like pipe but threads through first argument
from toolz import thread_first
result = thread_first(
    [1, 2, 3],
    (map, lambda x: x * 2),
    list,
    sum
)
```

#### funcy

Another FP library with convenient utilities:

```python
from funcy import select, where, project, walk, rcompose

# select: Filter with predicate
evens = select(lambda x: x % 2 == 0, range(10))

# where: Filter dict-like objects
users = [{'name': 'Alice', 'age': 25}, {'name': 'Bob', 'age': 30}]
adults = where(users, age__gte=18)

# project: Select fields from dicts
names = project(users, ['name'])

# walk: Map over nested structures
doubled = walk(lambda x: x * 2, [1, [2, 3], [[4]]])

# rcompose: Compose functions left-to-right
process = rcompose(
    lambda x: x * 2,
    lambda x: x + 1,
    str
)
```

## Type System

### Type Hints (PEP 484)

Python 3.5+ supports optional type annotations:

```python
# Basic types
def greet(name: str) -> str:
    return f"Hello, {name}"

# Multiple types (Union)
from typing import Union
def process(value: Union[int, str]) -> str:
    return str(value)

# Optional (Union with None)
from typing import Optional
def find_user(id: int) -> Optional[dict]:
    return None  # or user dict

# Collections
from typing import List, Dict, Set, Tuple
def process_items(items: List[int]) -> Dict[str, int]:
    return {"count": len(items)}

# Generics
from typing import TypeVar, Generic
T = TypeVar('T')
def first(items: List[T]) -> Optional[T]:
    return items[0] if items else None

# Callable
from typing import Callable
def apply(func: Callable[[int], str], value: int) -> str:
    return func(value)

# Protocols (structural typing, PEP 544)
from typing import Protocol
class Drawable(Protocol):
    def draw(self) -> None: ...

def render(obj: Drawable) -> None:
    obj.draw()  # Any object with draw() method works
```

### mypy (Static Type Checking)

```bash
# Install
pip install mypy

# Check types
mypy script.py

# Configuration (mypy.ini or setup.cfg)
[mypy]
python_version = 3.10
warn_return_any = True
warn_unused_configs = True
disallow_untyped_defs = True
```

```python
# Type checking errors
def add(x: int, y: int) -> int:
    return x + y

result: str = add(1, 2)  # mypy error: incompatible types
```

### Modern Type Hints (Python 3.10+)

```python
# Union with | operator
def process(value: int | str) -> str:
    return str(value)

# Type aliases
UserId = int
UserName = str
User = dict[str, str | int]

def get_user(id: UserId) -> User:
    return {"id": id, "name": "Alice"}
```

## Data Analysis

### pandas

The de facto standard for data manipulation:

```python
import pandas as pd

# Create DataFrame
df = pd.DataFrame({
    'name': ['Alice', 'Bob', 'Carol'],
    'age': [25, 30, 35],
    'city': ['NYC', 'LA', 'SF']
})

# Read data
df = pd.read_csv('data.csv')
df = pd.read_parquet('data.parquet')

# Common operations
filtered = df[df['age'] > 25]
sorted_df = df.sort_values('age', ascending=False)
grouped = df.groupby('city')['age'].mean()

# Method chaining
result = (df
    .query('age > 25')
    .assign(age_group=lambda x: x['age'] // 10)
    .groupby('age_group')['name']
    .count())

# Apply functions
df['name_upper'] = df['name'].apply(str.upper)
df['age_squared'] = df['age'].map(lambda x: x ** 2)

# Merge/join
merged = pd.merge(df1, df2, on='id', how='left')

# Pivot
pivot = df.pivot_table(
    values='sales',
    index='date',
    columns='product',
    aggfunc='sum'
)
```

### polars

Modern alternative to pandas, focusing on speed and expressiveness:

```python
import polars as pl

# Create DataFrame (eager)
df = pl.DataFrame({
    'name': ['Alice', 'Bob', 'Carol'],
    'age': [25, 30, 35],
})

# Lazy evaluation (recommended for large data)
lazy_df = pl.scan_csv('data.csv')

# Expression syntax
result = (lazy_df
    .filter(pl.col('age') > 25)
    .select([
        pl.col('name'),
        pl.col('age'),
        (pl.col('age') * 2).alias('age_doubled')
    ])
    .group_by('name')
    .agg([
        pl.col('age').mean().alias('avg_age')
    ])
    .collect())  # Execute lazy query

# When to use polars vs pandas
# - Polars: Larger datasets, performance-critical, clean slate
# - Pandas: Existing code, wide library support, complex operations
```

**See:** [Polars Regex Cheat Sheet](./polars-regex-cheatsheet.md)

### DuckDB Integration

DuckDB can query Python DataFrames with zero-copy via Arrow:

```python
import duckdb
import pandas as pd

# Create DataFrame
df = pd.DataFrame({'a': [1, 2, 3], 'b': [4, 5, 6]})

# Query DataFrame directly (zero-copy via Arrow)
result = duckdb.query("SELECT * FROM df WHERE a > 1").df()

# Register as persistent view
con = duckdb.connect(':memory:')
con.register('my_table', df)
result = con.execute("SELECT * FROM my_table").fetchall()

# Query parquet directly
result = duckdb.query("""
    SELECT *
    FROM 'data/*.parquet'
    WHERE value > 100
""").df()
```

**See:** [DuckDB Notes](./duckdb.md)

## Iterators & Generators

### Generator Functions

Functions that use `yield` to produce values lazily:

```python
# Simple generator
def count_up_to(n):
    i = 0
    while i < n:
        yield i
        i += 1

# Use like any iterable
for num in count_up_to(5):
    print(num)  # 0, 1, 2, 3, 4

# Generator with state
def fibonacci():
    a, b = 0, 1
    while True:
        yield a
        a, b = b, a + b

# Take first 10 Fibonacci numbers
import itertools as it
first_10 = list(it.islice(fibonacci(), 10))

# yield from (delegate to another generator)
def flatten(nested_list):
    for item in nested_list:
        if isinstance(item, list):
            yield from flatten(item)
        else:
            yield item

list(flatten([1, [2, 3], [[4, 5]]]))  # [1, 2, 3, 4, 5]
```

### Generator Pipelines

Chain generators for memory-efficient data processing:

```python
def read_lines(filename):
    with open(filename) as f:
        for line in f:
            yield line.strip()

def filter_comments(lines):
    for line in lines:
        if not line.startswith('#'):
            yield line

def parse_numbers(lines):
    for line in lines:
        yield int(line)

# Compose pipeline (lazy, memory-efficient)
numbers = parse_numbers(filter_comments(read_lines('data.txt')))
total = sum(numbers)  # Only loads one line at a time
```

### Iterator Protocol

```python
# Implementing iterator
class Counter:
    def __init__(self, max):
        self.max = max
        self.current = 0
    
    def __iter__(self):
        return self
    
    def __next__(self):
        if self.current >= self.max:
            raise StopIteration
        self.current += 1
        return self.current

# Use it
for i in Counter(5):
    print(i)  # 1, 2, 3, 4, 5
```

## Python-Specific Idioms

### Context Managers

Ensure cleanup with `with` statement:

```python
# Built-in: file handling
with open('file.txt', 'r') as f:
    data = f.read()
# File automatically closed

# Custom context manager (class-based)
class Timer:
    def __enter__(self):
        self.start = time.time()
        return self
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        print(f"Elapsed: {time.time() - self.start:.2f}s")

with Timer():
    # ... code to time ...

# Custom context manager (function-based)
from contextlib import contextmanager

@contextmanager
def temporary_value(var, temp_value):
    original = var.value
    var.value = temp_value
    try:
        yield var
    finally:
        var.value = original
```

### Decorators

Modify function behavior:

```python
# Simple decorator
def log_calls(func):
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}")
        result = func(*args, **kwargs)
        print(f"Finished {func.__name__}")
        return result
    return wrapper

@log_calls
def add(x, y):
    return x + y

add(1, 2)  # Prints: Calling add, Finished add

# Decorator with arguments
def repeat(times):
    def decorator(func):
        def wrapper(*args, **kwargs):
            for _ in range(times):
                result = func(*args, **kwargs)
            return result
        return wrapper
    return decorator

@repeat(3)
def greet(name):
    print(f"Hello, {name}")

# Preserve function metadata
from functools import wraps

def my_decorator(func):
    @wraps(func)  # Copies __name__, __doc__, etc.
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper
```

### Argument Patterns

```python
# Variable positional arguments
def sum_all(*args):
    return sum(args)

sum_all(1, 2, 3, 4)  # 10

# Variable keyword arguments
def print_info(**kwargs):
    for key, value in kwargs.items():
        print(f"{key}: {value}")

print_info(name="Alice", age=25)

# Combined
def func(required, *args, **kwargs):
    print(f"Required: {required}")
    print(f"Args: {args}")
    print(f"Kwargs: {kwargs}")

func(1, 2, 3, a=4, b=5)

# Keyword-only arguments (after *)
def greet(name, *, enthusiastic=False):
    msg = f"Hello, {name}"
    if enthusiastic:
        msg += "!"
    return msg

greet("Alice", enthusiastic=True)  # OK
greet("Alice", True)  # Error: must use keyword

# Positional-only arguments (before /)
def divide(x, y, /):
    return x / y

divide(10, 2)  # OK
divide(x=10, y=2)  # Error: must be positional
```

### Unpacking

```python
# Sequence unpacking
a, b = (1, 2)
first, *rest = [1, 2, 3, 4]  # first=1, rest=[2,3,4]
*start, last = [1, 2, 3, 4]  # start=[1,2,3], last=4

# Dict unpacking
dict1 = {'a': 1, 'b': 2}
dict2 = {'c': 3}
merged = {**dict1, **dict2}  # {'a': 1, 'b': 2, 'c': 3}

# Function arguments
def func(a, b, c):
    return a + b + c

args = [1, 2, 3]
func(*args)  # Equivalent to func(1, 2, 3)

kwargs = {'a': 1, 'b': 2, 'c': 3}
func(**kwargs)  # Equivalent to func(a=1, b=2, c=3)
```

### List Slicing

```python
lst = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# Basic slicing [start:stop:step]
lst[2:5]      # [2, 3, 4]
lst[:3]       # [0, 1, 2]
lst[7:]       # [7, 8, 9]
lst[::2]      # [0, 2, 4, 6, 8]

# Negative indices
lst[-1]       # 9 (last element)
lst[-3:]      # [7, 8, 9] (last three)
lst[:-2]      # [0, 1, 2, 3, 4, 5, 6, 7] (all but last two)

# Reverse
lst[::-1]     # [9, 8, 7, 6, 5, 4, 3, 2, 1, 0]

# Copy idiom
copy = lst[:]

# Assignment to slice
lst[2:5] = [20, 30, 40]  # Replace elements
lst[2:5] = []  # Delete elements
```

## REPL Workflow

### IPython Features

IPython provides an enhanced interactive Python shell:

```python
# Magic commands
%timeit sum(range(1000))  # Time execution
%run script.py  # Run script in current namespace
%load script.py  # Load script into cell
%who  # List variables
%whos  # Detailed variable info
%history  # Command history
%pwd  # Print working directory
%cd /path  # Change directory

# Shell commands
!ls  # Run shell command
files = !ls  # Capture output

# Inline help
?function_name  # Quick help
??function_name  # Source code

# Tab completion
import pandas as pd
pd.Dat<TAB>  # Completes to DataFrame

# History navigation
# Ctrl+R: Reverse search
# Up/Down: Navigate history

# Automatic parentheses
len [1,2,3]  # Works without ()
```

### Jupyter Integration

```python
# Cell magic
%%time
# ... code to time ...

%%capture output
print("This is captured")

%%writefile script.py
# Writes cell contents to file

# Display functions
from IPython.display import display, HTML, Image
display(df)  # Rich display of DataFrame
display(HTML("<h1>Hello</h1>"))

# Inline plots
%matplotlib inline
import matplotlib.pyplot as plt
plt.plot([1, 2, 3])
```

### Debugging

#### pdb (Python Debugger)

```python
import pdb

def buggy_function(x):
    pdb.set_trace()  # Breakpoint
    return x * 2

# Python 3.7+ built-in breakpoint()
def buggy_function(x):
    breakpoint()  # Same as pdb.set_trace()
    return x * 2

# Common pdb commands:
# n (next): Execute next line
# s (step): Step into function
# c (continue): Continue execution
# p variable: Print variable
# l: List source code
# q: Quit debugger
# up/down: Navigate call stack
```

#### ipdb (IPython Debugger)

```bash
pip install ipdb
```

```python
import ipdb
ipdb.set_trace()  # Breakpoint with IPython features
```

## Comparison with Other Languages

### Python vs Rust

| Aspect | Python | Rust |
|--------|--------|------|
| **Speed** | Interpreted, slower | Compiled, very fast |
| **Safety** | Runtime errors | Compile-time guarantees |
| **Memory** | Garbage collected | Ownership model |
| **Types** | Dynamic | Static |
| **Learning Curve** | Gentle | Steep |
| **Use Cases** | Scripting, data analysis, prototyping | Systems programming, performance-critical |
| **Concurrency** | GIL limits true parallelism | Fearless concurrency |

**When to choose:**
- **Python**: Rapid development, data analysis, glue code
- **Rust**: Performance-critical, systems programming, safety-critical

**See:** [Rust Notes](./rust-notes.md)

### Python vs Scheme

| Aspect | Python | Scheme |
|--------|--------|--------|
| **Syntax** | Indentation-based | S-expressions |
| **Paradigm** | Multi-paradigm | Functional-first |
| **Type System** | Dynamic, optional hints | Dynamic |
| **Metaprogramming** | Limited (decorators, metaclasses) | Powerful (macros) |
| **Ecosystem** | Massive (PyPI) | Small |
| **Learning Resources** | Abundant | Academic-focused |

**When to choose:**
- **Python**: Production work, libraries, data science
- **Scheme**: Learning FP, language-oriented programming, education

**See:** [Scheme](./scheme.md), [Language-Oriented Programming](./language-oriented-programming.md)

### Python vs JavaScript

| Aspect | Python | JavaScript |
|--------|--------|-----------|
| **Primary Domain** | Backend, data science | Frontend, backend (Node.js) |
| **Syntax** | Clean, readable | C-like, flexible |
| **Async Model** | asyncio (explicit) | Promises, async/await (pervasive) |
| **Package Manager** | pip, conda | npm, yarn |
| **Type System** | Optional hints (mypy) | Optional (TypeScript) |

**Common ground:** Both are dynamic, multi-paradigm, have active communities.

## Common Gotchas

### Mutable Default Arguments

```python
# WRONG: Default is evaluated once at function definition
def append_to(element, lst=[]):
    lst.append(element)
    return lst

append_to(1)  # [1]
append_to(2)  # [1, 2] - UNEXPECTED!

# RIGHT: Use None and create new list
def append_to(element, lst=None):
    if lst is None:
        lst = []
    lst.append(element)
    return lst
```

### Late Binding Closures

```python
# WRONG: All closures reference same variable
funcs = [lambda: i for i in range(3)]
[f() for f in funcs]  # [2, 2, 2] - UNEXPECTED!

# RIGHT: Use default argument to capture current value
funcs = [lambda i=i: i for i in range(3)]
[f() for f in funcs]  # [0, 1, 2]

# Or use functools.partial
from functools import partial
funcs = [partial(lambda x: x, i) for i in range(3)]
```

### Global Interpreter Lock (GIL)

Python's GIL prevents true parallel execution of threads:

```python
# Threading doesn't help CPU-bound tasks
import threading

def cpu_task():
    sum(range(1000000))

# These run sequentially due to GIL
threads = [threading.Thread(target=cpu_task) for _ in range(4)]
for t in threads:
    t.start()

# SOLUTION: Use multiprocessing for CPU-bound work
import multiprocessing as mp

with mp.Pool(4) as pool:
    results = pool.map(lambda x: sum(range(1000000)), range(4))

# NOTE: I/O-bound tasks are fine with threading (GIL released during I/O)
```

### Mutable vs Immutable Types

```python
# Immutable: int, float, str, tuple, frozenset
x = 5
y = x
x = 10  # Creates new object, y still 5

# Mutable: list, dict, set
lst1 = [1, 2, 3]
lst2 = lst1  # Same object!
lst1.append(4)
print(lst2)  # [1, 2, 3, 4] - UNEXPECTED!

# To copy: use .copy() or [:]
lst2 = lst1.copy()  # Shallow copy
import copy
lst2 = copy.deepcopy(lst1)  # Deep copy (nested structures)
```

### String Immutability

```python
# WRONG: Inefficient (creates new string each time)
result = ""
for s in strings:
    result += s  # O(n²) time complexity

# RIGHT: Use join
result = "".join(strings)  # O(n) time complexity
```

## Best Practices

### PEP 8 Style Guide

```python
# Naming conventions
class MyClass:  # PascalCase
    pass

def my_function():  # snake_case
    pass

MY_CONSTANT = 42  # UPPER_SNAKE_CASE

# Indentation: 4 spaces (never tabs)
# Line length: 79 characters (PEP 8), 88-100 (common in practice)
# Imports: Standard library, third-party, local (separated by blank line)
```

### Docstrings

```python
def calculate_mean(numbers):
    """
    Calculate the arithmetic mean of a list of numbers.
    
    Args:
        numbers (List[float]): List of numbers to average.
    
    Returns:
        float: The arithmetic mean.
    
    Raises:
        ValueError: If the list is empty.
    
    Examples:
        >>> calculate_mean([1, 2, 3])
        2.0
    """
    if not numbers:
        raise ValueError("Cannot calculate mean of empty list")
    return sum(numbers) / len(numbers)
```

### Testing Patterns

```python
# pytest (recommended)
def test_addition():
    assert add(2, 3) == 5

def test_division_by_zero():
    with pytest.raises(ZeroDivisionError):
        divide(10, 0)

# Fixtures
@pytest.fixture
def sample_data():
    return [1, 2, 3, 4, 5]

def test_sum(sample_data):
    assert sum(sample_data) == 15

# unittest (standard library)
import unittest

class TestMath(unittest.TestCase):
    def test_addition(self):
        self.assertEqual(add(2, 3), 5)
    
    def setUp(self):
        self.data = [1, 2, 3]
    
    def tearDown(self):
        pass
```

### Virtual Environments

```bash
# venv (built-in)
python -m venv myenv
source myenv/bin/activate  # Linux/Mac
myenv\Scripts\activate  # Windows
pip install package
deactivate

# conda
conda create -n myenv python=3.10
conda activate myenv
conda install numpy pandas
conda deactivate

# poetry (modern dependency management)
poetry new myproject
poetry add pandas
poetry install
poetry run python script.py
```

## Connections

- [Functional Programming](./fp.md) - FP concepts and patterns
- [DuckDB](./duckdb.md) - Query DataFrames with SQL
- [Polars Regex](./polars-regex-cheatsheet.md) - Regex in Polars
- [Data Analysis Patterns](./data-analysis-patterns.md) - Cross-tool workflows

## Resources

### Books
- *Fluent Python* (Ramalho) - Advanced Python techniques
- *Python Cookbook* (Beazley & Jones) - Practical recipes
- *Effective Python* (Slatkin) - Best practices

### Documentation
- [Official Python Docs](https://docs.python.org/)
- [Real Python](https://realpython.com/) - Tutorials
- [Python Package Index (PyPI)](https://pypi.org/)

### Style
- [PEP 8](https://pep8.org/) - Style guide
- [PEP 20](https://www.python.org/dev/peps/pep-0020/) - Zen of Python

## Summary

Python excels at:
- **Readability**: Clear, expressive syntax
- **Versatility**: Multi-paradigm, broad application
- **Ecosystem**: Unmatched library support
- **Productivity**: Rapid development, powerful REPL

Key strengths for data work:
- pandas/polars for data manipulation
- DuckDB integration for SQL on DataFrames
- Rich functional programming support
- Excellent REPL (IPython/Jupyter)

Trade-offs:
- Slower than compiled languages (but often fast enough)
- GIL limits CPU parallelism
- Dynamic typing (can use mypy for checking)

**Philosophy:** "Simple is better than complex. Complex is better than complicated." Use Python's strength—clarity—to write code that's easy to understand and maintain.
