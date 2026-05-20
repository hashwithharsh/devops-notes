# Ansible Fundamentals Notes

**Author:** Harsh Yadav

My Ansible notes for inventory, modules, playbooks, variables, roles, Vault and idempotent configuration.

I am keeping this as a long-term study/reference repo. The useful part for me is the combination of explanation, practical commands, small labs and the troubleshooting notes I tend to forget.

I don't expect every note to have exactly the same structure. I write more detail for topics that I actually need to revisit often.


## My placement reference

## The Ansible idea I want to keep

The playbook describes the state I want.

That means a second run matters. If the first run creates the configuration and the second run keeps changing the same things, I stop and investigate.

Idempotency is one of the practical reasons I use configuration management instead of a collection of shell commands.

## How I study this repo

I learn Ansible by applying it to disposable Linux hosts.

I normally go:

```text
inventory
   ↓
SSH connectivity
   ↓
ad-hoc commands
   ↓
playbooks
   ↓
variables / facts
   ↓
handlers
   ↓
roles
   ↓
templates
   ↓
Vault
   ↓
larger server workflow
```

For a new playbook I first test connectivity:

```bash
ansible all -i inventory.ini -m ping
```

Then I run the playbook in check mode when useful:

```bash
ansible-playbook -i inventory.ini site.yml --check
```

The most important practical exercise for me is running the same playbook twice and understanding why the second run should normally report little or no unnecessary change.

When revising, I concentrate on desired state, idempotency, inventory, variables, roles and troubleshooting.
