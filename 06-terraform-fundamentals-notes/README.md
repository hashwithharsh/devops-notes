# Terraform Fundamentals Notes

**Author:** Harsh Yadav

My Terraform notebook for IaC, providers, state, modules, AWS examples, drift and troubleshooting.

I am keeping this as a long-term study/reference repo. The useful part for me is the combination of explanation, practical commands, small labs and the troubleshooting notes I tend to forget.

I don't expect every note to have exactly the same structure. I write more detail for topics that I actually need to revisit often.


## My placement reference

## My Terraform rule

I read the plan.

Especially when it contains:

```text
+   create
~   update
-   destroy
-/+ replace
```

A replacement deserves more attention than an in-place update. If I cannot explain why Terraform wants to change a resource, I am not ready to apply the plan.

## How I study this repo

I study Terraform by changing one small thing at a time and reading the plan before applying it.

My progression is:

```text
HCL basics
   ↓
providers / resources
   ↓
variables / outputs
   ↓
data sources
   ↓
state
   ↓
modules
   ↓
remote state
   ↓
AWS infrastructure
   ↓
drift / troubleshooting
```

My normal workflow is:

```bash
terraform fmt
terraform validate
terraform plan
terraform apply
```

I also practice looking at state:

```bash
terraform state list
terraform state show <address>
```

If Terraform wants to replace a resource, I stop and understand why before applying.

For interview revision, I focus on the relationship between configuration, state and real infrastructure because that explains most of the important Terraform behavior.
