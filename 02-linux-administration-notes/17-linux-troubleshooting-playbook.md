# Linux Troubleshooting Playbook

This is the sequence I want to remember when somebody gives me a vague ticket like "the server is slow" or "the application is down".

I first turn the statement into a specific symptom.

## Basic state

```bash
hostnamectl
uptime
who
date
```

I want to know what machine I am on and whether the problem is recent.

## CPU and load

```bash
uptime
top
ps aux --sort=-%cpu | head
```

High load does not automatically mean high CPU, so I check what is actually consuming resources.

## Memory

```bash
free -h
ps aux --sort=-%mem | head
```

I look at available memory and swap activity rather than only staring at the "used" number.

## Disk

```bash
df -Th
df -ih
sudo du -xhd1 /var | sort -h
```

A full filesystem and an inode problem can look similar from the application side.

## Service

```bash
systemctl status <service>
journalctl -u <service> -n 100 --no-pager
```

I read the error before restarting the service.

## Network

```bash
ip addr
ip route
ss -lntup
```

If the application should listen on port 8080:

```bash
ss -lntp | grep ':8080'
curl -v http://127.0.0.1:8080/
```

If local access works but remote access doesn't, I move outward to firewall, cloud security group, route and upstream network.

## My rule

I try to find the **first broken layer**:

```text
resources
   ↓
OS
   ↓
network
   ↓
service/process
   ↓
application
```

Restarting everything may make a ticket look fixed for five minutes, but it does not tell me what actually failed.
