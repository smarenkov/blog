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

## Hostnames on the Same Tunnel

### Step 1: Login to Cloudflare

```bash
cloudflared login
```

### Step 2: Create a New Tunnel

Replace `tunnel_name` with your desired tunnel name.
```bash
cloudflared tunnel create tunnel_name
```

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

### Step 3: Configure the Tunnel

Edit or create if not exist a `.config.yaml` file in your `.cloudflared` directory
```yaml
tunnel: tunnel_uuid
credentials-file: ./tunnel_uuid.json

ingress:
  - hostname: api.your_domain
    service: http://localhost:3000
  - hostname: web.your_domain
    service: http://localhost:5173
  - service: http_status:404
```

### Step 4: Verify Folder Structure
Your `.cloudflared` directory should look like this:
```bash
tunnel_uuid.json -- created after 'cloudflared tunnel create'
cert.pem         -- created after 'cloudflared login'
config.yml       -- your custom configuration file
```

### Step 5: Add DNS Records

Map your domain to the tunnel by adding DNS records:
```bash
cloudflared tunnel route dns tunnel_uuid api.your_domain
cloudflared tunnel route dns tunnel_uuid web.your_domain
```

### Step 6: Start the Tunnel

Run the tunnel:
``` bash
cloudflared tunnel run tunnel_name
```

Ensure your local services are running on the specified ports.

## Hostnames on Different Tunnels

### Step 1: Login to Cloudflare

```bash
cloudflared login
```

### Step 2: Create a New Tunnels

Replace `tunnel_name_api` and `tunnel_name_web` with your desired tunnel names.
```bash
cloudflared tunnel create tunnel_name_api
cloudflared tunnel create tunnel_name_web
```

Verify the created tunnels by listing all existing tunnels:
```bash
cloudflared tunnel list
```

Example Output:
```bash
ID                 NAME               CREATED       CONNECTIONS
tunnel_uuid_api    tunnel_name_api    created_at
tunnel_uuid_web    tunnel_name_web    created_at
```

In your `.cloudflared` directory, the `tunnel_uuid_api.json` and `tunnel_uuid_web.json` files should automatically be created.

### Step 3: Configure the Tunnels

Create a `.config_api.yaml` and `.config_web.yaml` files in your `.cloudflared` directory
```yaml
tunnel: tunnel_uuid_api
credentials-file: ./tunnel_uuid_api.json

ingress:
  - hostname: api.your_domain
    service: http://localhost:3000
  - service: http_status:404
```
```yaml
tunnel: tunnel_uuid_web
credentials-file: ./tunnel_uuid_web.json

ingress:
  - hostname: web.your_domain
    service: http://localhost:5173
  - service: http_status:404
```

### Step 4: Verify Folder Structure

Your `.cloudflared` directory should look like this:
```bash
tunnel_uuid_api.json -- created after 'cloudflared tunnel create'
tunnel_uuid_web.json -- created after 'cloudflared tunnel create'
cert.pem             -- created after 'cloudflared login'
config_api.yml       -- your custom configuration file
config_web.yml       -- your custom configuration file
```

### Step 5: Add DNS Records

Map your domain to the tunnel by adding DNS records:
```bash
cloudflared tunnel route dns tunnel_uuid_api api.your_domain
cloudflared tunnel route dns tunnel_uuid_web web.your_domain
```
If these subdomains are already assigned to different tunnels, navigate to the Cloudflare dashboard and remove the associated DNS records.

### Step 6: Start the Tunnels

Run the tunnel:
``` bash
cloudflared tunnel --config ~/.cloudflared/config-api.yml run
cloudflared tunnel --config ~/.cloudflared/config-web.yml run
```
