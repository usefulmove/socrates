### 1. **Overwrite the current line with `\r` (carriage return)**
This works for updating a single line in place:

```python
import time
import sys

for i in range(10):
    sys.stdout.write(f"\rProgress: {i}/10")
    sys.stdout.flush()
    time.sleep(0.5)
sys.stdout.write("\n")  # Final newline when done
```

### 2. **Clear to end of line with ANSI escape codes**
```python
import sys

def update_line(text):
    sys.stdout.write(f"\r\033[K{text}")  # \033[K clears to end of line
    sys.stdout.flush()

for i in range(5):
    update_line(f"Processing item {i}...")
    time.sleep(1)
```

### 3. **Move cursor up/down with ANSI codes**
```python
# Move cursor up 2 lines and overwrite
print("Line 1")
print("Line 2") 
print("Line 3")
time.sleep(1)
sys.stdout.write("\033[2A")  # Move up 2 lines
sys.stdout.write("\rUpdated Line 2\n")
sys.stdout.write("Updated Line 3\n")
sys.stdout.flush()
```

## When You DO Need Raw Mode

Raw mode becomes necessary when you need to:
- Handle individual keystrokes without waiting for Enter
- Read input while simultaneously updating the display
- Implement complex interactive UIs (like text editors)

For example, if you want to update a counter while also responding to keypresses:

```python
import sys
import select
import tty
import termios

# This requires raw mode to read keys immediately
old_settings = termios.tcgetattr(sys.stdin)
try:
    tty.setraw(sys.stdin.fileno())
    # Now you can read individual keystrokes and update display
    # while handling input
finally:
    termios.tcsetattr(sys.stdin, termios.TCSADRAIN, old_settings)
```

## Recommendation

- **For progress bars, counters, or simple line updates**: Use `\r` and ANSI escape codes (no raw mode needed)
- **For interactive apps that need immediate key response**: Use raw mode
- **For complex terminal UIs**: Consider libraries like `curses` (Unix) or `windows-curses` (Windows), or higher-level libraries like `rich`, `prompt_toolkit`, or `blessed`
