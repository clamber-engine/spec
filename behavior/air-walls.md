# Walls in the air

What happens when a player who is jumping or falling moves sideways into
ground that is higher than their feet: being stopped by it, being lifted
onto it when it is only a little higher, and landing on it. Frames are
logic frames at 60 Hz, numbered as in a trace. `x`, `y`, `vx` and `vy` are
as in a trace: right and down are positive, speeds in px per frame.
Speeds are whole multiples of 1/256 px per frame and are written both
ways, as in `ground-speed.md`. "Left", "Right" and "Jump" are the neutral
inputs `L`, `R`, `J` of the scenario format.

Columns, face columns and vertical steps are as defined in
`steep-faces.md`: the floor has one height per 1-px-wide column of `x`,
the player's column is the one containing `x`, and "higher" means higher
on screen (smaller `y`). In this file "how far a floor is above the
player" means the player's `y` minus that floor's `y`.

Measured with the lead character in ordinary jumps and falls, by inputs
alone (67 runs) and in controlled single frames (about 120 000), against:
two vertical steps of 64 px rising to the left; steep faces of 4 px per
column rising to the right and to the left; and a 3-px rise to the right
at the top of a face. Where the rules below came from controlled frames
only, that is said.

## When the rules apply

On every frame that starts with the player airborne, after the frame's
new `vx` (`air-control.md`) and new `vy` (`jump-arc.md`, `walk-off.md`)
are worked out. On such a frame the player's feet are always at or above
the floor of their own column. (Falls into pits that carry the player
below the floor of the column they are in are not covered.)

Take the column the player's `x` would move into, `x + vx`. If it is the
**same column** as before the frame, or that column has no floor, nothing
in this file applies: the frame goes on as in `air-control.md`,
`jump-arc.md` and `landing.md`.

Otherwise, compare that column's floor with the player's feet **before**
the frame's vertical move:

| The new column is | Its floor is | Result |
|---|---|---|
| a face column | higher than the feet by any amount, even 1/16 px | **Blocked** |
| not a face column | more than 8 px higher than the feet | **Blocked** |
| not a face column | higher than the feet by 8 px or less | **Stepped onto** |
| any column | level with the feet or lower | Unaffected |

## Blocked

- `x` is set to the left edge of the column the player was in before the
  frame: the fraction of a pixel is dropped and `x` is a whole number.
  The player moves back slightly if moving right and forward slightly if
  moving left. This is the same as a grounded block in `steep-faces.md`.
- `vx` becomes **0**.
- Vertical motion is not affected: `y` moves by the frame's `vy` as usual,
  and the landing rule of `landing.md` applies to the floor of the
  player's own column. A blocked frame can also be a landing frame.
- The player stays airborne (`ground` is 0) unless that landing rule
  lands them. Facing doesn't change, nothing about the jump changes (a
  held jump keeps the lighter gravity of `jump-arc.md`), and no event is
  added to the row.

From the next frame `vx` starts again from 0 under `air-control.md`. A
player who keeps holding toward the wall moves toward it from the whole
pixel and is checked again each time they would cross into the next
column. So a player falling past a tall wall while holding toward it
stays at the same `x`, blocked on every frame that would cross into the
wall's column. A player rising past it can be stepped onto its top once
the top is no more than 8 px above the feet (an example follows).

## Stepped onto (not a face column, 8 px or less)

- **Rising** (`vy` < 0 after the frame's speed change): first the player
  is moved up by the smallest whole number of pixels that brings the feet
  level with or above the new column's floor. Then `y` moves by the
  frame's `vy` as usual. `x` and `vx` are not affected. The player stays
  airborne and goes on rising and falling as usual, and later lands on
  that floor (or another) by the rule of `landing.md`.
- **Falling** (`vy` > 0): the player lands on this frame on the new
  column's floor. This is a landing frame as in `landing.md`: `y` equal
  to that floor's `y`, `vy` 0, `ground` 1 and `land` in the row. `x` and
  `vx` are not affected.

Because the lift is whole pixels, the fraction of a pixel in `y` doesn't
change on that frame before the vertical move.

The limit is exact: measured in steps of 1/256 px, a floor exactly 8 px
above the feet was stepped onto and one 8 1/256 px above blocked the
player, both rising and falling, and the same in steps of 1/8 px at
several speeds.

## Measured cases

| Rise | Direction of move | Seen by inputs | Seen in controlled frames |
|---|---|---|---|
| Vertical step, 64 px | left | blocked; stepped onto while rising | blocked; stepped onto, rising and falling; the 8 px limit |
| Steep face, 4 px per column | right | blocked | blocked; any height above the feet blocks |
| Steep face, 4 px per column | left | — | blocked; any height above the feet blocks |
| 3-px rise onto ordinary floor | right | — | stepped onto, rising and falling |

The 8 px limit was measured only on non-face columns met moving left. In
the one case measured moving right (3 px), the rise was stepped onto as
the rule says. The horizontal speed, the fraction of `x` and of `y`, and
`vy` from −6 to +6 px per frame made no difference to which case applies.

## Examples

Synthetic floors. `y` is down-positive, as in a trace.

**A step up, jumping left.** Floor at `y` = 0 for columns 100 and up; a
step up to `y` = −48 for columns 99 and below (not face columns). Left
held, jump not held (gravity 0.4375), airborne at `x` = 104.5,
`y` = −33, `vx` = −512/256 and `vy` = −880/256 before frame 1:

| Frame | `x` | `y` | `vx` (1/256) | `vy` (1/256) | `ground` | Note |
|---|---|---|---|---|---|---|
| 1 | 102.5 | −36 | −512 | −768 | 0 | |
| 2 | 100.5 | −38.5625 | −512 | −656 | 0 | |
| 3 | 100 | −40.6875 | 0 | −544 | 0 | Blocked: column 98 is 9.4375 px above the feet |
| 4 | 99.75 | −50.375 | −64 | −432 | 0 | Column 99 is 7.3125 px above: up 8 px to −48.6875, then `vy` |
| 5 | 99.28125 | −51.625 | −120 | −320 | 0 | |
| 6 | 98.62109375 | −52.4375 | −169 | −208 | 0 | |
| 7 | 97.796875 | −52.8125 | −211 | −96 | 0 | |
| 8 | 96.828125 | −48 | −248 | 0 | 1 | Lands on the step (`landing.md`) |

**Falling past the same step.** Left held, `x` = 100, `y` = −20, `vx` = 0
and `vy` = 144/256 before frame 1. Each frame `vx` would become −64/256
and take `x` into column 99, 28 px or more above the feet, so the player
is blocked every frame and falls straight down at `x` = 100:

| Frame | `x` | `y` | `vx` (1/256) | `vy` (1/256) | `ground` |
|---|---|---|---|---|---|
| 1 | 100 | −19 | 0 | 256 | 0 |
| 2 | 100 | −17.5625 | 0 | 368 | 0 |
| 3 | 100 | −15.6875 | 0 | 480 | 0 |

**A face rising to the right.** Floor at `y` = 0 for columns up to 199; a
face rises from column 200 (`y` = −1, −5, −9, … for columns 200, 201,
202, …). Right held, jump held (gravity 0.28125), `x` = 196.25, `y` = −2,
`vx` = 512/256 and `vy` = −1372/256 before frame 1:

| Frame | `x` | `y` | `vx` (1/256) | `vy` (1/256) | `ground` | Note |
|---|---|---|---|---|---|---|
| 1 | 198.25 | −7.078125 | 512 | −1300 | 0 | |
| 2 | 200.25 | −11.875 | 512 | −1228 | 0 | Column 200 is below the feet |
| 3 | 202.25 | −16.390625 | 512 | −1156 | 0 | Column 202 (`y` −9) is below the feet |
| 4 | 202 | −20.625 | 0 | −1084 | 0 | Blocked: face column 204 (`y` −17) is 0.609375 px above |
| 5 | 202.25 | −24.578125 | 64 | −1012 | 0 | Same column: not checked |
| 6 | 202.71875 | −28.25 | 120 | −940 | 0 | |
| 7 | 203.37890625 | −31.640625 | 169 | −868 | 0 | Column 203 (`y` −13) is below the feet |

## Not covered

- Ceilings, and anything above the player's head.
- Vertical steps and other non-face rises of 4 to 63 px, rises met moving
  right that are higher than 3 px and not face columns, and faces
  steeper or less steep than 4 px per column.
- A frame with `vy` exactly 0 that is stepped onto a higher column.
- Rolls, bounces off enemies (`bounce.md`), carrying (`carrying.md`),
  being knocked back (`knock-out.md`) and the other character meeting a
  wall in the air.
- Falls into pits that carry the player below the floor of their own
  column: there the player was seen to pass under neighbouring floors.
- The frames after landing on a face column (`steep-faces.md`).
- Entities and moving platforms as walls.
