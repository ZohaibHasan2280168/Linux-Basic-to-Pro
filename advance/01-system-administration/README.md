<div align="center">

# 🖥️ Module 01 — System Administration

### Monitor, Manage and Control Your Linux System

[![Back to Advance](https://img.shields.io/badge/⬅️%20Back-Advance%20Branch-yellow)](../README.md)

</div>

---

## 📌 Table of Contents

- [System Information](#system-information)
- [Process Management](#process-management)
- [Memory Management](#memory-management)
- [Service Management](#service-management)
- [Quick Reference Cheatsheet](#quick-reference-cheatsheet)
- [Practice Exercises](#practice-exercises)

---

## System Information

### `uname` — Kernel & System Info

```bash
# Show all system info (most useful)
uname -a
# Output: Linux hostname 5.15.0 #1 SMP x86_64 GNU/Linux

# Show just the kernel version
uname -r
# Output: 5.15.0-91-generic

# Show OS type
uname -s
# Output: Linux

# Show machine hardware (architecture)
uname -m
# Output: x86_64

# Show node (hostname)
uname -n
```

---

### `uptime` — System Runtime Duration

```bash
# How long the system has been running
uptime
# Output: 21:30:00 up 3 days, 4:22, 2 users, load average: 0.15, 0.10, 0.09

# Breakdown:
# up 3 days, 4:22     → how long the system has been running
# 2 users             → currently logged in users
# load average        → CPU load over 1, 5, 15 minutes
```

> 💡 **Load average > number of CPU cores = system is overloaded!**

---

### `hostname` — System Name

```bash
# Show hostname
hostname

# Show full domain name
hostname -f

# Show IP address
hostname -I

# Change hostname (temporary)
hostname new-name

# Change hostname (permanent)
sudo hostnamectl set-hostname new-name
```

---

## Process Management

### `top` — Real-Time Process Monitor

```bash
# Launch top (interactive, real-time)
top

# Inside top:
#   q        → Quit
#   k        → Kill a process (enter PID)
#   M        → Sort by memory usage
#   P        → Sort by CPU usage
#   1        → Show all CPU cores
#   h        → Help

# Better alternative: htop (install first)
sudo apt install htop
htop
```

**Understanding top output:**
```
top - 21:30:00 up 3 days | Tasks: 200 total
%Cpu(s):  2.5 us,  0.5 sy,  0.0 ni, 96.8 id
MiB Mem:   8000 total,  2000 free,  4000 used
MiB Swap:  2000 total,  1900 free,   100 used

  PID USER    PR  NI  VIRT  RES  SHR  S  %CPU  %MEM  TIME+    COMMAND
 1234 zohaib  20   0  512M  80M  30M  S   2.5   1.0  0:10.00  nginx
```

---

### `ps` — Snapshot of Running Processes

```bash
# Show processes for current user
ps

# Show ALL processes with full details (most used)
ps aux

# Breakdown of ps aux columns:
# USER    → who owns the process
# PID     → Process ID (unique number)
# %CPU    → CPU usage percentage
# %MEM    → Memory usage percentage
# COMMAND → What program is running

# Find a specific process (combine with grep)
ps aux | grep nginx
ps aux | grep python
ps aux | grep "java"

# Show process tree (parent-child relationships)
ps axjf
pstree
```

---

### `kill` / `pkill` / `killall` — Terminate Processes

```bash
# Kill by PID
kill 1234

# Force kill (cannot be ignored)
kill -9 1234
sudo kill -9 1234

# Kill by process name
pkill nginx
pkill -9 python

# Kill all instances of a program
killall apache2
killall -9 node

# Common kill signals:
#   -1  (SIGHUP)  → Reload config (graceful restart)
#   -9  (SIGKILL) → Force kill (immediate)
#   -15 (SIGTERM) → Graceful terminate (default)
```

---

### `jobs` / `bg` / `fg` — Background & Foreground Jobs

```bash
# Run command in background
long_running_script.sh &

# List background jobs
jobs

# Bring job to foreground
fg %1

# Send running job to background (Ctrl+Z first, then bg)
bg %1

# Keep job running after logout
nohup script.sh &
```

---

## Memory Management

### `free` — Display Memory Usage

```bash
# Show memory info
free

# Human-readable (GB/MB)
free -h

# Show in megabytes
free -m

# Update every 2 seconds (like watch)
free -h -s 2

# Output breakdown:
#             total   used   free  shared  buff/cache  available
# Mem:         8.0G   4.0G   1.0G    200M       3.0G       3.5G
# Swap:        2.0G   100M   1.9G
```

> 💡 **`available` is the real free memory you can use!**  
> `free` column doesn't count buffer/cache which Linux can release.

---

### `vmstat` — Virtual Memory Stats

```bash
# Show system statistics
vmstat

# Refresh every 2 seconds, 5 times
vmstat 2 5

# Memory stats with MB
vmstat -s -S M
```

---

## Service Management

### `systemctl` — Control Services (systemd)

```bash
# Start a service
sudo systemctl start nginx
sudo systemctl start mysql

# Stop a service
sudo systemctl stop nginx

# Restart a service
sudo systemctl restart nginx

# Reload config without restart
sudo systemctl reload nginx

# Enable service to start on boot
sudo systemctl enable nginx

# Disable from starting on boot
sudo systemctl disable nginx

# Check service status
sudo systemctl status nginx

# List all running services
systemctl list-units --type=service --state=running

# Show failed services
systemctl --failed
```

---

## Quick Reference Cheatsheet

| Command | What It Does | Example |
|---|---|---|
| `uname -a` | All system info | `uname -a` |
| `uname -r` | Kernel version | `uname -r` |
| `uptime` | System uptime + load | `uptime` |
| `top` | Live process monitor | `top` |
| `htop` | Better process monitor | `htop` |
| `ps aux` | All processes | `ps aux` |
| `ps aux \| grep x` | Find process | `ps aux \| grep nginx` |
| `kill -9 PID` | Force kill process | `kill -9 1234` |
| `pkill name` | Kill by name | `pkill nginx` |
| `free -h` | Memory usage | `free -h` |
| `systemctl start` | Start service | `systemctl start nginx` |
| `systemctl status` | Check service | `systemctl status sshd` |

---

## Practice Exercises

```bash
# 1. Explore your system
uname -a
uptime
hostname

# 2. Monitor processes
top          # Press q to quit
ps aux | head -20

# 3. Find specific processes
ps aux | grep bash
ps aux | grep ssh

# 4. Check memory
free -h

# 5. Start/stop a service (if nginx installed)
sudo systemctl status nginx
sudo systemctl start nginx
sudo systemctl status nginx
sudo systemctl stop nginx
```

---

> ➡️ Next: [Users, Groups & Permissions](../02-users-permissions/README.md)
