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

- **While standing** (a mode defined in "Standing, walking and running"
  below; it includes standing still with `vx` 0): holding the direction
  opposite to facing starts a turn on that same frame (T = the first frame
  it is held). Holding it for one frame is enough: the turn completes and
  facing changes on T + 4 even if nothing is held afterwards, or if the
  original direction is held for a frame during the turn.
- **At the end of a turn**, if the direction opposite to the new facing is
  held on T + 8, a new turn starts on T + 8. Example: walking right, Left
  held for 5 frames and then Right: facing becomes left 4 frames into the
  first turn and right again 4 frames into the second.
- **While walking or running**, a turn can start only on frames with a
  turn check, which come at set points of the stride cycle below. If the
  opposite direction is held on such a frame, the turn starts on that
  frame. Holding it on other frames does nothing and isn't remembered. In
  practice the turn starts 0 to 7 frames after the opposite direction is
  first held when walking, and 0 to 12 frames when running.

In `title-1`, on frames held toward a rise right ahead, `facing` and the
start of turns follow `steep-faces.md` ("Turns next to a rise") instead.

### Standing, walking and running

This section is `title-1` only. It decides when a turn can start. It has
no effect on speed: the speed rule applies on every frame. The first
character's pictures follow it (`player-frames.md`, "Walking, running
and turning").

Outside a turn, the grounded player is always in one of three **modes**:
standing, walking or running. After walking off a ledge without a face
the mode goes on in the air too ("After a fall or a bounce"). The mode
isn't a trace column, and it isn't the trace's `state`
(`traces/VOCABULARY.md`: `idle` means not moving horizontally). A standing player can still be sliding (`vx` not 0),
for example after a turn that ends with no direction held.

In this section **forward** is the direction the player faces and
**opposite** is the other one. "A direction held" means Left or Right
held alone. **v** is |`vx`| in 1/256 px per frame on the current frame,
after the speed rule. The direction the player is moving in doesn't
matter.

#### The stride cycle

While walking or running, the player goes through a cycle of 20
**steps**, numbered 1 to 20. Step 1 follows step 20. A **stride counter**
measures progress through the current step:

- When walking or running starts, the cycle starts at step 1 and the
  counter is set to 256. Nothing else happens on that frame. This applies
  every time the mode becomes walking or running (see "Changing mode"
  below), and also when walking starts again at a turn check.
- On each later frame in the same mode, the counter goes down by the
  **stride rate** for that frame's v. If the result is 0 or less, the
  current step **ends** on that frame: 256 is added to the counter and the
  next step begins.

| Mode | Stride rate |
|---|---|
| Walking | ⌊v/4⌋ + ⌊v/8⌋ − 32, but at least 96 |
| Running | ⌊v/8⌋ + ⌊v/16⌋ + ⌊v/32⌋, but at least 96 |

⌊ ⌋ rounds down. Walking below v = 344 and running below v = 448, the
rate is 96, and a step lasts 2⅔ frames. Walking at 2 px per frame
(v = 512) the rate is 160: a step lasts 1.6 frames and the cycle 32
frames. Running at 3 px per frame (v = 768) the rate is 168 and the cycle
lasts 20 × 256 / 168 ≈ 30.5 frames. On flat ground v stays at or below
768, so at most one step ends on a frame.

#### When a step ends

Some steps end with a **turn check**, and some walking steps with a
**run check**. A check is made on the frame its step ends, using that
frame's inputs and v:

| Mode | Turn check at the end of steps | Run check at the end of steps |
|---|---|---|
| Walking | 1, 3, 6, 8, 11, 14, 16, 19 | 2, 4, 6, 9, 12, 14, 17, 20 |
| Running | 1, 2, 4, 6, 8, 10, 12, 14, 16 | none |

**Turn check while walking:**

1. If the opposite direction is held, a turn starts on this frame.
2. Otherwise, if v < 48: with no direction held, the player is standing
   from this frame. With forward held, walking starts again at step 1
   (counter 256).
3. Otherwise nothing happens.

**Turn check while running:**

1. If the opposite direction is held, a turn starts on this frame.
2. Otherwise, if v < 128 and no direction is held, the player is
   standing from this frame.
3. Otherwise nothing happens. With forward held, running carries on
   however slow the player is.

**Run check** (walking only): if Action is held and a direction is held
(either one), running starts on this frame at step 1 (counter 256). On
steps 6 and 14 the turn check comes first, and the run check is made
only if the turn check changed nothing.

#### Changing mode at other times

- **Standing:** holding the opposite direction starts a turn on that
  frame, at any speed. Holding forward starts walking on that frame. This
  is walking even with Action held: running only comes through a run
  check. With no direction held the player stays standing.
- **End of a turn** (T + 8): if the opposite direction (to the new
  facing) is held, a new turn starts, as above. With no direction held,
  the player is standing. With forward held, running starts if Action is
  held and v ≥ 512, and walking starts otherwise.
- **Running with Action released:** walking starts at step 1 on any frame
  on which Action isn't held, a direction is held, v < 640 and step 1 of
  the current run has already ended (including the frame it ends). On a
  frame with a turn check, this comes after the check, and only if the
  check started no turn and didn't make the player stand. Before step 1
  of the run has ended, the player keeps running.

#### After a landing

Measured for landings after jumps. With L the landing frame (the frame
carrying `land`; `landing.md`) and M the landing's switch frame
(`jump-input.md`, "The switch frame M"), the player is in none of the
three modes from L until a mode starts. These are the **landing
frames**. The speed rule applies as usual on them.

- **Facing** follows the input on every landing frame, as in the air
  (`air-control.md`, "Facing"): Left or Right held alone makes it that
  direction on that frame, and the frame carries the `turn` event if
  facing changed. No 8-frame turn starts on a landing frame.
- **On M**, if Action and exactly one of Left or Right are held and no
  roll starts on M (`roll-input.md`), running starts on M at step 1
  (counter 256), in the held direction. In practice this is Action held
  from the air through the landing, as when landing from a running
  jump. Speed doesn't matter. No other landing frame before K can start
  a mode.
- **On K**, if no mode has started: with Action and exactly one
  direction held, running starts at step 1; with one direction held and
  not Action, walking starts at step 1; otherwise the player is
  standing. Facing on K follows the input as above, so the held
  direction is always forward. The run needs no minimum speed here.
  K is:
  - M + 6 if M is later than L;
  - L + 10 if M = L and L is the seventh stage frame itself;
  - L + 9 if M = L and L is later than the seventh stage frame.
- From the frame after the mode starts, the rules above apply as usual.

The most common case is M = L + 1: running on L + 1, the mode on L + 7.

A roll (`roll-input.md`) or a jump (`jump-input.md`) can start on a
landing frame as those files say; a jump starts on E = M + 2 at the
earliest. An Action press on L + 1 or a later landing frame starts a
roll, not a run.

A landing after the first character's jump out of a roll pressed on the
ground follows these rules with M and K as for any other jump
(`jump-input.md`, "The switch frame M"). Measured on `title-1` on 246
such landings (213 on flat floor and 33 in random play through the
first level) where nothing else started before K: running started on M
on the 58 with Action and one direction held on M, and on the other 188
standing, walking or running started on K as above.

The same holds after the first character's jumps out of a roll pressed
in the air (`rolling-off-ledges.md`, "Jumping during the fall"):
measured on `title-1` on 30 such landings after rolls off a raised
platform where nothing else started before K and every frame up to the
start could be followed on screen, running started on M on the 2 with
Action and one direction held on M, and on the other 28 standing (9),
walking (18) or running (1) started on K.

For a landing after a jump that the second character started from a
roll (`jump-input.md`, "Landings this doesn't cover"), the spec uses M = L + 1: running on
L + 1, the mode on L + 7. In the original these vary; reference
comparisons on such landings should allow −1 frame on a run starting
just after the landing, +3 frames on walking or standing starting after
it, and the same on the turn checks that follow. Landings after falls
that didn't start with a jump, and after bounces, are in "After a fall
or a bounce" below.

Example: a standing jump facing right, Jump held for 20 frames, nothing
else held in the air (M = L + 1, so K = L + 7). In the first case Left
is held from L + 2; in the second Action is held from take-off and Right
is added from L + 3.

| Frame | Left from L + 2: `vx` (1/256), facing | Mode, counter | Action, Right from L + 3: `vx` (1/256) | Mode, counter |
|---|---|---|---|---|
| L, L + 1 | 0, right | landing | 0 | landing |
| L + 2 | −64, left (`turn`) | landing | 0 | landing |
| L + 3 to L + 6 | −120, −169, −211, −248 | landing | 96, 180, 253, 317 | landing |
| L + 7 | −281 | walking starts, 256 | 373 | running starts, 256 |
| L + 8 | −309 | walking, 160 | 422 | running, 160 |
| L + 9 | −334 | walking, 64 | 465 | running, 59 |
| L + 10 | −356 | walking, 219 (step 1 ends) | 502 | running, 207 (step 1 ends) |

With Right and Action both held from take-off, running starts on L + 1
(M) instead.

#### After a roll

With D the frame after a roll's last frame, as in `roll-input.md` and
`roll-speed.md`:

- **After a stop** (D to D + 16 in the stop), the player is standing on
  D + 17, whatever is held: holding a direction on D + 17 starts neither
  walking nor a turn on that frame. From D + 18 the standing rules apply
  (unless a roll starts on D + 17, `roll-input.md`).
- **After carrying on into running**, walking (not running) starts on
  D + 3, the `roll_end` frame, at step 1 (counter 256), whatever is
  held, even the opposite direction. From D + 4 the walking rules apply,
  so with Action and a direction held, running starts at the first run
  check (the end of step 2), and with the opposite direction held a turn
  starts at the first turn check (the end of step 1).

#### After a fall or a bounce

This section is `title-1` only, measured with the lead character. It
covers landings after the falls that don't start with a jump, and after
bounces. **G** is the landing frame (the frame carrying `land`), as in
`rolling-off-ledges.md`. Where a mode starts on a set frame below, it
depends on that frame's input and v, with forward and opposite as
above.

- **Walking or running off a ledge without a face** (`walk-off.md`,
  "Ledges without a face"): nothing changes at the edge, in the air or on
  landing. The mode the player was in goes on through the fall, and every
  rule of this section applies on each airborne frame as on the ground:
  the stride cycle goes on, its turn checks and run checks are made
  (so turns start in the air and facing changes on T + 4 as usual,
  running can start in the air, and the player can come to stand in the
  air), and a turn in progress at the edge goes on. There are no landing
  frames: G is an ordinary frame in whatever mode the player is in.
- **Falling from a steep face** (walking or running down the face at the
  top of a ledge and leaving it into a fall, `steep-faces.md`, "Following
  a face down, or leaving it"), landing on floor that isn't part of a
  face: G to G + 10 are **landing frames**, however long the fall was
  (falls of 4 to 47 frames measured). On them the player is in none of
  the three modes, facing doesn't change whatever is held, and no turn
  starts. On **G + 11** a mode starts as at the end of a turn ("Changing
  mode at other times"):
  - the opposite direction held (with or without Action): a turn starts;
  - forward held: running at step 1 (counter 256) if Action is held and
    v ≥ 512, otherwise walking at step 1;
  - no direction held: standing.

  An Action press on G + 1 or a later landing frame starts a roll on
  that frame, as on the ground (`roll-input.md`). Facing during the fall
  itself doesn't change (`air-control.md`).
- **After a bounce** (`bounce.md`): there are no landing frames. Facing
  doesn't change from the bounce frame to G, whatever is held. On G
  itself a mode starts:
  - Action and either direction held: running at step 1, facing
    unchanged, even with the opposite direction held. The speed doesn't
    matter. A press of Action on G counts as held on G; the roll it
    starts follows on G + 1 (`roll-input.md`, "In the air");
  - otherwise the opposite direction held: a turn starts on G;
  - forward held: walking at step 1;
  - no direction held: standing.
- **After the entrance throw** (`level-start.md`, "The landing"): as
  after a bounce, with the throw's landing frame as G. Facing is right
  from the start of the throw to G.
- **Rolling off a ledge**: the roll goes on through the fall and the
  landing (`rolling-off-ledges.md`, "When the roll ends"), and the mode
  after it is as in "After a roll" above, with that file's D.

From the frame after the mode starts, the rules above apply as usual.
None of this changes speed: the target and the speed rule are as on any
grounded or airborne frame, landing frames included.

Example, falling from a steep face: running right off it, then Action
alone held through the fall and the first landing frames, and Right
added to Action from G + 4 (first case) or G + 5 (second case).

| Frame | Right + Action from G + 4: `vx` (1/256) | Mode | Right + Action from G + 5: `vx` (1/256) | Mode |
|---|---|---|---|---|
| G | 113 | landing | 113 | landing |
| G + 3 | 77 | landing | 77 | landing |
| G + 4 | 163 | landing | 68 | landing |
| G + 10 | 495 | landing | 452 | landing |
| G + 11 | 529 | running starts | 491 | walking starts |

Example, a bounce: Right and Action held in the air, facing right, `vx`
768/256 on the frame before G, then Left and Action held from G.

| Frame | `vx` (1/256) | Mode, facing |
|---|---|---|
| G | 576 | running starts, right |
| G + 1, G + 2 | 408, 261 | running, right |
| G + 3 | 133 | turn starts (step 1 ends with a turn check) |
| G + 7 | −238 | turn, facing left |
| G + 11 | −456 | walking starts (end of the turn, v < 512) |
| G + 15 | −584 | running starts (run check at the end of step 2) |

#### Example

From standing still, Right held from frame 0 (speeds as in "Worked
values"):

| Frame | `vx` (1/256) | Mode | Counter after the frame | Step ended |
|---|---|---|---|---|
| 0 | 64 | walking starts | 256 | |
| 1 | 120 | walking | 160 | |
| 2 | 169 | walking | 64 | |
| 3 | 211 | walking | 224 | 1 (turn check) |
| 4 | 248 | walking | 128 | |
| 5 | 281 | walking | 32 | |
| 6 | 309 | walking | 192 | 2 (run check) |
| 7 | 334 | walking | 96 | |
| 8 | 356 | walking | 251 | 3 (turn check) |

The rate is 96 up to frame 7 and 101 on frame 8. Left held from frame 3
gives a turn on frame 3. Left held from frame 4 gives a turn on frame 8,
and facing changes on frame 12. Left held from frame 9 on: the speed falls (248, 153, 70,
−2, −65, −120, −169, −211), the rate stays 96, steps 4 and 5 end on
frames 11 and 14 without a turn check, and step 6 ends on frame 16 with
a turn check, so the turn starts on frame 16.

#### Evidence

All by inputs, with the lead character on flat ground, from standing
still. Each run was checked from its first standing frame after its last
landing, using only each frame's inputs and `vx`.

- 603 runs, 285918 frames, 5541 turn starts, 0 mismatches in mode, turn
  start or facing:
  - 320 runs of random Right, Left and no-direction segments (1 to 40
    frames), each with Action either never pressed or held from mid-air
    through the landing. In one set of 160, runs holding Action released
    it for good at a random frame.
  - 123 scripted runs: reversals after walking or running and then
    releasing for 0 to 40 frames, opposite-direction taps of 1 to 3
    frames at 20 points of a walk, and Action released 0 to 14 frames
    after running starts.
  - 160 further random runs of both kinds, checked only after the rules
    were fixed.
- Walking from standing still and holding the opposite direction from
  frame 1, 2, … 129 on: the turn starts on the frame these rules give
  with `vx` from the speed rule, in all 129 cases.

Landings and roll endings ("After a landing", "After a roll"), all by
inputs with the lead character on flat ground, 0 mismatches in mode,
turn start or facing:

- 315 runs of random input (Right, Left or no direction, Action on or
  off, Jump held for the first frames of a segment, segments of 1 to 40
  frames), 120 168 frames, each checked from its first standing frame
  using only each frame's inputs and `vx`, with the landing variant
  (above) taken from the original for each landing. 159 of them, with
  other jump and Action rates, were checked only after the rules were
  fixed. They hold 2 916 landings that end in a mode (1 092 running,
  924 walking, 900 standing), 528 stops and 570 roll carry-ons; among
  the landings ending without a jump or roll, the mode started on
  L + 7 in 1 531, L + 9 in 201 and L + 10 in 244, and with Action and a
  direction held through the landing running started on L + 1 in 777
  and on L in 425.
- 2 812 scripted standing jumps (Jump held 1 to 30 frames; nothing,
  Right, Action, or Action and Right held in the air; then nothing,
  Right, Left, Action, Action with either direction, or a one-frame
  Action press, from 3 frames before L to L + 10), each checked to
  L + 24 or until a roll started. Running on L + 1 was seen from `vx`
  17/256, and on L + 7 from 96/256.
- 806 of the landings after jumps measured for `jump-input.md` ("The
  switch frame M"), with M from 0 to 6 frames after L: the first frame
  in a mode was M (running) with Action and a direction held, otherwise
  K, on every one. The first two sets above took each landing's timing
  from the original; these check that M, and so K, follows from the
  jump.

Falls and bounces ("After a fall or a bounce"), all by inputs with the
lead character, each checked using only each frame's inputs and `vx`,
0 mismatches in mode, turn start or facing:

- 400 runs at a ledge without a face 176 px above flat floor: walking,
  rolling or running off it, then random input (Right, Left or no
  direction, Action on or off, segments of 1 to 20 frames) through the
  fall and 60 frames after landing; half of the falls made shorter or
  longer by moving the player up or down once partway through (falls of
  1 to 45 frames). 59 164 frames in a mode, 12 534 of them airborne:
  433 walk-offs (342 walking, 83 running, 6 in a turn, 2 standing), and
  376 mode changes and 122 facing changes in the air.
- At three ledges with a steep face at the top: 300 runs of the same
  kind, and 2 580 scripted falls (walking or running off; Right, Left,
  nothing, Action alone or with a direction in the air; then one input
  from a frame between G − 12 and G + 11; some falls shortened or
  lengthened as above; falls of 4 to 47 frames). Every landing frame was
  checked in 1 250 of the scripted falls, and 622 falls in all reached
  the mode start on G + 11 away from the face's foot and without a
  roll. With Action and forward held on
  G + 11, walking started at v up to 510 and running from v 516.
- 240 runs of random input after bounces off a small walker, and 216
  scripted bounces (Jump held 1, 12 or 30 frames; six inputs in the air
  and one of six from G or G + 1): 453 mode starts on G (151 running,
  70 turns, 102 walking, 130 standing).
- 335 of the runs above rolled off a ledge; the 147 rolls that ended on
  flat floor away from walls ended as "After a roll" says.

Rules that fail on the same runs: no stride cycle in the air after a
faceless walk-off (277 runs mismatch), 10 or 12 landing frames after a
fall from a face (722 and 562 cases), no speed limit or a limit of 768
for running on G + 11 (63 and 184), and a bounce pick in which the
opposite direction always starts a turn (88).

#### Not covered (`title-1` modes)

- Which mode the player is in, and where the stride cycle starts, after
  crouching, the Down + Action ground action, being hit or a swap.
  Landings after jumps, falls and bounces, and the ends of rolls on flat
  ground, are in "After a landing", "After a roll" and "After a fall or
  a bounce".
- After a fall from a steep face: landing on the face itself (the
  player then goes on down it), landing at or next to its foot (the
  rise there was seen to stop the player and change facing on landing
  frames), speed next to the foot, a fall from a face that started
  without walking or running down it, and Action presses made in the
  air or on G.
- Landing on uneven floor after any of these falls, and bounces off
  anything other than a small walker (`walker-contact.md`).
- Left and Right held together, walls, carrying, and the second
  character. Down held on or after the landing frame starts a crouch
  from L + 2 ("Crouching"); the mode after a crouch is the first item
  above. On 1-in-4 slopes this section applies unchanged
  (`slopes.md`, "Turning around on a slope"); other slopes are not
  covered.

## Crouching

This section is `title-1` only; `title-2` crouching is different (see
"In `title-2`").

Holding Down while grounded (without an Action press on that frame; see
`roll-input.md`) makes the player crouch. During a roll and the stop
after it Down makes no difference (`roll-input.md`, "Down during a
roll"): a crouch starts no earlier than D + 18 after a roll that ends in
a stop, or D + 4 after one that carries on into running (D as in
`roll-input.md`, not the crouch's D below). Call the first frame Down is
held **D**:

- On D, the target speed is still the one in the table above.
- The crouch lasts as long as Down is held, and **at least 13 frames**,
  D to D + 12, even if Down is released sooner. On every crouch frame
  after D the target is 0 whatever else is held, so the player slows
  down under the speed rule and can't start moving.
- Call the first frame after the crouch **U**: the frame Down is
  released, or D + 13 if Down was released before that. The target stays
  0 on frames U to U + 9. From U + 10 the target is the normal one again.
- Down held again on any frame from U to U + 9 starts a new crouch with
  that frame as its D, except that the target is 0 on that D as well.
  The new crouch again lasts at least 13 frames and is followed by its
  own U to U + 9.
- **After a landing**, Down held on the landing frame L or on L + 1
  starts no crouch: D is the first frame from L + 2 on with Down held
  (`air-control.md`, "Action and Down around take-off and landing").
- **Jumping from a crouch**: a Jump press on any crouch frame, or on U to
  U + 9, starts a jump as on any grounded frame (`jump-input.md`,
  "Crouching"). On the press frame the target is still the crouch's (0,
  or on D the one in the table); from the next frame the jump's
  (`air-control.md`). A Jump press on the first frame Down is held
  starts no crouch.

Example: walking at +2 with Right held, Down held on frames 40 to 69 and
Right on throughout. `vx` is 2 on frame 40, 1.75 on 41, 1.53125 on 42, and
keeps falling while Down is held and after it is released, to 0.02734375
on 76 and 0 on 77. It stays 0 to frame 79 and is 0.25 again on frame 80.

Example of a short press: walking right with Right held throughout and
Down held only on frame 30. Frame 30 is D: `vx` still rises, to
500/256. The crouch lasts to frame 42 (D + 12) and `vx` falls
under the target 0 (438/256 on frame 31, 384/256 on 32, … 104/256 on
42). U is frame 43 (D + 13): the target stays 0 to frame 52, and from
frame 53 the player speeds up again. A press of 5 frames (30 to 34)
gives exactly the same speeds.

Measured on flat floor and on 1-in-4 slopes with Down held for 1 to 40
frames, from rest, from walking speed and from running speed, with Right,
Left or neither held, with turns, and with Down pressed again 0 to
12 frames after U: 5,713 runs, every frame as above (742 times Down was
pressed again between U and U + 9). Crouching on slopes is in `slopes.md`.

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
frame also grounded. The frames after a landing are in "After a landing
(`title-2`)" below.

As in `title-1`, an Action **press** starts a roll; this section covers
Action that is already held. Speed during a roll and the stop after it
are in `roll-speed.md` ("In `title-2`"). From the first frame of normal
movement after a roll (D + 1 after a roll that carries on into running,
D + 15 after a stop) the rules of this section apply.

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

- On a grounded frame that is not part of a turn, a crouch, the 11
  frames after a crouch, or a landing ("After a landing (`title-2`)"),
  holding the net direction opposite to facing
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
- After a roll, a turn starts on the first frame of normal movement
  (D + 1 or D + 15, as above) if the opposite direction is held then.

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

### After a landing (`title-2`)

Measured on flat ground with the lead character, after standing,
walking and running jumps and after walking and running off a ledge.
**A** is the landing frame (the row carrying `land`). **S** is A after
a jump, and A + 1 after a fall that didn't come from a jump. This is the
same one-frame shift as for jumps after a fall (`walk-off.md`, "Jumping
on landing after a fall (`title-2`)").

The player is **landing** on S and on the frames after it:

- **9 frames**, S to S + 8, if Action and a net direction (Left or Right
  alone) are held on S;
- **18 frames**, S to S + 17, otherwise.

Only what is held on S decides this. Speed, the height of the jump or
fall, and what is held before or after S make no difference. **E** is the
first frame after the landing: S + 9 or S + 18.

- **On S** (and, after a fall, on A before it) the target and facing
  follow the air rules of `air-control.md` ("In `title-2`"): Down doesn't
  make the target 0, and Left or Right held turns the player on that
  frame. After a jump, S is the landing frame `air-control.md` already
  describes.
- **From S + 1 to E − 1** the target is the grounded target of "Target
  speed (`title-2`)". As always it depends on the previous frame, which
  on S + 1 is S. **Facing doesn't change and no turn starts**, whatever
  is held. So holding the opposite direction slows the player under the
  speed rule and then moves them backward, still facing the old way.
- **On E** the usual rules apply again. If the direction opposite to
  facing is held on E, a turn starts on E ("Turns (`title-2`)"), and
  facing changes on E + 4.
- A crouch, a roll or a jump can start on any frame from S + 1, under
  its own rules ("Crouching (`title-2`)", `roll-input.md` and
  `jump-input.md`, "In `title-2`"), and ends the landing. None can
  start on S: Down held on S starts no crouch. After a fall, an Action
  press on A + 1, or one made in the air on A − 4 to A, held through
  A + 2, starts its roll on A + 2 (after a jump this is A + 1,
  `roll-input.md`).

Example: walking right at +2.25, a jump with Jump and Right held for 20
frames, Right held in the air, then Left held alone from A + 1 (S = A,
18 frames, E = A + 18):

| Frame | `vx` (1/256) | Facing | Note |
|---|---|---|---|
| A | 576 | right | lands |
| A + 1 to A + 5 | 432, 306, 196, 100, 16 | right | |
| A + 6 to A + 8 | −58, −122, −178 | right | moving backward |
| A + 17 | −453 | right | last landing frame |
| A + 18 | −468 | right | E: turn starts |
| A + 22 | −511 | left | facing changes |

`x` is 4.1015625 px further right on A + 5 than on A, then decreases.
In a standing jump with Action and Right held from take-off through A
(9 frames, E = A + 9) and Left alone from A + 1, `vx` is 648 on A + 1
(target −864, since Action and a direction were held on A), then 495,
362, 245, …, −93 on A + 8; the turn starts on A + 9 and facing changes
on A + 13.

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
- Landings ("After a landing (`title-2`)"): swept runs from 8 kinds of
  jump (standing, walking and running, right and left, with and without
  Action held in the air) and 4 kinds of walk-off, each with 3 to 12
  different inputs switched on at every frame from A − 4 to A + 23; 30
  runs with Down held for one frame on A, A + 1 or A + 2 of a fall; and
  1 200 runs of random input in the air and after the landing, in four
  independent sets. 5 592 landings: 4 221 after jumps (1 011 of them 9
  frames) and 1 371 after falls (413 of them 9 frames). On 62 702 frames
  from S + 1 to E − 1 facing didn't change and no turn started, 0
  exceptions; the opposite direction was held on 4 483 of them. On 3 620
  E frames (425 with the opposite direction held) a turn started exactly
  as the rule says, 0 exceptions. Grounded target from S + 1: 66 322
  frames, 0 mismatches. Air target and facing on S after a fall: 1 371
  frames, 0 mismatches. Landings of 17 or 19 frames instead of 18, 8 or
  10 instead of 9, or S = A after a fall, each give 58 to 543
  mismatched frames. Left out: falls from rolling off a ledge (162),
  falls while crouched (23) and jumps that rose up through the surface
  they landed on (5).

### Not covered in `title-2`

- The Down + Action ground action. Rolls are in `roll-input.md` and
  `roll-speed.md` ("In `title-2`").
- Walls other than steep faces, and the second character. Slopes are
  in `slopes.md` ("In `title-2`"), steep faces (being blocked by a
  rise, and going down a face) in `steep-faces.md` ("In `title-2`").
  Air control and the landing frame are in `air-control.md` ("In
  `title-2`"), the frames after it in "After a landing (`title-2`)".
- After a landing: falls from rolling off a ledge, falls while crouched,
  landings on a platform after rising up through it, landings on slopes
  and faces, and what follows a crouch that starts during the landing.

## Not covered

This list is for `title-1`; `title-2` has its own list above.

- When the walking and running modes start after actions other than
  jumps and rolls (see "Not covered (`title-1` modes)").
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
