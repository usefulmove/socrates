# Python CLI Tab Completion Guide

Manual bash completion for Python CLI tools without external libraries.

## How Bash Completion Works

When you press TAB, bash calls a registered function with these variables:
- `COMP_WORDS` - array of all words typed
- `COMP_CWORD` - index of current word being completed
- `COMP_LINE` - entire command line
- `COMP_POINT` - cursor position

## Python Completion Script

```python
#!/usr/bin/env python3
import sys

def get_completions(current_word, prev_word, all_words):
    """Return list of possible completions based on context."""
    
    # Example: Complete subcommands for first argument
    if len(all_words) == 2:  # command + first arg
        commands = ['start', 'stop', 'restart', 'status']
        return [cmd for cmd in commands if cmd.startswith(current_word)]
    
    # Example: Complete options for 'start' subcommand
    elif len(all_words) > 2 and all_words[1] == 'start':
        options = ['--force', '--verbose', '--config']
        return [opt for opt in options if opt.startswith(current_word)]
    
    return []

if __name__ == "__main__":
    current_word = sys.argv[1] if len(sys.argv) > 1 else ""
    prev_word = sys.argv[2] if len(sys.argv) > 2 else ""
    all_words = sys.argv[3:] if len(sys.argv) > 3 else []
    
    completions = get_completions(current_word, prev_word, all_words)
    print(' '.join(completions))
```

## Bash Completion Function

```bash
_mycommand_complete() {
    local current_word="${COMP_WORDS[COMP_CWORD]}"
    local prev_word="${COMP_WORDS[COMP_CWORD-1]}"
    
    # Call Python script with context
    local completions="$(python /path/to/completion.py "$current_word" "$prev_word" "${COMP_WORDS[@]}")"
    
    COMPREPLY=($(compgen -W "$completions" -- "$current_word"))
}

# Register completion for your command
complete -F _mycommand_complete mycommand
```

## Installation

1. Save Python script as `/usr/local/bin/mycommand_complete.py` (make executable)
2. Save bash function to `/etc/bash_completion.d/mycommand`
3. Source the completion: `source /etc/bash_completion.d/mycommand`
4. Test: `mycommand <TAB>`

## Advanced Features

- **File completion**: Use `compgen -f` for file paths
- **Directory completion**: Use `compgen -d` for directories  
- **Dynamic completion**: Query APIs, databases, or file system
- **Context awareness**: Different completions based on previous arguments

## Debugging

Add to your bash function:
```bash
echo "DEBUG: current_word=$current_word" >&2
echo "DEBUG: all_words=${COMP_WORDS[@]}" >&2
```