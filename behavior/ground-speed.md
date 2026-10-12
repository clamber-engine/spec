# Ground speed

Titles: `title-1`, `title-2`

Horizontal speed while grounded on flat ground: starting, stopping,
walking, running, reversing, crouching, and when facing changes. Frames
are logic frames at 60 Hz, numbered as in a trace. `x` and `vx` are as in
a trace: right is positive, `vx` in px per frame. "Left", "Right", "Down",
"Action" are the neutral inputs `L`, `R`, `D`, `A` of the scenario format.
Measured on flat ground with the lead character.

The speed rule is the same in both titles. Target speeds, when run speed
applies, turns and crouching differ. The sections below up to "In
`title-2`" give the `title-1` values, and each place where `title-2`
differs is marked. "In `title-2`" gives the complete `title-2` values.

Speeds are always whole multiples of 1/256 px per frame. The rule below
is exact in those units, so this section writes speeds both ways
(512/256 = 2 px per frame).

## Target speed

This section is `title-1` only; see "In `title-2`" for its targets.

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

The rule is the same in `title-1` and `title-2`.

The same rule applies whether the player is speeding up, slowing down,
going from running to walking speed or back, or reversing; there is no
separate braking or turning rate. The rule applies from the first frame a
direction is held (the speed is already 0.25 on that frame when starting
from rest) and the target changes on the same frame the input changes.

Rounding toward zero matters when the gap is negative: a gap of −686/256
gives a step of −85/256, not −86/256.

### Worked values

These values are for `title-1`. Worked values for `title-2` are in "In
`title-2`".

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

The 8-frame turn and the facing change on T + 4 are the same in both
titles. When a turn starts while moving, and crouching during a turn,
differ in `title-2`; see "In `title-2`".

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

This section is `title-1` only; `title-2` crouching is different (see
"In `title-2`").

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

## In `title-2`

Everything in this section was measured on `title-2` only, with the lead
character at the start of a game, on flat ground. Speeds are again whole
multiples of 1/256 px per frame. Here "net direction" means Right or
Left held alone; both together, or neither, is no direction.

### Target speed (`title-2`)

On every grounded frame the target is:

| Held on this frame | Target |
|---|---|
| Down (with or without anything else) | 0 |
| Right, no Down | +2.25 (+576/256) or +3.375 (+864/256) |
| Left, no Down | −2.25 (−576/256) or −3.375 (−864/256) |
| No direction, or both Left and Right, no Down | 0, with or without Action |

The larger speed (3.375) applies only if, on the **previous** frame,
Action was held together with a net direction (Right or Left alone). It
does not matter which direction that was. Otherwise the smaller speed
(2.25) applies. So:

- On the first frame a direction is held after a frame with no
  direction, the target is ±2.25 even if Action is held throughout. From
  the next frame it is ±3.375.
- On the frame Action is released while a direction is held, the target
  is still ±3.375. From the next frame it is ±2.25.
- Reversing with Action held (Right and Action, then Left and Action)
  gives −3.375 on the first frame of Left.

Up makes no difference to the target, except that it keeps a crouch
going. While crouching, and on the first frame after a crouch, the target
is 0 (see "Crouching" below). All of this was measured with the previous
frame also grounded; the first grounded frame after a landing is not
covered.

As in `title-1`, an Action **press** starts a roll; this section covers
Action that is already held. Speed on roll frames, and on the first frame
after a roll ends, is not covered for `title-2`.

### Speed rule (`title-2`)

The speed rule in "The speed rule" above applies unchanged on every
grounded frame, including turn frames and crouch frames, with the target
above.

### Worked values (`title-2`)

From rest, Right held from frame 0:

| Frame | `vx` (1/256) | `vx` (px/frame) | `x` |
|---|---|---|---|
| 0 | 72 | 0.28125 | 0.28125 |
| 1 | 135 | 0.52734375 | 0.80859375 |
| 2 | 190 | 0.7421875 | 1.55078125 |
| 3 | 238 | 0.9296875 | 2.48046875 |
| 10 | 441 | 1.72265625 | 12.5859375 |
| 20 | 538 | 2.1015625 | 32.28515625 |
| 30 | 563 | 2.19921875 | 53.94921875 |
| 36 | 569 | 2.22265625 | 67.2265625 |
| 37 | 576 | 2.25 | 69.4765625 |

Summary (frame 0 is the first frame with the new input; "reached" is the
first frame at the target; the distance includes that frame):

| From | Input from frame 0 | First frames, `vx` | Reached on | Distance until reached |
|---|---|---|---|---|
| 0 | Right | 0.28125, 0.52734375, 0.7421875 | 37 | 69.4765625 px |
| 0 | Right, with Action already held (no direction before) | 0.28125, 0.66796875, 1.00390625 | 40 | 113.37890625 px |
| +2.25 | nothing | 1.96875, 1.72265625, 1.5078125 | 37 | 16.0234375 px |
| +3.375 | nothing, or Action only | 2.953125, 2.5859375, 2.265625 | 40 | 23.984375 px |
| +3.375 | Right, Action released on frame 0 | 3.375, 3.234375, 3.11328125 | 33 | 85.7421875 px |

Reversing from +2.25 with Left held from frame 0: `vx` is 1.6875,
1.1953125, 0.765625, 0.390625, 0.0625 on frames 0 to 4. The player
travels 4.1015625 px further right, is first negative on frame 5
(−0.2265625) and reaches −2.25 on frame 42. From +3.375 with Left and
Action held: 2.53125, 1.79296875, 1.1484375, 0.5859375, 0.09375, then
−0.33984375 on frame 5, reaching −3.375 on frame 45.

### Turns (`title-2`)

A turn lasts 8 frames, T to T + 7, and facing changes on T + 4, as in
`title-1` (one exception below). It has no effect on speed. Unlike `title-1`, a turn starts
without delay, whether the player is standing or moving:

- On a grounded frame that is not part of a turn, a crouch, or the 11
  frames after a crouch, holding the net direction opposite to facing
  starts a turn on that frame (T). This holds at any speed, walking or
  running.
- Once started, the turn completes even if the direction is released or
  the original direction is held.
- On T + 8, if the direction opposite to the new facing is held, a new
  turn starts on T + 8.
- **Short turn.** If on T + 1 the player is moving toward the new facing
  at 797/256 px per frame or faster, the turn lasts 7 frames, T to T + 6.
  Facing still changes on T + 4, and a new turn can start on T + 7. At
  780/256 or slower the turn lasts 8 frames. Speeds between these weren't
  seen. This happens when a turn starts on T + 8 of a previous turn while
  the player is running toward the new facing.
- If a crouch starts during a turn, the turn ends at once. If the crouch
  starts on T + 1 to T + 4, facing doesn't change. If it starts on T + 5
  or later, facing has already changed and stays changed.
- The first frame after a roll is not covered.

### Crouching (`title-2`)

- A crouch starts on a grounded frame on which Down is held without Up.
  The target is 0 from that frame, so the player slows down under the
  speed rule.
- The crouch continues on every frame on which exactly one of Down and
  Up is held. Up alone keeps the player crouched (target 0) once a crouch
  has started, even with a direction held.
- The crouch ends on the first frame U on which neither, or both, of
  Down and Up are held. The target is 0 on frame U and normal from U + 1.
- From U to U + 10 no turn can start, even if the opposite direction is
  held. A turn can start on U + 11.
- If Down is held without Up on any frame from U to U + 10, a new crouch
  starts on that frame.
- Holding Down and Up together without a crouch (for example while
  walking) gives target 0 but doesn't start a crouch.

Example: walking at +2.25 with Right held, Down held on frames 45 to 74,
Right on throughout. `vx` is 1.96875 on frame 45, 1.72265625 on 46, and
falls to 0.05078125 (13/256) on frame 75 (= U), then rises again from
frame 76 (0.32421875).

### Evidence (`title-2`)

All runs were by inputs, with the lead character on flat ground.

- 868 runs, made up of:
  - turns after walking for 1 to 60 frames, and turns from running;
  - crouches of 1 to 40 frames, including a crouch on each frame of a
    turn;
  - 487 runs of chained turns at different speeds;
  - 270 runs of random inputs, in two independent sets of 120 and 150.
    The second set was checked only after the target rule was fixed;
    it gave 0 mismatches in 22735 frames.
- Speed rule: 79477 grounded frames, 0 mismatches.
- Target speed: 66680 frames, 0 mismatches.
- Turns: 1447 turns in 46746 frames, 0 mismatches.
- Frames during and directly after a roll, and frames touching a wall,
  were left out.

### Not covered in `title-2`

- Rolls (speed, the stop after a roll, and facing on the first frame after
  a roll). The Down + Action ground action.
- The frame after a landing, slopes, walls, and the second character.
  Air control and the landing frame are in `air-control.md` ("In
  `title-2`").

## Not covered

This list is for `title-1`; `title-2` has its own list above.

- When a turn starts while the player is moving (the delay above).
- Air control: horizontal speed while jumping or falling, and on the
  landing frame.
- Moving platforms, and the frame before walking off a ledge
  (where speed and position were seen to behave differently).
- The Down + Action ground action is in `ground-action.md` (roll speed
  is in `roll-speed.md`).
- Slopes are in `slopes.md`: on 1-in-4 slopes these rules apply
  unchanged.
- Collisions with walls and entities. The second character's values
  are in `second-character.md`.
