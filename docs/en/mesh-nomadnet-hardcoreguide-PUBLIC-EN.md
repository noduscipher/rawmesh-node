# 🌐 Mesh & NomadNet Hardcore Guide (Public Edition)

**Stack:** Reticulum · NomadNet · MeshChatX · rBrowser · LXMF
**Base setup:** iMac + MacBook Air + Raspberry Pi 4 as public node
**Target audience:** Mesh operators who want a working daily driver + survival kit for when everything breaks.

> ☥ walk quietly, but keep the signal alive. ₿uilt with Love in cooperation with Nature.

---

## 📑 Table of Contents

1. [General architecture](#1-general-architecture)
2. [Daily startup](#2-daily-startup)
3. [Pages, navigation & hashes](#3-pages-navigation--hashes)
4. [NomadNet & Micron (RPi4)](#4-nomadnet--micron-rpi4)
5. [Git & docs workflow](#5-git--docs-workflow)
6. [Troubleshooting by symptom](#6-troubleshooting-by-symptom)
7. [Full disaster recovery](#7-full-disaster-recovery)
8. [Cheat sheet — quick commands](#8-cheat-sheet--quick-commands)
9. [Full path map](#9-full-path-map)
10. [Identities & LXMF](#10-identities--lxmf)
11. [Final checklists](#11-final-checklists)

> ℹ️ **Placeholders used in this guide:**
> `<USER_HOME>` → user's home directory (`~`)
> `<IMAC_HOST>` / `<AIR_HOST>` → machine hostnames
> `<NODE_HASH>` → Reticulum hash of the public node (32 hex chars)
> `<NODE_IP>` → RPi4 local LAN IP
> `<NODE_USER>` → SSH user on the RPi4 (e.g. `storage`, `pi`)
> `<LXMF_ID_*>` → LXMF addresses for each client
> Replace with your own real values. Keep your data out of public repos.

---

## 1. General architecture

### 1.1. Machines

| Machine | Hostname | Role |
|---|---|---|
| **iMac** | `<IMAC_HOST>` | Main client: `rnsd` + MeshChatX headless + rBrowser |
| **MacBook Air** | `<AIR_HOST>` | Portable client: same pattern (rnsd + MeshChatX + rBrowser) |
| **RPi4** | `<NODE_USER>@<NODE_IP>` | Public node: Reticulum + NomadNet daemon, serves Micron pages |

### 1.2. Local ports (Mac)

- **MeshChatX backend:** `127.0.0.1:8787` (headless — UI is external at `https://meshchatx.com/index.html`)
- **rBrowser:** `127.0.0.1:5500`

> ⚠️ **Important:** MeshChatX does **NOT** serve an HTTP UI at `http://127.0.0.1:8787`. It runs headless. The actual interface is the external web client at `https://meshchatx.com/index.html`. Old tutorials that tell you to open `http://127.0.0.1:8787` in a browser are **outdated**.

### 1.3. Components on the RPi4

- Reticulum (`rnsd`, or via the NomadNet daemon)
- NomadNet daemon (managed by `systemctl` → `sudo systemctl restart nomadnet`)
- Micron pages in `~/.nomadnetwork/storage/pages/`
- Public LXMF node with hash `<NODE_HASH>`

---

## 2. Daily startup

### 2.1. iMac — full startup

```bash
# Option 1: everything at once
~/start-mesh-driver.sh

# Option 2: step by step
~/start-mesh-driver.sh       # rnsd + MeshChatX + rBrowser
open https://meshchatx.com/index.html
open http://127.0.0.1:5500   # rBrowser UI
```

### 2.2. MacBook Air — full startup

```bash
~/start-mesh-driver.sh       # Air version, launches start-rbrowser-air.sh
open https://meshchatx.com/index.html
```

### 2.3. NomadNet TextUI (optional, on any Mac)

```bash
source ~/nomadnet-venv/bin/activate
nomadnet --textui
# To navigate: press `G` (Go to) → paste hash
```

### 2.4. `~/start-mesh-driver.sh` script (iMac)

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
echo "   MeshChatX: https://meshchatx.com/index.html  (external UI)"
echo "   rBrowser : http://127.0.0.1:5500"
echo "================================================================================"
```

### 2.5. `~/start-mesh-driver.sh` script (MacBook Air)

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

After any edit:
```bash
chmod +x ~/start-mesh-driver.sh
```

---

## 3. Pages, navigation & hashes

### 3.1. Public node hash (RPi4)

```
<NODE_HASH>
```

Set a human-readable name for the node (e.g. `MY-MESH-NODE`) in the NomadNet config.

### 3.2. Typical pages

| Page | Reticulum link |
|---|---|
| INDEX (main) | `<NODE_HASH>:/page/index.mu` |
| PRINTLAB | `<NODE_HASH>:/page/printlab.mu` |
| ABOUT | `<NODE_HASH>:/page/about.mu` |
| FIELD-NOTES | `<NODE_HASH>:/page/field-notes.mu` |

### 3.3. How to access

**Via MeshChatX** (`https://meshchatx.com/index.html`):
1. Click **NomadNet** → **Browse**
2. Paste hash + path (e.g. `<NODE_HASH>:/page/index.mu`)
3. Enter

**Via rBrowser** (`http://127.0.0.1:5500`):
1. Paste the Reticulum link in the address bar
2. Enter

**Via NomadNet TUI:**
1. Press `G` (Go to)
2. Paste the hash/link
3. Enter

### 3.4. Micron/NomadNet link format

```
<hash_reticulum>:/page/<page_name>.mu
```

Example inside a `.mu` file:
```
`<<NODE_HASH>:/page/index.mu`>← Back to INDEX`<`>
```

---

## 4. NomadNet & Micron (RPi4)

### 4.1. Main paths on the RPi4

```
~/.nomadnetwork/storage/pages/     # Public pages (.mu)
~/.nomadnetwork/storage/identity   # Node identity
~/.reticulum/                      # Reticulum config
~/.config/nomadnetwork/            # NomadNet config
~/nomadnet-venv/                   # Virtualenv NomadNet
~/index.mu                         # Local editing mirror of the homepage
```

Typical files under `~/.nomadnetwork/storage/pages/`:
```
index.mu
about.mu
printlab.mu
field-notes.mu
```

### 4.2. Editing pages — four options

#### Option 1 — SSH directly to the RPi4

```bash
ssh <NODE_USER>@<NODE_IP>
nano ~/.nomadnetwork/storage/pages/index.mu
# Save: Ctrl+O → Enter → Ctrl+X
sudo systemctl restart nomadnet
```

#### Option 2 — Local mirror on the RPi4

```bash
ssh <NODE_USER>@<NODE_IP>
nano ~/index.mu
cp ~/index.mu ~/.nomadnetwork/storage/pages/index.mu
sudo systemctl restart nomadnet
```

#### Option 3 — Edit on the Mac and sync via `scp`

```bash
# On the Mac
nano ~/.nomadnetwork/storage/pages/index.mu
# or: code ~/.nomadnetwork/storage/pages/index.mu

# Sync
scp ~/.nomadnetwork/storage/pages/index.mu \
    <NODE_USER>@<NODE_IP>:~/.nomadnetwork/storage/pages/index.mu

# Restart on the Pi
ssh <NODE_USER>@<NODE_IP> "sudo systemctl restart nomadnet"
```

#### Option 4 — Via the MeshChatX web UI

1. `https://meshchatx.com/index.html`
2. **Tools** → **Mesh Server** → **Micron Pages**
3. Edit `index.mu` inside the interface
4. Save → applied automatically

### 4.3. Quick workflow for large changes

```bash
# Backup before editing
ssh <NODE_USER>@<NODE_IP> \
  "cp ~/.nomadnetwork/storage/pages/index.mu \
      ~/.nomadnetwork/storage/pages/index.mu.backup-$(date +%Y%m%d-%H%M%S)"

# Show current content
ssh <NODE_USER>@<NODE_IP> "cat ~/.nomadnetwork/storage/pages/index.mu"

# List pages
ssh <NODE_USER>@<NODE_IP> "ls -lah ~/.nomadnetwork/storage/pages/"
```

### 4.4. Micron markup — quick reference

```
`!c          Centered text
`!           Highlighted/colored text
`B           Bold ON
`b           Bold OFF
`F<hex>      Foreground color (e.g. `Ff80)
`=           Horizontal rule
`<url`>      Link start
`<`>         Link end
```

Example of an internal link:
```
`<<NODE_HASH>:/page/index.mu`>INDEX`<`>
```

### 4.5. Micron best practices

- Use `#` / `##` for headings, `---` for separators
- Avoid `>` and large ASCII blocks — they create "white back" rendering issues
- Keep lines relatively short
- For minimal/raw content, avoid plugins, themes or external assets

### 4.6. Creating a new `.mu` page — template

```bash
ssh <NODE_USER>@<NODE_IP> "cat > ~/.nomadnetwork/storage/pages/new-page.mu << 'ENDOFFILE'
\`!c
┌─────────────────────────────────────────┐
│             PAGE TITLE                  │
└─────────────────────────────────────────┘

\`c══════════════════════════════════════════════════════════════════════

\`!c                    CENTERED SECTION

\`!
Normal left-aligned text.

\`B Bold text \`b

\`<<NODE_HASH>:/page/index.mu\`>← Back to INDEX\`<\`>

\`c══════════════════════════════════════════════════════════════════════
ENDOFFILE"

sudo systemctl restart nomadnet
```

Link for the new page:
```
<NODE_HASH>:/page/new-page.mu
```

### 4.7. Editing `~/index.mu` (local mirror) + field notes

```bash
# Start the NomadNet environment
cd ~
source nomadnet-venv/bin/activate
nomadnet

# Edit main index
nano ~/index.mu

# Edit field notes in Markdown
cd ~/mesh-guides
nano FIELD-NOTES.md

# Edit Micron version of the field notes
cd ~/.nomadnetwork/storage/pages
nano field-notes.mu

# Sync index into the capsule
cp ~/index.mu ~/.nomadnetwork/storage/pages/index.mu
```

---

## 5. Git & docs workflow

### 5.1. Repository

```
GitHub: <USER>/mesh-guides
Local : <USER_HOME>/mesh-guides
```

### 5.2. `docs/` structure

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

Create all at once:
```bash
mkdir -p docs/pt/technical docs/pt/user
mkdir -p docs/en/technical docs/en/user
mkdir -p docs/es/technical docs/es/user
```

### 5.3. Safe flow — checklist

```bash
cd ~/mesh-guides
git status

# 1. Clean .DS_Store
rm -f .DS_Store docs/.DS_Store docs/pt/.DS_Store docs/en/.DS_Store docs/es/.DS_Store

# 2. Add specific files (NEVER `git add .` blindly)
git add docs/pt/technical/file.md
git status

# 3. Commit
git commit -m "Clear message"

# 4. Sync with remote
git pull --rebase origin main

# 5. Push
git push origin main
```

### 5.4. Copying files from `~/Downloads`

If the `pt/`, `en/`, `es/` structure already exists under `~/Downloads`:

```bash
cp -R ~/Downloads/pt docs/
cp -R ~/Downloads/en docs/
cp -R ~/Downloads/es docs/
```

One file at a time:
```bash
cp ~/Downloads/pt/technical/meshtastic-technical-guide.md docs/pt/technical/
cp ~/Downloads/pt/user/meshtastic-beginner-guide.md docs/pt/user/
cp ~/Downloads/en/technical/meshtastic-technical-guide.md docs/en/technical/
cp ~/Downloads/en/user/meshtastic-beginner-guide.md docs/en/user/
cp ~/Downloads/es/technical/meshtastic-technical-guide.md docs/es/technical/
cp ~/Downloads/es/user/meshtastic-beginner-guide.md docs/es/user/
```

Verify:
```bash
find docs -maxdepth 4 -type f
```

### 5.5. Recovering files deleted by accident

If `git status` shows `deleted:`:

```bash
git restore path/to/file.md

# Examples
git restore FIELD-NOTES.md
git restore docs/pt/technical/handshake-protocol.md
```

### 5.6. Removing `.DS_Store` already staged

```bash
git restore --staged .DS_Store 2>/dev/null
git restore --staged docs/.DS_Store 2>/dev/null
git restore --staged docs/pt/.DS_Store 2>/dev/null
git restore --staged docs/en/.DS_Store 2>/dev/null
git restore --staged docs/es/.DS_Store 2>/dev/null
```

Tip: add to `.gitignore`:
```
.DS_Store
**/.DS_Store
```

### 5.7. Resolving an `add/add` conflict during rebase

```
CONFLICT (add/add): Merge conflict in docs/pt/technical/meshtastic-technical-guide.md
```

During a rebase: `--ours` = GitHub side, `--theirs` = your local side.

To keep your local version:
```bash
git checkout --theirs docs/pt/technical/meshtastic-technical-guide.md
git checkout --theirs docs/pt/user/meshtastic-beginner-guide.md
git add docs/pt/technical/meshtastic-technical-guide.md
git add docs/pt/user/meshtastic-beginner-guide.md
git rebase --continue
```

If `vim` opens:
```
ESC  :wq  ENTER
```

### 5.8. Fixing the remote URL (when the repo was renamed)

```bash
git remote set-url origin https://github.com/<USER>/mesh-guides.git
git remote -v
```

You should see:
```
origin  https://github.com/<USER>/mesh-guides.git (fetch)
origin  https://github.com/<USER>/mesh-guides.git (push)
```

### 5.9. Post-push cleanup

```bash
rm -rf FIELD-NOTES.md.backup
rm -rf meshtastic-guides-final-v1.zip
rm -rf meshtastic-guides-final-v1/
```

### 5.10. Condensed safe flow

```bash
cd ~/mesh-guides
git status
rm -f .DS_Store docs/.DS_Store docs/pt/.DS_Store docs/en/.DS_Store docs/es/.DS_Store
git add path/to/file.md
git status
git commit -m "Clear commit message"
git pull --rebase origin main
git push origin main
```

---

## 6. Troubleshooting by symptom

### 6.1. "Link establishment timed out" / page won't load

**Cause:** The RPi4 node announce has expired, or Reticulum on the Mac doesn't know the hash.

**Fix — force an announce on the RPi4:**

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

Wait 10–20 seconds and try again.

### 6.2. MeshChatX is unresponsive / backend error

```bash
# Diagnostics
ps aux | grep meshchatx | grep -v grep
lsof -i :8787

# Kill and free the port
lsof -ti :8787 | xargs kill -9

# Relaunch
~/start-mesh-driver.sh
```

If it persists, check the wrapper log at `/tmp/meshchatx-*.log` and confirm the `--reticulum-config-dir ~/.reticulum` flag.

### 6.3. MeshChatX "won't open" in the browser

**Don't try `http://127.0.0.1:8787` in your browser** — it doesn't work.

```bash
~/start-meshchatx.sh
open https://meshchatx.com/index.html
```

❌ WRONG: `http://127.0.0.1:8787`
✅ RIGHT: `https://meshchatx.com/index.html`

### 6.4. Multiple `rnsd` processes or weird behavior

```bash
ps aux | grep rnsd | grep -v grep
pkill -f rnsd
~/start-mesh-driver.sh
```

### 6.5. NomadNet on the Pi isn't serving

```bash
ssh <NODE_USER>@<NODE_IP> "ps aux | grep nomadnet | grep -v grep"

# If no process is running:
ssh <NODE_USER>@<NODE_IP>
source ~/nomadnet-venv/bin/activate
nomadnet &
exit

# Or via systemd:
ssh <NODE_USER>@<NODE_IP> "sudo systemctl status nomadnet"
ssh <NODE_USER>@<NODE_IP> "sudo systemctl restart nomadnet"
ssh <NODE_USER>@<NODE_IP> "journalctl -u nomadnet.service -n 50"

# Confirm pages are in place
ssh <NODE_USER>@<NODE_IP> "ls ~/.nomadnetwork/storage/pages"
ssh <NODE_USER>@<NODE_IP> "head -40 ~/.nomadnetwork/storage/pages/index.mu"
```

### 6.6. rBrowser won't load

```bash
lsof -i :5500
# If nothing → relaunch
~/start-rbrowser.sh           # iMac
~/start-rbrowser-air.sh       # MacBook Air
# Wait 5s
open http://127.0.0.1:5500
```

### 6.7. Empty page or 404 error

```bash
ssh <NODE_USER>@<NODE_IP>
ls -lh ~/.nomadnetwork/storage/pages/index.mu
cat ~/.nomadnetwork/storage/pages/index.mu
```

If empty, create a minimal one:
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

### 6.8. `websockets>=16.0` error on Python 3.9

→ You need **Python 3.11**. See section 7 (Disaster recovery).

### 6.9. "Too many files" error

Add to `~/.zshrc`:
```bash
ulimit -n 10240
```

Then reload:
```bash
source ~/.zshrc
```

### 6.10. NTFS USB drives — don't write via terminal

NTFS USB on macOS: use **Finder** (writes go through translation), **not** the terminal.

### 6.11. Identity vs ratchets

> **ratchets ≠ identity** — the `identity` file is the critical one.

Typical locations:
- Mac (MeshChatX real): `~/storage/identity`
- Mac (classic MeshChat): `~/reticulum-meshchat/storage/identity`
- RPi4 (NomadNet): `~/.nomadnetwork/storage/identity`

Without the right `identity`, you lose the node hash — you lose the public identity itself.

---

## 7. Full disaster recovery

### 7.1. USB backup stack

Recommended structure on the USB drive (`/Volumes/<USB_NAME>/BACKUPID/`):
```
iMac-meshchat-real/
iMac-reticulum/
Air-meshchat/
*-nomadnet/pages/
```

### 7.2. Recovering the MacBook Air — MeshChat venv

```bash
cd ~/reticulum-meshchat
rm -rf venv_novo
python3.11 -m venv venv_novo
source venv_novo/bin/activate
pip install rns lxmf peewee aiohttp websockets

~/start-meshchat.sh
```

> ⚠️ Use Python **3.11**, not 3.9. `websockets>=16.0` requires it.

### 7.3. Recovering the iMac — MeshChat real + Reticulum

```bash
# Restore MeshChat real
cp -r /Volumes/<USB_NAME>/BACKUPID/iMac-meshchat-real/* ~/storage/

# Restore global Reticulum
cp -r /Volumes/<USB_NAME>/BACKUPID/iMac-reticulum/* ~/.reticulum/storage/

~/start-meshchatx.sh
```

### 7.4. Recovering NomadNet (both Macs + RPi4)

```bash
cp -r /Volumes/<USB_NAME>/BACKUPID/*-nomadnet/pages ~/.nomadnetwork/storage/
```

### 7.5. Rebuilding the NomadNet venv from scratch (any machine)

```bash
cd ~
python3.11 -m venv nomadnet-venv
source nomadnet-venv/bin/activate
pip install nomadnet rns lxmf
```

### 7.6. Full Mac backup

```bash
~/backup-imac-all.sh   # or equivalent covering ~/.reticulum, ~/.nomadnetwork, ~/storage, venvs
```

### 7.7. Limits — add to `~/.zshrc`

```bash
echo 'ulimit -n 10240' >> ~/.zshrc
source ~/.zshrc
```

### 7.8. Restore checklist

- [ ] Python 3.11 installed (`brew install python@3.11`)
- [ ] venvs rebuilt in the right places
- [ ] `identity` restored in each storage
- [ ] Micron pages restored on the RPi4
- [ ] `~/.reticulum/` restored
- [ ] `ulimit -n 10240` applied
- [ ] `~/start-*.sh` scripts marked `chmod +x`
- [ ] `~/start-mesh-driver.sh` brings everything up cleanly
- [ ] Forced announce of the public node completed (see 6.1)
- [ ] Access to hash `<NODE_HASH>` confirmed

---

## 8. Cheat sheet — quick commands

### 8.1. Startup & shutdown

```bash
# === STARTUP === #
~/start-mesh-driver.sh                          # Everything at once (Mac)
~/start-meshchatx.sh                            # MeshChatX headless only
~/start-rbrowser.sh                             # rBrowser only (iMac)
~/start-rbrowser-air.sh                         # rBrowser only (Air)
open https://meshchatx.com/index.html           # MeshChatX UI
open http://127.0.0.1:5500                      # rBrowser UI

# === NomadNet TUI === #
source ~/nomadnet-venv/bin/activate
nomadnet --textui

# === Clean shutdown === #
pkill -f rnsd
pkill -f meshchatx
lsof -ti :8787 | xargs kill -9 2>/dev/null
lsof -ti :5500 | xargs kill -9 2>/dev/null
```

### 8.2. SSH to the RPi4

```bash
ssh <NODE_USER>@<NODE_IP>
```

### 8.3. Edit a page + restart

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

### 8.5. Status checks

```bash
rnstatus                                                   # Reticulum status on the Mac
ps aux | grep rnsd | grep -v grep
ps aux | grep meshchatx | grep -v grep
lsof -i :8787
lsof -i :5500
ssh <NODE_USER>@<NODE_IP> "sudo systemctl status nomadnet"
ssh <NODE_USER>@<NODE_IP> "ps aux | grep nomadnet | grep -v grep"
```

### 8.6. Quick page backup

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

## 9. Full path map

### 9.1. iMac (`<IMAC_HOST>`)

```
~/.reticulum/                              # Global Reticulum config
~/storage/                                 # MeshChatX real storage (holds identity)
~/meshchatx-py314/                         # MeshChatX venv (Python 3.14)
~/rbrowser/                                # rBrowser code
~/nomadnet-venv/                           # NomadNet venv
~/.nomadnetwork/                           # Local NomadNet config (TUI)
~/start-mesh-driver.sh                     # Unified launcher
~/start-meshchatx.sh                       # MeshChatX launcher
~/start-rbrowser.sh                        # rBrowser launcher
~/backup-imac-all.sh                       # Backup tool
~/mesh-guides/                             # Git repo (docs)
```

### 9.2. MacBook Air (`<AIR_HOST>`)

```
~/.reticulum/
~/reticulum-meshchat/                      # MeshChat (with venv_novo)
~/reticulum-meshchat/storage/identity      # ⚠️ critical identity
~/meshchatx-venv/                          # MeshChatX venv
~/nomadnet-venv/
~/start-mesh-driver.sh
~/start-meshchatx.sh
~/start-rbrowser-air.sh
~/start-meshchat.sh
```

### 9.3. RPi4 (`<NODE_USER>@<NODE_IP>`)

```
~/.nomadnetwork/storage/pages/             # Public Micron pages
~/.nomadnetwork/storage/identity           # ⚠️ public node identity
~/.reticulum/                              # Reticulum config
~/.config/nomadnetwork/
~/nomadnet-venv/                           # NomadNet venv
~/index.mu                                 # Local homepage mirror
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

## 10. Identities & LXMF

| Machine | Client | LXMF Address | Role |
|---|---|---|---|
| iMac | MeshChatX | `<LXMF_ID_IMAC>` | Main client |
| iMac | NomadNet textui | `<LXMF_ID_IMAC_TUI>` | Local TUI |
| RPi4 | NomadNet daemon | `<NODE_HASH>` | **Public node** |
| MacBook Air | MeshChat | `<LXMF_ID_AIR>` | Portable |

**Sending an LXMF message via MeshChatX:**

1. Open `https://meshchatx.com/index.html`
2. **New Message**
3. Destination: `<NODE_HASH>` (or another)
4. Type & Send (encrypted end-to-end)

---

## 11. Final checklists

### 11.1. Before every `git push`

- [ ] `git status` — no `.DS_Store`
- [ ] `git status` — no unintended `deleted:` entries
- [ ] Only the right files in "Changes to be committed"
- [ ] Remote points to the correct repo
- [ ] `git pull --rebase origin main` before pushing

### 11.2. Before leaving the house (on-the-go)

- [ ] MeshChatX running on the Mac (port 8787 in use)
- [ ] NomadNet running on the RPi4 (`systemctl status nomadnet` active)
- [ ] `.mu` pages up to date
- [ ] Backup done if you edited anything critical
- [ ] Forced announce done if you touched the network
- [ ] Links tested in MeshChatX/rBrowser
- [ ] SSH to the RPi4 tested from external networks (if applicable)

### 11.3. Before making big changes to the node

```bash
# On the Mac
ps aux | grep -E "rnsd|meshchatx" | grep -v grep

# On the Pi
ssh <NODE_USER>@<NODE_IP> "ps aux | grep nomadnet | grep -v grep"

# Page backup beforehand
ssh <NODE_USER>@<NODE_IP> \
  "cp ~/.nomadnetwork/storage/pages/index.mu \
      ~/.nomadnetwork/storage/pages/index.mu.backup-$(date +%Y%m%d-%H%M%S)"
```

### 11.4. Quick recovery (when everything panics)

1. Kill `rnsd` and MeshChatX if they're acting weird: `pkill -f rnsd && pkill -f meshchatx`
2. `~/start-mesh-driver.sh`
3. Make sure NomadNet is running on the Pi: `ssh <NODE_USER>@<NODE_IP> "sudo systemctl restart nomadnet"`
4. Force announce (section 6.1 or 8.4)
5. Verify Micron pages and NomadNet links

---

## 📌 Final notes

This guide is a distillation of real practice — daily driver + survival kit — for anyone running Reticulum/NomadNet/MeshChatX on a mixed Mac + RPi4 setup. Adapt the placeholders to your own setup and **keep your hashes, IPs and identities out of public repos**.

Contributions, corrections and forks are welcome. If you discover a better workflow for any of the symptoms listed here, open a PR or share it via LXMF — the mesh grows when we share recipes that actually work in the field.

---

**☥ walk quietly, but keep the signal alive ⚡**

*Built with Love in cooperation with Nature.*

*cypherpunks write code /// nature writes resilience /// we bridge both*

---

## 📜 Suggested license

Content of this guide released under **Creative Commons Attribution-ShareAlike 4.0 International (CC BY-SA 4.0)**:
https://creativecommons.org/licenses/by-sa/4.0/

Code snippets (shell scripts, Python) may be used under **MIT** or any equivalent permissive license, at the user's choice.
