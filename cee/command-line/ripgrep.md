---
description: https://github.com/burntsushi/ripgrep
---

# Ripgrep

### Basic Search

```bash
# Basic search for a string in the current directory (recursively)
rg "pattern"

# Search for a pattern in a specific file
rg "pattern" file.txt
 
# Search for a pattern in a specific directory
rg "pattern" ./src

# Search for a literal string (no regex interpretation)
rg -F "pattern.with.dots"

# Search case-insensitively (matches "Error", "error", or "ERROR")
rg -i "error"

# Search for a whole word only (won't match "strawberry" if searching for "berry")
rg -w "berry"
```

### Output Control

```bash
# Show only the matching part of the line (not the full line)
rg -o "pattern"
 
# List only the files that contain the word "TODO" (without showing the lines)
rg -l "TODO"
 
# Count the number of matches for a pattern
rg -c "FIXME"
 
# Show N lines of context around each match (great for understanding code)
rg -C 3 "class Database" 
 
# Show N lines of context before each match
rg -B 3 "class Database"
 
# Show N lines of context after each match
rg -A 3 "class Database"

# Suppress filename in output (useful when searching a single file)
rg --no-filename "pattern"
 
# Print results with null byte after each match (useful for piping to xargs)
rg -l -0 "pattern"
```

### File & Directory Filtering

```bash
# Search for a pattern matching a glob pattern
rg "api_key" -g '*.js'

# Exclude files matching a glob pattern
rg "api_key" -g "!*.min.js"

# Search only files of a specific type (e.g. rust, py, js, ts, go, html, css)
rg "requests" -t py
 
# Exclude a specific file type
rg "requests" -T py

# Search hidden files and directories (dotfiles)
rg -. "pattern"
rg --hidden "pattern"
 
# Search files even if listed in .gitignore
rg --no-ignore "pattern"
 
# Limit directory traversal depth
rg --max-depth 2 "pattern"
 
# Follow symbolic links during traversal
rg -L "pattern"
```

### Regex & Pattern Options

```bash
# Search for a regex pattern (e.g., lines starting with 'ref')
rg "^ref"

# Use a fixed list of patterns from a file (one pattern per line)
rg -f patterns.txt

# Search for multiple patterns (logical OR)
rg "pattern1|pattern2"

# Find lines that do NOT contain "DEBUG"
rg -v "DEBUG"

# Multiline mode — allow patterns to match across line boundaries
rg -U "start.*?end"
```

### Performance & Behaviour

```bash
# Search with multiple threads (default is automatic; set explicitly)
rg -j 4 "pattern"

# Limit to first N matches total
rg -m 5 "pattern"
 
# Sort results by file path (disables parallelism)
rg --sort path "pattern"
 
# Sort results by last modified time
rg --sort modified "pattern"
```

Output Formatting

```bash
# Force colored output (even when piping)
rg --color always "pattern"
 
# Disable colored output
rg --color never "pattern"
 
# Show line numbers (on by default; shown for clarity)
rg -n "pattern"
 
# Do not show line numbers
rg -N "pattern"
 
# Compact output — omit the file path header between match groups
rg --no-heading "pattern"
 
# Print results as NUL-separated (null-delimited), useful for scripting
rg --null "pattern"
```
