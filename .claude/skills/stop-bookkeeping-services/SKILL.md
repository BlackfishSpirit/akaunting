---
name: stop-bookkeeping-services
description: Use when stopping local development - stops PHP artisan serve and Docker MySQL
disable-model-invocation: true
allowed-tools: Bash
---

# Stop Bookkeeping Services

Stop all Bookkeeper development services. Run each step sequentially.

## Step 1: Stop PHP Dev Server

Kill any running `php artisan serve` processes:

```bash
pkill -f "artisan serve" 2>/dev/null || taskkill //F //IM php.exe 2>/dev/null
```

Try both Unix and Windows approaches since we're on Git Bash on Windows.

## Step 2: Stop Docker MySQL

```bash
docker stop akaunting-db
```

## Step 3: Verify and Report

Confirm both services are stopped:

- PHP: `pgrep -f "artisan serve"` should return nothing
- MySQL: `docker ps --filter name=akaunting-db --format "{{.Status}}"` should be empty

Report the status of each service to the user.
