# Ground speed

Titles: `title-1`

Horizontal speed while grounded on flat ground: starting, stopping,
walking, running, reversing, crouching, and when facing changes. Frames
are logic frames at 60 Hz, numbered as in a trace. `x` and `vx` are as in
a trace: right is positive, `vx` in px per frame. "Left", "Right", "Down",
"Action" are the neutral inputs `L`, `R`, `D`, `A` of the scenario format.
Measured on flat ground with the lead character.

Speeds are always whole multiples of 1/256 px per frame. The rule below
is exact in those units, so this section writes speeds both ways
(512/256 = 2 px per frame).

## Target speed

On every grounded frame the player has a **target speed** set by what is
held on that frame:

| Held | Target |
|---|---|
| Right | +2 (+512/256) |
| Left | −2 (−512/256) |
| Right and Action | +3 (+768/256) |
| Left and Action | −3 (−768/256) |
| No direction, or both Left and Right | 0, with or without Action |

Up makes no difference. Down is covered under "Crouching".

An Action **press** starts a roll (`roll-input.md`); this spec covers
Action that is already held, for example held through a landing or held
on after a roll carries on into running. The rule below also holds on
every frame of the roll in the `run-jump-flat` reference trace, where
Right and Action are held; speed during rolls in general is in
`roll-speed.md`.

## The speed rule

On each grounded frame, using speeds in 1/256 px per frame:

1. **gap** = target − current speed.
2. **step** = gap ÷ 8, rounded toward zero to a whole number.
3. If step is 0 (the gap is less than 8/256 either way), the speed
   becomes the target. Otherwise the speed changes by step.
4. The player moves by the new speed: `x` changes by exactly the new
   `vx` on that frame.

The same rule applies whether the player is speeding up, slowing down,
going from running to walking speed or back, or reversing; there is no
separate braking or turning rate. The rule applies from the first frame a
direction is held (the speed is already 0.25 on that frame when starting
from rest) and the target changes on the same frame the input changes.

Rounding toward zero matters when the gap is negative: a gap of −686/256
gives a step of −85/256, not −86/256.

### Worked values

From rest, Right held from frame 0:

| Frame | `vx` (1/256) | `vx` (px/frame) | `x` |
|---|---|---|---|
| 0 | 64 | 0.25 | 0.25 |
| 1 | 120 | 0.46875 | 0.71875 |
| 2 | 169 | 0.66015625 | 1.37890625 |
| 3 | 211 | 0.82421875 | 2.203125 |
| 10 | 392 | 1.53125 | 11.16796875 |
| 20 | 478 | 1.8671875 | 28.69921875 |
| 29 | 499 | 1.94921875 | 45.99609375 |
| 30 | 500 | 1.953125 | 47.94921875 |
| 35 | 505 | 1.97265625 | 57.77343750 |
| 36 | 512 | 2 | 59.7734375 |

Near the target the gap is between 8/256 and 15/256, so the step is
1/256 per frame (frames 29 to 35), and on frame 36 the gap of 7/256 is
closed at once.

Summary of common changes (frame 0 is the first frame with the new
input; "reached" is the first frame at the target):

| From | To (input) | First frames, `vx` | Reached on | Distance until reached |
|---|---|---|---|---|
| 0 | +2 (Right) | 0.25, 0.46875, 0.66015625 | 36 | 59.7734375 px |
| 0 | +3 (Right + Action held) | 0.375, 0.703125, 0.98828125 | 39 | 98.74609375 px |
| +2 | 0 (nothing) | 1.75, 1.53125, 1.33984375 | 36 | 14.2265625 px |
| +3 | 0 (nothing, or Action only) | 2.625, 2.296875, 2.01171875 | 39 | 21.25390625 px |
| +3 | +2 (Right, Action released) | 2.875, 2.765625, 2.671875 | 31 | 71.24609375 px |
| +2 | +3 (Action held, no press) | 2.125, 2.234375, 2.328125 | 31 | 88.75390625 px |

Reversing from walking speed (+2, Left held from frame 0): `vx` is 1.5,
1.0625, 0.6796875, 0.34765625, 0.0546875 on frames 0 to 4, so the player
travels 3.64453125 px further right, and is first negative on frame 5
(−0.19921875). It reaches −2 on frame 41. From running speed with Left
and Action held: 2.25, 1.59375, 1.01953125, 0.51953125, 0.08203125, then
−0.30078125 on frame 5, reaching −3 on frame 44.

## Facing and turns

Facing (the trace's `facing` column) is not tied to the sign of `vx`. It
changes through a **turn**, which lasts 8 frames, T to T + 7. Facing
changes on frame **T + 4** (the row carrying the `turn` event). The turn
has no effect on speed: the speed rule above applies on every frame of it.

- **From standing still** (`vx` 0, not moving): holding the direction
  opposite to facing starts a turn on that same frame (T = the first frame
  it is held). Holding it for one frame is enough: the turn completes and
  facing changes on T + 4 even if nothing is held afterwards, or if the
  original direction is held for a frame during the turn.
- **At the end of a turn**, if the direction opposite to the new facing is
  held on T + 8, a new turn starts on T + 8. Example: walking right, Left
  held for 5 frames and then Right: facing becomes left 4 frames into the
  first turn and right again 4 frames into the second.
- **While moving**, holding the opposite direction starts a turn after a
  delay of 0 to 12 frames (measured). The delay depends on the player's
  recent movement in a way this spec doesn't define yet; see "Not covered".

## Crouching

Holding Down while grounded (without an Action press on that frame; see
`roll-input.md`) makes the player crouch:

- On the first frame Down is held, the target speed is still the one in
  the table above.
- From the next frame, and for as long as Down is held, the target is 0
  whatever else is held, so the player slows down under the speed rule and
  can't start moving.
- When Down is released on frame U, the target stays 0 on frames U to
  U + 9. From U + 10 the target is the normal one again.

Example: walking at +2 with Right held, Down held on frames 40 to 69 and
Right on throughout. `vx` is 2 on frame 40, 1.75 on 41, 1.53125 on 42, and
keeps falling while Down is held and after it is released, to 0.02734375
on 76 and 0 on 77. It stays 0 to frame 79 and is 0.25 again on frame 80.

Measured with Down held for 30 and 40 frames, from rest, from walking
speed and from running speed. Shorter holds are not measured.

## Not covered

- When a turn starts while the player is moving (the delay above).
- Air control: horizontal speed while jumping or falling, and on the
  landing frame.
- Moving platforms, and the frame before walking off a ledge
  (where speed and position were seen to behave differently).
- The Down + Action ground action (roll speed is in `roll-speed.md`).
- Slopes are in `slopes.md`: on 1-in-4 slopes these rules apply
  unchanged.
- Collisions with walls and entities. The second character's values
  are in `second-character.md`.
