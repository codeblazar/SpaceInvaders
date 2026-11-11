# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A self-contained Space Invaders clone in a single file: [index.html](index.html). No build tools, dependencies, or server required — open directly in a browser.

## Architecture

Everything lives in `index.html`: inline `<style>` for the dark-terminal theme, then a `<canvas id="gameCanvas">` (800×600), then a `<script>` block containing all game logic.

### Script structure (top to bottom)

1. **Constants** — canvas dimensions, game-state enums (`PLAYING`, `WON`, `LOST`), sprite pixel maps (ASCII `'X'` = filled pixel), colour values, timing/speed constants.
2. **State initialisation** — `initGame()` resets all mutable state: player position, invader grid (5 rows × 11 columns), 4 bunkers, projectile arrays, mystery-ship timer, score/lives/wave.
3. **Update functions** — one per subsystem (`updatePlayer`, `updateInvaders`, `updateProjectiles`, `updateBunkers`, `updateMysteryShip`, `updateExplosion`). Called each frame in order.
4. **Render functions** — `drawSprite` renders a pixel-art sprite at a given scale; higher-level draw calls assemble the HUD (score, wave, lives, hi-score) and all game objects.
5. **Game loop** — `requestAnimationFrame`-based loop; game logic runs at a fixed 60 updates per second (time accumulator), then render each frame.
6. **Input** — `keydown`/`keyup` listeners write into a `keys` object; the update functions read from it.

### Key design facts

- Sprites are defined as arrays of strings; `drawSprite` iterates characters and fills a rect for each `'X'`.
- Bunkers use a 2-D boolean grid; shots punch holes pixel-by-pixel via AABB collision + area-of-effect radius.
- Invader move interval shrinks as the count drops, creating the classic speed-up effect.
- High score is persisted to `localStorage` (with try/catch for cross-origin contexts).
- Explosion on player death: white-flash frame → particle scatter with gravity, colour-shifting orange→red over ~120 frames.
