---
name: sync-interactive-gltf-format-from-engine
description: >-
  When code in the separate interactive-gltf-engine repo (igltf-engine, editor
  front/back/core) changes glTF extension JSON, script packaging, host API, or
  on-disk layout, update proposals/specifications in this interactive-gltf-specs
  repo and use the existing spec skills. Use for any engine change that affects
  portable format truth.
---

# Sync interactive-gltf format from engine work

Apply this skill whenever implementation in the **`interactive-gltf-engine`** repository (sibling clone or second workspace root) affects **what valid assets mean** for tools and runtimes other than a single app build. Examples: new extension properties, script URI rules, required `extensionsUsed`, host callbacks, security assumptions, or API/file conventions exposed to authors.

## Do not modify UMI3D SDK reference

If **`UMI3D-SDK-version-2.9`** is in the workspace, it stays **read-only**. Align **names and concepts** in Markdown only; do not copy SDK source.

## Language (required)

All normative or roadmap body text under **`specifications/`** and **`proposals/`** MUST be **English**. See repository **`AGENTS.md`**.

## Workflow

1. **Identify the delta** — List what changed in behavior or data (JSON fields, file pairs like `test.glb` + `test.js`, endpoints only if they encode normative packaging rules).
2. **Update proposals first** — If the behavior is still experimental, edit the relevant files under **`proposals/`** (and **`temp-md/`** only for scratch notes, not as a substitute for proposal updates).
3. **Promote when stable** — When a behavior is ready to be implementable across engines, use **`promote-proposal-to-specification`** to move or mirror text into **`specifications/`**.
4. **Khronos alignment** — For glTF extension mechanics and naming, apply **`edit-specification-interactive-gltf`** to the spec Markdown you touch.
5. **Track open questions** — If the engine needs a decision that is not yet specified, add or update rows in proposal **Open issues** via **`manage-proposal-open-issues`**, and add deferred product items to **`ROADMAP.md`** in the **`interactive-gltf-engine`** repository when they are implementation choices rather than format issues.
6. **Finish** — Summarize: spec files changed, engine repo files changed, and any remaining gaps between code and spec.

## When *not* to use this skill

- Pure UI styling, internal refactors, or backend deployment config that does **not** change the asset format or author-visible contracts.
- Typos in code comments with no spec meaning.
