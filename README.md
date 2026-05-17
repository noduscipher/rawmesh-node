# ☥ rawmesh-node

> A practical reference for building and maintaining a **Reticulum mesh node** on a Raspberry Pi 4,
> with NomadNet, LXMF and an off-grid–ready configuration.

This repository documents a real, working setup for running a small, resilient, low-power node
that can operate 24/7, serve local content, and provide messaging over Reticulum.
The philosophy is **silent infrastructure — always on, nothing in excess**.

---

## What this is

`rawmesh-node` is a practical reference for:

- Installing and configuring **Reticulum** on a Raspberry Pi 4
- Running **NomadNet** to serve Micron pages over the mesh
- Exposing **LXMF** endpoints for bots, announcements and direct messaging
- Preparing the node for **off-grid use** — low power, solar-ready
- Building a maintenance structure where stability comes before convenience

It is not a one-click installer. It is a set of opinionated steps and configs
that you can adapt to your own environment.

---

## Contents

The `docs/` directory contains the main manuals and supporting technical material.

### System manuals

For a consolidated, end-to-end view of how the Raspberry Pi node, desktop and laptop
fit together — Reticulum, NomadNet, Micron, MeshChatX and LXMF — see:

- [`docs/en/mesh-nomadnet-hardcoreguide-PUBLIC-EN.md`](docs/en/mesh-nomadnet-hardcoreguide-PUBLIC-EN.md) — Mesh & NomadNet hardcore guide (public EN)
- [`docs/pt/mesh-nomadnet-hardcoreguide-PUBLIC-PT.md`](docs/pt/mesh-nomadnet-hardcoreguide-PUBLIC-PT.md) — Guia hardcore Mesh & NomadNet (edição pública PT)

### Topics covered

- System preparation and base OS setup for RPi4
- Reticulum configuration and interface examples
- NomadNet setup and content layout
- LXMF basics for announcements and bots
- Notes on off-grid and solar power planning for the node

As the project evolves, more detailed guides for specific roles — gateway, local hub,
archive node — may be added.

---

## Relationship to other projects

This repository is part of a broader set of practical documentation around
resilient networks and lightweight infrastructure.

| Repository | Focus |
|---|---|
| [mesh-guides](https://github.com/noduscypher/mesh-guides) | Meshtastic EU868 operation guides (PT/EN/ES) — radio presets, roles, regulatory context |
| [tdeck-guides](https://github.com/noduscypher/tdeck-guides) | Device-specific documentation for LILYGO® T-Deck / T-Deck Plus running Meshtastic |
| **rawmesh-node** | Reticulum backbone — always-on, low-power nodes that carry traffic and host mesh services |

Use the other repositories if you are working with Meshtastic nodes.
`rawmesh-node` focuses on the Reticulum side: nodes that stay online, carry traffic
and host useful services for the mesh.

---

## Status

This repository reflects a working setup in **Beira Baixa, Portugal**, and is updated
as the underlying node configuration improves in the field.

Expect incremental updates rather than versioned releases.

---

## ☥ Contributing

Issues, corrections and pull requests are welcome.

Useful contributions include:

- Technical corrections to commands, paths or parameter names
- Clearer explanations or diagrams
- Notes from real installations — urban or rural, varied hardware, power setups
- Adjustments for 24/7 operation and solar integration
- Translations and editorial improvements in EN / PT / ES

The goal is to keep this repository a living reference for running small,
robust and useful Reticulum nodes.

---

## ☥ Value for Value

If this repository helped you build or maintain your own node, you can return value
in whatever way makes sense to you.

The main goal is to keep small, low-power nodes online and quietly evolving —
not to optimise for attention or growth.

⚡ **Lightning:** `trustyflame02@zeuspay.com`

No sats? Carry the signal further: share it, mirror it, translate it, remix it.

---

## ☥ License

Distribution terms are described in the [`LICENSE`](LICENSE) file in this repository.

---

*☥ Walk in silence, ₿ keep the signal alive.*

*₿uilt with love in cooperation with nature.* · 🜁 🜂 ☰ ☱ ☲ ☳ ☴ ☵ ☶ ☷ 🜃 🜄
