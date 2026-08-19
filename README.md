# Spaceflight Sim (SFSiHTML)

This is a SINGULAR file. The whole game sits in the single file 'SpaceflightSimulatorIn.HTML'. Real simple.
Spaceflight is compressed is the compressed version of this. Wow.

Current version as of readme update: **vR1.6.0**

## Running it

Open in browser on any modern os using.. whatever your version of file explorer is. It's the most compatible thing on the whole damn planet. No server, no install, no npm, and certainly no BS. Progress (saved rockets, settings, procedural galaxy state) is stored in the browser via `localStorage`/exported save strings, so it persists between sessions on the same browser/device. 

The game is designed to work on enrolled school chormebooks by creating a mostly unblockable webpage (file://). Also doesn't require any installing (which would usually be blocked)

## What it does

- **Build**: You build your rocket on the grid and it gets sent out to the launchpad when you are ready. Symmetry and drag placing exist for easy building. All parts are rotatable.


Here's an example of a capable rocket:

<img width="380" height="544" alt="Screenshot_2026-08-13_18-13-27" src="https://github.com/user-attachments/assets/e1244f78-b976-4f0f-a3a4-fdecb9ef618c" />


- **Flight**: You fly your rocket through the solar system, or other solar systems, or through the galaxy if you're ambitious. If you go to enough systems, you may find a rare epic or legendary system. This is foreshadowing.
- **Solar system + procedural galaxy**: Essentially, turn on **Procedural Planets** from the main menu to generate a seeded, effectively infinite galaxy of systems on top of the hardcoded existing real-life systems.
Here's the full galaxy render (870K+ stars in view, 120K off screen). These are deterministic always. Any star you see here you can generate yourself.

<img width="4096" height="4096" alt="galaxy" src="https://github.com/user-attachments/assets/315d12c5-b3f3-4c3b-bcff-2ce0c6920748" />

Star systems from the procedural set can be unusual, rare, epic, and legendary. 
A legendary star system can look like this, or even more complex:

<img width="982" height="753" alt="Screenshot_2026-08-16_20-17-02" src="https://github.com/user-attachments/assets/03a417ac-5f58-43f8-a3c0-2d387390c86d" />


- **Saves**: The game saves and loads files, you can copy your save and share it, and do a bunch of funky stuff. If you can decode it I guess you earn the rite of passage to edit the save data.

- **Cheats & dev tools**: If you stay 10 minutes (or type 'unlockcheats' - if u do this you're a loser!!! Just kidding. But it might burn you out of the game real quick.) you will unlock cheats which allow you to, well, cheat. They, of course, invalidate your mission. Except maybe Rotate Camera. That's reasonable.

## Controls

**Build screen**
| Action | Control |
|---|---|
| Place part | Click |
| Remove part | Right-click |
| Rotate hovered/placed part 90° | `R` |
| Pan | Drag |
| Zoom | Scroll wheel |

**Flight screen**
| Action | Control |
|---|---|
| Toggle main engine | `Space` |
| Rotate ship | `←`/`Q` (counter-clockwise), `→`/`E` (clockwise) |
| RCS translate | `W`/`A`/`S`/`D` (requires RCS parts + fuel) |
| Toggle free camera | `F` |
| Reset camera | `Home` |
| Time warp up / down | `↑`/`T`, `↓`/`G` |
| Quicksave / quickload (cheat) | `H` / `Y` |
| Staging, parachute deploy, docking | UI buttons/panels (no dedicated keys) |

I use a floating panel design for most GUI elements. Press ctrl (-/+) to change their scale.

## Structure of the file

It's all one HTML document:
- `<style>` would be the layout and theming for pretty much all GUI you see.
- `<script>` is a giant IIFE which contains the entire game - so that means everything we previously discussed:
  - Part definitions and ship/part-placement logic,
  - Save/load, export/import, and procedural-galaxy generation,
  - The physics loop (`integrate()`), engine/RCS firing, gravity, drag, heating, landing/crash/docking checks, more..
  - Rendering for both the build grid and the flight canvas,
  - UI wiring (buttons, panels, keyboard shortcuts) and a hidden dev-tool/profiler overlay. If you can decode the button combo to unlock it you have earned the right to use it.

## Known limitations / notes

- Single file by design — The JS is compact and stuff. This is because it was specifically designed to run on a Lenovo 300e Gen 2 school enrolled chromebook.
Performance on such a device (Integrated Mediatek processor from 7 years ago, 4gb ddr3 1866mhz)
60fps solid with 1000 procedural systems loaded. 14ms frametime. Yep. It's real.

- Dev tools are gated behind easter-egg key sequences rather than menu items; see the code near `DEV_COMBO` if you're looking for it. (also why i said if you can decode the button combo you can use it)

## Credits

Made by CatPrinceHQ. Some modded features that made it into the main game are by KampfGuy.

**Songs** Nullify, New Beginnings belong to https://www.reddit.com/user/Gallium-Gonzollium/ Neurofunk Remastered, Neurofunk remix again (Mooneurofunk) goes to https://www.reddit.com/user/NewFall2020/, Frozen Planet (Signal Of Distress), Through The Star System goes to https://www.reddit.com/user/SthephanShi/, Night Vision goes to https://battleofthebits.com/barracks/Profile/absolutegalaxy/, Niarix Visions (Sight of the Radiant Shine) by https://www.reddit.com/user/lhphr/ and I lost the credits to the others (that's about six of them). PLEASE let me know if you own those tracks. I sincerely apologize in advance for my incompotence in tracking which ones i used and didn't use. They are all located on Dollchan though.

## More pics?
Yes. Here

The black hole in the center of the Galaxy!!

<img width="584" height="533" alt="Screenshot_2026-08-18_18-59-45" src="https://github.com/user-attachments/assets/f3c01369-bb5b-4815-a0a8-88500b71dbc9" />

The galaxy, render of ~20K stars ingame:

<img width="982" height="753" alt="Screenshot_2026-08-16_19-23-18" src="https://github.com/user-attachments/assets/957785d4-c919-47c4-a768-97e458481aa9" />
