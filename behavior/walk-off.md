# Walking off a ledge

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
  before L. Which drops the player follows and which make them airborne
  isn't specified yet. The rules above start from wherever the player is
  on the frame before L.
- Horizontal motion during the fall.
- Jumping after walking off, rolling off a ledge, and the lead
  character swap.
