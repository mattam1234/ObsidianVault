# Grep

**Grep** is a command-line utility for searching plain-text data sets for lines that match a regular expression. Its name comes from the ed command `g/re/p` (globally search for a regular expression and print matching lines), which has the same effect.

Grep was originally developed for Unix operating systems like **Linux** ([[linux]]), but is now available for all Unix-like systems and some others such as OS-9.

---

## Basic Usage

**Search for Pattern:**
```bash
# Search for pattern in file
grep "pattern" file.txt

# Search in multiple files
grep "pattern" file1.txt file2.txt

# Search in all files in directory
grep "pattern" *

# Recursive search in directory
grep -r "pattern" /path/to/directory
```

---

## Common Options

**Case Sensitivity:**
```bash
# Case-insensitive search
grep -i "pattern" file.txt

# Case-sensitive (default)
grep "Pattern" file.txt
```

**Line Numbers:**
```bash
# Show line numbers
grep -n "pattern" file.txt

# Show only matching part
grep -o "pattern" file.txt
```

**Counting:**
```bash
# Count matching lines
grep -c "pattern" file.txt

# Count non-matching lines
grep -vc "pattern" file.txt
```

**Invert Match:**
```bash
# Show lines that don't match
grep -v "pattern" file.txt
```

**Word Match:**
```bash
# Match whole words only
grep -w "word" file.txt

# Match lines starting with pattern
grep "^pattern" file.txt

# Match lines ending with pattern
grep "pattern$" file.txt
```

---

## Advanced Options

**Context Lines:**
```bash
# Show 3 lines after match
grep -A 3 "pattern" file.txt

# Show 3 lines before match
grep -B 3 "pattern" file.txt

# Show 3 lines before and after match
grep -C 3 "pattern" file.txt
```

**Multiple Patterns:**
```bash
# Match any of multiple patterns
grep -e "pattern1" -e "pattern2" file.txt

# Extended regex (supports |, +, ?, etc.)
grep -E "pattern1|pattern2" file.txt
egrep "pattern1|pattern2" file.txt

# Read patterns from file
grep -f patterns.txt file.txt
```

**File Selection:**
```bash
# Only show filenames with matches
grep -l "pattern" *.txt

# Show filenames without matches
grep -L "pattern" *.txt

# Recursive search
grep -r "pattern" directory/

# Recursive with specific file types
grep -r --include="*.txt" "pattern" directory/

# Exclude specific files
grep -r --exclude="*.log" "pattern" directory/

# Exclude directories
grep -r --exclude-dir="node_modules" "pattern" directory/
```

**Output Control:**
```bash
# Quiet mode (only exit status)
grep -q "pattern" file.txt

# Silent mode (suppress errors)
grep -s "pattern" file.txt

# Show only matches (not full lines)
grep -o "pattern" file.txt

# Show max N matching lines
grep -m 5 "pattern" file.txt
```

---

## Regular Expressions

**Basic Regex (BRE):**
```bash
# Match beginning of line
grep "^pattern" file.txt

# Match end of line
grep "pattern$" file.txt

# Match any single character
grep "p.ttern" file.txt

# Match zero or more of previous character
grep "pat*tern" file.txt

# Match character class
grep "[abc]" file.txt
grep "[0-9]" file.txt
grep "[a-z]" file.txt
```

**Extended Regex (ERE):**
```bash
# Use -E or egrep
grep -E "pattern1|pattern2" file.txt

# Match one or more
grep -E "pat+tern" file.txt

# Match zero or one
grep -E "patt?ern" file.txt

# Match exactly n times
grep -E "a{3}" file.txt

# Match n to m times
grep -E "a{2,4}" file.txt

# Grouping
grep -E "(pattern1|pattern2)" file.txt
```

**Perl Compatible Regex (PCRE):**
```bash
# Use -P for PCRE
grep -P "\d+" file.txt      # Match digits
grep -P "\w+" file.txt      # Match word characters
grep -P "\s+" file.txt      # Match whitespace
grep -P "(?i)pattern" file.txt  # Case-insensitive flag
```

---

## Practical Examples

**System Administration:**
```bash
# Find errors in log files
grep -i "error" /var/log/syslog

# Search for specific IP address
grep "192.168.1.100" /var/log/auth.log

# Find failed login attempts
grep "Failed password" /var/log/auth.log

# Show active users
grep -v "^#" /etc/passwd | grep -v "nologin"

# Find processes
ps aux | grep "nginx"

# Search running services
systemctl list-units | grep "running"
```

**Development:**
```bash
# Find TODO comments
grep -rn "TODO" src/

# Find function definitions
grep -rn "function.*myFunction" src/

# Find imports
grep -rn "^import" *.js

# Find all console.log statements
grep -rn "console\.log" src/

# Find large files
ls -lh | grep "^-" | grep "G\s"

# Find empty files
find . -type f -exec grep -L "." {} \;
```

**Text Processing:**
```bash
# Extract email addresses
grep -oE "[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}" file.txt

# Extract IP addresses
grep -oE "\b([0-9]{1,3}\.){3}[0-9]{1,3}\b" file.txt

# Extract URLs
grep -oE "https?://[a-zA-Z0-9./?=_%:-]*" file.txt

# Find lines with numbers
grep "[0-9]" file.txt
```

**Configuration Files:**
```bash
# Show non-comment lines
grep -v "^#" config.conf

# Show non-empty, non-comment lines
grep -v "^#" config.conf | grep -v "^$"

# Find specific configuration
grep "^Port" /etc/ssh/sshd_config

# Search Apache config
grep -r "VirtualHost" /etc/apache2/
```

---

## Performance Tips

**Speed Optimization:**
```bash
# Use fixed strings (faster)
grep -F "exact_string" file.txt
fgrep "exact_string" file.txt

# Limit recursion depth
grep -r --max-depth=2 "pattern" directory/

# Use binary file skip
grep -I "pattern" *

# Parallel grep (GNU parallel)
find . -type f | parallel grep "pattern" {}
```

---

## Grep Variants

**Different Implementations:**
- `grep` - Standard grep (BRE)
- `egrep` - Extended grep (ERE) = `grep -E`
- `fgrep` - Fixed string grep = `grep -F`
- `pgrep` - Process grep (searches running processes)
- `zgrep` - Grep for compressed files
- `ripgrep (rg)` - Modern, fast grep alternative
- `ag (The Silver Searcher)` - Fast code searching tool

**Compressed File Searching:**
```bash
# Search in gzip files
zgrep "pattern" file.gz

# Search in bzip2 files
bzgrep "pattern" file.bz2

# Search in xz files
xzgrep "pattern" file.xz
```

---

## Exit Status

Grep returns:
- `0` - Match found
- `1` - No match found
- `2` - Error occurred

**Using in Scripts:**
```bash
# Check if pattern exists
if grep -q "pattern" file.txt; then
    echo "Pattern found"
else
    echo "Pattern not found"
fi

# Count matches and act
matches=$(grep -c "pattern" file.txt)
if [ $matches -gt 0 ]; then
    echo "Found $matches matches"
fi
```

---

## Common Patterns

**Match Empty Lines:**
```bash
grep "^$" file.txt
```

**Match Non-Empty Lines:**
```bash
grep -v "^$" file.txt
```

**Match Lines with Specific Word Count:**
```bash
# Lines with exactly 5 words
grep -E "^(\w+\s+){4}\w+$" file.txt
```

**Match Between Patterns:**
```bash
# Using awk (grep can't easily do this)
awk "/start/,/end/" file.txt
```

---

## Alternatives and Related Tools

**Modern Alternatives:**
- [[linux/awk|awk]] - Pattern scanning and processing
- [[linux/sed|sed]] - Stream editor
- `ripgrep (rg)` - Faster recursive search
- `ag` - The Silver Searcher
- `ack` - grep-like tool for programmers

**Related Commands:**
```bash
# Find files by name
find . -name "*.txt"

# Search file contents with find
find . -type f -exec grep "pattern" {} +

# Search with xargs
find . -type f | xargs grep "pattern"
```

---

## Best Practices

✅ Use `-i` for case-insensitive searches when appropriate
✅ Use `-r` for recursive directory searches
✅ Use `-n` to include line numbers for debugging
✅ Use `-v` to exclude unwanted results
✅ Use `--color` for better readability (usually default)
✅ Use `-F` for literal string searches (faster)
✅ Escape special regex characters when needed
✅ Use `-q` in scripts for silent checking
✅ Combine with other tools (pipes) for powerful text processing
✅ Consider `ripgrep` or `ag` for large codebases

---

## Cheat Sheet

```bash
# Basic search
grep "pattern" file.txt

# Case-insensitive
grep -i "pattern" file.txt

# Recursive
grep -r "pattern" directory/

# With line numbers
grep -n "pattern" file.txt

# Invert match
grep -v "pattern" file.txt

# Count matches
grep -c "pattern" file.txt

# Multiple patterns
grep -E "pattern1|pattern2" file.txt

# Context lines
grep -C 3 "pattern" file.txt

# Only filenames
grep -l "pattern" *.txt

# Whole words only
grep -w "word" file.txt
```

---

## Related Topics

- [[linux/sed|sed]] - Stream editor
- [[linux/awk|awk]] - Pattern processing language
- [[linux/linux|Linux]] - Operating system
- [[tools/git|Git]] - Uses grep for searching repos
