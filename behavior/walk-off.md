# Walking off a ledge

Titles: `title-1`, `title-2`

What happens to vertical motion when a grounded player stops being
grounded without jumping (walks off the end of the ground), until they
land. Frames are logic frames at 60 Hz, numbered as in a trace. `y` and
`vy` are as in a trace: down is positive, `vy` in px per frame. Measured
by walking right off ledges of several heights with the lead character,
from many different approach positions.

The sections below up to "In `title-2`" give the `title-1` values. "In
`title-2`" gives the `title-2` values, which differ in the fall, the
landing, the grace period for jumps and crouching.

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

## In `title-2`

Everything in this section was measured on `title-2` only, with the lead
character, walking and running right and left off both ends of a raised
stretch of ground 64 px above flat floor. At both ends the ground simply
ends: there's no face, and nothing carries the player down before L.
Approaches came from many positions and speeds, with many patterns of
input in the fall. Longer falls (up to about 380 px) were made by moving
the player up partway through a fall without changing anything else.
Speeds are whole multiples of 1/256 px per frame, written both ways.
Jump names are as in `jump-input.md` and `jump-arc.md` ("In `title-2`"):
a jump starts on frame **G** and takes off on **T = G + 2**. **L** is the
leave frame, as above. **A** is the landing frame of the fall (the row
carrying `land`). "Crouch", and when one starts, continues and ends,
are as in `ground-speed.md` ("Crouching (`title-2`)").

### The leave frame (`title-2`)

On L the player moves down by 3 px plus one frame of gravity. That makes
**3.4375 px** (3 + 0.4375, 880/256), as in `title-1`, whatever their
position relative to the edge, their speed and their direction. The one
exception is a jump that has started but hasn't taken off yet on L (see
"Jumping near the edge"). If that jump has the lighter gravity on L, the
move is **3.3515625 px** (3 + 0.3515625, 858/256).

At the end of L, `vy` is **0.00390625** (1/256), as in `title-1`.

### Falling (`title-2`)

Unless the player is crouching on L (next section) or a jump starts:

- On **L + 1**, `vy` becomes **1.4375** (1 + 0.4375, 368/256) and the
  player moves down by that. This is a step up from 1/256, not an increase
  of 0.4375 as in `title-1`.
- On each later frame, `vy` increases by **0.4375** (112/256), but never
  above **8**, and the player moves down by the new `vy`.

So `vy` on L + n is 1 + 0.4375 × n for n = 1 to 16. It is exactly **8**
from L + 16 on.

Distance fallen below the height the player was at before frame L,
before any landing:

| Frame | `vy` after the frame | Total fallen (px) |
|---|---|---|
| L | 0.00390625 | 3.4375 |
| L + 1 | 1.4375 | 4.875 |
| L + 2 | 1.875 | 6.75 |
| L + 5 | 3.1875 | 15 |
| L + 10 | 5.375 | 37.5 |
| L + 15 | 7.5625 | 70.9375 |
| L + 16 | 8 | 78.9375 |
| L + 16 + k | 8 | 78.9375 + 8k |

For n from 1 to 16 the total is 3.4375 + n + 0.4375 × n(n + 1)/2.

**Landing.** If moving by the frame's `vy` would take the player to the
floor or below it, they land on that frame instead. They are placed on
the floor, `ground` is 1 and the row carries `land`. Exactly reaching
the floor lands too. There is no 5 px margin as in `title-1`'s
`landing.md`: a player 0.625 px above the floor after moving stays
airborne. This matches the rule for `title-2` jumps (`jump-arc.md`),
where reaching the floor exactly wasn't seen.

Example: off a ledge 64 px above the floor, the player is 60.5625 px
above the floor after L, 0.625 px above it after L + 14 (`vy` 7.125),
and lands on L + 15.

**Horizontal speed** follows `air-control.md` ("In `title-2`") on every
frame from L to the landing frame, L included: the same targets, chosen
from the previous frame's input, and the same speed rule.

**Facing** doesn't change on L or on L + 1, whatever is held. From L + 2
to the landing frame it follows the input of the same frame, as in
`air-control.md` ("Facing (`title-2`)"). So holding the opposite
direction from L on turns the player on L + 2.

### Crouching on the leave frame (`title-2`)

A crouch can start on L (Down held without Up), and a crouch that
started before L continues on L under the usual rule. Either way the
player falls crouched:

- On L the player moves as above (3.4375 px; `vy` 1/256 at the end).
- On each later frame of the crouch, `vy` increases by 0.4375 (never
  above 8). There is no step up to 1.4375. So `vy` on L + n is
  1/256 + 0.4375 × n, as in `title-1`.
- The target is 0 on every crouched frame, as on the ground, so the
  player slows down in the air under the speed rule. Facing doesn't
  change.
- The crouch goes on in the air for as long as exactly one of Down and
  Up is held.
- On the frame **U** the crouch ends (neither, or both, of Down and Up
  held), `vy` still increases by 0.4375, the target is still 0, and
  facing doesn't change. On **U + 1**, `vy` becomes **1.4375**, whatever
  it was before, even if it was larger. From then on the player falls as
  after L + 1 above. U + 1 plays the part of L + 1: facing doesn't
  change on it and follows the input from U + 2.

Example: walking right at +2.25 (+576/256) and pressing Down on L. The
crouch starts on L and Down is held until L + 11, then released (U =
L + 12) with Right held. `vx` is 1.96875 on L and keeps falling
(0.41015625 on L + 12). `vy` is 5.25390625 on L + 12 and then 1.4375 on
L + 13. From L + 13 the target is +2.25 again.

A crouch held through the landing lands crouched (the landing rule
doesn't change).

### Jumping near the edge (`title-2`)

Unlike `title-1`, there is a short grace period after the ground ends.
Jump presses work as follows:

| Press frame P | Result |
|---|---|
| L − 2 or earlier | An ordinary jump from the ground (`jump-input.md`). If P = L − 2, T falls on what would have been L, and the player takes off from the ground. |
| L − 1 | The jump starts on G = L − 1 and the player still leaves the ground on L. T = L + 1, in the air. |
| L or L + 1 | The jump starts on G = P. T = P + 2, in the air. |
| L + 2 or later | No jump in the air (except while crouched, below). A press held through the landing can start a jump after it (below). |

On G these presses follow the usual `title-2` rules. For example, a
press on L with Down held without Up starts a crouch on L as well. That
makes it a crouch jump ("Jumping while crouched in the air"). Up held
alone without a crouch makes no difference.

**Vertical motion until take-off.** On every frame from L (or from G if
later) until T, with **g(f)** the frame's gravity:

- **g(f)** is 0.3515625 (90/256) if Jump has been held on every frame
  from G to f, and 0.4375 otherwise. The lighter gravity applies even
  though the player is moving down, unlike the arc rule of `jump-arc.md`.
- On **L**: the player moves down by 3 + g(L) if G is L − 1 or L, else
  by 3.4375. `vy` ends at 1/256.
- On each frame after L and before T: `vy` increases by g(f) and the
  player moves down by the new `vy`. There is no step up to 1.4375.
- On **T**: the player moves down by `vy` + g(T) (never more than 8),
  then `vy` becomes **−8.21875** (−2104/256). The trace's `vy` on T is
  −8.21875.
- From **T + 1**: `jump-arc.md`, "Each frame after T (`title-2`)", with
  this G.

Horizontal speed follows the `title-2` air rules on every one of these
frames, as in the fall. Facing doesn't change from L to T. From T + 1 it
follows `air-control.md` ("Facing (`title-2`)").

Example: walking right at +2.25 off the ledge, Jump pressed and held for
20 frames. Height is measured downward from the ledge (positive is
below it), after each frame:

| Frame | Press on L − 1 | Press on L | Press on L + 1 |
|---|---|---|---|
| L | 3.3515625 | 3.3515625 | 3.4375 |
| L + 1 | 3.70703125 (T) | 3.70703125 | 3.79296875 |
| L + 2 | −4.16015625 | 4.4140625 (T) | 4.5 |
| L + 3 | −11.67578125 | −3.453125 | 5.55859375 (T) |
| L + 4 | −18.83984375 | −10.96875 | −2.30859375 |

`vy` on the T rows is −8.21875. After T the jumps rise as a held
`title-2` jump: −7.8671875 on T + 1, and so on.

### Jumping while crouched in the air (`title-2`)

While the player is crouching in the air (see "Crouching on the leave
frame"), a Jump press starts a jump on that frame, G = P, however long
the crouch has lasted. This includes a press on the frame the crouch ends
(Jump pressed as Down is released) and a press on U + 1, the frame after
the crouch ends. A press on U + 2 or later doesn't start a jump in the
air.

- **Crouching on G** (the crouch goes on through G, or starts on G with
  Down held without Up): the gravity is **0.3515625** on every frame from
  G to T, whatever is held.
  The crouch rule of `jump-input.md` ("Crouching (`title-2`)") applies.
  If the crouch ends on G + 1 or G + 2, the jump doesn't take off. The
  frame the crouch ends is then U as above, still with 0.3515625, and
  U + 1 is the 1.4375 frame. If Jump is held on that frame U, a new jump
  starts there instead (G = U, not a crouch jump; the target on U is
  still 0).
- **Crouch ending on G** (Jump pressed with neither, or both, of Down
  and Up): a normal
  jump. g(f) is as in "Jumping near the edge", and the target on G is 0.

In both cases T = G + 2, and T and the frames after it are as in
"Jumping near the edge".

Example: crouching in the air from L with Down held, Down and Jump on
L + 3, then Down alone. G = L + 3, T = L + 5. `vy` is 0.87890625 on
L + 2, 1.23046875 on L + 3, 1.58203125 on L + 4. On L + 5 the player
moves down 1.93359375, and `vy` becomes −8.21875.

### Jumping on landing after a fall (`title-2`)

After a fall that didn't come from a jump, the earliest frame a jump can
start is **A + 2** (take-off A + 4). After a jump the earliest frame is
the landing frame + 1 (`jump-input.md`). So it comes one frame later
here.

| Case | Result |
|---|---|
| Pressed on A − 13 or later (but after L + 1) and held through A + 2 | Jump starts on A + 2 (T = A + 4) |
| Pressed on A − 14 or earlier, held through A + 2 | No jump |
| Pressed in the air, released before A + 2 | No jump |
| Pressed on A, A + 1 or A + 2 | Jump starts on A + 2 |
| Pressed on A + 3 or later | Jump starts on the press frame |

This is the usual `title-2` window (the press may come up to 15 frames
before G), with G = A + 2. Measured after falls of 15 and 27 airborne
frames, without Down or Up held near the landing.

### Evidence (`title-2`)

671 falls with 17,225 airborne frames match this section with 0
mismatches:

- vertical moves and `vy`, including 1,695 frames at the cap;
- the landing frame on 280 falls that landed on known flat floor;
- the target and speed rule on 17,879 frames, and facing;
- 201 take-offs in the air: 51 from presses on L − 1, 54 on L, 46 on
  L + 1, and the rest from crouches;
- 248 jumps or non-jumps on landing.

Of those falls, 343 come from three sets of random input sequences
(Right, Left, Down, Up and Jump in random combinations around the
edge). The first two sets showed the crouch cases, which were not in the
model yet. The third set (125 falls) was recorded after the last model
change and matched without any change. Facing was added to the check
after all sets were recorded.

### Not covered in `title-2`

- Ledges with a face or a slope at the top, moving platforms, and the
  second character. Going down 1-in-2 and 1-in-4 slopes never made the
  player leave the ground (`slopes.md`, "In `title-2`").
- Action pressed on L: a roll was seen to start there, which wasn't
  studied. (An Action press from L + 1 on starts nothing and counts as
  Action held.) Rolling off a ledge is in `rolling-off-ledges.md` ("In
  `title-2`").
- A crouch that ends on L itself, and Down pressed on L while a jump that
  started on the ground is still waiting for take-off.
- Facing on L when a ground turn is still in progress there.
- Jumping on landing with Down or Up held near the landing (the crouch
  rules of `jump-input.md` were not checked here). Landing on slopes is
  in `slopes.md` ("In `title-2`").

## Not covered

This list is for `title-1`; `title-2` has its own list above.

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
