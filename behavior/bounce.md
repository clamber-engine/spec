# Bouncing off an enemy

What happens to the player's motion when they bounce off an enemy, from
the bounce until they land. Which contacts cause a bounce, and what
happens to the enemy, are not covered here. Frames are logic frames at
60 Hz, numbered as in a trace. `y` and `vy` are as in a trace: down is
positive, `vy` in px per frame. Measured with the lead character on
several bounces off ground enemies at different points in a level, with
many patterns of jump and direction input.

## The bounce frame

Call **B** the frame on which the bounce happens (the trace row carrying
the `bounce` event).

1. On B the player first moves exactly as on any other airborne frame:
   vertically by the rules that applied before the bounce (`jump-arc.md`
   or `walk-off.md`), horizontally as usual.
2. Then `vy` is set to **−7.125** (−7 1/8), whatever it was before.
   Measured incoming `vy` ranged from −1.3125 (still rising) to 7.25;
   the result was always −7.125.

The row for B shows the moved position and `vy` −7.125. `ground` is 0.

## Two frozen frames

On **B + 1** and **B + 2** the player doesn't move: `x`, `y`, `vx` and
`vy` stay exactly as they were at the end of B. Direction input on these
frames doesn't change `vx`.

## Each frame from B + 3

Until the player lands, on each frame:

1. `vy` increases by the frame's gravity, but never above **8**.
2. The player moves down by the new `vy` (up while `vy` is negative).
3. The landing rule in `landing.md` applies.

The gravity is:

- **0.28125** (9/32) if the jump input is held on **every** frame from
  B + 3 up to and including this frame;
- **0.4375** (7/16) otherwise.

Only the hold from B + 3 matters:

- Holding jump before or during the bounce neither helps nor is needed.
  A hold that started earlier counts only if it is still held on B + 3,
  and a press first made on B + 3 counts the same as a hold carried over.
- Releasing jump, even for one frame, gives 0.4375 for the rest of the
  bounce. Pressing jump on B + 4 or later never gives the lighter
  gravity, and doesn't start a jump.

Horizontally, the bounce doesn't change `vx`; from B + 3 on it changes
as on any other airborne frame.

While airborne after the bounce the player's trace state is `bounce`,
from B up to the landing frame.

## How long jump is held

Let **K** be the number of frames jump is held without a break, starting
with B + 3 (K = 0 if it isn't held on B + 3). The lighter gravity applies
on frames B + 3 to B + 2 + K.

`bounce_height` as defined in `traces/METRICS.md` (it doesn't depend on
how high above the ground the bounce happens, as long as the player
doesn't land or hit anything before the top of the arc):

| K | `bounce_height` (px) |
|---|---|
| 0 | 54.5 |
| 1 | 57 |
| 2 | 59.34375 |
| 3 | 61.6875 |
| 4 | 63.875 |
| 5 | 65.9375 |
| 8 | 71.625 |
| 10 | 74.90625 |
| 15 | 81.375 |
| 20 | 85.3125 |
| 25 or more | 86.71875 |

From K = 25 on, the top of the arc doesn't get higher, but holding longer
still makes the fall slower.

On frame B + 2 + n (n ≥ 1, before landing and before `vy` reaches 8),
with gravity g used on every frame since B + 3:

- `vy` = −7.125 + g × n
- `y` relative to B = −7.125 × n + g × n(n + 1) / 2

## Example

A synthetic bounce 20 px above flat ground, jump not held after the
bounce (K = 0). Heights are above the floor at the end of each frame.

| Frame | Height above floor | `vy` | Notes |
|---|---|---|---|
| B | 20 | −7.125 | `bounce` |
| B + 1 | 20 | −7.125 | frozen |
| B + 2 | 20 | −7.125 | frozen |
| B + 3 | 26.6875 | −6.6875 | |
| B + 4 | 32.9375 | −6.25 | |
| B + 18 | 74.5 | −0.125 | top of the arc (54.5 above B) |
| B + 19 | 74.1875 | 0.3125 | |
| B + 35 | 9.6875 | 7.3125 | |
| B + 36 | 1.9375 → 0 | 7.75 → 0 | under 5 px and moving down: lands, placed on the floor, `vy` 0, `land` |

## Not covered

- Which contacts cause a bounce rather than damage, including bounces
  that happen while the player is still rising, and what happens to the
  enemy.
- Facing during the frozen frames.
- Bouncing off other kinds of objects, bounces from a roll, bouncing
  again before landing, and the other character.
- Jumping on the landing frame after a bounce.
- Falls long enough after a bounce for `vy` to reach 8 weren't measured
  (the largest seen was 7.75); the cap is taken to be the same as for
  other falls.
