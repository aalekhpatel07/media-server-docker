# Jellyfin

Starting Jellyfin with HW acceleration.

```sh
docker run \
    --runtime nvidia \
    --gpus=all \
    --name jellyfin \
    --detach \
    --restart unless-stopped \
    -v /srv/jellyfin/config:/config \
    -v /srv/jellyfin/cache:/cache \
    -v /media/nvme1n1p1/jellyfin-data:/media \
    --network=host \
    jellyfin/jellyfin:latest
```

# Immich


