# Bash Conditions, Loops and Functions

These are the Bash features I expect to use repeatedly in small automation scripts.

## Conditions

```bash
if [[ -f "$FILE" ]]; then
    echo "File exists"
else
    echo "File does not exist"
fi
```

String:

```bash
if [[ "$ENV" == "prod" ]]; then
    echo "Production"
fi
```

Numeric:

```bash
if (( COUNT > 10 )); then
    echo "More than ten"
fi
```

## Loops

```bash
for service in nginx sshd docker; do
    echo "Checking $service"
done
```

For arrays:

```bash
services=(nginx sshd docker)

for service in "${services[@]}"; do
    echo "$service"
done
```

I quote array expansion because Bash word splitting can produce surprising results.

## Functions

```bash
check_service() {
    local service="$1"

    if systemctl is-active --quiet "$service"; then
        echo "$service: OK"
    else
        echo "$service: FAILED"
        return 1
    fi
}
```

Then:

```bash
check_service nginx
check_service sshd
```

Using `local` prevents accidental changes to global variables.

## Practical retry loop

```bash
for i in {1..10}; do
    if systemctl is-active --quiet nginx; then
        echo "nginx is ready"
        exit 0
    fi

    echo "Waiting..."
    sleep 2
done

echo "nginx did not become ready"
exit 1
```

This pattern is useful when another process starts something and I need to wait for readiness.

## Common mistakes

I avoid:

```bash
for file in $FILES
```

when the value can contain spaces or unusual filenames.

I also avoid:

```bash
command || true
```

unless ignoring failure is genuinely intentional.

For scripts that process arbitrary filenames, I prefer null-delimited tools such as `find -print0` and corresponding `read -d ''` handling.
