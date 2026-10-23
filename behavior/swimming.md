# Swimming

Titles: `title-1`, `title-2`

How the player moves in the levels that are set underwater: swimming
left and right, strokes, sinking, walls, ceilings, the level's left end,
and resting on the floor. Frames are logic frames at 60 Hz, numbered as
in a trace. `x`, `vx`, `vy` and
`facing` are as in a trace: right and down are positive, speeds in px
per frame. "Left", "Right", "Up", "Down", "Jump", "Action" are the
neutral inputs `L`, `R`, `U`, `D`, `J`, `A` of the scenario format.
Measured on `title-1` with the first character swimming alone, in
eight underwater areas; `title-2` is in "In `title-2`" near the end.

Speeds are always whole multiples of 1/256 px per frame. The rules below
are exact in those units, so this spec writes speeds both ways
(512/256 = 2 px per frame).

Underwater, the player is swimming all the time. There is no walking,
running, jumping, rolling or turn: the rules below replace those on
land.

## Where the player swims

Swimming belongs to a whole **area** (a level, or a room the player
enters from one), not to a part of it. When an area begins, it either
starts the player swimming or it doesn't. In an area that starts the
player swimming, the player swims everywhere: from the left edge to the
right, and from the top of the grid to the bottom. There is no water
surface, and there is no line or region inside an area where the player
starts or stops swimming, so there is nothing to cross. Nothing inside
such an area turns swimming off. The player swims until the area ends
or they are knocked out (`knock-out.md`). When the player goes on to
another area, that area's start decides again.

Which areas are underwater, in `title-1`:

- Every area that starts the player swimming has a ceiling grid for its
  ground (`../formats/level-ground.md`, "Ceiling grids").
- No area whose ground is a column grid or a row grid starts the
  player swimming, and the player doesn't swim there.
- All but one of the areas on a ceiling grid start the player swimming.
  The exception is a short bonus area. The player starts it on a helper
  animal and rides underwater for the whole area. There are no enemies,
  and the player never swims there. After about a minute (about 3 680
  frames in the runs measured) the area ends and the player is back in
  an area where they walk. Riding underwater is not covered here.

So an imported area needs water over the whole level exactly when its
ground is a ceiling grid and it doesn't start the player riding.

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

## In a trace

Every frame on which the player is swimming has the state `swim`
(`../traces/VOCABULARY.md`): in open water, at a wall, under a ceiling
and on the floor alike. None of the land states (`idle`, `walk`, `run`,
`jump`, `fall`, `roll`, ...) appears while swimming.

The first character's pictures while swimming are in `player-frames.md`,
"Swimming".

- `ground` is 1 on a frame that ends with the player on the floor ("On
  the floor": the landing frame and every resting frame), and 0 on every
  other frame. A wall or a ceiling is not ground, so a frame stopped by
  a wall has `ground` 0 unless the player is also on the floor.
- `vy` is the frame's `vy` after its move, as everywhere in a trace: 0
  on the floor and after a ceiling bump, and +1/256 on the first frame
  of leaving without a stroke. On resting frames `y` still changes
  while `vy` is 0: by +12/256, or by −244/256 on a frame where the
  fraction wraps around.

Events while swimming:

| Event | Frames |
|---|---|
| `stroke` | Every frame with a stroke (Jump pressed, "Strokes and sinking"), also from the floor and also when a ceiling bump sets `vy` to 0 on that same frame |
| `land` | The frame the player comes onto the floor (`ground` 0 to 1) |
| `leave_ground` | The first frame no longer on the floor without a stroke (`ground` 1 to 0, "Leaving without a stroke") |
| `turn` | Every frame on which facing changes ("Facing"); there is no turn movement |

A stroke that takes the player off the floor is a `stroke`, not a
`leave_ground`; its row has `ground` 0. `jump_start`, `jump_release`,
`roll_start`, `roll_end` and `bounce` never appear while swimming.

### Example (synthetic)

The player rests on the floor facing left with the fraction of its
height at least 24/256, holds nothing on frames 0 and 1, and presses Jump
on frame 2 (held through frame 3). Rows relative to the start, with `x`,
`vx` and `facing` left out (`vx` is 0, facing stays left):

| Frame | Input | `y` | `vy` | State | `ground` | Events |
|---|---|---|---|---|---|---|
| 0 | `.` | 0.046875 | 0 | `swim` | 1 | |
| 1 | `.` | 0.09375 | 0 | `swim` | 1 | |
| 2 | `J` | −1.859375 | −1.953125 | `swim` | 0 | `stroke` |
| 3 | `J` | −3.765625 | −1.90625 | `swim` | 0 | |

The scenario `swim-strokes` (`../scenarios/swim-strokes.scn`) records
these rows in open water, with a `title-1` reference trace and a `title-2`
one ("In a trace (`title-2`)").

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
- Trace rows (state, `ground`, events) on the same kind of random input
  with Jump pressed for 1 to 6 frames: 8 underwater areas, 2 runs each
  on the game's own ground and 2 more on the copy without ground, 3 000
  frames per run: 93 000 frames in 31 runs (the 32nd stopped at a
  knock-out, as above, and isn't counted). Every `stroke` row had the
  stroke's own `vy` (or 0 after a bump, 417 times) and no other row did,
  every `ground` 1 row had `vy` 0, and every `leave_ground` row had `vy`
  +1/256. There were 4 554 strokes (25 from the floor, all with `ground`
  0), 781 `ground` 1 frames, 21 `land`, 19 `leave_ground` and 1 080
  `turn`.
- Which areas swim: every area the title can load (199 areas) was
  entered and watched for 300 frames. The player swam in exactly 8 of
  them, all on a ceiling grid. The 9th area on a ceiling grid started
  the player riding, and in the other 190 the player never swam. In each of the 9 areas on a ceiling grid, 600
  placements went to random open-water points anywhere in the grid (any
  level x from 0 to 6 399, about 15 % in the top 4 bands), each followed
  by 60 frames of random input with enemies kept away. In the 8
  swimming areas the player swam on every frame of all 4 800
  placements. In the riding area the player rode on every frame of all
  600.

## In `title-2`

Everything in this section was measured on `title-2` only, in open water
in one underwater level: the only one of the title's 48 level entries
that starts the player swimming. The player was the lead character,
swimming alone, with enemies kept away. Speeds are whole multiples of
1/256 px per frame, written both ways. Frames, inputs and the trace
fields are as at the top of this file.

In open water, swimming works as in `title-1` (the sections above) except
for the target speeds, the Up stroke, the sinking limit with Down, and
facing. Walls, floors, ceilings and the sides of the screen have their own
rules in `title-2` ("Walls, floors and ceilings (`title-2`)").

### Horizontal speed (`title-2`)

The speed rule is the one in "Horizontal speed" (step = gap ÷ 32
rounded toward zero; if the step is 0, the speed becomes the target),
and in open water `x` changes by exactly the new `vx`. Only the targets
differ:

| Held on this frame | Held on the frame before | Target |
|---|---|---|
| Right | Action with Left or Right | +2.5 (+640/256) |
| Right | anything else | +1.5 (+384/256) |
| Left | Action with Left or Right | −2.5 (−640/256) |
| Left | anything else | −1.5 (−384/256) |
| No direction | anything | 0 |

So Action makes the player swim faster, one frame late: the first frame
with Action and a direction still uses 1.5, and the first frame after
letting go of Action (with a direction still held) still uses 2.5. Action
held with no direction on the frame before doesn't count. Up, Down and
Jump make no difference to the target.

Consequences:

- From rest, holding Right gives 12/256 on the first frame, then 23, 34,
  44, 54, 64, 74, 83, …
- From rest, holding Right and Action together gives 12/256 on the first
  frame, then 31, 50, 68, 85, 102, 118, 134, …

### Strokes and sinking (`title-2`)

As in "Strokes and sinking", with two changes:

1. **Up stroke.** A stroke with Up held sets `vy` to −2.75 (−704/256),
   so the stroke frame itself has `vy` −692/256. Plain strokes (−2,
   −512/256) and Down strokes (−1, −256/256) are as in `title-1`.
2. **Sinking limit.** The limit is 1.25 (320/256) or 2.25 (576/256).
   It is 2.25 on a frame on which Down is held, and on a frame on which
   Up is held if the limit was 2.25 on the frame before. On every other
   frame it is 1.25. So after Down, switching straight to Up keeps the
   deeper limit for as long as Up is held: the player can sink at up to
   2.25 while holding Up. One frame with neither Up nor Down brings the
   limit back to 1.25, and Up after that doesn't bring it back.

As in `title-1`, when the limit drops below `vy` (for example on letting
go of Down while sinking at 2.25), `vy` goes straight to the limit on
that frame. Action, and Jump held without a new press, change nothing in
`vy`.

### Facing (`title-2`)

Facing doesn't follow the held direction at once. It changes through a
**turn**:

- A turn starts on a frame on which Left or Right is held, opposite to
  the player's facing, and no turn is running. Call that frame **S**.
- Facing changes on S + 5. That frame is the `turn` frame in a trace.
- The turn runs until S + 11. From S + 12 on, a new turn can start.
- Once started, a turn always finishes, even if the direction is let go
  or reversed. Holding the opposite direction during a turn starts
  nothing. If it is still held on S + 12, a new turn starts then, and
  facing changes back on S + 17.
- With no direction held, or the held direction the same as facing, no
  turn starts.
- The turn doesn't change speed. `vx` follows the target from S on, as
  in "Horizontal speed (`title-2`)", whatever the facing.

A **stroke during a turn** (Jump pressed on S + j, the turn's first
stroke) shortens the turn. Facing changes on frame F, and a new turn can
start from frame N:

| Stroke on | S + 1 | S + 2 | S + 3 | S + 4 | S + 5 | S + 6 | S + 7 | S + 8 | S + 9 | S + 10 | S + 11 |
|---|---|---|---|---|---|---|---|---|---|---|---|
| F | S + 2 | S + 3 | S + 4 | S + 4 | S + 5 | S + 5 | S + 5 | S + 5 | S + 5 | S + 5 | S + 5 |
| N | S + 7 | S + 7 | S + 8 | S + 9 | S + 9 | S + 10 | S + 11 | S + 11 | S + 12 | S + 10 | S + 11 |

A stroke on S itself, or on a frame up to 30 frames before S (all that
was measured), changes nothing:
facing changes on S + 5 and a new turn can start from S + 12. A new turn
started on N is an ordinary turn (its own S is N).

### Walls, floors and ceilings (`title-2`)

Measured in the same level, where the ground is a row grid. The
player's **point** is the whole-pixel part of the position, (⌊`x`⌋,
⌊`y`⌋), with ⌊`y`⌋ as the feet's level y. **G** is the level y of the
ground below a point in that grid (`../formats/level-ground.md`, "Row
grids (`title-2`)"). It can be above the point: then the point is **in
the ground** by ⌊`y`⌋ − G px. With no ground below, the point is not in
the ground.

After the new `vx` and `vy` (the sections above), in this order:

1. **Across.** Take the point the move would reach, (⌊`x` + `vx`⌋,
   ⌊`y`⌋), the `y` from before the frame, and let U = ⌊`y`⌋ − G there.
   This is done on every frame, also when ⌊`x` + `vx`⌋ is the column the
   player is already in. Columns passed over on the way aren't looked at.
   - **U 0 or less**, or no ground below: `x` changes by `vx`.
   - **U 1 to 4: a step.** `x` changes by `vx`, and the whole-pixel part
     of `y` becomes G. The fraction of `y` is kept, so the player is
     lifted by exactly U px.
   - **U 5 or more: a wall.** `x` becomes ⌊`x`⌋ (the fraction is
     dropped) and `vx` becomes 0. `y` is not changed by this step.
2. **Up and down.** `y` changes by `vy`, from the `y` after step 1. Take
   G below the new point, at the `x` after step 1.
   - **`vy` 0 or more** (sinking). The player **lands** if there is
     ground below and ⌊`y`⌋ ≥ G, or ⌊`y`⌋ ≥ G − 3 when the surface found
     is in a sloped half (a half whose surface is not at one height
     across its 16 columns). Landing sets `y` = G exactly (the fraction
     becomes 0) and `vy` = 0, and the player is on the floor (`ground` 1
     in a trace). Otherwise, if the player was on the floor at the end
     of the frame before, `vy` becomes 1/256.
   - **`vy` below 0** (rising). If the new point is in the ground, and the
     surface found is not passable from below (bit 14 of its half word),
     that is a **ceiling bump**: the whole-pixel part of `y` goes back to
     what it was before this move, the fraction of the moved `y` is kept,
     and `vy` becomes 0. Ground that is passable from below doesn't stop
     a rising player.
3. **Held on screen.** Let **L** be ⌊`V`⌋, the left edge of the picture
   (`view.md`, "The view's state (`title-2`)"). If ⌊`x`⌋ is less than
   L + 16 it becomes L + 16; if it is more than L + 240 it becomes
   L + 240. In both cases the fraction of `x` and `vx` are kept, and this
   is not a wall. So the player is kept at least 16 px inside each side of
   the 256-px-wide picture.

Notes:

- On a wall frame `vy` goes on as usual, so a player can sink or rise
  along a wall. From the next frame `vx` starts again from 0 by the rule
  of "Horizontal speed (`title-2`)".
- A step while sinking always lands on the same frame: after the lift
  ⌊`y`⌋ is G, and moving down keeps ⌊`y`⌋ at G or more. A step while
  rising lifts the player and the rise goes on.
- Ceilings stop only rising. Moving across into ground at the feet is a
  step or a wall, by U as in step 1. Unlike `title-1`, nothing pushes
  the player down.
- **Resting.** On the floor with no stroke, sinking adds 12/256 to `vy`,
  the move takes `y` 12/256 below the surface, and the player lands
  again: `y` = G and `vy` 0 on every such frame. Unlike `title-1`, the
  fraction of `y` is 0 while resting. A stroke from the floor works as
  in open water (rising frames never land).
- **Leaving without a stroke** (the floor drops away): on the first
  frame off the floor the player moves down by the `vy` of that frame
  (12/256 from rest) and `vy` is then 1/256; sinking goes on from there
  (13/256, 25/256, …), as in `title-1`.
- A player in the ground at the start of a frame (seen in controlled
  frames) is handled by step 1 as well: in its own column, in by 1 to 4
  px lifts the player and 5 or more is a wall.

Differences from `title-1` ("Moving"): the step limit is 4 px, not 3; a
wall drops the fraction of `x`; landing drops the fraction of `y` and has
a 3 px margin on sloped halves; there is no push down under a ceiling;
and instead of a fixed left end the player is held on screen.

### Example (synthetic, `title-2`)

Made-up ground; `vx` and `vy` are the frame's new speeds, after
"Horizontal speed (`title-2`)" and "Strokes and sinking (`title-2`)".

- **Wall.** `x` = 100.25, `y` = 200, `vx` = 384/256, `vy` = 0. Column
  101 has G = 192: U = 8, a wall. `x` = 100, `vx` = 0, `y` = 200.
- **Step.** `x` = 100.25, `y` = 200.5, `vx` = 384/256, `vy` = 0. Column
  101 has G = 197: U = 3, a step to `y` = 197.5 with `x` = 101.75. Then
  the feet are 0 px from G, so the player lands: `y` = 197, `vy` 0,
  `ground` 1.
- **Step while rising.** As above but `vy` = −512/256: lifted to 197.5,
  then `y` = 195.5, `vy` stays −512/256, not on the floor.
- **Landing on a flat half.** G = 200. `y` = 199.75 with `vy` = 128/256
  ends at 200.25: ⌊`y`⌋ = 200 ≥ G, so the player lands with `y` = 200.
  From `y` = 199.25 the move ends at 199.75: ⌊`y`⌋ = 199, not landed,
  `vy` stays 128/256.
- **Landing on a sloped half.** G = 200 in a sloped half. `y` = 196.75
  with `vy` = 128/256 ends at 197.25: ⌊`y`⌋ = 197 ≥ G − 3, so the player
  lands with `y` = 200.
- **Leaving the floor.** Resting at `y` = 200 on G = 200 with `x` =
  100.25, `vx` = 384/256, `vy` = 12/256; column 101 has no ground below.
  `x` = 101.75, `y` = 200.046875 (200 + 12/256), not on the floor, `vy`
  = 1/256.
- **Bump.** `y` = 210.25, `vy` = −384/256. The move ends at 208.75. At
  ⌊`y`⌋ = 208 the point is in the ground (G = 150, ground from 150 down
  to 209). ⌊`y`⌋ goes back to 210: `y` = 210.75, `vy` 0. If that ground
  is passable from below, `y` = 208.75 and `vy` stays −384/256.
- **Held on screen.** L = 256, `x` = 272.75, `vx` = −384/256, no ground
  near. The move reaches 271.25. ⌊`x`⌋ = 271 < 272, so `x` = 272.25 and
  `vx` stays −384/256.

### Worked values (`title-2`)

Sinking, already moving right at 77/256, then Jump and Right pressed
together on frame P, with Action added on P + 5 and an Up stroke on
P + 8:

| Frame | Held | `vx` (1/256) | `vy` (1/256) |
|---|---|---|---|
| P − 1 | – | 77 | 148 |
| P | Jump, Right | 86 | −500 |
| P + 1 | Right | 95 | −488 |
| P + 2 | Right | 104 | −476 |
| P + 3 | Right | 112 | −464 |
| P + 4 | Right | 120 | −452 |
| P + 5 | Right, Action | 128 | −440 |
| P + 6 | Right, Action | 144 | −428 |
| P + 7 | Right, Action | 159 | −416 |
| P + 8 | Jump, Up, Right, Action | 174 | −692 |
| P + 9 | Right | 188 | −680 |
| P + 10 | Right | 194 | −668 |

On P + 5 the target is still 1.5; from P + 6 to P + 9 it is 2.5; on
P + 10 it is 1.5 again.

A turn with a stroke, facing right and moving right at 65/256. Left is
held from frame S, Jump is pressed on S + 2, and Right is held from
S + 7:

| Frame | Held | `vx` (1/256) | `vy` (1/256) | Facing |
|---|---|---|---|---|
| S − 1 | – | 65 | −188 | R |
| S | Left | 51 | −176 | R |
| S + 1 | Left | 38 | −164 | R |
| S + 2 | Jump, Left | 25 | −500 | R |
| S + 3 | Left | 13 | −488 | L |
| S + 4 | Left | 1 | −476 | L |
| S + 5 | Left | −11 | −464 | L |
| S + 6 | Left | −22 | −452 | L |
| S + 7 | Right | −10 | −440 | L |
| S + 8 | Right | 2 | −428 | L |
| S + 9 | Right | 13 | −416 | L |
| S + 10 | Right | 24 | −404 | L |
| S + 11 | Right | 35 | −392 | L |
| S + 12 | Right | 45 | −380 | R |

The stroke on S + 2 brings the change of facing to S + 3, and the next
turn can start from S + 7, so the Right turn starts on S + 7 and facing
changes on S + 12.

Sinking with Down held, then Up, then neither (`vx` 0):

| Frame | Held | `vy` (1/256) |
|---|---|---|
| Q − 2 | Down | 556 |
| Q − 1 | Down | 568 |
| Q | Down | 576 |
| Q + 1 | Up | 576 |
| Q + 2 | Up | 576 |
| Q + 3 | Up | 576 |
| Q + 4 | Up | 576 |
| Q + 5 | – | 320 |
| Q + 6 | – | 320 |

On every row, `x` changes by `vx` and `y` by `vy`.

### In a trace (`title-2`)

As in "In a trace": every swimming frame has the state `swim`, and
`stroke` marks every frame with a stroke. `turn` marks the frame on which
facing changes (S + 5, or F after a stroke during the turn), not the
frame the turn starts.

The `title-2` reference trace for `swim-strokes`
(`../traces/reference/title-2/swim-strokes.trace`) shows this. Its rows
follow this section exactly from the scenario's start (`vx` 0, `vy`
1.25, facing right). The strokes on frames 20, 50 and 85 have `vy` −500,
−692 and −244 (in 1/256 px). Holding Down on frames 70 to 99 changes
only the stroke on 85: `vy` is still below 0 on frame 99. Left from
frame 130 starts a turn, and facing changes on frame 135. The top speed
is 1.5 (384/256), and the trace ends at `x` 126.6796875, `y` −39.046875.

The path stays between `x` 0.046875 and 133.6875 and between `y`
−90.09375 and 25. Because of the stronger Up stroke, it goes 90 px above
the start, 8 px higher than the 82 px in the scenario's `requires`
(which follows the `title-1` path). Ground 122 px above the start
(82 + 40) is still 32 px above the path's highest point; with a ceiling
there the trace is the same.

### Checks (`title-2`)

- Random input (runs of Left, Right or neither with Up, Down or neither,
  Action held in about 40 % of the runs, Jump presses of 1 to 6 frames),
  enemies kept away, 32 runs of 2 000 frames from a point in open water:
  64 000 frames. Every frame was compared with this section, starting
  from the game's own state on the frame before: `x`, `y`, `vx`, `vy`
  and facing. 3 626 frames with the player on the floor or against a
  wall were left out. Of the other 60 374, 55 447 were equal in every
  field. The rest were all ceilings and walls stopping or pushing the
  player (4 926 frames, covered by "Walls, floors and ceilings
  (`title-2`)" and the checks of it below) and 1 frame on which a turn
  started late just after the player had been against a wall. On 24
  frames facing wasn't compared, because a second stroke had come
  during the same turn.
- Turns: 58 scripted cases (opposite direction after 0 to 30 frames of
  the other, released after 1 to 14 frames, a stroke on every frame of
  the turn with either direction held, strokes 0 to 30 frames before a
  turn, two strokes in one turn): every change of facing on the frame
  given here.
- The worked values above, and 180 frames around them: all equal.
- Walls, floors and ceilings, by random input as in the first check from
  five points in the level (four in open water near walls, floors and
  ceilings, and the level's start on the floor), 16 runs of 3 000 frames
  from each: 240 000 frames, every frame compared, contact included:
  `x`, `y`, `vx`, `vy` and whether the player was on the floor or
  against a wall. All were equal. They held 8 708 wall frames, 7 421
  ceiling bumps, 2 392 landing or resting frames, 25 steps, 10 frames
  leaving the floor without a stroke and 16 220 frames held on screen
  (10 943 on the right, 5 277 on the left). Facing differed on 28
  frames, all turns starting late near a wall (not covered).
- Walls, floors and ceilings in controlled single frames: the player put
  in open water next to one made-up cell of the level's grid, with its
  speeds set, for one frame. Across (U from −4 to 14 px, moving right and
  left, into flat, full and sloped halves): 4 000 frames. Landing (the
  move ending from 4 px below to 6 px above the surface, on flat, sloped
  and passable halves): 4 000. Rising into a full cell, a half-height
  cell and two kinds of passable cell: 4 000, with 671 bumps; the 683
  rises into passable cells all went on. Resting and leaving the floor,
  with and without a stroke: 2 000. All equal. As controls, a step
  limit of 3 or 5 px, or a margin of 0 or 4 px on sloped halves, each
  gave 19 to 58 differences in 1 000 of these frames.

### Not covered in `title-2`

- Moving across into ground that is passable from below: whether it
  stops the player depended on the half's shape and the direction of the
  move. Steep faces (none in the level measured), and the level's own
  ends.
- Whether the hold on screen (step 3 of "Walls, floors and ceilings
  (`title-2`)") uses the picture of the frame itself or of the frame
  before: on every frame on which the player was held, the picture
  didn't move. Whether the player is held on screen out of water too.
- A turn doesn't start while the player is against a wall; the details
  aren't covered.
- Which levels and areas start the player swimming, and whether the
  player swims everywhere in them. Only one level was measured.
- A second stroke during the same turn.
- The second character, the partner, enemies and other objects.
- Left and Right held together (the measuring setup can't hold both).

## Not covered

- The right end of a level.
- Half bytes with bit 6 set (none in the levels measured).
- The second character, and the partner: in the areas measured the
  player swam alone, and a hit was a knock-out (`knock-out.md`).
- Areas where the player rides a helper animal underwater; enemies,
  currents and other objects.
- What a stroke looks like on screen.
- Traces of a hit while swimming (a knock-out, `knock-out.md`).
- Left and Right held together (the measuring setup can't hold both).
- A stroke on the floor that leaves the player still less than 1 px
  above it (a Down stroke from near the bottom of the pixel) wasn't
  seen.
- The first frames of an area, and how an image records which areas
  start the player swimming and which one starts them riding.
- Areas on a column grid or a row grid were checked only from their
  start (first 300 frames), not all over.
