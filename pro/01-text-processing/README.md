<div align="center">

# 🔍 Module 01 — Advanced Text Processing

### grep, sed, and awk — The Holy Trinity of Linux Text Processing

[![Back to Pro](https://img.shields.io/badge/⬅️%20Back-Pro%20Branch-red)](../README.md)

</div>

---

## 📌 Table of Contents

- [Why Text Processing Matters](#why-text-processing-matters)
- [grep — Search Text](#grep--search-text)
- [sed — Stream Editor](#sed--stream-editor)
- [awk — Pattern Scanning & Processing](#awk--pattern-scanning--processing)
- [Combining Tools with Pipes](#combining-tools-with-pipes)
- [Quick Reference Cheatsheet](#quick-reference-cheatsheet)
- [Practice Exercises](#practice-exercises)

---

## Why Text Processing Matters

In Linux and DevOps, **everything is text** — logs, configs, CSVs, API responses.
These 3 tools let you **filter, transform, and extract** exactly what you need:

```
grep  →  FIND    lines matching a pattern
sed   →  EDIT    lines (replace, delete, insert)
awk   →  PARSE   structured text (columns, fields)
```

> 💡 Mastering grep + sed + awk = 10x more productive as a DevOps/SRE engineer!

---

## grep — Search Text

### Basic Usage

```bash
# Search for pattern in a file
grep "error" /var/log/syslog

# Case-insensitive search (-i)
grep -i "error" /var/log/syslog

# Show line numbers (-n)
grep -n "ERROR" app.log

# Show surrounding context
grep -A 3 "ERROR" app.log    # 3 lines AFTER match
grep -B 3 "ERROR" app.log    # 3 lines BEFORE match
grep -C 3 "ERROR" app.log    # 3 lines AROUND match

# Invert match — show lines NOT matching
grep -v "INFO" app.log

# Count matching lines
grep -c "error" app.log

# Show only the matched part
grep -o "ERROR.*" app.log
```

---

### grep with Regular Expressions

```bash
# Search using regex (-E = extended regex)
grep -E "error|warning|critical" app.log

# Match lines STARTING with pattern (^)
grep "^ERROR" app.log

# Match lines ENDING with pattern ($)
grep "failed$" app.log

# Match word boundaries
grep -w "error" app.log     # matches "error" but not "errors"

# Match any character (.)
grep "err.r" app.log         # matches "error", "errwr", etc.

# Match zero or more (*)
grep "fail.*ed" app.log      # matches "failed", "file not opened", etc.
```

---

### Searching Multiple Files & Directories

```bash
# Search in multiple files
grep "error" file1.log file2.log file3.log

# Search recursively in directory (-r)
grep -r "password" /etc/
grep -r "TODO" /var/www/html/

# Search with file name shown (-l = only show filenames)
grep -rl "database" /etc/

# Exclude directories
grep -r --exclude-dir={.git,node_modules} "TODO" .

# Grep with color highlighting
grep --color=auto "error" app.log
```

---

### Real DevOps grep Examples

```bash
# Find all failed logins in auth log
grep "Failed password" /var/log/auth.log

# Find nginx errors
grep -i "error" /var/log/nginx/error.log | tail -50

# Find processes using a port
ss -tulnp | grep :80

# Check config for a setting
grep -r "ServerName" /etc/apache2/

# Count HTTP 500 errors in access log
grep " 500 " /var/log/nginx/access.log | wc -l

# Find all IP addresses in a log
grep -Eo '[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}' access.log | sort | uniq -c | sort -rn
```

---

## sed — Stream Editor

### Understand sed

```bash
# Basic syntax:
sed 'COMMAND' file

# Common commands:
# s/old/new/    → substitute (replace)
# d             → delete line
# p             → print line
# i             → insert before line
# a             → append after line
```

---

### Substitution (Most Common)

```bash
# Replace FIRST occurrence on each line
sed 's/old_text/new_text/' file.txt

# Replace ALL occurrences on each line (g = global)
sed 's/old_text/new_text/g' file.txt

# Case-insensitive replace
sed 's/error/ERROR/gi' file.txt

# Replace and save to file directly (-i = in-place)
sed -i 's/old_text/new_text/g' file.txt

# Backup before editing (-i.bak)
sed -i.bak 's/old_text/new_text/g' file.txt

# Replace only on specific line number
sed '5s/old/new/' file.txt

# Replace on line range (lines 2-10)
sed '2,10s/old/new/g' file.txt
```

---

### Delete Lines

```bash
# Delete specific line number
sed '5d' file.txt

# Delete line range
sed '5,10d' file.txt

# Delete lines matching pattern
sed '/^#/d' config.txt      # delete comment lines
sed '/^$/d' file.txt         # delete empty lines

# Delete last line
sed '$d' file.txt
```

---

### Print Lines

```bash
# Print only matching lines (like grep)
sed -n '/error/p' app.log

# Print specific line numbers
sed -n '5p' file.txt         # print line 5
sed -n '5,10p' file.txt      # print lines 5-10

# Print last line
sed -n '$p' file.txt
```

---

### Real DevOps sed Examples

```bash
# Update server config file
sed -i 's/localhost/production-server.com/g' config.yaml

# Remove all comment lines from a config
sed -i '/^#/d' /etc/nginx/nginx.conf

# Remove blank lines
sed -i '/^$/d' file.txt

# Add line after a match
sed '/ServerName/a ServerAlias www.example.com' /etc/apache2/sites.conf

# Replace environment in config files
sed -i 's/ENV=development/ENV=production/g' .env

# Extract IP addresses from log
sed -n 's/.*client: \([0-9.]*\).*/\1/p' nginx.log
```

---

## awk — Pattern Scanning & Processing

### Understand awk

awk processes text **field by field** (like spreadsheet columns).

```bash
# Default field separator = whitespace
# $1 = field 1, $2 = field 2, $NF = last field, $0 = whole line

echo "hello world linux" | awk '{print $1}'    # hello
echo "hello world linux" | awk '{print $2}'    # world
echo "hello world linux" | awk '{print $NF}'   # linux
echo "hello world linux" | awk '{print $0}'    # hello world linux
```

---

### Basic awk Usage

```bash
# Print specific columns from a file
awk '{print $1, $3}' access.log

# Print with custom format
awk '{print "User:", $1, "| IP:", $2}' file.txt

# Custom field separator (-F)
awk -F: '{print $1}' /etc/passwd        # print usernames
awk -F: '{print $1, $3}' /etc/passwd   # print username and UID
awk -F, '{print $1, $4}' data.csv      # CSV with comma separator

# Print last field
awk '{print $NF}' file.txt

# Print number of fields on each line
awk '{print NF, $0}' file.txt

# Print line numbers
awk '{print NR, $0}' file.txt
```

---

### awk with Conditions

```bash
# Print lines where field 3 > 100
awk '$3 > 100' file.txt

# Print if line contains "error"
awk '/error/' file.txt

# Print specific field if line matches pattern
awk '/404/ {print $1, $7}' access.log

# Multiple conditions
awk '$3 > 100 && $4 < 500 {print $0}' data.txt

# Print lines between patterns
awk '/START/,/END/' file.txt
```

---

### awk Built-in Variables

```bash
# NR  = Number of Records (current line number)
# NF  = Number of Fields (in current line)
# FS  = Field Separator (default: space)
# OFS = Output Field Separator
# RS  = Record Separator (default: newline)
# $0  = Entire current line
# $1, $2 ... = Fields

# Print line count at end
awk 'END {print "Total lines:", NR}' file.txt

# Print with custom output separator
awk 'BEGIN {OFS=","} {print $1, $2, $3}' file.txt
```

---

### Real DevOps awk Examples

```bash
# Analyze nginx access log — print IP and URL
awk '{print $1, $7}' /var/log/nginx/access.log

# Find top 10 most frequent IPs
awk '{print $1}' access.log | sort | uniq -c | sort -rn | head 10

# Calculate total data transferred
awk '{sum += $10} END {print "Total bytes:", sum}' access.log

# Extract usernames from /etc/passwd
awk -F: '$3 >= 1000 {print $1}' /etc/passwd

# Print memory usage from free
free -m | awk '/Mem:/ {print "Used:", $3, "MB of", $2, "MB"}'

# Monitor high CPU processes from ps
ps aux | awk '$3 > 5.0 {print $1, $2, $3, $11}'

# Parse CSV and calculate totals
awk -F, 'NR>1 {sum += $3} END {print "Total:", sum}' sales.csv
```

---

## Combining Tools with Pipes

The real power comes from **combining** these tools with pipes (`|`):

```bash
# Find all unique error types and count them
grep "ERROR" app.log | awk '{print $4}' | sort | uniq -c | sort -rn

# Find top IP addresses making requests
cat access.log | awk '{print $1}' | sort | uniq -c | sort -rn | head 20

# Find config settings, strip comments, show values
grep -v "^#" /etc/ssh/sshd_config | grep -v "^$" | awk '{print $1, $2}'

# Extract all email addresses from a file
grep -Eo '[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}' file.txt | sort -u

# Find files modified in last 24h and count lines
find /var/log -mtime -1 -name "*.log" | xargs wc -l

# Replace version in all config files
grep -rl "version=1.0" /etc/ | xargs sed -i 's/version=1.0/version=2.0/g'
```

---

## Quick Reference Cheatsheet

| Command | What It Does | Example |
|---|---|---|
| `grep -i "pat" file` | Case-insensitive search | `grep -i "error" app.log` |
| `grep -n "pat" file` | Show line numbers | `grep -n "fail" log` |
| `grep -r "pat" dir/` | Recursive search | `grep -r "TODO" ./src/` |
| `grep -v "pat" file` | Invert match | `grep -v "INFO" app.log` |
| `grep -c "pat" file` | Count matches | `grep -c "error" log` |
| `grep -E "a\|b"` | Regex OR | `grep -E "err\|warn"` |
| `sed 's/a/b/g'` | Replace all | `sed 's/dev/prod/g' cfg` |
| `sed -i 's/a/b/g'` | In-place replace | `sed -i 's/v1/v2/g' .env` |
| `sed '/^#/d'` | Delete comments | `sed '/^#/d' config.txt` |
| `sed '/^$/d'` | Delete blank lines | `sed '/^$/d' file.txt` |
| `awk '{print $1}'` | Print field 1 | `awk '{print $1}' log` |
| `awk -F: '{print $1}'` | Custom separator | `awk -F: '{print $1}' /etc/passwd` |
| `awk '/pat/ {print}'` | Filter + print | `awk '/404/ {print $1}'` |
| `awk 'END {print NR}'` | Total line count | `awk 'END{print NR}' f` |

---

## Practice Exercises

```bash
# Create sample log file
cat > sample.log << 'EOF'
2024-09-24 10:00:01 INFO  User zohaib logged in from 192.168.1.10
2024-09-24 10:05:22 ERROR Database connection failed for user admin
2024-09-24 10:10:33 INFO  Request processed successfully
2024-09-24 10:15:44 WARNING Disk usage at 85% on /dev/sda1
2024-09-24 10:20:55 ERROR Failed to start nginx service
2024-09-24 10:25:06 INFO  Backup completed successfully
2024-09-24 10:30:17 ERROR Authentication failed for user root from 10.0.0.50
EOF

# grep exercises
grep "ERROR" sample.log
grep -c "ERROR" sample.log
grep -n "WARNING\|ERROR" sample.log
grep -v "INFO" sample.log

# sed exercises
sed 's/ERROR/🔴 ERROR/g' sample.log
sed -n '/ERROR/p' sample.log
sed '/^2024-09-24 10:0/d' sample.log

# awk exercises
awk '{print $1, $2}' sample.log            # date and time
awk '{print $3}' sample.log                # log level
awk '/ERROR/ {print NR, $0}' sample.log   # ERROR lines with numbers
awk '{print $3}' sample.log | sort | uniq -c  # count by level

# Combine them
grep "ERROR" sample.log | awk '{print $NF}'   # last field of errors
```

---

> ➡️ Next: [Storage, LVM & AWS EBS](../02-storage-lvm/README.md)
