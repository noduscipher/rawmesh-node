# MESH DAILY DRIVER
última actualização: 2026-05

---

## PORTÁTIL

```bash
~/start-meshchatx.sh
```

conteúdo do script:
```bash
#!/bin/bash
ulimit -n 4096
source "$HOME/meshchatx-venv/bin/activate"
meshchatx --headless --host 127.0.0.1 --port 8787 --no-https \
  --reticulum-config-dir "$HOME/.reticulum" \
  --public-dir "$HOME/meshchatx-src/meshchatx/public"
```

Abre browser: http://127.0.0.1:8787

---

## MAC FIXO

```bash
~/start-meshchatx.sh
```

conteúdo do script:
```bash
#!/bin/bash
ulimit -n 4096
source "$HOME/meshchatx-venv/bin/activate"
meshchatx --headless --host 127.0.0.1 --port 8787 --no-https \
  --reticulum-config-dir "$HOME/.reticulum" \
  --public-dir "$HOME/meshchatx-src/meshchatx/public"
```

Abre browser: http://127.0.0.1:8787

---

## RPi4 (verificar estado)

```bash
ssh utilizador@192.168.x.x
rnstatus
sudo systemctl status reticulum nomadnet
```

---

## BACKUP (fazer regularmente)

Portátil:
```bash
~/backup-portatil-all.sh
```

Mac fixo:
```bash
~/backup-mac-all.sh
```

---

## EDITAR SITE

```bash
nano /tmp/index.mu
scp /tmp/index.mu utilizador@192.168.x.x:~/.nomadnetwork/storage/pages/index.mu
```

---

## IDENTIDADES ACTIVAS

| dispositivo | app | lxmf |
|---|---|---|
| Mac fixo | MeshChatX | [lxmf aqui] |
| Portátil | MeshChatX | [lxmf aqui] |
| Android | Sideband | [lxmf aqui] |
| RPi4 | NomadNet | [node id aqui] |

---

## SITE

node id: [node id aqui]
acesso: [node id]:/page/index.mu

---

licensed under cc by-sa 4.0 · https://creativecommons.org/licenses/by-sa/4.0/

*walk quietly, but keep the signal alive.*

₿uilt with Love  🧡  in cooperation with Nature  🌿
