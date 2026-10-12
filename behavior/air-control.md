# Air control

Titles: `title-1`

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

## Not covered

- Facing on the landing frame when the input there differs from the
  last airborne frame, and turns that are in progress when the player
  leaves the ground.
- Down held in the air.
- An Action press on the take-off frame T.
- Jumps that start from a roll, falls after rolling off a ledge, and
  bounces off entities (see `bounce.md`).
- Ceilings. Walls met in the air are in `air-walls.md`; platforms passed
  from below are in `platforms-from-below.md`.
- Speed on frame L of a walk-off fall and on the grounded frames just
  before it (on rounded edges the speed was seen to rise above the
  target there).
