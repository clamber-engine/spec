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

- Frame L's horizontal speed and the frames just before it (the ledges
  measured all have a face at the top).
- Fast and slow rolls off ledges, rolling off to the left, rolls that
  start in the stop after an earlier roll, and the second character.
- Rolls that hit a wall or an enemy during the fall or after landing.
- Ledges without a face at the top.
