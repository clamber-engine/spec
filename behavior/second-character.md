# Second character: movement

Titles: `title-1`

How the **second character** (the one that joins later; see
`losing-a-character.md`) moves on the ground and in the air when it is
the character under control. It follows the same rules as the first
character, with its own values. This file lists what differs and gives
the second character's numbers; the rules themselves are in
`ground-speed.md`, `jump-arc.md`, `air-control.md`, `landing.md`,
`walk-off.md`, and for rolls `roll-input.md` and `roll-speed.md`.

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
| Crouch: shortest crouch (D to U − 1), even after a shorter press | 13 (D to D + 12) | **9** (D to D + 8) |
| Crouch: Down held again starts a new crouch on | U to U + 9 | **U to U + 12** |

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

Crouching, short press: walking left with Left held, Down also held
on frame 40 only. The crouch is frames 40 to 48 (D to D + 8), so U is
49; the target is 0 from 41 to 61 (U + 12), and `vx` grows again from
62. Down held for 1, 3 or 8 frames gives the same frames; held for 10,
12 or 14 frames, U is the frame Down is released and `vx` grows again
from U + 13. Down held again on any of U + 1 to U + 12 after a longer
crouch started a new 9-frame crouch, with the target 0 on its D as
well; on U + 13 it started one whose D has the normal target.

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
| Next roll after carrying on: a press on P ≥ D + 2 starts it on | max(P, D + 4), if Action is still held then | **max(P, D + 7)**, if Action is still held then |
| Down makes no difference, roll ending in a stop | S + 1 to D + 17 | S + 1 to D + 17, the same |
| Down makes no difference, roll carrying on | S + 1 to D + 3 | **S + 1 to D + 6** |
| Down held on the press frame | the ground action | a roll (`roll-input.md`) |

Rolling off a ledge with a steep face: `rolling-off-ledges.md`, "The
second character".

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
- **Next roll after carrying on.** A press on D + 2 to D + 6 starts a
  roll on D + 7, the frame after `roll_end`, if Action is still held on
  D + 7; if Action is released before D + 7, that press starts no roll.
  A press on D + 7 or later starts a roll on that frame.
- **Down during a roll.** As for the first character (`roll-input.md`,
  "Down during a roll"), Down makes no difference at all from S + 1 to
  D + 17 of a roll that ends in a stop, and from S + 1 to D + 6 of one
  that carries on into running. As this character has no ground action,
  a press with Down held is an ordinary roll press at all times:
  remembered or ignored during the stop as without Down, and starting a
  roll on the frames given above with or without Down held then. This
  character's crouch (see "Values") starts no earlier than D + 18 after
  a stop or D + 7 after carrying on, and only if Down is held on that
  frame and no roll starts on or before it.

  For example, standing facing left with Left held from frame 0 on,
  Action is pressed on frame 10 and held to 34: the roll is frames 10 to
  33 and carries on into running (D = 34, `roll_end` on 40). Action is
  pressed again on 39 (D + 5) for one frame and Down is held on 41
  (D + 7) only: the character crouches from 41 and no roll starts. With
  Action held from 39 to 41 instead, a roll starts on 41 whether Down is
  held on 41 or not. After a one-frame press from standing on frame 10
  (roll 10 to 33, D = 34), Down held from frame 25 on: with no other
  press, the crouch starts on 52 (D + 18); with Action pressed again on
  51 (D + 17) or 52, a roll starts on that frame.

  Checked with 19 200 runs of 200 frames, from standing facing either
  way on the flat ground described above: Action pressed for 1 to 6
  frames or held for 40 to 89, Down held for 1 to 40 frames at a time,
  Left, Right or neither in stretches, no Jump; half of the runs begin
  with a roll that carries on into running. Each run was played again
  with Down taken off from S + 1 to D + 17 (stop) or D + 6 (carried on)
  of every roll: all 3 644 937 frames were the same in both (this
  character's `x`, `y`, `vx`, `facing`, `ground` and state, and the
  following first character's `x` and state), 513 261 of them with Down
  taken off and 12 020 Action presses among those. Taking Down off one
  frame longer as well (D + 18 or D + 7) changed 300 812 frames in 5 929
  of the 19 200 runs. Presses on D + 2 to D + 6 after carrying on: 1 131
  with Action held on D + 7 all started a roll there (174 with Down held
  on D + 7); 1 486 released before D + 7 started none (225 crouched on
  D + 7, all with Down held then; the others neither rolled nor
  crouched).

  These rules hold on 1-in-4 slopes as well (`slopes.md`), uphill and
  downhill. Checked with 19 200 more runs of the same kind, from
  standing still facing either way on the flat floor just before a dip,
  on its downhill side, at its flat bottom and on its uphill side, with
  the first character following, each run stopping when this character
  left the dip and the flat floor just before it: all 2 612 635 frames
  were the same with Down taken off as above, 182 913 of them with Down
  taken off while this character was on a slope, and 4 324 Action
  presses among those. Over the runs, 610 325 roll frames, 248 342 stop
  frames and 45 825 frames of rolls carrying on into running were on a
  slope. Taking Down off one frame longer changed 127 454 frames in
  2 641 of 9 600 runs. Presses on D + 2 to D + 6 after carrying on: 917
  with Action held on D + 7 all started a roll there (136 with Down held
  on D + 7); 1 245 released before D + 7 started none (190 crouched on
  D + 7, all with Down held then; the others neither rolled nor
  crouched).

  They also hold next to and on steep faces (`steep-faces.md`): rolls
  going down a face falling to the right and on into the air beyond it,
  rolls blocked or stopped in front of a face rising to the right, and
  rolls down the gentle rise before the falling face. Checked with
  19 200 more runs of the same kind, from standing still facing either
  way on the floor at the top of the falling face and on the floor in
  front of the rising face, with the first character following, each
  run stopping when this character left that stretch of floor: all
  2 356 126 frames were the same with Down taken off as above, 11 064 of
  them with Down taken off while this character was on a face, and 277
  Action presses among those. Over the runs, 62 759 roll frames, 5 469
  stop frames and 899 frames of rolls carrying on into running were on a
  face, 309 444 frames of the three kinds had `vx` 0 within 16 px in
  front of the rise, and 148 084 were in the air. Taking Down off one
  frame longer changed 71 251 frames in 2 144 of 9 600 runs. Presses on
  D + 2 to D + 6 after carrying on, made on the floor with this
  character on the floor up to D + 7: 664 with Action held on D + 7 all
  started a roll there (89 with Down held on D + 7); 897 released before
  D + 7 started none (141 crouched on D + 7, all with Down held then).
  When D + 7 started on a face, or the character was in the air on any
  frame from P − 1 to D + 7, none of the 53 presses started a roll on
  D + 7, including the 15 with Action held from P to D + 7.
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

## Walking off a ledge

Walking or running off a ledge with a steep face follows `walk-off.md`
unchanged, with this character's take-off `vy` and lighter gravity:

- The leave frame L and the fall ("The leave frame", "Falling"): on L
  the player moves down 3.4375 and `vy` ends at 0.00390625; then `vy`
  increases by 0.4375 per frame up to 8, and the landing rule of
  `landing.md` applies.
- Jumping near the edge ("Jumping near the edge"): which presses count,
  which jump, and the jump gravity g(f) after a press that counted but
  gave no jump, with g(f) = 0.3515625 (90/256) while Jump stays held.
  A take-off sets `vy` to −8.3515625, and the jump then follows "Jump
  arc" above.
- `vx` in the fall from L + 1 on follows the speed rule toward ±2.25
  (walking) or ±3.375 (Action held), as on the ground.

Example, walking right at 2.25 px per frame (Right held): the player
moves onto the face on L − 2, so L − 1 and L start on it. Height below
the top of the ledge after each frame (negative is above it), Jump held
20 frames from the press:

| Frame | No press | Press on L − 4 | Press on L − 3 | Press on L − 2 | Press on L − 1 |
|---|---|---|---|---|---|
| L − 3 | 0 | 0 (T) | 0 | 0 | 0 |
| L − 2 | 3 | −8 | 3 (T) | 3 | 3 |
| L − 1 | 11 | −15.6484375 | −5 | 11 | 11 |
| L | 14.4375 | −22.9453125 | −12.6484375 | 14.3515625 | 14.4375 |
| L + 1 | 14.87890625 | −29.890625 | −19.9453125 | 14.70703125 | 14.87890625 |
| L + 2 | 15.7578125 | −36.484375 | −26.890625 | 15.4140625 | 15.7578125 |

The press on L − 2 counts (L − 2 doesn't start on the face) but gives no
jump (L − 1 starts on it), so the fall uses the lighter gravity: `vy` is
0.00390625 after L, 0.35546875 after L + 1 and 0.70703125 after L + 2.
The press on L − 1 does nothing (L − 1 starts on the face).

Running right at about 3.3 px per frame, the player goes over the face
of the same kind of ledge without a frame starting on it, and presses
on L − 1 and L still jump:

| Frame | No press | Press on L − 2 | Press on L − 1 | Press on L |
|---|---|---|---|---|
| L − 1 | 0 | 0 (T) | 0 | 0 |
| L | 3.4375 | −8 | 3.3515625 (T) | 3.3515625 |
| L + 1 | 3.87890625 | −15.6484375 | −4.6484375 | 3.70703125 (T) |
| L + 2 | 4.7578125 | −22.9453125 | −12.296875 | −4.29296875 |
| L + 3 | 6.07421875 | −29.890625 | −19.59375 | −11.94140625 |

Measured on three ledges with a steep face, the second character under
control after a swap: walking and running right from 12 approach
positions each, with presses from L − 6 to L + 8 held 1, 3 or 20 frames
(3,312 cases). Every frame from L to the landing matched, 0 mismatches
(evidence in `walk-off.md`, "Jumping near the edge"). With the first
character's values instead (take-off `vy`, lighter gravity, walking and
running speeds), all 3,312 cases mismatch; with the normal gravity in place of 0.3515625, 837 do.

## Not covered

- Rolls on slopes (other than Down held during them), off ledges other
  than those in `rolling-off-ledges.md` ("The second character"), into
  walls or enemies; steering during
  D to D + 6 of a carry-on with a changed input; running frames during
  the stop; presses in the air for other jump heights. This character
  has no Down + Action ground action (`roll-input.md`).
- What the first character does while following a rolling second
  character.
- Walking off a ledge without a face, and walking or running left off a
  ledge. Jumping on landing from a press made in the air more than one
  frame before landing, and the landing lockout after falls. The
  lockout after jumps is in `jump-input.md` ("The second character").
- Slopes, steep faces, walls, platforms from below, carrying, bouncing
  off enemies and being hurt, with the second character under control.
- The swap itself (`S`) is in `swap.md`. The measurements above start
  after it has finished.
- The first character's values when it follows the second.
- When a turn starts while the player is moving (as for the first
  character, see `ground-speed.md`).
