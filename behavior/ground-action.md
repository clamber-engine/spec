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

On **E**, the player is no longer in the action, but its speed doesn't
change: `vx` on E is `vx` on P + 58, and the player moves by it. What is
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
and the landing frame `land` (see "At a ledge without a face"). There is no event on P. E, and a Jump
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

- Ledges with a steep face, slopes or a rounded edge at the top
  (`steep-faces.md`), steps up or down, walls, and slopes during the
  action. Landing on anything other than flat floor after going over a
  ledge, and the second character.
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
