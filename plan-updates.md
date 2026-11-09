# Neon Arena — planned updates

Every new number goes in `CONFIG` so it can be tuned later (and from the debug panel once it exists).

## 1. Debug panel ✅ done
(Entry ended up as: tap PAUSED 5 times, ` key, or controller Y on the pause screen. Midway-boss cheat comes with item 4.)
Tune the game on the phone without editing code.

- Hidden entry: tap the timer 5 times on the pause screen (or press the backtick key on PC)
- Sliders for the main numbers: tap cooldown, reload delay, reload time, damage, spawn rate, enemy HP scaling, enemy speed, XP curve, run length
- Cheat buttons for testing: +1 level, spawn elite, spawn midway boss, spawn final boss, skip 1 minute, fill HP, god mode
- Changes apply live to the current run
- "Copy config" button that shows the changed values as text, so they can be pasted back into `CONFIG`
- "Reset" button to go back to the defaults
- Works with touch, mouse and controller

## 2. Sound pass ✅ done
(Volumes are cycle buttons in Options/pause: MUSIC and SFX in 20% steps. Layers: pad + hats from start, bass 0:45, kick 2:00, arp 4:00, snare 6:00. Tune in `CONFIG.audio`.)
Replace the placeholder beeps.

- Background music: a looping synthwave track generated in code (bass line, arpeggio, drums), no audio files
- Music builds in layers as the run goes on, then switches to a boss track
- Separate volume sliders for music and sound effects in Options
- Richer effects: layered shots, distinct sounds per enemy death, a reload click-clack, low-HP heartbeat, level-up chime, boss warning siren
- Last Round shot and crits get their own punchier sound
- Limit how many of the same sound can play at once so big fights don't get noisy

## 3. Evolutions ✅ done
(Debug panel has a READY EVOLUTION cheat that maxes a random pair so you can test them.)
Maxing two related upgrades unlocks a powered-up version on the next level-up.

| Evolution | Requires | Effect |
|---|---|---|
| Chain Shot | Split Shot + Ricochet | Every bounce splits into 2 shots |
| Railstorm | Phase Rounds + Railgun Coil | Shots pierce everything and leave a damaging trail |
| Executioner | Last Round + Hot Rounds | Last round always crits and explodes on impact |
| Overflow | Extended Mag + Fresh Mag | A full reload fires an automatic burst ring |
| Blade Storm | Orbitals + Pulse Nova | Orbitals grow, spin faster and release a nova when they hit |
| Juggernaut | Plating + Nanites | Big HP bonus and brief invulnerability when hit below 30% HP |

- Evolution cards look different (gold border, "EVOLUTION" tag)
- Level-up cards show a small hint when an upgrade is part of an evolution you're close to
- Evolutions replace their two parent upgrades in the level-up pool

## 4. More bosses and a midway boss
- Midway boss at 5:00, while the timer keeps running, so it's a fight in the middle of the swarm
- Three midway bosses, one picked at random each run:
  - **Splitter Prime**: a huge square that breaks into smaller copies as it takes damage
  - **The Hive**: stays at range and launches swarms of darts
  - **Lancer**: dashes across the screen in straight lines, leaving damaging trails
- Midway boss drops a guaranteed evolution chest (or a free upgrade if none are ready) and a heal
- Final boss variety: the Overseer plus one or two alternates, random per run or unlocked by winning
- Each boss gets its own intro banner, health bar and music cue

## 5. Shrinking arena
- Optional mode, chosen on the menu (Endless or Shrinking), with a `CONFIG` switch
- A circular glowing wall centered where the run starts
- The wall shrinks in steps (for example every 90 seconds), with a warning and a countdown before each shrink
- Touching or standing outside the wall deals damage over time and pushes you back in
- Enemies spawn inside the wall, near its edge
- Final boss fight happens in the smallest ring
- Gives extra shards as a reward for the harder mode

### Mini map
- Small circle in a corner of the HUD (top-right, under the buttons)
- Shows the arena wall, the next wall size as a dashed ring, your position and facing
- Shows bosses and elites as dots; regular enemies as a faint density haze, not individual dots
- Flashes when a shrink is coming
- Option to hide it in Options

## 6. Nuke (screen-clear bomb)
- Earn 1 nuke charge per 1,000 kills (`CONFIG.nuke.killsPerCharge`), carried across the run, with a cap (e.g. 3)
- Activating it destroys every enemy currently on screen; enemies off screen are not affected
- Bosses take heavy damage instead of dying (e.g. 10% of max HP); elites die
- Enemy shots on screen are cleared too
- Kills from a nuke still drop XP gems and count toward the next charge
- Controls: a nuke button on the HUD (touch/mouse, near the ammo readout), controller Y during play, keyboard N
- HUD shows the charge count and a progress bar toward the next charge
- Big effect: white flash, expanding shockwave ring from the player, screen shake, hit-stop, a deep boom
- Brief cooldown after use so it can't be double-tapped by accident
- Saved with the run; debug panel gets a "+1 NUKE" cheat
- Shop idea for later: a perk that lowers the kills needed per charge

## Backlog (not scheduled)
- Walled arena mode (fixed border that doesn't shrink)
- Landmarks: pillars and obstacles that block movement and bullets
- Weapon choice separate from ship (shotgun, SMG, sniper)
- Balance pass after real runs: boss HP, shop prices, difficulty curve
