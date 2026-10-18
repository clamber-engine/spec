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

1. `vy` increases by **0.4375** (7/16), but never above **8**. (After a
   jump press that counted but gave no jump, by g(f) while Jump stays
   held; see "Jumping near the edge".)
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

This section is for ledges with a steep face at the top
(`steep-faces.md`). It applies to both characters (for the second
character's values see `second-character.md`, "Walking off a ledge").
At a ledge without a face, see also "Ledges without a face" below; the
rules here give the same results there.

On the last few grounded frames before L the player may be carried down
the face. A frame **starts on the face** if the frame before it ended
with the player grounded on a face column, being carried down the face.
The frame on which the player first moves onto the face doesn't start on
it (`steep-faces.md`). L starts on the face if L − 1 ended on it.

**P** is the press frame (as in `jump-input.md`) and **T** the take-off
frame (the row carrying `jump_start`).

1. A press on P **counts** only if frame P starts with the player
   grounded and not on the face. A press on a frame that starts on the
   face or in the air doesn't count: no jump on P + 1, and the fall is
   the same as without it. (A press made during the fall and held through
   landing can still start a jump on landing, as `jump-input.md`
   describes for any press made in the air. So can a press on a frame
   that starts on the face, if Jump is held until the player is on other
   floor and the take-off comes at most 12 frames after the press:
   `steep-faces.md`, "Jumping on or next to a face", measured with the
   lead character.)
2. A press that counts sets the **jump gravity** g(f) for the frames
   after it: the lighter jump gravity (`jump-arc.md`; 0.28125 for the
   lead character) on frame f if Jump has been held on every frame from
   P to f, and 0.4375 otherwise. It is used on every airborne frame from
   P on, including L, whether or not the press gives a jump. On L the
   player moves down 3 + g(L) (3.4375 with g = 0.4375) and `vy` ends at
   0.00390625; on each later frame of a fall `vy` increases by g(f)
   instead of 0.4375, up to 8 as in "Falling". Once Jump is released the
   fall goes on with 0.4375.
3. A press that counts starts a jump on T = P + 1, unless frame P + 1
   starts on the face. On T the player first moves as they would without
   the jump (a take-off on the frame that moves onto the face is carried
   onto it; a take-off on L or later moves down as in rule 2), then `vy`
   becomes the take-off value, and the jump follows `jump-arc.md` with
   g(f) from rule 2. If P + 1 starts on the face there is no jump on
   P + 1, but rule 2 still applies to the fall (and the press can still
   take off later under the condition in rule 1).

So when the player is carried down the face, there is **no grace
period** after leaving the ground: L starts on the face, and no press on
L or any later frame of the fall starts a jump in the air. In terms of
`traces/METRICS.md`, `coyote_frames` is missing for every press on L or
later. The last press that still gives a jump is on the frame before the
first frame that starts on the face, and its take-off is on the frame
that moves onto the face.

At running speed the player can go over the face without any frame
starting on it: L − 1 ends grounded and not on the face. Then the rules
above give the same results as at a ledge without a face ("Jumping at a
ledge without a face"): a press on L − 1 takes off on L, which moves down
3 + g(L) before `vy` becomes the take-off value; a press on L counts and
takes off on L + 1, which moves down 0.00390625 + g(L + 1) first; a
press on L + 1 or later does nothing. `coyote_frames` is **1** for a
press on L.

Example, walking right at about 1.9 px per frame toward a ledge whose
face the player moves onto on frame 21, so frames 22 and later start on
the face, and L = 23:

| Press frame | Take-off | Result |
|---|---|---|
| 20 | 21 | Jump (take-off on the frame that moves onto the face) |
| 21 | — | No jump (22 starts on the face). Counts: while Jump stays held, the fall from L uses the lighter gravity |
| 22 | — | Does nothing (22 starts on the face) |
| 23 (L) or later | — | Does nothing (no jump in the air) |

Measured first walking right off three ledges with presses from 3 frames
before L to 8 frames after it. On each ledge every press whose next
frame started on the face gave no jump, and on the two ledges where the
press before that was measured, it gave a jump. Then, with both
characters, on the same three ledges (of different heights): walking
and running right from 12 approach positions each, a press on every
frame from L − 6 to L + 8, held 1, 3 or 20 frames, and every case
without a press, 3,312 cases per character. Every frame from L to the
landing (cases that rose onto a raised platform above one ledge were
followed up to there) matched the rules above and "Falling", and the
speed rule for `vx` (`air-control.md`) from L + 1 on:

| | Lead character | Second character |
|---|---|---|
| Presses giving a jump before L | 891 | 1,026 |
| Presses on L that counted and jumped (runs) | 21 | 36 |
| Presses that counted but gave no jump | 195 | 180 |
| Presses on a frame that started on the face | 405 | 270 |
| Presses on a frame that started in the air | 1,728 | 1,728 |
| Airborne frames checked | 82,664 | 75,398 |
| Of them falling with the lighter gravity after a press with no jump | 1,111 | 1,038 |
| Landings | 3,104 | 2,600 |
| Mismatches | 0 | 0 |

The rules as written before (an ignored press changes nothing, and no
press on L ever jumps at a ledge with a face) gave 131 and 153
mismatching cases on the same runs (lead and second character).

## Example

A ledge 64 px above the floor below it. The player was grounded at the
ledge's height on the frame before L.

| Frame | Height above lower floor after the frame | `vy` | Result |
|---|---|---|---|
| L | 60.5625 | 0.00390625 | Airborne, `leave_ground` |
| L + 1 | 60.12109375 | 0.44140625 | Airborne |
| L + 15 | 8.00390625 | 6.56640625 | Airborne |
| L + 16 | 1 → 0 | 7.00390625 → 0 | Lands: placed on the floor, `vy` 0, `land` |

## Ledges without a face

Everything in this section was measured on `title-1` only, with the
lead character, at both ends of a stretch of raised ground that ends
flat on both sides (no face, no slope, no rounded lip) 176 px above the
floor below. Walking right and left off both ends from many positions
and speeds (from 0.03 to 2 px per frame, including sliding off with no
direction held and arriving in short taps), and running off both ends
(about 2.85 to 3 px per frame, and slowing from running to walking
speed after Action is released). The frames before L, L itself, and
jump presses from L − 4 to L + 3 were measured. The rules above
for L and the fall hold unchanged; this section adds when L happens,
the frames before it, horizontal speed on L, and jumping. Walking,
running and turns go on through the fall and the landing as on the
ground (`ground-speed.md`, "After a fall or a bounce").

### When the player leaves

The column terms are as in `steep-faces.md`: the floor has one height for
each 1-px-wide column of `x`, and the player's column is the one
containing `x`.

- On every grounded frame before L the player moves exactly as on flat
  ground: `x` and `vx` by `ground-speed.md`, `y` unchanged. Nothing
  slows or stops the player near the end, and nothing carries them
  down. However slowly the player is moving, they go over the end.
- **L** is the first frame whose move ends with the player's column
  past the end of the ground. Going right, that is the first frame
  ending with `x` at or beyond the left side of the first column without
  ground. Going left, it is the first frame ending with `x` less than
  the left side of the last column with ground (`x` in the column
  without ground just beyond it). A frame that ends with `x` exactly on
  the last column with ground, even 1/256 px from its far side, is
  still grounded.
- On L the player moves down 3.4375 and `vy` ends at 0.00390625, as in
  "The leave frame" above.

### Horizontal speed on L

On L, `vx` and `x` follow the speed rule of `ground-speed.md` with the
target from the input of frame L, exactly as on the grounded frames
before and as `air-control.md` describes for the frames after. There is
no special case on L, walking or running, speeding up or slowing down.

Example: walking right with Right held, `vx` is 1.96875 on L − 1,
1.97265625 on L (a step of 1/256 toward 2) and 2 on L + 1 (the gap is
under 8/256, so the speed becomes the target).

### Jumping at a ledge without a face

**P** is the press frame (as in `jump-input.md`) and **T** the take-off
frame (the row carrying `jump_start`). A press on P starts a jump if the
player was grounded at the end of frame P − 1. That includes **P = L**.
T is P + 1 in every case. **g(f)** is the frame's jump gravity: 0.28125
if Jump has been held on every frame from P to f, and 0.4375 otherwise
(`jump-arc.md`).

| Press frame P | What happens |
|---|---|
| L − 2 or earlier | An ordinary jump from the ground (`jump-input.md`, `jump-arc.md`). T is grounded and the player is in the air on what would have been L. There is no L. |
| L − 1 | T = L. On L the player goes over the end and moves down 3 + g(L); then `vy` becomes −7. The row for L has `ground` 0, `vy` −7 and `jump_start`, and no `leave_ground`. |
| L | L is as without a jump, except that the player moves down 3 + g(L) = 3.28125 (Jump is held on L). `vy` ends at 0.00390625 and the row carries `leave_ground`. On T = L + 1 the player moves down by 0.00390625 + g(T), then `vy` becomes −7; the row carries `jump_start`. |
| L + 1 or later | No jump in the air: the fall of "Falling" above. A press held through the landing can start a jump on landing (`jump-input.md`). |

From T + 1 on, every jump above follows `jump-arc.md` ("Each frame after
T"), with gravity g(f) counted from P. So in terms of
`traces/METRICS.md`, `coyote_frames` is **1** for a press on L at a
ledge without a face, and missing for presses on L + 1 or later.

Example: walking right with Right held, as in the speed example above,
Jump pressed on the frame given and held for 20 frames. Height is
measured downward from the top of the ledge (positive is below it) after
each frame; T is marked.

| Frame | Press on L − 2 | Press on L − 1 | Press on L | No press |
|---|---|---|---|---|
| L − 1 | 0 (T) | 0 | 0 | 0 |
| L | −6.71875 | 3.28125 (T) | 3.28125 | 3.4375 |
| L + 1 | −13.15625 | −3.4375 | 3.56640625 (T) | 3.87890625 |
| L + 2 | −19.3125 | −9.875 | −3.15234375 | 4.7578125 |
| L + 3 | −25.1875 | −16.03125 | −9.58984375 | 6.07421875 |

`vy` on the T rows is −7, and the jumps then rise as a held jump: −6.71875
on T + 1, −6.4375 on T + 2, and so on.

With Jump held for 1 frame only, g is 0.4375 from P + 1 on: a press on
L − 1 moves down 3.4375 on L (= T), and a press on L moves down 3.28125
on L and 0.44140625 on T.

### Evidence (ledges without a face)

- 332 walks and runs off the two ends without Jump: 16,120 grounded
  frames (`y` unchanged, grounded exactly while the player's column has
  ground), 332 leave frames, 3,984 airborne frames up to L + 12, 0
  mismatches.
- Horizontal speed by the speed rule on 85,825 frames, including L and
  the jump cases, 0 mismatches.
- 984 cases with Jump pressed from L − 4 to L + 3, held 1, 2, 3, 5 or 20
  frames: 295 ground take-offs, 197 take-offs on L, 197 take-offs on
  L + 1 after a press on L, no jump for every press on L + 1 or later,
  and the vertical motion of every frame up to L + 12, 0 mismatches.

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
move is **3.3515625 px** (3 + 0.3515625, 858/256). Leaving the ground at
a step down rather than a ledge moves the same 3.4375 px from flat floor
and gentle slopes, and more from steeper slopes (`steep-faces.md`,
"Following the floor down (`title-2`)").

At the end of L, `vy` is **0.00390625** (1/256), as in `title-1`.

### Falling (`title-2`)

Unless the player is crouching on L (next section) or a jump starts:

- On **L + 1**, `vy` becomes **1.4375** (1 + 0.4375, 368/256) and the
  player moves down by that. This is a step up from 1/256, not an increase
  of 0.4375 as in `title-1`. The exception is a small step down
  (`steep-faces.md`, "Following the floor down (`title-2`)"). There, the
  floor of the column the player is in after L + 1's move may be close
  below the feet as they were after L. When it was 0.5625, 1.5625 or
  2.5625 px below, `vy` only increased by 0.4375, to 113/256, and the
  player moved down by that. From 3.5625 px below on it was 368/256 as
  usual. Distances in between weren't measured.
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
  second character. Walking onto a steep face from the floor above it,
  leaving the ground on one, and steps down between pieces of floor are
  in `steep-faces.md` ("In `title-2`"). Going down 1-in-2 and 1-in-4
  slopes never made the player leave the ground (`slopes.md`, "In
  `title-2`").
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

- What happens before L on ground that falls away gradually (a slope,
  or a rounded edge), which is followed while the player is still
  grounded, so on some ledges the player is lower than the ledge's top
  on the frame before L. On steep faces, which drops the player follows
  and which make them airborne is in `steep-faces.md`; where the ground
  simply ends, it is in "Ledges without a face". The rules above start
  from wherever the player is on the frame before L.
- Horizontal motion during the fall: see `air-control.md`, which covers
  it from L + 1 on. Frame L is covered only at a ledge without a face
  ("Horizontal speed on L").
- At ledges without a face: drops other than 176 px, the second
  character, Down or Action pressed near the end (the Down + Action
  ground action going over the end is in `ground-action.md`, "At a
  ledge without a face"), and
  a turn in progress there. Rolling off a ledge is in
  `rolling-off-ledges.md`. The lead character swap.
- At ledges with a steep face: walking or running left, approaches
  slower than walking speed, Down or Action held near the edge (the
  Down + Action ground action going over a face is in
  `ground-action.md`, "At a steep face"), and
  whether a press that counted but gave no jump can still start a jump
  on landing.
