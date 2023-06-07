# DevContainerSampleBug

shows a bug in DevContainers Docker in Docker feature

## Reproduction

1. Start an ubuntu docker container and check the `resolv.conf` file.

```bash
docker run ubuntu /bin/bash -c "cat /etc/resolv.conf"
```

you should see something like this:

```bash
# DNS requests are forwarded to the host. DHCP DNS options are ignored.
nameserver 192.168.65.7
```

The nameserver that is shown there is the internal Docker IP address from your WSL. This DNS will redirect DNS requests to the DNS server that is configured in your Windows OS.

2. Now change in the `.devcontainer.json` the code to use the compose file:

```json
{
    ...
    //"image": "mcr.microsoft.com/devcontainers/base:bullseye",
    "dockerComposeFile": "devcontainer.docker-compose.yml",
    "service": "devcontainer",
   ...
}
```

After this change rebuild the devcontainter and repeat the step from before:

```bash
docker run ubuntu /bin/bash -c "cat /etc/resolv.conf"
```

now you should see things like that:

```bash
options ndots:0

nameserver 8.8.8.8
nameserver 8.8.4.4
```

And this will be an issue if you company network blocks DNS requests to the internet. Then all your docker in docker containers are basically offline