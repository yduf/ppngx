# ppngx: Podman + Paperless-ngx

This is a **Fork** to install [Paperless-ngx](https://github.com/paperless-ngx/paperless-ngx) with [Rootless Podman](https://github.com/containers/podman/blob/main/docs/tutorials/rootless_tutorial.md) inside a [Podman Pod](https://docs.podman.io/en/latest/markdown/podman-pod.1.html).

It will launch Redis, Tika, Gotenberg, and Paperless-ngx inside a self-contained pod.

This setup is using SQLite as backend (since I see no reason to move to postgresql...)

## Feature of this Fork

Rather than executing start.sh, use `paperless.yml` to describe all pod setup.

## Setup

0. Ensure `podman` packages are installed
1. `podman play kube ./paperless.yaml`    setup according to kube config
2.  Wait a bit and make sure <http://localhost:8000> is loading paperless. You can check status of individual component:

```bash
$ podman ps
CONTAINER ID  IMAGE                                       COMMAND       CREATED         STATUS                   PORTS                   NAMES
12fe4e4cddf2  localhost/podman-pause:4.9.3-0                            50 seconds ago  Up 51 seconds            0.0.0.0:8000->8000/tcp  d28730c88434-infra
63049c24408a  docker.io/library/redis:7                   redis-server  50 seconds ago  Up 51 seconds (healthy)  0.0.0.0:8000->8000/tcp  paperless-redis
6ab2675548fd  docker.io/gotenberg/gotenberg:7.8                         50 seconds ago  Up 51 seconds (healthy)  0.0.0.0:8000->8000/tcp  paperless-gotenberg
35a9b5e3633c  docker.io/apache/tika:3.2.0.0                             50 seconds ago  Up 51 seconds            0.0.0.0:8000->8000/tcp  paperless-tika
b6ac1b43b61b  ghcr.io/paperless-ngx/paperless-ngx:2.16.3                50 seconds ago  Up 50 seconds (healthy)  0.0.0.0:8000->8000/tcp  paperless-webserver
```

3. Add a superuser to paperless-ngx with:

```bash
$ podman exec -it paperless-webserver python manage.py createsuperuser
```

Didn't support other part of the config yet (SFTP / postgres) since I am just discovering Paperless-ngx.

**Note: Tearing down**  
When adjusting the yaml, you have to teardown the pod first, since podman does not automatically reconcile updates in the YAML spec with running containers, so do:
```bash
$ podman pod rm paperless -f
# and then again
$ podman play kube ./paperless.yaml
```

If you need to look at some container logs
```bash
$ podman logs b6ac1b43b61b -f # use container id listed from command above
```


## Systemd Config

Startup can be automated through Systemd + [Podman Quadlet](https://www.redhat.com/sysadmin/quadlet-podman)
Requirement: Podman 4.4 (it include Quadlet)

from [Deploying a multi-container application using Podman and Quadlet](https://www.redhat.com/sysadmin/multi-container-application-podman-quadlet)

```bash
# The place where we will store the definition
$ mkdir -p $HOME/.config/containers/systemd/
```

**paperless.kube** stored in `$HOME/.config/containers/systemd/`
```ini
[Install]
WantedBy=default.target

[Kube]
# Point to the yaml file in the same directory
Yaml=/mnt/paperless/paperless.yaml
# Publish the envoy proxy data port => this has to match otherwise service won't start
PublishPort=8000:8000
```

```bash
# to start pod
$ systemctl --user daemon-reload
$ systemctl --user start paperless.service
```

 <span style="color:green">●</span>  After a while everything should be green
```bash
$ systemctl --user status 
```


### see MIT License applying to start.sh

## Changelog

2025-06-06
- add healthcheck
- remove call to `/usr/local/bin/paperless_cmd.sh` that disapears in later version
- specify all component version to avoid unexpect broken system linked to unexpected update.

2023-10-21
- first version
