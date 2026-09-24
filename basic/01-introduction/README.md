<div align="center">

# 📖 Module 01 — Introduction to Linux

### Understanding What Linux Is and How It Works

[![Back to Basic](https://img.shields.io/badge/⬅️%20Back-Basic%20Branch-brightgreen)](../README.md)

</div>

---

## 📌 Table of Contents

- [What is Linux?](#what-is-linux)
- [Linux vs Unix](#linux-vs-unix)
- [The Kernel](#the-kernel)
- [The Shell](#the-shell)
- [The Bootloader](#the-bootloader)
- [Linux Distributions](#linux-distributions)
- [Practice Exercises](#practice-exercises)

---

## What is Linux?

**Linux** is an open-source, Unix-like **operating system kernel** created by **Linus Torvalds** in **1991**.

It acts as the **core interface** between computer hardware and its processes (programs).

```
Simple Definition:
Linux = Open Source + Unix-like + Free + Powerful
```

### Key Facts:
- 🐧 **Created by:** Linus Torvalds (Finland, 1991)
- 📜 **License:** GNU General Public License (GPL) — free to use, share, modify
- 💻 **Used on:** Servers, Android phones, Supercomputers, Smart TVs, Cars
- 🌐 **Market Share:** 96%+ of world's web servers run Linux

---

## Linux vs Unix

| Feature | Unix | Linux |
|---|---|---|
| **Created** | 1969 by Bell Labs (AT&T) | 1991 by Linus Torvalds |
| **Cost** | Paid / Proprietary | Free & Open Source |
| **Source Code** | Closed | Open (anyone can view/modify) |
| **Community** | Limited | Huge global community |
| **Examples** | macOS, Solaris, AIX, HP-UX | Ubuntu, CentOS, Debian, Kali |
| **Hardware** | Specific vendors | Runs on almost anything |

### The Story:
```
1969 → Unix born at Bell Labs (Ken Thompson + Dennis Ritchie)
         ↓
1983 → Richard Stallman: GNU Project (free software for all)
         ↓
1987 → MINIX created (Unix-like, for teaching)
         ↓
1991 → Linus Torvalds + GNU tools = Linux! 🐧
```

---

## The Kernel

The **kernel** is the heart ❤️ of Linux. It runs in **kernel space** (privileged mode) and handles:

```
┌────────────────────────────────────────┐
│              KERNEL JOBS               │
├────────────────────────────────────────┤
│  1. CPU Management   → Who runs when?  │
│  2. Memory Mgmt      → RAM allocation  │
│  3. Device Drivers   → Hardware access │
│  4. File Systems     → Read/write data │
│  5. Networking       → Data packets    │
│  6. Security         → Access control  │
└────────────────────────────────────────┘
```

### Kernel Types:
| Type | Description | Example |
|---|---|---|
| **Monolithic** | All services in one block | Linux |
| **Microkernel** | Minimal kernel, rest in user space | Minix |
| **Hybrid** | Mix of both | Windows NT, macOS |

> 💡 **Linux uses a monolithic kernel** — fast and efficient!

---

## The Shell

The **shell** is your **command-line interface** to talk to the kernel.

```
YOU → [Shell] → [Kernel] → [Hardware] → Result
```

### Popular Shells:

| Shell | Full Name | Default On |
|---|---|---|
| `bash` | Bourne Again Shell | Most Linux distros |
| `zsh` | Z Shell | macOS (since 2019), Oh My Zsh |
| `sh` | Bourne Shell | POSIX standard |
| `fish` | Friendly Interactive Shell | Some modern distros |
| `dash` | Debian Almquist Shell | Ubuntu (for scripts) |

### Check your current shell:
```bash
echo $SHELL
# Output: /bin/bash

# Switch to zsh
chsh -s /bin/zsh
```

---

## The Bootloader

The **bootloader** is the first program that runs when your computer powers on. It **loads the operating system** into memory.

```
Power ON
   ↓
BIOS/UEFI (hardware self-check)
   ↓
Bootloader (GRUB)
   ↓
Linux Kernel loads
   ↓
Init System starts (systemd)
   ↓
Login Prompt 🎉
```

### Common Bootloaders:
| Bootloader | Description |
|---|---|
| **GRUB** | Grand Unified Bootloader — most common on Linux |
| **GRUB2** | Updated version of GRUB (modern standard) |
| **LILO** | Old Linux Loader (legacy) |
| **systemd-boot** | Lightweight, part of systemd |

---

## Linux Distributions

A **distribution (distro)** = Linux Kernel + GNU Tools + Package Manager + Desktop

```
Linux Kernel (Core)
     ↓
┌────────────────────────────────────────────────────┐
│ Distribution = Kernel + Tools + Software + Desktop │
└────────────────────────────────────────────────────┘
```

### Popular Distros by Category:

| Category | Distros | Best For |
|---|---|---|
| 🟢 **Beginner** | Ubuntu, Linux Mint, Pop!_OS | New Linux users |
| 🔵 **Enterprise** | RHEL, CentOS, Rocky Linux | Production servers |
| 🟡 **Intermediate** | Debian, Fedora, openSUSE | Developers |
| 🔴 **Advanced** | Arch Linux, Gentoo | Power users |
| 🔐 **Security** | Kali Linux, Parrot OS | Ethical hacking |
| ☁️ **Cloud/DevOps** | Amazon Linux, Ubuntu Server | AWS, GCP, Azure |

---

## Practice Exercises

Try these in your terminal after you understand the concepts:

```bash
# 1. Check which kernel version you are running
uname -r

# 2. Check which shell you're using
echo $SHELL

# 3. See all available shells on your system
cat /etc/shells

# 4. Check system info
uname -a

# 5. Check bootloader info (if using GRUB)
grub-install --version
```

---

> ➡️ Next: [Navigation & File Operations](../02-navigation-file-ops/README.md)
