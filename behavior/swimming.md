# Swimming

Titles: `title-1`

How the player moves in the levels that are set underwater: swimming
left and right, strokes, sinking, walls, ceilings, the level's left end,
and resting on the floor. Frames are logic frames at 60 Hz, numbered as
in a trace. `x`, `vx`, `vy` and
`facing` are as in a trace: right and down are positive, speeds in px
per frame. "Left", "Right", "Up", "Down", "Jump", "Action" are the
neutral inputs `L`, `R`, `U`, `D`, `J`, `A` of the scenario format.
Measured with the first character swimming alone, in eight underwater
areas.

Speeds are always whole multiples of 1/256 px per frame. The rules below
are exact in those units, so this spec writes speeds both ways
(512/256 = 2 px per frame).

Underwater, the player is swimming all the time. There is no walking,
running, jumping, rolling or turn: the rules below replace those on
land.

## Horizontal speed

Each frame the player has a **target speed** set by what is held on that
frame:

| Held | Target |
|---|---|
| Right | +2 (+512/256) |
| Left | −2 (−512/256) |
| No direction | 0 |

Action, Up, Down and Jump make no difference to the target. Holding
Action changes nothing in swimming.

Each frame, using speeds in 1/256 px per frame:

1. **gap** = target − current speed.
2. **step** = gap ÷ 32, rounded toward zero to a whole number.
3. If step is 0 (the gap is less than 32/256 either way), the speed
   becomes the target. Otherwise the speed changes by step.
4. The player moves by the new speed: in open water `x` changes by
   exactly the new `vx` on that frame. Walls and the level's left end
   can stop it ("Moving", below).

This is the same rule as on land (`ground-speed.md`) with 32 in place of
8, so speed changes four times more slowly. Consequences:

- From rest, holding Right gives 16/256 on the first frame, then 31, 46,
  60, 74, 87, 100, … It reaches 481/256 on frame 105 and exactly 2 on
  frame 106.
- Letting go at 2 slows to 496/256, 481, 466, … down to 32/256, then
  31/256, then 0: 106 frames from 2 to a stop.
- Holding the opposite direction at full speed reverses through 0 to the
  other full speed in 128 frames.

## Facing

Holding Left makes the player face left, and holding Right makes them
face right, on that same frame, whatever the speed. There is no turn
(unlike `ground-speed.md`). With no direction held, facing doesn't
change. Up, Down, Jump and strokes don't change facing.

The one exception is a **stopped** frame: a frame on which a wall or
the level's left end stops the player ("Moving", below). On a stopped
frame facing doesn't change, whatever is held. So a player moving left
who presses Right on the frame they meet a wall still faces left, and
turns on the next frame.

## Strokes and sinking

`vy` is down-positive, as in a trace. Each frame, in this order:

1. **Stroke.** If Jump is **pressed** on this frame (held on this frame
   and not on the frame before), `vy` is set to:

   | Held with the press | `vy` set to |
   |---|---|
   | Up | −2.5 (−640/256) |
   | Down | −1 (−256/256) |
   | Neither | −2 (−512/256) |

   The new value replaces the old one, whatever it was: a stroke while
   rising doesn't add to the rise, and a stroke while sinking stops the
   sinking at once. Holding Jump down doesn't stroke again; each stroke
   needs a new press.
2. **Sinking.** `vy` grows by 12/256 (0.046875). Then, if `vy` is
   positive (moving down), it is limited to at most the **sinking
   limit**: 1.25 (320/256), or 2 (512/256) on frames when Down is held.
3. The player moves by the new `vy`: in open water `y` changes by
   exactly the new `vy`. Ceilings and the floor can change that
   ("Moving", below).

So the stroke frame itself already has `vy` −500/256 (or −628/256 with
Up, −244/256 with Down).

Notes:

- Up held without a stroke changes nothing. Down held without a stroke
  only raises the sinking limit.
- The limit follows Down frame by frame. Letting go of Down while
  sinking at 2 brings `vy` straight back to 1.25 on that frame.
- A plain stroke (no Up or Down) moves the player up on the press frame
  and the 41 frames after it, 41.671875 px in all. `vy` reaches the
  limit 1.25 on the 70th frame counting the press frame as the 1st,
  if no new stroke comes first. With Up: up for 53 frames, 65.421875 px.
  With Down: up for 21 frames, 10.171875 px.
- Horizontal and vertical motion are independent: a stroke doesn't
  change `vx`, and Left or Right doesn't change `vy`.

## Moving

Underwater levels use a ceiling grid (`../formats/level-ground.md`,
"Ceiling grids"). The player's **point** is the column `⌊x⌋` and the
feet line `F` (`view-vertical.md`: level y rounded up to a whole px).
Moving up or down by whole px below always changes `y` by exactly that
many px, so the fraction `F` − `y` is kept.

### In the ground

A point is **in the ground** by a number of px, its **depth**, worked
out from the ground below the point (line `L`) and the feet's half (as
in "The ground below a point in a ceiling grid"):

- If the feet's half is a **ceiling half**: depth = `L` − `F` + 1. The
  ceiling line itself is in the ground: feet on the ceiling line are in
  by 1, and feet 1 px below it are not in. With no ground below, the
  depth counts as 4 or more.
- Otherwise: depth = `F` − `L`, how far the feet are below the surface.
  Feet on the surface are not in. With no ground below, the point is
  not in the ground.

A depth of 0 or less means the point is not in the ground.

### Each frame's move

After the target, the stroke, sinking and the new `vx` (the sections
above), in this order:

1. **The level's left end.** If `vx` is below 0 and `⌊x⌋` is less than
   18, `vx` becomes 0 and the frame is stopped. (Level x as in
   `../formats/level-ground.md`; the limit was 18 in every underwater
   area measured.) So once `⌊x⌋` is below 18 the player can't move left
   any more. A move that starts at 18 or more can still end a little
   below 18.
2. **Across.** Take the player's point at the start of the frame.
   - **Not in the ground.** Take the point the move would reach:
     column `⌊x + vx⌋`, the same `F`.
     - Not in the ground: `x` changes by `vx`.
     - In by 1 to 3: `x` changes by `vx`, and then, if that point's
       feet half is a ceiling half, the player moves **down** by the
       depth + 1, which puts the feet line 2 px below the ceiling line
       (`L` + 2). Otherwise the player moves **up** by the depth, onto
       the surface (`F` = `L`).
     - In by 4 or more: a **wall**. `vx` becomes 0 and the frame is
       stopped. `⌊x⌋` stays the same, but the fraction of `x` becomes the
       fraction of `x + vx` (with `vx` from before it was set to 0).
   - **In by 1 to 3.** No move across (`vx` is kept). The player moves
     up by the depth, whatever kind the feet's half is.
   - **In by 4 or more.** No move across, and nothing else in this
     step.
3. **Up and down.** Take the player's point after step 2.
   - **In the ground:** `y` changes by `vy`. Nothing else.
   - **Not in the ground, `vy` 0 or less** (rising): `y` changes by
     `vy`. If the new point is in the ground, the player moves down by
     whole px until `F` is what it was before this move, and `vy`
     becomes 0. That is a **ceiling bump**: the fraction of the move
     stays, so a bump can still move the player up by less than 1 px.
   - **Not in the ground, `vy` above 0** (sinking): `y` changes by `vy`.
     Take the ground below the new point, line `L`. The player is **on
     the floor** if the feet's half is not a ceiling half and `F` ≥ `L`,
     or if it is a ceiling half and `F` < `L`. Then `F` becomes `L`
     (moving by whole px) and `vy` becomes 0. Otherwise the player is
     not on the floor; if it was on the floor on the frame before, `vy`
     becomes 1/256 ("Leaving without a stroke", below).
4. **Facing**, as in "Facing": not on a stopped frame.

Notes:

- Walls stop horizontal motion only. `vy` goes on as usual on a wall
  frame, so a player can sink or rise along a wall.
- Ceilings act in two ways. Moving across under a ceiling that comes
  down by 1 to 3 px pushes the player down to 2 px below it (step 2).
  Rising into a ceiling stops the rise (step 3). In the levels measured,
  most pushes were 2 px (in by 1) and the rest 3 px (in by 2).
- A floor that rises by 1 to 3 px across one move is a step: the player
  moves up onto it and keeps `vx`. More than 3 px is a wall.
- In the measured levels the player was never in the ground at the start
  of a frame. That case, and step 3's ceiling-half landing, came up only
  with made-up ground (see "Checks").

### Example (synthetic)

All examples start with `y` a whole number (fraction 0) and the player
not in the ground.

- **Wall.** `x` = 100.75, `vx` 0.5 after the new speed. Column 101 has a
  floor 8 px above `F`: in by 8, a wall. `x` becomes 100.25, `vx` 0, and
  facing doesn't change on this frame.
- **Step.** `vx` 0.5, and the move reaches a column whose floor surface
  is 2 px above `F`: `x` changes by 0.5 and the player moves up 2 px,
  feet on the surface.
- **Under a ceiling.** The move reaches a column with a ceiling half at
  the feet whose ceiling line is `F` − 1 (in by 0: nothing happens), or
  `F` (in by 1): the player moves down 2 px, to `F` + 2 = `L` + 2.
  If it is `F` + 2 (in by 3): down 4 px, again to `L` + 2.
- **Bump.** Feet line `F` = `L` + 1, just below the ceiling line `L` of
  a ceiling half at the feet with `u` of 2 or more (so `L` and `L` + 1 are
  in the same band), and `vy` −1.25 after sinking.
  The move takes `y` to `L` − 0.25, so `F` = `L`: in by 1. The player
  moves down 1 px: `y` = `L` + 0.75, `F` = `L` + 1 again, `vy` 0. On the
  next frame sinking starts from 0 (`vy` 12/256).

## On the floor

The floor is the ground below the player (`../formats/level-ground.md`).
Heights here are measured upward from the floor surface, in pixels.

- **Landing.** After the frame's move, if the player's height above the
  floor is less than 1 px (including below the floor), the player is on
  the floor: the whole-pixel part of the height becomes 0, the fraction
  is kept, and `vy` becomes 0. This is step 3 of "Moving" with a floor
  at the feet. Unlike on land (`landing.md`) there is no 5 px margin,
  and the fraction isn't dropped.
- **Resting.** While on the floor the frame still runs as above. With no
  stroke, `vy` 12/256 moves the player down within the bottom pixel:
  each frame the fraction drops by 12/256, and when it would go below 0
  it wraps around (fraction + 1 − 12/256), with the height's whole part
  staying 0. `vy` is 0 at the end of each such frame.
- **Moving along the floor.** Horizontal speed and facing follow the
  rules above unchanged.
- **Leaving with a stroke.** A stroke works as in open water: the
  player moves up by the stroke frame's `vy` from the resting position,
  fraction included.
- **Leaving without a stroke** (the floor drops away under the player):
  on the first frame the player is no longer on the floor, it moves
  down 12/256 as on the floor, but its `vy` is then 1/256. From the next
  frame on sinking goes on as usual from there (13/256, 25/256, …).

## Worked values

Sinking at the limit 1.25, then Jump and Right pressed together on frame
P, Right held from P to P + 10, Down added from P + 6 and Jump pressed
again on P + 9:

| Frame | Held | `vx` (1/256) | `vy` (1/256) |
|---|---|---|---|
| P − 1 | – | 0 | 320 |
| P | Jump, Right | 16 | −500 |
| P + 1 | Right | 31 | −488 |
| P + 2 | Right | 46 | −476 |
| P + 3 | Right | 60 | −464 |
| P + 4 | Right | 74 | −452 |
| P + 5 | Right | 87 | −440 |
| P + 6 | Right, Down | 100 | −428 |
| P + 7 | Right, Down | 112 | −416 |
| P + 8 | Right, Down | 124 | −404 |
| P + 9 | Jump, Right, Down | 136 | −244 |
| P + 10 | Right, Down | 147 | −232 |

Later, sinking at 2 with Right and Down held. Down is let go on frame Q,
and on Q + 3 Jump is pressed with Up and no direction:

| Frame | Held | `vx` (1/256) | `vy` (1/256) |
|---|---|---|---|
| Q − 1 | Right, Down | 466 | 512 |
| Q | Right | 467 | 320 |
| Q + 1 | Right | 468 | 320 |
| Q + 2 | Right | 469 | 320 |
| Q + 3 | Jump, Up | 455 | −628 |
| Q + 4 | – | 441 | −616 |

On every row, `x` changes by `vx` and `y` by `vy`.

## Checks

Random input (runs of Left, Right or neither with Up, Down or neither,
and Jump presses of 1 to 6 frames), with enemies kept away from the
player, was compared every frame with these rules, starting each frame
from the game's own state: `x`, `y`, `vx`, `vy`, facing, and whether the
player was on the floor or at a wall.

- The game's own ground, 8 underwater areas, 2 runs each: 46 202 frames,
  all equal. They had 507 + 152 walls (feet's half not a ceiling half /
  a ceiling half), 1 497 ceiling bumps, 556 + 71 pushes under a ceiling
  (in by 1 / by 2), 12 steps, 858 landings, 4 leaves without a stroke
  and 804 frames at the left end. One run ended early, after 1 202
  frames, when an object that wasn't kept away knocked the player out.
- A copy with all underwater ground removed, 16 runs: 48 000 frames, all
  equal (907 at the left end).
- Four copies where every ground type was given made-up halves, 8 areas
  each: 96 000 frames, all equal. Every case of "Each frame's move" came
  up at least 17 times there, including a start in the ground (by 1, 2,
  3 and 4 or more), steps of 1 to 3 px, pushes from in by 1 to 3, and
  74 landings with a ceiling half at the feet.

## Not covered

- The right end of a level.
- Half bytes with bit 6 set (none in the levels measured).
- The second character, and the partner: in the areas measured the
  player swam alone, and a hit was a knock-out (`knock-out.md`).
- Areas where the player rides a helper animal underwater; enemies,
  currents and other objects.
- The swimming state's name in the trace vocabulary, and what a stroke
  looks like.
- Left and Right held together (the measuring setup can't hold both).
- A stroke on the floor that leaves the player still less than 1 px
  above it (a Down stroke from near the bottom of the pixel) wasn't
  seen.
- Entering and leaving the water, and the first frames of an area.
