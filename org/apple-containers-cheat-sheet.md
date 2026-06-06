# `container` cheat sheet — Ubuntu

## 1. Create + enter a named, persistent box

```bash
container run -it --name ubuntu ubuntu:latest bash
```

Inside, do your installs:

```bash
apt update && apt install -y vim git curl python3 pipx
pipx ensurepath
# install pi (one of these)
curl -fsSL https://pi.dev/install.sh | sh
# or: npm i -g @mariozechner/pi-coding-agent
```

Exit with `Ctrl+D` or `exit`. The container stops but is **not removed** — state is kept.

> `--rm` would have deleted it on exit. **Don't use it** if you want to keep the box.

## 2. Next time: re-enter and resume

```bash
container start -i ubuntu   # -i reattaches stdin so you get a shell
```

`container exec -it ubuntu bash` also works for a second shell while it's running.

## 3. Save it as your own image (optional, but the cleanest "persist")

```bash
# stop it first so the FS is quiescent
container stop ubuntu
container commit ubuntu ubuntu-dev:latest

# from now on:
container run -it --name ubuntu-dev ubuntu-dev:latest bash
```

`commit` snapshots the filesystem into a new image. Use this when you want to throw away the container itself but keep the installed software.

## 4. Lifecycle

```bash
container ls -a        # all containers (running + stopped)
container start NAME   # restart a stopped one
container stop NAME    # graceful stop (SIGTERM, then SIGKILL after 5s)
container rm NAME      # delete a stopped container
container rm -f NAME   # force-delete even if running
container image ls     # your local images
container image rm IMG # delete an image
```

## 5. Handy flags for `run`

| flag | meaning |
|---|---|
| `-d` | detach (run in background) |
| `--rm` | auto-delete on stop (**the opposite of persist**) |
| `--name X` | stable name (else you get a random ID) |
| `-p 8080:80` | publish host:container port |
| `-v ~/work:/work` | bind-mount host dir |
| `--cpus N` / `--memory 4g` | resource limits |
| `--init` | proper init (reaps zombies, forwards signals) |

## 6. When you're done for the day

```bash
container stop ubuntu           # keep the box
container stop ubuntu && container rm ubuntu   # toss it
container system stop           # shut the whole service down
```

## TL;DR

- **Persist** → use `--name X`, no `--rm`, then `container start -i X` later.
- **Don't persist** → add `--rm`, or `rm` it after stopping.
- **Snapshot the software** → `container commit X myimg:tag`.
