# ppngx: Podman + Paperless-ngx

This is a **Fork** to install [Paperless-ngx](https://github.com/paperless-ngx/paperless-ngx) with [Rootless Podman](https://github.com/containers/podman/blob/main/docs/tutorials/rootless_tutorial.md) inside a [Podman Pod](https://docs.podman.io/en/latest/markdown/podman-pod.1.html).

It will launch Redis, Tika, Gotenberg, and Paperless-ngx inside a self-contained pod.

This setup is using SQLite as backend (since I see no reason to move to postgresql...)

## Fork

Rather than executing start.sh, use Kubernetes.yml to describe pod setup.

## Setup

0. Ensure `podman` packages are installed
1. `podman play kube ./paperless.yaml`    setup according to kube config
2.  Wait a bit and make sure <http://localhost:8000> is loading paperless.
3. Add a superuser to paperless-ngx with:

```bash
$ podman exec -it paperless-paperless-webserver python manage.py createsuperuser
```

Didn't support other part of the config yet (SFTP / postgres) since I am just discoverint Paperless-ngx.

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


### see MIT License applying to start.sh

