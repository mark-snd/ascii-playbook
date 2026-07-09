# ASCII Fishtank

An animated ASCII art aquarium rendered as a live character grid, styled like a
deep-sea terminal. Everything is a single self-contained HTML file — no build,
no dependencies.

**Live page:** https://mark-snd.github.io/ascii-playbook/fishtank.html
**Repo:** https://github.com/mark-snd/ascii-playbook

## What's in the tank

| Element | Behavior |
|---|---|
| Small fish ×7 (`><(((º>` `><((('>` `><>`) | Swim both directions with individual speed, bobbing rhythm, and color; chase food; flee the predator |
| Big fish (`><((((((((º>`, violet) | Cruises until hungry, then hunts the nearest small fish; shows a belly bulge (`@`) after a catch |
| Crab (`(\/)°.°(\/)`) | Patrols the gravel with animated claws; eats crumbs that reach the bottom |
| Jellyfish ×2 (pink, 3-row bell + tentacles) | Pulse on independent rhythms: bell contracts and kicks upward, then drifts back down while open; wander slowly, bounce at the walls; ignored by the predator |
| Bubbles | Rise from the treasure chest and fish mouths, wobble, pop at the surface (`.` → `o` → `O`) |
| Seaweed | Sways on independent sine rhythms |
| Surface / gravel / chest | Rippling `~` water line, static seeded gravel bed, bubbling treasure chest |

**Interaction:** click the tank (or focus it and press Enter) to drop food
flakes. Fish chase them — unless the predator is nearby, in which case the food
sinks to the crab. Press and hold the tank (or hold Space) for 250ms to tap the
glass instead: an expanding ripple ring follows your pointer (or the tank
center, for the keyboard), nearby fish startle away from it, the crab wanders
over to investigate, and any jellyfish under the tap contracts early and
pulses rapidly while you hold. A quick press/click still feeds; only a
sustained hold taps. Footer census tracks `fish / bubbles / gulps`.

Respects `prefers-reduced-motion` (renders a composed still frame instead of
animating).

## Version history

| Version | Tag | What changed |
|---|---|---|
| v1.0 | `v1.0` | Original tank: fish, bubbles, seaweed, chest, click-to-feed |
| v1.1 | `v1.1` | Predator: big fish hunts and swallows smaller fish; prey flee; eaten fish respawn from the edges; `gulps` counter |
| v1.2 | `v1.2` | Crab: patrols the gravel; uneaten flakes become crumbs it cleans up |
| v1.3 | `v1.3` | Jellyfish: two pink jellies pulse and drift through the mid-water, occasionally releasing a bubble |
| v1.4 | `v1.4` | Tap the glass: press-and-hold startles nearby fish, draws the crab, makes jellyfish pulse; expanding ripple rings |

## How it works (for future upgrades)

All code lives in `fishtank.html` — one `<style>` block, one `<script>` IIFE.

- **Rendering:** a `W × H` cell grid is rebuilt every frame (~11 fps,
  `setInterval` 90 ms) and serialized into the `<pre>` as HTML, grouping
  consecutive same-color cells into `<span>`s. Draw order = z-order:
  surface → seaweed → chest → gravel → food/crumbs → jellyfish → fish → predator → crab → bubbles.
- **Entities:** plain arrays of objects (`fishes`, `bubbles`, `flakes`,
  `crumbs`, `respawns`, `jellies`) plus singletons `pred` and `crab`. `step()` advances
  the simulation; `render()` draws it. Add a new creature by seeding it,
  updating it in `step()`, and drawing it in `render()`.
- **Sprites:** each swimmer has left/right string pairs (see `SPRITES`, `BIG`,
  `CRAB`). Characters like `<` and `>` are HTML-escaped at serialization time,
  so sprites can use any character.
- **Colors:** CSS variables in `:root`, one class per entity type
  (`.f1`–`.f5`, `.cr`, `.b`, `.s`, `.g`, `.c`, `.p`, `.w`/`.wd`).
- **Responsive:** grid width recomputed from measured character width on
  resize; scenery reseeds. Don't cache positions across `sizeGrid()` calls
  without clamping to the new `W`/`H`.

## Upgrade checklist

1. Edit `fishtank.html`.
2. Bump the version string in the header: `<span class="name">fishtank vX.Y</span>`
   (minor bump for features, major for redesigns).
3. Test locally: `python3 -m http.server 8642` → http://localhost:8642/fishtank.html
4. Commit with the version in the message, tag, and push:
   ```
   git commit -am "vX.Y: <what changed>"
   git tag vX.Y
   git push && git push --tags
   ```
5. GitHub Pages redeploys automatically (~1 min). Verify the live header shows
   the new version (hard-refresh: Cmd+Shift+R).

## Idea backlog

- Day/night cycle (palette shift, sleeping fish, moonlight ripples)
- Sunken castle to replace or join the treasure chest
- A school of tiny fish that moves as one flock
- Snail climbing the glass walls
- Fish that grow after eating enough flakes
- Rare event: diver or submarine crossing the tank
