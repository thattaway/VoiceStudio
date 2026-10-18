# Self-hosted VoiceStudio (web app)

Runs VoiceStudio on your own server and serves it as a web app you open in a
browser. The image is built from this checkout, so any changes you make to the
code are included.

## Requirements

- Linux on x86-64 (amd64)
- Docker with Compose
- NVIDIA GPU with the NVIDIA Container Toolkit (or see "No GPU" below)
- About 15 GB of disk for the image, plus about 4 GB for the default models

## First start

```bash
git clone https://github.com/thattaway/VoiceStudio.git
cd VoiceStudio/deploy/selfhost
cp .env.example .env
python3 -c 'import secrets; print(secrets.token_urlsafe(32))'   # paste into OMNIVOICE_API_KEY in .env
docker compose up -d --build
docker compose logs -f        # first start downloads models; wait for the health check
```

Open `http://<server-ip>:3900` and enter the API key when asked.

## Everyday commands

```bash
docker compose ps                 # status
docker compose logs -f            # logs
docker compose restart            # restart
docker compose down               # stop (your data is kept)
```

## Updating

Pull your fork's latest code (and upstream changes, see below), then rebuild:

```bash
git pull
docker compose up -d --build
```

To bring in new upstream VoiceStudio releases:

```bash
git remote add upstream https://github.com/debpalash/VoiceStudio.git   # once
git fetch upstream
git merge upstream/main
docker compose up -d --build
```

## No GPU

Delete the `deploy:` block in `docker-compose.yml`. It runs on the CPU, more
slowly.

## Security

- Keep the API key secret. Anyone with it can use the app and change settings.
- `BIND=0.0.0.0` exposes the app to every network this machine is on. Use
  `BIND=127.0.0.1` plus Tailscale or a reverse proxy with HTTPS if the machine
  is reachable from the internet.
- Your data lives in the `voicestudio-data` Docker volume.
  `docker compose down -v` deletes it.

## License

VoiceStudio is AGPL-3.0. Personal use is unrestricted. If you let other people
use a modified version over a network, you must offer them its source code.
