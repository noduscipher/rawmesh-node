> ℹ️ Antes de começar  
> Este guia descreve um perfil **desktop daily driver** ligado em permanência à rede Reticulum (iMac / workstation ou máquina equivalente).  
> O foco está em ter um nó estável para uso diário (trabalho, navegação, terminais) que também participa na malha (Reticulum + NomadNet/LXMF).  
> Não é um guia legal nem de planeamento de rede em larga escala – é um perfil prático para um único nó.  
> Se também utilizas Meshtastic em EU868, usa o repositório `mesh-guides` para presets, roles e notas regulatórias:  
> https://github.com/noduscypher/mesh-guides


# R A W M E S H · O N T H E G O
last updated: 2026-05

---

## TAILSCALE NETWORK

RPi4:     100.x.x.x
Desktop:  100.x.x.x
Laptop:   100.x.x.x

Replace IPs with your Tailscale IPs.
Find them at: https://login.tailscale.com/admin/machines

---

## REMOTE ACCESS (from anywhere)

### Connect to RPi4
```bash
ssh user@100.x.x.x
```

### Connect to desktop
```bash
ssh user@100.x.x.x
```

### Connect to laptop
```bash
ssh user@100.x.x.x
```

---

## UPDATE NOMADNET SITE REMOTELY

### Edit locally
```bash
nano /tmp/index.mu
```

### Send to RPi4
```bash
scp /tmp/index.mu user@100.x.x.x:~/.nomadnetwork/storage/pages/index.mu
```

---

## QUICK START

### Laptop
```bash
#!/bin/bash
ulimit -n 4096
source "$HOME/meshchatx-venv/bin/activate"
meshchatx --headless --host 127.0.0.1 --port 8787 --no-https \
  --reticulum-config-dir "$HOME/.reticulum" \
  --public-dir "$HOME/meshchatx-src/meshchatx/public"
```
Open browser: http://127.0.0.1:8787

### Desktop
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

## NETWORK MAP

RPi4    · NomadNet  · [node id here]
Desktop · MeshChatX · [lxmf here]
Laptop  · MeshChatX · [lxmf here]
Android · Sideband  · [lxmf here]

---

## TROUBLESHOOTING

### MeshChatX won't start
```bash
ulimit -n 4096
source ~/meshchatx-venv/bin/activate
meshchatx --headless --host 127.0.0.1 --port 8787 --no-https \
  --reticulum-config-dir ~/.reticulum \
  --public-dir ~/meshchatx-src/meshchatx/public
```

### RPi4 not responding via Tailscale
```bash
# Check Tailscale app → confirm RPi4 is online
```

### Port in use
```bash
lsof -i :8787 | grep LISTEN
kill -9 [PID]
```

### Restart Reticulum on RPi4
```bash
sudo systemctl restart reticulum
sudo systemctl restart nomadnet
rnstatus
```

---

## USB BACKUPS

BACKUPID-DESKTOP/ ← desktop full backup
BACKUPID-LAPTOP/  ← laptop full backup
RNS/              ← site and guides
FOR-FRIENDS/      ← share with friends

---

licensed under cc by-sa 4.0 · https://creativecommons.org/licenses/by-sa/4.0/

*walk quietly, but keep the signal alive.*

₿uilt with Love  🧡  in cooperation with Nature  🌿
