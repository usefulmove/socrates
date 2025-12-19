# Polars Regular Expressions


The `polars.Series.str.contains()` function uses regular expressions (regex) **by default** to check if strings in a Series match a given pattern.

The behavior is controlled by the `literal` parameter.

### Default Behavior: Regex Matching (`literal=False`)

When you use the default setting (`literal=False`), the input `pattern` is interpreted as a regular expression.

  * **Function:** It checks if the regex pattern matches **any part** of the string. This is equivalent to Python's `re.search()`, not `re.match()` (which only checks from the beginning).
  * **Metacharacters:** Special characters like `\d` (digit), `\w` (word), `.` (any character), `*` (zero or more), `+` (one or more), and `[]` (character sets) are all active.

**Example:**

```python
import polars as pl

s = pl.Series("data", [
    "apple-123",
    "orange-45",
    "banana",
    "grape.fruit"
])

# Default (literal=False) uses regex
# Check for one or more digits (\d+)
s.str.contains(r"\d+")
```

**Output:**

```
shape: (4,)
Series: 'data' [bool]
[
	true
	true
	false
	false
]
```

-----

### Disabling Regex: Literal Matching (`literal=True`)

If you want to check for a simple, literal substring and *not* use regex, you must set `literal=True`. This is much faster if you don't need pattern matching.

**Example:**

If you want to find the literal dot (`.`) character, not the regex "any character" metacharacter.

```python
# Regex mode (default): '.' matches any character, so all are True
s.str.contains(r".")
```

**Output:**

```
shape: (4,)
Series: 'data' [bool]
[
	true
	true
	true
	true
]
```

```python
# Literal mode: '.' only matches the literal period character
s.str.contains(r".", literal=True)
```

**Output:**

```
shape: (4,)
Series: 'data' [bool]
[
	false
	false
	false
	true
]
```

-----

### Common Regex Use Cases

#### 1\. Matching the Entire String (Anchoring)

Because `contains` searches for a match *anywhere*, you must use the regex anchors `^` (start of string) and `$` (end of string) if you want to validate the *entire* string.

```python
s = pl.Series(["abc", "a-b-c", "abc-123"])

# Matches 'abc' anywhere
s.str.contains(r"abc")
# [true, false, true]

# Matches only the exact string 'abc'
s.str.contains(r"^abc$")
# [true, false, false]
```

#### 2\. Using Character Classes

```python
s = pl.Series(["file_01.txt", "image.png", "doc.pdf"])

# Check for files that are .txt or .pdf
# Note: We must escape the dot '\.' to mean a literal dot
s.str.contains(r"\.(txt|pdf)$")
```

**Output:**

```
shape: (3,)
Series: '' [bool]
[
	true
	false
	true
]
```

-----

### Important Limitation: The Regex Engine

Polars uses Rust's `regex` crate for its pattern matching, which is very fast. However, it does **not** support all features of Python's `re` module or PCRE.

The most notable unsupported features are:

  * **Lookarounds** (e.g., `(?=...)` for positive lookahead, `(?!...)` for negative lookahead)
  * **Backreferences** (e.g., `\1` to match a previously captured group)

If your pattern requires lookarounds or backreferences, it will raise a `ComputeError`. You must use a different method, such as `Series.str.extract()` with multiple groups or a subsequent filter operation, to achieve a similar result.
