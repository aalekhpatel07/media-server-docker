# Usage

## Prerequisites

### Wireguard Tunnel on a VPS.

- Provision some VPS (for example, a DigitalOcean droplet) and set up Wireguard with a config similar to [vps.wireguard](./vps.wireguard) under `/etc/wireguard/`.

```sh
sudo systemctl enable wg-quick@wg.service
sudo systemctl start wg-quick@wg.service
```

- Setup a wireguard conf on the home server (i.e. one that will run the docker compose cluster) similar to [home.wireguard](./home.wireguard) under `/etc/wireguard/`.

```sh
sudo systemctl enable wg-quick@media-server.service
sudo systemctl start wg-quick@media-server.service
```

- Test that the tunnel is set up by pinging home from the VPS, and by pinging VPS from home:

```sh
# on the VPS
ping 10.10.10.10
```

```sh
# on the home server
ping 10.10.10.1
```

### DNS records

- Setup A records for `movienight.aalekhpatel.com` and `photos.aalekhpatel.com` to the VPS's public IP (`<vps-pub-ip>`) on your domain provider.

## Start services

- Make sure your home server has an NVIDIA GPU available.

- Spin up two services:

```sh
docker compose up -d
```
1. [Jellyfin](https://jellyfin.org/) at [https://movienight.aalekhpatel.com](https://movienight.aalekhpatel.com)
2. [Immich](https://immich.app/) at [https://photos.aalekhpatel.com](https://photos.aalekhpatel.com)

and an nginx reverse proxy that listens at `http://localhost:8002` for requests for the corresponding hostnames. 

- Test that nginx works:
```sh
curl http://localhost:8002 -H 'Host photos.aalekhpatel.com
```

## Start the reverse proxy on the VPS.

1. Install `nginx` on the VPS.

```sh
# on the VPS.
sudo dnf install -y nginx
```

2. Copy the contents of [vps.nginx.conf](./vps.nginx.conf) to VPS's `/etc/nginx/nginx.conf`:

```sh
# on the home server.
scp vps.nginx.conf root@<vps-pub-ip>:/etc/nginx/nginx.conf
```

3. Enable and start nginx:

```sh
sudo systemctl enable nginx
sudo systemctl start nginx
```

> You might need to run `setsebool -P httpd_can_network_connect 1` if there are permission errors in the nginx logs.


## Success!

[https://photos.aalekhpatel.com](https://photos.aalekhpatel.com) and [https://movienight.aalekhpatel.com](https://movienight.aalekhpatel.com) should be accessible now.
