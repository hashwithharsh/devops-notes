# Terraform Troubleshooting Playbook

When Terraform behaves unexpectedly, I separate three things:

```text
configuration
state
real infrastructure
```

## Plan is surprising

First:

```bash
terraform plan
terraform state list
terraform state show <address>
```

Then I ask:

- Did configuration change?
- Did somebody change the resource manually?
- Did provider behavior change?
- Is the attribute immutable?
- Is Terraform trying to replace instead of update?

## Replacement

If I see:

```text
-/+ resource
```

I stop before applying.

Replacement can mean destroying and creating a resource, which can have downtime or change an address.

## Drift

I can create a small manual change in a lab and run:

```bash
terraform plan
```

That gives me a practical way to see the difference between declared configuration, Terraform's state and the real object.

## Import

For an existing resource, I practice import against a disposable resource first. Importing does not magically create good configuration; I still need configuration that describes the object I want Terraform to manage.

## My rule

If Terraform proposes a change I don't understand:

**do not apply first and investigate later.**

Read the plan and find out why.
