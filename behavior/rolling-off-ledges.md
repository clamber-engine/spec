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
  the frame). When the frame's steering (`roll-speed.md`) turns a roll
  that is moving at the start of the frame, the new direction counts:
  a slow roll turned away from the edge moves on. A stopped roll that
  is turned pushes off instead (below). Floor up to 47 px below the player's feet counts as ground
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

## The stop after a roll, next to an edge

The test of "Stopping before a ledge" also applies on the frames of a
roll's stop (`roll-input.md`, "How a roll ends": D to D + 3, while the
player is still moving). A frame of the stop on the ground is a stopped
frame when both hold at its start:

- `|vx|` is at most 1.05859375 (271/256), and
- there is no ground 15 px ahead (as above, in the direction of the
  roll).

On a stopped frame `vx` becomes 0 and the player doesn't move, so the
stop ends early. This only matters when the roll was slow at the end,
for example after the direction opposite to the roll was held for several
frames, which slows and reverses it (`roll-speed.md`), or when the roll
stops within 15 px of the edge at a speed close to 1.

Examples, rolling right toward an edge whose last column with ground is
E − 1, the roll ending in a stop (Action released during the roll), Right
and Action held on D to D + 3:

- `vx` is 1.0039 on D and floor(x) is E − 15 after D: `vx` is 0 on D + 1
  and the player doesn't move. With ground 15 px ahead (floor(x) at most
  E − 16), `vx` would be about 0.88 on D + 1, as the stop's speed rule
  gives (`roll-speed.md`).
- `vx` is 1.2421875 on D (not slow), 1.0898 on D + 1 and 0.957 on D + 2,
  floor(x) = E − 14 from D + 1 on: the player moves on D + 1 and D + 2;
  D + 3 starts with `vx` 0.957 and is a stopped frame, so the player doesn't
  make the move that D + 3 has otherwise.

Measured with the lead character on the first level's ground, rolling
and turning right at the end of a flat stretch, in 420 runs (14 starting
distances from the edge, Left held for 4 to 8 frames in the roll and then
Right and Action again, Action released for one frame early in the roll).
In every one of the 2 197 frames from D − 2 to D + 3 that started with
`vx` above 0 and Right and Action held, the player stood still with `vx`
0 exactly on those with `vx` at most 1.05859375 and no ground 15 px
ahead (24 frames, 14 of them from D to D + 3), and moved on the others
(131 slow frames with ground ahead, 2 042 not slow). Not measured:
the same stop at a left edge (runs at one left edge stopped earlier than
the rule gives when a direction was pressed on D + 1 to D + 3; not
worked out); a direction pressed
toward the edge on D + 1 to D + 3 after the roll was reversed away from
it, where runs also stopped earlier than the rule gives; a roll that carries on into running
(`roll-input.md`) when its `vx` is that slow on D + 1 to D + 3.

## A roll started in the stop after a roll

After a roll that ends in a stop on the ground, the next roll can start
on D + 17 (from a press remembered from D − 2 to D + 16, or a new press
on D + 17) or later (`roll-input.md`, "Starting the next roll"). Call
its first frame **S′**. Near a ledge where the ground simply ends, that
roll follows this file with these differences and additions.

**A roll started on D + 17.** It lasts 30 frames on the ground
(`roll-input.md`). S′ is its press frame, whether the press was
remembered or new: what is held on S′ sets the roll's size and
direction (`roll-speed.md`). On S′ its speed stays 0 and the player
doesn't move; from S′ + 1:

- S′ is a stopped frame when there is no ground 15 px ahead in the
  direction set on S′ ("Stopping before a ledge"); with ground there it
  isn't, even though the player doesn't move.
- "Stopping before a ledge" and "Pushing off" apply as usual from
  S′ + 1, with the steering of each frame (`roll-speed.md`). So standing
  within 15 px of the edge, facing it, with the direction toward it
  held on S′ + 1, the roll pushes off at 2 on S′ + 1. With the other
  direction held on S′ + 1 it turns and pushes off away from the edge
  at 2 on the same frame.
- Facing the edge there with the direction away from it held on S′
  only, the roll goes away from the edge as a normal roll. Held away on
  S′ and toward the edge from S′ + 1, S′ isn't a stopped frame, so
  S′ + 1 is a stopped frame (the turned roll's new direction counts)
  and the roll pushes off toward the edge on S′ + 2.
- The hover depends on **n = L − S′** like this (n = 0 can't happen):

  | n | Hover frames |
  |---|---|
  | 1 to 8 | L and the 9 frames after it |
  | 9 | L only |
  | 10 or more | none |

  These are not the values of "The fall": there n = 8 hovers on L only
  and n = 9 not at all.
- Everything else is as in "The fall" and "When the roll ends": the
  roll ends on G + 30 after landing, or on G when G = L + 30, whatever
  its 30 frames on the ground would have been.

**A roll started on D + 18 or later** is an ordinary roll and follows
the rest of this file unchanged, with n = L − S′ in "The fall".

**Running in the stop.** Action and a direction held during the stop
(from D + 1 to D + 16) don't make the next roll fast (`roll-speed.md`,
"Running recently").

**When the stop carries the player off the edge.** A roll can stop at
the edge and then push off on its last frames (on D, the roll's speed
rule still applies, `roll-speed.md`). The stop's first frames then
carry the player over the edge. The stop goes on in the air with its
usual speeds (`roll-speed.md`, "When the roll ends": D + 1 and D + 2
slow down, D + 3 moves one more step, then `vx` is 0 and the player
falls straight down). The fall is like walking off a ledge: the first
airborne frame moves down by 3.4375 px, and from then on `vy` increases
by 0.4375 per frame (never above 8). A remembered press then starts its
roll on D + 17 in the air:

- S′: `vx` stays 0, and the player falls as on the frame before (`vy`
  + 0.4375).
- S′ + 1 to S′ + 9 are hover frames ("The fall"). From S′ + 10 the
  fall goes on as after any hover.
- The landing rule of `landing.md` applies on every airborne frame from
  S′ on, hover frames included: a hover frame moves down, so it lands
  when it ends less than 5 px above the floor. The player can therefore
  land on S′ itself or on any hover frame, and the landing is as any
  other (`ground` 1, `vy` 0, the roll goes on). Horizontal speed is the
  same as without the landing.
- From S′ + 1 the roll's speed rule and steering apply as in the air in
  "The fall". The roll ends on G + 30, except when the player lands
  exactly 30 frames after S′ (G = S′ + 30): then D = G, as in "When the
  roll ends" with S′ in place of L. Landings 0 to 16 and 26 to 35
  frames after S′ were measured; all but G = S′ + 30 end on G + 30,
  including landings on S′ and on hover frames. The frame on which the stop
  carried the player off (1 to 3 frames after D) makes no difference.

Example, rolling right toward an edge whose last column with ground is
E − 1: standing with floor(x) = E − 10, Action alone on frame 0. Every
roll frame is stopped and D = 29. Action is pressed again on 34 for one
frame (remembered, so S′ = 46), and Right is held from 47. Frame 46
doesn't move; on 47 the roll pushes off at 2, then 2.125, 2.234375,
2.328125. The player leaves the ground on L = 51 (n = 5), and 51 to 60
are hover frames.

Example in the air: the same edge, standing with floor(x) = E − 4,
Action alone on frame 0, so every roll frame is stopped and D = 29.
Action is pressed again on 28 for one frame, and Right is held from 29
on. On 29 (D) the roll pushes off at 2; the stop slows to 1.75 on 30
and 1.53125 on 31, which carries the player off the edge (31 moves down
by 3.4375 px), and from 32 `vx` is 0 while they fall. The remembered
press starts the roll on 46 (S′) in the air: 46 falls as usual (`vy`
6.56640625), 47 to 55 are hover frames while the speed rises toward 3
(0.375 on 47), and the fall goes on from 56 (`vy` 0.44140625).

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

Rolls started in the stop after a roll: 53,472 runs at the stretch
without a face, going right and left. The first roll was slow (started
2 to 90 px from the edge) or normal (78 to 95 px), tapped, and ended in
a stop on the ground. Action was pressed again on each frame from D − 2
to D + 25, tapped or held. Left, Right or nothing was held on the press
frame only, from it on, or from the frame after it, and in 5,088 runs
toward the edge only from S′ + 1 to S′ + 12. All runs match this file
with 0 mismatches: the frame each roll starts on, 2,283,936 roll speeds
(371,458 frames meeting the conditions of "Stopping before a ledge":
354,838 stopped and 16,620 pushed off, 2,372 of the push-offs by turning
away), 786,676 airborne vertical moves (99,074 hover frames) and the end
of 23,352 rolls that went off. On the ground, rolls started on D + 17
lasted 30 frames (19,844 from remembered presses, 4,756 from new
presses on D + 17) and later ones 29 (5,152). The rolls on D + 17 that
went off had n from 1 to 9 in 9,464 runs and 10 or more in 10,140.
Counting n from S′ + 1 in the usual table instead gives 86 mismatching
runs, and the usual table with n from S′ gives 1,056. Without the turned
roll's new direction in "Stopping before a ledge", 26 runs mismatch. If
Action and a direction held in the stop counted as running, 16,290
runs would. 40 runs whose stop carried the player off the edge (30 of
them landed) match "When the stop carries the player off the edge".
Left out: runs in which the first roll itself went off the edge, and
runs that walked off the edge before a later press.

Directions around S′ of a roll started on D + 17 from a remembered
press: 43,624 more runs at the same stretch (the same first rolls,
Action pressed on each frame from D − 2 to D + 16, tapped or held).
Toward or away from the edge was held on S′ only; on S′ and from S′ + 2;
from the press to S′; on S′ with the other direction on S′ + 1 or from
S′ + 1 on; or nothing on S′ and the direction on S′ + 1 or from S′ + 1
on. All match this file with 0 mismatches. Setting the size and
direction on S′ + 1 instead of S′ gives 27,070 mismatching runs, and
deciding the zone's direction on S′ + 1 without counting S′ as a stopped
frame gives 4,256. Landings of a roll started in the air: 2,684 runs
whose stop carried the player off, with the player moved up or down
during the stop's fall so that the roll landed 26 to 35 frames after S′
(352 of them exactly 30), match "When the stop carries the player off
the edge" with 0 mismatches.
Landings on S′ and during the hover: 1,728 more such runs (stops
carrying the player off 1, 2 or 3 frames after D, the roll steered
toward the floor below, Action tapped or held), with the player's
height set, on one frame from S′ − 1 to S′ + 10, to between 1 and 16 px
above the floor, including every 1/256 px from 5 to 5.046875. They
landed 0 to 16 frames after S′ and match this file with 0 mismatches on
17,340 airborne frames, the frame of each landing and each roll's end.
The horizontal speeds were the same as in the runs that landed later.
Without the landing rule on hover frames, 486 runs mismatch (landings
1 to 9 frames after S′).

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

On the three ledges with a face, 6,336 runs by inputs (normal, slow and
fast rolls going right, Action tapped or held, Jump pressed on each
frame from L to L + 17 and held 1, 5, 12 or 40 frames) were made, and
the 4,485 of them that rolled off and were still in the air on P (L − S
from 1 to 7 and from 9 to 23) match "Jumping during the fall" with 0
mismatches on 230,981 vertical moves and 219,689 horizontal speeds (L
excepted, see "The fall"), to landing: 1,200 presses on hover frames,
139 presses on an L that wasn't a hover frame, and 4,320 frames that
would have been hover frames without the press. In all of them the roll
ends on P and no stop follows the landing. With the second character's
jump values instead (`second-character.md`), or with the jump rule on P
instead of a kept hover frame, 1,200 to 4,421 runs mismatch. Left out:
152 runs that fell into a pit, frames after a wall stopped the player,
and the horizontal speeds of 144 rolls pushed off at the top of a face.

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

**Jumping during the fall** is as in "Jumping during the fall",
including the two kinds of P there (a hover frame, and an L that isn't
one), with this character's jump (`second-character.md`, "Values"):

- g(f) is **0.3515625** (90/256) if Jump has been held on every frame
  from P to f, and 0.4375 otherwise.
- On T, `vy` becomes **−8.3515625** (−2138/256).
- From T, the air targets are ±2.25 (576/256), ±3.375 (864/256) with
  Action held, or 0 (`second-character.md`, "Jumping out of a roll").
- A press on an L that isn't a hover frame moves the player down by
  **3.3515625** px on P (3 + g(P), as the first character's 3.28125 is
  3 + 0.28125), and `vy` ends at 0.00390625.

As for the first character, P is still a roll frame for horizontal
speed, a press on a hover frame keeps P a hover frame, no frame from T
on is a hover frame, the jump ends the roll, a move on T that would
land the player lands them on T, and no stop follows the landing.

Example: the hover example above (L = S + 7, hover frames L to L + 11),
with Jump pressed on L + 3 and held 12 frames:

| Frame | Moved down by | `vy` after the frame | `vx` after the frame |
|---|---|---|---|
| L to L + 2 | 0.00390625 each | 0.00390625 | (roll) |
| P = L + 3 | 0.00390625 | 0.00390625 | 3.12890625 |
| T = L + 4 | 0.35546875 | −8.3515625 | 3.01953125 |
| L + 5 | −8 | −8 | 2.92578125 |
| L + 6 | −7.6484375 | −7.6484375 | 2.84375 |

On T, `vx` moves toward 2.25 (Right held, Action released). With Jump
tapped on P alone, T moves the player down by 0.44140625 and L + 5 by
−7.9140625.

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

Jumps during the fall, on the same three ledges: 8,176 runs (normal,
slow and fast rolls going right, Action tapped or held, Jump pressed on
each frame from L to L + 17 and held 1, 5, 12 or 40 frames; 40 of them
steering back or releasing Right from L + 1; 2,016 from standing starts
moved by up to 3 px in 1/64 px steps to reach L = S). In 4,869 of them
Jump was pressed in the fall of a roll (L − S from 0 to 23), and all
match the rules above with 0 mismatches on 245,385 vertical moves and
240,522 horizontal speeds, to landing: 1,807 presses on hover frames,
137 on an L that wasn't one, 88 jumps landing on T, and 8,728 frames
that would have been hover frames without the press. In all of them the
roll ends on P and no stop follows the landing. With the first
character's values instead, runs mismatch: `vy` −7 on T 4,780, held
gravity 0.28125 4,238, air targets ±2 and ±3 4,196, and the jump rule on
P instead of a kept hover frame 1,807. Left out: frames after the roll
or jump knocked out a walker (726 runs), fell into a pit (685) or met a
wall (246), and 4 runs where rising ground pushed the player up just
before landing.

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
- The second character: ledges where the ground simply ends, rolling
  left, the conditions of a stopped
  frame (only the stops and pushes at the top of one face in "The
  second character" were measured), and the first character following
  it.
- Rolls that hit a wall or an enemy during the fall or after landing.
- Jumps pressed during a hover on ledges with a face at the top when
  L is S or S + 8 (not seen there with the first character leading).
- Jump pressed on S itself when the player also leaves the ground on S
  (with Action: it gave a jump and no roll).
- Ledges of other heights where the ground simply ends.
