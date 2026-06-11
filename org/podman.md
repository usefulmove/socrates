# Ubuntu 24.04 Podman Container

```bash
podman run -d --name ubuntu2404 docker.io/library/ubuntu:24.04 sleep infinity
podman exec -it ubuntu2404 bash
```

Changes persist in the named container.

```bash
podman stop ubuntu2404
podman start ubuntu2404
podman exec -it ubuntu2404 bash
```

Optional reusable image:

```bash
podman commit ubuntu2404 localhost/ubuntu2404-custom
```
