# Rolling off a ledge

Titles: `title-1`, `title-2`

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
different heights with a steep face at the top (`steep-faces.md`; falls
of 16 to 40 airborne frames), from normal rolls started standing or
walking with Right held, Action tapped or held, at several distances
from the edge; and rolling right and left off both ends of a raised
stretch of ground 176 px above flat floor where the ground simply ends,
with no face (falls of 30 airborne frames, made shorter or longer by
moving the player down or up partway through the fall without changing
anything else). On that stretch the rolls were slow, normal and fast,
started standing, walking or right after running, with Action tapped or
held and with many distances from the edge and patterns of steering.

## The fall

The roll doesn't end when the player leaves the ground. On every frame
from L until landing the state is still `roll`, with `ground` 0.

- **Vertical motion** is that of a walk-off fall (`walk-off.md`) when
  the player leaves the ground 9 or more frames after S (L ≥ S + 9): on
  L the player moves down by 3.4375 px and `vy` ends at 0.00390625; then
  `vy` increases by 0.4375 per frame up to 8, and the landing rule of
  `landing.md` applies.
- **Hover.** When L is S + 8 or earlier, the player first hangs in the
  air for a few frames. On a hover frame the player moves down by
  0.00390625 px (1/256) and `vy` ends at 0.00390625. Which frames hover
  depends only on L − S:

  | L | Frame L | Hover frames after L |
  |---|---|---|
  | S | Moves down 3.4375 px, `vy` 0.00390625 | L + 1 to L + 8 |
  | S + 1 to S + 7 | Hover frame | L + 1 to L + 9 |
  | S + 8 | Hover frame | none |
  | S + 9 or later | Moves down 3.4375 px, `vy` 0.00390625 | none |

  After the last hover frame (or after L when there are none), `vy`
  increases by 0.4375 per frame up to 8, starting from 0.00390625, and
  the landing rule of `landing.md` applies. The hover is the same on
  the ledges with a face and on the one without.
- **Horizontal speed** keeps following the roll's speed rule
  (`roll-speed.md`) with the roll's target (1, 3 or 4 px per frame for a
  slow, normal or fast roll) and steering: holding the direction
  opposite to the roll reverses it, in the air as on the ground, and a
  slow roll becomes normal when its own direction is held, as on the
  ground. The air control targets of `air-control.md` don't apply while
  the roll lasts; holding Action, or a direction in the roll's direction
  in a normal or fast roll, changes nothing. Where the ground simply
  ends, this holds on L too. On the ledges with a face, frame L isn't
  covered (the speed was seen to rise above the roll's target on L,
  while the player was carried down the face).

## When the roll ends

However long the roll went on before L and however long the fall is,
the roll ends as if it had started on the frame after landing:

- The roll's last frame is **G + 29**, and **D = G + 30**.
- **Except** when the player lands exactly 30 frames after leaving the
  ground (**G = L + 30**): then **D = G**. The landing frame is already
  the first frame after the roll, and the roll's last frame is G − 1,
  in the air. A fall one frame shorter or longer (G = L + 29 or
  G = L + 31) ends at G + 30 as above. This is the same with and without
  a hover, and on the ledges with a face and without.
- Apart from that exception the roll can't end in the air: a fall
  longer than a whole roll (up to 50 airborne frames were measured)
  still ends at G + 30.
- From D everything in `roll-input.md` and `roll-speed.md` applies with
  this D: if Action was held on every frame from S to D the roll carries
  on into running (not after a fast roll, which always ends in a stop);
  otherwise it ends in a stop.

Example: standing about 30 px in front of a ledge with Right held,
Action pressed on frame 0 and held. The player leaves the ground on
frame 17 and lands on frame 33. Frames 0 to 62 are all `roll`
(63 frames rather than 29), D = 63, and because Action was held
throughout, the roll carries on into running (`roll_end` on D + 3 = 66).
With Action tapped on frame 0 instead, `roll_end` is on 63 and the stop
follows.

Example where the ground simply ends, 176 px above the floor below:
standing 56 px from the edge, Right held, Action tapped on frame 0
(S = 0). The player leaves the ground on frame 25 (L = S + 25, no hover)
and lands on frame 55, 30 frames later, so D = 55: the stop starts on
the landing frame. Had the fall been one frame longer, the roll would
have gone on along the floor to G + 29.

Example of a hover: walking right, Right held, Action tapped 7 frames
before the player leaves the ground (L = S + 7). Below the ledge's
ground, after each frame:

| Frame | Below the ledge (px) | `vy` |
|---|---|---|
| L | 0.00390625 | 0.00390625 |
| L + 9 | 0.0390625 | 0.00390625 |
| L + 10 | 0.48046875 | 0.44140625 |
| L + 11 | 1.359375 | 0.87890625 |

The player lands 40 frames after L, so D = G + 30.

## Stopping before a ledge

Where the ground simply ends, a roll that is moving slowly stops before
the edge instead of rolling off. A roll frame on the ground (S to D − 1)
is a **stopped frame** when both hold at its start:

- `|vx|` is at most 1.05859375 (271/256). At 1.0625 (272/256) or more
  the frame moves as usual.
- There is no ground 15 px ahead: in the column floor(x) + 15 when the
  roll goes right, floor(x) − 15 when it goes left (x at the start of
  the frame). Floor up to 47 px below the player's feet counts as ground
  there, and floor 49 px or more below doesn't (48 wasn't measured);
  floor higher than the feet counts (measured up to 64 px higher). At
  the top of a pit whose edge is a short face, a face column 15 px
  below the player's feet did count as ground (the roll moved on), and
  the first column with no ground at all, one further, stopped it.
  Measured at the top of a face (`steep-faces.md`, "Rolling on, onto or
  into a face"): 45 to 47 px below counted, 49 to 64 px below didn't.

On a stopped frame `vx` becomes 0 and the player doesn't move. The roll
is then a **stopped roll** (`steep-faces.md`, "Stopped rolls"): it stays
where it is until it pushes off, as below. A roll going away from the
edge has ground ahead, so it has no stopped frames (rolls away from both
edges, started up to 20 px from them, were checked).

**Pushing off.** On a frame S + 1 or later of a stopped roll on which
exactly one of Left or Right is held, in the roll's direction, `vx`
instead becomes 2 (512/256) in the roll's direction and the player moves
by it. The direction is the one after the frame's steering
(`roll-speed.md`): holding the direction away from the edge turns the
roll and pushes off away from it on the same frame. From the next frame
the roll's speed rule applies again with a size of 2 (512/256), whatever
the roll's size was; holding the roll's direction on a later frame makes
it a **normal** roll (3), as for a slow roll, and with nothing held it
goes on at 2. The roll usually goes off the edge a few frames later.

The roll's other rules don't change: it still lasts to D = S + 29 and
then stops or carries on into running as in `roll-input.md` (with `vx`
0 when it was stopped). So:

- A roll started standing within 15 px of the edge, facing it, doesn't
  move at all unless the direction toward the edge is held from S + 1.
- A slow roll heading for the edge from farther away stops when its
  column comes within 15 px of the end of the ground, since its speed
  is close to 1. Slow rolls go off a ledge only if they reach those last
  15 px faster than 1.0625, for example right after walking.
- Normal and fast rolls are faster than that and roll off as usual,
  unless they start slowly within those 15 px (a normal roll started
  from standing starts each of its first four frames slower than
  1.0625).

Examples, rolling right toward an edge whose last column with ground is
E − 1 (so E is the first column without):

- Standing with floor(x) = E − 10, Action alone on S: every frame from
  S to D − 1 is stopped; the stop after the roll starts on D = S + 29
  with the player where they started.
- The same place, Right and Action on S and Right held after: S is
  stopped (`vx` 0), on S + 1 `vx` is 2, then 2.125, 2.234375, 2.328125,
  …, toward 3; the player leaves the ground on S + 5.
- Walking right, then Right released and Action pressed alone about
  42 px from the edge: the slow roll's `vx` is 1.51953125 on S and falls
  toward 1, and the first frame that starts with floor(x) = E − 15, S + 24
  (`vx` 1.0390625 at its start), is stopped. The player stays there
  until D.

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
   Two kinds of P are different:
   - **P is a hover frame** ("The fall"): P stays a hover frame. The
     player moves down by 0.00390625 px and `vy` ends at 0.00390625.
   - **P is L and isn't a hover frame**: the player moves down by
     3.28125 px (instead of 3.4375) and `vy` ends at 0.00390625.
2. **T**: the player moves down by `vy` + g(T) (that sum also capped at
   8). Then `vy` becomes **−7**: the trace's `vy` on T is −7.
3. **From T + 1**: `jump-arc.md`, "Each frame after T", with this P.

The jump ends the hover: T is never a hover frame, and neither is any
frame after it, whatever the table in "The fall" says for those frames.
So a press on a hover frame takes off from where the hover has held the
player, with `vy` 0.00390625 at the end of P.

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

Example of a press during a hover: walking right, Right held, Action
tapped 7 frames before the player leaves the ground (L = S + 7, hover
frames L to L + 9). Jump pressed on L + 3 and held 12 frames:

| Frame | Moved down by | `vy` after the frame |
|---|---|---|
| L to L + 2 | 0.00390625 each | 0.00390625 |
| P = L + 3 | 0.00390625 | 0.00390625 |
| T = L + 4 | 0.28515625 | −7 |
| L + 5 | −6.71875 | −6.71875 |

With Jump tapped on P alone, T moves down by 0.44140625 and `vy` is
−6.5625 after L + 5. L + 4 to L + 9 would have been hover frames without
the press; with it they follow the jump from T on.

### Evidence (where the ground simply ends)

3,082 runs by inputs at the stretch of ground without a face, 1,040 of
them rolling off (725 normal, 39 fast and 14 slow rolls going right;
223, 29 and 10 going left), match this file with 0 mismatches: roll
speed on 80,428 roll frames (L included), vertical moves on 13,952
airborne frames (3,486 of them hover frames), the end of 773 rolls after
landing (102 of them after falls of exactly 30 frames), 622 rolls that
stayed on the ground, and 10,434 frames that met the conditions of
"Stopping before a ledge" (10,047 stopped, 387 pushed off). Without the
G = L + 30 exception, all 102 of those would be wrong. Frames after a
roll hit a wall or reached another ledge on the floor below were left
out, as were 12 runs where moving the player put them below the floor.
On two of the ledges with a face, 612 runs with falls of 26 to 45
frames made by moving the player match the hover and vertical rules on
2,100 airborne frames (882 hover frames) and the end rule on 159 rolls
(7 of them after falls of exactly 30 frames).

Jumps during and around a hover: 5,828 runs at the stretch without a
face (normal and slow rolls going right and left, Action tapped or held,
L − S from 0 to 19, Jump pressed on each frame from L to L + 13 and held
1, 5, 12 or 40 frames, some steering back or releasing the direction from
L + 1) match "Jumping during the fall" with 0 mismatches on 246,195
vertical moves and 246,768 horizontal speeds, to landing: 2,373 presses
on hover frames, 128 presses on an L that wasn't a hover frame, and 7,613
frames that would have been hover frames without the press. In all of
them the roll ends on P and no stop follows the landing. With the
jump rule on P instead (P not kept as a hover frame), 2,299 runs
mismatch. Frames once the player had moved past the flat floor below
the stretch, or back under its edge, were left out.

## The second character

With the second character leading (`second-character.md`), a roll off
a ledge with a steep face at the top follows this file with that
character's roll values (`second-character.md`, "Rolls": the roll lasts
24 frames on the ground, and its sizes are 1.125, 3.375 and 4.5 for a
slow, normal and fast roll), and these values differ:

| Value | First character | Second character |
|---|---|---|
| L = S: hover frames after L | L + 1 to L + 8 | **L + 1 to L + 10** |
| L is a hover frame, and so are the frames after it up to | L + 9, when L is S + 1 to S + 7 | **L + 11**, when L is S + 1 to S + 9 |
| L is the only hover frame | L = S + 8 | **L = S + 10** |
| No hover | L ≥ S + 9 | **L ≥ S + 11** |
| The roll's last frame | G + 29 (D = G + 30) | **G + 24 (D = G + 25)** |
| Fall length for which D = G | 30 frames (G = L + 30) | **25 frames (G = L + 25)** |

Everything else is as for the first character:

- **The fall.** A frame that isn't a hover frame moves as in "The fall":
  on L, when L isn't a hover frame, the player moves down by 3.4375 px
  and `vy` ends at 0.00390625; after the last hover frame (or after L)
  `vy` increases by 0.4375 per frame up to 8 from 0.00390625, and the
  landing rule of `landing.md` applies. A hover frame moves the player
  down by 0.00390625 px with `vy` 0.00390625.
- **Horizontal speed.** From L + 1 on, `vx` follows the roll's speed
  rule (`roll-speed.md`) toward the second character's size: 3.375
  (864/256) for a normal roll, 4.5 (1152/256) for a fast one and 1.125
  (288/256) for a slow one. Frame L isn't covered here either.
- **When the roll ends.** However long the roll went on before L and
  however long the fall is, the roll's last frame is G + 24 and D is
  G + 25, except that D = G when the fall lasts exactly 25 frames
  (falls of 24 or 26 frames end at G + 25). If the roll goes off another
  ledge on the floor below, each landing starts the count again: the
  rule applies to the last landing, with L the first airborne frame of
  that fall, and a last fall of exactly 25 frames ends the roll on its
  landing frame. From D, `second-character.md` ("Rolls") applies: if
  Action was held on every frame from S to D the roll carries on into
  running (`roll_end` on D + 6), otherwise it ends in a stop.
- **Pushing off.** Standing at the top of a face within reach of the
  edge, a roll from standing with Action alone stayed where it started
  with `vx` 0 until D = S + 24, and with Right held from S + 1 it pushed
  off at 2 (512/256) on S + 1 and went over the edge, as in "Stopping
  before a ledge". After the push the roll's size is 2, and holding the
  roll's direction makes it 3 (768/256), the first character's normal
  size, not 3.375. This is the same as a slow roll steered in its own
  direction (`second-character.md`, "Rolls").

Example: standing about 33 px in front of a ledge with Right held,
Action pressed on frame 0 and held. The player starts down the face on
frame 15, leaves the ground on frame 16 and lands on frame 31, 15
frames later. Frames 0 to 55 are all `roll` (56 frames rather than 24),
D = 56 = G + 25, and because Action was held throughout, the roll
carries on into running (`roll_end` on D + 6 = 62). With Action tapped
on frame 0 instead, `roll_end` is on 56 and the stop follows.

Example of a hover: walking right from standing, Right held, Action
pressed with Right on frame 12 and released. The player leaves the
ground on frame 19 (L = S + 7). Below the player's height on L − 1,
after each frame:

| Frame | Below L − 1 (px) | `vy` |
|---|---|---|
| L | 0.00390625 | 0.00390625 |
| L + 11 | 0.046875 | 0.00390625 |
| L + 12 | 0.48828125 | 0.44140625 |
| L + 13 | 1.3671875 | 0.87890625 |

The player lands on frame 46, 27 frames after L, so D = G + 25 = 71.

### Evidence (second character)

Measured on three ledges with a steep face at the top, rolling right,
the second character under control after a swap and the first character
following (no position changes before the roll, except where given
below): normal, slow and fast rolls started standing or walking from
many distances, Action tapped or held; walking rolls from starts moved
by up to 3 px in steps of 1/32 or 1/16 px, to find rolls that leave the
ground on S;
rolls from standing placed in 1/16 px steps at the top of one face; and
falls made shorter or longer by moving the player down up to 12 px or up
to 150 px after L + 4. 1,031 rolls left the ground during the roll (642
normal tapped, 295 normal held, 41 and 10 slow, 33 and 10 fast; L − S
from 0 to 23, 36 of them with L = S), and all match the rules above with
0 mismatches: vertical moves on 23,853 airborne frames (7,265 of them
hover frames), roll speed on 52,202 frames and the end of 941 rolls,
150 of them after a last fall of exactly 25 frames and 150 after more
than one landing; 65 rolls pushed off at the top of the face. With the
first character's values instead, the runs mismatch: hover frames
688 rolls, the end 941 (all), sizes 3 and 4 for normal and fast
rolls 915,
the size after a push 3.375 all 65, and without the 25-frame exception
150. Left out: frames after a roll hit a wall (61 rolls) or knocked out
a walker on the floor below (29 rolls), and the leave frames of later
ledges.

## Trace

The fall frames of a roll are `roll` with `ground` 0; `leave_ground` is
on L. Stopped frames before a ledge are `roll` with `vx` 0. When a
jump starts in the fall, `roll_end` is on P (its state is
`fall`) and `jump_start` on T (state `jump`, or as on the ground if T
lands).

## In `title-2`

Everything in this section was measured on `title-2` only, with the lead
character, rolling right and left off both ends of a raised stretch of
ground 64 px above flat floor (the site of `walk-off.md`, "In
`title-2`": the ground simply ends, with no face). Rolls were slow,
normal and fast. They started standing, walking, or from a press
remembered through a stop or a landing, at many distances from the
edge, with many patterns of input in the fall. Longer falls (up to about
260 px) were made by moving the player up partway through a fall without
changing anything else. Speeds are whole multiples of 1/256 px per
frame, written both ways.

Names: **S**, **D** and the stop are as in `roll-input.md` and
`roll-speed.md` ("In `title-2`"): S is the roll's first frame and, for a
roll on the ground, D = S + 31. **P** is the frame of the Action press
that started the roll: S itself, or an earlier frame for a press
remembered through a stop or a landing (`roll-input.md`). **L** is the
leave frame (the first airborne frame) and **A** the landing frame, as
in `walk-off.md` ("In `title-2`"). (`roll-input.md` calls the landing
frame L in its "In the air" section. Here L is always the leave frame.)
A jump starts on **G** and takes off on **T = G + 2**, as in
`jump-input.md` ("In `title-2`").

This section covers rolls that are still going on L: L ≤ S + 30, or
L = S + 31 = D (the roll ends on the leave frame).

### The roll in the air (`title-2`)

The roll doesn't end when the player leaves the ground. Until D, every
frame is a roll frame, in the air as on the ground:

- **Horizontal speed** follows the roll's speed rule with the roll's
  target and steering (`roll-speed.md`, "In `title-2`") on every frame,
  L included. Holding the opposite direction reverses the roll and the
  player's facing at once, in the air too. The air targets of
  `air-control.md` don't apply. Action, Down, Up and a direction in the
  roll's direction change nothing.
- If the player lands before D, the roll goes on along the ground with
  the same speed and steering. Landing changes nothing about it.

### Vertical motion (`title-2`)

Until a jump starts or the roll ends in the air:

- On **L**: as in `walk-off.md` ("In `title-2`"): the player moves down by
  3.4375 px (880/256), and `vy` ends at 0.00390625 (1/256).
- On each later frame, `vy` increases by **0.4375** (112/256), never
  above 8, and the player moves down by the new `vy`. There is no step up
  to 1.4375 on L + 1 as in a walk-off fall. So `vy` on L + 1 is
  0.44140625 (113/256).
- **Hover.** If L ≤ P + 10, the player doesn't move vertically on any
  frame from **L + 2 to P + 12**, and `vy` is 0 at the end of each of
  them. From P + 13, `vy` increases by 0.4375 per frame again, starting
  from 0. Horizontal motion goes on as usual during the hover. If
  L ≥ P + 11 there is no hover.
- **Landing** is as in `walk-off.md` ("In `title-2`"): the player lands on
  the first frame whose move would take them to the floor or below it
  (exactly reaching the floor lands too), and is placed on the floor.

So a roll that starts close to the edge hangs in the air for a moment.
The hover ends at a fixed time after the press, not after the leave
frame. A roll started 3 frames before the edge (P = S, L = S + 3)
hovers on S + 5 to S + 12, 8 frames. A roll that started from a
remembered press hovers less, or not at all, because P is earlier than S.

### How long the roll lasts (`title-2`)

On the ground a roll lasts 31 frames (D = S + 31). A roll that is in the
air at the right moment lasts longer:

- At the end of frame **S + 28**, if the player is in the air, the roll
  is lengthened by 28 frames: D becomes S + 59. "In the air" means L or
  later and before the landing frame. The landing frame counts as on the
  ground, and L counts as in the air.
- At the end of **S + 56**, if the roll was lengthened and the player is
  still in the air, it is lengthened again: D becomes S + 87. The same
  check is made every 28 frames for as long as the roll keeps being
  lengthened.

So landing on S + 28 or earlier leaves D = S + 31, and landing on S + 29
or later gives D = S + 59 (up to S + 56; landing on S + 57 or later
gives S + 87, and so on). Measured falls: a roll started on the ground
next to the edge by a press on S (hovering) lands on S + 29 off the 64 px
ledge, so its roll lasts to S + 58. One that leaves on S + 11 doesn't hover and lands
on S + 28, so it ends at D = S + 31 on the floor. A roll that leaves on
S + 29 or later is still on the ground at the end of S + 28, so it ends
at D = S + 31, in the air.

How the roll ends on D (carry-on or stop, `roll-input.md`) doesn't
change: Action held on every frame from S to D (and not a fast roll)
carries on into running, otherwise a stop follows. A roll that lands
before D ends on the ground exactly as on flat ground.

### A roll that ends in the air (`title-2`)

If D comes before the landing:

- **Stop.** On D the player moves by one more step of the speed rule and
  `vx` is 0 at the end of the frame, as on the ground. From D + 1 to
  D + 14 the player doesn't move horizontally (`vx` 0, target 0). They
  keep falling: `vy` increases by 0.4375 per frame (never above 8). On
  **D + 15**, `vy` becomes **1.4375** (368/256), whatever it was, even
  if it was much larger. From D + 16, `vy` increases by 0.4375 again.
  From D + 15 the horizontal speed follows `air-control.md` ("In
  `title-2`"), with the target chosen from the previous frame's input.
- **Carry-on.** D is still a roll frame for horizontal speed and steering,
  and `vy` increases by 0.4375. On **D + 1**, `vy` becomes 1.4375 and the
  air rules apply from D + 1.

In both cases D + 15 (stop) or D + 1 (carry-on) plays the part of L + 1
in a walk-off fall.

### Jumping (`title-2`)

A Jump press on a frame in the table starts a jump on G = that frame.
T = G + 2.

| Press frame | Jump? | Horizontal on G to T | Gravity on G to T |
|---|---|---|---|
| Any roll frame from L to D − 1, or D itself | yes | roll frames: roll target and steering, even after D | 0.3515625 (90/256), whatever is held |
| D + 1 to D + 14 of a stop in the air | yes | target 0, `vx` 0, no movement | 0.3515625 if Jump is held on every frame from G, else 0.4375 |
| D + 15 of a stop in the air | yes | air rules | as the line above |
| D + 1 of a carry-on in the air | yes | air rules | as the line above |
| Any later airborne frame | no | | |

Vertical motion on G to T, with g(f) the gravity in the table:

- On frames before T: `vy` increases by g(f) and the player moves down by
  the new `vy`. The step to 1.4375 doesn't happen on a frame where a
  jump is waiting to take off. A jump pressed on L (or on L − 1 or
  L − 2 on the ground) makes the move on L 3 + g(L) px.
- During a hover the player stays still on these frames too, T included.
- On **T**: the player moves down by `vy` + g(T) (never more than 8), then
  `vy` becomes **−8.21875** (−2104/256).
- From **T + 1**: `jump-arc.md`, "Each frame after T (`title-2`)", with
  this G, and the air rules of `air-control.md`. The roll is over and no
  stop follows.

If the player lands on G, G + 1 or T, they are placed on the floor and
the jump still takes off on T, from the ground.

The gravity before take-off differs from the jump grace period of a
walk-off fall: for a jump pressed on a roll frame it is always the
lighter one, even if Jump is tapped for one frame.

### Action in the fall (`title-2`)

An Action press never starts a roll in the air. After a roll that ended
in a stop in the air, a press held through the landing starts a roll on
**A + 2** if it was pressed on A − 9 or later (at most 11 frames before
A + 2, the usual memory of `roll-input.md`). A press on A or A + 1
starts nothing unless it is held to A + 2. The roll then starts on
A + 2 and not on the press frame. Measured for presses from the stop to
A + 1 after the same fall.

### Examples (`title-2`)

Off a ledge 64 px above flat floor, rolling right with Right held.
Heights are below the ledge's ground, after each frame.

**Hover.** Walking right, Action pressed on S, 3 frames before the edge
(P = S, L = S + 3):

| Frame | Below the ledge (px) | `vy` |
|---|---|---|
| L = S + 3 | 3.4375 | 0.00390625 |
| L + 1 | 3.87890625 | 0.44140625 |
| L + 2 to S + 12 | 3.87890625 | 0 |
| S + 13 | 4.31640625 | 0.4375 |
| S + 28 | 63.37890625 | 7 |
| S + 29 (A) | 64 (lands) | |

The player is in the air at the end of S + 28, so D = S + 59. The roll
goes on along the floor to S + 58, and the stop is S + 59 to S + 73
(Action was tapped).

**Long roll.** Walking right, Action pressed 24 frames before the edge
(L = S + 24, no hover): the player lands on S + 41, rolls on along the
floor to S + 58, and the stop starts on D = S + 59.

**Stop in the air.** Standing 84 px from the edge, Right held, Action
tapped on S: the roll's last frame is S + 30 and D = S + 31 is also L.
On D the player moves its last step and leaves the ground. From D + 1
to D + 14 it falls straight down (`vy` 0.44140625 on D + 1, rising by
0.4375 to 6.12890625 on D + 14, 49.4296875 px below the ledge). On
D + 15 `vy` is 1.4375, and the player lands on D + 20 having moved
right again under the air rules from D + 15.

### Evidence (`title-2`)

565 runs in which a roll was still going when the player left the ground
match this section with 0 mismatches:

- vertical moves and `vy` on 15,972 airborne frames, 697 of them hover
  frames;
- landings on 405 falls onto known flat floor;
- 151 rolls ending without lengthening, 256 lengthened once, 16 twice
  and 1 three times (the long falls);
- 46 rolls from a remembered press;
- roll speed and steering on 8,013 airborne roll frames and 12,963 roll
  frames on the ground; stops on 1,097 airborne frames;
- 144 jumps in the air: 104 pressed on roll frames, 35 during a stop, 2
  on D + 15 of a stop and 3 on D + 1 of a carry-on.

Two sets of random inputs (Right, Left, Up, Down, Jump and Action
around the edge) were recorded as holdouts. The first, recorded before
any model existed, showed that the hover depends on the press and not
on S (2 runs where the roll came from a press remembered through a
landing; 2 more runs differed only through mistakes in the checking
script). The second (45 rolls off the ledge) was recorded after that
change and matched with no change. The gravity of jumps pressed during
a stop in the air was found in a later targeted set. Landings with Action
held and landings away from the flat floor were left out (see below).

### Not covered in `title-2`

- Ledges of other heights. With falls shorter than these, landing
  during the stop in the air, or before L + 2, wasn't seen.
- Ledges with a face or a slope at the top, landing on slopes, and the
  roll going on along a slope after landing.
- Facing after a roll ends in the air (stop or carry-on).
- Jumping on landing after a roll's fall. Action presses held into the
  landing other than after a stop in the air.
- Landing with Action held next to an object that can be carried (it
  was seen to be picked up).
- Walls and enemies met in the fall, and the second character.

## Not covered

This list is for `title-1`; `title-2` has its own list above.

- Frame L's horizontal speed on ledges with a face at the top, and the
  frames just before it there.
- Slow rolls toward the ledges with a long face at the top, and whether
  ground exactly 48 px below counts.
- Stopped frames while both Left and Right are held; and whether
  carry-on frames (D to D + 3) can be stopped.
- How long a roll lasts when it is pushed off and still on the ground
  at S + 29 (not seen: pushed rolls always went off the edge first).
- Rolls that start in the stop after an earlier roll.
- The second character: ledges where the ground simply ends, rolling
  left, jumps pressed during the fall, the conditions of a stopped
  frame (only the stops and pushes at the top of one face in "The
  second character" were measured), and the first character following
  it.
- Rolls that hit a wall or an enemy during the fall or after landing.
- Jumps pressed during a hover on ledges with a face at the top (the
  hover jumps above were measured where the ground simply ends).
- Ledges of other heights where the ground simply ends.
