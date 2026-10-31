# Lens: Parametric CAD & OpenBIM (FreeCAD)

Example domain lens for parametric 3D modeling, architectural/structural design, OpenBIM assemblies and FreeCAD Python scripts/macros (via the `freecad` MCP server or `.FCStd` / `.py` files).

## Shared Principles (High → Low)

1. **Master Sketch & Datum Topology**: Drive assemblies and repeating members from master guide sketches, axes or datum planes rather than attaching features to generated B-Rep face indices (which shatter under the Topological Naming Problem when upstream geometry changes).
2. **Single Source of Dimensional Truth**: Bind primary dimensions (spans, pitches, member widths, clearances, story elevations) to canonical `Spreadsheet` or `VarSet` (`<<global-vars>>`) aliases via FreeCAD expressions (`setExpression`). Changing one parameter must cleanly recompute the model without detaching or overlapping parts.
3. **Link Instantiation over Solid Duplication**: Define one canonical base solid and instantiate repeated framing, grids and hardware via lightweight `App::Link` placements or parametric arrays (`EdgeArray`), keeping file size small and edits instantaneous.
4. **Fix the Source Sketch or Placement, Never Boolean-Patch Downstream**: Never slap downstream boolean cuts (`Part::Cut`), filler blocks or manual offset hacks onto a model to hide a misalignment. Trace the upstream sketch constraint, axis normal or placement matrix that produced the bad geometry and fix it at the source.
5. **Watertight Solids & Clash-Free Envelopes**: Every physical member must resolve to a valid, closed manifold solid (`Shape.isValid()`, `Shape.Volume > 0`, zero error flags in `obj.State` after `doc.recompute()`) with zero unintended solid intersections (`s1.common(s2).Volume == 0`).
6. **OpenBIM & IFC Semantic Hygiene**: Maintain a symmetrical, human-readable model tree (`Site` → `Building` → `BuildingStorey` → `ElementAssembly`), explicit `Material` links on physical solids (never on empty organizational groups) and accurate `IfcType` / `PredefinedType` metadata.
7. **Idempotent Transactions & Token-Frugal MCP Execution**: Wrap scripted mutations in `doc.openTransaction(...)` / `doc.commitTransaction()` followed by `doc.recompute()`. Pass `include_screenshot=False` during intermediate edits and capture targeted multi-angle views (`Isometric`, `Front`, `Top`, `Right`) only at verification milestones. Use `execute_code_headless` + `reload_document` for heavy OCCT booleans or sweeps.

## Role Dynamics

### Thesis

- Execute modeling tasks inside clean transactions, recompute the document and save a milestone snapshot (`.FCStd`).  
- In your report, list touched objects and document state under `CHANGES`, recompute/solid/clash check results under `VERIFICATION`, and topology choices under `KEY_DECISIONS`.  

### Antithesis

- Run read-only inspection queries (`execute_code` with `include_screenshot=False`, `get_objects`, `get_view` or isolated checks on a temporary copy) without mutating or saving over the active `.FCStd` deliverable.  
- On continuity turns, inspect newly modified objects (`<last_reviewed_state>..current`) first, then verify full-assembly solid validity, clearances and `ExpressionEngine` bindings against `<base_anchor>`.  
- Cite exact `Object.Name` (`Label`), world coordinates, clash volume (mm³) and the upstream sketch constraint or placement formula responsible for every `[F#]`.  

### Synthesis

- **Baseline (`<base_anchor>`)**: Save a baseline snapshot (`<name>.base.FCStd` or git commit) and lock coordinate conventions, units and global parameter sheets at Setup.  
- **Realism Filter & Tree Curation**: `[NEGATE]` sub-millimeter tolerances irrelevant to the target fabrication method (e.g. timber framing vs. CNC machining). On convergence, remove temporary debug/measurement probes, recompute and save the final `.FCStd` document.  
