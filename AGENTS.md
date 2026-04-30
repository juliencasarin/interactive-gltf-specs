## Cursor Cloud specific instructions

This repository defines **interactive-gltf** (glTF extensions for interactivity): **`proposals/`**, **`specifications/`**, and supporting Markdown only.

### Reference UMI3D repositories (do not modify)

The workspace may include **UMI3D** trees as **separate folder roots** (multi-root), for example under `C:\Repos\UMI3D\`. The **entire** tree under each root is **read-only reference**:

| Folder | Role | Upstream (informative) |
|--------|------|------------------------|
| **`UMI3D-SDK-version-2.9`** | SDK (`docs/`, `UMI3D-SDK/`, …) | [UMI3D/UMI3D-SDK](https://github.com/UMI3D/UMI3D-SDK) |
| **`UMI3D-BROWSER-version-3.6_sdk2.9`** | Unity Browser | [UMI3D/UMI3D-BROWSER](https://github.com/UMI3D/UMI3D-BROWSER) |
| **`UMI3D-Sketcher-no-history-2.11`** | Sketcher (Intraverse) | [UMI3D/UMI3D-Sketcher](https://github.com/UMI3D/UMI3D-Sketcher) |

- **Do not modify** any file under those roots: no edits, renames, or deletes.
- They reflect **UMI3D** technology that **inspires** interactive-gltf; portable format definitions belong in **`proposals/`** and **`specifications/`** at this repo root.

### Reference implementation (separate repository)

POC and product code (**`igltf-engine`**, editor frontend/backend, Python core) live in the **`interactive-gltf-engine`** repository, not here.

### Format changes driven by engine work

When implementation in **`interactive-gltf-engine`** affects extension JSON, script packaging, host API, or author-visible contracts, update **`proposals/`** and **`specifications/`** in **this** repository in the same effort and use the Cursor skill **`sync-interactive-gltf-format-from-engine`**.

### Language

- All Markdown under **`specifications/`** and **`proposals/`** MUST be **English** (body text, tables, roadmap). Exceptions: proper names, quoted material, and required code or JSON identifiers.

### Development workflow

- Edit Markdown and JSON Schema; no application build is required for documentation-only changes.
- Standard git operations (`git add`, `git commit`, `git push`) apply to all contributions.
