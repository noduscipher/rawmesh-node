# rawmesh-node

Micron pages, notes, and public content for a resilient NomadNet / Reticulum node in Beira Baixa, Portugal.

This repository gathers the structure and text of the node, including homepage content, about pages, development notes, and lightweight publishing experiments.

## Current workflow

The live node serves plain Micron pages from:

`~/.nomadnetwork/storage/pages`

Main homepage file:

`index.mu`

Typical workflow:

1. Edit pages locally or through SSH on the Raspberry Pi node.
2. Keep content raw and minimal so that any NomadNet client can render it cleanly.
3. Create a backup before changing important pages.
4. Publish updates by replacing the corresponding `.mu` files in the live pages directory.
5. Verify the result in NomadNet-compatible clients such as MeshChatX and related tools.

## Notes

This project grows step by step in cooperation with nature, with a focus on resilient communication, quiet tools, and minimal publishing.

Licensing is handled carefully per file or per work where applicable.

---

Licensed under CC BY-SA 4.0  
https://creativecommons.org/licenses/by-sa/4.0/

*walk quietly, but keep the signal alive.*  
₿uilt with Love 🧡 in cooperation with Nature 🌿
