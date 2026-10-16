# Second character: movement

Titles: `title-1`

How the **second character** (the one that joins later; see
`losing-a-character.md`) moves on the ground and in the air when it is
the character under control. It follows the same rules as the first
character, with its own values. This file lists what differs and gives
the second character's numbers; the rules themselves are in
`ground-speed.md`, `jump-arc.md`, `air-control.md`, `landing.md`, and
for rolls `roll-input.md` and `roll-speed.md`.

Frames are logic frames at 60 Hz, numbered as in a trace. `x`, `y`, `vx`
and `vy` are as in a trace: right and down are positive, speeds in px per
frame. Speeds are whole multiples of 1/256 px per frame and are written
both ways where it helps (576/256 = 2.25). "Left", "Right", "Down",
"Jump", "Action" are the neutral inputs `L`, `R`, `D`, `J`, `A`.

Measured on flat ground with the second character under control after a
swap (`S`), the first character following: 41 runs of up to 170 frames
covering starting, stopping, reversing, walking, running, crouching,
jumps with the jump input held for 1 to 40 frames and until landing, a
second jump press in the air, and jumps from standing, walking and
running with each kind of air input. On every frame of every run, `x`,
`y`, `vx`, `vy` and `ground` match the rules below.

## Values

| Value | First character | Second character |
|---|---|---|
| Walking target speed (Left or Right) | ±2 (512/256) | **±2.25** (576/256) |
| Running target speed (with Action held) | ±3 (768/256) | **±3.375** (864/256) |
| `vy` set on the take-off frame | −7 | **−8.3515625** (−2138/256) |
| Gravity while the jump input is held since the press | 0.28125 (72/256) | **0.3515625** (90/256) |
| Gravity otherwise | 0.4375 (112/256) | 0.4375 (112/256), the same |
| Fastest fall (`vy` cap) | 8 | 8, the same |
| Crouch: frames with target 0 after Down is released | 10 (U to U + 9) | **13** (U to U + 12) |

Everything else in those files applies to the second character
unchanged:

- The speed rule: each grounded or airborne frame, the step is
  (target − speed) ÷ 8 rounded toward zero, and the speed becomes the
  target when the step is 0. The target follows the input of the same
  frame, and is 0 with no direction or with both. It applies on the
  ground, in the air, on the take-off frame and on the landing frame.
- Crouching: on the first frame Down is held the target is still the
  normal one; from the next frame until Down is released it is 0.
- Turns on the ground last 8 frames (T to T + 7) and facing changes
  on T + 4, including after a single frame of the opposite
  direction from standing still. In a jump, from the take-off frame on,
  facing follows the input of the same frame.
- The jump: on the take-off frame `y` doesn't change and `ground` is
  still 1; the lighter gravity applies on frames T + 1 to P + H − 1
  (H = 1 and H = 2 give the same jump); once released, pressing jump
  again in the air doesn't bring the lighter gravity back.
- The landing rule (`landing.md`): a falling frame that ends less than
  5 px above the floor lands, and the player is placed on it.

## Ground speed

From rest, Left or Right held from frame 0: `vx` (in the direction held)
is 72/256, 135/256, 190/256 (0.28125, 0.52734375, 0.7421875) on frames
0 to 2. It reaches 2.25 on frame 37, after 69.4765625 px.

Summary (frame 0 is the first frame with the new input; "reached" is the
first frame at the target; distance from the end of the frame before
frame 0 to the end of the "reached" frame; moving right, mirror for
left):

| From | To (input) | First frames, `vx` | Reached on | Distance until reached |
|---|---|---|---|---|
| 0 | +2.25 (Right) | 0.28125, 0.52734375, 0.7421875 | 37 | 69.4765625 px |
| 0 | +3.375 (Right + Action held) | 0.421875, 0.7890625, 1.109375 | 40 | 114.390625 px |
| +2.25 | 0 (nothing) | 1.96875, 1.72265625, 1.5078125 | 37 | 16.0234375 px |
| +3.375 | 0 (Action only) | 2.953125, 2.5859375, 2.265625 | 40 | 23.984375 px |
| +3.375 | +2.25 (Right, Action released) | 3.234375, 3.11328125, 3.0078125 | 32 | 82.3671875 px |
| +2.25 | +3.375 (Action held, no press) | 2.390625, 2.51171875, 2.6171875 | 32 | 103.2578125 px |

Reversing from walking speed (+2.25, Left held from frame 0): `vx` is
1.6875, 1.1953125, 0.765625, 0.390625, 0.0625 on frames 0 to 4, so the
player travels 4.1015625 px further right, and is first negative on
frame 5 (−0.2265625). It reaches −2.25 on frame 42. From running speed
with Left and Action held: 2.53125, 1.79296875, 1.1484375, 0.5859375,
0.09375, then −0.33984375 on frame 5, reaching −3.375 on frame 45.

Crouching, example: walking at +2.25 with Right held, Down also held on
frames 60 to 89. `vx` is 2.25 on frame 60, 1.96875 on 61, and keeps
falling while Down is held and after it is released, to 0.02734375 on
frame 97 and 0 on 98. It stays 0 to frame 102 and is 0.28125 again on
frame 103. Measured with Down held for 30 and 40 frames, from rest, from
walking speed and from running speed.

## Jump arc

`vy` on frame T + n, while the jump is in the air with the jump input
held since P, is −8.3515625 + 0.3515625 × n, and `y` relative to the
take-off frame is −8.3515625 × n + 0.17578125 × n(n + 1).

Held from take-off to landing:

| Frame | `y` | `vy` |
|---|---|---|
| T | 0 | −8.3515625 |
| T + 1 | −8 | −8 |
| T + 2 | −15.6484375 | −7.6484375 |
| T + 5 | −36.484375 | −6.59375 |
| T + 10 | −64.1796875 | −4.8359375 |
| T + 20 | −93.203125 | −1.3203125 |
| T + 23 | −95.0546875 | −0.265625 |
| T + 24 | −94.96875 | 0.0859375 |
| T + 30 | −87.0703125 | 2.1953125 |
| T + 40 | −45.78125 | 5.7109375 |
| T + 45 | −11.953125 | 7.46875 |
| T + 46 | 0 | 0 |

T + 46 is the landing frame: that frame's `vy` (7.8203125) would leave
the player 4.1328125 px above the floor, which is less than 5, so they
land (`landing.md`).

`apex_height` and `airtime` (`traces/METRICS.md`) for a jump from flat
ground landing on the same ground, with the jump input held for H frames
from P (as in `jump-arc.md`):

| H | `apex_height` (px) | `airtime` (frames) |
|---|---|---|
| 1 or 2 | 75.5546875 | 37 |
| 3 | 77.1875 | 37 |
| 4 | 78.734375 | 38 |
| 5 | 80.1953125 | 38 |
| 8 | 84.1796875 | 39 |
| 10 | 86.5 | 40 |
| 12 | 88.5 | 41 |
| 15 | 91.078125 | 42 |
| 20 | 93.9296875 | 43 |
| 25 | 95.0546875 | 44 |
| 30 | 95.0546875 | 45 |
| 40 | 95.0546875 | 46 |
| held until landing | 95.0546875 | 46 |

From H = 25 on, the top of the arc doesn't get higher.

## Jump distances

Horizontal distance (`x` on the landing frame minus `x` on the take-off
frame) for jumps with the jump input held until landing, landing on
T + 46 on flat ground. Columns as in `air-control.md`; moving right
(mirror images give the same distances with the sign reversed).

| Before | In the air | Distance (px) | `vx` on landing |
|---|---|---|---|
| Standing | Right | 89.4453125 | 2.25 |
| Standing | Right and Action, from T + 1 | 131.265625 | 3.375 |
| Walking at +2.25, Right | Right | 103.5 | 2.25 |
| Walking at +2.25, Right | nothing | 14.0546875 | 0 |
| Walking at +2.25, Right | Left | −75.5546875 | −2.25 |
| Running at +3.375, Right and Action | Right and Action | 155.25 | 3.375 |
| Running at +3.375, Right and Action | Right | 110.6328125 | 2.25 |

## Rolls

The rules of `roll-input.md` and `roll-speed.md` apply to the second
character with the differences below. They use those files' names: S is
the `roll_start` frame and D is the frame after the roll's last frame.

Measured on flat ground with the second character under control after a
swap, the first character following: 166 runs of up to 189 frames, 190
rolls (most going left, 11 runs with rolls going right), covering
presses from standing, walking, sliding, speeding up and running, no
direction, either one, both, Up, Down and Action held on and after S,
steering both ways, the fast-roll windows on both sides of their
bounds, carrying on into running, the next roll after a stop (presses
on every frame from D − 6 to D + 21) and after carrying on (D to
D + 11), presses in
the air before and after landing, and jumps out of a roll. On every roll
frame and every frame of the stop or the carry-on, `vx` and the movement
match the rules below, and every start and end frame matches.

| Value | First character | Second character |
|---|---|---|
| Roll length | 29 frames (D = S + 29) | **24 frames** (D = S + 24) |
| A roll that starts on D + 17 of the previous roll | 30 frames | **25 frames** |
| Slow roll size | 1 (256/256) | **1.125** (288/256) |
| Normal roll size | 3 (768/256) | **3.375** (864/256) |
| Fast roll size | 4 (1024/256) | **4.5** (1152/256) |
| Size of a slow roll after steering in its direction | 3 (768/256) | **3** (768/256), the same; not 3.375 |
| Carrying on into running: still `roll` on | D, D + 1, D + 2 | **D to D + 5** |
| Carrying on: `roll_end` | D + 3 | **D + 6** |
| Next roll after carrying on, press on P ≥ D + 2 | max(P, D + 4) | **max(P, D + 7)** |
| Down held on the press frame | the ground action | a roll (`roll-input.md`) |

In detail:

- **Length.** A roll that starts on D + 17 of the previous roll lasts 25
  frames whether it starts from a remembered press or from a new press
  on D + 17 (on its first frame the speed stays 0, as for the first
  character). A press on D + 18 or later starts a 24-frame roll.
- **Carrying on.** The roll's speed rule applies from D to D + 6 (the
  `roll_end` frame); the ground rule of `ground-speed.md` applies from
  D + 7. Example: Action alone held through a slow roll, `vx` is
  1.09375 (280/256) on D + 6, then 0.95703125 on D + 7 (slowing toward 0,
  no direction held). Running frames on D + 6 don't count toward a fast
  next roll, as `roll_end` frames never do.
- **Steering.** A slow roll steered in its direction moves toward 3
  (768/256) from that frame on: the first character's normal size, not
  this character's. Normal and fast rolls keep their sizes. Reversing is
  as for the first character (the size doesn't change on that frame).
- **Running recently**, the windows of `roll-speed.md` are the same:
  a running frame on or after S − 15 (Left or Right held on S) or
  S − 14 (neither). The measured exception differs: if the only running
  frame is a single one that starts from standing still, a press with
  neither Left nor Right held on S gives a slow roll at every gap
  measured (1 to 14 frames), **even when Left or Right was held on
  frames between them** (measured with the direction held on the frame
  before S only and on every frame between, gaps 3 and 10). With Left or
  Right held on S the roll is fast within the window (gaps 1 to 14), and
  two running frames in a row follow the window rule.
- **Facing in the stop** turns to a direction on the frame it is
  pressed, for presses on D + 1, D + 3, D + 6, D + 14, D + 16 and D + 17,
  while the stop's speeds stay as in `roll-speed.md`. A direction pressed
  on D + 18 starts a normal ground turn.
- **Jumping out of a roll** is as in `roll-speed.md`, with this
  character's targets after take-off (±2.25, ±3.375 with Action, 0) and
  its own jump (`vy` −8.3515625 on the take-off frame). Measured with
  presses on S + 1, S + 5, S + 12, S + 22, S + 23 and D.

Everything else is as for the first character: when a press starts a
roll on the ground and in the air (a press on P ≥ L − 6 held through
L + 1 starts a roll on L + 1, measured for one jump height), the size and
direction on S, the speed rule, the stop after a roll (D + 1 and D + 2
toward 0, D + 3 moves with `vx` 0 at the end of the frame, still to
D + 17, moving again from D + 18), the remembered press (D − 2 to
D + 16 starts the next roll on D + 17, earlier presses are ignored), and
a fast roll always ending in a stop.

Worked values, moving right (frame S is the press; distances from the
position at the start of S):

| Before S | Held on S, then | Size | `vx` on S, S + 1, S + 2 | `vx` on D | Distance S to D | Distance S to D + 3 (stop) |
|---|---|---|---|---|---|---|
| Standing | Action; nothing | slow | 0.140625, 0.26171875, 0.3671875 | 1.0703125 | 20.28125 | 22.7578125 |
| Standing | Action and Right; Right | normal | 0.421875, 0.7890625, 1.109375 | 3.2421875 | 61.31640625 | 68.82421875 |
| Walking right at +2.25 | Action and Right; Right | normal | 2.390625, 2.51171875, 2.6171875 | 3.3203125 | 76.53125 | 84.20703125 |
| Running right; Action released one frame (`vx` 3.234375) | Action and Right; Right | fast | 3.390625, 3.52734375, 3.6484375 | 4.44140625 | 103.703125 | 113.97265625 |

Reversing: walking right at +2.25, Action and Right on S, then Left
held. `vx` is 2.390625 on S, then 1.671875, 1.04296875, 0.4921875,
0.01171875 on S + 1 to S + 4, first negative on S + 5 (−0.41015625), and
−3.12890625 on D; the player ends 39.625 px left of the start of S on D.

Steering a slow roll: standing, Action alone on S, Right from S + 10.
`vx` is 0.8203125 on S + 9 and 1.08984375 on S + 10 (toward 3), and
2.6953125 on D.

## Not covered

- Rolls on slopes, off ledges, into walls or enemies; steering during
  D to D + 6 of a carry-on with a changed input; running frames during
  the stop; presses in the air for other jump heights. This character
  has no Down + Action ground action (`roll-input.md`).
- What the first character does while following a rolling second
  character.
- Falls that don't start with a jump (walking off a ledge), jumping on
  landing from a press made in the air, and the landing lockout of
  `jump-input.md` for this character.
- Slopes, steep faces, walls, platforms from below, carrying, bouncing
  off enemies and being hurt, with the second character under control.
- The swap itself (`S`) is in `swap.md`. The measurements above start
  after it has finished.
- The first character's values when it follows the second.
- When a turn starts while the player is moving (as for the first
  character, see `ground-speed.md`).
