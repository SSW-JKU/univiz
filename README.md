# univiz.org

The public landing page for [univiz.org](https://univiz.org), which links to
interactive educational visualizations and tools from JKU Linz.

## Run locally

Open `index.html` in a browser, or serve the repository with any static HTTP
server. No Node.js build step is required.

## Run with Docker

The public image is published to GitHub Container Registry after every push to
`main` or `master`:

```bash
docker pull ghcr.io/neonmika/univiz:latest
docker run --rm -p 8080:80 ghcr.io/neonmika/univiz:latest
```

Open `http://localhost:8080`.

## Host on a server

The supported long-running setup keeps the application available only on the
server loopback interface and lets a host-level HTTPS reverse proxy expose it.
It includes Watchtower, which checks for a new `latest` image every 120 seconds
and replaces only the labeled application container.

Install Docker Engine and the Docker Compose plugin, then download and start
the included Compose file:

```bash
sudo mkdir -p /opt/univiz
cd /opt/univiz

sudo curl -fsSL -o docker-compose.yml \
  https://raw.githubusercontent.com/SSW-JKU/univiz/HEAD/deploy/docker-compose.yml

sudo docker compose pull
sudo docker compose up -d --remove-orphans
sudo docker compose ps
```

`univiz-app` is then reachable only at `127.0.0.1:12345`. Point the DNS records
for `univiz.org` (and optionally `www.univiz.org`) at the server, then choose
one of the following host reverse proxies.

### Caddy

Add the contents of [deploy/Caddyfile.example](deploy/Caddyfile.example) to
the host Caddy configuration and replace the domain if needed. Caddy obtains
and renews HTTPS certificates automatically when DNS points to the host.

```caddy
univiz.org, www.univiz.org {
  reverse_proxy 127.0.0.1:12345
}
```

Reload Caddy:

```bash
sudo systemctl reload caddy
```

### nginx

Add the contents of [deploy/nginx.conf.example](deploy/nginx.conf.example) to
the existing HTTPS `server` block for the domain, then reload nginx:

```bash
sudo systemctl reload nginx
```

## Published tags

Each successful default-branch build publishes:

```text
ghcr.io/neonmika/univiz:latest
ghcr.io/neonmika/univiz:<commit-sha>
```

The immutable commit tag is useful for pinning a server to a known version;
the provided Compose file follows `latest` for automatic updates.
