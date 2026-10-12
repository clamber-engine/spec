# Changelog

Spec releases are tagged `spec-vMAJOR.MINOR.PATCH`. The engine pins one.

## Unreleased

## spec-v0.25.0

Coverage work; answers no `spec` issue.

- Behavior: new `behavior/ground-action.md`, for the ground action the
  first character starts by pressing `A` with `D` held on the press
  frame while grounded (not from the air; a press remembered through
  landing still rolls). It lasts 59 frames (P to P + 58), with a
  direction set only by `L`/`R` held on P, and a speed that restarts
  from 0 on P and follows the `ground-speed.md` rule toward
  direction × 3. Covers how it ends (speed kept on E, normal rules from
  E + 1), what directions, Action, Swap and Jump presses do during it
  (a Jump press ends it early), the two-frame stop when it knocks out
  an enemy, trace rows, worked examples and a "Not covered" list.
- Traces: new state `ground_action` in `traces/VOCABULARY.md`.
- `roll-input.md`: corrects the placeholder that said the player stops
  and doesn't move after a Down + Action press; with a direction held
  the player moves. Also states that Down makes no difference for the
  second character, and that a press remembered through landing rolls
  even with Down held. Its "Not covered" item, and the one in
  `ground-speed.md`, point to the new file.

## spec-v0.24.0

Coverage work; answers no `spec` issue.

- Behavior: new `behavior/swap.md`, for what happens when the player
  presses `S` with the other character present. A press on Q starts a
  swap unless there is no other character, a swap is still going on (up
  to E), the player was in the air or landing on Q − 1, in a roll or
  the stop after it, crouching (a window after the first Down frame and
  after the release, per character), or the two characters' heights
  differ by 8 px or more. Presses on a jump's press frame or the two
  frames after it cancel the jump. Both characters stay still, then
  each moves for 32 frames to where the other started (horizontal steps
  of 1/32 of the whole-pixel distance; a vertical rule toward the
  other's whole-pixel height that ignores the floor; facing unchanged),
  with a frame table per character and when control returns. Trace
  rows, worked examples with made-up numbers and a "Not covered" list.
- `second-character.md`: its "Not covered" item about the swap points
  to the new file.

## spec-v0.23.0

Coverage work; answers no `spec` issue.

- Behavior: new `behavior/walker-contact.md`, for when touching a walker
  hurts the player and when it gives a bounce. Offsets are taken from
  whole-pixel positions (I horizontal, U vertical). A hit on frame H is
  decided from the positions at the end of H − 1: I within a hurt range
  that depends on the walker's kind (small or large), the player's
  facing and the walker's heading, and U within a vertical reach for
  the ground, early in a jump (to T + 5) or later. A bounce on frame B
  needs the player airborne and not landing, `vy` greater than −1.25,
  and I and U within wider bounce ranges, using the player's position
  at the end of B and the walker's at the end of B − 1; a hit takes
  priority. Synthetic examples and a "Not covered" list.
- Behavior: new `behavior/second-character.md`, the second character's
  movement when under control: the same rules as the first character
  with its own values (walking target 2.25, running target 3.375,
  take-off `vy` −8.3515625, held-jump gravity 0.3515625, 13 frames of
  crouch recovery), with worked ground speeds, the jump arc,
  `apex_height`/`airtime` per hold length and jump distances.
- `bounce.md`, `knock-out.md`, `walker.md`, `losing-a-character.md`,
  `ground-speed.md`, `jump-arc.md`, `air-control.md`: intros and "Not
  covered" lists point to the new files.

## spec-v0.22.0

Coverage work; answers no `spec` issue.

- Behavior: new `behavior/losing-a-character.md`, for a hit taken while
  the other character is with the player. On the hit frame the hit
  character doesn't move, gets `vy` −8 and faces right; input is ignored
  and nothing pauses. It rises and falls straight (usual gravity, or the
  jump's lighter gravity if a held jump was in progress), lands, then
  runs right at 4 px per frame out of view. The other character waits
  until H + 36, then closes on the hit spot one axis at a time (gap ÷ 16,
  at least 3), is placed half a pixel off the spot and hops with fixed
  per-frame moves; Left/Right steer from T + 1 and jumping follows
  `jump-input.md` from the hop's landing. Trace rows (`hurt`, then
  `swap`), synthetic worked examples and a "Not covered" list.
- `knock-out.md`: its "Not covered" item for two-character hits points to
  the new file.

## spec-v0.21.0

Coverage work; answers no `spec` issue.

- Behavior: new `behavior/platforms-from-below.md`, for a jumping player
  meeting ground above their head. Nothing overhead stops a rising
  player: a platform has a top per column and an underside, and a player
  who rises from below the underside into the layer between the two is
  inside it. While inside, the platform neither blocks nor steps the
  player sideways; a rising frame that starts 2 px or less below the top
  (exact to 1/32 px) lifts the player onto it by whole pixels, as in
  `air-walls.md`; a falling frame never lands on the top, so the player
  falls back out through the underside. Moving sideways into a platform
  from outside uses the column's top as the floor for `air-walls.md`
  when the feet are at or above the underside, and ignores the platform
  below it. Falling onto the top uses `landing.md`, except that a frame
  ending below the underside isn't caught. Nothing new in the trace.
  Measured cases, synthetic examples and a "Not covered" list.
- `air-control.md`, `air-walls.md`: their "Not covered" ceiling items
  point to the new file.

## spec-v0.20.0

Answers `spec` #10.

- New `TITLES.md`: engine-neutral title ids `title-N`, numbered by each
  title's position in the series in order of first release; ids are never
  renamed or reused. Each measured title was measured on one 60 Hz
  release; differences between releases are not covered. So far only
  `title-1` has been measured, and it covers every behavior file and
  reference trace.
- `traces/FORMAT.md`: new header line `# title: <id>`, required once in
  reference traces. Engine traces should carry it, naming the title whose
  preset the run used; the comparison picks the engine preset for the
  reference trace's title. If both traces name a title and the ids differ,
  the comparison is not run and is reported as a title mismatch. An engine
  trace without the line is compared as before; the format stays v1.
- `behavior/README.md`: every behavior file has a `Titles:` line under its
  heading; values hold only for the titles listed. All behavior files are
  `title-1`.
- Both reference traces gain `# title: title-1`; their rows are unchanged.
- `README.md`: row for `TITLES.md`.

## spec-v0.19.0

Coverage work; answers no `spec` issue.

- Behavior: new `behavior/air-walls.md`, for a jumping or falling player
  moving sideways into higher ground. On an airborne frame whose move
  would take `x` into another column, that column's floor is compared
  with the feet before the vertical move: a face column higher by any
  amount, or another column more than 8 px higher (exact to 1/256 px),
  blocks the player (`x` drops to the whole pixel, `vx` 0, vertical
  motion and landing unchanged, still airborne, no event). Another
  column 8 px or less higher is stepped onto: a rising player is lifted
  by whole pixels to that floor and then moves by `vy`; a falling player
  lands on it at once. Synthetic worked examples, a table of measured
  cases and a "Not covered" list.
- `air-control.md`, `steep-faces.md`: "Not covered" entries for walls
  and faces met in the air now point to `air-walls.md`.

## spec-v0.18.0

Coverage work; answers no `spec` issue.

- Behavior: new `behavior/walker.md`, for the motion of the simplest
  ground enemy. Walkers head at 1 px per frame, easing `vx` toward the
  target by one eighth of the difference (rounded toward zero to 1/256,
  snapping once the step would be 0); they follow flat floor and 1-in-4
  slopes. At the end of a patrol range (level data) the target flips
  without stopping and facing changes 4 frames later; at a barrier the
  walker is stopped at it with `vx` 0, then speeds up the other way,
  facing changing 5 frames after the stop. Walkers freeze on frames
  H + 1 to H + 10 of a player knock-out (except the one that caused it).
  A bounced-on walker is launched at `vy` −6, frozen on B + 1 and B + 2,
  then falls with 0.4375 gravity capped at 8 through the floor and is
  removed. Synthetic worked examples and a "Not covered" list.

## spec-v0.17.0

Coverage work; answers no `spec` issue.

- Behavior: new `behavior/carrying.md`, for picking up, carrying,
  throwing and putting down objects. A pick-up starts on moving into an
  object with Action held, or on an Action press while standing within
  reach (no roll then); it lasts 15 frames, target speed 0 for the first
  10. Carrying uses the Action-held ground targets; Down and Up have no
  effect, and jumps take off on P + 2. Letting go of Action throws
  (43 frames, target 0 for the first 22); the object leaves on Q + 21 at
  16 px per frame forward and 1 px per frame down, then eases toward
  3.5 px per frame with 0.4375 gravity capped at 8. Down held on letting
  go on the ground puts the object down 16 px ahead (14 frames). Rules
  for Jump and Action during a throw and for picking up again. Synthetic
  worked examples and a "Not covered" list.
- `roll-input.md`: an Action press within reach of a carriable object
  picks it up instead of rolling.
- `slopes.md` points to the new spec.

## spec-v0.16.0

Coverage work; answers no `spec` issue.

- Behavior: new `behavior/steep-faces.md`, for grounded movement at
  ground too steep to walk on: steep faces (4 px per column) and a 64 px
  vertical step. A move into a column of a rising face or past a step up
  is blocked (`x` drops its fraction, `vx` 0). From `vx` 0 a direction
  toward a rise is ignored when the column 5 ahead is 9 px or more
  higher. On a face the target is 1.5 px per frame downhill, plus the
  walking target if downhill is held. Drops of 8 px or less in a frame
  are followed, and 11 px or more starts the `walk-off.md` leave frame.
  For two frames after leaving a face onto floor, input back up the face
  is ignored. Synthetic worked examples and a "Not covered" list.
- `walk-off.md` points to the new spec for which drops are followed on
  steep faces.

## spec-v0.15.0

Coverage work; answers no `spec` issue.

- Behavior: new `behavior/slopes.md`, for 1-in-4 slopes. Floor height
  per 1-px column; while grounded `y` is the floor height of the column
  under `x` after the horizontal move (whole pixels, `vy` 0, `ground` 1,
  no `leave_ground` going downhill at the speeds measured). Walking,
  running, stopping and normal and fast rolls are as on flat floor. The
  take-off frame follows the floor; landing uses the column under the
  new `x`. Synthetic worked examples and a "Not covered" list.
- `ground-speed.md`, `jump-arc.md`, `landing.md` and `roll-speed.md`
  point to the new spec.

## spec-v0.14.0

Coverage work; answers no `spec` issue.

- Behavior: new `behavior/knock-out.md`. Taking damage with no other
  character: no motion on the hit frame and input ignored from then on;
  11 frozen frames with a 32-frame whole-game pause (no trace rows)
  inside them; a knock-back flight away from the facing direction with
  its own launch speed, gravity and fall cap, two rebounds and a stop on
  the third landing; then lying still until the level ends. Trace fields
  over the sequence and worked values for a hit on flat ground.

## spec-v0.13.0

Coverage work; answers no `spec` issue.

- Behavior: new `behavior/roll-speed.md`. During a roll the speed moves
  toward a roll target (slow 1, normal 3 or fast 4 px per frame, in the
  facing direction) by the ground speed rule. Fast if Action and a
  direction were held within the last 15 frames (14 with no direction on
  the press), normal if a direction is held on the press, else slow.
  Steering: holding the roll's direction makes a slow roll normal; the
  opposite direction reverses the roll and facing at once. Speed on the
  frames after the roll, and a fast roll always ends in a stop.
- `roll-input.md`: fast rolls end in the stop even with Action held; the
  slowdown after a roll is the ground speed rule. `ground-speed.md`
  points to the new spec.

## spec-v0.12.0

Coverage work; answers no `spec` issue.

- Traces: reference trace `reference/walk-off-ledge.trace` for the
  `walk-off-ledge` scenario (scenario unchanged). Recorded on a square
  edge 56–58 px ahead of the start with a 176 px drop to flat ground.

## spec-v0.11.0

Coverage work; answers no `spec` issue.

- Behavior: new `behavior/air-control.md`. In the air (and on the
  take-off and landing frames) horizontal speed follows the same target
  speeds and rule as on flat ground. During a jump, facing follows the
  held direction on the same frame with no turn delay; during a fall
  after walking off a ledge it doesn't change. Table of jump distances.

## spec-v0.10.0

Coverage work; answers no `spec` issue.

- Behavior: new `behavior/bounce.md`. Bouncing off an enemy: after
  normal motion on the bounce frame `vy` becomes −7.125; the next two
  frames are frozen; then gravity 0.28125 while jump is held without a
  break from the third frame after the bounce, 0.4375 otherwise. Table
  of `bounce_height` by hold length.

## spec-v0.9.0

Coverage work; answers no `spec` issue.

- Behavior: new `behavior/ground-speed.md`. Grounded horizontal speed
  moves toward a target (0, ±2 walking, ±3 with Action held) by one eighth
  of the gap per frame in 1/256 px units, rounded toward zero, snapping to
  the target when the step would be 0. Same rule for starting, stopping,
  walk/run changes and reversing. Facing changes 4 frames into an 8-frame
  turn. Crouching (Down) targets 0 until 10 frames after release.

## spec-v0.8.0

Coverage work; answers no `spec` issue.

- Behavior: new `behavior/jump-arc.md`. A jump takes off with `vy` −7;
  each airborne frame after take-off `vy` grows by 0.28125 while jump has
  been held without a break since the press, by 0.4375 otherwise, capped
  at 8. Tables of apex height and airtime by how long jump is held.

## spec-v0.7.0

Coverage work; answers no `spec` issue.

- Behavior: new `behavior/walk-off.md`. Walking off a ledge: the player
  drops 3.4375 px on the first airborne frame, then falls with `vy`
  starting at 1/256 and growing by 0.4375 per frame up to 8; landing as
  in `landing.md`, which now covers these falls.

## spec-v0.6.0

Coverage work; answers no `spec` issue.

- Behavior: new `behavior/landing.md`. A falling player lands on the
  first frame its motion takes it less than 5 px above the floor, and is
  placed on the floor with `vy` 0.
- Scenarios: `run-jump-flat` shortened to 120 frames and asks for 360 px
  of clear ground; first reference trace `traces/reference/run-jump-flat.trace`.
- Vocabulary: `roll_end` goes on the first frame after the roll.

## spec-v0.5.0

- Behavior: new `behavior/roll-input.md`. When an Action press starts a
  roll (any grounded press without Down; not in the air, except a late
  press held through landing), how a roll ends depending on whether Action
  stays held, and when the next roll can start. Answers spec#9.

## spec-v0.4.0

- Behavior: new `behavior/jump-input.md`. Covers jump timing on the
  ground and during a roll, a defined landing lockout (earliest jump on
  L + 3), and buffered presses held through landing. Answers spec#8.

## spec-v0.3.0

- Traces: every row has 10 fields (trailing tab when `events` is empty;
  readers also accept 9); `frame` starts at 0 with no gaps.
  Answers spec#2, spec#3.
- Metrics: a missing closing `land` makes `apex_height`, `airtime` and
  `bounce_height` missing; height windows include the `land` frame.
  Answers spec#4.
- Curves: fail if the aligning event is missing from one trace, pass if
  missing from both. Answers spec#5.
- Scenarios: the `# clamber-scenario v1` line is now **required**; unknown
  or repeated header keys and inputs at or past `frames` are errors;
  buttons may be listed in any order, repeats are errors. Answers spec#6,
  spec#7.

## spec-v0.2.0

- Process for autonomous agents: governance roles, `COMMUNICATION.md`,
  `GATEKEEPING.md`, issue templates, comment firewall.

## spec-v0.1.0

- Governance and review process.
- v1 formats for scenarios, traces, vocabulary and metrics.
- Example scenarios `run-jump-flat` and `walk-off-ledge`.
