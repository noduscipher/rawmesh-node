# MESH DAILY DRIVER
last updated: 2026-05

---

## LAPTOP

```bash
~/start-meshchatx.sh
```

script contents:
```bash
#!/bin/bash
ulimit -n 4096
source "$HOME/meshchatx-venv/bin/activate"
meshchatx --headless --host 127.0.0.1 --port 8787 --no-https \
  --reticulum-config-dir "$HOME/.reticulum" \
  --public-dir "$HOME/meshchatx-src/meshchatx/public"
```

Open browser: http://127.0.0.1:8787

---

## DESKTOP

```bash
~/start-meshchatx.sh
```

script contents:
```bash
#!/bin/bash
ulimit -n 4096
source "$HOME/meshchatx-venv/bin/activate"
meshchatx --headless --host 127.0.0.1 --port 8787 --no-https \
  --reticulum-config-dir "$HOME/.reticulum" \
  --public-dir "$HOME/meshchatx-src/meshchatx/public"
```

Open browser: http://127.0.0.1:8787

---

## RPi4 (check status)

```bash
ssh user@192.168.x.x
rnstatus
sudo systemctl status reticulum nomadnet
```

---

## BACKUP (run regularly)

Laptop:
```bash
~/backup-laptop-all.sh
```

Desktop:
```bash
~/backup-desktop-all.sh
```

---

## EDIT SITE

```bash
nano /tmp/index.mu
scp /tmp/index.mu user@192.168.x.x:~/.nomadnetwork/storage/pages/index.mu
```

---

## ACTIVE IDENTITIES

| device | app | lxmf |
|---|---|---|
| Desktop | MeshChatX | [lxmf here] |
| Laptop | MeshChatX | [lxmf here] |
| Android | Sideband | [lxmf here] |
| RPi4 | NomadNet | [node id here] |

---

## SITE

node id: [node id here]
access: [node id]:/page/index.mu

---

licensed under cc by-sa 4.0 · https://creativecommons.org/licenses/by-sa/4.0/

*walk quietly, but keep the signal alive.*

₿uilt with Love  🧡  in cooperation with Nature  🌿
