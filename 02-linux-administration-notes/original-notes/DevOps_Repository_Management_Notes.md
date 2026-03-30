# 📦 RHEL Repository Management -- DevOps Notes (80:20)

------------------------------------------------------------------------

## 1️⃣ What is a Repository?

A **repository (repo)** is a software source from which Linux installs
packages.

When you run: - show all installed repos in system.
``` bash
dnf repolist 
```

When you run:

``` bash
dnf install nginx
```

System downloads nginx from a repository.

Without repo → ❌ No package installation.

------------------------------------------------------------------------

## 2️⃣ Types of Repositories

### 🟢 Online Repo

-   Internet-based
-   Requires subscription
-   Used in dev/test/cloud systems

### 🟢 Offline Repo

-   Uses ISO file
-   No internet required
-   Used in secure/air-gapped production

------------------------------------------------------------------------

## 3️⃣ Why DevOps Needs Repo Knowledge

  Reason            Why Important
  ----------------- ---------------------------------
  Stability         Prevent unexpected updates
  Security          Avoid downloading from internet
  Compliance        Use only approved packages
  Automation        CI/CD needs controlled installs
  Troubleshooting   Fix broken package systems

Production = Control + Predictability.

------------------------------------------------------------------------

## 4️⃣ Offline Repo Setup

### 🔹 VM Method

``` bash
lsblk
sudo mount /dev/sr0 /mnt/iso
```

### 🔹 Dual Boot Method

``` bash
sudo mount -o loop ~/iso/rhel-10.iso /mnt/iso
```

`-o loop` makes Linux treat ISO file as a virtual disk.

------------------------------------------------------------------------

### 🔹 Create Repo File

Location:

``` bash
/etc/yum.repos.d/
```

Create:

``` bash
sudo vi /etc/yum.repos.d/offline.repo
```

``` ini
[Offline-BaseOS]
name=Offline BaseOS
baseurl=file:///mnt/iso/BaseOS
enabled=1
gpgcheck=0

[Offline-AppStream]
name=Offline AppStream
baseurl=file:///mnt/iso/AppStream
enabled=1
gpgcheck=0
```
⚠ Important:
Must use file:///
We use file:/// because baseurl must follow standard URL format, and the triple slash means “file protocol with no hostname, starting from the root (/) of the local filesystem.”

------------------------------------------------------------------------

### 🔹 Clean & Rebuild Cache

``` bash
sudo dnf clean all
sudo dnf makecache
```
dnf clean all - removes old cached metadata and packages to prevent using outdated information.
dnf makecache - downloads fresh repository metadata so DNF can quickly search and install packages.
------------------------------------------------------------------------

## 5️⃣ Make Mount Permanent

Edit:

``` bash
sudo vi /etc/fstab
```

Add:

    /home/harsh/iso/rhel-10.iso  /mnt/iso  iso9660  loop,defaults  0  0

remark :- anymount which is not required by core linux we must include nofail it allows our system that if this mount has error system will boot without it not stucking on boot process
   anymount which is required for core linux never include nofail because it will allow system boot without core linux files hence system error means ex- /,/root,/var,/swap,/home,/root/efi  
    /home/harsh/iso/rhel-10.iso  /mnt/iso  iso9660  loop,defaults,nofail  0  0

Test:

``` bash
sudo mount -a 
```
remarks :- run mount -a after every new entry in /etc/fstab and if any error appears means you did something wrong in fstab entry, no error = hahaha all good reboot..
------------------------------------------------------------------------

## 6️⃣ Force Install from Specific Repo 

``` bash
sudo dnf install tree --disablerepo="*" --enablerepo="Offline-*"
```

this will only be for single command means only this time tree will be installed by offline repo and all other are disabled, remember only for a single commands <temporary>

Used in secure or CI/CD environments.

------------------------------------------------------------------------

## 7️⃣ Enable / Disable Repo

Temporary:

``` bash
--disablerepo="epel"
```

Permanent:

``` bash
sudo dnf config-manager --set-disabled epel
sudo dnf config-manager --set-enabled epel
```

example :- dnf repolist something like this appears rhel-10-for-x86_64-baseos-rpms
rhel-10-for-x86_64-appstream-rpms this is the repo name, when disabling or enabling repo this name is followed

sudo dnf config-manager --set-disabled rhel-10-for-x86_64-appstream-rpms
sudo dnf config-manager --set-enabled rhel-10-for-x86_64-appstream-rpms

# what it is doing internally 
its editing the repo file present in etc/yum.repos.d in this directory all your repo files are saved and in these files enable-1 is for enabled repo and enable-0 is for disabled repo.... 
means when ever you are disabling a repo permanently you are editing these value through a command shown above.
------------------------------------------------------------------------

## 8️⃣ Repo Priority

Install plugin:to make priority feature enabled..

``` bash
sudo dnf install dnf-plugins-core
```

Add inside any repo file at the end of the line you just type priority= any no. <lower is more priorities>

``` ini
priority=1
```
# Example :
[rhel-10-for-x86_64-appstream-rpms]
name = Red Hat Enterprise Linux 10 for x86_64 - AppStream (RPMs)
baseurl = https://cdn.redhat.com/...
enabled = 1
gpgcheck = 1

👉 Add priority= anywhere inside this block (usually after enabled=).


```~Lower number = Higher priority.~```

------------------------------------------------------------------------

## 9️⃣ Version Locking

Install: this is a plugin which adds feature of locking dnf verison..

``` bash
sudo dnf install python3-dnf-plugin-versionlock
```
# Check Installed Version Before Locking

```bash
dnf list installed nginx
```

Example output:

nginx.x86_64   1.24.0-1.el10   @rhel-10-for-x86_64-appstream-rpms

👉 Always check current version before locking.

# Lock package:

``` bash
sudo dnf versionlock add nginx
```
✅ Expected Output:

Adding versionlock on: <nginx-1.24.0-1.el10.*>
This locks the currently installed version.

#🔎 Advanced Usage

Lock Specific Version Manually
          sudo dnf versionlock add nginx-1.24.0-1.el10.*

Lock Major Version Only
           sudo dnf versionlock add nginx-1.24.*

This allows minor updates within 1.24 but blocks 1.25+.

# Check:

``` bash
dnf versionlock list
```
Example output:< nginx-1.24.0-1.el10.*>

This confirms the package is locked.

# Remove:

``` bash
sudo dnf versionlock delete nginx
```

Expected output: <Deleting versionlock on: nginx-1.24.0-1.el10.>*

# Remove all locks :
 bash ``` sudo dnf versionlock clear ```

# Where Locks Are Stored
     /etc/dnf/plugins/versionlock.list

View manually: cat /etc/dnf/plugins/versionlock.list

------------------------------------------------------------------------

## 🔟 Internal Repo Server (Enterprise)

Architecture:

Repo Server → All Client Servers

Steps:

1.  Install httpd
2.  Copy ISO content to `/var/www/html/`
3.  Allow firewall http
4.  Configure clients using baseurl:

```{=html}
<!-- -->
```
    http://repo-server-ip/rhel10/BaseOS

Used in banks, enterprises, secure environments.

------------------------------------------------------------------------

# 🎯 Interview Summary

-   Repository = Software source
-   Offline repo = Used in air-gapped production
-   Priority = Controls preferred repo
-   Version lock = Prevents accidental updates
-   DevOps goal = Stability, Security, Predictability

------------------------------------------------------------------------

✔ ISO Mounting\
✔ Repo Creation\
✔ Permanent Mount\
✔ Repo Control\
✔ Priority\
✔ Version Locking\
✔ Internal Repo Server

You are now industry-ready at repository level.
