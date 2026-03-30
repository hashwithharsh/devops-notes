# DNF & RPM Enterprise Mastery – Complete DevOps Practical Guide

Author: Harsh Yadav
Target OS: RHEL / Rocky Linux / AlmaLinux / CentOS / Amazon Linux
Level: Production-Ready DevOps Engineer

---

# 📌 QUICK INDEX

1. Why DNF Matters in DevOps
2. Core DNF Operations 
3. Dependency Management Deep Dive 
4. Repository Management (Enterprise Governance) 
5. Security Patch Management (CVE Handling) 
6. Version Locking Strategy 
7. Transaction History & Rollback 
8. Upgrade vs Distro-Sync (Senior Concept) 
9. Air-Gapped Environment Strategy
11. Enterprise Troubleshooting Playbook
11. Repository Priority Strategy 
12. RPM Engine Essentials (Must-Know) 
13. Real Enterprise Failure Scenarios 
14. Production Best Practices 
15. Interview-Level Questions 

---

# 1️⃣ WHY DNF MATTERS IN DEVOPS

DNF is the modern package manager replacing YUM.

In enterprise production systems, DNF handles:

- Secure software installation
- Dependency resolution
- Controlled upgrades
- Security patch management
- Version locking
- Rollback after failed updates
- Repository governance
- Compliance & auditing

DNF uses the RPM engine internally.

Production Rule:
Use DNF for operations. 
Use RPM for deep debugging.

---

# 2️⃣ CORE DNF OPERATIONS

## Install Package

```
sudo dnf install nginx
```

Install specific version:
```
sudo dnf install nginx-1.24.0
```

Install local RPM:
```
sudo dnf install ./custom-app.rpm
```

Industry Use:
- Deploy application dependencies
- Install monitoring agents
- Deploy internal company software

---

## Upgrade Packages

Upgrade all:
```
sudo dnf upgrade
```

Upgrade specific:
```
sudo dnf upgrade nginx
```

Industry Use:
- Monthly patch cycles
- Security hotfix rollout
- Controlled staging → production deployment

---

## Remove Package

```
sudo dnf remove nginx
```

Used for:
- Removing deprecated services
- Cleaning compromised systems

---

## Search & Info

```
dnf search nginx
dnf info nginx
dnf list installed
```

---

# 3️⃣ DEPENDENCY MANAGEMENT (CRITICAL IN PRODUCTION)

View dependency tree:
```
dnf deplist nginx
```

Check broken dependencies:
```
dnf check
```

Synchronize system with repo state:
```
sudo dnf distro-sync
```

Important:
Never use:
```
rpm --nodeps
```
in production unless in a controlled environment.

Dependency conflicts are one of the most common production incidents.

---

# 4️⃣ REPOSITORY MANAGEMENT (ENTERPRISE GOVERNANCE)

Repository config directory:
```
/etc/yum.repos.d/
```

List enabled repos:
```
dnf repolist
```

List all repos:
```
dnf repolist all
```

Enable repo:
```
sudo dnf config-manager --set-enabled repo_name
```

Disable repo:
```
sudo dnf config-manager --set-disabled repo_name
```

Clean metadata:
```
sudo dnf clean all
```

Rebuild cache:
```
sudo dnf makecache
```

Enterprise Rule:
Production servers should use internal repository mirrors — not public internet repos.

---

# 5️⃣ SECURITY PATCH MANAGEMENT (CVE RESPONSE)

Check security updates:
```
dnf updateinfo list security
```

Apply only security patches:
```
sudo dnf update --security
```

Enterprise Workflow:
1. CVE advisory released
2. Patch tested in staging
3. Application compatibility validated
4. Production patch applied during change window

DevOps Responsibility:
Security without breaking applications.

---

# 6️⃣ VERSION LOCKING STRATEGY

Install versionlock plugin:
```
sudo dnf install 'dnf-command(versionlock)'
```

Lock a package:
```
sudo dnf versionlock add nginx
```

List locks:
```
dnf versionlock list
```

Remove lock:
```
sudo dnf versionlock delete nginx
```

Industry Use:
- Prevent auto-upgrade breakage
- Maintain certified dependency versions
- Control runtime compatibility

---

# 7️⃣ TRANSACTION HISTORY & ROLLBACK

View history:
```
dnf history
```

Inspect transaction:
```
dnf history info <ID>
```

Undo specific transaction:
```
sudo dnf history undo <ID>
```

Rollback system to earlier state:
```
sudo dnf history rollback <ID>
```

Important:
Rollback may fail if old versions no longer exist in repositories.

Enterprise Solution:
Maintain repository snapshots or internal mirrors.

---

# 8️⃣ DNF UPGRADE VS DISTRO-SYNC (SENIOR CONCEPT)

## dnf upgrade
- Upgrades packages to newest available versions
- Never downgrades
- Used for standard patching

## dnf distro-sync
- Aligns installed packages exactly to repository versions
- Can upgrade OR downgrade
- Ensures full system consistency

When to Use distro-sync:
- After repo changes
- After partial upgrade failure
- After dependency graph corruption
- During OS version alignment

Senior Insight:
Upgrade = forward movement 
Distro-sync = state correction

---

# 9️⃣ AIR-GAPPED ENVIRONMENT STRATEGY

Download package only:
```
dnf download nginx
```

Download with dependencies:
```
dnf download --resolve nginx
```

Used for:
- Offline production environments
- Secure data centers
- Compliance-controlled systems

---

# 🔟 ENTERPRISE TROUBLESHOOTING PLAYBOOK

If installation fails:

1️⃣ Check repos
```
dnf repolist
```

2️⃣ Clear cache
```
dnf clean all
```

3️⃣ Rebuild cache
```
dnf makecache
```

4️⃣ Check dependency issues
```
dnf check
```

5️⃣ Sync system
```
dnf distro-sync
```

6️⃣ Inspect logs
```
/var/log/dnf.log
```

7️⃣ If RPM DB corrupted
```
sudo rpm --rebuilddb
```

This is production-grade troubleshooting logic.

---

# 1️⃣1️⃣ REPOSITORY PRIORITY STRATEGY (SENIOR LEVEL)

Inside repo config file:
```
priority=1
```

Lower number = higher priority.

Why Important:
Multiple repos may provide the same package.

Enterprise Use:
- Internal repo overrides public repo
- Security compliance
- Prevent unstable third-party overrides

---

# 1️⃣2️⃣ RPM ENGINE ESSENTIALS (MUST KNOW)

List all installed packages:
```
rpm -qa
```

Package info:
```
rpm -qi nginx
```

List installed files:
```
rpm -ql nginx
```

Find which package owns a file:
```
rpm -qf /usr/sbin/nginx
```

Verify package integrity:
```
rpm -V nginx
```

Rebuild RPM database:
```
sudo rpm --rebuilddb
```

Used for:
- Security audits
- Incident response
- Binary tampering detection
- DB corruption recovery

---

# 1️⃣3️⃣ REAL ENTERPRISE FAILURE SCENARIOS

Scenario 1: Patch breaks application 
→ `dnf history undo` 
→ `dnf versionlock add package`

Scenario 2: Third-party repo overrides base OS 
→ Check repo priority 
→ Adjust priority 
→ `dnf distro-sync`

Scenario 3: Repo metadata corrupted 
→ `dnf clean all` 
→ `dnf makecache`

Scenario 4: Suspicious binary modified 
→ `rpm -V package`

Scenario 5: Unknown file discovered 
→ `rpm -qf file`

---

# 1️⃣4️⃣ PRODUCTION BEST PRACTICES

- Always patch in staging first
- Lock critical packages
- Use internal repository mirrors
- Apply security updates regularly
- Avoid forced installs
- Maintain transaction audit history
- Validate application health after upgrade
- Understand dependency graph impact

---

# 1️⃣5️⃣ INTERVIEW-LEVEL QUESTIONS

1. Difference between upgrade and distro-sync?
2. Why can rollback fail?
3. How does DNF resolve dependencies?
4. Why is repository priority critical?
5. How do you prevent breaking production during upgrades?
6. What would you do if a third-party repo overrides system packages?
7. How do you handle air-gapped systems?
8. What does `rpm -V` actually verify internally?

---

END OF DOCUMENT
