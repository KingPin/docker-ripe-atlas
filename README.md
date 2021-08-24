# RIPE Atlas Docker Image

This is the [RIPE Atlas software probe](https://atlas.ripe.net/docs/software-probe/) packaged as a Docker image. the original code is at https://github.com/Jamesits/docker-ripe-atlas but I needed this on arm64 so here we are, thanx FOSS.

## Registery

This image is located both on dockerhub and github
* https://hub.docker.com/r/kingpin/ripe-atlas-docker
* `docker pull kingpin/ripe-atlas-docker:latest`
* https://github.com/KingPin/ripe-atlas-docker/pkgs/container/ripe-atlas-docker 
* `docker pull ghcr.io/kingpin/ripe-atlas-docker:latest`


## Requirements

* 1 CPU core (of course)
* 20MiB memory
* 100MiB HDD
* A Linux installation with Docker installed
* Internet access

## Tags

The following prebuilt tags are available at [Docker Hub](https://hub.docker.com/r/kingpin/ripe-atlas-docker):

* `latest`: For arm64, amd64 devices

## Running

First we start the container:

```shell
docker run --detach --restart=always --log-opt max-size=10m \
	--cpus=1 --memory=64m --memory-reservation=64m \
	--cap-add=SYS_ADMIN --cap-add=NET_RAW --cap-add=CHOWN \
	--mount type=tmpfs,destination=/var/atlasdata,tmpfs-size=64M \
	-v /var/atlas-probe/etc:/var/atlas-probe/etc \
	-v /var/atlas-probe/status:/var/atlas-probe/status \
	-e RXTXRPT=yes \
	--name ripe-atlas --hostname "$(hostname --fqdn)" \
	kingpin/ripe-atlas-docker:latest
```

Then we fetch the generated public key:

```shell
cat /var/atlas-probe/etc/probe_key.pub
```

[Register](https://atlas.ripe.net/apply/swprobe/) the probe with your public key. After the registration being manually processed, you'll see your new probe in your account.

### Auto Update

Use this recipe for auto updating the docker container.

```shell
docker run -d -v /var/run/docker.sock:/var/run/docker.sock --name watchtower containrrr/watchtower --cleanup --label-enable
```

Then start the RIPE Atlas container with argument `--label=com.centurylinklabs.watchtower.enable=true`.

### Backup

All the config files are stored at `/var/atlas-probe`. Just backup it.

