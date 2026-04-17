---
name: manage-proposal-open-issues
description: >-
  Manages the Open issues table in proposal Markdown under proposals/ with
  strict rules: create rows, append timestamped comments, close with decisions,
  sync roadmap when actions are implied. Use when editing proposals/*.md open
  issues or when the user asks to add, update, comment, or close a proposal issue.
---

# Manage proposal open issues

Apply when working on **`proposals/*.md`** and manipulating the **Open issues** table.

## Language (required)

All prose in **proposals** and **specifications** Markdown (titles, descriptions, table cells, roadmap, comments) MUST be **English**, except proper names, quoted specifications, or code identifiers.

## Paths

- Proposals: `proposals/` (often from `proposals/proposal-template.md`).
- Specifications: `specifications/` — read-only context when roadmap links to spec files.

## Username and timestamps

- Before writing any **`comments`** entry, obtain a **display name** for the current user (e.g. from the user message or ask once). Use it in brackets: `[DisplayName]`.
- Use **ISO 8601 in UTC** for date and time, e.g. `2026-04-17T14:32:00Z`. Append to the comment text as `(date, time)` using that instant or the user-provided time if they specify it.

## Open issues table

Expected columns: **name**, **description**, **comments**, **decision**, **state**.

Recommended **state** values: `open`, `closed` (or `resolved` if the proposal defines that — stay consistent within one file).

## Operations

### new (create row)

1. Set **name** and **description** (English).
2. Set **comments** to: `[DisplayName] : created (ISO8601 UTC)`.
3. Set **state** to **`open`**.
4. Leave **decision** empty or `-` until closure.

### edit (change name or description, or decision before close)

- **Only if `state` is `open`.** If not, refuse the edit and explain that the row is closed; offer to add a **new** row for follow-up work.
- **Append** to **comments** (do not delete prior notes): new line or bullet, format `[DisplayName] : <summary of edit> (ISO8601 UTC)`.

### comment (discussion only)

- **Append** to **comments**: `[DisplayName] : <text> (ISO8601 UTC)`.
- If **`state` is not `open`**, do not modify the row unless the user explicitly requests an exception; default is **refuse** (closed issues are immutable for routine updates).

### close

- **Only if `state` is `open`.**
- Require a clear, non-empty **decision** (English). If missing or vague, **stop** and ask the user for the exact **decision** text.
- Set **state** to **`closed`** (or agreed terminal state).
- **Append** to **comments**: `[DisplayName] : closed — <short summary> (ISO8601 UTC)`.
- If **decision** implies follow-up actions (e.g. write spec section, open PR), **update the Roadmap** section in the same file (see below).

## Roadmap synchronization

- If an **open issue** closure or **comment** implies concrete actions, add or adjust **Roadmap** items (English).
- When work **lands in `specifications/`**, update **Roadmap** so progress is visible, e.g.:
  - Prefix delivered items with **`[SPEC]`** and link to the path under `specifications/`, or
  - Add a short subsection **Delivered into specifications** with links.

## Refusals (default)

- Do not change **name**, **description**, **decision**, or **comments** on a row whose **state** is not **`open`**, except when the user explicitly overrides with a documented exception.
- Do not **close** without a clear **decision**.

## Workflow summary

1. Confirm **DisplayName** if needed.
2. Apply the operation rules above.
3. Ensure **English** throughout the edited sections.
4. If specs are touched indirectly (roadmap links), paths must point under `specifications/` as created or updated by the promotion workflow (see skill **promote-proposal-to-specification**).
