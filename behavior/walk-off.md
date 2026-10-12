# Walking off a ledge

Titles: `title-1`

What happens to vertical motion when a grounded player stops being
grounded without jumping (walks off the end of the ground), until they
land. Frames are logic frames at 60 Hz, numbered as in a trace. `y` and
`vy` are as in a trace: down is positive, `vy` in px per frame. Measured
by walking right off ledges of several heights with the lead character,
from many different approach positions.

## The leave frame

Call **L** the first frame on which the player is no longer grounded
without having jumped (the trace row carrying `leave_ground`).

- On frame L the player moves down by exactly **3.4375 px** (3 7/16),
  whatever their position relative to the edge and however far the
  ground below is.
- At the end of frame L, `vy` is **0.00390625** (1/256 px per frame).
  It is not 0, and the difference carries through the fall.

## Falling

On each frame after L, in this order:

1. `vy` increases by **0.4375** (7/16), but never above **8**.
2. The player moves down by the new `vy`.
3. The landing rule in `landing.md` applies unchanged: if the player is
   now less than 5 px above the floor, or at or below it, they land on
   this frame.

So `vy` on frame L + n is 0.00390625 + 0.4375 × n until that would
exceed 8. It is 7.87890625 on L + 18 and **8** from L + 19 on.

Distance fallen below the height the player was at before frame L,
before any landing:

| Frame | `vy` after the frame | Total fallen (px) |
|---|---|---|
| L | 0.00390625 | 3.4375 |
| L + 1 | 0.44140625 | 3.87890625 |
| L + 2 | 0.87890625 | 4.7578125 |
| L + 10 | 4.37890625 | 27.5390625 |
| L + 18 | 7.87890625 | 78.3203125 |
| L + 19 | 8 | 86.3203125 |
| L + 19 + k | 8 | 86.3203125 + 8k |

For n from 1 to 18 the total is 3.4375 + n/256 + 7n(n + 1)/32.

## Jumping near the edge

There is **no grace period** after leaving the ground. A jump press on
frame L or on any later frame of the fall never starts a jump in the
air. (A press made during the fall and held through landing can still
start a jump on landing, as `jump-input.md` describes for any press made
in the air.) In terms of `traces/METRICS.md`, `coyote_frames` is
missing for every press on L or later.

Before L, a press on a grounded frame P starts a jump on P + 1 as usual
(`jump-input.md`), with one exception at the edge. All three ledges
measured have a steep face at their top (`steep-faces.md`): on the last
few grounded frames before L the player is carried down the face. A
press on P starts **no jump** if frame P + 1 starts on a face column
(the frames on which the player is being carried down the face). The
press is then ignored as if it had been made in the air.

So the last frame on which a press still gives a jump is the frame
before the first frame that starts on the face. The frame on which the
player first moves onto the face doesn't start on it (`steep-faces.md`),
so a press on the frame before that move still jumps, and the take-off
frame is the frame of the move.

Example, walking right at about 1.9 px per frame toward a ledge whose
face the player moves onto on frame 21, so frames 22 and later start on
the face, and L = 23:

| Press frame | Take-off | Result |
|---|---|---|
| 20 | 21 | Jump (take-off on the frame that moves onto the face) |
| 21 | — | No jump (22 starts on the face) |
| 22 | — | No jump |
| 23 (L) or later | — | No jump in the air |

Measured walking right off three ledges with presses from 3 frames
before L to 8 frames after it. On each ledge every press whose next
frame started on the face gave no jump, and on the two ledges where the
press before that was measured, it gave a jump.

## Example

A ledge 64 px above the floor below it. The player was grounded at the
ledge's height on the frame before L.

| Frame | Height above lower floor after the frame | `vy` | Result |
|---|---|---|---|
| L | 60.5625 | 0.00390625 | Airborne, `leave_ground` |
| L + 1 | 60.12109375 | 0.44140625 | Airborne |
| L + 15 | 8.00390625 | 6.56640625 | Airborne |
| L + 16 | 1 → 0 | 7.00390625 → 0 | Lands: placed on the floor, `vy` 0, `land` |

## Not covered

- What happens before L. Ground that falls away gradually (a slope, or a
  rounded edge) is followed while the player is still grounded, so on
  some ledges the player is lower than the ledge's top on the frame
  before L. On steep faces, which drops the player follows and which
  make them airborne is in `steep-faces.md`; on other ground it isn't
  specified yet. The rules above start from wherever the player is on
  the frame before L.
- Horizontal motion during the fall: see `air-control.md`, which covers
  it from L + 1 on.
- Ledges without a face at the top (the ground simply ending), where
  the last-chance frame above wasn't measured. Rolling off a ledge is in
  `rolling-off-ledges.md`. The lead character swap.
