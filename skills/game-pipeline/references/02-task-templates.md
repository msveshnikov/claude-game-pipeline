# 02 — The five task files

Each task is a markdown file run by one autonomous Claude Code session ("do task3.md"). Run them
strictly in order. **Run 1 = vertical slice** (one level or area, the most important one). Review it, fix
the task texts, then **run 2** = the same five tasks for the remaining areas, then (for open worlds)
stitch one world.

Fill every `<…>` with the game's own terms. Keep the step structure; drop steps the genre doesn't need
(e.g. no merchants in a racer, no vehicles in a dungeon crawler) and add genre-specific ones.

Paste these two blocks at the top of every task file.

## Block A — autonomous mode

```markdown
## Mode: fully autonomous
- Don't ask questions or wait for confirmation. Make every decision yourself and record it in decisions.md.
- If something is unclear in the concept art, choose a reasonable option in the target style and continue.
- Don't wait for Blender MCP, Godot MCP or other tools to connect. Work with what is there: run Blender
  from the command line with Python scripts (blender -b --factory-startup -P script.py).
- If a step fails, try another approach. If that fails too, record the problem in issues.md and move on.
- Don't stop until the task is complete. At the end write a short report in report_taskN.md:
  what was done, what did not work.
- Look at your results (preview renders, contact sheets, screenshots) and compare with the concept art
  before calling anything done.
```

## Block B — external assets

```markdown
## External assets
- Build all models and textures yourself in Blender with Python scripts. No paid 3D generation services.
- No terrain plugins: build terrain, hills and cliffs in Blender with code.
- Sounds and music: search and download free ones (<footsteps, weapons, engines, ambience, impacts,
  UI clicks, music for menu and action>).
- Animations: make them in Blender first. Only if they still look bad after several attempts (foot
  sliding, jerks, unnatural poses), find a free one and retarget it to your rigs.
- Only free assets with open licences (CC0, CC-BY): Freesound, OpenGameArt, Kenney, Quaternius, Sonniss
  GDC bundles. Godot Asset Library (godotengine.org/asset-library/api) for water/sky shaders, weather,
  particles, controllers — check Godot version compatibility.
- If a site needs a login, skip it and use another source; don't wait for the user.
- Record every downloaded asset in assets/CREDITS.md: file, source, author, licence, link.
```

## task1.md — environment and props (Blender)

```markdown
# Task 1: Environment and props in Blender
## Goal
From the location concept art, build 3D models of the environment and props for <Game>
(<style in one line from the style block>).
## Inputs
- concept/locations/ (all mood variants), concept/locations/world_map.png
## Steps
1. Study the art. Write props_list.md per location: <structures, terrain pieces, doors/gates,
   interactive objects, cover, containers, lights, signage, set dressing>.
2. Split into static architecture/terrain, small props, and interactive objects
   (<doors, switches, pickups, breakables, shops, spawners — whatever the game's verbs touch>).
3. Model each as a separate asset in Blender, one shared scale (1 unit = 1 m).
4. Make textures in one style and the palette of the style block.
5. Light-emitting objects get light sources + emissive materials for the dark mood variant.
6. Add simplified collision meshes and node-name prefixes per the conventions (03).
7. Export to assets/architecture/ and assets/props/ (glTF), with preview renders of every asset.
## Result
props_list.md, .blend libraries, glTF exports, preview renders, conventions written into decisions.md.
```

## task2.md — characters, creatures, vehicles

```markdown
# Task 2: Characters <and creatures / vehicles>
## Goal
3D models of everything that moves, from concept/characters/, in the same style and scale as Task 1.
## Steps
1. Write characters_list.md with size and key details of each.
2. Model them (humans ~1.8 m; others per the art).
3. Rig and animate what the game needs: <idle, walk, run, attack, hit, death; player extras such as
   jump, dodge, aim, reload, block, climb; NPC actions; vehicle wheels, suspension, doors, turrets>.
4. Emissive details (eyes, screens, lights, magic, engines).
5. Export to assets/characters/ (glTF with animations), preview renders and contact sheets.
## Result
characters_list.md (incl. the walk/run speed at which feet don't slide), rigged animated models, renders.
```

## task3.md — empty levels

```markdown
# Task 3: Empty levels
## Goal
Assemble from Task 1 assets the empty level(s) without characters, matching concept/empty/ and the
world map.
## Steps
1. Plan the layout from the world map: compress distances for play, design the routes the player
   takes (<streets, corridors, tracks, platforms, arenas>), with shortcuts and landmarks for orientation.
2. Place architecture, terrain and static props; the slice area is the most detailed.
3. Place empty markers for gameplay: <player start, spawns, entrances, interactables, cover points,
   checkpoints, pickups, NPC posts, objective locations>.
4. Lighting presets for every mood variant, and a smooth transition if the game switches between them.
5. Render every location in every mood and compare with the concept art.
6. Write the level for the engine as JSON (assets/levels/<level>_layout.json: instances, markers,
   areas, lighting presets, map info).
## Result
Level .blend, layout JSON, renders.
```

## task4.md — population

```markdown
# Task 4: Bring the level to life
## Steps
1. Place NPCs on Task 3 markers (<who stands where and does what>).
2. Place opponents (<types, groups, difficulty curve, when they appear>).
3. Put the player at the start point; place pickups and objectives.
4. Effects: <smoke, fire, water, dust, sparks, flags, moving machinery, traffic>.
5. Check every location in every mood against the art; fix style mismatches.
6. Export for the engine: assets/levels/<level>_population.json (who/what, where, facing, schedule or
   trigger, behaviour), describe the file structure in README.md.
## Result
Full scene, renders, population JSON, README.md.
```

## task5.md — the game in Godot

```markdown
# Task 5: Game logic and UI in Godot 4
## Steps
1. Import world, characters, animations. Check scale, collisions, materials, lights. Build the level at
   runtime from the Task 3/4 JSON.
2. Player control: <camera type>, movement (<walk, run, jump, crouch, drive...>), interaction.
3. Core verbs: <combat / shooting / platforming / driving / building>, with feedback (hit reactions,
   sound, camera shake) and failure (damage, death, crash, time out).
4. Opponent AI per type (<behaviours that make each type play differently>).
5. Interactive objects from the markers (<doors, switches, pickups, shops, vehicles>).
6. Progression and economy: <currency, upgrades, unlocks, score, objectives>.
7. Cycles if any: <day/night, waves, laps, rounds> and what changes with them.
8. UI in the style of concept/ui: title screen, settings (graphics, sound, controls), HUD, <inventory /
   loadout / shop>, map, game over, pause.
9. Sound: <footsteps, actions, ambience, music for menu and action>.
10. Save/load (or checkpoints).
11. An --autotest mode that plays through everything and writes screenshots + a log.
12. Export Windows (+ Web and Android with touch controls), play it, fix bugs, ship (06).
## Result
Playable builds, Godot project, README.md with controls and rebuild steps, report_task5.md.
```

## Run 2 and the stitched world

For each further area, copy the five tasks with "Run 2 — <areas>" in the title, list exactly which new
assets/characters/levels are needed, and require reuse of the run-1 libraries and conventions.
For an open world, add a "one world" task: a world builder script that places all area layouts with
offsets, connects them, seals the borders, names the areas for the HUD, streams NPCs by distance,
renders a top-down plan for the map, and writes one `<world>_layout.json` + `<world>_population.json`.
For a level-based game, instead add a level list and a level-select/progression screen.
