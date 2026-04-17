# JavaScript script dependencies, interaction callbacks, and UMI3D-shaped transactions

**Language:** All content in this file MUST be **English** (see repository convention for `proposals/` and `specifications/`).

## Description

This proposal specifies how **interactive-gltf** can attach **JavaScript** files to a glTF asset, bind **interactions** to named **callbacks**, pass a **JSON-only** invocation `payload` (author parameters plus a slim **`umi3d`** browser→server request), expose a **global host API** (working name **`GLTF`**) for scene access and optional imperative helpers, and allow handlers to **return JSON transactions** aligned with **UMI3D EDK / DTO** shapes so that **web (Three.js)** and **Unity (C#)** clients **validate and apply** updates on the same path as **network-delivered** messages where possible.

**Relationship:** It complements [`proposal-umi3d-interaction-model.md`](proposal-umi3d-interaction-model.md) (tools, interactables, interaction types). That proposal covers the interaction **taxonomy**; this one covers **scripting**, **`callback`** / **`payload`** on interaction DTOs, and the **runtime bridge**.

**Motivation:** UMI3D SDK 2.9 does not define glTF-level script dependencies for interactions. Authors need **reusable** handlers, **fixed** per-instance parameters (`payload.targetId`, etc.), and **one portable script** for both JS and embedded JS-in-C# runtimes.

**Scope:** glTF extension design for `scripts[]`, interaction fields `callback` and `payload`, host naming, `Umi3dTransactionJSON` (informative until schema exists), security and loader behaviour. **Out of scope:** a single mandated JS engine for Unity (Jint vs WebView remains a product choice).

**Expected outcome:** Normative Markdown and JSON Schema under `specifications/` for script extensions and interaction callback fields; conformance notes for runtimes; informative alignment tables for UMI3D `InteractionRequestDto` and EDK-style operations.

## Contributors

JCA

## Open issues

Each row maps to the **points to decide** tracked in the technical design (former section 8). Close with an explicit **decision** when resolved.

| name | description | comments | decision | state |
|------|-------------|----------|----------|-------|
| Asset and scripts | Resolve: extension names (`EXT_*` vs vendor), JSON Schema location; inline script blobs in `.glb` and size cap; multi-file `scripts[]` resolution and optional `scriptId` on interaction; minification vs stable `callback` export names. | [JCA] : promoted from `temp-md/interactive-gltf-javascript-scripts-and-host-api.md` (2026-04-17T17:00:00Z) | - | open |
| `payload` and handler argument | Normative mapping for `payload.umi3d` per `InteractionRequestDto` subtype and operation key; omitted-field rules; versioning when UMI3D DTOs evolve; behaviour if author uses reserved `umi3d` inside glTF `payload` (error vs override vs merge). | [JCA] : promoted from temp-md (2026-04-17T17:00:00Z) | - | open |
| Global host object (`GLTF`) | Final global name (`GLTF` vs `interactiveGltf` vs other) to avoid collisions; read-only vs mutable `SceneObjectHandle`; sync vs async; optional non-global alias for ES modules. | [JCA] : promoted from temp-md (2026-04-17T17:00:00Z) | - | open |
| Transactions (UMI3D-shaped JSON) | Granularity (single vs batch operations); schema source (interaction requests only vs property/entity updates as EDK); validation allowlist; mandatory reuse of network apply path in CDK; local-only vs forward to server vs both; formal `Umi3dTransactionJSON` schema. | [JCA] : promoted from temp-md (2026-04-17T17:00:00Z) | - | open |
| Runtime and security | Missing handler behaviour; void vs returned transaction; async `Promise` handlers; sandbox (network/file), CSP, URI trust. | [JCA] : promoted from temp-md (2026-04-17T17:00:00Z) | - | open |

## Roadmap

1. **Decide** — Close or narrow **Open issues** rows (naming, `payload.umi3d` mapping, transaction schema, host mutability).
2. **Specify** — Add normative documents under `specifications/` (script root extension, interaction `callback`/`payload`, optional JSON Schema fragments) using [`edit-specification-interactive-gltf`](../.cursor/skills/edit-specification-interactive-gltf/SKILL.md) conventions.
3. **Align** — Cross-check with [`proposal-umi3d-interaction-model.md`](proposal-umi3d-interaction-model.md) and UMI3D SDK DTOs; split into multiple extensions if needed (`scripts` vs `callbacks`).
4. **Implement** — Reference CDK/EDK and browser runtimes outside this documentation-only repository.

---

## Technical design (proposal body)

The following sections are the full technical content of the proposal (formerly developed under `temp-md/`).

### 1. Design principles

| Principle | Rationale |
|-----------|-----------|
| **Host API, not engine API** | Scripts use a **small, versioned** global host object (working name **`GLTF`**, TBD) for scene queries and transaction helpers — **not** Three.js or Unity APIs directly if cross-runtime portability is required. |
| **JSON-only handler argument** | The **`payload`** passed to `handler(payload)` is **JSON data only** (serializable): author fields + `umi3d` slim request. **No** live objects embedded in `payload`. |
| **Stable identifiers** | Use **glTF extension object indices** for scripts and **existing interaction / entity ids** (e.g. UMI3D `ulong` ids as strings in JSON). **Handler identity** is the **`callback`** string on the interaction DTO (JavaScript function name). |
| **UMI3D-shaped transactions** | Ideal model: scripts **read** current state via the host, **return** a **JSON transaction** aligned with what the **UMI3D server / EDK emits today** (DTO / operation shape); the **browser runtime (JS or C#) applies** the transformation using the same code path as for network-received updates, where possible. |
| **Security** | Treat scripts as **untrusted** unless a separate trust model is defined (signed URIs, allowlists, CSP for Web). |

**SDK reference:** UMI3D 2.9 does **not** define a first-class “script dependency” on glTF for interactions; behaviour is driven by DTOs (`AbstractInteractionDto`, `InteractableDto`, …) and runtime networking. This proposal describes **target** spec/runtime architecture, not current SDK behaviour.

### 2. Declaring scripts in glTF extensions

#### 2.1 Recommended shape (vendor / `EXT_` extension on the asset root)

Add a **root-level extension** (name TBD, e.g. `EXT_interactive_gltf_scripts` or a vendor-prefixed name) that defines a **`scripts`** array. Each entry is a logical script resource:

```jsonc
{
  "extensions": {
    "EXT_interactive_gltf_scripts": {
      "scripts": [
        {
          "id": 0,
          "uri": "behaviors/pick-handler.mjs",
          "mimeType": "text/javascript",
          "kind": "module",
          "integrity": "sha384-…"
        },
        {
          "id": 1,
          "uri": "behaviors/legacy.iife.js",
          "mimeType": "text/javascript",
          "kind": "classic"
        }
      ]
    }
  }
}
```

**Why not put JS inside `buffers`?** glTF buffers are for geometry/skin/animation; embedding large scripts in binary chunks hurts authoring and caching. Prefer **URI** (relative to `.gltf` / `.glb` container rules) with optional **integrity** (SRI-style).

**`.glb` note:** For single-file delivery, either (a) **append** script sidecars next to the `.glb` in the package, or (b) define an optional **`data` + `byteLength`** inline slot in the extension for small snippets only; keep large code external.

#### 2.2 `extensionsUsed` / `extensionsRequired`

Follow Khronos rules: declare the extension name in **`extensionsUsed`**; set **`extensionsRequired`** only if the asset is meaningless without script execution.

#### 2.3 Relationship to UMI3D `extensions.umi3d`

Keep **orthogonal**: `EXT_interactive_gltf_scripts` lists **code**; existing **`umi3d`** payloads still describe **interactables, interactions, nodes**. The **handler** for an interaction is selected by a **`callback`** field on the serialized interaction DTO itself (section 3), not by a separate sidecar map.

### 3. Linking functions to interactions — `callback` on the interaction DTO

**Proposal:** add a single optional property on the **interaction** object (same level as `id`, `name`, type-specific fields), e.g. **`callback`**, whose value is the **name of the JavaScript function** to invoke when the interaction fires.

Example (shape illustrative; exact extension key for interactive-gltf TBD):

```jsonc
{
  "id": "1234567890",
  "type": "Event",
  "name": "OnTrigger",
  "callback": "onDoorInteract",
  "payload": {
    "targetId": "9876543210"
  }
}
```

If the interaction is nested under `extensions.umi3d` (or your interactive-gltf analogue of `AbstractInteractionDto`), **`callback`** (and optional **`payload`**) live **inside that interaction DTO** so they travel with the same entity as `name`, `description`, and type-specific data.

Semantics:

- **`callback`**: string, **JavaScript function name** to resolve after script modules are loaded (see below). For **ES modules**, this is typically a **named export** matching this string. For **classic** scripts, `globalThis[callback]` or an equivalent host registry.
- **`payload`** (optional): JSON object authored in the asset. **Fixed, reusable parameters** for this interaction instance (e.g. `targetId`, labels, feature flags). These keys are **copied verbatim** into the runtime object passed to the script (section 3.1). Authors choose names; they must not use the reserved key **`umi3d`** here (that key is injected at runtime).
- **Omission:** if `callback` is absent or empty, the runtime uses built-in behaviour only (no script handler).

#### 3.1 Runtime `payload` passed to the handler — **JSON only** (`umi3d` + author fields)

To keep **one function reusable** across many interactions, the runtime calls the handler with a **single argument** `payload`:

```txt
handler(payload)
```

Where **`payload`** is **JSON-serializable data only** (plain object after parse — no host handles, no engine objects):

1. **Start** from the interaction DTO’s optional **`payload`** object from the asset (author-defined keys only), e.g. `{ "targetId": "9876543210" }`.
2. **Set** `payload.umi3d` to a **normalized interaction request** object that corresponds to what **browsers send toward the environment** for this event: same information as the relevant **`InteractionRequestDto`** subtype in UMI3D (e.g. `EventTriggeredDto`, `HoveredDto`, …), but **serialized in a slim JSON shape** — **omit** properties that are `null` / undefined / redundant defaults (“useless” for the wire format). The exact subtype depends on the interaction type and event (hover vs trigger vs form answer, etc.).

**Scene access, C# bridge, transactions** are **not** embedded in `payload`. They are exposed through a **separate global host object** injected by the runtime (section 4; working name **`GLTF`**).

**Reserved keys** on the merged `payload` object (runtime wins on conflict if mis-authored):

| Key | Source |
|-----|--------|
| `umi3d` | Runtime only — browser→server-shaped **JSON** for this invocation. |

All other keys come from **author `payload`** in the asset unless the spec adds more reserved names later. Authors **must not** put **`umi3d`** in the glTF `payload`; if they do, behaviour is TBD (see **Open issues**).

**TypeScript sketch (informative):**

```ts
/** Slim JSON for one InteractionRequestDto-shaped message; omitted-null style. */
type Umi3dInteractionRequestJSON = Record<string, unknown>;

/** JSON-only; safe to stringify. */
interface ScriptInvocationPayload extends Record<string, unknown> {
  umi3d: Umi3dInteractionRequestJSON;
}

/** Returned transaction shape — normative schema TBD (section 4.2). */
type Umi3dTransactionJSON = Record<string, unknown>;

type ScriptHandler = (payload: ScriptInvocationPayload) => Umi3dTransactionJSON | Promise<Umi3dTransactionJSON> | void | Promise<void>;
```

**Return value:** the handler may **`return`** a **JSON transaction** (shape TBD, aligned with UMI3D DTOs / operations — section 4.2) for the runtime to **validate and apply** (browser JS or Unity C#).

**Multiple script files:** if section 2 declares several URIs, the runtime applies a **documented resolution rule**, e.g. (a) load all scripts into one registry and pick the first matching export name, (b) require authors to use a **single entry** module that re-exports all handlers, or (c) reserve an **optional** future field `scriptId` only if disambiguation is needed. The default story is: **one callback string per interaction**; authors ensure that name exists in the loaded script surface.

#### 3.2 Loader behaviour (conceptual)

1. Load script resources from section 2 (all `scripts[]` entries, or the single bundle policy your runtime defines).
2. Inject the **global host** (e.g. `GLTF`) into the JS scope before invoking handlers (section 4).
3. Build a **handler registry**: exported functions (and/or globals) keyed by name.
4. On interaction event, read **`interaction.callback`**; resolve `fn = registry[callback]` (or `module[callback]`).
5. Build **`payload`**: shallow clone of **`interaction.payload`** from the asset (or `{}`), then set **`payload.umi3d`** from the current browser request (slim JSON only).
6. Call **`fn(payload)`**; if the handler **returns** a value, treat it as a **transaction JSON** to validate and apply (section 4.2).
7. If **`callback`** is set but no function is found, treat as **runtime error** or **no-op** (policy TBD in the spec).

### 4. Common JavaScript API (global host, JSON transactions)

#### 4.1 Global host object (working name `GLTF`)

Expose a **single injected global** (e.g. **`GLTF`** on `globalThis`) implemented by each runtime. **Not** part of the glTF file and **not** part of `payload`. C# / Unity registers the same surface via the JS engine (`SetValue("GLTF", …)`).

```ts
// Normative *interface* (informative TypeScript)
interface InteractiveGltfHost {
  readonly apiVersion: "1.0.0";

  getObjectByUmi3dId(id: string): SceneObjectHandle | undefined;
  // Methods to build UMI3D-shaped transaction JSON, read state, etc. — TBD in spec.
}

interface SceneObjectHandle {
  readonly umi3dId: string;
  getLocalPosition(): { x: number; y: number; z: number };
  getWorldPosition(): { x: number; y: number; z: number };
  // setters / mutation helpers if allowed — TBD.
}
```

**Rules:**

- Scripts use **`GLTF`** (name TBD — **Open issues**) for **reading** current data and **constructing** transactions, not `THREE` or `UnityEngine` directly.
- **`payload.umi3d`** remains the **authoritative slim request JSON** for this invocation (section 3.1).
- **Ideal pipeline:** script **returns** JSON matching **UMI3D server / EDK** operation shapes where possible; **browser (JS or C#)** **validates** and **applies** using the same path as for **network-delivered** DTO updates (to be specified).

#### 4.2 Transactions (JSON aligned with UMI3D)

Handlers **return** **JSON-serializable** transaction objects (`Umi3dTransactionJSON`). The runtime:

1. **Validates** (allowlist of operation types, ids, etc.).
2. **Applies** locally (Unity / Three) using shared “apply DTO” logic where feasible.
3. Optionally **forwards** to the server unchanged or after merge — **policy TBD** (**Open issues**).

A **builder** on `GLTF` (e.g. `transaction.add(…)`) is optional sugar as long as the **final value is plain JSON** consumable by C# or JS apply paths.

#### 4.3 Worked example — Event: move a referenced object by +1 on local **Y**

**Scenario:** An **`Event`** interaction fires a script that must **translate** the object whose id is given in the **author `payload`** by **+1** unit along **local Y**. The object id is **`targetId`** (string, UMI3D entity id).

**Shared authoring (glTF interaction DTO, abbreviated):**

```jsonc
{
  "type": "Event",
  "name": "NudgeOnY",
  "callback": "moveTargetPlusOneLocalY",
  "payload": {
    "targetId": "9876543210"
  }
}
```

At runtime, **`payload`** passed to the handler also includes **`payload.umi3d`** (slim `EventTriggeredDto`-like JSON for this click). The script uses **`payload.targetId`** to know **which** object to move.

**A — Imperative host API** (mutation inside `GLTF`, little or no returned JSON):

```js
function moveTargetPlusOneLocalY(payload) {
  var id = payload.targetId;
  var obj = GLTF.getObjectByUmi3dId(id);
  if (!obj) return;
  obj.translateLocal(0, 1, 0);
}
```

**B — Declarative only** (return a JSON transaction; minimal reads):

```js
function moveTargetPlusOneLocalY(payload) {
  return {
    "version": 1,
    "operations": [
      {
        "kind": "transform.localDelta",
        "entityId": payload.targetId,
        "translation": { "x": 0, "y": 1, "z": 0 }
      }
    ]
  };
}
```

**C — Hybrid** (read via `GLTF`, then return UMI3D-shaped JSON):

```js
function moveTargetPlusOneLocalY(payload) {
  var id = payload.targetId;
  var obj = GLTF.getObjectByUmi3dId(id);
  if (!obj) return null;
  var p = obj.getLocalPosition();
  return GLTF.buildPropertyUpdateTransaction({
    entityId: id,
    localPosition: { x: p.x, y: p.y + 1, z: p.z }
  });
}
```

Illustrative JSON shapes and method names are **non-normative** until schemas and host API are fixed.

**Web (Three.js) vs Unity (C#):** the same script source can run in both if **`GLTF`** is implemented per platform and returned JSON is applied through a shared validation path.

### 5. Three.js (web) vs Unity (C#): making one script work

The **same source file** can run in both only if:

1. It **depends solely** on the **host API** and ECMAScript features supported by **both** engines, and  
2. Each runtime provides the global **`GLTF`** (or chosen name) host + **JSON transaction** apply path.

Unity options include embedded engines (**Jint**, **Jurassic**, **NiL.JS**), **V8**, or **WebView** (UMI3D already has WebView DTOs). **C# → JS:** `engine.Invoke(handlerName, payload)` after building `payload` per section 3.2. **JS → C#:** `engine.SetValue("GLTF", hostObject)`.

Document a **minimum ECMAScript profile** (e.g. ES2020 subset) for portability.

### 6. Security and packaging

- **Integrity:** optional `integrity` on script URIs; runtime may refuse to run without match.
- **Sandbox:** Jint/Jurassic can disable **file** and **network** from JS unless explicitly exposed on the host.
- **Web:** CSP and **no `eval`** of unauthenticated remote strings.

### 7. Summary of recommended artefacts

1. **`EXT_interactive_gltf_scripts`** (root): `scripts[]` with `id`, `uri`, `mimeType`, `kind`, optional `integrity`.  
2. **Interaction DTO** (in `extensions.umi3d` / interactive-gltf interaction object): optional **`callback`** (handler name); optional **`payload`** (JSON object of **fixed** author parameters, e.g. `targetId`).  
3. **Runtime invoke:** **`handler(payload)`** where **`payload`** is **JSON-only** (author keys + **`umi3d`** slim request).  
4. **Global host** (e.g. **`GLTF`**): scene queries, helpers to build transactions — **not** embedded in `payload`.  
5. **Return value:** optional **JSON transaction** aligned with UMI3D DTO / operation shapes; runtime **validates** and **applies** (JS or C# client).  
6. **Unity:** choose **one** JS strategy (embedded engine vs WebView); register the **same** `GLTF` surface from C#.  
7. **UMI3D alignment:** `payload.umi3d` and returned transactions align with **`InteractionRequestDto`** / EDK-style operations; omit nulls / useless defaults in slim JSON.
