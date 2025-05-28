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

## Setup nftables on the VPS

1. Install `nftables` and enable ip forwarding so we can use the VPS's kernel's builtin packet routing framework:

```sh
sudo dnf install -y nftables
sudo systemctl enable nftables
sudo systemctl start nftables
echo "net.ipv4.ip_forward=1" >> /etc/sysctl.d/99-ip_forward.conf
sysctl -p
```

2. Copy the [vps.nftables.conf](./vps.nftables.conf) file to VPS's `/etc/nftables.conf`

```sh
scp vps.nftables.conf root@<vps-pub-ip>:/etc/nftables.conf
```

3. Flush and restore the nftables ruleset.

```sh
nft flush ruleset
nft -f /etc/nftables.conf
```

## Success!

[https://photos.aalekhpatel.com](https://photos.aalekhpatel.com) and [https://movienight.aalekhpatel.com](https://movienight.aalekhpatel.com) should be accessible now.
