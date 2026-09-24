<div align="center">

# 📂 Module 02 — Navigation & File Operations

### Master the Linux Filesystem Like a Pro

[![Back to Basic](https://img.shields.io/badge/⬅️%20Back-Basic%20Branch-brightgreen)](../README.md)

</div>

---

## 📌 Table of Contents

- [Linux Filesystem Hierarchy](#linux-filesystem-hierarchy)
- [Navigation Commands](#navigation-commands)
- [File & Directory Operations](#file--directory-operations)
- [Viewing File Contents](#viewing-file-contents)
- [Disk & Storage Info](#disk--storage-info)
- [Quick Reference Cheatsheet](#quick-reference-cheatsheet)
- [Practice Exercises](#practice-exercises)

---

## Linux Filesystem Hierarchy

Linux stores everything in a **single tree** starting from `/` (root):

```
/                    ← Root of everything
├── bin/             ← Essential binaries (ls, cat, cp)
├── boot/            ← Bootloader and kernel files
├── dev/             ← Device files (sda, tty, null)
├── etc/             ← Configuration files
├── home/            ← User home directories (/home/zohaib)
├── lib/             ← Shared libraries
├── media/           ← Mounted USB drives, CDs
├── mnt/             ← Temporary mount point
├── opt/             ← Optional/third-party software
├── proc/            ← Virtual: running process info
├── root/            ← Home directory for root user
├── sbin/            ← System binaries (for root)
├── srv/             ← Service data (web servers)
├── sys/             ← Virtual: kernel/hardware info
├── tmp/             ← Temporary files (cleared on reboot)
├── usr/             ← User programs and utilities
└── var/             ← Variable files: logs, databases
```

> 💡 **Pro Tip:** In Linux, **everything is a file** — even hardware devices!

---

## Navigation Commands

### `pwd` — Print Working Directory
Shows your **current location** in the filesystem.

```bash
pwd
# Output: /home/zohaib/projects
```

---

### `ls` — List Directory Contents

```bash
# Basic usage
ls

# Long format (permissions, size, date)
ls -l

# Show hidden files (starting with .)
ls -a

# Long format + hidden files (most used)
ls -la

# Human-readable file sizes
ls -lh

# Sort by modification time (newest first)
ls -lt

# List specific directory
ls -la /var/log

# List with color
ls --color=auto
```

**Output breakdown:**
```
-rw-r--r-- 1 zohaib zohaib 4096 Sep 24 10:00 file.txt
│          │ │      │      │    │              └── filename
│          │ │      │      │    └── date/time
│          │ │      │      └── file size (bytes)
│          │ │      └── group name
│          │ └── owner name
│          └── number of hard links
└── permissions (r=read, w=write, x=execute)
```

---

### `cd` — Change Directory

```bash
# Go to home directory
cd
cd ~

# Go to specific path (absolute)
cd /var/log

# Go to relative path
cd projects/myapp

# Go one level up (parent directory)
cd ..

# Go two levels up
cd ../..

# Go to previous directory (toggle back)
cd -

# Go to root
cd /
```

---

### `mkdir` — Make Directory

```bash
# Create a single directory
mkdir projects

# Create directory with spaces (use quotes)
mkdir "My Projects"

# Create nested directories at once (-p flag)
mkdir -p projects/linux/basics

# Create multiple directories at once
mkdir dir1 dir2 dir3

# Create directory with specific permissions
mkdir -m 755 secure_folder
```

---

## File & Directory Operations

### `touch` — Create Empty File / Update Timestamp

```bash
# Create a new empty file
touch newfile.txt
touch index.html

# Create multiple files at once
touch file1.txt file2.txt file3.txt

# Update timestamp of existing file
touch existing_file.txt
```

---

### `cp` — Copy Files and Directories

```bash
# Copy a file
cp source.txt destination.txt

# Copy file to another directory
cp file.txt /home/zohaib/backup/

# Copy and rename at the same time
cp file.txt backup_file.txt

# Copy entire directory (-r = recursive)
cp -r projects/ backup_projects/

# Copy with verbose output (see what's happening)
cp -rv projects/ /backup/

# Copy and preserve file attributes
cp -a source/ destination/

# Interactive: ask before overwriting
cp -i file.txt /backup/
```

---

### `mv` — Move or Rename Files

```bash
# Rename a file
mv oldname.txt newname.txt

# Move file to another directory
mv file.txt /var/www/html/

# Move entire directory
mv projects/ /home/zohaib/work/

# Move multiple files to a directory
mv file1.txt file2.txt file3.txt /backup/

# Interactive: ask before overwriting
mv -i source.txt destination.txt

# Verbose output
mv -v file.txt /backup/
```

---

### `rm` — Remove Files and Directories

```bash
# Delete a file
rm file.txt

# Delete without confirmation prompt (-f = force)
rm -f file.txt

# Delete a directory and all its contents (-r = recursive)
rm -r folder/

# Delete directory recursively + force (dangerous!)
rm -rf folder/

# Interactive: confirm each deletion
rm -i file.txt

# Delete multiple files
rm file1.txt file2.txt file3.txt
```

> ⚠️ **WARNING:** `rm -rf` is **permanent** — there is NO trash bin in Linux!
> Always double-check before running `rm -rf`.

---

### `rmdir` — Remove Empty Directory

```bash
# Only works on empty directories
rmdir empty_folder

# Remove nested empty directories
rmdir -p parent/child/grandchild
```

---

## Viewing File Contents

### `cat` — Display File Contents

```bash
# View file contents
cat file.txt

# View multiple files at once
cat file1.txt file2.txt

# Show line numbers
cat -n file.txt

# View compressed file (without extracting)
zcat archive.gz

# Create a file and type content (Ctrl+D to save)
cat > newfile.txt

# Append content to existing file
cat >> file.txt
```

---

### `head` — View Beginning of File

```bash
# Show first 10 lines (default)
head file.txt

# Show first 20 lines
head -n 20 file.txt

# Show first 5 lines of multiple files
head -n 5 file1.txt file2.txt

# Real use: check recent log entries
head -n 50 /var/log/syslog
```

---

### `tail` — View End of File

```bash
# Show last 10 lines (default)
tail file.txt

# Show last 20 lines
tail -n 20 error.log

# Follow file in real-time (great for logs!)
tail -f /var/log/syslog

# Follow with line count
tail -f -n 50 app.log

# Real use: monitor live server logs
tail -f /var/log/nginx/access.log
```

> 💡 **`tail -f` is one of the most used commands by DevOps engineers!**

---

### `less` / `more` — Paginate Large Files

```bash
# Open file with pagination (recommended)
less /var/log/syslog

# Navigation inside less:
#   SPACE       → Next page
#   b           → Previous page
#   /pattern    → Search forward
#   ?pattern    → Search backward
#   n           → Next search result
#   q           → Quit

# Older pager (only goes forward)
more /var/log/syslog
```

---

### `wc` — Word/Line/Byte Count

```bash
# Count lines, words, bytes
wc file.txt
# Output: 42 300 1500 file.txt
#          │   │    └── bytes
#          │   └── words
#          └── lines

# Count only lines (-l most used)
wc -l data.txt

# Count only words
wc -w file.txt

# Count only characters
wc -c file.txt

# Count lines in multiple files
wc -l file1.txt file2.txt
```

---

## Disk & Storage Info

### `df` — Disk Free Space

```bash
# Show disk usage for all filesystems
df

# Human-readable format (GB, MB)
df -h

# Show filesystem type too
df -hT

# Check specific mount point
df -h /home
```

---

### `du` — Disk Usage (per file/folder)

```bash
# Show size of directory
du folder/

# Human-readable
du -h folder/

# Summary only (not per-file breakdown)
du -sh folder/

# Check top 10 largest directories
du -h /var | sort -rh | head -10

# Check current directory size
du -sh .
```

---

## Quick Reference Cheatsheet

| Command | What It Does | Example |
|---|---|---|
| `pwd` | Print current path | `pwd` |
| `ls -la` | List all files + details | `ls -la /etc` |
| `cd /path` | Go to directory | `cd /var/log` |
| `mkdir -p` | Create nested dirs | `mkdir -p a/b/c` |
| `touch file` | Create empty file | `touch app.js` |
| `cp -r src dst` | Copy directory | `cp -r app/ backup/` |
| `mv old new` | Rename or move | `mv file.txt docs/` |
| `rm -rf dir` | Delete directory | `rm -rf temp/` |
| `cat file` | View file | `cat config.yml` |
| `head -n 20` | First 20 lines | `head -n 20 log` |
| `tail -f log` | Live log view | `tail -f app.log` |
| `less file` | Paginate file | `less big_file.txt` |
| `wc -l file` | Count lines | `wc -l data.csv` |
| `df -h` | Disk free space | `df -h` |
| `du -sh dir` | Folder size | `du -sh /var/log` |

---

## Practice Exercises

Work through these exercises in order:

```bash
# Exercise 1: Navigate the filesystem
pwd
cd /
ls -la
cd /home
ls -la
cd ~

# Exercise 2: Create directory structure
mkdir -p linux-practice/files linux-practice/scripts linux-practice/logs

# Exercise 3: Create and manage files
touch linux-practice/files/hello.txt
touch linux-practice/files/world.txt
ls -la linux-practice/files/

# Exercise 4: Copy and move files
cp linux-practice/files/hello.txt linux-practice/files/hello_backup.txt
mv linux-practice/files/world.txt linux-practice/scripts/

# Exercise 5: View file info
cat /etc/os-release
head -n 5 /etc/passwd
tail -n 5 /etc/passwd
wc -l /etc/passwd

# Exercise 6: Check disk space
df -h
du -sh linux-practice/

# Exercise 7: Clean up
rm -rf linux-practice/
```

---

> ⬅️ [Back: Introduction](../01-introduction/README.md) | ➡️ Next: [Go to Advance Branch](../../../tree/advance)
