- **Easily Moddable** Just change numbers around. It does stuff. Claude wrote some of the code comments for me. Couldn't be bothered to do it myself.

**Song creds** - Nullify, New Beginnings belong to https://www.reddit.com/user/Gallium-Gonzollium/ Neurofunk Remastered, Neurofunk remix again (Mooneurofunk) goes to https://www.reddit.com/user/NewFall2020/, Frozen Planet (Signal Of Distress), Through The Star System goes to https://www.reddit.com/user/SthephanShi/, Night Vision goes to https://battleofthebits.com/barracks/Profile/absolutegalaxy/, and I lost the credits to the others (that's "Wander Through The Sky" and "Assembly Line" — if you know, tell me). PLEASE let me know if you own the bytebeat tracks for Tau Ceti's track or Build Area's track.

# It's spaceflight sim cuh

No building or dependencies. Just open the HTML in Google or whatever.

**~6,000 lines of vanilla HTML/CSS/JS in one file.** Canvas 2D rendering, a hand-rolled orbital mechanics engine, and a procedural bytebeat music system, all self-contained without need for internet. This allows bypassing of work or schooling internet blocks by creating a website in the browser with no actual website. It is all stored locally. And it's very compact, less than one megabyte.

Type 'unlockcheats' if you can't wait 10 minutes for it. (Or just actually play for 10 minutes the cheats panel legit unlocks itself once you've clocked enough playtime dawg... might save you from burning yourself out of the game in 5 seconds.)

## What it does

Spaceflight Sim has two screens:

- **Build screen**. snap parts (command pods, fuel tanks, engines including Quickburn variants and an Ion Engine, RCS thrusters, parachutes, separators, docking ports, wheels, landing legs, solar panels, ion storage, a fuel compressor, nose cones, fairings, and heat shields) onto a grid to design a rocket. Live stats update as you build: dry mass, fuel mass, total thrust, thrust-to-weight ratio, and an estimated Δv via the Tsiolkovsky rocket equation. You can't launch until the design has a pod, an engine, and TWR ≥ 1.
- **Flight screen** You then launch into a 9-planet solar system (Mercury through Pluto, orbiting a central sun) with 26 moons scattered around them (Earth's Moon, Mars' two, the big Jupiter and Saturn and Uranus and Neptune moons, even Pluto's Charon) and fly using my BS interpretation of two-body gravity, atmospheric drag, reentry heating, staging, docking, and time warp up to 1,000,000,000×. This is all coded somewhat crappily, but, it works and is fun to play. Just uh, ignore how Nav Beacon paths you to planets. I couldn't figure out the math.

## Core systems
- **Orbital mechanics** N-body-adjacent simulation where each ship is pulled by the gravity of whichever body currently dominates it through the function (`findDominantBody`), with planets themselves moving on fixed circle orbits, and moons orbiting their planet the same way (so gravity nests sun -> planet -> moon). If you want to edit that, go to the (`planetPositionAt`) variable. Distances, planet radii, and gravity are gameplay-tuned rather than physically accurate, this is game, not a real simulator, with each scaling factor (`GM_SUN_MULT`, `PLANET_GM_MULT`, `ORBIT_PERIOD_CALIBRATION_MULT`, `SOI_SCALE`, `MOON_SOI_MULT`) isolated so tuning one doesn't warp the others. Pluto and Charon are even set up as a lil binary pair (`binaryAnchorPos`) so they orbit a shared point instead of Charon just circling Pluto like every other moon. Because i noticed that most games don't do this and i wanted to be different (Am i turning into a 14 year old emo boy??)...

**Refactor** Under the hood the entire map now gets built through the same `registerBody`/`deriveOrbitingBody`/`buildPlanets`/`buildMoons` instead of each system having its own copy-pasted setup code, so adding a new planet or moon is mostly just adding one line to the list of existing stuff now. epic. also saved space big time

- **Adaptive time warp** up to 100,000,000×, automatically clamped near atmospheres/planets to avoid tunneling through terrain, with sub-stepped integration (`stepSimSeconds`) so high warp stays somewhat stable. The path the rocket takes is irreversibly changed by timewarp though. I also couldn't figure out the math to simplify rocket pathing math without changing how trajectories work, so, it's sort of garbage and can change your pathing but I guess it works.
- **Staging & fuel routing** fuel tanks feed engines through a BFS over physically touching parts. You can edit this through (`computeEngineConnections`); separators act as hard walls in that graph, so cutting a rocket with a separator genuinely isolates each stage's fuel pool. Seperators SHOULD delete the part of the rocket with the least amount of command pods. Its random if both sides have one. So put the command pods where you want to keep stuff.
- **Reentry heating & heat shields** parts build up heat during atmospheric flight based on drag and speed (`applyAeroHeating`), and can str8 up burn off (`burnOffPart`) if too many parts overheat at once and nothing's shielding them. Heat Shield parts (small/medium/large) and the Fuel Compressor block a big chunk of incoming heat (`heatBlock`) for whatever's stacked behind them, and every part now has its own baseline `heatResist`. There's an "Indestructible" cheat if you'd rather not deal with any of that realistic re-entry aah stuff.
- **Docking** ships with docking ports can merge in flight through function (`mergeShips`) when two ports meet face-to-face within a snap radius, re-parenting one ship's parts onto the other's grid. This is janky as crap and there isn't a good way to navigate to another rocket. YET. This is beta.
- **Nav Beacon autopilot (cheat)** — given a target planet, computes a minimum-Δv intercept window (`findBestInterceptWindow`), draws a burn marker and predicted path. The math is so confusing that i lowkey just made it give you velocity in the direction of the planet so the path looks stupid. Thats why it's a cheat. It's insanely hard to path to planets without this because you actually have to find the path which even the game's math cannot.
- **Asteroids** randomly spawned rocks (`spawnAsteroidAt`) show up around planets and moons, each with their own negligible amount of gravity, SOI, and landable surface, just like a mini moon. But they are weirdly shaped. Noncircular, i mean. They share the moon soundtrack since they're basically moon SOI's weird little cousin.
- **Procedural soundtrack**. nine bytebeat tracks, synthesized sample-by-sample inside an `AudioWorklet`, crossfading based on where the active ship is: Nullify (interplanetary coasting), New Beginnings (in a planet or moon's SOI), Neurofunk Remastered (atmospheric flight), Mooneurofunk (specifically inside a moon's or asteroid's SOI), Through The Star System (deep interstellar space with no dominant body), Night Vision (around Alpha Centauri A or B), Wander Through The Sky (around Tau Ceti), Signal of Distress (around Gliese 876), and Assembly Line (build screen). Allows for ultra compressed audio in just text.
- **Persistence** active ships and saved rocket blueprints are stored in `localStorage`, so a session SHOULD survive a page reload. Sometimes this can get corrupt. Especially across updates. Careful.

- **Multi-ship management** fly and switch between multiple simultaneously-active ships, each independently simulated.

- **Ion power!!!** Solar Panels multiply charging in new Ion Storages (`chargeIonStorage`), and the charge rate depends how close you are to stars. It's fast near a star's surface, basically forever near SOI edge. Basically i made it 1 day at Mercury and 1 year at Pluto, exponentially greater closer than Mercury and logarithmically worse past Pluto. It uses the function `ionChargeTimeForStar`). The Ion Engine (`ionOnly`) doesn't use normal fuel. at all. It draws straight from Ion Storage instead of a fuel tank. There's literally a wire connecting them when you place a ion engine and storage.

- **Interstellar travel** There are extra star systems beyond the Sun, alpha centauri reuses the same binary-pair hooks (`binaryAnchorPos`/`binaryPairPosition`) as Pluto and Charon.

  - **Alpha Centauri** A and B orbiting their shared barycenter way off from the Sun, plus Proxima Centauri (with 2 planets, Proxima b and Proxima d) further out orbiting that same barycenter. This is where "Night Vision" plays. It's a banger and ofc it slaps.
  
  - **Tau Ceti** got 4 planets, its own gravity tuning, and its own track ("Wander Through The Sky").
  - **Gliese 876** 4 planets (Gliese d with a FAT ass atmosphere, so it gets its own warp cap so you don't have to sit for literal minutes to through it), with the track "Signal of Distress".

## Controls

| Action | Input |
|---|---|
| Place / remove part | Click / right-click (build screen) |
| Rotate part | `R` (build screen) |
| Pan / zoom | Drag / scroll |
| Throttle, staging, chutes | On-screen HUD controls |
| Warp up / down | `Arrow Up` / `T` and `Arrow Down` / `G` |
| Rotate ship (in flight) | `Arrow Left/Right` or `Q` / `E` |
| Drive while landed | `A` / `D` (requires wheels) |
| RCS strafe | `WASD` (requires RCS thrusters) |
| Toggle engine on/off | `Space` |
| Free camera (pan around without following ship) | `F` to toggle, `Home` to snap back to ship |

A **Cheats** panel (build and flight) exposes developer/debug tools: infinite fuel, mouse-pull thrust, camera rotation lock, the Nav Beacon autopilot, direct stat editing (speed/altitude/fuel/throttle/warp), noclip part placement, an expanded build grid, and an Indestructible toggle that shuts off both reentry burn-off and hard-landing damage. These should all work relatively well except Nav Beacon LOL

## Tech notes

- Pure client-side vanilla JS. Literally just download the HTML and run it. No stupid BS with installation or whatever.
- Rendering is two `<canvas>` elements (build grid and flight view) via 2D context, with a DPR cap for performance on high-density displays.
- CSS custom properties drive a light/dark theme toggle.
- Ship visuals are rasterized once per design into an offscreen sprite canvas rather than redrawn part-by-part every frame.

## Running it

No terminal bs just download the html and double click it in your version of File Explorer. Might be something else if you are on Mac or Linux but doesnt matter. This is not OS specific or anything. It's the most accessible thing on the planet.

No build tooling required... Actually i'm wrong. Claude said that a local static server is only needed if your browser restricts `AudioWorklet` or `localStorage` under the `file://` origin e.g. `npx serve .`.)

## Status

This is a from-scratch personal/hobby project. There's no package.json, test suite, or CI. Code comments throughout should explain pretty damn well the non-obvious tuning decisions (constant scaling, warp-drift correction, UI layout dependencies). It was active iteration by me with some claude polish. Claude said that some good next steps for open-sourcing this would be to split the single HTML file into modules (parts data, physics, rendering, UI), add a LICENSE, and add a screenshot/GIF to this README file. I'm not going to do any of that because I am lazy. Actually i'll add the screenshot, that's sort of smart.

<img width="1872" height="1004" alt="Screenshot_2026-08-08_22-03-01" src="https://github.com/user-attachments/assets/ebe55e0b-594a-4d96-87e4-34e9e4643aa6" />
<img width="1872" height="1004" alt="Screenshot_2026-08-08_22-02-56" src="https://github.com/user-attachments/assets/de386367-e24d-4fcd-aaaf-aa7882174382" />
