# Media Server
This is a media server compose file complete with the media service (jellyfin/plex), download client, dashboard, discovery services, and reverse proxy.

This isn't configured for external connection. If you want offsite connection, I'd suggest setting up a VPN using Tailscale or Wireguard. An illustration of my setup can be seen in [here](#example-setup)

# What's in it
The docker-compose contains all these services:
- [Traefik](https://github.com/traefik/traefik)
- [Jellyfin](https://github.com/jellyfin/jellyfin)/[Plex](https://github.com/plexinc/plex-media-server)
- [Jellyseerr](https://github.com/Fallenbagel/jellyseerr)/[Overseerr](https://github.com/sct/overseerr)
- [qBittorrent](https://github.com/qbittorrent/qBittorrent)/[Transmission](https://github.com/transmission/transmission)
- Arr stack
    - [Prowlarr](https://github.com/Prowlarr/Prowlarr)
    - [Sonarr](https://github.com/Sonarr/Sonarr)
    - [Radarr](https://github.com/Radarr/Radarr)
    - [Lidarr](https://github.com/Lidarr/Lidarr)
    - [Bazarr](https://github.com/morpheus65535/bazarr)
- [Gluetun](https://github.com/qdm12/gluetun)
- [FireSolverr](https://github.com/FireSolverr/FireSolverr)
- [Watchtower](https://github.com/containrrr/watchtower)
- [Portainer](https://github.com/portainer/portainer)

You can chose which services to run and whether to run it or no by changing the `COMPOSE_PROFILES` Environment Variable in `.env`. you can choose whether you want Jellyfin or Plex (and its relevant serr service), qBittorrent or Transmission, Portainer, etc. Refer to `.env.example` for available profile options.

Traefik automatically discover which services you enabled and will map `http://<service>.<DOMAIN_NAME>/` to its corresponding service endpoint. For example, if `DOMAIN_NAME=media.home`, `http://jellyfin.media.home` will redirect to jellyfin:8096. However, make sure your PC can resolve `DOMAIN_NAME` into the docker host server's IP. 

# How to use it
1. Download the `docker-compose.yml` file
2. Configure the `.env`
3. Run `docker compose up`
4. Done

# Example Setup for Easy External Access
TBA - too lazy to make a good diagram.

basically, I have a proxmox running these VMs/LXCs:
- Docker (where the Media server will run) + Tailscale
- AdGuard Home + Tailscale
- Nginx Proxy Manager (Not necessary for minimal setup)

AdGuard is set to resolve `*.home` to Nginx Proxy Manager. Nginx Proxy Manager then reverse proxy `media.home` and `*.media.home` to the Media server, and also handle SSL certificate _(I use self generated certificate because why the fuck not? why bother setting up Let's Encrypt when it's all gonna be through VPN?)_.

If you don't want to run NPM, just set AdGuard to resolve `media.home` to your Media server.

In Tailscale, I set the AdGuard machine as a Nameserver restricted to `home` domain. This way every `*.home` FQDN will resolve through AdGuard. The AdGuard machine is also setup to Subnets my internal network 192.168.1.0/24.

And that's it. with this setup you should be able to access `media.home` anywhere so long as you're connected to your Tailscale network.

Tip: Management will be astronomically easier if you also set AdGuard as the DHCP server for your intranet.
# Contact
If you have any questions or suggestions, please don't contact me. Make a new Issue and pray someone will answer it.

or make a PR.