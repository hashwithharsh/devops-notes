# AWS Architecture Notes I Want to Remember

I find AWS much easier when I draw the request path instead of studying services independently.

## Basic web application

```text
Internet
   |
 Route 53
   |
  ALB
   |
+--+--+
|     |
EC2  EC2
|     |
+--+--+
   |
  RDS
```

The exact architecture depends on the application, but the exercise helps me reason about dependencies.

## If the browser cannot reach the application

I check:

```text
DNS
 ↓
ALB listener
 ↓
target group health
 ↓
EC2 security group
 ↓
application listener
 ↓
application response
```

## If EC2 cannot reach the database

I check:

```text
RDS endpoint
 ↓
route
 ↓
RDS security group
 ↓
database availability
 ↓
database port
 ↓
application credentials
```

## If an AWS API call is denied

I start with:

```bash
aws sts get-caller-identity
```

Then I identify:

```text
principal
action
resource
region
policy evaluation
```

I don't immediately attach administrator permissions.

## Main takeaway

AWS troubleshooting is mostly dependency tracing. Knowing the service name is not enough; I need to know what network path, identity and resource relationship sits underneath it.
