# Roll speed

Titles: `title-1`

Horizontal speed and facing during a roll on flat ground, and as the roll
ends. Frames are logic frames at 60 Hz, numbered as in a trace. `x`, `vx`
and `facing` are as in a trace: right is positive, `vx` in px per frame.
"Left", "Right", "Up", "Action" are the neutral inputs `L`, `R`, `U`, `A`
of the scenario format. Measured on flat ground with the lead character.

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
next roll fast. Frames during the stop after a roll weren't measured.

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

**A roll started by a remembered press** (the 30-frame roll in
`roll-input.md`, starting on D + 17 of the previous roll): on its first
frame the speed stays 0 and the player doesn't move. From the next frame
everything in this spec applies with that frame as the press frame.
Measured with a direction held throughout. A roll started by a new press
on D + 17 behaves the same way on its first frame; one started on D + 18
or later moves on its press frame as usual.

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

## Not covered

- Rolls off ledges, into walls or enemies other than walkers, and in
  the air. (On 1-in-4 slopes normal and fast rolls are as on flat floor;
  see `slopes.md`. Rolls into walkers are in `rolling-into-walkers.md`.)
- Steering on D + 1 to D + 3 when the roll carries on into running
  (measured only with the input unchanged).
- Running frames during the stop after a roll.
- The other character.
