# Walkers

Titles: `title-1`, `title-2`

A **walker** is the simplest ground enemy: it walks along the floor at a
steady speed, turns around at the ends of its patrol, and is defeated by
a bounce (`bounce.md`). This file covers how a walker moves, how it
turns, how it moves while the player is knocked out, and what happens to
it when the player bounces off it. Which contacts hurt the player and
which give a bounce, and the sizes of walkers, are in
`walker-contact.md`.

Frames are logic frames at 60 Hz, numbered as in a trace. `x` grows to
the right; `y` and `vy` are as in a trace (down is positive). Speeds are
in px per frame. Measured on two walkers at different points of a level
over several thousand frames each: walking on flat floor and on 1-in-4
slopes, turning at both ends of a patrol, turning at a barrier, while the
player stood still, walked, jumped over them, bounced off them or was
knocked out by them.

## Walking

A walker has a **facing** (left or right) and a horizontal speed `vx`.
Its **target speed** is **+1** when it is heading right and **−1** when
it is heading left.

On each frame:

1. `vx` moves toward the target speed by one eighth of the difference,
   rounded toward zero to a multiple of 1/256. If that step would be 0
   (the difference is less than 8/256), `vx` becomes the target speed
   exactly.
2. The walker moves horizontally by the new `vx`.

A walker at full speed therefore moves exactly 1 px per frame. A walker
that appears at rest speeds up by the same rule. Starting from 0, its
speed on the following frames is 32, 60, 84, 105, 123, 139, 153, 165,
176, 186, 194, 201, 207, 213, 218, 222, 226, 229, 232, 235, 237, 239,
241, 242, 243, 244, 245, 246, 247, 248, 249 and then 256 (in 1/256 px
per frame): full speed on the 32nd frame.

The walker stays on the floor: its height is the floor's height at its
`x`, on flat floor and on 1-in-4 slopes alike, and slopes don't change
its speed.

## Turning at the end of a patrol

A walker can have a **patrol range** from L to R (level data). At the
start of each frame:

- heading left, if `x` rounded down is less than L, or
- heading right, if `x` rounded down is greater than R,

its target speed changes to the opposite direction from this frame on.
Call that frame **T**. The walker doesn't stop: `vx` eases from −1 to +1
(or +1 to −1) by the rule above, so it carries on a little past the end
of its range before coming back. Its **facing** changes on **T + 4**.

It then walks to the other end and turns there the same way, and so on.
The pattern repeats exactly: in the measured walker, each lap took 544
frames, and its range was 256 px wide, centred on its start (the point
where it first appears, `walker-view.md`).

A walker without a patrol range (the other one measured) keeps walking
until something else turns it.

## Turning at a barrier

Some places stop a walker heading toward them. In the case measured the
barrier was invisible and didn't stop the player. A walker heading left
toward a barrier at position X_b (it can't have `x` below X_b):

1. On the frame **S** where its move would take `x` below X_b, it is
   placed at `x` = X_b instead, and `vx` becomes **0**.
2. From **S + 1** its target speed is +1, and it speeds up by the rule
   above. Its facing changes on **S + 5**.

A barrier on the other side works the same way with the directions
swapped (not measured).

## While the player is knocked out

When the player is knocked out (`knock-out.md`, hit frame H), a walker
moves normally on H, then doesn't move on **H + 1 to H + 10**, and moves
normally again from H + 11. During the 32-frame pause between H + 7 and
H + 8 nothing moves (no logic frames run). The walker that knocked the
player out isn't affected: it walks on through the player and away.

## Being bounced on

Call **B** the frame on which the player bounces off the walker (the
frame with the player's `bounce` event, `bounce.md`).

1. On **B** the walker moves horizontally as usual but not vertically,
   and its `vy` becomes **−6**.
2. On **B + 1** and **B + 2** it doesn't move at all (the same frozen
   frames as the player's).
3. On each frame from **B + 3**: `vy` increases by **0.4375** (7/16),
   but never above **8**; the walker moves down by the new `vy` and
   horizontally by its `vx`.

From B on the walker no longer turns, its facing doesn't change, and its
`vx` stays what it was on B. It doesn't land: it falls through the floor
and out of the bottom of the view, and is then removed. In every case
measured (all from flat floor, falling past the same view) it moved for
the last time on B + 41, 95 px below the floor it had been walking on.

On frame B + 2 + n (n ≥ 1, before `vy` reaches 8), relative to its
position on B:

- `vy` = −6 + 0.4375 × n
- `y` = −6 × n + 0.4375 × n(n + 1) / 2

It reaches the top of its arc 38.1875 px above the floor on B + 15, and
is below the floor from B + 29.

## Examples

Synthetic. A walker heading left at full speed with a patrol range
starting at L = 200 (soft turn):

| Frame | `x` at the end of the frame | `vx` | Facing | Notes |
|---|---|---|---|---|
| 1 | 200.5 | −1 | left | |
| 2 | 199.5 | −1 | left | `x` rounded down is now below L |
| 3 | 198.75 | −0.75 | left | T: target speed becomes +1 |
| 4 | 198.21875 | −0.53125 | left | |
| 5 | 197.87890625 | −0.33984375 | left | |
| 6 | 197.703125 | −0.17578125 | left | |
| 7 | 197.671875 | −0.03125 | right | T + 4: facing changes |
| 8 | 197.76953125 | 0.09765625 | right | |
| 9 | 197.9765625 | 0.20703125 | right | |
| 38 | 221.2734375 | 0.97265625 | right | |
| 39 | 222.2734375 | 1 | right | full speed |

A walker heading left at full speed toward a barrier at X_b = 200:

| Frame | `x` at the end of the frame | `vx` | Facing | Notes |
|---|---|---|---|---|
| 3 | 200.5 | −1 | left | |
| 4 | 200 | 0 | left | S: would pass the barrier, stopped at it |
| 5 | 200.125 | 0.125 | left | |
| 6 | 200.359375 | 0.234375 | left | |
| 7 | 200.6875 | 0.328125 | left | |
| 8 | 201.09765625 | 0.41015625 | left | |
| 9 | 201.578125 | 0.48046875 | right | S + 5: facing changes |
| 36 | 224.75390625 | 1 | right | full speed from here |

A walker bounced on while walking left on flat floor, positions relative
to the end of B − 1, `y` down positive:

| Frame | `x` | `y` | `vy` | Notes |
|---|---|---|---|---|
| B | −1 | 0 | −6 | |
| B + 1 | −1 | 0 | −6 | frozen |
| B + 2 | −1 | 0 | −6 | frozen |
| B + 3 | −2 | −5.5625 | −5.5625 | |
| B + 4 | −3 | −10.6875 | −5.125 | |
| B + 15 | −14 | −38.1875 | −0.3125 | top of the arc |
| B + 16 | −15 | −38.0625 | 0.125 | |
| B + 29 | −28 | 3.375 | 5.8125 | below the floor: it doesn't land |

## In `title-2`

Everything in this section was measured on `title-2` only. Walkers there
walk like the ones above, with different rules for speeding up and for
being bounced on. Frames, `x`, `y`, `vx` and `vy` are as above
(down positive); speeds are whole multiples of 1/256 px per frame. Bounce
frames (B) and the player's frozen frames are those of `bounce.md` ("In
`title-2`").

### Walking (`title-2`)

A walker's **target speed** is **+1** heading right and **−1** heading
left, as in `title-1`. On each frame:

1. The walker moves horizontally by its current `vx` (the speed it had
   at the end of the previous frame).
2. Then `vx` moves toward the target speed. With D the difference
   between the target and `vx`, measured in 1/256 px per frame and
   taken without its sign, the step is D/16 rounded down plus D/64
   rounded down, in the direction of the target. If that step would be
   0 (D below 16/256), `vx` becomes the target speed exactly.

Unlike `title-1`, the move on a frame uses the speed from before that
frame's change, and the step is about 5/64 of the difference rather
than one eighth.

A walker at full speed moves exactly 1 px per frame. Starting from 0,
its speed at the end of the following frames is 20, 37, 53, 68, 81, 93,
105, 116, 126, 136, 144, 152, 159, 166, 172, 178, 183, 188, 193, 196,
199, 202, 205, 208, 211, 213, 215, 217, 219, 221, 223, 225, 226, 227,
228, 229, 230, 231, 232, 233, 234, 235, 236, 237, 238, 239, 240, 241
and then 256 (in 1/256 px per frame): full speed at the end of the 49th
frame, first used for the move on the 50th.

The walker stays on the floor: at the end of each frame its `y` is the
floor's height at the whole-pixel column containing its new `x`. This is
the same floor height the player stands on (`slopes.md`, "In
`title-2`"), on flat floor, 1-in-2 slopes and 1-in-4 slopes alike, and
slopes don't change its speed.

Every measured walker headed left from where it appeared, sped up to
full speed and kept going; none turned (see "Not covered in
`title-2`").

### Being bounced on (`title-2`)

1. On **B** the walker doesn't move at all. Its `vx` becomes **−1** if
   the player faces right at the end of B and **+1** if the player faces
   left, and its facing becomes that direction.
   Its `vy` becomes **−8**.
2. On **B + 1** and **B + 2** it doesn't move.
3. On each frame from **B + 3**: the walker moves horizontally by `vx`
   and vertically by `vy`; then `vy` increases by **0.4375** (7/16), but
   never above **12**.

From B on the walker no longer turns, its facing doesn't change, and its
`vx` stays what it was set to on B. It doesn't land: it falls through the
floor and on down until it is removed. In the measured bounces it was
removed between B + 27 and B + 53: soonest when the player ran on away
from it, latest when the player stood still (see "Not covered in
`title-2`").

On frame B + 2 + n (n ≥ 1, while `vy` stays below 12), relative to its
position on B:

- `x` = n × `vx`
- `y` = −8 × n + 0.4375 × n(n − 1) / 2

It reaches the top of its arc 77.1875 px above the floor on B + 21, is
below the floor from B + 40, and moves 12 px per frame from B + 49.

### Other walkers during a bounce (`title-2`)

When the player bounces off one walker, every other walker moves as usual
on B and doesn't move at all on **B + 1** and **B + 2** (the player's two
frozen frames), then carries on from B + 3 as if those two frames hadn't
happened: its `vx` is unchanged by them.

### Example (`title-2`)

Synthetic. A walker heading left at full speed, bounced on by a player
facing right, positions relative to the end of B − 1:

| Frame | `x` | `y` | `vy` | Notes |
|---|---|---|---|---|
| B | 0 | 0 | −8 | doesn't move |
| B + 1 | 0 | 0 | −8 | frozen |
| B + 2 | 0 | 0 | −8 | frozen |
| B + 3 | −1 | −8 | −7.5625 | |
| B + 4 | −2 | −15.5625 | −7.125 | |
| B + 5 | −3 | −22.6875 | −6.6875 | |
| B + 20 | −18 | −77.0625 | −0.125 | |
| B + 21 | −19 | −77.1875 | 0.3125 | top of the arc |
| B + 22 | −20 | −76.875 | 0.75 | |
| B + 39 | −37 | −4.625 | 8.1875 | |
| B + 40 | −38 | 3.5625 | 8.625 | below the floor: it doesn't land |
| B + 48 | −46 | 84.8125 | 12 | `vy` reaches the cap |
| B + 49 | −47 | 96.8125 | 12 | |

The same bounce by a player facing left gives the same `y` and `vy`, and
`x` = +1, +2, … from B + 3.

A walker starting at rest with `x` = 100 and target +1:

| Frame | `x` at the end of the frame | `vx` at the end of the frame |
|---|---|---|
| 1 | 100 | 0.078125 |
| 2 | 100.078125 | 0.14453125 |
| 3 | 100.22265625 | 0.20703125 |
| 4 | 100.4296875 | 0.265625 |
| 5 | 100.6953125 | 0.31640625 |

### Evidence (`title-2`)

Nine walkers at different places in the first level, met along a route
through it: 1,079 frames of walking (including speeding up from rest),
and the frozen frames of the other walkers during a bounce, match this
section with 0 mismatches. 286 bounces off walkers at three places, from
the bounce measurements in `bounce.md` ("In `title-2`") and 16 more
with two other walkers walking nearby, with the player facing right or
left on B: 70,629 frames of walking, every bounce frame, 10,123 frames
of the bounced walkers' falls (including frames at the cap of 12) and
64 frozen frames of the other walkers match with 0 mismatches. The
walker's height matched the player's floor on 46,826 frames, on flat
floor, 1-in-4 slopes and a 1-in-2 slope.

### Not covered in `title-2`

- Turning: patrol ranges and barriers. No measured walker turned.
- What the walker does while the player is hit, and walkers defeated by
  thrown objects. Rolls, including the two frames other walkers stop
  for, are in `rolling-into-walkers.md` ("In `title-2`").
- Walkers bounced on before reaching full speed (every measured one was
  at full speed heading left on B): whether their `vx` is still set to
  exactly ±1.
- When walkers appear and are removed (`walker-view.md` is `title-1`
  only), including how a defeated walker leaving the view is decided,
  and walkers appearing above the floor and dropping onto it.
- Which contacts hurt the player and which give a bounce
  (`walker-contact.md` is `title-1` only), walkers at ledges, and how
  walkers look.

## Not covered

This list is for `title-1`; `title-2` has its own list above.


- Whether a walker defeated by a bounce can still hurt the player while
  it falls. Walkers defeated in other ways are in `thrown-objects.md`
  ("Hitting walkers") and `rolling-into-walkers.md`.
- Ledges and steep faces: one walker was seen to follow a steep face
  down and fall off its end without turning, then land on the floor
  below; this isn't specified. Walkers on other slopes.
- What places act as barriers, barriers met heading right, and a walker
  placed past a barrier.
- Whether the patrol range is always centred on the walker's start. When
  a walker appears and disappears as the view moves is in
  `walker-view.md`.
- Whether other enemies freeze during the frozen frames of a bounce.
- Other kinds of enemies, and how walkers look (poses while turning or
  falling).
