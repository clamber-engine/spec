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
below. The other 4 reached a ledge (see "Not covered").

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
- **During a roll or the stop after it**: Down makes no difference from
  the frame after the roll starts to D + 17 of a roll that ends in a stop,
  or to D + 3 of one that carries on into running (`roll-input.md`,
  "Down during a roll"). A press with Down held then is remembered or
  ignored as without Down, and never starts the ground action. The first
  frame on which a press with Down held starts it is D + 18 after a stop
  and D + 4 after a roll that carried on.
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
`ground` 1 and `vy` 0 (2 more frames for each pause after a knock-out,
`ground-action-enemies.md`). There is no event on P. E, and a Jump
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

- Ledges: the player was seen to keep moving off the edge of a floor,
  the action ending on reaching it. Steps up or down, walls, and slopes
  during the action.
- Enemies and hits taken during the action are in
  `ground-action-enemies.md`, with their own "Not covered" list.
- Jump, Down and Action all pressed on the same frame from standing: a
  jump starts, but later than a plain jump press. The timing isn't
  specified.
- When a turn that the action interrupts resumes, and turns started on
  E + 1 or later while moving.
- Swap presses from E + 1 on while Down is still held from the action.
- The action while carrying an object, and with the other character
  present (how it follows).
