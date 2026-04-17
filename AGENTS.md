## Cursor Cloud specific instructions

This is a **specification-only / documentation-only** repository (`interactive-gltf-specs`).
It defines an extension of the glTF format for interactivity.

### Reference SDK (do not modify)

The workspace may include **`UMI3D-SDK-version-2.9`** as a **separate folder root** (multi-root), for example next to `interactive-gltf-specs`. On a typical machine it may live at `C:\Repos\UMI3D\UMI3D-SDK-version-2.9`. Under that root you will see subtrees such as **`docs/`** and **`UMI3D-SDK/`** — the **entire** SDK tree is **read-only reference**.

- **Do not modify** any file under `UMI3D-SDK-version-2.9` (including `docs`, `UMI3D-SDK`, and everything else): no edits, renames, or deletes.
- It reflects the **UMI3D** technology that **inspires** interactive-gltf; specification work belongs in **this** repo’s own files (e.g. `README.md`, future spec Markdown), not in the SDK.

### Key facts

- The repo contains **no source code, no dependencies, no build tooling, and no runnable services** (aside from optional read-only UMI3D SDK reference material, if present).
- The only meaningful files are `README.md` (project description) and `LICENSE` (Apache 2.0).
- There is no package manager, no lockfile, no test suite, no linter configuration, and no CI pipeline.
- No update script is needed — there are no dependencies to refresh.

### Language

- All Markdown under **`specifications/`** and **`proposals/`** MUST be **English** (body text, tables, roadmap). Exceptions: proper names, quoted material, and required code or JSON identifiers.

### Development workflow

- Contributions are documentation / specification text in Markdown.
- No build, lint, or test commands exist.
- Standard git operations (`git add`, `git commit`, `git push`) are sufficient for all workflows.
