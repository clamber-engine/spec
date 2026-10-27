# Walls in the air

Titles: `title-1`, `title-2`

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

The sections below up to "In `title-2`" give the `title-1` rules. "In
`title-2`" gives the `title-2` rules, which differ in the limits, the
lift, which columns are looked at and the landing.

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
- The player stays airborne (`ground` is 0, except on T+1 below) unless that landing rule
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

### Blocked on the first frame after take-off

If the frame that is blocked is **T+1**, the first frame after the
take-off frame T of `jump-arc.md`, the row also differs in two fields:
`state` reads `idle` and `ground` reads **1**, as if the player were still
standing against the wall. Everything else in the row is as for any
blocked frame: `x` whole, `vx` 0, `y` moved up by the frame's `vy`
(the jump's arc is the usual one), facing unchanged. On T+2 the row is
back to the ordinary jump: `state` `jump`, `ground` 0, and `vx` starts
again from 0 under `air-control.md`.

Only T+1 does this. A jump that is blocked on T+2 or later reads `jump`,
`ground` 0 on the blocked frame as above. A jump pressed while the
player is already standing against the wall has `idle` and `ground` 1 on
T itself (the player was not moving), then an ordinary T+1.

Seen in the original with the lead at a steep face (4 px per column), to
the right and to the left, for a walking approach (`vx` 2 px per frame)
and a running one (3 px per frame), with the jump held for one frame and
for twenty; the press timed so that the frame after take-off was the first
to meet the face. Pressed a frame or more earlier, the feet are higher
and the block comes later (T+2 or later: ordinary rows).
Against walls built of several stacked surfaces the `idle` and
`ground` 1 reading was seen lasting for the whole rise; that is not
covered.

**Example.** Walking right at 2 px per frame on a floor at `y` = 0. A steep
face's columns from 205 up are all higher than the feet at T+1. Jump
pressed so that T is the frame with `x` = 204.96875:

| Frame | `x` | `y` | `vx` (1/256) | `vy` (1/256) | `ground` | `state` | Note |
|---|---|---|---|---|---|---|---|
| T | 204.96875 | 0 | 512 | −1792 | 1 | walk | |
| T+1 | 204 | −6.5625 | 0 | −1680 | 1 | idle | Blocked: `x` + `vx` would be column 206 |
| T+2 | 204.25 | −12.6875 | 64 | −1568 | 0 | jump | |
| T+3 | 204.71875 | −18.375 | 120 | −1456 | 0 | jump | |

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

## In `title-2`

Everything in this section was measured on `title-2` only, with the lead
character, against two steep faces of 4 px per column that rise to the
right (one 64 px tall, one 96 px tall, each a run of face columns as in
`steep-faces.md`) and the 3-px rise from the top of each face onto the
floor beyond it, which is not a face column. Measured by inputs alone
(624 runs: jumps and falls toward and along the faces, walking and
running, with jump held for different lengths, and random inputs) and in
controlled single frames (about 280 000), with enemies kept away. Speeds
are whole multiples of 1/256 px per frame and are written both ways.
Columns, face columns and "higher" are as in `steep-faces.md`. **F** is
the floor `y` of a column, always a whole number.

### When the rules apply (`title-2`)

As in `title-1`: on every frame that starts with the player airborne in a
jump or a fall, after the frame's new `vx` (`air-control.md`, "In
`title-2`") and new `vy` (`jump-arc.md`, `walk-off.md`, "In `title-2`")
are worked out. Take the column `x + vx` would move into.

- If it is the **same column** as before the frame, nothing in this
  section applies.
- Otherwise **only that column** is compared with the player's feet.
  Columns passed over on the way are not looked at: at running speed a
  frame can cross two or three columns, and a face column 1 px above the
  feet that was passed over in this way didn't block.

Let **U** = ⌊`y`⌋ − F, with `y` taken **before** the frame's vertical
move and ⌊`y`⌋ meaning `y` rounded down to a whole number (for example
⌊−26.3⌋ = −27). U counts the whole pixels by which that column's floor
is above the player's feet: because F is a whole number, U ≥ *k* exactly
when `y` − F ≥ *k*.

| The new column is | U | Result |
|---|---|---|
| a face column | 1 or more | **Blocked** |
| a face column | 0 or less (less than 1 px above the feet, or not above) | Unaffected |
| not a face column | 5 or more | **Blocked** |
| not a face column | 1 to 4 | **Stepped onto** |
| not a face column | 0 or less | Unaffected |

So in `title-2`, unlike `title-1`, a face column less than 1 px above the
feet doesn't block, and the limit for other columns is **4 px**, not 8.

### Blocked (`title-2`)

As in `title-1`: `x` is set to the left edge of the column the player was
in (the fraction is dropped), `vx` becomes **0**, `y` moves by the
frame's `vy` as usual, and the player lands on this frame if the landing
rule (below) lands them on their own column. Facing follows the input as
in `air-control.md` ("In `title-2`"), the jump's gravity is unchanged and
no event is added. From the next frame `vx` starts again from 0.

### Stepped onto (`title-2`)

`x` and `vx` are not affected (the player moves into the new column).

- If the frame's `vy` (after its gravity) is **0 or more**, the player
  lands on this frame on the new column's floor: `y` = F, `vy` 0,
  `ground` 1, `land` in the row.
- If `vy` is negative (rising), the whole-pixel part of `y` becomes F and
  its fraction is kept; then `y` moves by `vy`. So the player is lifted
  by exactly U whole pixels and, before the move, the feet are still the
  fraction of a pixel below the new floor. (In `title-1` the lift is the
  smallest one that brings the feet level with the floor or above it.)
  The player stays airborne.

### Landing (`title-2`)

On frames that are not stepped onto, the player lands if the frame's
`vy` is **0 or more** and moving by it takes the feet to:

- the floor or below it, on a column that is not a face column (as in
  `walk-off.md`, "In `title-2`");
- **3 px above the floor** or lower, on a face column (the margin of
  sloped pieces in `slopes.md`, "In `title-2`").

The column is the player's own column after the frame's horizontal move
(the column they were in, if blocked). On landing, `y` = F, `vy` 0,
`ground` 1 and the row carries `land`. A frame with `vy` exactly 0 that
reaches the limit lands; in `title-1` it never does.

The floor at the top of the 96-px face shares a 16-column piece with the
top 8 face columns, but on those floor columns the margin was **0**, not
3: falls that ended a frame 0 to 3 px above that floor without reaching
it stayed airborne (controlled frames only). The margin followed the
column, not the piece. `slopes.md` ("In `title-2`") measured the piece
rule on 1-in-2 and 1-in-4 slopes only.

### Measured cases (`title-2`)

| Rise | Direction of move | Seen by inputs | Seen in controlled frames |
|---|---|---|---|
| Steep face, 4 px per column, 64 px and 96 px | right | blocked (304 frames, 66 of them landing frames); 240 landings on face columns | blocked at 1 px and more; less than 1 px enters; landings on face columns with the 3 px margin |
| 3-px rise onto floor beyond a face | right | stepped onto, rising and falling | stepped onto, rising and falling |
| Floor beyond a face, reached across 2 or 3 columns in one frame, 1 to 16 px above the feet | right | — | stepped onto up to 4 px, blocked from 5 px |

The limits are exact: in steps of 1/256 px, a face column exactly 1 px
above the feet blocked and one 255/256 px above didn't, and other floor
exactly 4 255/256 px above the feet was stepped onto while 5 px blocked,
rising and falling. Horizontal speed, the fraction of `x` and `vy` from
about −4 to +4.5 px per frame (and exactly 0) made no difference. No
rise to the left, and no rise taller than 3 px other than faces, was
available.

### Examples (`title-2`)

Synthetic floor, `y` down-positive: `y` = 0 for columns up to 199; a face
rises to the right over columns 200 to 215 (`y` = −1, −5, −9, …, −61);
floor at `y` = −64 from column 216 on (not face columns: 3 px above
column 215).

**Jumping into the face.** Right held, jump held from the start (gravity
0.3515625 while rising), airborne at `x` = 194.5, `y` = −3.25,
`vx` = 512/256, `vy` = −1450/256 before frame 1:

| Frame | `x` | `y` | `vx` (1/256) | `vy` (1/256) | `ground` | Note |
|---|---|---|---|---|---|---|
| 3 | 200.671875 | −18.1328125 | 533 | −1180 | 0 | Column 200 (−1) is below the feet |
| 4 | 202.7734375 | −22.390625 | 538 | −1090 | 0 | |
| 5 | 204.890625 | −26.296875 | 542 | −1000 | 0 | |
| 6 | 204 | −29.8515625 | 0 | −910 | 0 | Blocked: column 207 (−29) is 2.703125 px above the feet (U 2) |
| 7 | 204.28125 | −33.0546875 | 72 | −820 | 0 | Same column |
| 8 | 204.80859375 | −35.90625 | 135 | −730 | 0 | Same column |
| 9 | 205.55078125 | −38.40625 | 190 | −640 | 0 | Column 205 (−21) is below the feet |

**Blocked and landed on a face column.** Right held, jump not held,
`x` = 201.75, `y` = −7.5, `vx` = 200/256, `vy` = 300/256 before the
frame. Column 202 (−9) is 1.5 px above the feet: blocked, `x` = 201,
`vx` 0. The feet move to −5.890625, within 3 px of column 201's floor
(−5), a face column: the player lands with `y` = −5.

**Onto the floor beyond the face in one running frame.** Right held,
`x` = 213.5, `vx` = 768/256 (the frame's `vx` is 744/256, so `x + vx`
is in column 216, passing over face columns 214 and 215):

| Before the frame: `y`, `vy` (1/256) | U | Result |
|---|---|---|
| −60.5, 300 | 3 | Stepped onto, falling: lands, `x` = 216.40625, `y` = −64 |
| −59.125, 300 | 4 | Stepped onto, falling: lands, `y` = −64 |
| −58.5, 300 | 5 | Blocked: `x` = 213, `vx` 0, `y` = −56.890625; still airborne, 3.890625 px above column 213's floor (−53), a face column |
| −60.25, −600 | 3 | Stepped onto, rising: `y` = −64 + 0.75, then −488/256: `y` = −65.15625, `x` = 216.40625, `vx` 744 |

### Not covered in `title-2`

- Rises to the left, vertical steps, and floor that is not a face column
  more than 3 px above the column next to it (the 4 px limit was
  measured only by crossing several columns in one frame).
- The first two airborne frames of a jump that takes off from a face
  column: the face's pull and limits (`steep-faces.md`, "In `title-2`")
  were seen to carry into them.
- Rolls, bounces, knock-outs, carrying and the second character meeting a
  wall in the air; ceilings; entities and moving platforms as walls.

## Not covered

This list is for `title-1`; `title-2` has its own list above.

- Ceilings, and anything above the player's head. Platforms passed from
  below are in `platforms-from-below.md`.
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
