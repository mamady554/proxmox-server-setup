# proxmox-server-setup

One-command Proxmox provisioner that deploys a full self-hosted media and AI stack from a fresh install.

## What it installs

| Component | Details |
|---|---|
| Plex Media Server | Debian 12 LXC (ID 100), media HDD auto-mounted |
| Ollama + Llama 3.2 | Local AI model, pulled and running on host |
| Open WebUI | Docker container on port 3000, connected to Ollama |
| Tailscale | Installed and enabled for secure remote access |
| Samba | `/mnt/media` shared as `\\SERVER\Media` on LAN |
| AI Media Organizer | Python script + inotify watcher as a systemd service |

## Requirements

- Fresh Proxmox VE install (tested on 8.x / 9.x)
- Run as root on the Proxmox host (not inside a container)
- Internet access for package downloads

## Install

**Option 1 — One-liner (run directly from GitHub):**

```bash
bash <(curl -fsSL https://raw.githubusercontent.com/mamady554/proxmox-server-setup/main/setup.sh)
```

**Option 2 — Clone and run:**

```bash
git clone https://github.com/mamady554/proxmox-server-setup.git
cd proxmox-server-setup
chmod +x setup.sh
bash setup.sh
```

> ⚠️ After install, run `tailscale up` and authenticate to enable remote access.

## After setup

| Service | URL |
|---|---|
| Plex | `http://:32400/web` |
| Open WebUI | `http://:3000` |
| SMB Share (Mac) | `smb:///Media` |
| Proxmox UI | `https://:8006` |

## AI Media Organizer

Files dropped into `/mnt/media/dump` are automatically detected by an inotify watcher, classified by a local Llama 3.2 model, and moved into a Plex-ready folder structure.

```
Movies/Film Title (2023)/Film Title (2023).mkv
TVShows/Show Name/Season 01/S01E04.mkv
Music/Artist/Album/01 - Song Title.flac
```

**Manual run:**

```bash
# Dry run — preview what would move
python3 /root/media_organizer.py --source /mnt/media/dump

# Apply moves
python3 /root/media_organizer.py --source /mnt/media/dump --live

# Watch the log
tail -f /var/log/media_organizer.log
```

## Stack

`Bash` `Python 3` `Proxmox VE` `Docker` `Ollama` `Llama 3.2` `Tailscale` `Samba` `systemd` `inotify-tools`

## License

MIT
