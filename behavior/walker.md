# Walkers

Titles: `title-1`

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
frames, and its range was 256 px wide, centred on the point where it
first appeared.

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

## Not covered

- Whether a walker defeated by a bounce can still hurt the player while
  it falls, and walkers defeated in other ways (thrown objects; rolls
  are in `rolling-into-walkers.md`).
- Ledges and steep faces: one walker was seen to follow a steep face
  down and fall off its end without turning, then land on the floor
  below; this isn't specified. Walkers on other slopes.
- What places act as barriers, barriers met heading right, and a walker
  placed past a barrier.
- When a walker appears and disappears as the view moves, and whether
  the patrol range is always centred where it appears.
- Whether other enemies freeze during the frozen frames of a bounce.
- Other kinds of enemies, and how walkers look (poses while turning or
  falling).
