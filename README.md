# ppngx: Podman + Paperless-ngx

This is a **Fork** to install [Paperless-ngx](https://github.com/paperless-ngx/paperless-ngx) with [Rootless Podman](https://github.com/containers/podman/blob/main/docs/tutorials/rootless_tutorial.md) inside a [Podman Pod](https://docs.podman.io/en/latest/markdown/podman-pod.1.html).

It will launch Redis, Tika, Gotenberg, SFTPGo and Paperless-ngx inside a self-contained pod.

This setup is using SQLite as backend (since I see no reason to move to postgresql...)


## Fork

Rather than executing start.sh, use Kubeernetes.yml to describe pod setup

## Setup

0. Ensure `podman` packages are installed
1. `podman play kube ./paperless.yaml`    setup according to kube config
2.  Wait a bit and make sure <http://localhost:8000> is loading paperless.
3. Add a superuser to paperless-ngx with:

  ```bash
  podman exec -it paperless-paperless-webserver python manage.py createsuperuser
  ```

Didn't support other part of the config yet (SFTP / postgres) since I am just discoverint Paperless-ngx.

# License

MIT License

Copyright (c) 2022 Joe Doss

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
