# interactive-gltf-specs

This repository defines **interactive-gltf**: a specification effort to describe **interactive 3D content** as a **standard glTF 2.0** asset—so the same file can be authored once and consumed across **different 3D engines** (e.g. web, Unity, Unreal) and **different devices** (desktop, mobile, headset), without locking behavior to a single vendor stack.

## Goals

- **glTF-centered** — Build on [glTF 2.0](https://www.khronos.org/gltf/), the widely adopted Khronos format for transmitting 3D scenes. Interactivity is expressed as **documented glTF extensions** (JSON alongside meshes, materials, and nodes).
- **Engine-agnostic** — Runtimes implement a shared meaning for interactions, scripts, and identifiers; they are not required to use a specific game engine API.
- **Device-agnostic** — The format describes *what* is interactive and *how* it should behave at a logical level; input mapping and rendering remain the host’s responsibility.
- **Declarative first** — Prefer data in the asset (extensions, metadata, optional script URIs) so tools can validate, diff, and package interactive scenes like any other glTF.

In short: **a portable, interactive 3D object format**—not a replacement for glTF, but an **extension family** that makes glTF scenes **meaningfully interactive** in a standardized way.

## Why not invent a new binary format?

glTF already solves geometry, materials, animation, and scene graph structure. interactive-gltf adds a **thin, explicit layer** for interactivity so pipelines (authoring, validation, delivery, viewers) can stay aligned with the **existing glTF ecosystem**.

## Relationship to UMI3D (optional context)

You do **not** need to know **UMI3D** to use this project. For teams that already work with UMI3D, the proposals borrow a **proven interaction model** (tools, interactables, interaction types, request-like payloads) as an **informative alignment** so concepts and identifiers can map cleanly. That alignment is documented in the proposals; the **normative** target remains **Khronos-style glTF extensions** usable by any runtime.

## Repository layout

| Path | Purpose |
|------|---------|
| [`proposals/`](proposals/) | Design proposals (interaction model, scripts, callbacks, transactions). |
| [`specifications/`](specifications/) | Normative specification text and JSON schema fragments (as they are published). |
| [`temp-md/`](temp-md/) | Working notes and drafts; not normative. |

**Language:** Documents under `specifications/` and `proposals/` are written in **English**, per repository convention.

## Reference material (read-only)

If **`UMI3D-SDK-version-2.9`** is present in the workspace (often as a second root, with `docs/` and `UMI3D-SDK/`), it is **read-only** reference for UMI3D terminology and DTOs. **Do not modify** that tree; evolve the interactive-gltf specification only in **this** repository.

## Contributing

Contributions are **documentation and specification text** (Markdown, JSON Schema). There is no application code or build step in this repository. Use normal Git workflows (`git add`, `git commit`, `git push`).

## License

See [`LICENSE`](LICENSE) (Apache 2.0).
