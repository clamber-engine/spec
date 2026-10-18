# Ground action (Down + Action)

Titles: `title-1`

What happens when the first character presses Action while holding Down
on the ground: which presses start the **ground action**, how long it
lasts, how the player moves during it, what other inputs do, and how it
ends. Frames are logic frames at 60 Hz, numbered as in a trace. `x`, `y`,
`vx` and `facing` are as in a trace: right and down are positive, `vx` in
px per frame. "Left", "Right", "Up", "Down", "Jump", "Action", "Swap" are
the neutral inputs `L`, `R`, `U`, `D`, `J`, `A`, `S` of the scenario
format. The **first character** is the one under control when a game
begins; the **second character** is the one that joins later
(`losing-a-character.md`).

Measured on flat ground with the first character under control, from
standing, walking, running, crouching, turning and just after landing,
with Left, Right, both or neither held on the press frame, and with
Jump, Action, Swap, Up and direction presses at many points during and
just after the action (79 actions). On every frame of 75 of them, from
the press frame to the frame after the action ends, `x`, `vx`, `y`,
`ground`, `facing` and whether the action is in progress match the rules
below. The other 4 reached an exit (see "Not covered"). What happens
when the action goes over the end of the ground is in "At a ledge
without a face".

Speeds are whole multiples of 1/256 px per frame, written both ways as
in `ground-speed.md` (768/256 = 3 px per frame).

## Which presses start it

A **press** of Action is a frame where Action is held after a frame where
it is not held (`roll-input.md`). Call the press frame **P**.

With the first character under control, a press on P while grounded
starts the ground action on P if Down is held on P. Down may be pressed
on P together with Action, or held from earlier, including while
crouching. One frame of both is enough; releasing either afterwards
changes nothing.

- **Down pressed after Action** (on P + 1 or later): no ground action; the
  press starts a roll as usual and Down doesn't affect it.
- **Action held, Down pressed later**: no ground action (there is no
  Action press). The player crouches (`ground-speed.md`).
- **In the air**: a press in the air never starts the ground action, on
  landing or later. A press that `roll-input.md` remembers through
  landing (pressed from L − 6 to L and held through L + 1) starts a
  **roll** on L + 1 even with Down held. A press on L + 1 or later with
  Down held starts the ground action on that frame.
- **Second character**: it has no ground action. With the second
  character under control, Down held on the press frame makes no
  difference; the press starts a roll as it would without Down.

## Duration

The action is in progress on frames P to P + 58 (59 frames). Call
**E** = P + 59 the first frame after it.

A Jump press ends it earlier (see "Inputs during the action"). Knocking
out an enemy makes it 2 frames longer each time (see "Enemies").

## Direction

The action has a **direction**, set on P only:

| Held on P | Direction |
|---|---|
| Right (without Left) | +1 |
| Left (without Right) | −1 |
| Neither, or both | 0 |

The direction comes only from what is held on P. It doesn't depend on
facing or on how the player was moving: walking right and releasing Right
on P gives direction 0, and Left held on P while facing right gives
direction −1, so the player moves left while still facing right.

## Speed and position

On P, the speed first becomes 0. Then, on every frame from P to P + 58,
the speed moves toward a target of direction × 3 (768/256) by the speed
rule of `ground-speed.md` (step = gap ÷ 8 rounded toward zero; if the
step is 0, the speed becomes the target), and the player moves by the
new speed. What is held on these frames makes no difference.

- **Direction 0**: `vx` is 0 on P and stays 0; the player doesn't move,
  whatever speed it had on P − 1.
- **Direction ±1**: `vx` on P is ±0.375 (96/256) whatever the speed on
  P − 1, then rises toward ±3. It reaches ±3 on P + 39 and stays there.

Example, direction +1 (any starting speed):

| Frame | `vx` (1/256) | `vx` (px/frame) | `x` − `x` on P − 1 |
|---|---|---|---|
| P | 96 | 0.375 | 0.375 |
| P + 1 | 180 | 0.703125 | 1.078125 |
| P + 2 | 253 | 0.98828125 | 2.06640625 |
| P + 3 | 317 | 1.23828125 | 3.3046875 |
| P + 4 | 373 | 1.45703125 | 4.76171875 |
| P + 9 | 564 | 2.203125 | 14.48046875 |
| P + 19 | 712 | 2.78125 | 40.3125 |
| P + 29 | 751 | 2.93359375 | 69.1328125 |
| P + 38 | 761 | 2.97265625 | 95.74609375 |
| P + 39 | 768 | 3 | 98.74609375 |
| P + 58 | 768 | 3 | 155.74609375 |
| E | 768 | 3 | 158.74609375 |

Running recently doesn't change the target (unlike rolls, `roll-speed.md`).

`facing` doesn't change from P to E. `y` doesn't change on flat ground,
and `ground` is 1 throughout.

## How it ends

On **E**, the player is no longer in the action, but the speed takes one
more step of the action's rule (toward direction × 3, as in "Speed and
position"), and the player moves by it. On flat ground `vx` is already
direction × 3 on P + 58, so it doesn't change: `vx` on E is `vx` on
P + 58. (It can differ after a steep face: "At a steep face".) What is
held on E doesn't affect the speed on E.

From **E + 1** the normal grounded rules apply (`ground-speed.md`) from
the speed on E. If Down is held on E + 1, E + 1 counts as the first frame
Down is held: the target on E + 1 is still the one set by Left, Right and
Action, and it is 0 from E + 2 while Down stays held.

Examples, direction +1, so `vx` is 3 on E:

| Held from E on | `vx` on E + 1 | `vx` on E + 2 |
|---|---|---|
| Right | 2.875 | 2.765625 |
| Right and Down | 2.875 | 2.515625 |
| Left | 2.375 | 1.828125 |
| Nothing | 2.625 | 2.296875 |

With direction 0 the player is standing still on E, and stands, walks or
crouches from E + 1 as usual.

## Inputs during the action

On frames P + 1 to E:

- **Left, Right, Up, Down**: no effect on speed, position or facing.
- **Action**: a press does nothing, with or without Down held, and it is
  not remembered: a press on E (held on into E + 1) does nothing either.
  Holding Action and Down doesn't repeat the action. A new press on
  E + 1 or later starts a new ground action if Down is held on it, or a
  roll otherwise (`roll-input.md`).
- **Swap**: a press on Q is refused while Q − 1 is a frame of the action
  (P ≤ Q − 1 ≤ P + 58), so presses up to and including E are refused. A
  press on E + 1 can start a swap.
- **Jump**: a press on any frame Q from P + 1 to E starts a jump as from
  the ground (`jump-input.md`): `jump_start` on Q + 1. The action ends on
  Q: Q is not a frame of the action, but the speed on Q still follows the
  action's rule above (target direction × 3), and the player is grounded
  on Q. From the take-off frame Q + 1 the jump follows `air-control.md`
  from the speed on Q.

Example, walking right, press on P with Right held, Jump pressed on
P + 20 and Right held throughout: `vx` is 2.80859375 on P + 20 (the
action's speed), then 2.7109375 on P + 21, the take-off frame, where
`air-control.md` takes over with a target of 2.

## At a ledge without a face

Measured on `title-1` with the first character alone, at both ends of a
stretch of raised ground that ends flat (no face, no slope, no rounded
lip) 176 px above the floor below, as in `walk-off.md` ("Ledges without
a face"). The action was started from standing at every whole-pixel
position on the raised ground, moving toward either end, and with
inputs during and after it; the fall was made longer or shorter in some
runs by moving the player up or down after L, so that the landing came
from 30 frames before E to well after it. Things on the lower floor that
the action could strike were removed. Directions are written for an
action moving right; moving left is the mirror image. Moving left, only
the fall down to 157 px below the top was measured, not the landing.

**Leaving the ground.** The player goes over the end exactly as when
walking (`walk-off.md`, "When the player leaves"): nothing slows or
stops the action near the end. **L** is the first frame whose move ends
with the player's column past the end of the ground. On L the player
moves down 3.4375 and `vy` ends at 0.00390625, and from L + 1 the player
falls as in `walk-off.md` ("Falling": `vy` grows by 0.4375 a frame up to
8, landing as in `landing.md`).

**The action goes on in the air.** Leaving the ground doesn't end it.
Frames P to P + 58 are still frames of the action, grounded or not, and
on each of them `vx` follows the action's speed rule toward direction ×
3, as in "Speed and position". Left, Right, Up, Down and Action make no
difference in the air, as on the ground. E is still P + 59.

**Jump.** A Jump press starts a jump only if the player was grounded at
the end of the frame before it, as at any ledge without a face
(`walk-off.md`, "Jumping at a ledge without a face"). So a press on any
frame Q from P + 1 to L ends the action on Q as in "Inputs during the
action" (Q is not a frame of the action, and `vx` on Q still follows
the action's rule):

- Q before L − 1: an ordinary jump from the ground.
- Q = L − 1: the take-off (`jump_start`) is on L, with the move down on
  L given in `walk-off.md` for a press on L − 1.
- Q = L: L is the leave frame, as without a press but moving down
  3 + g(L); the take-off is on L + 1.

From the take-off on, the jump follows `jump-arc.md` and
`air-control.md`. A press on L + 1 or later, in the air, does nothing:
the action goes on, and the press is only remembered for the landing
(below).

**Landing during the action.** Call the landing frame **G** (the row
with `land`). If G is P + 58 or earlier, the player lands as in
`landing.md` and the action simply goes on on the ground: G and the
frames after it are frames of the action, `vx` follows its rule, and it
ends on E as in "How it ends". A Jump press on the ground, on G + 1 or
later, ends it as in "Inputs during the action".

**The action ending in the air.** If the player is still in the air on
E, `vx` on E is unchanged, as on the ground. From E + 1 the air rules
apply (`air-control.md`: target from the input of each frame), then the
landing as in `landing.md`.

**Jump held through the landing.** After either kind of landing, a Jump
press made from G − 10 to G + 1 and held through G + 1 starts a jump
whose take-off (`jump_start`) is on **G + 2**. It works as a press on
G + 1: if G + 1 is still a frame of the action (G ≤ P + 57), the action
ends on G + 1 and `vx` on G + 1 follows the action's rule; if G + 1 is
E, `vx` on it is unchanged; later, the normal rules apply. A press on
G − 11 or earlier gives no jump on landing, and a press on G + 2 or later
takes off on the frame after it, as on the ground. In the terms of
`jump-input.md` ("Pressing before landing"), these landings have
E = L + 2 (its E and L, the landing lockout and the landing frame, not
this file's).

**Down after the landing.** From E + 1 on, Down makes the player crouch
as in `ground-speed.md` ("Crouching"), except that on the landing frame
G and on G + 1 Down does nothing. The first crouch frame (its D) is the
first frame from E + 1 and from G + 2 on with Down held.

Example, direction +1 from standing 56 px before the end of the ground
(the first column without ground starts 56 px to the right of the
start), nothing held after the press (`x` and `y` relative to the
start; rows 6 to 28, 32 to 57 and 61 to 62 left out). Frame 29 ends
with `x` at 54.5546875, still over the ground; frame 30 ends at
57.44921875, past its end, so L is P + 25. The landing G is P + 55 and
E is P + 59.

```
frame	input	x	y	vx	vy	state	ground	facing	events
5	RDA	0.375	0	0.375	0	ground_action	1	R	
29	.	54.5546875	0	2.8828125	0	ground_action	1	R	
30	.	57.44921875	3.4375	2.89453125	0.00390625	ground_action	0	R	leave_ground
31	.	60.35546875	3.87890625	2.90625	0.44140625	ground_action	0	R	
58	.	140.74609375	158.3203125	3	8	ground_action	0	R	
59	.	143.74609375	166.3203125	3	8	ground_action	0	R	
60	.	146.74609375	176	3	0	ground_action	1	R	land
63	.	155.74609375	176	3	0	ground_action	1	R	
64	.	158.74609375	176	3	0	walk	1	R	
65	.	161.37109375	176	2.625	0	walk	1	R	
```

### Evidence (ledge without a face)

- 2 246 runs (2 more, which walked off before the press, were left out):
  every whole-pixel start position on the raised ground in
  both directions; starts while walking toward the end or away from it;
  621 runs with Left, Right, Action, Down, Up or Jump (pressed from L − 3
  to L + 6, held 1 or 20 frames or to the end) from around L on; 539
  runs with the fall made longer or shorter; 800 runs of random input
  after the press. On 134 555 frames `x`, `vx`, whether the action is in
  progress and `facing` during it, and on every frame up to 8 frames
  after the landing `y`, `vy` and `ground`, matched the rules above
  (2 095 leave frames, 53 623 frames of the action in the air, 1 391
  landings), with 0 differences.
- 1 664 runs with Jump pressed from 13 frames before to 2 frames after
  the landing and held, for 104 landings from 30 frames before E to
  8 frames after it: the last frame of the action, the take-off frame and `vx` on G + 1
  as above in every run.

## At a steep face

Measured on `title-1` with the first character alone, on flat ground
next to steep faces (`steep-faces.md`, "Columns, faces and steps"):

- going right, over a face of 8 columns falling to the right whose
  lowest column is above a gap, with flat floor 48 px below the top
  beyond the gap;
- going right, over a face of 32 columns falling to the right, with
  lower floor at the bottom and below it;
- going left, over a face of 4 columns falling to the left whose lowest
  column is above a gap;
- going right, toward a face rising to the right.

The action was started from standing at every whole-pixel position on
the flat ground before each face (every 1/16 px on the last 2 px, and
on the whole 16 px top before the long face), from walking toward the
face, and on frames that start on a face or move onto one, with inputs
during it and random input after the press. "Starts on a face" and
"face column" are as in `steep-faces.md`. Directions are written for an
action moving right; moving left is the mirror image, measured at one
face.

**Going down a face.** Nothing slows or stops the action at the top of a
face. Its grounded frames follow the face down or leave the ground
exactly as when walking (`steep-faces.md`, "Following a face down, or
leaving it": a new column 8 px lower or less is followed, 11 px lower
or no floor makes that frame the leave frame L, with the move down of
3.4375 and `vy` 0.00390625 of `walk-off.md`). The action goes on, on the
face, after L and through a landing, as at a ledge without a face
(above), and ends on E.

On every frame of the action that **starts on a face** (including L
when the frame before it ended on the face), the speed rule's target
is direction × 3 plus 1.5 downhill: **+4.5** (1152/256) for an action
moving down a face that falls to the right. The frame that moves onto
the face, and frames in the air, have the plain target direction × 3.
Left, Right, Up, Down and Action make no difference on face frames
either. So `vx` goes above 3 on the face and comes back toward 3 in
the air by the speed rule: in the example below it is 2.93359375 on the
frame that moves onto the face, 3.12890625 on L, then 3.11328125,
3.1015625, … in the air.

When `vx` is still above 3 on P + 58, the step on E ("How it ends")
changes it: for example 3.046875 on P + 58 and 3.04296875 on E, after a
landing on the floor below.

**Jump.** The press rules of `walk-off.md` ("Jumping near the edge")
apply, with one difference:

- A Jump press on a frame that starts on the face does nothing: no
  jump, the action goes on, and later frames fall with 0.4375 per frame
  as without the press.
- A press on any other frame Q of the action from P + 1 on (grounded,
  not on the face) ends the action on Q as in "Inputs during the
  action", and the take-off (`jump_start`) is on **Q + 1 even when
  Q + 1 starts on the face**, that is when Q is the frame that moves
  onto the face. (Walking, a press whose next frame starts on the face
  gives no jump.) On Q + 1 the player first moves as without the jump:
  following the face down (`y` changes by the drop to the new column)
  or leaving it (down 3 + g, `walk-off.md`), then `vy` becomes the
  take-off value. From Q + 1 the target is that of `air-control.md`
  from the input, with no part downhill.

**Pressing on the face or onto it.** If the press frame P starts on a
face, or its move ends on a face column, there is **no ground action**.
`vx` becomes 0 at the start of P, as when the action starts, and from
then on P and the frames after it go as without the press: on P the
target is that of a grounded frame with the inputs held there (Action
is held, so the running target, `ground-speed.md`), or the face target
of `steep-faces.md` ("Speed on a face") on a frame that starts on the
face. With Action held on face frames, that target was 1.5 downhill
plus the running target of 3 when the downhill direction was held
(4.5), and 1.5 downhill otherwise; Down held on face frames made no
difference. None of these frames are `ground_action`.

**Toward a rising face.** A frame of the action that would move the
player into a face column of a face rising in the direction of the
move is blocked as in `steep-faces.md` ("Stopped by a rise"): `x`
becomes the left edge of the column the player was in, and `vx` 0. The
action still lasts to E, but the player doesn't move again: on every
frame of the action (and on E) that starts with `vx` 0, the rule of
`steep-faces.md` ("Can't start toward a rise") applies with the
action's direction in place of a held direction, so with the column
5 ahead 8 px or more higher, `vx` stays 0 and `x` doesn't change. The
same holds on P when the player was standing (`vx` 0 on P − 1): the
action starts but the player doesn't move at all. From E + 1 the
normal rules apply, including "Can't start toward a rise" for a held
direction.

If instead `vx` was not 0 on P − 1 and the column 5 ahead of the
player's column on P is 8 px or more higher, the speed on P becomes 0
and then moves toward direction × **2** (512/256) instead of × 3:
`vx` is 0.25, 0.46875, 0.66015625, 0.82421875, … on P, P + 1, … until
the player is blocked, and 0 from then to E. Measured going right only.

Example, direction +1 from standing 68 px before the first column of a
face that falls to the right (8 columns, then the gap), nothing held
after the press (`x` and `y` relative to the start; rows 7 to 32, 37 to
45, 49 and 52 to 54 left out). The player moves onto the face on frame
34, L is P + 30 and the landing G on the floor beyond the gap is
P + 42:

```
frame	input	x	y	vx	vy	state	ground	facing	events
4	.	0	0	0	0	idle	1	R	
5	RDA	0.375	0	0.375	0	ground_action	1	R	
6	RDA	1.078125	0	0.703125	0	ground_action	1	R	
33	.	66.19921875	0	2.92578125	0	ground_action	1	R	
34	.	69.1328125	7	2.93359375	0	ground_action	1	R	
35	.	72.26171875	10.4375	3.12890625	0.00390625	ground_action	0	R	leave_ground
36	.	75.375	10.87890625	3.11328125	0.44140625	ground_action	0	R	
46	.	106.04296875	39.35546875	3.04296875	4.81640625	ground_action	0	R	
47	.	109.08203125	48	3.0390625	0	ground_action	1	R	land
48	.	112.1171875	48	3.03515625	0	ground_action	1	R	
50	.	118.17578125	48	3.02734375	0	ground_action	1	R	
51	.	121.17578125	48	3	0	ground_action	1	R	
55	.	133.17578125	48	3	0	ground_action	1	R	
```

### Evidence (steep faces)

- 4 844 runs (4 743 checked; in 99 the press came after walking off the
  face or not at all, and 2 pressed just after a landing on a face were
  left out): every start position as above toward each face, starts
  while walking toward the face (press 1 to 7 frames after starting to
  walk), presses on frames that start on the face or move onto it while
  walking down it, 830 runs with Left, Right, Action, Down or Up held or
  tapped from the frame before the player moves onto the face, 1 136
  runs with Jump pressed from 3 frames before to 5 frames after that
  frame (held 1 or 20 frames), 156 runs walking into the rising face
  with the press 1 to 7 frames after starting, and 1 200 runs of random
  input after the press. On 278 234 frames `x`, `vx`, whether the action
  is in progress and `facing` during it, and on every frame up to
  8 frames after the landing `y`, `vy` and `ground`, matched the rules
  above with 0 differences: 4 243 frames of the action starting on a
  face (2 354 of them leave frames), 839 blocked frames and 34 773
  frames held still by "Can't start toward a rise", 1 914 landings
  during the action, 209 Jump presses on face frames, 754 take-offs
  (174 of them following the face down on Q + 1), 56 presses on or
  onto a face, and 21 moving and 85 standing presses next to the rise.
- Without the 1.5 downhill on face frames, 2 425 frames differ; without
  the step on E, 85; without "Can't start toward a rise" during the
  action, 767.

## On a slope

On gentle slopes that rise or fall 1 px for every 4 px across
(`slopes.md`), the ground action is the same as on flat floor, uphill
and downhill, in either direction: which presses start it, the
direction, the duration, the speed on every frame from P to E, the step
on E, the inputs during the action and the normal grounded rules from
E + 1 are all as in the sections above. The slope makes no difference to
`x`, `vx` or `facing` on any frame:

- **Speed**: the action is no faster downhill and no slower uphill. It
  reaches 3 px per frame on P + 39 as on flat floor and stays there,
  also across the frames where the floor goes from flat to sloped, from
  falling to rising, or back to flat.
- **Direction 0**: a player that presses on a slope with neither or both
  of Left and Right held stays where it is, with `vx` 0, to E. It
  doesn't slide.
- **Height**: on every frame of the action, and on E, `y` is the floor
  height of the column containing `x` after the frame's move, as in
  `slopes.md` "Grounded on a slope": a whole number, `vy` 0 and `ground`
  1. Following a 1-in-4 slope down at the action's top speed of 3 px per
  frame never made the player airborne.
- **Jump**: a Jump press on Q during the action ends it as on flat floor
  ("Inputs during the action"), with the same speed on Q and on the
  take-off frame Q + 1. The jump itself follows `slopes.md` "Jumping
  from a slope".

**Example.** A synthetic dip: columns up to 99 at `y` = 0, then going
down 1 px every 4 columns to the right (columns 100 to 103 at `y` = 1,
104 to 107 at `y` = 2, and so on to 156 to 159 at `y` = 15), columns 160
to 191 at `y` = 16, then going up 1 px every 4 columns (192 to 195 at
`y` = 15, and so on to 248 to 251 at `y` = 1), and columns from 252 at
`y` = 0. Standing still facing right at `x` = 74.26953125, `y` = 0,
Down, Action and Right pressed together on frame 0 = P, and Right held
alone from frame 1 on. Every row has `ground` 1 and `vy` 0; frames 0 to
58 are the action and frame 59 is E. `vx` on every frame is the flat
floor's (the table in "Speed and position").

| Frame | `x` | `y` | `vx` (1/256) |
|---|---|---|---|
| 0 = P | 74.64453125 | 0 | 96 |
| 13 | 98.42578125 | 0 | 647 |
| 14 | 101.01171875 | 1 | 662 |
| 15 | 103.6484375 | 1 | 675 |
| 16 | 106.328125 | 2 | 686 |
| 17 | 109.046875 | 3 | 696 |
| 35 | 161.109375 | 16 | 758 |
| 39 = P + 39 | 173.015625 | 16 | 768 |
| 45 | 191.015625 | 16 | 768 |
| 46 | 194.015625 | 15 | 768 |
| 47 | 197.015625 | 14 | 768 |
| 58 = P + 58 | 230.015625 | 6 | 768 |
| 59 = E | 233.015625 | 5 | 768 |
| 60 | 235.890625 | 5 | 736 |
| 66 | 251.296875 | 1 | 615 |
| 67 | 253.65234375 | 0 | 603 |

On frame 17 the move of 2.71875 px crosses from column 106 to column
109, so `y` goes from 2 to 3 in one frame although column 106 is at
`y` = 2 and column 109 at `y` = 3 (there is no in-between frame for
columns 107 and 108). The same inputs from standing still on flat floor
give the same `x` − (`x` on P − 1) and the same `vx` on every frame.

### Evidence (slopes)

Measured on `title-1` with the first character alone, by playing the
same input sequences from standing still at points on flat floor and at
16 points on and beside a 1-in-4 dip (down, a flat bottom, up), and
comparing every run frame by frame with the same inputs from flat
floor, with no enemies or objects in reach (any that were, were removed
for these runs). The inputs were presses from
standing (with Right, Left, neither or both held on P for 1 or 2
frames, and from a crouch), while walking (direction held, released or
reversed on P) and while running after a roll; Left, Right, Down, Up,
Action and nothing held during the action; Jump pressed on every fourth
frame from P + 1 to P + 57 and on E and E + 1, held 1 or 12 frames; a
second press with Down, or a roll, 1 to 6 frames after E; turns from E
on; and 80 random sequences of Left, Right, Down, Action and Jump.

- 2 488 slope runs, 176 051 frames up to and including the first airborne frame, 68 101
  of them on slope columns: `x` relative to the starting point, `vx`,
  `ground`, `facing` and whether the action is in progress matched the
  flat-floor run on every frame, and `y` was the column's floor height on
  every grounded frame, with 0 differences. 1 115 of the runs took off
  after a Jump press; they were compared up to and including the
  take-off frame.
- They include 34 947 frames of the action on slope columns (10 948
  moving downhill, 11 755 moving uphill, 12 244 still), 857 presses on
  slope columns and 590 E frames on slope columns. The fastest `vx`
  during the action on a slope column was 3 px per frame.
- 214 runs from different flat points gave identical runs as well.
  Taking `y` from the column before the frame's move instead would be
  wrong on 28 297 of 173 631 grounded frames of the action.

## Enemies

The action knocks out walkers and the thrower, with any direction
including 0, on three of its frames; the player can be hurt on the
others. Each knock-out pauses the action for two frames, on which `x`
and `vx` stay as they were, and so makes it 2 frames longer:
E = P + 59 + 2 × (number of pauses). Which frames, the offsets, the
pause, the hurt rule during the action and what happens to the enemy
are in `ground-action-enemies.md`.

## Trace rows

Frames P to P + 58 have state `ground_action` (`../traces/VOCABULARY.md`),
`ground` 1 and `vy` 0 while on the ground (2 more frames for each pause
after a knock-out, `ground-action-enemies.md`). Frames of the action in
the air, after going over a ledge, are `ground_action` too, with
`ground` 0 and the fall's `vy`; the leave frame carries `leave_ground`
and the landing frame `land` (see "At a ledge without a face"). On a
1-in-4 slope the rows are the same, with `y` following the floor ("On a
slope") and `vy` 0. There is no event on P. E, and a Jump
press frame Q ending the action early, are not `ground_action`: they map by the
normal grounded rules (`idle` if `vx` is 0, else `walk` or `run` by
whether Action is held). Example rows, walking right with Right held,
Down and Action pressed on frame 30 and held to 33 (rows 32 to 87
left out):

```
frame	input	x	y	vx	vy	state	ground	facing	events
29	R	45.99609375	0	1.94921875	0	walk	1	R	
30	RDA	46.37109375	0	0.375	0	ground_action	1	R	
31	RDA	47.07421875	0	0.703125	0	ground_action	1	R	
88	R	201.7421875	0	3	0	ground_action	1	R	
89	R	204.7421875	0	3	0	walk	1	R	
90	R	207.6171875	0	2.875	0	walk	1	R	
```

## Not covered

- Ledges with slopes or a rounded edge at the top, steps up or down,
  walls, and slopes other than 1 in 4 or where a slope meets a ledge,
  wall or face. Steep faces other than those in
  "At a steep face": an action moving up a face (after a landing on one,
  or started on one going uphill), E on a face frame, and moving starts
  next to a rise going left. Landing on anything other than flat floor
  after going over a ledge or a face (landings on a face were seen and
  not checked), and the second character.
- Jump held through the landing after a fall from a face during the
  action, and an uphill direction held on the frame after a take-off
  that ends on a face column (in the one random run seen, the target on
  that frame was 0).
- Enemies and other objects met in the air during the action, and the
  action's poses in the air.
- Exits: in the 4 runs that reached the first level's left end, the
  exit there took over (`exits.md`, "The walk-out") and the action ended
  on the walk-out's second frame. Exits weren't measured otherwise.
- Enemies and hits taken during the action are in
  `ground-action-enemies.md`, with their own "Not covered" list.
- An Action press with Down held during a roll or the stop after a roll.
- Jump, Down and Action all pressed on the same frame from standing: a
  jump starts, but later than a plain jump press. The timing isn't
  specified.
- When a turn that the action interrupts resumes, and turns started on
  E + 1 or later while moving.
- Swap presses from E + 1 on while Down is still held from the action.
- The action while carrying an object, and with the other character
  present (how it follows).
