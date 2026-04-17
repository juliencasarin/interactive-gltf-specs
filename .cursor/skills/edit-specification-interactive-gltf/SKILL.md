---
name: edit-specification-interactive-gltf
description: >-
  Edits interactive-gltf specification Markdown (especially glTF extension
  specs) and verifies alignment with Khronos glTF extension conventions. Use
  when changing or reviewing extension documents, JSON schema wording, naming,
  or extensionsUsed/extensionsRequired in this repo’s glTF-related specs.
---

# Edit specification (interactive-gltf)

Apply this skill to **any modification** of the **interactive-gltf** project’s specification documents that describe or depend on **glTF extensions** proposed by this repository.

## Language (required)

All specification Markdown under **`specifications/`** MUST be written in **English** (including titles, normative text, and tables). Exceptions: proper names, quoted third-party text, and code or JSON keys as required by glTF.

## Compliance check (required)

Before finishing, verify consistency with the official guidance in the **glTF 2.0 Extension Registry** and embedded “Extension Mechanics” / “Creating Extensions” sections:

- **Registry and rules**: [extensions/README.md](https://github.com/KhronosGroup/glTF/blob/main/extensions/README.md) on the Khronos glTF repo.

Validate at least:

- **Naming**: prefix rules (`KHR`, `EXT`, vendor prefixes), lowercase snake_case after the prefix, recommended `<PREFIX>_<scope>_<feature>` pattern when applicable.
- **Mechanics**: extensions add properties under `extensions` objects; used extensions appear in top-level `extensionsUsed`; truly load-blocking extensions belong in `extensionsRequired` per spec text.
- **No breaking changes to core**: extensions do not remove or redefine existing glTF properties.
- **Documentation quality**: status, dependencies, overview, schema links — aligned with how Khronos extension READMEs are structured (see [Template.md](https://github.com/KhronosGroup/glTF/blob/main/extensions/Template.md)).

If something is intentionally stricter or narrower than Khronos examples, state that explicitly in the spec text.

## Scope

- Specs live in this repo (e.g. under `extensions/` or root Markdown as the project evolves) — **not** under `UMI3D-SDK-version-2.9/`.
- Cross-check README/spec text against the registry’s **ratified** and **in-progress** lists only when claiming interoperability or citing related extensions.

## Workflow

1. Read the current spec files being changed.
2. Apply edits matching project style and the Khronos template expectations for extension docs.
3. Run through the **Compliance check** above; fix gaps.
4. Summarize what changed and any remaining assumptions or open questions.
