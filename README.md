# rpi-server

## Running the server 

For each app to start, `cd` into the desired app under `apps/` 

### Tailscale (without Caddy)

1. Run `sudo docker compose pull` for any updates
2. Run `sudo tailscale serve --https=443 off && sudo docker compose down && sudo docker compose up -d --remove-orphans && sudo tailscale serve --bg http://0.0.0.0:8989`

### Tailscale + Caddy 

1. Run `sudo docker compose pull` for any updates
2. Run `sudo docker compose down && sudo docker compose up -d --remove-orphans`

Don't forget to run `caddy fmt --overwrite` to properly format `Caddyfile`
