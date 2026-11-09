# CLAUDE.md — Neon Arena

Hobby game for the repo owner (Matt). A landscape phone survivor shooter in the spirit of 20 Minutes Till Dawn, neon/synthwave look. Played mainly on his Android phone (OnePlus 13R, Chrome, installed from GitHub Pages), sometimes with a USB-C telescopic controller, sometimes on PC.

- Live site: https://mfleshner.github.io/neonarena/ (GitHub Pages, `main` branch, root folder; a push to `main` deploys)
- Next work: see `plan-updates.md` (do items in its order unless told otherwise)

## Files
- `index.html` — the whole game: HTML, CSS and one inline `<script>`. Source of truth. No build step, no libraries.
- `manifest.webmanifest`, `icons/` — installable app, fullscreen + landscape
- `sw.js` — offline support, network first with cache fallback (no version bump needed for updates)
- `plan-updates.md` — roadmap; `README.md` — player-facing controls

## Design decisions (keep unless Matt changes them)
- **Tap to fire**: each tap / click / button press fires one shot. Not hold-to-fire.
- **Magazine + idle reload**: reload starts after `reloadDelay` seconds of not firing, or right away at 0 ammo. Tapping during a reload cancels it if ammo > 0.
- **Aim**: setting `SET.aim` = `device` (default: auto-aim on touch, manual with mouse/controller) | `auto` | `manual`. Auto-aim targets the nearest enemy with a small lead.
- **Infinite map**: camera keeps the player centered; enemies spawn on a ring around the view and far ones are recycled back to the ring.
- **Run**: a midway boss at 5:00; survive `runSeconds` (600), then a final boss spawns; killing it wins.
- Every tunable number lives in `CONFIG` at the top of the script. Add new numbers there, not inline.

## Script map (in order)
- `CONFIG` — all tuning: gun, player, ships, perks, shards, orbital, nova, evolutions, overload, spawn, difficulty, elite, boss, xp, enemies, controls, audio
- `UPGRADES` — in-run level-up choices (`id`, `name`, `desc`, `max`, `apply()` mutates `S`)
- Evolutions: `UPGRADES` entries with `evo:true, needs:[a,b]`; offered (up to 2, shown first, gold card) once both parents are maxed (`evoReady`). Flags live in `S.evo` (chain, rail, exec, overflow, blade, jugg); numbers in `CONFIG.evolutions`. Helpers: `explode()`, `overflowRing()`, `orbRadius()`, rail `S.trails`. Level-up cards show an "Evolves into…" hint via `evoHint()`.
- Canvas setup: `resize()` sets `W`, `H`, `DPR`, `SC` (world→screen scale = H / view.height)
- Save data: `SAVE` (localStorage `neonArena.save`: shards, best, wins, ship, owned, perks, settings); `SET = SAVE.settings`; `persist()`
- Audio (all synthesized): buses `BUS.master/sfx/music/duck` with a compressor; `audioLevels()` applies `SET.sound`, `SET.musicVol`, `SET.sfxVol` and ducks music when paused; `tone()`, `noise()`, `allow()` (limits the same sound in a burst); `sfx.*`. Music: `SONGS` (4-bar chord loops), step sequencer `playStep()` / `musicTick()` (called each frame, schedules 0.15s ahead), layers switch on by run time from `CONFIG.audio.layers`, boss track in boss phase, low-HP heartbeat. `buzz()` vibration; `goFullscreen()` (fullscreen + landscape lock on Android)
- Sprites: `SHAPES`, `sprite(shape,color,r)` pre-renders glowing shapes to cached canvases (do NOT use per-frame shadowBlur, it kills phone FPS)
- Spatial grid: `gridBuild(list)`, `gridQuery(x,y,r,fn)` — used for all enemy collisions
- State: `newGame()` builds `S` (player, gun copy, arrays: enemies, bullets, eshots, gems, parts, texts, rings, ripples; flags: bossPhase, boss, state)
- `S.state`: `menu` | `play` | `levelup` | `paused` | `ending` | `over`
- Input: touch joystick (`joy`, left `joystickZone` of screen) + tap right side; keyboard (`KEYMAP`, Space fires); mouse (`mouse`, click fires); `inputMode` = touch | mouse | gamepad; `manualAim()`, `manualAngle()`
- Controller: `gp`, `PAD` (standard mapping), `pollPad(dt)` called each frame; menu nav via `visibleButtons()`, `navMove()`, `navBack()`, `keepFocus(fn)` (use when rebuilding button lists)
- Gun: `tap()`, `fire()`, `nearestEnemy()`, `nearestFrom()`
- Spawning: `spawnEnemy(type,x,y,elite)`, `eshot(x,y,a,speed,dmg,life,r)` (enemy bullets; speed 0 = lingering mine)
- Bosses: `makeBoss(kind,def,final)`; final boss `spawnBoss()` uses `S.finalKind` (random from `CONFIG.finalBosses.pick`: overseer | conductor) and sets `S.boss`; midway boss `spawnMidBoss(kind)` at `CONFIG.midBoss.at` (prime | hive | lancer) sets `S.midBoss`. `bossDef(e)` returns a boss's config; AI in `BOSS_AI[kind]` via `updateBoss()`. Killing a final boss (`e.final`) wins; a midway boss drops a chest (`openChest()`: random ready evolution, else a free level-up), a heal and XP. Bosses use `windup` (charge line) and `blinkT/bx/by` (teleport marker) for telegraphs.
- Combat: `damageEnemy()`, `killEnemy()`, `hurt()`, `addXp()`, `pulseNova()`, `levelBlast()`
- Overload (screen-clear): `CONFIG.overload`; every kill calls `chargeOverload()` (`S.ovKills` → `S.ovCharges`, capped); `activateOverload()` kills every enemy inside the visible area, clears on-screen enemy shots, and deals `bossDamage` × max HP to bosses. Triggers: HUD `#ovBtn`, controller Y during play, N key. Shockwave drawn from `S.wave`.
- `update(dt)` — gameplay; `updateFx(dt)` — particles/texts/rings/shake (also runs during `ending` slow-mo)
- `render(dt)` — world pass (grid, gems, enemies, bullets, player, ammo arc) then screen pass (vignette, boss bar, banner, joystick, crosshair, hints, FPS)
- HUD: DOM elements updated in `updateHud()` with `setText()` caching
- Screens: `SCREENS` list + `show(id)`; builders `buildShips()`, `buildShop()`, `buildToggles()` (fills both `#toggles` on pause and `#optToggles` in Options), `buildCards()` for level-ups
- Run flow: `start()`, `pause()`, `resume()`, `openLevelUp()`, `beginEnd(won)`, `finishRun()` (awards shards, best, wins)
- Run save: `saveRun()` / `loadRun()` / `clearRun()` / `continueRun()` (localStorage `neonArena.run`, `v:1`; drops bullets/effects; boss restored via `bossIdx`). Saved on pause, tab hidden, menu, and every 10s. Cleared on new run and on death/win. If `S` gains fields, they save automatically, and `continueRun()` merges a loaded run over a fresh `newGame()` state so older saves get new fields' defaults; bump `v` only for breaking changes.
- Debug panel: `DEBUG_ROWS` (tunable paths into `CONFIG`, with `live` hooks into `S`), `CHEATS`, `buildDebug()`, `openDebug()`; tweaks persist in localStorage `neonArena.debug` and are applied over `CONFIG` at load (`DEFAULT_CONFIG` keeps the originals). Cheats set `S.cheated`, which blocks records. Open: tap PAUSED 5×, ` key, or controller Y on pause.
- `frame()` — main loop: `pollPad`, hitstop, update or ending slow-mo, render, HUD

## Gotchas
- Keep `[hidden]{display:none!important}` in the CSS. `.screen` sets `display:grid`, which otherwise overrides `hidden` and shows every menu at once.
- Phone performance: draw batched paths, reuse cached sprites, keep particle caps (`parts` ≤ 520, `eshots` ≤ 400, `rings` ≤ 80).
- Wrap all localStorage access in try/catch.
- Landscape phones are short (~390 CSS px tall): keep menus compact, test card rows at ~850×390.
- New screens: add the element with `class="screen" hidden`, add its id to `SCREENS`, decide in `show()` whether the HUD hides, and add a B-button target in `navBack()`.

## Checking work
No test suite. Before committing, extract the script and syntax-check it:
```
python3 -c "import re;s=open('index.html').read();open('/tmp/g.js','w').write(re.findall(r'<script>(.*?)</script>',s,re.S)[0])" && node --check /tmp/g.js
```
Matt tests on his phone after each push. Keep replies short and say what changed and how to try it.

## Commits
Commit as Matt (`git -c user.name="mfleshner" -c user.email="mattfleshner@gmail.com"`), short imperative messages, and push to `main`.
Bump `BUILD` (top of the script) by 1 in every push that changes `index.html`. It shows on the menu and pause screen so Matt can tell whether his phone has the latest version.
