# Ripgrep

```bash
# Basic search for a string in the current directory
rg "function_name"

# Search case-insensitively (matches "Error", "error", or "ERROR")
rg -i "error"

# Search for a whole word only (won't match "strawberry" if searching for "berry")
rg -w "berry"

# Search only within Python files using a built-in alias
rg "import requests" -t py

# Search for a pattern but ignore the 'tests' directory
rg "api_key" -g '!tests/*'

# Show 3 lines of context around each match (great for understanding code)
rg "class Database" -C 3

# List only the files that contain the word "TODO" (without showing the lines)
rg -l "TODO"

# Count the number of matches for a pattern
rg -c "FIXME"

# Search for a regex pattern (e.g., lines starting with 'ref')
rg "^ref"

# Find lines that do NOT contain "DEBUG"
rg -v "DEBUG"
```
