# 📦 Linux Automatic Partition Mounting Guide
## (NTFS, ext4, xfs with User & Permission Control)

------------------------------------------------------------------------------------

##📌 Overview

This guide explains how to:

- Automatically mount partitions at boot
- Mount NTFS and Linux filesystems
- Control ownership and permissions
- Restrict access to specific users or groups
- Safely configure `/etc/fstab`
- Avoid boot failures

Compatible with:
RHEL / CentOS / Rocky Linux / Fedora / Ubuntu

----------------------------------------------------------------------------------------

# 1️⃣ What Is /etc/fstab?

`/etc/fstab` (File System Table) is a configuration file used by Linux to mount filesystems automatically during system boot.

If a partition is not listed in `/etc/fstab`, it will NOT mount automatically after reboot.

-----------------------------------------------------------------------------------------

# 2️⃣ fstab File Format

Each line contains 6 fields:

<device>  <mount_point ex:- mountpoints like /, /boot, or a mount directory such as /home/harsh/software_backup>  <filesystem>  <options>  <dump>  <pass>

Example:

UUID=xxxx-xxxx  /data  xfs  defaults  0 0

Field Explanation:
1. Device (UUID recommended)
2. Mount Point (directory)
3. Filesystem type
4. Mount options
5. Dump (usually 0)
6. Pass (fsck order, usually 0 for data disks)

--x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x-x

# 3️⃣ Step-by-Step Mounting Procedure

------------------------------------------------------------------------------------------------------------

## Step 1 — Identify the Partition

```bash
lsblk -f  or  blkid

Find:
- Filesystem type
- UUID
- Device name

⚠ Always use UUID instead of /dev/sdX (device names can change).

--------------------------------------------------------------------------------------------------------------

## Step 2 — Create Mount Directory

```bash
sudo mkdir -p /mount/path        Example: sudo mkdir -p /home/harsh/software_backup

---------------------------------------------------------------------------------------------------------------

## Step 3 — Backup fstab (Important)

```bash
sudo cp /etc/fstab /etc/fstab.backup

## Step 4 — Edit fstab

```bash
sudo nano /etc/fstab

Add your new mount entry at the bottom.
----------------------------------------------------------------------------------------------------------------

# 4️⃣ Mounting Linux Filesystems (ext4, xfs, btrfs)

Basic example:

UUID=xxxx  /mountpoint or directory  xfs  defaults  0 0

Recommended safer option:

UUID=xxxx  /mountpoint or directory   xfs  defaults,nofail  0 0

for example:- UUID=12972GSJJJUP23   /home/harsh/software_backup   ntfs-3g defaults,nofail 0 0

What `nofail` does:
If disk is missing, system still boots.

Use `nofail` for:
- Secondary storage
- Backup disks
- External drives

Do NOT use `nofail` for:
- /
- /boot
- /boot/efi

---

# 5️⃣ Mounting NTFS Partitions (Windows Drives)

Linux requires ntfs-3g.

Install if needed:

```bash
sudo dnf install ntfs-3g

Basic NTFS mount:

UUID=xxxx  /data  ntfs-3g  defaults,nofail  0 0

--------------------------------------------------------------------------------

# 6️⃣ Controlling Ownership & Permissions (NTFS Only)

NTFS does NOT support native Linux permissions.

We must use:

- uid=
- gid=
- umask=

---

## 🔹 Mount NTFS As Specific User

Find user ID:

```bash
id username

Example output:
uid=1000

fstab entry:

UUID=xxxx  /data  ntfs-3g  defaults,nofail,uid=1000,gid=1000,umask=022  0 0

for example:- UUID=12972GSJJJUP23   /home/harsh/software_backup   ntfs-3g defaults,nofail,uid=< value of the uid of user whom you want to be owner of this mount ex:- uid 1000 (user=harsh),uid=0 (root)>,gid= < a specific  group you want to make owner of this mount ex:- gid 1000 (harsh),gid=0 (root), gid=10 (wheel group <sudo>) > 0 0

Result:
- User owns files
- Permission behaves like 755

---

## 🔹 Restrict Access to Root + Wheel Group

Check wheel group:

```bash
getent group wheel
```

Example:
wheel:x:10:

fstab entry:

UUID=xxxx  /secure  ntfs-3g  defaults,nofail,uid=0,gid=10,umask=007  0 0

Result:
- root → full access
- wheel group → full access
- others → no access
- Permission = 770

---

# 7️⃣ Understanding umask

Formula:

777 - umask = final permission

Examples:

umask=022 → 755  
umask=007 → 770  
umask=000 → 777  
umask=077 → 700  

---

# 8️⃣ Apply & Test Configuration

After editing /etc/fstab, we must reload and test.

---

## 🔹 Why `systemctl daemon-reload`?

```bash
sudo systemctl daemon-reload

Reason:

- systemd manages mount units.
- `/etc/fstab` entries are converted into systemd mount units at boot.
- When we modify `/etc/fstab`, systemd may not immediately recognize changes.
- `daemon-reload` tells systemd to re-read its configuration files.
- It prevents stale mount configuration issues.

In short:
👉 It refreshes systemd after configuration changes.

---

## 🔹 Why `mount -a`?

```bash
sudo mount -a

Meaning:

- Mount ALL filesystems listed in `/etc/fstab`
- EXCEPT those marked with `noauto`

Why we use it:

- Tests the new fstab entry immediately
- Detects syntax errors before reboot
- Prevents boot failure
- Applies mounts without restarting system

If `mount -a` shows no error:
✔ Your configuration is correct  
✔ Safe to reboot  

If it shows error:
❌ Fix fstab before reboot  

---

# 9️⃣ Verify Mount

```bash
lsblk -f  or  df -h

Check directory ownership:

```bash
ls -ld /mountpoint

----------------------------------------------------------------------------------------------

# 🔟 Troubleshooting

Permission denied while editing fstab:

Use:

```bash
sudo nano /etc/fstab
```

System boot failure:

- Caused by invalid fstab entry
- Always test with `mount -a`
- Use `nofail` for non-critical disks

NTFS unsafe state error:

Disable Windows Fast Startup.

---

# 1️⃣1️⃣ Best Practices

✔ Always use UUID  
✔ Always test with `mount -a`  
✔ Backup fstab before editing  
✔ Use `nofail` for non-critical disks  
✔ Do not modify root (/) entries carelessly  

---

# 1️⃣2️⃣ Quick Templates

Linux filesystem:

UUID=xxxx  /mnt/data  xfs  defaults,nofail  0 0

NTFS (user owned):

UUID=xxxx  /mnt/data  ntfs-3g  defaults,nofail,uid=1000,gid=1000,umask=022  0 0

NTFS (root + wheel restricted):

UUID=xxxx  /mnt/secure  ntfs-3g  defaults,nofail,uid=0,gid=10,umask=007  0 0

---

# ✅ Summary

Automatic Mount Workflow:

1. Identify partition
2. Create mount directory
3. Backup fstab
4. Edit /etc/fstab
5. Run systemctl daemon-reload
6. Run mount -a
7. Verify
8. Reboot safely

---

End of Document
