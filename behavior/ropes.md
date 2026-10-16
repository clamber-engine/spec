# Ropes

Titles: `title-1`

A **rope** hangs straight down and sways slowly from side to side. The
player catches it by jumping into it. Holding it, the player climbs up
and down, turns from one side of it to the other, jumps off it, or
slides off its lower end. This file covers the rope's own
motion, catching, everything the player does while holding a rope, and
the jump or fall that follows, up to landing or the next catch.

Frames are logic frames at 60 Hz, numbered as in a trace. `x` grows to
the right; `y` and `vy` are as in a trace (down is positive). Positions
are **level** positions in px, with `y` measured down from the top of
the level as in `view-vertical.md`. The player's **feet line** `F` is
its `y` rounded up to a whole px (`view-vertical.md`). Speeds are whole
multiples of 1/256 px per frame; this file writes them both ways
(384/256 = 1.5).

"Left", "Right", "Up", "Down", "Jump", "Action" are the neutral inputs
`L`, `R`, `U`, `D`, `J`, `A` of the scenario format. A **press** of Jump
is a frame on which Jump is held and was not held on the frame before.

Measured with the first character leading, on the two ropes of the
second level, in 1 500 runs of random input: 300 starting on each rope
and 900 starting on the ground near them (walking, running and jumping
into the ropes). The rules below matched every frame of them: 37 789
frames on a rope (950 jumps off, 340 slides off the end, 542 frames held
down by the top of the view, 8 308 turning frames), 35 048 frames in the
air after a rope, 826 catches out of 198 430 jump frames, and 283 305
frames of rope motion.

## The rope

A rope has:

- a position `x`, kept to 1/256 px: the vertical line the player holds;
- a **bottom end** `B`: a whole-px level `y`. The rope reaches up from
  there without end; it doesn't move vertically;
- a horizontal speed `vx` and a **target speed** `s` (+ or − a rope
  speed);
- a **centre** `c` (whole px) and a **swing limit** to each side,
  `w_left` and `w_right` (whole px).

Both ropes measured had a rope speed of 0.1875 (48/256) and swing limits
of 3 px each way. Their bottom ends were at level `y` 368 and 256. Where
these values come from in the level data is not covered.

On every frame, in this order:

1. If `s` is negative and ⌊`x`⌋ < `c` − `w_left`, or `s` is 0 or more
   and ⌊`x`⌋ > `c` + `w_right`, the target speed changes sign.
2. **gap** = `s` − `vx`, in 1/256 px per frame. **step** = gap ÷ 32,
   rounded toward zero to a whole number. If step is 0, `vx` becomes
   `s`; otherwise `vx` changes by step.
3. `x` changes by the new `vx`.

So a rope at full speed keeps going for about 2.3 px after its target
turns round, while it slows down, and sways over about 12 px in all
(from about 5.5 px left of the centre to about 6.4 px right of it).

A rope moves after the player on each frame: on frame `n` the player
sees the rope where it was at the end of frame `n` − 1.

### Example

Synthetic. Centre 1000, swing limits 3 each way, rope speed 48/256, moving
right at full speed (`vx` = `s` = 48):

| Frame | Start `x` | Target after step 1 | `vx` after step 2 (1/256) | End `x` |
|---|---|---|---|---|
| 1 | 1004.16015625 | −48 (⌊`x`⌋ 1004 > 1003) | 45 | 1004.3359375 |
| 2 | 1004.3359375 | −48 | 43 | 1004.50390625 |
| 3 | 1004.50390625 | −48 | 41 | 1004.6640625 |
| … | | | | |
| 15 | 1005.91015625 | −48 | 17 | 1005.9765625 |
| 16 | 1005.9765625 | −48 | 15 | 1006.03515625 |
| 17 | 1006.03515625 | −48 | 14 | 1006.08984375 |
| … | | | | |
| 30 | 1006.44140625 | −48 | 1 | 1006.4453125 |
| 31 | 1006.4453125 | −48 | 0 | 1006.4453125 |
| 32 | 1006.4453125 | −48 | −1 | 1006.44140625 |
| … | | | | |
| 48 | 1005.9140625 | −48 | −17 | 1005.84765625 |
| 49 | 1005.84765625 | −48 | −48 | 1005.66015625 |

The step is −2 while the gap is −64 to −95 and −1 while it is −32 to
−63. On frame 49 the gap is −31, the step is 0, and `vx` becomes −48 at
once.

## Catching a rope

A rope can be caught only from a **jump**: a jump from the ground
(`jump-input.md`) or a jump off a rope (below). Falls after walking off a
ledge, rolls off a ledge and slides off a rope's end never catch one.
Other airborne states are not covered.

At the end of every frame, each rope checks whether it has the player
**in reach**:

- `F` ≤ `B` + 24 (the feet line is no more than 24 px below the bottom
  end; there is no upper limit), and
- |⌊player `x`⌋ − ⌊rope `x`⌋| is less than **12** if |player `vx`| is
  less than 0.25 (64/256), and less than **4** otherwise.

On a frame of a jump (from the frame after the press on), after the
player has moved as usual in the air (`jump-arc.md`, `air-control.md`):

- If the frame ended on the ground (a landing, or the take-off frame of
  a jump from the ground, which is still on the ground), nothing is
  caught.
- Otherwise, if a rope had the player in reach at the end of the frame
  before, the player catches it, **unless** it is the rope last held
  (see below).

The **rope last held** is the rope the player most recently held. It is
forgotten on any frame that ends on the ground, and on a jump frame for
which no rope had the player in reach at the end of the frame before.
So after jumping off a rope, the player can't catch the same rope again
until it has been out of its reach for one frame; another rope can be
caught at once.

On the frame of the catch the player keeps the position it moved to;
`vx` and `vy` become 0. From the next frame on it holds the rope.

## Holding a rope

While the player holds a rope, `vx` is 0 and the player is not on the
ground. On each frame, in this order:

1. **Position on the rope.** The whole px of the player's `x` become the
   whole px of the rope's `x` (as at the end of the frame before); the
   player's own fraction of a px is kept.
2. **Sliding off the end.** If `F` > `B` + 24, the player lets go: the
   player falls from the next frame (see "After a rope"). This frame
   still does steps 4 to 7 (but see "Turning").
3. **Jumping off.** If Jump is pressed on this frame while Left or Right
   is held, the player jumps off (see "Jumping off") instead of steps 4
   to 7.
4. **Climbing target.** The target climbing speed `t` (up positive) is:

   | Held | `t` without Action | `t` with Action held |
   |---|---|---|
   | Down | −2.5 (−640/256) | −3.5 (−896/256) |
   | Up, or Jump without Left or Right | +1.5 (+384/256) | +2.5 (+640/256) |
   | anything else | 0 | 0 |

   Down wins over Jump. Jump held together with Left or Right (and
   without Up or Down) gives 0. Only Action held on this frame counts
   (not the run memory below).
5. **Climbing speed.** With `u` = −`vy` (up positive), in 1/256 px per
   frame: **gap** = `t` − `u`; **step** = gap ÷ 8, rounded toward zero.
   If step is 0, `u` becomes `t`; otherwise `u` changes by step. Then
   `vy` = −`u`.
6. **Top of the view.** If `t` is 0 or more and `F` < ⌊`V`⌋ + 41, where
   `V` is the view's position (`view-vertical.md`) as it was at the end
   of the frame before, the player moves down by the whole number of px
   that makes `F` = ⌊`V`⌋ + 41 (its fraction is kept).
7. The player moves by `vy`.

On the ropes measured nothing but step 6 limited climbing up: holding Up there, the player moves down by 1 or
2 px and up by 1.5 on every frame, and its feet line ends each frame 39
or 40 px below the view's top row. Climbing down ended with sliding off
the end: on both ropes that point was above the ground below.

### Turning

The player faces the rope from one side. Holding the direction the player
faces makes it swing round to the other side and face the other way:

- On a frame holding the rope (not a jump-off frame) where the player
  isn't already turning, if Left or Right is held (without the other)
  and is the direction the player faces, a **turn** starts on that
  frame.
- A turn lasts 8 frames (the frame it starts on is the first). Facing
  changes on its 5th frame. A new turn can start on the frame after the
  8th.
- Climbing goes on as usual during a turn, and a jump off can start on
  any frame of it (the jump sets the facing).
- On the frame the player slides off the end, a turn in progress stops
  and no turn starts; facing doesn't change on that frame.

The player also remembers the **rope direction**: Left, Right or none,
the direction held on the most recent frame spent holding a rope (steps
1–7, the jump-off frame included). It is kept through jumps, falls and
everything on the ground until the player next holds a rope. On the
frame of a catch, if the rope direction is the direction the player
faces at the end of that frame (`air-control.md`, "Facing"), a turn
starts on that catch frame (facing changes 4 frames later). So a
player who jumps off a rope to the right and catches the next rope
still facing right swings round it at once.

## Jumping off

On a frame where Jump is pressed while Left or Right is held (frame
**P**), after step 1:

- Facing becomes the direction held.
- `vy` = −(6.125 + `k`), where `k` is the step of step 5 taken from a
  speed of 0 toward the target `t` of step 4 worked out **without** Jump
  (from Up, Down and Action only): `k` = `t` ÷ 8 rounded toward zero.
  So `vy` is −6.125 (−1568/256) with no Up or Down, −6.3125 with Up,
  −6.4375 with Up and Action, −5.8125 with Down and −5.6875 with Down
  and Action. The speed the player was climbing at doesn't count.
- If `t` (without Jump) is 0 or more, step 6 applies.
- The player moves by `vy` on frame P itself. `vx` stays 0 and `x` is
  the rope's (step 1).
- The **run memory** is set (see "After a rope").

From P + 1 on, the player is in a jump with P as its press frame:
gravity as in `jump-arc.md` (0.28125 while Jump has been held on every
frame since P, 0.4375 after; fastest fall 8), horizontal speed by the
rule of `air-control.md` with the targets of "After a rope", facing as
in `air-control.md`, landing as in `landing.md`. It can catch ropes
(see "Catching a rope").

### Example

Holding a rope with `vy` 0, facing left; Jump and Left pressed on
frame P and held:

| Frame | `vy` | `vx` | Change in `y` | Change in `x` |
|---|---|---|---|---|
| P | −6.125 | 0 | −6.125 | 0 (rope's whole px) |
| P + 1 | −5.84375 | −0.375 | −5.84375 | −0.375 |
| P + 2 | −5.5625 | −0.703125 | −5.5625 | −0.703125 |
| P + 3 | −5.28125 | −0.98828125 | −5.28125 | −0.98828125 |

## After a rope

The player has a **run memory**. Jumping off a rope sets it. It stays
set in the air and while holding ropes; it is cleared at some point
after the player is back on the ground (when exactly is not covered).

While the run memory is set, every target speed below is the one for
Action held, whether Action is held or not:

- **In a jump off a rope:** the air speed rule of `air-control.md` with
  targets ±3 (±768/256) for Left/Right and 0 with no direction. (The run
  memory is always set in such a jump.)
- **In a fall after sliding off the end:** from the frame after the
  let-go frame, `vy` increases by 0.4375 per frame up to 8 and the player
  moves by it (the fall starts from the `vy` of the let-go frame); the
  air speed rule of `air-control.md` with targets ±2 for Left/Right, ±3
  with Action held or the run memory set, 0 with no direction. Facing
  doesn't change during this fall, whatever is held. It ends with a
  landing (`landing.md`); it can't catch a rope.

## Not covered

- The second character holding a rope, and the follower on ropes.
- Enemies and other contacts while holding a rope.
- Other kinds of objects the player can hold (the second level has at least one
  more kind, not identified); ropes in other levels.
- Airborne states other than jumps that might catch a rope.
- When the run memory is cleared on the ground, and its effect there.
- Which placement-list references place ropes, and where a rope's
  centre, reach, speed and starting direction come from.
- A jump off on the same frame as sliding off the end.
- Ropes whose end is low enough for the player to reach the ground while
  holding them.
- The player's pictures on a rope.
