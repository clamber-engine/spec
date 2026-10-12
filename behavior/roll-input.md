# Roll input

When an Action press starts a roll, and when it doesn't. Frames are logic
frames at 60 Hz, numbered as in a trace. "Action" and "Down" are the
neutral inputs `A` and `D` of the scenario format. Measured on flat ground
with the lead character.

A **press** of Action is a frame where Action is held after a frame where
it was not held. Holding Action never starts a second roll by itself; every
roll needs its own press. Running is Action *held* while moving; a press
always rolls first (see "Rolling into a run").

## On the ground

A press on frame P while grounded starts a roll on frame P (`roll_start`
on P), unless Down is held on frame P, or the player is standing within
reach of an object that can be carried (then it is picked up instead; see
`carrying.md`).

This does not depend on:

- speed: standing still, any point while speeding up to walking speed,
  walking at full speed, sliding to a stop after releasing the direction,
  or running (Action released for one frame and pressed again);
- direction: nothing, Left, Right, Up, or the direction opposite to the
  current motion, held or pressed on the same frame;
- how long Action is held after the press (one frame is enough).

Down held on the press frame (pressed together with Action, or held
earlier) means **no roll**. It starts a different ground action that this
spec doesn't define yet: the player stops (horizontal speed 0 from the
next frame, even from a run) and doesn't move for 59 frames. Down pressed
after a roll has started has no effect on the roll.

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
change with airtime.

## How a roll ends

A roll lasts 29 frames, S to S + 28, where S is the `roll_start` frame
(one exception below). What happens next depends on frame **D** = S + 29:

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
- D + 3 to D + 16: speed is 0, whatever direction is held (on D + 3
  the player still moves; see `roll-speed.md`);
- from D + 17: normal movement.

The stop is not part of the roll: while the speed is not 0 the trace
state is `walk` (or `run` if Action is held), then `idle`. A jump can start during the
stop as usual (`behavior/jump-input.md`).

## Starting the next roll

After a roll that carried on into running (Action held through D): a new
press on P ≥ D + 2 starts a roll on max(P, D + 4), i.e. no earlier than
the frame after `roll_end`.

After a roll that ended in a stop: a press on P with D − 2 ≤ P ≤ D + 16
is remembered, even if Action is held for one frame only, and the next
roll starts on D + 17. A press during the roll before D − 2 is ignored. A
press on D + 17 or later starts a roll on that frame.

**Exception:** a roll started on D + 17 from a remembered press lasts 30
frames instead of 29 (S to S + 29, so D = S + 30). Everything in this
section and the previous one holds with that D.

## Rolling into a run

Pressing Action while walking and keeping it held gives a roll followed by
running. Scenario `run-jump-flat` presses Action on frame 20 while walking
and holds it: frames 20 to 51 are `roll` (`roll_start` 20, `roll_end` 52),
then `run`. Speed keeps rising toward running speed throughout the roll.

## Worked example

Standing still, Action is pressed on frame 10 for one frame. The roll is
frames 10 to 38 (D = 39), `roll_end` on 39. The player slows on 40 and 41,
stands still from 42 to 55 (`idle`), and moves normally again from 56. A second one-frame press
on frame 37 starts the next roll on frame 56. A press on frame 36 would be
ignored.

## Not covered yet

Roll speed and steering are in `roll-speed.md`. Rolls on slopes, rolls
into enemies, the other character, and the Down + Action ground action.
