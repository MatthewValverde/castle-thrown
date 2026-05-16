# Castle Thrown

An ASCII medieval castle-siege brawler that runs in a single HTML file.
Player vs. AI, real-time, tick-based.

![ASCII banner: # # # ... # # #]

## Quick start

Open `castle-thrown.html` in a modern desktop browser. That's it.
No build step, no dependencies, no server.

The page is self-contained: HTML, CSS, and JS in one file, with sound
synthesized at runtime via the Web Audio API and stats persisted to
`localStorage`.

## How to play

You command the left castle; the AI commands the right.
Each side regenerates gold every tick and spawns units that march toward
the other side's throne. First throne to fall loses.

### Controls

| Key     | Action                          |
|---------|---------------------------------|
| `1`     | Spawn Knight (20 gold)          |
| `2`     | Spawn Archer (35 gold)          |
| `3`     | Spawn Catapult (70 gold)        |
| `Space` | Pause / resume                  |
| `R`     | Reset the battle                |

Mouse works too — buttons at the bottom of the page mirror the keys.

## Units

| Unit     | Cost | HP | Damage | Range | Role |
|----------|------|----|----|-------|------|
| Knight   | 20   | 32 | 10 | melee (1) | Cheap front-liner. Moves every tick. |
| Archer   | 35   | 18 | 7  | 14 cells  | Mid-range. Holds position to fire arrows. |
| Catapult | 70   | 55 | 22 | 36 cells  | Siege weapon. Advances while firing; prefers walls and the throne over enemy units. |

Walls have 30 HP and degrade visually as they take damage (`#` → `=` → `.` → gone).
The throne has 45 HP. Once it's gone, the game is over.

## Difficulty

Pick before the match starts. Affects AI gold rate, spawn cadence, and
unit composition.

- **Squire** — slowest AI, mostly knights, easy win for most players.
- **Knight** — balanced. Mix of all three unit types, reactive to your catapults.
- **King** — fastest gold, aggressive spawn timing, leans on archers + catapults.

In headless simulations, a competent player wins ~4/5 Knight games and
loses ~4/5 King games. Squire is intentionally forgiving.

## Customization

There are seven built-in color themes (Parchment, Vellum, Dungeon,
Terminal, Blood Moon, Lapis, Snow) plus three live color pickers if you
want to dial in your own background, foreground (ASCII art and text),
and accent colors. Changes apply instantly via CSS variables.

The display font is MedievalSharp (Google Fonts) for headings and
JetBrains Mono for the battlefield grid.

## How it works (briefly)

The world is an 80×12 character grid rendered into a `<pre>` element on
every tick (400 ms). Everything that moves — units, projectiles,
crumbling walls, damage flashes — is just characters being written into
that grid.

Units occupy 3×3 sprite cells anchored at a column on the ground row.
Movement and attack are decoupled: a unit can advance one column per
tick (subject to its `moveCooldown`) and fire on its own
`attackCooldown`, independent of whether it has a target. Archers are
the exception — they hold position while a target is in range. Friendly
units don't block each other, only enemies and standing walls do, which
prevents stalemates behind the front line.

The AI uses a per-difficulty weighted bag for unit selection plus a
`saveChance` so it occasionally waits to afford expensive units instead
of always defaulting to the cheapest option. It also has a light
reactive layer: if you field multiple catapults, it shifts toward
archers.

Stats are stored under the localStorage key
`castle-thrown:stats:v1` (wins / losses / current streak). Delete that
key to reset.

## File layout

It's one file. Open it in a text editor and you'll find roughly:

1. CSS variables and theme definitions
2. World constants (grid size, tick rate, castle layout)
3. Unit definitions
4. Castle build + tile rendering
5. AI think loop
6. Tick loop (cooldowns → targeting → attack → movement → projectiles)
7. Rendering (paint to grid → join to string → assign to `<pre>`)
8. DOM wiring (buttons, pickers, keyboard, pause overlay)

## Browser requirements

Anything modern with Web Audio support. Tested in Chrome and Firefox on
desktop. Mobile works visually but the keyboard shortcuts and grid
density are tuned for a desktop window.

## License / use

Yours. Hack it, theme it, fork it, drop it on a Gumroad page, whatever
you like.
