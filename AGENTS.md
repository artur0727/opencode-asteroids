# AGENTS.md

Vanilla HTML5 Canvas game — no package.json, no build/lint/test tooling, no dependencies. Don't add a bundler or framework.

## Running / verification

- The only way to verify changes is to run it: `npx serve .` then open `http://localhost:3000`, or open `index.html` directly in a browser. There is no automated test suite.
- `game.js` runs top-to-bottom on load (`initGame()` + `requestAnimationFrame(loop)` at the bottom); it requires the `#canvas` element from `index.html`, so it must stay loaded via `<script src="game.js">` after the canvas.

## Architecture (single file)

All game code is in `game.js`. Everything else is static: `index.html`, `favicon.svg`, `README.md`.

Conventions an edit can easily break:

- **Entity pattern**: every entity (Bullet, Asteroid, Ship, Particle) has `update(dt)`, `draw()`, and a `dead` flag; arrays (`bullets`, `asteroids`, `particles`) are filtered by `!dead` each frame. New entities must follow this.
- **Toroidal world**: all moving positions must go through `wrap(v, W or H)`. New moving entities need this or they'll leave the screen.
- **World size**: `W`/`H` (800×600) in `game.js` must match `width`/`height` on the `<canvas>` in `index.html` — change both together.
- **Asteroid tuning tables** are indexed by `size` 1=small, 2=medium, 3=large, so `POINTS = [0, 100, 50, 20]` is deliberately inverse to size. `split()` returns two asteroids of `size - 1` (empty at size 1).
- **Input** uses `e.code` (physical keys). `justPressed` is one-shot: it's cleared by `pressed(code)` — call it exactly once per key per frame or you'll drop/miss presses.
- **dt** is seconds, clamped to 0.05 max (tab-switch protection). Ship collision uses a fudge factor (`a.radius * 0.82`) against the irregular asteroid polygon.
- **Comments and UI strings are in Spanish**; keep new strings consistent.

## Caveats

- `README.md` claims power-ups and a "estrella fugaz" asteroid type that do not exist in `game.js`. Trust the code, not the README.
