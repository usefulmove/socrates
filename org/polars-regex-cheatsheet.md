# Overview

Polars uses Rust’s regex engine.

- Most “normal” regex works.
- Used heavily in `.str.contains`, `.str.replace`, `.str.extract`, etc.
- No lookbehind, no backreferences.

All examples assume:

``` python
import polars as pl

s = pl.Series(["apple", "Apple pie", "banana", "apricot", None])
```

# Core Polars string methods that use regex

## `str.contains`

Returns a Boolean mask: does each string match the pattern?

``` python
s.str.contains(r"app")           # substring "app"
s.str.contains(r"^app")          # starts with "app"
s.str.contains(r"(?i)apple")     # case-insensitive "apple"
s.str.contains(r"a.*e")          # "a" then later "e"
s.str.contains(r"\d{3}-\d{2}")   # simple digit pattern
```

Use literal (no regex) matching:

``` python
s.str.contains("a.*e", literal=True)
```

## `str.extract / str.extract_all`

Extract captured groups.

``` python
# Single match, first capture group
s.str.extract(r"^a(\w+)", group_index=1)

# All matches as list
s.str.extract_all(r"[aeiou]")
```

## `str.replace / str.replace_all`

Regex-based replacements.

``` python
s.str.replace(r"\s+", "_")          # replace first whitespace run
s.str.replace_all(r"\s+", "_")      # replace all whitespace runs
```

## `str.count_match`

Count regex matches per string.

``` python
s.str.count_match(r"[aeiou]")
```

# Essential regex building blocks

## Anchors

- `^` : start of string
- `$` : end of string

``` python
s.str.contains(r"^app")      # starts with "app"
s.str.contains(r"e$")        # ends with "e"
```

## Character classes

- `[abc]` : one of a, b, or c
- `[^abc]` : anything **except** a, b, or c
- `\d` : digit (0–9)
- `\w` : “word” char (letter, digit, underscore)
- `\s` : whitespace

``` python
s.str.contains(r"[ab]")      # has 'a' or 'b'
s.str.contains(r"\d")        # has a digit
s.str.contains(r"^[A-Z]\w+") # starts with capital word
```

## Quantifiers

- `?` : 0 or 1 (optional)
- `*` : 0 or more
- `+` : 1 or more
- `{m}` : exactly m
- `{m,}` : m or more
- `{m,n}` : between m and n

``` python
s.str.contains(r"ap+le")      # "aple", "apple", "appple"
s.str.contains(r"a.*e")       # "a" then anything, then "e"
s.str.contains(r"\d{3}-\d{2}")
```

## Groups and alternation

- Group: `(…)`
- Alternation: `a|b`

``` python
s.str.contains(r"(apple|banana)")
s.str.extract(r"^a(\w+)", group_index=1)  # capture after leading 'a'
```

# Inline flags (modifiers)

Rust-style inline flags, placed at the start of the pattern or inside a group:

- `(?i)` : case-insensitive
- `(?m)` : multiline (^ and \$ match line boundaries)
- `(?s)` : dot matches newline (single-line mode)
- `(?x)` : ignore whitespace/comments in pattern

Most common in Polars: case-insensitive matches.

``` python
s.str.contains(r"(?i)apple")         # "apple", "Apple", "APPLE"
s.str.replace_all(r"(?i)apple", "fruit")
```

You can also limit flags to a group:

- `(?i:pattern)` → case-insensitive **only** for `pattern`.

``` python
s.str.contains(r"(?i:app)le")  # "App" case-insensitive, "le" normal
```

# Quick pattern reference (for data work)

| Goal                        | Pattern                                          | Notes                             |
|-----------------------------|--------------------------------------------------|-----------------------------------|
| Email-ish string            | `[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}` | crude but useful                  |
| US phone (simple)           | `\d{3}-\d{3}-\d{4}`                              | "123-456-7890"                    |
| 4-digit year                | `\b\d{4}\b`                                      | word boundaries around year       |
| Decimal number              | `-?\d+(\.\d+)?`                                  | optional minus, optional fraction |
| ISO date YYYY-MM-DD         | `\d{4}-\d{2}-\d{2}`                              | pure shape check                  |
| Starts with prefix          | `^prefix`                                        | anchor at beginning               |
| Ends with suffix            | `suffix$`                                        | anchor at end                     |
| Contains only digits        | `^\d+$`                                          | whole string digits               |
| Contains at least one digit | `\d`                                             | anywhere                          |
| Multiple whitespace         | `\s+`                                            | runs of whitespace                |

# DataFrame filtering examples

## Filter rows using `str.contains`

``` python
df = pl.DataFrame({
    "name": ["apple", "Apple pie", "banana", "apricot"],
    "price": [1.0, 2.5, 0.5, 1.3],
})

# Rows where name starts with "a" or "A"
df.filter(pl.col("name").str.contains(r"(?i)^a"))

# Rows where name contains "apple" (case-insensitive)
df.filter(pl.col("name").str.contains(r"(?i)apple"))

# Rows with a number in the name
df.filter(pl.col("name").str.contains(r"\d"))
```

## Extract + filter

``` python
# Extract first word from name
df.with_columns(
    pl.col("name").str.extract(r"^(\w+)", group_index=1).alias("first_word")
)

# Keep only rows where we successfully extracted (non-null)
df.filter(
    pl.col("name").str.extract(r"^(\w+)", group_index=1).is_not_null()
)
```

# Common gotchas in Polars regex

- **No lookbehind**: patterns like `(?<=foo)bar` are not supported.
- **No backreferences**: patterns like `(\w+)-\1` are not supported.
- Dot (`.`) does **not** match newlines unless you enable `(?s)`.
- Remember to use raw strings in Python for backslashes: `r"\d+`" not `"\\d+"`.
- `str.contains` treats the pattern as regex by default; use `literal=True` for plain substrings.

# Tiny mental model

- Write the regex as if you’re using “normal” modern regex, but:
  - Avoid lookbehind and backreferences.
  - Use inline flags like `(?i)` when you need case-insensitive or other modes.
- Then treat the Boolean result from `str.contains` as a mask for `filter` or `with_columns` logic.

# Personal notes

Use this space to jot down patterns you find yourself reusing:

- TODO: pattern for my typical IDs
- TODO: pattern for “sample-####” style strings
- TODO: nicer email / URL patterns for real-world data
