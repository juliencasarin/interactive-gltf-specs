---
name: promote-proposal-to-specification
description: >-
  Progressively promotes content from proposals/ into specifications/: create or
  update spec Markdown, keep the proposal roadmap in sync when milestones ship,
  and align with Khronos glTF conventions where relevant. Use when moving
  proposal text into normative docs or updating existing specs from a proposal.
---

# Promote proposal to specification

Use when **proposal → specification** work is requested. Promotion may be **incremental**: not everything must move at once; existing files under **`specifications/`** may be **updated** rather than replaced.

## Language (required)

All normative and narrative text in **`specifications/`** and remaining **`proposals/`** prose MUST be **English**, except proper names, quoted material, or code.

## Paths

- **Source:** `proposals/<proposal>.md` (roadmap, open issues decisions).
- **Target:** `specifications/` (new folders or existing Markdown / extension docs).

## Workflow

1. Read the proposal’s **Description**, **Open issues** (especially **decision**), and **Roadmap**.
2. For each roadmap item (or open-issue outcome) that should become normative:
   - **Create** new files under `specifications/` as needed, **or**
   - **Patch** existing specs when the decision extends or corrects them — do not duplicate; merge and cross-reference.
3. Apply **`edit-specification-interactive-gltf`** for glTF extension structure, naming, `extensionsUsed` / `extensionsRequired`, and Khronos alignment.
4. If a **new glTF extension folder** is required, follow **`create-gltf-extension`**.
5. **Update the proposal’s Roadmap** so shipped work is visible:
   - Mark completed milestones with **`[SPEC]`**, link to `specifications/...`.
   - Leave clearly labeled **proposal-only** or **in progress** items where work is not yet in spec form.
6. If the proposal’s scope shrinks or shifts after spec work, adjust the proposal **Description** or roadmap notes (English).

## Progressive delivery

- Partial delivery is normal: one section of the roadmap may land in `specifications/` while others stay in the proposal.
- When updating **existing** specifications, add a short changelog or revision note in the spec if the repo style expects it (otherwise at minimum reflect the change in the proposal **Roadmap**).

## Coordination with open issues

- If promotion was triggered by closing an open issue, ensure the **decision** in the proposal matches what was written in `specifications/`.
- Use **`manage-proposal-open-issues`** if the user needs the issues table updated in the same pass.

## Output

- Summarize: files created/updated under `specifications/`, and how the proposal **Roadmap** was amended to show spec progress.
