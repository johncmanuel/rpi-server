# rpi-server

Contains the Docker configuration for my RPI server running on RPI OS Lite (basically a port of Debian Trixie).

Each application in the `apps/` directory is isolated within its own network namespace using a Tailscale sidecar container. This provides each app with its own Tailnet IP and MagicDNS domain, completely preventing host port conflicts (like multiple apps needing port 80/443).

## Global Deployment

1. **Configure Environment Variables**
   Before starting the stack, ensure you have configured your global `.env` file in the root directory.
   
   **Recommendation:** Use a [Tailscale OAuth Client](https://tailscale.com/blog/docker-tailscale-guide) rather than a static Auth Key. Create an OAuth client in your Tailscale admin panel with `Auth Keys: Write` (or `Devices: Write`) permissions, and paste the Client Secret (which starts with `tskey-client-`) directly into the `TS_OAUTH_SECRET` variable in the root `.env` file. The sidecars will automatically inherit and use it to generate ephemeral machine keys securely!
   ```bash
   cp .env.example .env
   # Edit .env to set your TS_OAUTH_SECRET and any other global variables
   ```

2. **Run the services**
   From the root of this repository (`rpi-server/`), simply run:
   ```bash
   sudo docker compose pull
   sudo docker compose down
   sudo docker compose up -d --remove-orphans
   ```
   Docker Compose will automatically traverse into your `apps/` directories, build the stack, and launch each app alongside its respective Tailscale sidecar.

3. **Adding New Apps**
   When you migrate a new app (e.g. `apps/homepage`), simply add it to the `include` block in the root `docker-compose.yml`:
   ```yaml
   include:
     - path: ./apps/pneuma/docker-compose.yml
     - path: ./apps/bordle/docker-compose.yml
     - path: ./apps/homepage/docker-compose.yml
   ```

### Notes on Tailscale State
Because we mount a Docker volume for the tailscale state (`/var/lib/tailscale`) in each app, the sidecar containers will remember their identities after the first successful boot. They will *not* consume a new machine slot on your Tailnet every time you restart the global docker compose stack.
