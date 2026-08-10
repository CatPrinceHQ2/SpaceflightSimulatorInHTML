# Spaceflight Sim — Modding Example

This branch contains `SpaceModdingbyClaudeIn.HTML`, a modified copy of the
base game (`SpaceflightSimulatorIn_3_.HTML`) that demonstrates how easy the
game is to mod. Every change is tagged with a `MOD:` comment in the source,
so you can `grep -n "MOD:"` the file to find each one directly.

No engine/simulation code was touched. Every change is either a new data
entry in an existing array/object, or a small conditional hook into
existing render code that reads a new flag on that data.

## What changed

### 1. Two new planets + one new moon
Added to `PLANET_DEFS` (planets) and `MOON_DEFS` (moons):

- **Zorvath** — a purple gas giant far past Pluto (52.4 AU) on a wildly
  eccentric orbit (`eccMod: 0.18` → ecc ≈ 0.82), so it swings from a tight
  perihelion to a huge aphelion.
- **Kelbo** — a tiny, absurdly dense rock even further out (71.9 AU) with a
  surface gravity way out of proportion to its size — a "neutron pebble."
- **Glib** — a moon orbiting Zorvath, added to `MOON_DEFS` with
  `parent:'zorvath'`.

Planets and moons are entirely data-driven: orbits, gravity, spheres of
influence, rendering, and the navigation system all pick up new entries in
these arrays automatically. Nothing else needed to change.

### 2. Two new build-menu parts
Added to the `PARTS` object:

- **Ion Engine** — low thrust, very fuel-efficient.
- **Fuel Tank — XL** — a larger capacity stock tank variant.

Both reuse the existing `engine`/`tank` part `type`, so all the existing
thrust/fuel-burn/mass logic applies to them with zero simulation code
changes.

### 3. Yellow "new part" highlighting
Both new parts carry a `modded: true` flag. `buildPalette()` checks this
flag to:
- add a `.modded` class to the part card (gold border/background, see the
  `.part-card.modded` CSS rule — includes a dark-mode variant), and
- append a small yellow "NEW" badge next to the part name.

This makes it obvious at a glance which parts are stock and which are
modded, without touching any part-selection or build-grid logic.

### 4. Cosmetic labeling
The `<title>` and in-game header (`<h1>`) were updated to note this is the
modding example build, so it's not confused with the base game.

## How to make your own mod

Follow the same recipe:

1. **New celestial body** — add an entry to `PLANET_DEFS` (or `MOON_DEFS`
   with a `parent` matching an existing body's `key`). Pick any `distAU`,
   `radius`, `surfG`, `eccMod`, etc. — the physics and rendering derive
   everything else.
2. **New part** — add an entry to the `PARTS` object using one of the
   existing `type` values (`pod`, `tank`, `engine`, `chute`, `separator`,
   `dock`, `rcs`, `wheel`, `cone`) so existing gameplay logic applies to it.
3. **Flag it** — add any custom boolean/field to your new entry (like
   `modded: true`) and add a small conditional check wherever you want that
   flag to have a visible effect (e.g. in `buildPalette()`).

That's it — no changes to the physics, rendering pipeline, or save format
are required for either kind of addition.
