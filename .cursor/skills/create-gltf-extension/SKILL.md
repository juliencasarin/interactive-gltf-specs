---
name: create-gltf-extension
description: >-
  Adds a new glTF extension specification to the interactive-gltf-specs repo:
  creates a dedicated folder, root README from the Khronos extension template,
  then continues with edit-specification-interactive-gltf. Use when the user
  wants a new interactive-gltf glTF extension document or extension folder.
---

# Create glTF extension (interactive-gltf)

## Prerequisites

- All specification text MUST be **English** (same rule as `specifications/` and `proposals/`).
- Extension specs belong in **this repository** (`interactive-gltf-specs`), not in the UMI3D SDK tree.
- Follow **read-only** rules for `UMI3D-SDK-version-2.9/` if you need UMI3D context; do not modify the SDK.

## Steps

### 1. Dedicated folder

Create a **dedicated directory** for the extension, for example:

- `extensions/<EXTENSION_NAME>/`

Use the **exact extension id** as used in glTF JSON (e.g. `EXT_my_vendor_feature` or project-specific prefix per team convention). Keep naming consistent with [Khronos extension naming](https://github.com/KhronosGroup/glTF/blob/main/extensions/README.md#naming).

### 2. Root specification file

At the **root of that folder**, add **`README.md`** (or the single primary `.md` spec file the repo uses for that extension) filled from the **Khronos extension template**:

- Template source: [Template.md](https://github.com/KhronosGroup/glTF/blob/main/extensions/Template.md)

Replace placeholders (`TODO_EXTENSION_NAME`, TODO sections) with real content: Contributors, Status, Dependencies, Overview, glTF schema updates, JSON schema links, Known implementations, Resources.

A local copy of the template outline is in [khronos-template-reference.md](khronos-template-reference.md) for offline structure only; prefer the upstream template for section headings and expectations.

### 3. Edit specification (mandatory follow-up)

Apply the skill **`edit-specification-interactive-gltf`** (see `.cursor/skills/edit-specification-interactive-gltf/SKILL.md`): refine wording, schemas references, and **compliance** with the [Extension Registry README](https://github.com/KhronosGroup/glTF/blob/main/extensions/README.md).

### 4. Registry mention (optional)

If this repo maintains a central list of its extensions (e.g. main `README.md`), add a link to the new folder in the same edit or a follow-up task.

## Output

Summarize the new path(s), extension id, and any follow-ups (e.g. JSON schema files to add later).
