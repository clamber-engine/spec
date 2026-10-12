# Rolling off a ledge

Titles: `title-1`

What happens when a roll carries the player off the end of the ground:
the roll goes on through the fall, it ends a fixed time after landing,
and a jump can start at any time during the fall. Frames are logic
frames at 60 Hz, numbered as in a trace. `x`, `y`, `vx` and `vy` are as
in a trace: right and down are positive, speeds in px per frame. Speeds
are whole multiples of 1/256 px per frame and are written both ways, as
in `roll-speed.md`. "Left", "Right", "Jump", "Action" are the neutral
inputs `L`, `R`, `J`, `A` of the scenario format.

Names from other files: **S** is the roll's `roll_start` frame and **D**
the frame after its last roll frame (`roll-input.md`, `roll-speed.md`).
**L** is the first airborne frame (`walk-off.md`; the trace row carrying
`leave_ground`). **G** is the landing frame (the row carrying `land`).

Measured with the lead character, rolling right off three ledges of
different heights (falls of 16 to 40 airborne frames), from normal rolls
started standing or walking with Right held, Action tapped or held, at
several distances from the edge.

## The fall

The roll doesn't end when the player leaves the ground. On every frame
from L until landing the state is still `roll`, with `ground` 0.

- **Vertical motion** is that of a walk-off fall, unchanged
  (`walk-off.md`): on L the player moves down by 3.4375 px and `vy` ends
  at 0.00390625; then `vy` increases by 0.4375 per frame up to 8, and the
  landing rule of `landing.md` applies.
- **Horizontal speed** keeps following the roll's speed rule
  (`roll-speed.md`) on every frame from L + 1, with the roll's target
  (3 px per frame for a normal roll) and steering: holding the direction
  opposite to the roll reverses it, in the air as on the ground. The air
  control targets of `air-control.md` don't apply while the roll lasts;
  holding Action or a direction in the roll's direction changes nothing.
- Frame L itself is not covered (the ledges measured have a steep face
  at the top, where the speed was seen to rise above the roll's target
  on L).

## When the roll ends

However long the roll went on before L and however long the fall is,
the roll ends as if it had started on the frame after landing:

- The roll's last frame is **G + 29**, and **D = G + 30**.
- The roll can't end in the air: a fall longer than a whole roll (40
  airborne frames was measured) still ends this way.
- From D everything in `roll-input.md` and `roll-speed.md` applies with
  this D: if Action was held on every frame from S to D the roll carries
  on into running; otherwise it ends in a stop.

Example: standing about 30 px in front of a ledge with Right held,
Action pressed on frame 0 and held. The player leaves the ground on
frame 17 and lands on frame 33. Frames 0 to 62 are all `roll`
(63 frames rather than 29), D = 63, and because Action was held
throughout, the roll carries on into running (`roll_end` on D + 3 = 66).
With Action tapped on frame 0 instead, `roll_end` is on 63 and the stop
follows.

## Jumping during the fall

A jump press on any airborne frame P of the roll starts a jump on
**T = P + 1**. There is no limit: it works on every frame of the fall,
including the last airborne frame. (A fall that doesn't come from a
roll is different: no jump starts in the air, see `walk-off.md`.)

The jump ends the roll. Horizontal speed: P is still a roll frame, and
from T the targets of `air-control.md` apply, starting from the roll's
speed (`roll-speed.md`, "Jumping out of a roll"). Vertical motion, with
**g(f)** = 0.28125 if Jump has been held on every frame from P to f, and
0.4375 otherwise (as in `jump-arc.md`):

1. **P**: `vy` increases by g(P) (never above 8) and the player moves
   down by the new `vy`. Since Jump is held on P, g(P) is 0.28125.
2. **T**: the player moves down by `vy` + g(T) (that sum also capped at
   8). Then `vy` becomes **−7**: the trace's `vy` on T is −7.
3. **From T + 1**: `jump-arc.md`, "Each frame after T", with this P.

If the move on T would land the player (`landing.md`), they are placed
on the floor on T, `ground` is 1 on T, and the jump goes on from there
as from a take-off on the ground: on T + 1 `vy` is −7 + g(T + 1).

After the jump lands, the player walks, runs or stands under the normal
rules: no stop follows.

Example: a roll fall with `vy` 1.75390625 at the end of frame P − 1.
Jump pressed on P and held 12 frames (P to P + 11):

| Frame | Moved down by | `vy` after the frame |
|---|---|---|
| P | 2.03515625 | 2.03515625 |
| T = P + 1 | 2.31640625 | −7 |
| P + 2 | −6.71875 | −6.71875 |
| P + 3 | −6.4375 | −6.4375 |
| P + 12 | −3.75 | −3.75 |

On P + 12 Jump is no longer held, so from there `vy` increases by
0.4375 per frame.

## Trace

The fall frames of a roll are `roll` with `ground` 0; `leave_ground` is
on L. When a jump starts in the fall, `roll_end` is on P (its state is
`fall`) and `jump_start` on T (state `jump`, or as on the ground if T
lands).

## Not covered

- Frame L's horizontal speed and the frames just before it (the ledges
  measured all have a face at the top).
- Fast and slow rolls off ledges, rolling off to the left, rolls that
  start in the stop after an earlier roll, and the second character.
- Rolls that hit a wall or an enemy during the fall or after landing.
- Ledges without a face at the top.
