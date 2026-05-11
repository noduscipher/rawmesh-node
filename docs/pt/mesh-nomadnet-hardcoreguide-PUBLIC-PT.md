# 🌐 Mesh & NomadNet Hardcore Guide (Public Edition)

**Stack:** Reticulum · NomadNet · MeshChatX · rBrowser · LXMF
**Setup base:** iMac + MacBook Air + Raspberry Pi 4 como node público
**Target audience:** Operadores mesh que querem um daily driver funcional + survival kit para quando tudo parte.

> ☥ walk quietly, but keep the signal alive. ₿uilt with Love in cooperation with Nature.

---

## 📑 Índice

1. [Arquitetura geral](#1-arquitetura-geral)
2. [Arranque diário](#2-arranque-diário)
3. [Páginas, navegação & hashes](#3-páginas-navegação--hashes)
4. [NomadNet & Micron (RPi4)](#4-nomadnet--micron-rpi4)
5. [Git & docs workflow](#5-git--docs-workflow)
6. [Troubleshooting por sintomas](#6-troubleshooting-por-sintomas)
7. [Disaster recovery total](#7-disaster-recovery-total)
8. [Cheat sheet — comandos rápidos](#8-cheat-sheet--comandos-rápidos)
9. [Path map completo](#9-path-map-completo)
10. [Identidades & LXMF](#10-identidades--lxmf)
11. [Checklists finais](#11-checklists-finais)

> ℹ️ **Placeholders neste guia:**
> `<USER_HOME>` → home do utilizador (`~`)
> `<IMAC_HOST>` / `<AIR_HOST>` → hostnames das máquinas
> `<NODE_HASH>` → hash Reticulum do node público (32 hex chars)
> `<NODE_IP>` → IP local do RPi4 na LAN
> `<NODE_USER>` → utilizador SSH no RPi4 (ex: `storage`, `pi`)
> `<LXMF_ID_*>` → addresses LXMF de cada cliente
> Substitui pelos teus valores reais. Mantém os teus dados longe de repos públicos.

---

## 1. Arquitetura geral

### 1.1. Máquinas

| Máquina | Hostname | Papel |
|---|---|---|
| **iMac** | `<IMAC_HOST>` | Cliente principal: `rnsd` + MeshChatX headless + rBrowser |
| **MacBook Air** | `<AIR_HOST>` | Cliente portátil: mesmo padrão (rnsd + MeshChatX + rBrowser) |
| **RPi4** | `<NODE_USER>@<NODE_IP>` | Node público: Reticulum + NomadNet daemon, serve páginas Micron |

### 1.2. Portas locais (Mac)

- **MeshChatX backend:** `127.0.0.1:8787` (headless — UI é externa em `https://meshchatx.com/index.html`)
- **rBrowser:** `127.0.0.1:5500`

> ⚠️ **Importante:** MeshChatX **NÃO** serve UI HTTP em `http://127.0.0.1:8787`. Corre headless. A interface é o cliente web externo em `https://meshchatx.com/index.html`. Tutoriais antigos que dizem para abrir `http://127.0.0.1:8787` no browser estão **desatualizados**.

### 1.3. Componentes no RPi4

- Reticulum (`rnsd` ou via daemon NomadNet)
- NomadNet daemon (servido via `systemctl` → `sudo systemctl restart nomadnet`)
- Páginas Micron em `~/.nomadnetwork/storage/pages/`
- Node LXMF público com hash `<NODE_HASH>`

---

## 2. Arranque diário

### 2.1. iMac — startup completo

```bash
# Opção 1: tudo de uma vez
~/start-mesh-driver.sh

# Opção 2: passo a passo
~/start-mesh-driver.sh       # rnsd + MeshChatX + rBrowser
open https://meshchatx.com/index.html
open http://127.0.0.1:5500   # rBrowser UI
```

### 2.2. MacBook Air — startup completo

```bash
~/start-mesh-driver.sh       # versão Air, lança start-rbrowser-air.sh
open https://meshchatx.com/index.html
```

### 2.3. NomadNet TextUI (opcional, em qualquer Mac)

```bash
source ~/nomadnet-venv/bin/activate
nomadnet --textui
# Para navegar: tecla `G` (Go to) → cola hash
```

### 2.4. Script `~/start-mesh-driver.sh` (iMac)

```bash
#!/bin/zsh
# Mesh Daily Driver - iMac
# Reticulum + MeshChatX + rBrowser

echo "================================================================================"
echo "🚀 Mesh Daily Driver - iMac"
echo "================================================================================"

echo "🧱 Starting Reticulum (rnsd)..."
if ! pgrep -f "rnsd" >/dev/null 2>&1; then
  rnsd &
  RNSD_PID=$!
  echo "   ↳ rnsd started, PID: $RNSD_PID"
else
  echo "   ↳ rnsd already running."
fi

sleep 2

echo "💬 Starting MeshChatX..."
if ! lsof -ti :8787 >/dev/null 2>&1; then
  ~/start-meshchatx.sh &
  echo "   ↳ MeshChatX (wrapper) PID: $!"
else
  echo "   ↳ MeshChatX already bound to port 8787."
fi

sleep 2

echo "🌐 Starting rBrowser..."
if ! lsof -ti :5500 >/dev/null 2>&1; then
  ~/start-rbrowser.sh &
  echo "   ↳ rBrowser (wrapper) PID: $!"
else
  echo "   ↳ rBrowser already bound to port 5500."
fi

echo ""
echo "✅ Mesh daily driver is up on this iMac."
echo "   MeshChatX: https://meshchatx.com/index.html  (UI externa)"
echo "   rBrowser : http://127.0.0.1:5500"
echo "================================================================================"
```

### 2.5. Script `~/start-mesh-driver.sh` (MacBook Air)

```bash
#!/bin/zsh
# Mesh Daily Driver - MacBook Air

echo "================================================================================"
echo "🚀 Mesh Daily Driver - MacBook Air"
echo "================================================================================"

echo "🧱 Starting Reticulum (rnsd)..."
if ! pgrep -f "rnsd" >/dev/null 2>&1; then
  rnsd &
  echo "   ↳ rnsd started, PID: $!"
else
  echo "   ↳ rnsd already running."
fi

sleep 2

echo "💬 Starting MeshChatX..."
if ! lsof -ti :8787 >/dev/null 2>&1; then
  ~/start-meshchatx.sh &
  echo "   ↳ MeshChatX (wrapper) PID: $!"
else
  echo "   ↳ MeshChatX already bound to port 8787."
fi

sleep 2

echo "🌐 Starting rBrowser (Air)..."
if ! lsof -ti :5500 >/dev/null 2>&1; then
  ~/start-rbrowser-air.sh &
  echo "   ↳ rBrowser (wrapper) PID: $!"
else
  echo "   ↳ rBrowser already bound to port 5500."
fi

echo ""
echo "✅ Mesh daily driver is up on this MacBook Air."
echo "================================================================================"
```

Após qualquer edição:
```bash
chmod +x ~/start-mesh-driver.sh
```

---

## 3. Páginas, navegação & hashes

### 3.1. Hash do node público (RPi4)

```
<NODE_HASH>
```

Define um nome humano-legível para o node (ex: `MY-MESH-NODE`) na config do NomadNet.

### 3.2. Páginas típicas

| Página | Link Reticulum |
|---|---|
| INDEX (principal) | `<NODE_HASH>:/page/index.mu` |
| PRINTLAB | `<NODE_HASH>:/page/printlab.mu` |
| ABOUT | `<NODE_HASH>:/page/about.mu` |
| FIELD-NOTES | `<NODE_HASH>:/page/field-notes.mu` |

### 3.3. Como aceder

**Via MeshChatX** (`https://meshchatx.com/index.html`):
1. Clica em **NomadNet** → **Browse**
2. Cola o hash + path (ex: `<NODE_HASH>:/page/index.mu`)
3. Enter

**Via rBrowser** (`http://127.0.0.1:5500`):
1. Cola o link Reticulum no address bar
2. Enter

**Via NomadNet TUI:**
1. Prime `G` (Go to)
2. Cola o hash/link
3. Enter

### 3.4. Formato de links Micron/NomadNet

```
<hash_reticulum>:/page/<nome_pagina>.mu
```

Exemplo dentro de um `.mu`:
```
`<<NODE_HASH>:/page/index.mu`>← Back to INDEX`<`>
```

---

## 4. NomadNet & Micron (RPi4)

### 4.1. Paths principais no RPi4

```
~/.nomadnetwork/storage/pages/     # Páginas públicas (.mu)
~/.nomadnetwork/storage/identity   # Identidade do node
~/.reticulum/                      # Config Reticulum
~/.config/nomadnetwork/            # Config NomadNet
~/nomadnet-venv/                   # Virtualenv NomadNet
~/index.mu                         # Espelho de edição da homepage
```

Ficheiros típicos em `~/.nomadnetwork/storage/pages/`:
```
index.mu
about.mu
printlab.mu
field-notes.mu
```

### 4.2. Editar páginas — três opções

#### Opção 1 — SSH direto ao RPi4

```bash
ssh <NODE_USER>@<NODE_IP>
nano ~/.nomadnetwork/storage/pages/index.mu
# Guardar: Ctrl+O → Enter → Ctrl+X
sudo systemctl restart nomadnet
```

#### Opção 2 — Espelho local no RPi4

```bash
ssh <NODE_USER>@<NODE_IP>
nano ~/index.mu
cp ~/index.mu ~/.nomadnetwork/storage/pages/index.mu
sudo systemctl restart nomadnet
```

#### Opção 3 — Editar no Mac e sincronizar via `scp`

```bash
# No Mac
nano ~/.nomadnetwork/storage/pages/index.mu
# ou: code ~/.nomadnetwork/storage/pages/index.mu

# Sincronizar
scp ~/.nomadnetwork/storage/pages/index.mu \
    <NODE_USER>@<NODE_IP>:~/.nomadnetwork/storage/pages/index.mu

# Restart no Pi
ssh <NODE_USER>@<NODE_IP> "sudo systemctl restart nomadnet"
```

#### Opção 4 — Via MeshChatX web

1. `https://meshchatx.com/index.html`
2. **Tools** → **Mesh Server** → **Micron Pages**
3. Edita `index.mu` na interface
4. Guarda → aplicado automaticamente

### 4.3. Workflow rápido para grandes alterações

```bash
# Backup antes de editar
ssh <NODE_USER>@<NODE_IP> \
  "cp ~/.nomadnetwork/storage/pages/index.mu \
      ~/.nomadnetwork/storage/pages/index.mu.backup-$(date +%Y%m%d-%H%M%S)"

# Ver conteúdo atual
ssh <NODE_USER>@<NODE_IP> "cat ~/.nomadnetwork/storage/pages/index.mu"

# Listar páginas
ssh <NODE_USER>@<NODE_IP> "ls -lah ~/.nomadnetwork/storage/pages/"
```

### 4.4. Micron markup — quick reference

```
`!c          Texto centrado
`!           Texto com highlight/cor
`B           Bold ON
`b           Bold OFF
`F<hex>      Foreground color (ex: `Ff80)
`=           Horizontal rule
`<url`>      Início de link
`<`>         Fim de link
```

Exemplo de link interno:
```
`<<NODE_HASH>:/page/index.mu`>INDEX`<`>
```

### 4.5. Boas práticas Micron

- Usa `#` / `##` para headings, `---` para separadores
- Evita `>` e grandes blocos ASCII que criam "white back"
- Mantém linhas relativamente curtas
- Para conteúdo minimal/raw, evita plugins, temas ou assets externos

### 4.6. Criar página `.mu` nova — template

```bash
ssh <NODE_USER>@<NODE_IP> "cat > ~/.nomadnetwork/storage/pages/nova-pagina.mu << 'ENDOFFILE'
\`!c
┌─────────────────────────────────────────┐
│          TÍTULO DA PÁGINA               │
└─────────────────────────────────────────┘

\`c══════════════════════════════════════════════════════════════════════

\`!c                    SECÇÃO CENTRADA

\`!
Texto normal alinhado à esquerda.

\`B Texto Bold \`b

\`<<NODE_HASH>:/page/index.mu\`>← Back to INDEX\`<\`>

\`c══════════════════════════════════════════════════════════════════════
ENDOFFILE"

sudo systemctl restart nomadnet
```

Link da nova página:
```
<NODE_HASH>:/page/nova-pagina.mu
```

### 4.7. Editar `~/index.mu` (espelho local) + field notes

```bash
# Arrancar ambiente NomadNet
cd ~
source nomadnet-venv/bin/activate
nomadnet

# Editar index principal
nano ~/index.mu

# Editar field notes em Markdown
cd ~/mesh-guides
nano FIELD-NOTES.md

# Editar versão Micron das field notes
cd ~/.nomadnetwork/storage/pages
nano field-notes.mu

# Sincronizar index para a cápsula
cp ~/index.mu ~/.nomadnetwork/storage/pages/index.mu
```

---

## 5. Git & docs workflow

### 5.1. Repositório

```
GitHub: <USER>/mesh-guides
Local : <USER_HOME>/mesh-guides
```

### 5.2. Estrutura `docs/`

```
docs/
├── pt/
│   ├── technical/
│   └── user/
├── en/
│   ├── technical/
│   └── user/
└── es/
    ├── technical/
    └── user/
```

Criar de uma vez:
```bash
mkdir -p docs/pt/technical docs/pt/user
mkdir -p docs/en/technical docs/en/user
mkdir -p docs/es/technical docs/es/user
```

### 5.3. Fluxo seguro — checklist

```bash
cd ~/mesh-guides
git status

# 1. Limpar .DS_Store
rm -f .DS_Store docs/.DS_Store docs/pt/.DS_Store docs/en/.DS_Store docs/es/.DS_Store

# 2. Adicionar ficheiros específicos (NUNCA `git add .` às cegas)
git add docs/pt/technical/ficheiro.md
git status

# 3. Commit
git commit -m "Mensagem clara"

# 4. Sync com remote
git pull --rebase origin main

# 5. Push
git push origin main
```

### 5.4. Copiar ficheiros desde `~/Downloads`

Se a estrutura `pt/`, `en/`, `es/` já está em `~/Downloads`:

```bash
cp -R ~/Downloads/pt docs/
cp -R ~/Downloads/en docs/
cp -R ~/Downloads/es docs/
```

Caso a caso:
```bash
cp ~/Downloads/pt/technical/meshtastic-technical-guide.md docs/pt/technical/
cp ~/Downloads/pt/user/meshtastic-beginner-guide.md docs/pt/user/
cp ~/Downloads/en/technical/meshtastic-technical-guide.md docs/en/technical/
cp ~/Downloads/en/user/meshtastic-beginner-guide.md docs/en/user/
cp ~/Downloads/es/technical/meshtastic-technical-guide.md docs/es/technical/
cp ~/Downloads/es/user/meshtastic-beginner-guide.md docs/es/user/
```

Verificar:
```bash
find docs -maxdepth 4 -type f
```

### 5.5. Recuperar ficheiros apagados por engano

Se `git status` mostrar `deleted:`:

```bash
git restore caminho/do/ficheiro.md

# Exemplos
git restore FIELD-NOTES.md
git restore docs/pt/technical/handshake-protocol.md
```

### 5.6. Remover `.DS_Store` já adicionado

```bash
git restore --staged .DS_Store 2>/dev/null
git restore --staged docs/.DS_Store 2>/dev/null
git restore --staged docs/pt/.DS_Store 2>/dev/null
git restore --staged docs/en/.DS_Store 2>/dev/null
git restore --staged docs/es/.DS_Store 2>/dev/null
```

Dica: adiciona ao `.gitignore`:
```
.DS_Store
**/.DS_Store
```

### 5.7. Resolver conflito `add/add` durante rebase

```
CONFLICT (add/add): Merge conflict in docs/pt/technical/meshtastic-technical-guide.md
```

Durante rebase: `--ours` = GitHub, `--theirs` = local.

Para manter a tua versão local:
```bash
git checkout --theirs docs/pt/technical/meshtastic-technical-guide.md
git checkout --theirs docs/pt/user/meshtastic-beginner-guide.md
git add docs/pt/technical/meshtastic-technical-guide.md
git add docs/pt/user/meshtastic-beginner-guide.md
git rebase --continue
```

Se abrir o `vim`:
```
ESC  :wq  ENTER
```

### 5.8. Corrigir URL do remote (se o repo mudou de nome)

```bash
git remote set-url origin https://github.com/<USER>/mesh-guides.git
git remote -v
```

Deve aparecer:
```
origin  https://github.com/<USER>/mesh-guides.git (fetch)
origin  https://github.com/<USER>/mesh-guides.git (push)
```

### 5.9. Limpeza pós-push

```bash
rm -rf FIELD-NOTES.md.backup
rm -rf meshtastic-guides-final-v1.zip
rm -rf meshtastic-guides-final-v1/
```

### 5.10. Fluxo seguro condensado

```bash
cd ~/mesh-guides
git status
rm -f .DS_Store docs/.DS_Store docs/pt/.DS_Store docs/en/.DS_Store docs/es/.DS_Store
git add caminho/do/ficheiro.md
git status
git commit -m "Mensagem clara do commit"
git pull --rebase origin main
git push origin main
```

---

## 6. Troubleshooting por sintomas

### 6.1. "Link establishment timed out" / página não carrega

**Causa:** Announce do node RPi4 expirou ou o Reticulum no Mac não conhece o hash.

**Solução — forçar announce no RPi4:**

```bash
ssh <NODE_USER>@<NODE_IP>

python3 << 'EOF'
import RNS, RNS.vendor.umsgpack as msgpack, time
time.sleep(1)
reticulum = RNS.Reticulum()
identity = RNS.Identity.from_file("/home/<NODE_USER>/.nomadnetwork/storage/identity")
node_dest = RNS.Destination(identity, RNS.Destination.IN, RNS.Destination.SINGLE, "nomadnetwork", "node")
node_dest.announce(app_data=msgpack.packb({"v": 1}))
print(f"✅ Forced announce: {RNS.prettyhexrep(node_dest.hash)}")
EOF
```

Aguarda 10-20 segundos e tenta novamente.

### 6.2. MeshChatX não responde / erro de backend

```bash
# Diagnóstico
ps aux | grep meshchatx | grep -v grep
lsof -i :8787

# Matar e libertar porta
lsof -ti :8787 | xargs kill -9

# Relançar
~/start-mesh-driver.sh
```

Se persistir, ver log do wrapper em `/tmp/meshchatx-*.log` e confirmar flag `--reticulum-config-dir ~/.reticulum`.

### 6.3. MeshChatX "não abre" no browser

**Não tentes `http://127.0.0.1:8787` no browser** — não funciona.

```bash
~/start-meshchatx.sh
open https://meshchatx.com/index.html
```

❌ ERRADO: `http://127.0.0.1:8787`
✅ CORRETO: `https://meshchatx.com/index.html`

### 6.4. Vários `rnsd` ou comportamento estranho

```bash
ps aux | grep rnsd | grep -v grep
pkill -f rnsd
~/start-mesh-driver.sh
```

### 6.5. NomadNet no Pi não está a servir

```bash
ssh <NODE_USER>@<NODE_IP> "ps aux | grep nomadnet | grep -v grep"

# Se não houver processo:
ssh <NODE_USER>@<NODE_IP>
source ~/nomadnet-venv/bin/activate
nomadnet &
exit

# Ou via systemd:
ssh <NODE_USER>@<NODE_IP> "sudo systemctl status nomadnet"
ssh <NODE_USER>@<NODE_IP> "sudo systemctl restart nomadnet"
ssh <NODE_USER>@<NODE_IP> "journalctl -u nomadnet.service -n 50"

# Confirmar páginas
ssh <NODE_USER>@<NODE_IP> "ls ~/.nomadnetwork/storage/pages"
ssh <NODE_USER>@<NODE_IP> "head -40 ~/.nomadnetwork/storage/pages/index.mu"
```

### 6.6. rBrowser não carrega

```bash
lsof -i :5500
# Se nada → relançar
~/start-rbrowser.sh           # iMac
~/start-rbrowser-air.sh       # MacBook Air
# Aguarda 5s
open http://127.0.0.1:5500
```

### 6.7. Página vazia ou erro 404

```bash
ssh <NODE_USER>@<NODE_IP>
ls -lh ~/.nomadnetwork/storage/pages/index.mu
cat ~/.nomadnetwork/storage/pages/index.mu
```

Se vazio, criar básico:
```bash
cat > ~/.nomadnetwork/storage/pages/index.mu << 'EOF'
`!c
Ff80
█▓▒░ M E S H · N O D E ░▒▓█
f

Off-Grid Test

[ walk quietly, but keep the signal alive ]

₿uilt with Love in cooperation with Nature.
EOF

sudo systemctl restart nomadnet
```

### 6.8. Erro `websockets>=16.0` no Python 3.9

→ Tens de usar **Python 3.11**, ver secção 7 (Disaster recovery).

### 6.9. Erro "Too many files"

Adicionar ao `~/.zshrc`:
```bash
ulimit -n 10240
```

E recarregar:
```bash
source ~/.zshrc
```

### 6.10. USB NTFS — não escrever via terminal

USB NTFS no macOS: usa **Finder** (escreve via tradução), **não** terminal.

### 6.11. Identity vs ratchets

> **ratchets ≠ identity** — o ficheiro `identity` é o crítico.

Localizações típicas:
- Mac (MeshChatX real): `~/storage/identity`
- Mac (MeshChat clássico): `~/reticulum-meshchat/storage/identity`
- RPi4 (NomadNet): `~/.nomadnetwork/storage/identity`

Sem o `identity` correto, perdes o hash do node — perdes a identidade pública.

---

## 7. Disaster recovery total

### 7.1. Stack USB de backup

Estrutura recomendada no USB (`/Volumes/<USB_NAME>/BACKUPID/`):
```
iMac-meshchat-real/
iMac-reticulum/
Air-meshchat/
*-nomadnet/pages/
```

### 7.2. Recuperar MacBook Air — venv MeshChat

```bash
cd ~/reticulum-meshchat
rm -rf venv_novo
python3.11 -m venv venv_novo
source venv_novo/bin/activate
pip install rns lxmf peewee aiohttp websockets

~/start-meshchat.sh
```

> ⚠️ Usa Python **3.11**, não 3.9. `websockets>=16.0` requer-o.

### 7.3. Recuperar iMac — MeshChat real + Reticulum

```bash
# Restaura MeshChat real
cp -r /Volumes/<USB_NAME>/BACKUPID/iMac-meshchat-real/* ~/storage/

# Restaura Reticulum global
cp -r /Volumes/<USB_NAME>/BACKUPID/iMac-reticulum/* ~/.reticulum/storage/

~/start-meshchatx.sh
```

### 7.4. Recuperar NomadNet (ambos os Mac + RPi4)

```bash
cp -r /Volumes/<USB_NAME>/BACKUPID/*-nomadnet/pages ~/.nomadnetwork/storage/
```

### 7.5. Recriar venv NomadNet do zero (qualquer máquina)

```bash
cd ~
python3.11 -m venv nomadnet-venv
source nomadnet-venv/bin/activate
pip install nomadnet rns lxmf
```

### 7.6. Backup completo (Mac)

```bash
~/backup-imac-all.sh   # ou equivalente que cobre ~/.reticulum, ~/.nomadnetwork, ~/storage, venvs
```

### 7.7. Limites — adicionar ao `~/.zshrc`

```bash
echo 'ulimit -n 10240' >> ~/.zshrc
source ~/.zshrc
```

### 7.8. Restore — checklist

- [ ] Python 3.11 instalado (`brew install python@3.11`)
- [ ] venvs recriados nos sítios certos
- [ ] `identity` restaurado em cada storage
- [ ] Páginas Micron restauradas no RPi4
- [ ] `~/.reticulum/` restaurado
- [ ] `ulimit -n 10240` aplicado
- [ ] Scripts `~/start-*.sh` com `chmod +x`
- [ ] `~/start-mesh-driver.sh` arranca tudo OK
- [ ] Force announce do node público feito (ver 6.1)
- [ ] Acesso ao hash `<NODE_HASH>` confirmado

---

## 8. Cheat sheet — comandos rápidos

### 8.1. Startup & shutdown

```bash
# === STARTUP === #
~/start-mesh-driver.sh                          # Tudo num só (Mac)
~/start-meshchatx.sh                            # Só MeshChatX headless
~/start-rbrowser.sh                             # Só rBrowser (iMac)
~/start-rbrowser-air.sh                         # Só rBrowser (Air)
open https://meshchatx.com/index.html           # UI MeshChatX
open http://127.0.0.1:5500                      # UI rBrowser

# === NomadNet TUI === #
source ~/nomadnet-venv/bin/activate
nomadnet --textui

# === SHUTDOWN limpo === #
pkill -f rnsd
pkill -f meshchatx
lsof -ti :8787 | xargs kill -9 2>/dev/null
lsof -ti :5500 | xargs kill -9 2>/dev/null
```

### 8.2. SSH ao RPi4

```bash
ssh <NODE_USER>@<NODE_IP>
```

### 8.3. Editar página + restart

```bash
ssh <NODE_USER>@<NODE_IP>
nano ~/.nomadnetwork/storage/pages/index.mu
sudo systemctl restart nomadnet
```

### 8.4. Force announce (RPi4)

```bash
ssh <NODE_USER>@<NODE_IP>
python3 << 'EOF'
import RNS, RNS.vendor.umsgpack as msgpack, time
time.sleep(1)
reticulum = RNS.Reticulum()
identity = RNS.Identity.from_file("/home/<NODE_USER>/.nomadnetwork/storage/identity")
node_dest = RNS.Destination(identity, RNS.Destination.IN, RNS.Destination.SINGLE, "nomadnetwork", "node")
node_dest.announce(app_data=msgpack.packb({"v": 1}))
print(f"✅ {RNS.prettyhexrep(node_dest.hash)}")
EOF
```

### 8.5. Verificar estado

```bash
rnstatus                                                   # Estado Reticulum no Mac
ps aux | grep rnsd | grep -v grep
ps aux | grep meshchatx | grep -v grep
lsof -i :8787
lsof -i :5500
ssh <NODE_USER>@<NODE_IP> "sudo systemctl status nomadnet"
ssh <NODE_USER>@<NODE_IP> "ps aux | grep nomadnet | grep -v grep"
```

### 8.6. Backup rápido página

```bash
ssh <NODE_USER>@<NODE_IP> \
  "cp ~/.nomadnetwork/storage/pages/index.mu \
      ~/.nomadnetwork/storage/pages/index.mu.backup-$(date +%Y%m%d-%H%M%S)"
```

### 8.7. Golden links template — copy-paste

```
INDEX:        <NODE_HASH>:/page/index.mu
PRINTLAB:     <NODE_HASH>:/page/printlab.mu
ABOUT:        <NODE_HASH>:/page/about.mu
FIELD-NOTES:  <NODE_HASH>:/page/field-notes.mu

MESHCHATX UI: https://meshchatx.com/index.html
rBROWSER UI:  http://127.0.0.1:5500
LXMF ADDR:    <NODE_HASH>
```

---

## 9. Path map completo

### 9.1. iMac (`<IMAC_HOST>`)

```
~/.reticulum/                              # Config Reticulum global
~/storage/                                 # MeshChatX real storage (com identity)
~/meshchatx-py314/                         # venv MeshChatX (Python 3.14)
~/rbrowser/                                # Código rBrowser
~/nomadnet-venv/                           # venv NomadNet
~/.nomadnetwork/                           # Config NomadNet local (TUI)
~/start-mesh-driver.sh                     # Launcher unificado
~/start-meshchatx.sh                       # Launcher MeshChatX
~/start-rbrowser.sh                        # Launcher rBrowser
~/backup-imac-all.sh                       # Backup tool
~/mesh-guides/                             # Repo git (docs)
```

### 9.2. MacBook Air (`<AIR_HOST>`)

```
~/.reticulum/
~/reticulum-meshchat/                      # MeshChat (com venv_novo)
~/reticulum-meshchat/storage/identity      # ⚠️ identity crítica
~/meshchatx-venv/                          # venv MeshChatX
~/nomadnet-venv/
~/start-mesh-driver.sh
~/start-meshchatx.sh
~/start-rbrowser-air.sh
~/start-meshchat.sh
```

### 9.3. RPi4 (`<NODE_USER>@<NODE_IP>`)

```
~/.nomadnetwork/storage/pages/             # Páginas Micron públicas
~/.nomadnetwork/storage/identity           # ⚠️ identity do node público
~/.reticulum/                              # Config Reticulum
~/.config/nomadnetwork/
~/nomadnet-venv/                           # venv NomadNet
~/index.mu                                 # Espelho local da homepage
```

### 9.4. mesh-guides repo

```
~/mesh-guides/
├── FIELD-NOTES.md
└── docs/
    ├── pt/{technical,user}/
    ├── en/{technical,user}/
    └── es/{technical,user}/
```

GitHub remote: `https://github.com/<USER>/mesh-guides.git`

---

## 10. Identidades & LXMF

| Máquina | Cliente | LXMF Address | Uso |
|---|---|---|---|
| iMac | MeshChatX | `<LXMF_ID_IMAC>` | Cliente principal |
| iMac | NomadNet textui | `<LXMF_ID_IMAC_TUI>` | TUI local |
| RPi4 | NomadNet daemon | `<NODE_HASH>` | **Node público** |
| MacBook Air | MeshChat | `<LXMF_ID_AIR>` | Portátil |

**Enviar mensagem LXMF via MeshChatX:**

1. Abre `https://meshchatx.com/index.html`
2. **New Message**
3. Destino: `<NODE_HASH>` (ou outro)
4. Escreve & Send (encriptada end-to-end)

---

## 11. Checklists finais

### 11.1. Antes de cada `git push`

- [ ] `git status` — sem `.DS_Store`
- [ ] `git status` — sem `deleted:` indesejados
- [ ] Só ficheiros certos em "Changes to be committed"
- [ ] Remote aponta para o repo correto
- [ ] `git pull --rebase origin main` antes do push

### 11.2. Antes de sair de casa (on-the-go)

- [ ] MeshChatX a correr no Mac (porta 8787 ocupada)
- [ ] NomadNet a correr no RPi4 (`systemctl status nomadnet` ativo)
- [ ] Páginas `.mu` atualizadas
- [ ] Backup feito se editaste algo crítico
- [ ] Force announce feito (se mexeste na rede)
- [ ] Links testados em MeshChatX/rBrowser
- [ ] SSH ao RPi4 testado a partir de redes externas (se aplicável)

### 11.3. Antes de edições grandes ao node

```bash
# No Mac
ps aux | grep -E "rnsd|meshchatx" | grep -v grep

# No Pi
ssh <NODE_USER>@<NODE_IP> "ps aux | grep nomadnet | grep -v grep"

# Backup página antes
ssh <NODE_USER>@<NODE_IP> \
  "cp ~/.nomadnetwork/storage/pages/index.mu \
      ~/.nomadnetwork/storage/pages/index.mu.backup-$(date +%Y%m%d-%H%M%S)"
```

### 11.4. Recuperação rápida (qualquer pânico)

1. Matar `rnsd` e MeshChatX se estiverem estranhos: `pkill -f rnsd && pkill -f meshchatx`
2. `~/start-mesh-driver.sh`
3. Garantir NomadNet no Pi: `ssh <NODE_USER>@<NODE_IP> "sudo systemctl restart nomadnet"`
4. Force announce (secção 6.1 ou 8.4)
5. Verificar páginas Micron e links NomadNet

---

## 📌 Notas finais

Este guia é uma destilação de prática real — daily driver + survival kit — para quem corre Reticulum/NomadNet/MeshChatX num setup misto Mac + RPi4. Adapta os placeholders ao teu setup e **mantém os teus hashes, IPs e identities longe de repos públicos**.

Contribuições, correções e forks são bem-vindos. Se descobrires um workflow melhor para algum dos sintomas listados, abre PR ou partilha via LXMF — o mesh cresce quando partilhamos receitas que funcionam no terreno.

---

**☥ walk quietly, but keep the signal alive ⚡**

*Built with Love in cooperation with Nature.*

*cypherpunks escrevem código /// a natureza escreve resiliência /// nós fazemos a ponte*
*cypherpunks write code /// nature writes resilience /// we bridge both*

---

## 📜 Licença sugerida

Conteúdo deste guia disponibilizado sob **Creative Commons Attribution-ShareAlike 4.0 International (CC BY-SA 4.0)**:
https://creativecommons.org/licenses/by-sa/4.0/

Snippets de código (shell scripts, Python) podem ser usados sob **MIT** ou equivalente permissivo, à escolha do utilizador.
