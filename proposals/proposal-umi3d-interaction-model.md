# UMI3D-inspired interaction model for interactive-gltf

**Language:** All content in this file MUST be **English** (see repository convention for `proposals/` and `specifications/`).

## Description

This proposal recommends that **interactive-gltf** adopt, as a **normative conceptual baseline**, the interaction architecture already implemented and documented in **UMI3D** (tools, interactables, abstract interactions, and browser-to-environment request types). The goal is not to copy UMI3D wire formats or protocols wholesale, but to **map the same semantics** into glTF 2.0 extensions: scene-graph attachment (e.g. which node is interactable), declarative interaction definitions, and a clear taxonomy of user-driven events and payloads so that engines and authoring tools can interoperate.

**Motivation:** UMI3D already separates *tools* (containers with metadata and a list of interaction ids), *interactables* (tools bound to a node, with hover/distance/priority options), and *interactions* (manipulations, links, forms, events, etc.), with corresponding **InteractionRequestDto** variants for client feedback. Reusing this structure reduces reinvention, aligns interactive-gltf with a proven model, and leverages the read-only **UMI3D-SDK** reference in this workspace for traceability.

**Scope:** Define which UMI3D concepts become **first-class** in interactive-gltf (minimum viable set vs. full parity), how they attach to **glTF nodes** and **extensions**, and what remains **runtime-specific** (e.g. networking) vs. **asset-embedded**. Out of scope for this proposal alone: choosing a single vendor extension name prefix; that can follow Khronos and project naming rules in a follow-up.

**Expected outcome:** A staged roadmap leading to one or more **specifications/** documents (JSON schema fragments, property tables, conformance notes) that describe the interaction model in glTF terms, with explicit references to UMI3D DTOs as **informative** alignment notes where helpful.

## Contributors

JCA

## Open issues

Issue rows below follow **UMI3D SDK 2.9** `AbstractInteractionDto` types under `Common/InteractionSystem/.../InteractionModel` (plus **cross-cutting** rows). Each row tracks whether and how that type is represented in interactive-gltf. **Parameter interactions** are a single row covering all concrete `AbstractParameterDto` subclasses.

| name | description | comments | decision | state |
|------|-------------|----------|----------|-------|
| Asset vs runtime | Clarify what is serialized in the glTF asset (declarative tools/interactables/interactions) versus what belongs only to a live session protocol (e.g. operation keys, streaming updates). | [JCA] : created (2026-04-17T12:00:00Z) | - | open |
| glTF mapping | Specify how tools and interactables attach to nodes (extensions on `nodes`, extra root objects, or dedicated extension arrays) and how interaction ids relate to glTF identity. | [JCA] : created (2026-04-17T12:00:00Z) | - | open |
| Manipulation (`ManipulationDto`) | Map `ManipulationDto` / `UMI3DManipulation` (DOF groups, constraints) into glTF; define client-to-host feedback alignment (`ManipulationRequestDto`). | [JCA] : created (2026-04-17T15:00:00Z) | - | open |
| Link (`LinkDto`) | Map `LinkDto` / `UMI3DLink` (URI, open behaviour) into glTF; align with `LinkOpened` requests where relevant. | [JCA] : created (2026-04-17T15:00:00Z) | - | open |
| Form (`FormDto`) | Map `FormDto` / `UMI3DForm` and nested form DTOs into glTF; align with `FormAnswerDto` and form UI semantics. | [JCA] : created (2026-04-17T15:00:00Z) | - | open |
| Event (`EventDto`) | Map `EventDto` / `UMI3DEvent` (state, hold, etc.) into glTF; align with `EventTriggeredDto` and `EventStateChangedDto`. | [JCA] : created (2026-04-17T15:00:00Z) | - | open |
| Drawing interaction (`DrawingInteractionDto`) | Map `DrawingInteractionDto` / `UMI3DDrawingInteraction` (extends event semantics) into glTF; align with `DrawingDto` / upload flows. | [JCA] : created (2026-04-17T15:00:00Z) | - | open |
| Connection form (`ConnectionFormDto`) | Map `ConnectionFormDto` (connection-time form with `AbstractParameterDto` fields) into glTF or mark as session-only. | [JCA] : created (2026-04-17T15:00:00Z) | - | open |
| Wait connection (`WaitConnectionDto`) | Map `WaitConnectionDto` into glTF or mark as session-only. | [JCA] : created (2026-04-17T15:00:00Z) | - | open |
| Parameter interactions (`AbstractParameterDto`) | Map UMI3D parameter interactions (`AbstractParameter` / `AbstractParameterDto`) into glTF for all concrete types: `BooleanParameterDto`, `ColorParameterDto`, `DeviceIdParameterDto`, `EnumParameterDto`, `FloatParameterDto`, `FloatRangeParameterDto`, `IntegerParameterDto`, `IntegerRangeParameterDto`, `LocalInfoRequestParameterDto`, `StringParameterDto`, `UploadFileParameterDto`, `Vector2ParameterDto`, `Vector3ParameterDto`, `Vector4ParameterDto`, `WaitParameterDto`; align with `ParameterSettingRequestDto` (and upload-related flows where applicable). | [JCA] : created (2026-04-17T15:00:00Z). [JCA] : merged 15 per-parameter rows into this single issue (2026-04-17T16:00:00Z) | - | open |

## Roadmap

1. **Review** — Walk through UMI3D `Common/InteractionSystem` DTOs (`AbstractToolDto`, `InteractableDto`, each non-parameter `AbstractInteractionDto` type and the consolidated **Parameter interactions** issue) and related `InteractionRequestDto` types; prioritize milestones using the issue rows.
2. **Draft** — Add or extend Markdown under `specifications/` with glTF-oriented naming, property definitions, and informative “UMI3D alignment” notes.
3. **Iterate** — Close proposal issues as decisions land; split additional extensions if the working group prefers smaller, composable glTF extensions.
