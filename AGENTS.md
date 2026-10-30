# Pixel Space Background Generator: agent entry point

Scope: entire repository. This is an installable **Godot 4.7** procedural 2D
background addon. CI pins **4.7.2-stable**, Compatibility renderer.
Only `addons/pixel_space_background_generator/` is required in a host project.
Decorative backgrounds do not own gameplay entities, physics or a camera controller.

## Establish current behavior before editing

Inspect the authorized branch/HEAD, user changes, addon `plugin.cfg`,
`README.md`/`README_ES.md` and `.github/workflows/godot-ci.yml`.
The README contains historical incremental sections: the current infinite mode
streams **space objects across X and Y**, not the retired terrestrial silhouettes.
Confirm claims in current source/tests; do not copy stale snippets into new APIs.

| Task | Start under `addons/pixel_space_background_generator/` | Proof |
| --- | --- | --- |
| Configuration, generation, visual refresh, export | `src/pixel_space_background_generator.gd` and matching scene | `tests/regression.gd`; actual exported image/layers |
| Stars, planets, composition and color | `src/components/`, `src/color_schemes/` | Seeded comparisons, isolated materials and live renderer |
| Infinite signed X/Y streaming | `src/components/space_infinite_parallax.gd`, `space_parallax_chunk.gd` | Long/reverse travel, resize, bounded chunks and sky coverage |
| Host-facing demonstrations | Root `demo/space_lab.tscn`, `demo/parallax_preview.tscn`, `demo/infinite_parallax_preview.tscn` | Interactive camera/viewport/export checks |

Record acceptance, existing owner, reusable mechanism, smallest missing behavior,
compatibility and focused evidence. Read immediate callers and tests via narrow
searches. Use native Godot/current helpers before adding an abstraction; new shared
mechanisms need two independent consumer scenarios.

## Keep these contracts

- Instantiate the supplied generator scene: its script expects existing layer
  nodes and a nonzero Control size. Do not make root demos hidden dependencies.
  Preserve public Resource fields, enum IDs, defaults, method signatures and UIDs.
- Keep local RNG and same-config/seed repeatability. `generate_new()` and
  `regenerate_current()` have different seed intent; inspect before calling.
  Shader pixel identity across different drivers is not guaranteed.
- Structural changes rebuild; visual-only changes use `refresh_visuals()` and
  preserve generated node identities and RNG progression. Keep deferred preview
  coalescing/debounce; do not rebuild geometry every frame or every slider tick.
- Materials/palettes must not share writable per-instance state. Generated
  StarsContainer/PlanetContainer children are transient; keep authored content out.
  Preserve host clear color, InputMap, main scene and renderer settings.
- `set_parallax_offset()` is immediate; `follow_parallax_target()` is smoothed
  with frame-rate-independent behavior. Preserve the independent faux-3D and
  infinite-2D paths.
- Batched stars are opt-in. `star_objects` is empty in that mode; use
  `get_generated_star_count()` for both modes. Preserve downstream seeded planet
  results when changing star representation. CPU build time is not GPU frame time.
- Infinite streaming must remain deterministic in signed X/Y with bounded nearby
  chunks and coherent world-sampled seams. The sky is viewport-local and covers
  the viewport after long positive/negative travel and resize; do not mask coverage
  bugs by changing global clear color. Decorative objects are not colliders.
- `await export_png()` returns an Error, requires an existing parent directory,
  a live renderer and dimensions within 1–4096. Under headless it reports
  `ERR_UNAVAILABLE`; do not hang or claim a valid rendered export.
- Isolated export excludes editor/game UI and preserves the current seed/result.
  Layer/bundle export creates output directories and keeps five transparent
  full-canvas layers, composite and manifest coherent. Flattened stars lose
  individual depth velocity; bundles reproduce coarse layer factors, not that
  full live behavior. Infinite worlds need explicit finite capture bounds.
- Keep classic/opt-in visual defaults and license/Deep-Fold provenance. Avoid
  replacing assets, adding host-game policy or introducing global managers.

## Verify progressively and report honestly

From the repository root:

```sh
godot --version
python3 scripts/run_tests.py --godot godot
python3 scripts/run_tests.py --godot godot --render
```

The render pass requires a display and real Compatibility backend; use the current
workflow for virtual-display setup. The runner, `tests/load_scripts.gd` and
`tests/regression.gd` define current coverage. Headless import and image existence
are not visual/export-quality proof. Docs-only changes need path/link review.

For camera/streaming work, run both parallax demos, travel beyond X=14,415,
reverse into negative X/Y, resize and verify no clear-color gaps or leaked chunks.
For export/shader work, inspect rendered PNG, transparent layers and JSON on the
same seed/settings; test failure returns, hidden layers and instance isolation.
For lifecycle changes, exercise repeated generation, pending/deferred work,
teardown and plugin disable/re-enable. Package changes require an addon-only host.

Trace actual scene nodes, signal ownership and cleanup; a searched name is not
integration proof. Review API/default/save compatibility against the starting
revision, document migrations when required and keep both README languages aligned.
Report exact executed checks and unverified renderer/device paths. Benchmark on
named hardware before claiming performance gains; never report speculative FPS
or agent effectiveness. Publish only as authorized, protecting concurrent main
changes and never force-pushing.
