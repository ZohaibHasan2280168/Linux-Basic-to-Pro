<div align="center">

# 💾 Module 02 — Storage, LVM & AWS EBS

### Manage Disks, Partitions, Logical Volumes and Cloud Storage

[![Back to Pro](https://img.shields.io/badge/⬅️%20Back-Pro%20Branch-red)](../README.md)

</div>

---

## 📌 Table of Contents

- [Linux Disk Basics](#linux-disk-basics)
- [fdisk — Disk Partitioning](#fdisk--disk-partitioning)
- [parted — Modern Partitioning](#parted--modern-partitioning)
- [mkfs — Create Filesystem](#mkfs--create-filesystem)
- [mount / umount — Attach Volumes](#mount--umount--attach-volumes)
- [LVM Architecture](#lvm-architecture)
- [AWS EBS on EC2](#aws-ebs-on-ec2)
- [Quick Reference Cheatsheet](#quick-reference-cheatsheet)
- [Practice Exercises](#practice-exercises)

---

## Linux Disk Basics

### How Linux Identifies Disks

```
/dev/sda    → First SATA/SCSI disk
/dev/sdb    → Second SATA/SCSI disk
/dev/nvme0n1 → First NVMe SSD

/dev/sda1   → First partition on sda
/dev/sda2   → Second partition on sda
/dev/xvdf   → First additional disk on AWS EC2
```

### Disk Management Flow

```
Physical Disk
     ↓
Partition (fdisk / parted)
     ↓
Filesystem (mkfs)
     ↓
Mount Point (mount)
     ↓
Use It!
```

---

### View Current Disks

```bash
# List all block devices (disks and partitions)
lsblk

# Detailed disk info
lsblk -f          # with filesystem type
lsblk -o NAME,SIZE,FSTYPE,MOUNTPOINT

# View disk info with fdisk
sudo fdisk -l

# View partition info
sudo parted -l

# Check disk usage (mounted filesystems)
df -h

# Check disk usage by directory
du -sh /var/log
du -sh /home/*
```

---

## fdisk — Disk Partitioning

`fdisk` is the classic tool for creating and managing **MBR partitions** (up to 2TB).

```bash
# Open a disk for partitioning (interactive)
sudo fdisk /dev/sdb
sudo fdisk /dev/xvdf   # AWS EBS volume

# Inside fdisk interactive mode:
# p  → print current partition table
# n  → create a new partition
# d  → delete a partition
# t  → change partition type
# w  → write changes and exit (SAVES!)
# q  → quit without saving
# m  → show help menu
```

### Creating a New Partition (Step-by-Step)

```bash
sudo fdisk /dev/xvdf

# Inside fdisk:
Command (m for help): n         # new partition
Partition type: p               # primary
Partition number: 1             # first partition
First sector: [Enter]           # accept default (start of disk)
Last sector: [Enter]            # accept default (end of disk = use full disk)

Command (m for help): p         # print to verify
Command (m for help): w         # write and exit!

# Output: /dev/xvdf1 is now created!
```

---

### Partition Types

```bash
# Common partition type IDs (in fdisk 't' option):
# 83 → Linux (ext2/ext3/ext4) — most common
# 8e → Linux LVM
# 82 → Linux swap
# fd → Linux RAID
# c  → W95 FAT32 (for USB drives)
```

---

## parted — Modern Partitioning

`parted` supports **GPT partitions** (required for disks > 2TB).

```bash
# Open parted (interactive)
sudo parted /dev/sdb

# Inside parted:
# print              → show partition table
# mklabel gpt        → create GPT partition table
# mklabel msdos      → create MBR partition table
# mkpart             → create a partition
# rm 1               → remove partition 1
# quit               → exit

# Non-interactive examples:
# Create GPT partition table
sudo parted /dev/sdb mklabel gpt

# Create a partition (use full disk)
sudo parted /dev/sdb mkpart primary ext4 0% 100%

# View partitions
sudo parted /dev/sdb print
```

---

## mkfs — Create Filesystem

After partitioning, you must **format** the partition with a filesystem.

```bash
# Create ext4 filesystem (most common for Linux)
sudo mkfs -t ext4 /dev/xvdf1
# or
sudo mkfs.ext4 /dev/xvdf1

# Create ext3 filesystem (older, journaled)
sudo mkfs -t ext3 /dev/sdb1

# Create xfs filesystem (better for large files, AWS default)
sudo mkfs -t xfs /dev/xvdf1
sudo mkfs.xfs /dev/xvdf1

# Create FAT32 (for USB drives / Windows compatibility)
sudo mkfs -t vfat /dev/sdb1

# View filesystem info after creation
sudo blkid /dev/xvdf1
```

### Filesystem Comparison

| Filesystem | Best For | Max File Size | Max Volume |
|---|---|---|---|
| **ext4** | General Linux use | 16TB | 1EB |
| **xfs** | Large files, AWS | 8EB | 8EB |
| **btrfs** | Snapshots, RAID | 16EB | 16EB |
| **fat32** | USB, Windows compat | 4GB | 2TB |
| **ntfs** | Windows drives | 256TB | 256TB |

---

## mount / umount — Attach Volumes

**Mounting** makes a disk/partition accessible at a directory path.

```bash
# Create mount point (directory)
sudo mkdir /mnt/data
sudo mkdir /mnt/backup

# Mount a partition
sudo mount /dev/xvdf1 /mnt/data
sudo mount /dev/sdb1 /mnt/backup

# Mount with specific filesystem type
sudo mount -t ext4 /dev/xvdf1 /mnt/data

# Mount read-only
sudo mount -o ro /dev/xvdf1 /mnt/data

# Check what is mounted
mount
mount | grep /mnt
df -h

# Unmount (must not be in use!)
sudo umount /mnt/data

# Force unmount (if busy)
sudo umount -f /mnt/data

# Lazy unmount (when force fails)
sudo umount -l /mnt/data

# Find what is using a mount point
sudo lsof /mnt/data
sudo fuser -m /mnt/data
```

---

### Persistent Mounts with /etc/fstab

To **automatically mount** on boot, add to `/etc/fstab`:

```bash
# View current fstab
cat /etc/fstab

# Format: device   mountpoint   fstype   options   dump   pass
# Example entry:
/dev/xvdf1   /mnt/data   ext4   defaults   0   2

# Better: use UUID (more reliable than device name)
# Get UUID
sudo blkid /dev/xvdf1
# Output: /dev/xvdf1: UUID="abc123..." TYPE="ext4"

# Add to /etc/fstab:
UUID=abc123...   /mnt/data   ext4   defaults   0   2

# Test fstab without rebooting
sudo mount -a   # mount all entries in fstab

# Verify
df -h | grep /mnt/data
```

---

## LVM Architecture

**LVM (Logical Volume Manager)** adds a flexible abstraction layer over physical disks — you can resize volumes **without downtime**!

### LVM Layers

```
Physical Disks (/dev/sdb, /dev/sdc)
         ↓
Physical Volumes (PV) — disk or partition prepared for LVM
         ↓
Volume Group (VG) — pool of PVs combined
         ↓
Logical Volumes (LV) — virtual partitions (like flexible disks)
         ↓
Filesystem (ext4, xfs) + Mount Point
```

---

### Working with Physical Volumes (PV)

```bash
# Initialize disk for LVM
sudo pvcreate /dev/sdb
sudo pvcreate /dev/sdc

# View physical volumes
pvdisplay
pvs        # summary view

# Remove physical volume
sudo pvremove /dev/sdb
```

---

### Working with Volume Groups (VG)

```bash
# Create volume group (combine PVs)
sudo vgcreate my_vg /dev/sdb
sudo vgcreate data_vg /dev/sdb /dev/sdc   # combine two disks!

# View volume groups
vgdisplay
vgs        # summary view

# Extend VG (add more disk)
sudo vgextend data_vg /dev/sdd

# Remove volume group
sudo vgremove my_vg
```

---

### Working with Logical Volumes (LV)

```bash
# Create logical volume (10GB)
sudo lvcreate -L 10G -n my_lv my_vg

# Create logical volume using percentage of VG
sudo lvcreate -l 100%FREE -n data_lv data_vg

# View logical volumes
lvdisplay
lvs        # summary view

# Extend logical volume (+5GB more)
sudo lvextend -L +5G /dev/my_vg/my_lv

# Extend and resize filesystem in one command
sudo lvextend -L +5G -r /dev/my_vg/my_lv

# Resize filesystem after extending LV
sudo resize2fs /dev/my_vg/my_lv      # ext4
sudo xfs_growfs /mnt/data             # xfs (must be mounted)

# Reduce logical volume (DANGER — backup first!)
sudo lvreduce -L -2G /dev/my_vg/my_lv

# Remove logical volume
sudo lvremove /dev/my_vg/my_lv
```

---

### Full LVM Setup Example

```bash
# Step 1: Prepare physical volumes
sudo pvcreate /dev/sdb /dev/sdc

# Step 2: Create volume group
sudo vgcreate production_vg /dev/sdb /dev/sdc

# Step 3: Create logical volumes
sudo lvcreate -L 20G -n web_lv production_vg
sudo lvcreate -L 50G -n db_lv production_vg
sudo lvcreate -l 100%FREE -n backup_lv production_vg

# Step 4: Create filesystems
sudo mkfs.ext4 /dev/production_vg/web_lv
sudo mkfs.xfs  /dev/production_vg/db_lv
sudo mkfs.ext4 /dev/production_vg/backup_lv

# Step 5: Create mount points and mount
sudo mkdir -p /var/www /var/lib/mysql /backup
sudo mount /dev/production_vg/web_lv /var/www
sudo mount /dev/production_vg/db_lv /var/lib/mysql
sudo mount /dev/production_vg/backup_lv /backup

# Step 6: Add to fstab for persistence
echo "/dev/production_vg/web_lv  /var/www        ext4  defaults  0 2" | sudo tee -a /etc/fstab
echo "/dev/production_vg/db_lv   /var/lib/mysql  xfs   defaults  0 2" | sudo tee -a /etc/fstab

# Verify everything
df -h
lvs
vgs
pvs
```

---

## AWS EBS on EC2

**Amazon EBS (Elastic Block Store)** provides persistent block storage for EC2 instances — like attaching an external hard drive to your cloud server.

### EBS Volume Types

| Type | Use Case | IOPS | Throughput |
|---|---|---|---|
| **gp3** | General purpose (default) | Up to 16,000 | 1,000 MB/s |
| **gp2** | General purpose (older) | Up to 16,000 | 250 MB/s |
| **io1/io2** | High performance DBs | Up to 64,000 | 1,000 MB/s |
| **st1** | Throughput-optimized (big data) | 500 | 500 MB/s |
| **sc1** | Cold HDD (archival, cheap) | 250 | 250 MB/s |

---

### Attach and Mount an EBS Volume on EC2

```bash
# Step 1: Check available disks (after attaching in AWS Console)
lsblk
# You'll see your new volume as /dev/xvdf or /dev/nvme1n1

# Step 2: Verify it's unformatted
sudo file -s /dev/xvdf
# If output is: /dev/xvdf: data → unformatted, ready to use

# Step 3: Create partition (optional for single volume)
sudo fdisk /dev/xvdf
# n → p → 1 → Enter → Enter → w

# Step 4: Create filesystem
sudo mkfs -t ext4 /dev/xvdf1
# or for xfs:
sudo mkfs -t xfs /dev/xvdf1

# Step 5: Create mount point
sudo mkdir /mnt/data

# Step 6: Mount the volume
sudo mount /dev/xvdf1 /mnt/data

# Step 7: Verify
df -h | grep /mnt/data
lsblk

# Step 8: Make it persistent (add to fstab)
# Get the UUID
sudo blkid /dev/xvdf1

# Add to /etc/fstab (replace UUID with your actual UUID)
echo "UUID=YOUR-UUID-HERE  /mnt/data  ext4  defaults,nofail  0  2" | sudo tee -a /etc/fstab

# Test fstab
sudo mount -a
df -h
```

---

### EBS Volume with LVM on EC2

```bash
# Attach multiple EBS volumes in AWS Console
# Then on EC2:
lsblk
# /dev/xvdf   10G
# /dev/xvdg   10G

# Create PVs
sudo pvcreate /dev/xvdf /dev/xvdg

# Create VG (20GB total)
sudo vgcreate ec2_data_vg /dev/xvdf /dev/xvdg

# Create LV
sudo lvcreate -l 100%FREE -n app_lv ec2_data_vg

# Format and mount
sudo mkfs.ext4 /dev/ec2_data_vg/app_lv
sudo mkdir /app
sudo mount /dev/ec2_data_vg/app_lv /app

# Verify
df -h /app
lvs
```

### Resize EBS Volume (Zero Downtime!)

```bash
# 1. In AWS Console: Modify volume (increase size)
# 2. On EC2 (volume will show new size but not yet usable):

# Check partition table
lsblk

# Grow the partition
sudo growpart /dev/xvdf 1

# Resize the filesystem
sudo resize2fs /dev/xvdf1          # ext4
# or
sudo xfs_growfs /mnt/data           # xfs

# Verify new size
df -h /mnt/data
```

---

## Quick Reference Cheatsheet

| Command | What It Does | Example |
|---|---|---|
| `lsblk` | List block devices | `lsblk -f` |
| `fdisk /dev/sdb` | Partition a disk | `sudo fdisk /dev/xvdf` |
| `parted /dev/sdb` | Modern partitioning | `sudo parted /dev/sdb` |
| `mkfs.ext4 /dev/sdb1` | Create ext4 filesystem | `sudo mkfs.ext4 /dev/xvdf1` |
| `mkfs.xfs /dev/sdb1` | Create xfs filesystem | `sudo mkfs.xfs /dev/xvdf1` |
| `mount /dev/sdb1 /mnt` | Mount partition | `sudo mount /dev/xvdf1 /mnt/data` |
| `umount /mnt/data` | Unmount | `sudo umount /mnt/data` |
| `blkid /dev/sdb1` | Get UUID | `sudo blkid /dev/xvdf1` |
| `pvcreate /dev/sdb` | Init LVM PV | `sudo pvcreate /dev/sdb` |
| `vgcreate vg /dev/sdb` | Create VG | `sudo vgcreate my_vg /dev/sdb` |
| `lvcreate -L 10G -n lv vg` | Create LV | `sudo lvcreate -L 10G -n lv vg` |
| `lvextend -L +5G lv` | Extend LV | `sudo lvextend -L +5G /dev/vg/lv` |
| `resize2fs /dev/vg/lv` | Resize ext4 FS | `sudo resize2fs /dev/vg/lv` |
| `pvs / vgs / lvs` | View LVM status | `pvs; vgs; lvs` |

---

## Practice Exercises

```bash
# ⚠️ These exercises assume you have a spare disk/volume attached!
# On AWS: attach an EBS volume first

# Exercise 1: Explore current storage
lsblk
df -h
sudo fdisk -l

# Exercise 2: Check filesystem types
lsblk -f

# Exercise 3: Mount a temporary tmpfs (safe to practice!)
sudo mkdir /tmp/practice_mount
sudo mount -t tmpfs -o size=100M tmpfs /tmp/practice_mount
df -h /tmp/practice_mount

# Create files in it
echo "Hello from tmpfs" > /tmp/practice_mount/test.txt
ls /tmp/practice_mount/

# Unmount
sudo umount /tmp/practice_mount

# Exercise 4: View LVM (if LVM is set up)
pvs
vgs
lvs
```

---

<div align="center">

## 🏆 Congratulations!

You've completed the **Linux: Basic to Pro** journey!

```
🟢 Basic    ✅ Navigation & File Operations
🟡 Advance  ✅ System Admin, Users & Networking
🔴 Pro      ✅ Text Processing & Storage
```

**You are now a Linux Pro!** 🐧

</div>

---

> ⬅️ [Back: Text Processing](../01-text-processing/README.md) | 🏠 [Main README](../../../tree/main)
