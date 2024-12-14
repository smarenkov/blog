---
title: Cloudflared Tunnel
date: 2024-10-19
description: A quick guide to setup Cloudflared Tunnel
tags: 
    - Cloudflared
    - Tunnel
categories:
    - Cloudflared
    - Tunnel
---

## Step 1: Login to Cloudflare

```bash
cloudflared login
```

## Step 2: Create a New Tunnel

Replace `tunnel_name` with your desired tunnel name.
```bash
cloudflared tunnel create tunnel_name
```

### Check Existing Tunnels

Verify the created tunnel by listing all existing tunnels:
```bash
cloudflared tunnel list
```

Example Output:
```bash
ID             NAME           CREATED       CONNECTIONS
tunnel_uuid    tunnel_name    created_at
```

In your `.cloudflared` directory, the `tunnel_uuid.json` file should automatically be created.

## Step 3: Configure the Tunnel

Edit or create if not exist a `.config.yaml` file in your `.cloudflared` directory
```yaml
tunnel: tunnel_uuid
credentials-file: ./tunnel_uuid.json

ingress:
  - hostname: web.your_domain
    service: http://localhost:5173
  - hostname: api.your_domain
    service: http://localhost:3000
  - service: http_status:404
```

## Step 4: Verify Folder Structure
Your `.cloudflared` directory should look like this:
```bash
tunnel_uuid.json -- created after 'cloudflared tunnel create'
cert.pem         -- created after 'cloudflared login'
config.yml       -- your custom configuration file
```

## Step 5: Add DNS Records

Map your domain to the tunnel by adding DNS records:
```bash
cloudflared tunnel route dns tunnel_uuid web.your_domain
cloudflared tunnel route dns tunnel_uuid api.your_domain
```

## Step 6: Start the Tunnel

Run the tunnel:
``` bash
cloudflared tunnel run tunnel_name
```

Ensure your local services are running on the specified ports.