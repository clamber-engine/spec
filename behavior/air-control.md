# Air control

Titles: `title-1`, `title-2`

Horizontal speed and facing while the player is in the air: during a
jump and during a fall after walking off a ledge. Frames are logic frames
at 60 Hz, numbered as in a trace. `x`, `vx` and `facing` are as in a
trace: right is positive, `vx` in px per frame. "Left", "Right", "Jump",
"Action" are the neutral inputs `L`, `R`, `J`, `A` of the scenario
format. Measured over flat ground with the lead character.

Speeds are always whole multiples of 1/256 px per frame. The rule below
is exact in those units, so this spec writes speeds both ways
(512/256 = 2 px per frame).

Frames of a jump are named as in `jump-arc.md`: the press frame P, the
take-off frame T = P + 1 (the row with `jump_start`, still grounded), the
airborne frames after it, and the landing frame (the row with `land`).

The sections below up to "In `title-2`" give the `title-1` values. "In
`title-2`" gives the `title-2` values: the same speed rule with that
title's targets, and the same facing rule, but the frames around
take-off differ.

## Horizontal speed

On every airborne frame, and also on the take-off frame and on the
landing frame, the player has a **target speed** set by what is held on
that frame:

| Held | Target |
|---|---|
| Right | +2 (+512/256) |
| Left | −2 (−512/256) |
| Right and Action | +3 (+768/256) |
| Left and Action | −3 (−768/256) |
| No direction, or both Left and Right | 0, with or without Action |

Jump and Up make no difference to the target.

Each of those frames, using speeds in 1/256 px per frame:

1. **gap** = target − current speed.
2. **step** = gap ÷ 8, rounded toward zero to a whole number.
3. If step is 0 (the gap is less than 8/256 either way), the speed
   becomes the target. Otherwise the speed changes by step.
4. The player moves by the new speed: `x` changes by exactly the new
   `vx` on that frame.

This is the same target and rule as on flat ground (`ground-speed.md`):
there is no separate air acceleration, air braking or air turning rate,
and nothing carries the take-off speed through the jump. Releasing every
direction in the air slows the player to a stop in the air exactly as on
the ground; holding the opposite direction reverses them.

Notes:

- The rule holds whether the player is rising or falling, whether Jump is
  still held or was released early (short jumps), and in falls after
  walking off a ledge from frame L + 1 on (L is the first airborne frame,
  as in `walk-off.md`). Frame L itself and the grounded frames just before
  it are not covered.
- Holding Action in the air raises the target to ±3 even though no roll
  starts in the air (`roll-input.md`). Whether an Action press made in
  the air also starts a roll on landing is covered there.
- The target follows the input of the same frame, so input on the
  landing frame already counts: the landing frame is not a special case.

### Worked values

Walking right at +2, Jump pressed with Right held on frame 60 (T = 61),
Right held until frame 80 and Left from frame 81 (in the air, still
rising), Jump held throughout:

| Frame | Held | `vx` (1/256) | `vx` (px/frame) | Change in `x` |
|---|---|---|---|---|
| 80 | Right | 512 | 2 | 2 |
| 81 | Left | 384 | 1.5 | 1.5 |
| 82 | Left | 272 | 1.0625 | 1.0625 |
| 83 | Left | 174 | 0.6796875 | 0.6796875 |
| 84 | Left | 89 | 0.34765625 | 0.34765625 |
| 85 | Left | 14 | 0.0546875 | 0.0546875 |
| 86 | Left | −51 | −0.19921875 | −0.19921875 |

From rest, Right held from the take-off frame on: `vx` is 0.25, 0.46875,
0.66015625 on frames T, T + 1, T + 2, the same as starting to walk on the
ground.

### Jump distances

Horizontal distance `x` on the landing frame minus `x` on the take-off
frame, for a jump with Jump held from P until landing (landing frame
T + 49 over flat ground, as in `jump-arc.md`). "Before" is how the player
was moving before P, with the same input held on P; "in the air" is held
from T until landing.

| Before | In the air | Distance (px) | `vx` on landing |
|---|---|---|---|
| Standing | nothing | 0 | 0 |
| Standing | Right | 85.5234375 | 2 |
| Standing | Right and Action, from T + 1 (see below) | 125.74609375 | 3 |
| Walking at +2, Right | Right | 98 | 2 |
| Walking at +2, Right | nothing | 12.4765625 | 0 |
| Walking at +2, Right | Left | −73.19140625 | −2 |
| Running at +3, Right and Action | Right and Action | 147 | 3 |
| Running at +3, Right and Action | Right | 104.37109375 | 2 |
| Running at +3, Right and Action | nothing | 18.62890625 | 0 |
| Running at +3, Right and Action | Left and Action | −109.9375 | −3 |

In the standing row with Right and Action, only Jump is held on P and
on T, and Right and Action are held from T + 1 until landing, so the
speed rule starts from 0 on T + 1. Holding them from T instead would be
an Action press on the take-off frame, which is still grounded; that
case is not covered (see `roll-input.md` for presses on the ground).

Mirror images (moving left, Left held) give the same distances with the
sign reversed. A jump with Jump held for 5 frames from P (landing on
T + 33) from walking at +2 with Right held throughout covers 66 px.

## Facing

**During a jump**, from the take-off frame T to the last airborne frame,
facing follows the input on the same frame:

- Left held (without Right): facing becomes left on that frame.
- Right held (without Left): facing becomes right on that frame.
- No direction, or both: facing doesn't change.

There is no 8-frame turn in the air: facing changes on the first frame
the direction is held, rising or falling, and the frame carries the
`turn` event. The change has no effect on speed; the speed rule above
applies as usual, so the player usually keeps moving the old way for a
few frames while facing the new way.

On the press frame P the player is still on the ground and the rules of
`ground-speed.md` apply. Example: walking right, Jump and Left pressed
together on P: facing is still right on P and becomes left on T.

**During a fall after walking off a ledge** (no jump), facing doesn't
change while airborne, whatever is held. Speed still follows the rule
above, so holding the opposite direction slows and reverses the player
while they keep facing the way they walked off.

## In `title-2`

Everything in this section was measured on `title-2` only, with the lead
character at the start of a game, on flat ground, during jumps. Speeds
are whole multiples of 1/256 px per frame, written both ways. Frames of a
jump are named as in `jump-arc.md` ("In `title-2`"): the jump starts on
a grounded frame **G**, and T = **G + 2** is the `jump_start` frame, still
grounded. The first airborne frame is T + 1. "Net direction" means Right
or Left held alone, as in `ground-speed.md` ("In `title-2`").

### Before take-off (`title-2`)

On G, G + 1 and T the player is on the ground. Horizontal speed follows
the ground rules of `ground-speed.md` ("In `title-2`"): the same targets
and speed rule as on any grounded frame. Two things differ from other
grounded frames:

- **No turn starts.** Facing doesn't change on G, G + 1 or T, whatever
  is held. If the opposite direction is still held on T + 1, facing
  changes there (see "Facing" below). If it is released by then, facing
  stays as it was.
- **An Action press doesn't start a roll** on G + 1 or T. The jump goes
  ahead, and Action counts as held from that frame on.

Down held on these frames is the crouch case of `jump-input.md`
("Crouching (`title-2`)").

### Horizontal speed (`title-2`)

On every airborne frame from T + 1 on, and on the landing frame, the
target is:

| Held on this frame | Target |
|---|---|
| Right | +2.25 (+576/256) or +3.375 (+864/256) |
| Left | −2.25 (−576/256) or −3.375 (−864/256) |
| No direction, or both Left and Right | 0, with or without Action |

The larger speed applies only if, on the **previous** frame, Action was
held together with a net direction (either one). This is the same choice
as on the ground, and on T + 1 the previous frame is T, which is
grounded. So:

- On T + 1, Right gives +3.375 only if Right (or Left) and Action were
  held on T. Action first held in the air raises the target one frame
  later.
- On the frame Action is released with a direction held, the target is
  still ±3.375.

Down, Up and Jump make no difference to the target in the air. On the
ground, Down gives a target of 0; in the air it doesn't. The exception
is a crouch carried into a fall off a ledge (`walk-off.md`, "In
`title-2`"). An Action press
in the air starts nothing; it only counts as Action held.

Each of those frames, the speed rule of `ground-speed.md` applies
unchanged (step = gap ÷ 8 rounded toward zero, the target reached at once
when the step is 0), and `x` changes by exactly the new `vx`. There is no
separate air acceleration, braking or turning rate.

The same holds from T + 1 on for jumps that start during a roll. Their
frames G to T are in `roll-speed.md` ("In `title-2`").

### Facing (`title-2`)

From T + 1 to the landing frame, both included, facing follows the input
on the same frame:

- Left held without Right: facing becomes left.
- Right held without Left: facing becomes right.
- No direction, or both: facing doesn't change.

There is no 8-frame turn in the air. The change has no effect on speed.
The landing frame follows the same rule, also when the input there
differs from the last airborne frame (in `title-1` that case is not
covered).

### Worked values (`title-2`)

Walking right at +2.25: Right held from rest for 45 frames, then Jump and
Right on G, G + 1 and T, then Jump and Left from T + 1 on.

| Frame | Held | `vx` (1/256) | `vx` (px/frame) | Facing |
|---|---|---|---|---|
| G to T | Right, Jump | 576 | 2.25 | right |
| T + 1 | Left, Jump | 432 | 1.6875 | left |
| T + 2 | Left, Jump | 306 | 1.1953125 | left |
| T + 3 | Left, Jump | 196 | 0.765625 | left |
| T + 4 | Left, Jump | 100 | 0.390625 | left |
| T + 5 | Left, Jump | 16 | 0.0625 | left |
| T + 6 | Left, Jump | −58 | −0.2265625 | left |

### Jump distances (`title-2`)

Horizontal distance: `x` on the landing frame minus `x` on T. "Before" is
`vx` on G − 1 and what is held on G − 1. "G, G + 1, T" is what is held on
those three frames, and "From T + 1" is what is held from T + 1 until
landing. Jump is held throughout unless stated, so the player lands on
T + 44 (`jump-arc.md`, "In `title-2`").

| Before | G, G + 1, T | From T + 1 | Distance (px) | `vx` on landing |
|---|---|---|---|---|
| 0, nothing | Jump | nothing | 0 | 0 |
| 0, nothing | Jump on G; Right and Jump on G + 1 and T | Right | 86.66796875 | 2.25 |
| 0, nothing | Jump on G and G + 1; Right and Jump on T | Right | 84.9453125 | 2.25 |
| 0, nothing | Jump | Right and Action | 123.50390625 | 3.375 |
| 0, nothing | Jump on G and G + 1; Right, Action and Jump on T | Right and Action | 126.59765625 | 3.375 |
| 2.25, Right | Right and Jump | Right | 99 | 2.25 |
| 2.25, Right | Right and Jump | nothing | 16.0234375 | 0 |
| 2.25, Right | Right and Jump | Left | −67.1171875 | −2.25 |
| 2.25, Right | Right and Jump | Right and Action | 139.2578125 | 3.375 |
| 3.12890625 (801/256), Right and Action | Right, Action and Jump | Right and Action | 147.29296875 | 3.375 |
| 3.12890625 (801/256), Right and Action | Right, Action and Jump | Right | 107.02734375 | 2.25 |
| 3.12890625 (801/256), Right and Action | Right, Action and Jump | nothing | 22.82421875 | 0 |

Short jumps from walking at 2.25 with Right held: Jump held on G only
(Right alone on G + 1 and T and in the air) lands on T + 37 after
83.25 px. Jump held on G to T + 2 (5 frames) lands on T + 38 after
85.5 px.

Mirror images (moving left, Left held) give the same distances with the
sign reversed.

### Evidence (`title-2`)

312 jumps (56 from standing, 140 from walking, 71 from running, 45 during
a roll), 39,683 frames, with scripted cases and random input in the air
(directions, Action presses, Down, Up, Jump released at random times).
Away from walls: 12,821 airborne and landing frames for the speed rule
and target, and 12,529 for facing, with 0 mismatches. The input sequences
of 150 jumps were checked only after the rule was fixed on the others,
also with 0 mismatches. The frames G to T of all jumps that don't start
during a roll and have no Down held around take-off match the ground
rules (645 frames, 0 mismatches). Action presses on G + 1 or T: 46
jumps, none started a roll. Every
distance above was measured, and it is also what the rules give from the
listed inputs.

### Not covered in `title-2`

- Falls after walking off a ledge, and jumps from near the edge, are in
  `walk-off.md` ("In `title-2`"); falls after rolling off a ledge, and
  jumps during them, in `rolling-off-ledges.md` ("In `title-2`").
- The frames after the landing frame (turns that start there; rolls are
  in `roll-input.md`, "In `title-2`").
- Ceilings, and the second character. Walls met in the air are in
  `air-walls.md` ("In `title-2`"). Bounces are
  in `bounce.md` ("In `title-2`"); jumps from slopes and landing on them
  in `slopes.md` ("In `title-2`").

## Not covered

This list is for `title-1`; `title-2` has its own list above.

- Facing on the landing frame when the input there differs from the
  last airborne frame, and turns that are in progress when the player
  leaves the ground.
- Down held in the air.
- An Action press on the take-off frame T.
- Bounces off entities (see `bounce.md`). Jumps that start from a roll
  are in `roll-speed.md`; falls after rolling off a ledge in
  `rolling-off-ledges.md`.
- Ceilings. Walls met in the air are in `air-walls.md`; platforms passed
  from below are in `platforms-from-below.md`.
- The second character: its values are in `second-character.md`.
- Speed on frame L of a walk-off fall and on the grounded frames just
  before it (on rounded edges the speed was seen to rise above the
  target there).
