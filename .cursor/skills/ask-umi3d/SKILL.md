---
name: ask-umi3d
description: >-
  Answers questions about UMI3D by consulting the read-only UMI3D SDK reference
  (docs and UMI3D-SDK), optionally glTF/Khronos specs, and writes the detailed
  answer under temp-md. Use when the user asks about UMI3D technology, SDK
  behavior, DTOs, CDK/EDK, or anything requiring the original UMI3D reference.
---

# Ask UMI3D

## Reference sources (authoritative)

Treat these paths as the **sole authoritative reference** for UMI3D technology in the workspace. **Read-only** — never edit, create, or delete files there (see project rules).

- **`UMI3D-SDK-version-2.9/docs`** — API / generated documentation (e.g. Doxygen HTML).
- **`UMI3D-SDK-version-2.9/UMI3D-SDK`** — SDK source tree (namespaces, DTOs, runtime behavior).

If the workspace root name differs, use the SDK root that contains both `docs/` and `UMI3D-SDK/`.

Search and read files under these trees until the question is answerable (semantic search, grep, open relevant files).

## glTF / Khronos — when to consult

After framing the UMI3D-side answer, decide whether the request also needs **glTF core** or **glTF extensions**:

- **Consult** [Khronos glTF repository](https://github.com/KhronosGroup/glTF) (specification, JSON schemas under `specification/`) when the question involves glTF 2.0 asset structure, properties, or conformance to the base format.
- **Consult** [glTF 2.0 Extension Registry](https://github.com/KhronosGroup/glTF/blob/main/extensions/README.md) when the question involves extension naming (`KHR` / `EXT` / vendor prefixes), `extensionsUsed` / `extensionsRequired`, extension mechanics, or how vendor extensions relate to ratified extensions.

Use web fetch or clone documentation as needed; cite links in the markdown note under `temp-md/`.

## Output: `temp-md`

1. **Create or update** a Markdown file under the repo’s **`temp-md/`** directory (ignored by git unless the project changes that).
2. Use a **clear filename**, e.g. `temp-md/umi3d-<short-topic>.md` or update an existing note if the user continues the same topic.
3. The file should contain: the question (or context), findings from the SDK reference (with file paths when useful), optional glTF/Khronos notes with links, and a structured answer.

## Chat reply

In the chat, give a **summary of at most two sentences** pointing to the `temp-md` file for the full detail. Do not duplicate the full answer in chat.
