# 01 — Design doc, style block and art prompts

The design doc is the single source the whole pipeline grows from. Name it
`<Game title> — concept art and tasks.md` and keep it in the repo root. Sections:

1. **Pitch** (3–5 lines): genre, camera, setting, player character, opponents, core loop, key mechanics,
   any cycles (day/night, waves, seasons, races, rounds).
2. **Style block**: pasted at the start of EVERY image prompt so all art matches.
3. **Mood add-ons**: variants of light or atmosphere the game switches between (day/night, clean/
   infested, calm/alarm, dry/rain...).
4. **Prompts**: locations, empty locations, world map, characters/creatures/vehicles, UI screens.
5. **Task files**: the five task templates filled in for this game (see 02).

## Style block template

Name a reference era or games, the rendering style, what to avoid, the setting, the palette, the light,
the camera and the aspect ratio:

```
Game concept art in the style of <reference games/era>: <rendering style, e.g. semi-realistic,
hand-painted textures, slightly stylized proportions>, no <what to avoid>.
<Setting, time period>. Consistent palette: <4–6 named colours>. <Lighting>, readable silhouettes,
<camera: three-quarter view from above / eye level / top-down>, 16:9.
```

Examples of the first line for different games:
- dark fantasy RPG: `early-2000s European PC RPGs, semi-realistic, hand-painted textures, no cartoon, no photorealism`
- sci-fi shooter: `late-90s sci-fi shooters, chunky industrial forms, bold readable shapes, no photorealism`
- cozy platformer: `soft clay-toy look, rounded shapes, saturated pastel palette, no realism`
- post-apocalyptic survival: `gritty painterly realism, rust and dust, desaturated palette, no cartoon`
- arcade racer: `90s arcade racing, clean bright surfaces, strong sun, no photorealism`

Mood add-ons are one sentence each, e.g.
`Daytime, warm golden light, clear sky, lively atmosphere.` /
`Night, deep blue sky, warm light pools from lamps against cold shadows, mysterious but not horror.`

**Consistency trick:** once one image is great, attach it to every next request and write
"keep exactly the same art style as the reference image" (05 shows how to attach files).

## Prompt sets

### Locations (once per mood add-on)
Describe the landmarks, the ground, the main structures, what the player interacts with (doors that
look enterable, terminals, ledges, ramps, cover, pickups), set dressing, inhabitants and the exits
toward other areas. Make the vertical-slice location the most detailed.

```
<Location name>: <layout in one phrase>, <2–3 landmarks>, <main structures and what makes them
readable as interactive>, <props and set dressing>, <inhabitants/traffic>, <how paths lead out>.
```

### Empty locations (for building levels)
Same prompt + `Empty scene: no people, no animals, no vehicles, no loose items, only architecture,
terrain and fixed props.` These go to `concept/empty/` and drive task3.

### World map
```
Top-down stylized overview map of a compact <setting> game world: <hub> in the center, <area A> ...,
<area B> ..., connected by <roads/corridors/bridges>, all close together. Clear readable layout like a
game level map.
```
Later the *in-game* map is not this picture: it is repainted from a render of the real level (05).

### Characters, creatures, vehicles
Characters: add `full body character concept, front view and side view, neutral grey background,
character sheet`. Give age, build, face, hair, clothing or armour layers, colours, emblem, gear and
weapons. For creatures, give size relative to a human and any glow; for vehicles, add
`side, front and three-quarter view, orthographic-like` and state proportions and moving parts.
Cover the player character, each opponent type, background NPCs as one sheet, animals or vehicles.

### UI
Add `game UI design, <style> style, <materials of panels and frames>, <heading font style>, readable,
in the style of <reference> menus`. Screens: title screen (with the game title and menu items painted
in — it can be used directly as the menu background with invisible buttons), settings, HUD,
inventory/loadout, shop or upgrade screen if any, map, game over / death screen, pause.

## Worked example (Alchemists of Prague, third-person action RPG)

```
Game concept art in the style of early-2000s European PC RPGs like Gothic 1 and Gothic 2: semi-realistic,
hand-painted textures, grounded medieval look, slightly stylized proportions, no cartoon, no low-poly,
no photorealism. Medieval Prague, 14th century. Consistent palette: warm stone beige, terracotta roofs,
dark timber, muted greens and deep reds. Soft painterly lighting, readable silhouettes, isometric-ish
three-quarter view from above, 16:9.
```
Its locations were four districts (a market square, a bridge, a hillside quarter, a cliff fortress),
each painted by day, by night and empty; characters were the hero, three enemy types, townsfolk and a
horse; UI screens followed a parchment-and-iron look.

## Where things go

```
concept/locations/<place>_<mood>.png, world_map.png
concept/empty/<place>_empty.png
concept/characters/<name>.png
concept/ui/title_screen.png, settings.png, hud.png, inventory.png, ...
concept_art/   extra reference art ordered later (mood pieces, map repaints) — has .gdignore
```
