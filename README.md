# Neon Arena

A landscape phone survivor shooter in a single HTML file, in the spirit of 20 Minutes Till Dawn.

- Left thumb: floating joystick to move
- Tap the right side: fire one auto-aimed shot
- Stop firing for a moment: reload
- Speed reload: tap while the reload bar is inside the white zone to reload instantly. Miss on an empty mag and you fumble (slower reload)
- PC: WASD or arrows to move, mouse to aim, left click (or Space) to fire, Esc to pause
- Controller: left stick or D-pad to move, right stick to aim, RT / RB / LT / A to fire, Start to pause. D-pad, A and B work in every menu.
- Options → Aim: by device (auto on touch, manual with mouse or controller), always auto, or always manual
- Overload: every run starts with 1 charge, and every 1,000 kills earns another (up to 3). Tap the OVERLOAD button, press Y on a controller, or N on a keyboard to destroy every enemy on screen
- Survive 10:00, then destroy the Overseer

## Play

Open the GitHub Pages site in Chrome on Android, then use **⋮ → Add to Home screen / Install app**. The installed app opens fullscreen in landscape and works offline.

## Tweaking

Every number lives in the `CONFIG` block at the top of the script in `index.html`: gun and reload timing, spawn rates, enemy stats, boss, ships, shop perks, XP curve and run length. Upgrades are in the `UPGRADES` list right below it.

## Files

- `index.html` — the whole game
- `manifest.webmanifest`, `icons/` — install as an app (fullscreen, landscape)
- `sw.js` — offline support (network first, cache fallback)

Progress (shards, unlocks, a saved run) is stored in the browser's localStorage on this site.
