# Swimming

Titles: `title-1`

How the player moves in the levels that are set underwater: swimming
left and right, strokes, sinking, and resting on the floor. Frames are
logic frames at 60 Hz, numbered as in a trace. `x`, `vx`, `vy` and
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
4. The player moves by the new speed: `x` changes by exactly the new
   `vx` on that frame.

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
3. The player moves by the new `vy`.

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

## On the floor

The floor is the ground below the player (`../formats/level-ground.md`).
Heights here are measured upward from the floor surface, in pixels.

- **Landing.** After the frame's move, if the player's height above the
  floor is less than 1 px (including below the floor), the player is on
  the floor: the whole-pixel part of the height becomes 0, the fraction
  is kept, and `vy` becomes 0. Unlike on land (`landing.md`) there is no
  5 px margin, and the fraction isn't dropped.
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

## Not covered

- Walls, ceilings and the ends of a level. Touching a wall stops
  horizontal motion; touching a ceiling pushes the player down by whole
  pixels (2 or 3 px were common). Neither is specified here.
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
