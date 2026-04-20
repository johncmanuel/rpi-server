# rpi-server

## Running the server 

1. Run `sudo docker compose pull` for any updates
2. Run `sudo tailscale serve --https=443 off && sudo docker compose down && sudo docker compose up -d --remove-orphans`

> The tailscale part is for pneuma
