# Roll input

Titles: `title-1`, `title-2`

When an Action press starts a roll, and when it doesn't. Frames are logic
frames at 60 Hz, numbered as in a trace. "Action" and "Down" are the
neutral inputs `A` and `D` of the scenario format. Measured on flat ground
with the lead character.

The sections below up to "In `title-2`" give the `title-1` values.
`title-2` is different throughout; "In `title-2`" gives its complete
values.

A **press** of Action is a frame where Action is held after a frame where
it was not held. Holding Action never starts a second roll by itself; every
roll needs its own press. Running is Action *held* while moving; a press
always rolls first (see "Rolling into a run").

## On the ground

A press on frame P while grounded starts a roll on frame P (`roll_start`
on P), unless Down is held on frame P (Down makes no difference during
a roll and the stop after one; see "Down during a roll"), or the player
is standing within
reach of an object that can be carried (then it is picked up instead; see
`carrying.md`).

This does not depend on:

- speed: standing still, any point while speeding up to walking speed,
  walking at full speed, sliding to a stop after releasing the direction,
  or running (Action released for one frame and pressed again);
- direction: nothing, Left, Right, Up, or the direction opposite to the
  current motion, held or pressed on the same frame;
- how long Action is held after the press (one frame is enough).

With the first character under control, Down held on the press frame
(pressed together with Action, or held earlier) means **no roll**: the
press starts the ground action instead (`ground-action.md`). With the
second character under control, Down makes no difference and the press
starts a roll. Down pressed after a roll has started has no effect on the
roll.

## In the air

A press in the air never starts a roll in the air. It is remembered
through landing only while held, and only if it came late.

With landing frame L (the frame carrying `land`) and press frame P ≤ L,
a roll starts on frame L + 1 if both hold:

- P ≥ L − 6, and
- Action is held on every frame from P to L + 1.

Otherwise nothing happens on landing. A press on L + 1 or later starts a
roll on that frame, as on the ground.

| Case | Result |
|---|---|
| Pressed 1 to 6 frames before L, held through L + 1 | Roll on L + 1 |
| Pressed on L, held through L + 1 | Roll on L + 1 |
| Pressed 7 or more frames before L, held through landing | No roll |
| Pressed before or on L, released before L + 1 | No roll |
| Pressed on L + 1 or later | Roll on the press frame |

Measured for five jump heights, standing and walking; the window doesn't
change with airtime. A remembered press starts a roll on L + 1 even when
Down is held (`ground-action.md`).

## How a roll ends

A roll lasts 29 frames, S to S + 28, where S is the `roll_start` frame
(one exception below; a roll that defeats a walker lasts longer and
always carries on into running, see `rolling-into-walkers.md`). What
happens next depends on frame **D** = S + 29:

**Action held on every frame from S to D** (pressed once and never
released), and the roll is not a fast roll (`roll-speed.md`): the roll
carries on into running. The player is still in `roll` on D, D + 1 and
D + 2, and `roll_end` is on D + 3. From D + 3 the player walks or runs
normally (the speed on D + 3 still follows the roll; see
`roll-speed.md`).

**Otherwise** (Action not held on D, or released at any time during the
roll, even if pressed again, or the roll is a fast roll): `roll_end` is
on D and the player comes to a stop:

- D: horizontal speed still as in the roll;
- D + 1 and D + 2: the speed slows toward 0 by the speed rule of
  `ground-speed.md` (about ×0.876 per frame), whatever is held;
- D + 3 to D + 17: speed is 0, whatever direction is held (on D + 3
  the player still moves; see `roll-speed.md`). A direction held on
  D + 17 doesn't move the player yet;
- from D + 18: normal movement. With a direction held on D + 18 (pressed
  then or at any earlier time), the speed on D + 18 is the first step of
  the `ground-speed.md` rule from 0.

Inputs other than a direction already act on D + 17: a roll can start
on D + 17 (next section). Its speed stays 0 on that frame and the player
first moves on D + 18 (`roll-speed.md`).

While the speed is 0 in the stop, facing turns to a direction on the
frame it is pressed (measured for presses on D + 14 to D + 17).

The stop is not part of the roll: while the speed is not 0 the trace
state is `walk` (or `run` if Action is held), then `idle`. A jump can start during the
stop as usual (`behavior/jump-input.md`).

## Starting the next roll

After a roll that carried on into running (Action held through D): a new
press on P ≥ D + 4 starts a roll on P. A press on D + 2 or D + 3 starts
a roll on D + 4, the frame after `roll_end`, if Action is still held on
D + 4; if it is released before D + 4, that press starts no roll.

**When D + 4 starts on a steep face** (`steep-faces.md`; "starts on a
face" as in "Jumping on or next to a face" there: the player is being
carried down a face at the end of D + 3), a press on D + 2 or D + 3 starts
no roll on D + 4. It works like a carried jump press:

- Let **Q** be the first frame after D + 3 that starts with the player on
  the floor on a column that isn't a face column. Walking or coasting off
  the low end of the face, Q is the first frame that starts beyond its
  last column; if the player leaves the ground on the face and lands on
  other floor, Q is the frame after the landing frame.
- If Action is held on every frame from P to Q and Q ≤ P + 7, a roll
  starts on Q (`roll_start` on Q; an ordinary roll of 29 frames).
- Otherwise no roll starts. A new press is needed.

Until Q the press changes nothing: every frame from D + 1 to Q − 1 is the
same as if Action had been held without a break from the roll on, with no
new press. Down held on P or on D + 4 doesn't change any of this.

For example, a roll that carried on is being carried down a face falling
to the right at about 1 px per frame on D to D + 3. Action is released on
D + 1, pressed again on D + 2 and held. The player is carried down the
face on D + 4 to D + 8, and D + 9 takes them past its last column onto
the floor at its foot, so Q = D + 10 = P + 8: too late, and no roll
starts. Had the
face ended one column sooner, so that Q = D + 9 = P + 7, the roll would
start on D + 9.

Checked with the first character on the 32-column face falling to the
right of `steep-faces.md` ("Evidence (rolls)"), from 30 400 runs: from
standing on the floor at the face's top, a roll toward the face, turned
by holding the other direction as it moves onto the face so that it is
stopped and carried down (`steep-faces.md`, "Stopped rolls"), Action held
to D or D + 1, then a press on D + 2 or D + 3 held for 1 to 20 frames,
Down on P and on D + 4 or not, and Left, Right or nothing held after D.
In the 6 310 runs in which D + 4 started on the face, a roll started on Q
in all 1 222 in which Action was held from P to Q with Q ≤ P + 7, and no
roll started from D + 4 on in the other 5 088 (1 473 of them with Action
held from P to Q, Q from P + 8 to P + 13). Each was played again with
Action held without a break instead of the release and the new press:
all 65 229 frames from D + 1 to Q − 1 were the same. With the limit at
P + 8 or P + 6 instead, 102 and 204 runs differ. In the 7 225 runs in
which D + 4 started on other floor, the rule above held (a roll on D + 4
exactly when Action was held then).

After a roll that ended in a stop: a press on P with D − 2 ≤ P ≤ D + 16
is remembered, even if Action is held for one frame only, and the next
roll starts on D + 17. A press during the roll before D − 2 is ignored. A
press on D + 17 or later starts a roll on that frame.

**Exception:** a roll started on D + 17 from a remembered press lasts 30
frames instead of 29 (S to S + 29, so D = S + 30). Everything in this
section and the previous one holds with that D.

## Down during a roll

From S + 1 to D + 17 of a roll that ends in a stop, and from S + 1 to
D + 3 of a roll that carries on into running, Down makes no difference
at all: everything happens as it would without it. So:

- a press with Down held during the roll or the stop is remembered or
  ignored as above, and a remembered press starts its roll on D + 17
  even with Down held then; a press on D + 17 with Down held starts a
  roll on D + 17;
- after a roll that carried on into running, a press on D + 2 or D + 3
  with Down held starts a roll on D + 4 if Action is still held on
  D + 4, with or without Down on D + 4;
- a crouch (`ground-speed.md`, "Crouching") or the ground action
  (`ground-action.md`) starts no earlier than D + 18 (stop) or D + 4
  (carried on), and only if Down is held on that frame. Down held without
  a break from earlier is the same as Down first held then: after a stop,
  Down held from the roll on through D + 18 makes D + 18 the crouch's
  first frame.

For example, walking right with Right held throughout, Action is pressed
on frame 20 and held to 49: the roll is frames 20 to 48 and carries on
into running (D = 49, `roll_end` on 52). Action is pressed again on 52
for one frame and Down is held on 53 (D + 4) only: the player crouches
from 53 to 65 and no roll starts. With Action held on 52 and 53 instead,
the roll starts on 53 whether Down is held on 53 or not. After a
one-frame press from standing on frame 10 (roll 10 to 38, D = 39), Down
held from frame 30 on and Action pressed again on 57 (D + 18): the
ground action starts on 57; pressed on 56 (D + 17) instead, a roll
starts on 56.

Checked on flat ground with the first character, from standing facing
either way, with 19 200 runs of 200 frames: Action pressed for 1 to 6
frames or held for 40 to 89, Down held for 1 to 40 frames at a time,
Left, Right or neither in stretches, no Jump; half of the runs begin with
a roll that carries on into running. Each run was played again with Down
taken off from S + 1 to D + 17 (stop) or D + 3 (carried on) of every
roll: all 3 664 375 frames were the same in both (`x`, `y`, `vx`,
`facing`, `ground` and the player's state), 396 927 of them with Down
taken off and 9 631 Action presses among those. Taking Down off one frame
longer as well (D + 18 or D + 4) changed 180 970 frames in 3 150 of
9 600 runs. Presses on D + 2 or D + 3 after a roll that carried on: 367
with Action held on D + 4 all started a roll there (51 with Down held on
D + 4); 186 released before D + 4 started none (35 crouched on D + 4,
all with Down held then).

With the first character these rules hold on 1-in-4 slopes as well
(`slopes.md`), uphill and downhill. Checked with 19 200 more runs of the
same kind, from standing still facing either way on the flat floor just
before a dip, on its downhill side, at its flat bottom and on its uphill
side, each run stopping when the player left the dip and the flat floor
just before it: all 2 730 783 frames were the same with Down taken off
as above, 149 770 of them with Down taken off while the player was on a
slope, and 3 605 Action presses among those. Over the runs, 623 155 roll
frames, 199 785 stop frames and 21 131 frames of rolls carrying on into
running were on a slope. Taking Down off one frame longer changed
122 467 frames in 2 282 of 9 600 runs. Presses on D + 2 or D + 3 after
a roll that carried on: 311 with Action held on D + 4 all started a roll
there; 164 released before D + 4 started none.

With the first character they also hold next to and on steep faces
(`steep-faces.md`): rolls going down a face or up one, blocked or stopped in front of a rise, and
rolls that leave a face or a vertical step and go on in the air. Checked
with 19 200 more runs of the same kind, from standing still facing either
way on the floor at the top of a face falling to the right, on the floor
at its foot, and on the floor in front of a face rising to the right, each
run stopping when the player left that stretch of floor: all 2 589 411
frames were the same with Down taken off as above, 7 186 of them with
Down taken off while the player was on a face, and 193 Action presses
among those. Over the runs, 42 555 roll frames, 3 132 stop frames and 153
frames of rolls carrying on into running were on a face, 183 782 frames
of the three kinds had `vx` 0 within 16 px in front of the rise, and
217 171 were in the air. Taking Down off one frame longer changed 60 446
frames in 1 813 of 9 600 runs. Presses on D + 2 or D + 3 after a roll
that carried on, on the floor and with D + 4 starting on the floor: 212
with Action held on D + 4 all started a roll there (25 with Down held on
D + 4); 120 released before D + 4 started none (25 crouched on D + 4, all
with Down held then). Presses made in the air follow "In the air".

The second character is the same with its own D and with D + 6 in place
of D + 3 (`second-character.md`, "Rolls").

## Rolling into a run

Pressing Action while walking and keeping it held gives a roll followed by
running. Scenario `run-jump-flat` presses Action on frame 20 while walking
and holds it: frames 20 to 51 are `roll` (`roll_start` 20, `roll_end` 52),
then `run`. Speed keeps rising toward running speed throughout the roll.

## Worked example

Standing still, Action is pressed on frame 10 for one frame. The roll is
frames 10 to 38 (D = 39), `roll_end` on 39. The player slows on 40 and 41,
stands still from 42 to 56 (`idle`), and moves normally again from 57. A second one-frame press
on frame 37 starts the next roll on frame 56. A press on frame 36 would be
ignored.

## In `title-2`

Everything in this section was measured on `title-2` only, with the lead
character at the start of a game, on flat ground. A **press** of Action
is, as above, a frame where Action is held after a frame where it was not
held. Speed, size and steering during a roll are in `roll-speed.md` ("In
`title-2`"). This section names frames. In traces, the roll's 31 frames
(S to S + 30, below) have state `roll`, with `roll_start` on S; D is the
first frame after the roll and carries `roll_end`, whether the roll ends
in a stop or carries on into running (see
`traces/reference/title-2/roll-flat.trace`).

### On the ground (`title-2`)

A press on a grounded frame P starts a roll on P (S = P), unless:

- Down is held on P (the player crouches instead; not covered further);
- Jump is also pressed on P: the jump starts and no roll does (Action
  then counts as held, for example for air control);
- P is during a roll or the stop after one, or on G + 1 or T of a jump
  (`air-control.md`, "In `title-2`"). See below for presses during the
  stop.

Any speed, any direction (or none), and a press on any frame after a
landing (from the frame after the landing frame) start a roll the same
way. One frame of Action is enough.

### In the air (`title-2`)

A press in the air never starts a roll in the air. With landing frame L
(the frame carrying `land`, as in `jump-input.md`) and a press on P ≤ L,
a roll starts on L + 1 if both hold:

- P ≥ L − 10, and
- Action is held on every frame from P to L + 1.

Otherwise nothing happens on landing. A one-frame press on L itself does
nothing. A press on L + 1 or later starts a roll on that frame, as on the
ground.

If a jump starts on L + 1 (a Jump press on L + 1, or a Jump press made
in the air and held into landing as in `jump-input.md`, "Landing
(`title-2`)"), the jump wins and no roll starts.

Measured for three jump heights (landing 39, 45 and 46 frames after the
jump press); the window doesn't change with airtime.

After a fall that didn't come from a jump (walking or running off a
ledge), the roll comes one frame later: a press on L + 1, or one made in
the air on L − 4 to L, held through L + 2, starts a roll on L + 2
(`ground-speed.md`, "After a landing (`title-2`)"). Earlier presses in
the air weren't measured after falls.

### How long a roll lasts (`title-2`)

A roll lasts 31 frames, S to S + 30. What happens next depends on frame
**D** = S + 31:

- **Action held on every frame from S to D** (pressed once and never
  released), and the roll is not a fast roll: the roll **carries on into
  running**. D still moves at the roll's speed, and from D + 1 the player
  walks or runs normally.
- **Otherwise** (Action not held on D, or released at any time during the
  roll, even if pressed again, or the roll is a fast roll): the roll ends
  in a **stop**, D to D + 14. The player doesn't move from D + 1 to
  D + 14 and moves normally again from D + 15. Facing doesn't change
  from D + 1 to D + 14, and a turn toward a direction held then starts on
  D + 15.
- A Jump press on any frame from S + 1 to D ends the roll in a jump
  instead (`roll-speed.md`, "Jumping out of a roll"). A Jump press during
  the stop also starts a jump.

### Starting the next roll (`title-2`)

After a roll that carried on into running, a new press (on D + 2 at the
earliest, since Action is held on D) starts a roll on that frame.

Presses during a roll (S + 1 to D) are ignored. Presses during the stop
are ignored, except that a press on P with D + 4 ≤ P ≤ D + 14, with
Action held on every frame from P to D + 15, starts the next roll on
D + 15. A press on D + 15 or later starts a roll on that frame.

The remembered press after a landing and this one follow the same
pattern: a press that can't start a roll yet starts one on the first
frame it can, if that frame is at most 11 frames after the press and
Action is held until then.

| Press during the stop | Held through D + 15 | Result |
|---|---|---|
| D + 1 to D + 3 | either | No roll |
| D + 4 to D + 14 | yes | Roll on D + 15 |
| D + 4 to D + 14 | no | No roll |
| D + 15 or later | (any) | Roll on the press frame |

A roll that starts from a remembered press is like any other: it moves on
its first frame, lasts 31 frames, and carries on into running if Action
stays held through its D.

### Worked example (`title-2`)

Standing still, Action is pressed on frame 10 for one frame. The roll is
frames 10 to 40 (D = 41). The player stops on 41 and stands still from
42 to 55, and moves normally again from 56. A one-frame press on any
frame from 11 to 55 is ignored. A press on 45 to 55 held through 56
starts the next roll on 56, as does a press on 56.

With Action held from frame 10 onwards instead, the roll carries on into
running on 41: `vx` keeps rising on 41 toward the roll's target and from
42 follows the ground rules.

### Evidence (`title-2`)

See `roll-speed.md` ("In `title-2`"): the same 1,217 runs and 2,143
rolls, including 92 rolls started on D + 15 by a press during the stop
and 220 started on L + 1 by a press in the air, with 0 mismatches.
Bounds measured directly: a press on D + 3 held through D + 15 starts
nothing and D + 4 does; in the air L − 11 starts nothing and L − 10
does, for three jump heights.

### Not covered in `title-2`

- Down held with the press, and Down held during the stop.
- Rolls into walls other than steep faces, rolls into enemies other than
  walkers (walkers are in `rolling-into-walkers.md`, "In `title-2`"), and
  the second character. Rolls off ledges are in `rolling-off-ledges.md` ("In
  `title-2`"), rolls on slopes in `slopes.md` ("In `title-2`"), rolls
  blocked by a steep face in `steep-faces.md` ("In `title-2`").

## Not covered yet

This list is for `title-1`; `title-2` has its own list above.

Roll speed and steering are in `roll-speed.md`, rolls into walkers in
`rolling-into-walkers.md`, rolling off a ledge in `rolling-off-ledges.md`, rolls on, onto and
into steep faces in `steep-faces.md`. Rolls on slopes and
rolls into other enemies. A press on D + 2 or D + 3 after a roll that
carried on, when D + 4 starts on a face falling to the left (only a face
falling to the right was measured), with Down held on Q, or with the
second character. Down held during the second character's rolls on
slopes and steep faces.
The second character's rolls, with Down held during them, are in
`second-character.md` ("Rolls"). The Down + Action ground action
is in `ground-action.md`.
