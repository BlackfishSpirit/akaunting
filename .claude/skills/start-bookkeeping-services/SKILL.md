---
name: start-bookkeeping-services
description: Use when starting local development - starts Docker MySQL and PHP artisan serve
disable-model-invocation: true
allowed-tools: Bash
---

# Start Bookkeeping Services

Start all services needed for local Bookkeeper development. Run each step sequentially.

## Step 1: Docker MySQL

Check if the `akaunting-db` container exists and start it:

```bash
docker start akaunting-db 2>/dev/null || docker run --name akaunting-db -e MYSQL_ROOT_PASSWORD=pass -e MYSQL_DATABASE=akaunting -p 3306:3306 -d mysql:8.0
```

## Step 2: Wait for MySQL

Poll until MySQL accepts connections (max 30 seconds):

```bash
for i in $(seq 1 30); do docker exec akaunting-db mysqladmin ping -uroot -ppass --silent 2>/dev/null && break || sleep 1; done
```

## Step 3: Start PHP Dev Server

Run `php artisan serve` in the background:

```bash
cd E:/ClaudeCode/Bookkeeper && php artisan serve
```

Use `run_in_background: true` for this command.

## Step 4: Verify and Report

Check both services are running, then report to the user:

- MySQL: `docker exec akaunting-db mysqladmin ping -uroot -ppass --silent`
- Artisan: Check the background task output for "Server running on"

Report the status of each service and the URL (http://127.0.0.1:8000).
