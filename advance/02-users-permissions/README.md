<div align="center">

# 👤 Module 02 — Users, Groups & Permissions

### Control Who Can Do What on Your Linux System

[![Back to Advance](https://img.shields.io/badge/⬅️%20Back-Advance%20Branch-yellow)](../README.md)

</div>

---

## 📌 Table of Contents

- [User Management](#user-management)
- [Group Management](#group-management)
- [File Permissions](#file-permissions)
- [Ownership](#ownership)
- [umask](#umask)
- [Compression & File Transfer](#compression--file-transfer)
- [Quick Reference Cheatsheet](#quick-reference-cheatsheet)
- [Practice Exercises](#practice-exercises)

---

## User Management

### Understanding Linux Users

Linux has 3 types of users:

| Type | UID Range | Description |
|---|---|---|
| **Root** | 0 | Superuser — has ALL permissions |
| **System Users** | 1–999 | Services (nginx, mysql, www-data) |
| **Regular Users** | 1000+ | Normal human users |

```bash
# Check your current user
whoami

# Check current user's UID and groups
id

# Switch to another user
su - username

# Switch to root
sudo su -
# or
sudo -i
```

---

### `useradd` / `adduser` — Create User

```bash
# Basic user creation
sudo useradd white

# Create user with home directory + default shell
sudo useradd -m -s /bin/bash white

# Flags explained:
# -m        → Create home directory /home/white
# -s /bin/bash → Set default shell
# -u 1500   → Specify UID
# -g group  → Set primary group
# -G g1,g2  → Add to supplementary groups
# -c "Full Name" → Add comment/description

# Full example (recommended)
sudo useradd -m -s /bin/bash -c "White Hat Hacker" -G sudo white

# Ubuntu-friendly (interactive, easier)
sudo adduser white
```

---

### `usermod` — Modify Existing User

```bash
# Add user to a group (VERY commonly used)
sudo usermod -aG sudo white
sudo usermod -aG docker zohaib

# -a = append (keep existing groups!)
# -G = supplementary groups
# WARNING: Without -a, it REPLACES all groups!

# Change user's shell
sudo usermod -s /bin/zsh white

# Lock a user account (disable login)
sudo usermod -L white

# Unlock a user account
sudo usermod -U white

# Change username
sudo usermod -l newname oldname

# Change home directory
sudo usermod -d /new/home -m white
```

---

### `userdel` — Delete User

```bash
# Delete user (keep home directory)
sudo userdel white

# Delete user AND home directory
sudo userdel -r white

# Check if user exists
getent passwd white
id white
```

---

### `passwd` — Set/Change Password

```bash
# Set password for a user (as root)
sudo passwd white

# Change your own password
passwd

# Force user to change password on next login
sudo passwd -e white

# Lock user password (disable login)
sudo passwd -l white

# Unlock user password
sudo passwd -u white
```

---

### Useful User Info Commands

```bash
# List all users
cat /etc/passwd

# List just usernames
cut -d: -f1 /etc/passwd

# See who is logged in
who
w
last

# Check your groups
groups
id
```

---

## Group Management

### `groupadd` / `groupdel` — Create/Delete Groups

```bash
# Create a new group
sudo groupadd developers
sudo groupadd testers
sudo groupadd devops

# Create group with specific GID
sudo groupadd -g 2000 developers

# Delete a group
sudo groupdel developers

# View all groups
cat /etc/group

# See groups for a specific user
groups white
id white
```

---

### `gpasswd` — Manage Group Members

```bash
# Add user to group
sudo gpasswd -a white developers

# Remove user from group
sudo gpasswd -d white developers

# Set group administrator
sudo gpasswd -A white developers
```

---

## File Permissions

### Understanding Permission Notation

Every file in Linux has 3 sets of permissions:

```
-rwxr-xr--  1  zohaib  devs  4096  Sep 24  file.txt
│└──┴──┴──  │  └─────  └───  └───  └─────  └───────
│ u   g  o  │  owner   group size   date    filename
│           │
│           └── number of hard links
└── file type: - (file), d (directory), l (symlink)

Permission sets:
  rwx → owner (u)
  r-x → group (g)
  r-- → others (o)

r = read    (4)
w = write   (2)
x = execute (1)
- = no permission (0)
```

---

### `chmod` — Change Permissions

#### Method 1: Numeric (Octal) — Most Common

```bash
# Permission values:
# r = 4, w = 2, x = 1, - = 0

# 755 = rwxr-xr-x (owner: all, group: read+exec, others: read+exec)
chmod 755 script.sh

# 644 = rw-r--r-- (owner: read+write, others: read only)
chmod 644 file.txt

# 600 = rw------- (only owner can read/write, nobody else)
chmod 600 private_key.pem

# 777 = rwxrwxrwx (everyone can do anything — DANGEROUS!)
chmod 777 public_script.sh

# Common permission values:
# 400 → read only for owner (private files)
# 600 → read/write for owner only
# 644 → read/write owner, read-only others
# 700 → all permissions for owner only
# 755 → standard for executables/directories
# 777 → full access for everyone (avoid!)

# Apply recursively to directory
chmod -R 755 /var/www/html
```

#### Method 2: Symbolic — More Readable

```bash
# Add execute permission for owner
chmod u+x script.sh

# Remove write permission for group
chmod g-w file.txt

# Add read permission for others
chmod o+r file.txt

# Set permissions for all (u, g, o)
chmod a+x script.sh

# Combine operations
chmod u+x,g-w,o+r file.txt

# Set exact permissions
chmod u=rwx,g=rx,o=r file.txt
```

---

### `chown` — Change Ownership

```bash
# Change owner
sudo chown zohaib file.txt

# Change owner and group
sudo chown zohaib:developers file.txt

# Change just the group
sudo chown :developers file.txt

# Change ownership recursively (entire directory)
sudo chown -R zohaib:developers /var/www/html

# Real DevOps example: fix web server permissions
sudo chown -R www-data:www-data /var/www/html
sudo chmod -R 755 /var/www/html
```

---

### `chgrp` — Change Group Only

```bash
# Change file group
sudo chgrp developers file.txt

# Change recursively
sudo chgrp -R developers /project/
```

---

## umask

**`umask`** sets the **default permissions** for newly created files and directories.

```bash
# View current umask
umask
# Output: 0022

# How umask works:
# Files start with max 666 (no execute for files)
# Dirs  start with max 777
# umask subtracts from these

# umask 022:
# Files: 666 - 022 = 644 (rw-r--r--)
# Dirs:  777 - 022 = 755 (rwxr-xr-x)

# umask 027:
# Files: 666 - 027 = 640 (rw-r-----)
# Dirs:  777 - 027 = 750 (rwxr-x---)

# Set umask temporarily
umask 027

# Set umask permanently (add to ~/.bashrc)
echo "umask 022" >> ~/.bashrc
```

---

## Compression & File Transfer

### `tar` — Archive Files

```bash
# Create a compressed archive (gz)
tar -czvf backup.tar.gz /var/www/

# Flags:
# c = create
# z = compress with gzip
# v = verbose (show files being processed)
# f = filename follows

# Extract archive
tar -xzvf backup.tar.gz

# Extract to specific directory
tar -xzvf backup.tar.gz -C /tmp/

# Create bzip2 compressed archive (smaller but slower)
tar -cjvf backup.tar.bz2 /var/www/

# List contents without extracting
tar -tvf backup.tar.gz

# Real use: backup with timestamp
tar -czvf backup_$(date +%Y%m%d).tar.gz /var/www/html/
```

---

### `zip` / `unzip`

```bash
# Zip files
zip archive.zip file1.txt file2.txt

# Zip entire directory
zip -r website.zip /var/www/html/

# Unzip
unzip archive.zip

# Unzip to specific directory
unzip archive.zip -d /tmp/extracted/

# List zip contents
unzip -l archive.zip
```

---

### `scp` — Secure Copy (over SSH)

```bash
# Copy local file to remote server
scp file.txt user@192.168.1.100:/home/user/

# Copy from remote to local
scp user@192.168.1.100:/home/user/file.txt ./

# Copy entire directory (-r flag)
scp -r projects/ user@192.168.1.100:/home/user/

# Copy with specific SSH key
scp -i ~/.ssh/mykey.pem file.txt ec2-user@54.x.x.x:/home/ec2-user/

# Copy on non-standard SSH port
scp -P 2222 file.txt user@server:/path/
```

---

### `rsync` — Efficient File Sync/Transfer

```bash
# Sync local to remote (basic)
rsync -avz /local/dir/ user@remote:/remote/dir/

# Flags:
# a = archive (preserves permissions, timestamps, symlinks)
# v = verbose
# z = compress during transfer

# Sync with progress
rsync -avz --progress /local/ user@remote:/remote/

# Sync and delete files not in source (mirror)
rsync -avz --delete /local/ user@remote:/remote/

# Dry run (see what would happen without doing it)
rsync -avzn /local/ user@remote:/remote/

# Sync with SSH key
rsync -avz -e "ssh -i ~/.ssh/key.pem" /local/ user@server:/remote/

# Local to local sync (backup)
rsync -avz /home/zohaib/ /backup/zohaib/
```

> 💡 **rsync is smarter than cp** — it only transfers files that changed!

---

## Quick Reference Cheatsheet

| Command | What It Does | Example |
|---|---|---|
| `useradd -m -s /bin/bash` | Create user | `useradd -m -s /bin/bash john` |
| `usermod -aG` | Add to group | `usermod -aG sudo john` |
| `userdel -r` | Delete user+home | `userdel -r john` |
| `passwd` | Set password | `passwd john` |
| `groupadd` | Create group | `groupadd devops` |
| `chmod 755` | Set permissions | `chmod 755 script.sh` |
| `chmod -R 644` | Recursive perms | `chmod -R 644 /var/www` |
| `chown user:group` | Change ownership | `chown www-data:www-data file` |
| `umask 022` | Default perms | `umask 022` |
| `tar -czvf` | Create archive | `tar -czvf backup.tar.gz /var` |
| `tar -xzvf` | Extract archive | `tar -xzvf backup.tar.gz` |
| `scp file user@host:/path` | Secure copy | `scp app.py server:/home/` |
| `rsync -avz src dst` | Sync files | `rsync -avz /local/ server:/remote/` |

---

## Practice Exercises

```bash
# 1. Create users and groups
sudo groupadd developers
sudo useradd -m -s /bin/bash -G developers dev_user
sudo passwd dev_user
id dev_user

# 2. Permission practice
mkdir permission_test
touch permission_test/file1.txt

# Set permissions
chmod 700 permission_test/
chmod 644 permission_test/file1.txt
ls -la permission_test/

# 3. Ownership practice
sudo chown dev_user:developers permission_test/file1.txt
ls -la permission_test/

# 4. Create and extract archive
tar -czvf test_backup.tar.gz permission_test/
rm -rf permission_test/
tar -xzvf test_backup.tar.gz
ls -la permission_test/

# 5. Clean up
rm -rf permission_test/ test_backup.tar.gz
sudo userdel -r dev_user
sudo groupdel developers
```

---

> ⬅️ [Back: System Admin](../01-system-administration/README.md) | ➡️ Next: [Networking](../03-networking/README.md)
