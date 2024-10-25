# pi-net

A [Docker](https://www.docker.com/) container configuration for [Tailscale](https://tailscale.com/) with [Pi-hole](https://pi-hole.net/) using Docker Compose.

This sets up two containers and allows the Tailscale container to be used as an exit node with ad blocking provided by the Pi-hole container.

> [!NOTE]
> This configuration requires manually supplying the Pi-hole container's IP address to the Tailscale Docker Compose in order to correctly build the Tailscale container. \
> Instructions are included in the [Getting Started](#getting-started) section.
>
> I would like to have this set up without manually having to retrieve and set the IP address but this is what I've got working at the moment.

## Getting started

### Create the Docker network

```bash
docker network create -d bridge pi-net-work
```

### Set Pi-hole container variables

Set variables as defined by Pi-hole's [environment variables](https://github.com/pi-hole/docker-pi-hole/#environment-variables).

```env
# pi-hole/.env

TZ=America/New_York
WEBTHEME=default-dark
WEBPASSWORD=my-pihole-admin-password
```

### Build & Run Pi-hole container

From the `pi-hole/` directory, run:

```bash
docker compose up -d
```

The Pi-hole admin ui will be available at `http://localhost:8081/admin`

### Get the Pi-hole container's IP address

From the host machine, run:

```bash
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' pihole
```

> [!NOTE]
> `pihole` is the container name specified in `pi-hole/docker-compose.yml` but the container id can also be used as the last argument:
>
> ```bash
> docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <container_name_or_id>
> ```

### Generate a Tailscale auth key

See step 2 of [Create a container for Tailscale](https://tailscale.com/kb/1453/quick-guide-docker#create-a-container-for-tailscale).

### Set Tailscale container variables

The Tailscale container configuration requires the Pi-hole container's IP address and the Tailscale auth key:

```env
# tailscale/.env

PIHOLE_IP=X.X.X.X
TS_AUTHKEY=my-ts-auth-key
```

### Build & Run Tailscale container

From the `tailscale/` directory, run:

```bash
docker compose up -d
```

> [!NOTE]
> The Tailscale container can be configured via the `tailscale/docker-compose.yml` file.
>
> See [Using Tailscale with Docker](https://tailscale.com/kb/1282/docker) for valid settings.
>
> **Re-run the build command after editing to update the container.**
