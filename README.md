<div align="center">

# 🐧 Linux: Basic to Pro

### A Complete Linux Guidebook — From Zero to DevOps Hero

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Branches](https://img.shields.io/badge/Branches-Basic%20|%20Advance%20|%20Pro-blue)](#-repo-structure)
[![Author](https://img.shields.io/badge/Author-Zohaib%20Hassan-green)](https://github.com/Zohaib1195)
[![Mentorship](https://img.shields.io/badge/Mentorship-TrainWithShubham-orange)](https://www.trainwithshubham.com)

> 📖 A structured, community-friendly Linux learning repository covering everything from basic shell navigation to professional-level DevOps and storage management.

</div>

---

## 📌 Table of Contents

- [What is Linux?](#-what-is-linux)
- [Linux vs Unix](#-linux-vs-unix)
- [History of Linux](#-history-of-linux)
- [How Linux Works](#-how-linux-works)
- [Linux Architecture](#-linux-architecture)
- [Why Learn Linux?](#-why-learn-linux)
- [Repo Structure](#-repo-structure)
- [How to Use This Repo](#-how-to-use-this-repo)
- [Learning Path](#-learning-path)
- [Contributing](#-contributing)

---

## 🐧 What is Linux?

**Linux** is a free, open-source, Unix-like **operating system kernel** first created by **Linus Torvalds** in **1991**. It is the backbone of the modern tech world — powering everything from Android phones to cloud servers, supercomputers, and IoT devices.

> "Linux is the operating system of the internet." — Anonymous

Linux is not just an OS — it is a **kernel**. The kernel is the core that manages:
- 🖥️ **CPU scheduling** — what process runs when
- 🧠 **Memory management** — allocating and freeing RAM
- 💾 **Hardware communication** — drivers for disks, network cards, USB
- 🔐 **Security and access control** — file permissions, namespaces

---

## 🆚 Linux vs Unix

| Feature | Unix | Linux |
|---|---|---|
| **Origin** | Bell Labs (AT&T), 1969 | Linus Torvalds, 1991 |
| **License** | Proprietary (paid) | Open-Source (free) |
| **Source Code** | Closed / Restricted | Publicly available |
| **Cost** | Expensive | Free |
| **Examples** | macOS, AIX, HP-UX, Solaris | Ubuntu, CentOS, Kali, Fedora |
| **Community** | Limited | Massive global community |
| **Hardware** | Specific hardware only | Runs on almost any hardware |
| **Portability** | Less portable | Highly portable |

**In short:** Unix came first and Linux was inspired by it — but Linux became far more widespread due to being free and open-source.

---

## 📜 History of Linux

```
1969  →  Unix created at Bell Labs (AT&T) by Ken Thompson & Dennis Ritchie
1983  →  Richard Stallman starts GNU Project (free software movement)
1987  →  Andrew Tanenbaum creates MINIX (Unix-like teaching OS)
1991  →  Linus Torvalds (age 21) creates Linux kernel, posts on Usenet
         "I'm doing a (free) operating system (just a hobby)..."
1992  →  Linux kernel released under GNU GPL license
1993  →  Slackware — first Linux distribution created
1994  →  Linux kernel version 1.0 released
2000s →  Enterprise adoption: Red Hat, SUSE, Ubuntu
2003  →  CentOS, Fedora launched
2004  →  Ubuntu founded by Mark Shuttleworth
2008  →  Android (Linux-based) launches on mobile
2011  →  Linux dominates 90%+ of supercomputers
2020s →  Linux powers 96%+ of world's web servers
         Microsoft Azure, AWS, GCP — all run Linux heavily
```

---

## ⚙️ How Linux Works

When you type a command in Linux, here's what happens under the hood:

```
You Type a Command
      ↓
   [Shell]  ← bash / zsh / sh (interprets your command)
      ↓
  [Kernel]  ← Linux Kernel (translates to hardware instructions)
      ↓
 [Hardware] ← CPU, RAM, Disk, Network Card
      ↓
  Result displayed back to you
```

---

## 🏗️ Linux Architecture

```
┌──────────────────────────────────────────┐
│              USER SPACE                  │
│  ┌─────────────────────────────────────┐ │
│  │   Applications (bash, vim, nginx)   │ │
│  └─────────────────────────────────────┘ │
│  ┌─────────────────────────────────────┐ │
│  │   Shell (bash, zsh, sh)             │ │
│  └─────────────────────────────────────┘ │
│  ┌─────────────────────────────────────┐ │
│  │   GNU Libraries (glibc)             │ │
│  └─────────────────────────────────────┘ │
├──────────────────────────────────────────┤
│             KERNEL SPACE                 │
│  ┌─────────────────────────────────────┐ │
│  │   System Call Interface             │ │
│  ├─────────────────────────────────────┤ │
│  │   Process Mgmt | Memory Mgmt        │ │
│  │   File Systems | Network Stack      │ │
│  │   Device Drivers                    │ │
│  └─────────────────────────────────────┘ │
├──────────────────────────────────────────┤
│              HARDWARE                    │
│      CPU | RAM | Disk | Network          │
└──────────────────────────────────────────┘
```

### Key Components:
| Component | Role |
|---|---|
| **Kernel** | Core of Linux — manages everything |
| **Shell** | Your interface to the kernel (bash, zsh) |
| **Bootloader** | Loads the OS when machine powers on (GRUB) |
| **Init System** | First process after boot (systemd / SysV) |
| **File System** | How data is stored (ext4, xfs, btrfs) |
| **Package Manager** | Installs software (apt, yum, dnf, pacman) |

---

## 🌟 Why Learn Linux?

| Reason | Details |
|---|---|
| 🌐 **Powers the Internet** | 96%+ of web servers run Linux |
| ☁️ **Cloud & DevOps** | AWS, GCP, Azure all use Linux underneath |
| 🔐 **Cybersecurity** | Kali Linux is the standard for security pros |
| 🤖 **AI & Data Science** | Most ML environments run on Linux |
| 💰 **Career Value** | Linux skills = higher salaries in tech |
| 🆓 **Free & Open Source** | No licensing cost, ever |
| ⚡ **Performance** | Faster, lighter than Windows for servers |

---

## 📁 Repo Structure

This repository is organized into **3 branches**, each representing a skill level:

```
Linux-Basic_to_Pro/
│
├── main            ← 📖 You are here (Introduction & Overview)
├── basic           ← 🟢 Beginner: Navigation, Files, System Basics
├── advance         ← 🟡 Intermediate: Users, Permissions, Networking
└── pro             ← 🔴 Expert: Text Processing, Storage, LVM, AWS
```

### Branch Details:

| Branch | Level | Topics Covered |
|---|---|---|
| [`basic`](../../tree/basic) | 🟢 Beginner | File navigation, directories, basic commands, I/O |
| [`advance`](../../tree/advance) | 🟡 Intermediate | Users & groups, file permissions, networking, diagnostics |
| [`pro`](../../tree/pro) | 🔴 Expert | grep/sed/awk, LVM, disk management, AWS EBS |

---

## 🚀 How to Use This Repo

### Option 1: Browse on GitHub
Click the branch names above to navigate to each level directly.

### Option 2: Clone Locally

```bash
# Clone the full repo
git clone https://github.com/Zohaib1195/Linux-Basic_to_Pro.git
cd Linux-Basic_to_Pro

# Switch to basic branch
git checkout basic

# Switch to advance branch
git checkout advance

# Switch to pro branch
git checkout pro
```

---

## 🗺️ Learning Path

Follow this path for the best learning experience:

```
START HERE (main branch)
     ↓
🟢 basic branch
   ├── 01 - Introduction to Linux
   └── 02 - Navigation & File Operations
     ↓
🟡 advance branch
   ├── 01 - System Administration
   ├── 02 - Users, Groups & Permissions
   └── 03 - Networking & Diagnostics
     ↓
🔴 pro branch
   ├── 01 - Advanced Text Processing
   └── 02 - Storage, LVM & AWS EBS
     ↓
🏆 YOU'RE A LINUX PRO!
```

---

## 🤝 Contributing

Contributions are welcome! If you want to:
- Fix a typo or error
- Add a new command or example
- Improve explanations

Please open a Pull Request or raise an Issue.

---

## 👨‍💻 Author

**Zohaib Hassan**
- GitHub: [@ZohaibHassan](https://github.com/ZohaibHasan2280168)
- Mentorship: [@TrainWithShubham](https://github.com/TrainWithShubham/)

---

<div align="center">

⭐ **If this repo helped you, please give it a star!** ⭐

*"The more you learn, the more you earn."*

</div>
