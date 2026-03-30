# Bash Scripting Foundations

I am keeping Bash notes because small Linux automation tasks come up everywhere in DevOps.

## First script

```bash
#!/usr/bin/env bash

echo "Hello from Bash"
```

Run:

```bash
chmod +x hello.sh
./hello.sh
```

## Variables

```bash
NAME="Harsh"
echo "$NAME"
```

I quote variables when they can contain spaces:

```bash
FILE="/home/harsh/my notes.txt"
cat "$FILE"
```

## Exit status

```bash
echo $?
```

A normal convention is:

```text
0     success
non-0 failure
```

That matters in automation because a CI job or monitoring script needs a reliable success/failure signal.

## Safer script starting point

For scripts where I care about failures:

```bash
set -euo pipefail
```

I still need to understand commands that are intentionally allowed to fail; this is not a substitute for proper error handling.

## Arguments

```bash
NAME="${1:-world}"
echo "Hello $NAME"
```

## Practical server-health script

```bash
#!/usr/bin/env bash
set -euo pipefail

echo "Hostname: $(hostname)"
echo "Kernel:   $(uname -r)"

echo
echo "Uptime:"
uptime

echo
echo "Memory:"
free -h

echo
echo "Disk:"
df -h /
```

I prefer small scripts like this to a giant Bash program doing unrelated jobs.

## Debugging

Syntax:

```bash
bash -n script.sh
```

Trace:

```bash
bash -x script.sh
```

If a script works in my terminal but fails in cron, systemd or CI, I check PATH, working directory, user, permissions and environment variables.

## My rule

Bash is excellent glue. If the script becomes a complicated state-management system, I should probably look at Python, Ansible, Terraform or another purpose-built tool.
