# Roll speed

Titles: `title-1`, `title-2`

Horizontal speed and facing during a roll on flat ground, and as the roll
ends. Frames are logic frames at 60 Hz, numbered as in a trace. `x`, `vx`
and `facing` are as in a trace: right is positive, `vx` in px per frame.
"Left", "Right", "Up", "Action" are the neutral inputs `L`, `R`, `U`, `A`
of the scenario format. Measured on flat ground with the lead character.

The sections below up to "In `title-2`" give the `title-1` values.
`title-2` is different throughout; "In `title-2`" gives its complete
values.

When a roll starts and how long it lasts are in `roll-input.md`. This spec
uses its names: S is the `roll_start` frame (the frame of the Action
press), and D = S + 29 is the frame after the roll's 29 frames.

Speeds are always whole multiples of 1/256 px per frame. The rule below
is exact in those units, so this spec writes speeds both ways
(768/256 = 3 px per frame).

## The speed rule during a roll

On every frame from S to D, the speed moves toward a **roll target** by
the speed rule of `ground-speed.md` (step = gap ÷ 8 rounded toward zero;
if the step is 0, the speed becomes the target), and the player moves by
the new speed. What is held doesn't change the target the way it does on
the ground: the roll target has a **direction** and a **size**, set as
below.

Near the end of the ground, a slow-moving roll can stop instead
(`rolling-off-ledges.md`, "Stopping before a ledge").

The roll target is direction × size, where the size is one of:

| Size | Name |
|---|---|
| 1 (256/256) | slow roll |
| 3 (768/256) | normal roll |
| 4 (1024/256) | fast roll |

## Size and direction on the press frame

The direction on S is the player's facing on S, whatever is held. Holding
the opposite direction on S doesn't reverse the roll on S (see below for
S + 1).

The size on S:

1. **Fast** if the player was **running** recently (next section).
2. Otherwise **normal** if Left or Right is held on S (either one, even
   the direction opposite to facing).
3. Otherwise **slow**.

The size doesn't depend on the speed on S: a press while standing still
with Right held gives a normal roll, and a press while sliding at almost
running speed with no direction held gives a slow one, unless it is fast.

### Running recently

A **running frame** is a frame where Action and exactly one of Left or
Right are held. It counts on the ground and in the air. The frames of a
roll, from `roll_start` up to and including its `roll_end` frame, never
count, so holding Action and a direction during a roll doesn't make the
next roll fast. Frames of the stop after a roll that ends in a stop,
up to D + 16, don't count either: Action and a direction held there
don't make a roll started on D + 17 fast. Frames of the stop from D + 18
on weren't measured.

The roll is fast if there is a running frame on or after:

- S − 15, when Left or Right is held on S;
- S − 14, when neither is held on S.

Frames between the last running frame and S may hold anything else (a
direction alone, Action alone, nothing). The speed when pressing doesn't
matter: the player may have slowed almost to a stop.

**Exception (measured):** if the only running frame is a single one that
starts from standing still (`vx` 0 on the frame before it), and neither
Left nor Right is held on any frame after it up to and including S, the
roll is slow, at every gap measured (1 to 14 frames). With Left or Right
held on any of those frames, or two or more running frames in a row, the
rule above applies.

Examples, running right at full speed with Action and Right held, then:

| Then held for K frames | Then on S | Fast for | Otherwise |
|---|---|---|---|
| Right only | Action and Right | K ≤ 14 | normal |
| Nothing | Action and Right | K ≤ 14 | normal |
| Nothing, or Action only | Action only | K ≤ 13 | slow |

In the last row Action is not held on the last of the K frames, so that
S is a press.

## Steering during the roll

On each frame from S + 1 to D where exactly one of Left or Right is held:

- **Same as the roll's direction:** a slow roll becomes a normal roll on
  that frame and stays normal for the rest of the roll, even if the
  direction is released. Normal and fast rolls don't change.
- **Opposite to the roll's direction:** the roll's direction and the
  player's facing reverse on that frame (the trace's `turn` event goes on
  that frame; there is no turn delay as on the ground). The size doesn't
  change on that frame. If the new direction is still held on the next
  frame, it counts as "same as the roll's direction" from then on.

Holding both Left and Right, Up, or Action (with or without a direction)
has no effect during the roll. A roll never becomes fast after S.

## When the roll ends

Two endings, as in `roll-input.md`.

**The roll ends in a stop:**

- D: still a roll frame for speed (the speed rule above, with steering).
- D + 1 and D + 2: the target is 0, whatever is held; the speed rule
  applies.
- D + 3: the player moves by one more step of the speed rule toward 0,
  but `vx` is 0 at the end of the frame. In a trace, `x` changes on this
  row although `vx` is 0.
  A Jump press on this frame is the exception: `vx` is then not 0
  (`roll-input.md`, "A Jump press during the stop").
  Next to an edge, a slow stop can end earlier
  (`rolling-off-ledges.md`, "The stop after a roll, next to an edge").
- D + 4 to D + 17: `vx` is 0 and the player doesn't move, whatever direction
  or Action is held.
- D + 18: the speed rule of `ground-speed.md` applies again, starting
  from 0.

**The roll carries on into running:** D to D + 3 (the `roll_end` frame)
all follow the roll's speed rule above. From D + 4 the ground speed rule
of `ground-speed.md` applies. For example, holding only Action through a
slow roll, `vx` is 1 on D + 3 and then slows under the ground rule from
D + 4.

**A fast roll always ends in a stop**, even when Action is held on every
frame from S to D, unless it defeats a walker (`rolling-into-walkers.md`).

**A roll started on D + 17** (the 30-frame roll in `roll-input.md`,
from a press remembered in the stop or a new press on D + 17): D + 17 is
its press frame. Its size and direction are set on D + 17 by what is
held then, as on any press frame, whether the press was remembered or
new; on that frame the speed stays 0 and the player doesn't move. From
D + 18 everything in this spec applies as on the frames after any press
frame, steering included. For example, facing right with Left held on
D + 17 only, it goes left as a normal roll from D + 18; with Right held
on D + 17 and Left from D + 18, it is a normal roll going right that
turns left on D + 18; with nothing held on D + 17 and Right on D + 18
only, it is a slow roll made normal on D + 18. One started on D + 18 or
later moves on its press frame as usual.

## Jumping out of a roll

A jump press on a frame P of a roll (any frame from S to the last `roll`
frame, on the ground or in the air) starts a jump on T = P + 1
(`jump-input.md`; in the air, `rolling-off-ledges.md`) and ends the
roll:

- **P** is still a roll frame for speed: the speed rule above applies
  with the roll's target, and steering as above.
- From **T** on, the roll is over. On T and on every frame after it the
  target is the one of `air-control.md` (and of `ground-speed.md` once
  the player has landed), set by what is held on that frame: ±2, ±3 with
  Action, 0 with no direction. The speed rule is the same, starting from
  the roll's speed on P. Nothing keeps the roll's speed: a fast roll's
  speed falls toward 3 (Action held) or 2 by an eighth of the gap per
  frame, and a slow roll's rises toward the new target.
- No stop follows and the roll doesn't carry on into running: the
  trace's `roll_end` is on P (the state on P is no longer `roll`), and
  after landing the player walks, runs or stands under the normal rules.

Measured on flat ground from fast, normal and slow rolls with presses
from S + 1 to S + 28, holding Right, Right and Action, nothing, or Left
(which reverses the roll first) after S, and from normal rolls off three
ledges (`rolling-off-ledges.md`). Example: a fast roll going right with
`vx` 3.6953125 (946/256) on P − 1 and Right alone held from P: `vx` is
3.73046875 (955/256) on P (roll rule toward 4), then 3.515625
(900/256) on T and 3.328125 (852/256) on T + 1 (toward 2).

## Worked values

Frame S is the press; distances are from the position at the start of S.

| Before S | Held on S, then | Size | `vx` on S, S + 1, S + 2 | `vx` on D | Distance S to D | Distance S to D + 3 (stop) |
|---|---|---|---|---|---|---|
| Standing, facing right | Action; nothing | slow | 0.125, 0.234375, 0.328125 | 0.96875 | 22.78125 | 25.0234375 |
| Standing, facing right | Action and Right; Right | normal | 0.375, 0.703125, 0.98828125 | 2.93359375 | 69.1328125 | 75.921875 |
| Walking right at +2 | Action and Right; Right | normal | 2.125, 2.234375, 2.328125 | 2.96875 | 82.78125 | 89.64453125 |
| Running right; Action released one frame (`vx` 2.875) | Action and Right; Right | fast | 3.015625, 3.13671875, 3.2421875 | 3.96484375 | 111.94140625 | 121.11328125 |

Reversing: walking right at +2, Action and Right on S, then Left held.
On S the target is still +3 (`vx` 2.125, facing right); on S + 1 facing
becomes left and the target −3 (`vx` 1.484375, then 0.92578125 on S + 2).
`vx` is first negative on S + 5 and is −2.8828125 on D; the player ends
49.47265625 px left of the start of S on D.

Steering a slow roll: standing facing right, Action alone on S, Right
from S + 10. `vx` follows the slow target (0.125, 0.234375, …, 0.7265625
on S + 9) and from S + 10 the normal one (1.0078125 on S + 10).

## In `title-2`

Everything in this section was measured on `title-2` only, with the lead
character at the start of a game, on flat ground. Speeds are whole
multiples of 1/256 px per frame, written both ways. "Net direction" means
Right or Left held alone; both together, or neither, is no direction.
When a roll starts, how long it lasts and how it ends are in
`roll-input.md` ("In `title-2`"); this section uses its names: **S** is
the first frame of the roll and **D** = S + 31 is the frame after its 31
frames.

### The speed rule during a roll (`title-2`)

On every frame from S to S + 30, the speed moves toward a **roll target**
by the speed rule of `ground-speed.md` (step = gap ÷ 8 rounded toward
zero; if the step is 0, the speed becomes the target), and the player
moves by the new speed. The roll target is direction × size:

| Size | Name |
|---|---|
| 1.125 (288/256) | slow roll |
| 3.375 (864/256) | normal roll |
| 4.5 (1152/256) | fast roll |

The direction on S is the player's facing before S, whatever is held on
S.

### Size (`title-2`)

The size is set on S and never changes during the roll.

1. With **no net direction** held on S (nothing, Up, or both Left and
   Right), the roll is **slow**, always, even right after running.
2. With a net direction held on S (either one, even the one opposite to
   facing), the roll is **fast** if Action's last hold was long and
   recent (next paragraph), and **normal** otherwise.

**Long and recent hold.** Take the press of Action that starts the roll
(it is on S, or earlier for a remembered press; see `roll-input.md`).
Before that press, Action was last held on frames P to Q, and not held on
Q + 1. The roll is fast if both:

- the hold lasted at least 32 frames: Q − P + 1 ≥ 32, and
- it ended no more than 19 frames before S: S − (Q + 1) ≤ 19, that is,
  Q ≥ S − 20.

Only that last hold counts. A shorter hold after a long one (for example
Action tapped in the air, where it starts nothing) means the roll is not
fast. Where the hold happened doesn't matter: on the ground, in the air,
during a roll or during the stop after one. Nothing else matters either:
not the speed, not whether a direction was held during the hold, not
what was held between Q and S.

The usual way to get a fast roll is to hold Action through a roll that
carries on into running (`roll-input.md`), keep running, release Action
and press it again with a direction held within 19 frames.

Examples, with S − (Q + 1) = the number of frames without Action before
the press:

| Action held for | Then without Action for | Direction on S | Size |
|---|---|---|---|
| 66 frames (roll, then running) | 1 to 19 frames | Right | fast |
| 66 frames | 20 frames or more | Right | normal |
| 66 frames | 1 frame | none | slow |
| 32 frames | 1 frame | Right | fast |
| 31 frames | 1 frame | Right | normal |
| 31 frames (the frames of a roll that then stops) | 15 frames (the stop) | Right | normal |

### Steering during the roll (`title-2`)

On each frame from S + 1 to S + 30 (and on D, below) where a net
direction is held:

- **Opposite to the roll's direction:** the roll's direction and the
  player's facing reverse on that frame, and the target reverses on that
  same frame. There is no turn delay. The size doesn't change.
- **Same as the roll's direction:** nothing changes. A slow roll stays
  slow (in `title-1` it would become normal).

Holding both Left and Right, Up, Down or Action has no effect during the
roll. The direction can reverse any number of times.

### When the roll ends (`title-2`)

**The roll carries on into running** (`roll-input.md`): D still follows
the roll's speed rule and steering above. From D + 1 the ground rules of
`ground-speed.md` ("In `title-2`") apply, with their usual target from
the previous frame's input, and a turn starts on D + 1 if the direction
opposite to facing is held. Example: Action alone held through a slow
roll gives `vx` 1.09765625 (281/256) on D, then 0.9609375 (246/256) on
D + 1 (toward 0, since no direction is held).

**The roll ends in a stop:**

- D: the player moves by one more step of the roll's speed rule, but `vx`
  is 0 at the end of the frame (`x` changes on this frame although `vx` is
  0). Facing still reverses on D if the opposite direction is held.
- D + 1 to D + 14: `vx` is 0, the player doesn't move, and facing doesn't
  change, whatever is held.
- From D + 15: the ground rules of `ground-speed.md` ("In `title-2`"),
  starting from 0. A direction held on D + 15 gives the first step of the
  ground rule; the direction opposite to facing starts a turn on D + 15.

A fast roll always ends in a stop, even when Action is held on every
frame from S to D.

### Jumping out of a roll (`title-2`)

A Jump press on a frame G from S + 1 to D starts a jump (`jump-input.md`,
"In `title-2`"; take-off on T = G + 2) and ends the roll. This includes a
press on D of a roll that would otherwise stop: the stop doesn't happen.

- **G, G + 1 and T** follow the roll's speed rule with the roll's size,
  and steering as above (a net direction opposite to the roll reverses it
  and the player's facing on any of these frames).
- From **T + 1** the air rules of `air-control.md` ("In `title-2`")
  apply. Nothing keeps the roll's speed: a fast roll's speed falls toward
  3.375 or 2.25 by an eighth of the gap per frame.

Example: from standing, a normal roll with Right held, and Jump and Right
pressed on S + 10: `vx` is 2.58203125 (661/256) on G, 2.765625 (708/256)
on T (the roll target 3.375 still applies), and 2.703125 (692/256) on
T + 1 (air target 2.25).

A Jump press during the stop (D + 1 to D + 14) starts a jump as usual;
`vx` stays 0 on G, G + 1 and T, and from T + 1 the air rules apply.

### Worked values (`title-2`)

Frame S is the first frame of the roll; distances are from the position
at the start of S. Every roll below ends in a stop.

| Before S | Held on S, then | Size | `vx` on S, S + 1, S + 2 | `vx` on S + 30 | Distance S to S + 30 | Distance S to D |
|---|---|---|---|---|---|---|
| Standing, facing right | Action; nothing | slow | 0.140625, 0.26171875, 0.3671875 | 1.09375 | 26.78515625 | 27.8828125 |
| Standing, facing right | Action and Right; Right | normal | 0.421875, 0.7890625, 1.109375 | 3.30859375 | 81.02734375 | 84.34375 |
| Walking right at 2.25 | Action and Right; Right | normal | 2.390625, 2.51171875, 2.6171875 | 3.34375 | 96.53515625 | 99.8828125 |
| Running right after a long Action hold (see below), then Right alone for one frame (`vx` 3.15625) | Action and Right; Right | fast | 3.32421875, 3.46875, 3.59765625 | 4.46484375 | 129.953125 | 134.421875 |

The fast row starts from standing, facing right, with frame 0 the first
frame of input. Action alone is held on frames 0 to 45: a slow roll on
frames 0 to 30 that carries on (D = 31; Action held throughout, see
"When the roll ends"), then the ground rules with no direction, so `vx` falls toward 0
(0.1796875, 46/256, on frame 45). Action and Right are held on frames 46
to 65: the target is 2.25 on frame 46 (the first frame with a direction)
and 3.375 from frame 47, so `vx` is 0.4375 on frame 46 and
3.12890625 (801/256) on frame 65, still short of 3.375. Right alone is
held on frame 66: the target is still 3.375 (Action and a direction were
held on frame 65), so `vx` becomes 3.15625 (808/256). S is frame 67.
Action's last hold before the press on S is frames P = 0 to Q = 65
(66 frames) and S − (Q + 1) = 1, so the roll is fast (see "Size").

Holding Action and Right from frame 0 instead starts a normal roll on
frames 0 to 30 that carries on into running; `vx` is 3.375 from frame 40,
stays 3.375 on frame 66 (Right alone), and the fast roll from S = 67 has
`vx` 3.515625, 3.63671875, 3.7421875 on S, S + 1, S + 2.

Reversing: walking right at 2.25, Action and Right on S, then Left held.
On S the target is +3.375 (`vx` 2.390625, facing right); on S + 1 facing
becomes left and the target −3.375 (`vx` 1.671875, then 1.04296875 on
S + 2). `vx` is first negative on S + 5 and is −3.2578125 on S + 30; the
player ends 62.16796875 px left of the start of S on D.

Holding Right from S + 10 in a slow roll (Action alone on S) changes
nothing: the speeds are those of the first row.

### Evidence (`title-2`)

1,217 runs by inputs: scripted cases (sizes, the fast window, steering on
every part of the roll, endings, presses during the roll and the stop,
presses in the air, jumps out of rolls and out of the stop) and 550 runs
of random input (directions, Action and Jump held or released at random,
in three independent sets of 150, 200 and 200). All 2,143 rolls (1,292
slow, 620 normal, 231 fast), 73,102 checked frames, 559 stops, 563 rolls
carrying on into running, 413 jumps out of a roll, and 997 first frames
after a roll match this section and `roll-input.md` ("In `title-2`") with
0 mismatches. The third random set (200 runs, 452 rolls) was recorded
after the rules were fixed on the others; its one mismatch was a Jump
press on the same frame a remembered Action press would start a roll,
which is now in `roll-input.md`. Frames touching a wall and runs with
Down held were left out.

### Not covered in `title-2`

- Rolls into walls other than steep faces, and into enemies. Rolls off
  ledges are in `rolling-off-ledges.md` ("In `title-2`"), rolls on slopes
  in `slopes.md` ("In `title-2`"), rolls blocked by a steep face in
  `steep-faces.md` ("In `title-2`").
- Down held on the press frame (no roll starts; the player crouches) is
  seen but its details aren't covered.
- The second character.

## Not covered

This list is for `title-1`; `title-2` has its own list above.

- Rolls into walls other than steep faces, and enemies other than
  walkers. Rolls on, onto and into steep faces are in `steep-faces.md`
  ("Rolling on, onto or into a face"). Rolls off ledges, and so in the
  air, are in `rolling-off-ledges.md`. (On 1-in-4 slopes normal and fast rolls are as on flat floor;
  see `slopes.md`. Rolls into walkers are in `rolling-into-walkers.md`.)
- Steering on D + 1 to D + 3 when the roll carries on into running
  (measured only with the input unchanged).
- Running frames during the stop after a roll.
- The second character's rolls are in `second-character.md` ("Rolls").
