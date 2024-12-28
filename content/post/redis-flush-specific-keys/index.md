---
title: How to Flush Specific Keys in Redis
date: 2024-12-28
description: Learn how to flush specific keys in Redis with simple commands.
tags: 
    - Redis
categories:
    - Redis
---

Redis offers commands for managing keys, such as `flushall` to clear all keys. In this post, however, we’ll focus on how to flush specific keys.

## Step 1: Identify Keys

Use `redis-cli --scan --pattern` to find keys matching a specific pattern:

```bash
redis-cli --scan --pattern "session:auth:token:*"
```

This command will return all keys that start with `session:auth:token:`.

## Step 2: Delete the Keys

Once you have the list of keys, use the `DEL` command to delete them. To automate this, use the following one-liner:

```bash
redis-cli --scan --pattern "session:auth:token:*" | xargs redis-cli DEL
```

**Explanation**:
  1. redis-cli --scan --pattern "pattern" fetches the matching keys.
  2. xargs redis-cli DEL passes the fetched keys to the DEL command for deletion.