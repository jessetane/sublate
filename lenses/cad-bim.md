# Domain Lens: Parametric CAD & OpenBIM (FreeCAD)

This lens plugs into **Sublate** ([`SKILL.md`](../SKILL.md)) whenever the target deliverable is a parametric 3D model, architectural/structural design, OpenBIM assembly, or FreeCAD Python macro/addon (driven via the `freecad` MCP server or `.FCStd` / `.py` scripts). It orders the **7 Core CAD & BIM Principles** from highest conceptual geometry down to specific execution and MCP mechanics, and partitions them into role-specific injection blocks (`THESIS_LENS`, `ANTITHESIS_LENS`, `SYNTHESIS_LENS`).

---

## 1. The 7 Core CAD & BIM Principles (High → Low)

1. **Parametric Datum & Guide-Sketch Topology** *(Problem Framing — Highest Level)*  
   - *Question*: "Does this geometry flow from a clean master sketch or datum, or is it bolted together with fragile face attachments and magic coordinates?"  
   - *Rule*: Drive assemblies and repeating members from master guide sketches, axes, or datum planes rather than attaching sketches to generated B-Rep faces (which shatter under the Topological Naming Problem when upstream feature counts change).  
2. **Single Source of Dimensional Truth (Spreadsheet & VarSet Aliases)** *(System Architecture)*  
   - *Question*: "Why are there hardcoded millimeter literals inside sketches and placements?"  
   - *Rule*: Bind primary dimensions (spans, pitches, member widths, clearances, story elevations) to canonical `Spreadsheet` or `VarSet` (`<<global-vars>>`) aliases via FreeCAD expressions (`setExpression`). Changing one parameter must cleanly recompute the assembly without overlapping or detaching parts.  
3. **Link-Based Instantiation (`App::Link`) over Geometry Duplication** *(Assembly Structure)*  
   - *Question*: "Are we duplicating heavy B-Rep solids for every rafter, stud, or fastener?"  
   - *Rule*: Define one canonical base part and instantiate repeated framing, grids, and hardware via lightweight `App::Link` placements or parametric guide arrays (like `EdgeArray`), keeping file size small and edits instantaneous.  
4. **Fix the Base Sketch or Placement, Never Boolean-Patch Downstream** *(Geometry Data Flow)*  
   - *Question*: "Why are we adding a downstream `Part::Cut` or offset hack to trim an overlapping member instead of fixing the sketch or placement that produced it?"  
   - *Rule*: Never slap downstream boolean cuts, filler blocks, or manual placement tweaks onto a model to hide a misalignment. Trace the upstream sketch constraint, axis normal, or placement rotation that produced the bad geometry and fix it at the source.  
5. **Watertight Solids, Clash-Free Envelopes & Real Structural Loads** *(Geometric & Physical Integrity)*  
   - *Question*: "Is every solid valid (`shape.isValid()`, `shape.Volume > 0`), free of unintended intersections, and structurally realistic?"  
   - *Rule*: Every structural or fabricable member must resolve to a valid, closed manifold solid (`doc.recompute()` with zero error flags in `obj.State`) with zero unintended solid clashes (`shapeA.common(shapeB).Volume == 0`), verified empirically via B-Rep checks or CalculiX FEM analysis (`run_fem_analysis`) where load paths matter.  
6. **OpenBIM & IFC Semantic Hygiene** *(Spatial Hierarchy & Metadata)*  
   - *Question*: "Are spatial containers (`Site` → `Building` → `BuildingStorey` → `ElementAssembly`) and IFC semantics (`IfcType`, `PredefinedType`, `Material`) consistent?"  
   - *Rule*: Maintain a clean model tree with symmetrical, human-readable `Label` names, explicit `Material` links on physical solids (never on empty organizational groups), and accurate `IfcType` / `PredefinedType` classifications (`BEAM_GRID`, `RAFTER_GRID`, `TRUSS`, `ElementAssembly`).  
7. **Idempotent Transactions & Token-Conscious MCP Execution** *(Runtime & Tooling — Most Detailed)*  
   - *Question*: "Is this script wrapped in an undo transaction, safe for the GUI thread, and frugal with viewport screenshots?"  
   - *Rule*: Wrap document mutations in `doc.openTransaction(...)` / `doc.commitTransaction()` followed by `doc.recompute()`. Set `include_screenshot=False` during intermediate object creation/edits to avoid flooding context, and capture targeted multi-angle views (`Isometric`, `Front`, `Top`, `Right`) only at verification milestones. Offload crash-prone OCCT sweeps, lofts, or heavy multi-solid booleans to `execute_code_headless` (followed by `reload_document`) or `execute_code_async`.  

---

## 2. Role-Partitioned Injection Blocks

When dispatching subagents, Synthesis injects **only** the matching role block below into the prompt blueprint.

### A. Thesis Lens Injection (`THESIS_LENS`)

```text
DOMAIN LENS: PARAMETRIC CAD & OPENBIM (THESIS — BUILDER)
1. MASTER SKETCH & DATUM TOPOLOGY (#1): Drive geometry from master guide sketches and datum planes. Never attach dependent features to generated B-Rep face indices that break on recompute.
2. SPREADSHEET / VARSET EXPRESSIONS (#2): Bind key dimensions to `Spreadsheet` or `<<global-vars>>` aliases via `setExpression` rather than scattering magic numbers across sketches and placements.
3. LINK INSTANTIATION (#3): Use `App::Link` or parametric arrays (`EdgeArray`) for repeating structural members (studs, rafters, joists, fasteners) referencing a single canonical base solid.
4. FIX THE SOURCE SKETCH/PLACEMENT (#4): Never use downstream boolean cuts or filler solids to hide misalignments. Fix the upstream sketch constraint or placement matrix.
5. VALID SOLIDS & OPENBIM HYGIENE (#5, #6): Ensure every physical part has a valid solid (`Shape.isValid()`), assigned `Material`, clean `IfcType`/`PredefinedType`, and symmetrical tree `Label`.
6. TRANSACTION & MCP PROTOCOL (#7):
   - Wrap scripted edits in `doc.openTransaction()` / `doc.commitTransaction()` and call `doc.recompute()`.
   - Pass `include_screenshot=False` on intermediate `create_object` / `edit_object` / `execute_code` calls; capture screenshots only to verify completed milestones.
   - Use `execute_code_headless` + `reload_document` for heavy/risky OCCT booleans or sweeps.
   - Save a milestone snapshot (`.FCStd`) and end your report with `DOCUMENT_STATE`, `OBJECTS_TOUCHED`, `RECOMPUTE_AND_SOLID_CHECK`, and `KEY_DECISIONS`.
```

### B. Antithesis Lens Injection (`ANTITHESIS_LENS`)

```text
DOMAIN LENS: PARAMETRIC CAD & OPENBIM (ANTITHESIS — RED-TEAM)
1. EMPIRICAL INSPECTION & IMMUTABLE MODEL: You may run read-only inspection scripts via `execute_code` (with `include_screenshot=False` for data queries) or `get_view` / `get_objects` to inspect geometry, bounding boxes, and interferences, or run isolated checks in `execute_code_headless` on a temporary copy. You MUST NOT mutate or save over the active `.FCStd` deliverable.
2. B-REP, CLASH & CLEARANCE FALSIFICATION (#5 — MANDATORY PROOF):
   - Programmatically verify `obj.State` (no recompute errors), `obj.Shape.isValid()`, `obj.Shape.Solids`, and `obj.Shape.Volume > 0`.
   - Compute pairwise bounding-box and solid intersections (`s1.common(s2).Volume`) across structural/architectural members to prove physical clashes, floating/unseated members (gap > tolerance), or bearing failures.
   - Cite the exact `Object.Name` (`Label`), world coordinates / bounding box, intersection volume (mm³), and trace the upstream sketch constraint or placement formula that caused the clash.
3. PARAMETRIC PERTURBATION TEST (#1, #2): Check whether dimensions are hardcoded or properly bound via `ExpressionEngine`, and identify where changing a primary parameter (span, pitch, wall thickness) will break topology.
4. CHALLENGE OVER-COMPLICATED GEOMETRY (#1, #3, #4): Call out downstream boolean band-aids, duplicated heavy solids where `App::Link` belongs, or missing `Material` / `IfcType` metadata via Structural Provocations (`[P#]`).
```

### C. Synthesis Governance Rules (`SYNTHESIS_LENS`)

Synthesis applies these rules during **Setup** and **Beat 3**:

- **Datum, Unit & Parameter Lock (Setup)**: Before dispatching Thesis Iteration 1, inspect the active FreeCAD document (`list_documents`, `get_objects` with `include_screenshot=False`) to lock the coordinate convention (origin, story Z-levels, axis orientation), unit system, and global parameter sheet (`Spreadsheet` / `VarSet`).  
- **Root-Cause Parametric Fix over Boolean Patchwork (#4 at Beat 3)**: When Antithesis proves a clash, gap, or invalid solid, never instruct Thesis to boolean-cut the overlap away downstream. Issue a `[SUBLATE]` directive targeting the upstream sketch, placement formula, or datum offset.  
- **Fabrication & Construction Realism Filter (#5 at Beat 3)**: `[NEGATE]` pedantic sub-millimeter tolerances irrelevant to the material/fabrication method (e.g., timber framing vs. CNC aluminum) or unproved speculative issues, recording them in the Settled Ledger.  

---

## 3. Baseline & Snapshot Protocol (`.FCStd` & Scripts)

1. **Immutable `<base_anchor>`**:
   - At Setup, save a baseline copy of the active FreeCAD document (e.g., `<name>.base.FCStd` or a git commit if `.FCStd` / `.FCMacro` / `.py` generator scripts are tracked in git).  
2. **Iteration Verification**:
   - After each Thesis pass, ensure `doc.recompute()` reports zero touched/error objects (`[o.Name for o in doc.Objects if "Invalid" in o.State] == []`) and inspect orthographic + isometric views (`get_view`) before concluding convergence.  
3. **Clean Model Tree Curation**:
   - Before outputting `### STATUS: SUBLATED`, remove any temporary debug geometry, measurement probes, or unused construction objects, recompute, and save the final `.FCStd` document.  
