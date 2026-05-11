# rawmesh-node

Reticulum mesh node setup on Raspberry Pi 4, with NomadNet, LXMF and off‑grid–ready configuration.

This repository documents how to build and maintain a small, resilient mesh node that can run 24/7 on low power, serve local content, and provide messaging over Reticulum.

## What this is

rawmesh-node is a practical reference for:

- installing and configuring Reticulum on a Raspberry Pi 4  
- running NomadNet to serve local Micron pages over the mesh  
- exposing LXMF endpoints for bots and direct messaging  
- preparing the node for off‑grid use (low power, solar‑ready)

It is not a one‑click installer. It is a set of opinionated steps and configs that you can adapt to your own environment.

## Contents

The `docs/` directory contains:
### System manuals

For a consolidated, end‑to‑end view of how the Raspberry Pi node, desktop and laptop fit together (Reticulum, NomadNet, Micron, MeshChatX, LXMF), see:

- `docs/en/mesh-nomadnet-hardcoreguide-PUBLIC-EN.md` – Mesh & NomadNet hardcore guide (public EN)
- `docs/pt/mesh-nomadnet-hardcoreguide-PUBLIC-PT.md` – Guia hardcore Mesh & NomadNet (edição pública PT)

- system preparation and base OS setup for RPi4  
- Reticulum configuration and interface examples  
- NomadNet setup and content layout  
- LXMF basics for announcements and bots  
- notes on off‑grid / solar power planning for the node

As the project evolves, more detailed guides for specific roles (gateway, local hub, archive node) may be added.

## Relationship to other projects

- **mesh-guides** – Meshtastic EU868 operation guides (PT/EN/ES), focused on radio presets, roles and regulatory context.  
  Use those guides if you are working with Meshtastic nodes:
  - https://github.com/noduscypher/mesh-guides

- **tdeck-guides** – device‑specific documentation for LILYGO® T‑Deck / T‑Deck Plus running Meshtastic.  
  Use those guides for handheld Meshtastic devices:
  - https://github.com/noduscypher/tdeck-guides

rawmesh-node focuses on the **Reticulum backbone** side: always‑on, low‑power nodes that carry traffic and host services for the mesh.

## Status

This repository reflects a working setup in Beira Baixa, Portugal, and is updated as the underlying node configuration improves.

Expect incremental updates rather than versioned releases.

## License

This project is distributed under the terms described in the `LICENSE` file in this repository.
