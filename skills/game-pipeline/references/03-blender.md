# 03 — Blender: everything from Python

Blender runs headless: `blender -b --factory-startup -P <script>.py -- <args>` from `blender/scripts/`.
No manual edits in the UI, ever: a fix is a code change plus a re-run. Each builder saves a `.blend`
library (for later scripts) AND exports glTF (for Godot) AND renders previews (for you to look at).

## Script layout (proven split)

| Script | Role |
|---|---|
| `lib_geo.py` | mesh builders (boxes, prisms, lofts, arches, roofs, panels, pipes), world-scale planar UVs, glTF export helpers |
| `lib_tex.py` (+ more `lib_tex*.py` as needed) | numpy-painted tileable textures + normal maps from height |
| `lib_<decals>.py` | emblems, signs, logos, markings drawn by code |
| `lib_render.py` | preview cameras, lights, mood switch (e.g. night emission), contact sheets |
| `lib_char.py`, `lib_gait.py` | character bodies, skeleton, skinning, poses, procedural walk/run with IK |
| `make_textures.py`, `make_char_textures.py` | write textures to `assets/textures/` |
| `build_architecture.py`, `build_props.py` | Task 1 assets → `blender/*.blend` + `assets/architecture|props/*.gltf` + previews |
| `build_characters.py` (+ `build_vehicles.py`) | Task 2 → `characters.blend` + `assets/characters/*.gltf` |
| `build_world.py` | Task 3 → level `.blend` + `assets/levels/<level>_layout.json` + renders per mood |
| `build_scene.py` | Task 4 → population + effects + `<level>_population.json` |
| `build_<area>.py` | run 2 areas |
| `build_<world>.py` | stitched world, top-down plan render for the map |
| `make_ui_assets.py` | UI frames, buttons, item icons |
| `sheet.py` | contact sheet of many renders: `blender -b -P sheet.py -- out.png 400 4 a.png b.png ...` |
| `style_check.py` | assets side by side in one scene, every mood, to judge consistency |

Give long builders `--no-render` and `--only name1,name2` flags: full preview renders can take 10+ min.
Document the exact rebuild order in README ("textures → architecture/props → characters → levels → world").

## Conventions (write them into decisions.md in Task 1, obey them everywhere)

- 1 unit = 1 m. Blender Z up → glTF/Godot Y up.
- **Front faces Blender −Y = Godot +Z** for structures, props, characters, vehicles AND markers (a
  marker faces where the character standing on it faces). Pivot = centre of the base on the ground.
- Node-name prefixes the game code relies on (adapt the kinds to the game):
  - `INT_<kind>_*` interactive parts with pivots where they move: doors and gates (pivot on the
    hinge), windows, switches, levers, shop counters, chests, terminals, breakables.
  - `MARK_<kind>` empties: where the player or an NPC stands or appears, entry/exit points, where a
    knocked-back body lands, where a vehicle parks, spawn and pickup points.
  - `ANIM_*` simple looped motions (rotating, swaying, spinning parts).
  - `LIGHT_*` point/spot lights (KHR_lights_punctual).
  - `*-colonly` simplified collision meshes → Godot makes StaticBody3D automatically.
  - characters: `<name>_body`, `<name>_rig`, `WEAPON_*`, `HELD_*`.
- Duplicate names inside one .blend get `.001`: rename temporarily during export so glTF names are clean.
- Mounted props (wall lamps, signs, screens): pivot on the mounting plane at ground level, mount height fixed.

## Assets

- **Real openings** where the player or the game passes through or looks through (doors, windows,
  hatches, vents): build the wall around them, with a little space behind so an open door shows a room,
  not a void. Far-away detail can be decals.
- **Textures painted with numpy**: tileable, baked seam shading (very readable at game distance),
  normal maps from height. Palette = style block.
- **World-scale planar UVs** per face → identical texel density on every asset; a `fit` mode for decals.
- **Export glTF Separate** (`.gltf` + `.bin`) with textures shared in `assets/textures/` — `.glb`
  duplicates the same textures into every file (e.g. 25 MB per building instead of one shared set).
- **Lights**: export RAW with a 1/30 multiplier (a 70 W Blender lamp → energy ≈ 2.3 in Godot);
  the default SPEC mode gives energies in the thousands.
- **Mood-dependent emission**: lit windows, screens, lamp glass carry emissive textures; the game turns
  emission on or off per mood. Fire and similar sources glow always.
- Variety by parameters: one generator function with colour, width, height, roof/top type, trim
  options yields many variants that still match.

## Characters (and other rigged things)

- One shared humanoid skeleton (≈20 bones, same names) for all humans and humanoid creatures; animals
  and vehicles get their own.
- Bodies from **lofts**: elliptical tubes along bones, so limbs and torso have no seams. Clothes,
  armour and gear are separate shells.
- **Skinning by code**: weight = 1/d^k to the allowed bones of each body part (≤3 bones per vertex).
  Blender's automatic heat weights are unreliable on non-intersecting shells.
- **Poses** as rotations about world axes in rest pose, converted to bone-local quaternions (bone roll
  doesn't matter). Animations are procedural functions (sine gait with knee lift, pelvis sway) and key
  poses with smoothstep; a foot-planted IK gait (`lib_gait.py`) removes foot sliding.
- Held items are modelled in the character's base pose and moved into rest pose via the bone matrix,
  so a weapon sits in the hand.
- **Each animation is a separate NLA track** → separate glTF animation with a short name; 24 fps.
  No root motion: store in `characters_list.md` the walk/run speed at which feet don't slide, and let
  Godot scale playback speed to the actual movement speed.
- Vehicles: separate nodes for wheels, steering parts, doors and turrets with pivots at their axes, so
  Godot can drive them by code instead of baked animation.
- Blender may crash on exit after big builds (`EXCEPTION_ACCESS_VIOLATION`): harmless if everything
  was saved/exported first — print a `DONE` line before exit and check for it.

## Levels (Task 3–4)

- Plan from the world map but **follow the location concept art** when they disagree; compress distances.
- Place instances of library collections, plus markers, areas, lighting presets (per-mood key values,
  fog, sky), and write `*_layout.json`: `instances` (asset, glTF path, group, pos, rot), `markers`
  (kind, pos, yaw, owner), `areas`/districts (name, rect, offset, fog), `water`, `blockers`, `kill_y`, `map`.
- Population JSON: who/what, marker, facing, `schedule` or trigger, behaviour, plus `fx` entries
  (smoke, fire, sparks) for Godot particles.
- Render each location in each mood from the same cameras as the concept art and compare side by side.
- For a stitched world: a reachability probe (walk a grid in Godot, render reachable cells) helps
  seal holes in the border; a top-down **orthographic plan render** is the basis of the in-game map.
