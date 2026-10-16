# Changelog

Spec releases are tagged `spec-vMAJOR.MINOR.PATCH`. The engine pins one.

## Unreleased

Coverage (first level, `title-1`). Answers no open `spec` issue.

- `behavior/dimming.md` (new): near the first level's right end the
  background's colours change in 32 steps, one every 8 frames, started
  by the level's class 8 entry entering the window; step 1 on the first
  step frame from T + 4 (cycle placement not specified); the colour of
  every background colour at each step, from its palette set value.
  Sprite colours not covered.
- `formats/placements.md`: class 8 in the first level starts the
  dimming; "When placed objects appear" and "Not covered" updated.
- `formats/graphics.md`: the darker colours near the first level's end
  are the dimming; "Not covered" updated.

## spec-v0.73.0

Coverage (first level, `title-1`). Answers no open `spec` issue.

- `formats/locators.md`: `title-1` rows for the first level's grid and
  type table (`level-ground.md`), packed tile data, palette set and
  picture table (`graphics.md`), its placement list (`placements.md`),
  and the frame table (`sprites.md`).
- `formats/level-ground.md`: new section "The first level's data": the
  grid's 1 310 columns, the first level in columns 0 to 183, and
  `N` = 234. "Not covered" updated.
- `formats/graphics.md`: "The first level's data" points to the rows and
  corrects the colours: rows 1 to 7 hold the palette set's colours over
  most of the level but other, darker colours near its right end (view
  at level x 4 774 and more), not stored as palette rows. "Not
  covered" updated.
- `formats/sprites.md`, `formats/placements.md`: point to the rows;
  "Not covered" updated.

## spec-v0.72.0

Coverage (first level, `title-1`), using the kind fingerprints of
spec #23. Answers no open `spec` issue.

- `formats/placements.md`: new section "Kinds". Which kind of object a
  class 1 or class 2 entry places, told by fingerprint: for each bank
  block, the SHA-256 of the 64 image bytes at the reference's position
  in that block, looked up in the image's rows. Kind names
  `small-walker`, `large-walker`, `thrower`, `ball`, `bunch`,
  `letter-1` to `letter-4`, `barrel`, `holder`, `end-marker`. 15 of the
  first level's 37 references (28 entries) have rows. "Not covered"
  updated. New vectors `vectors/placements/kinds-*.tsv`.
- `formats/locators.md`: `title-1` kind fingerprint rows for placement
  references.
- `behavior/thrower.md`: "Not covered" item answered.

## spec-v0.71.1

Answers https://github.com/clamber-engine/spec/issues/28.

- `behavior/falling-out.md`: the fall-out frame Z moves with the input
  held on Z, as any falling frame: directions on Z set Z's target speed,
  `vx`, facing and `x` by `air-control.md`. Jump, Action, Up, Down and
  the swap press on Z do nothing, as on Z − 1. Input is ignored from
  Z + 1 on (was "from Z on"). Example added.

## spec-v0.71.0

Answers https://github.com/clamber-engine/spec/issues/23. Coverage
(first level, `title-1`).

- `REVIEW.md`: new section "The second exception: kind fingerprints"
  (director decision, spec #23). Object kinds may be told apart by a
  SHA-256 fingerprint over at least 32 bytes that can't be inverted,
  listed as fingerprint → engine-neutral kind rows in
  `formats/locators.md`. No reference values or bytes are published.
- `GOVERNANCE.md`: director process changes reach the spec as pull
  requests on `spec-staging`. `formats/README.md`: pointer.

- `behavior/ball-contact.md`: new (`title-1`). When a thrower's ball
  hurts the player: with the positions at the end of frame F, after the
  ball's move, the offsets I (across) and A (ball above the player) in
  the range for the player's pose on F hurt on F + 1, never on a frame
  the ball bounces or stops ignoring floors. Ranges for the upright,
  airborne, rolling, crouching and fidget poses, facing right and left.
  The ball turns in a 24-step cycle that moves its reach by 2 px across
  and up; the step before the frame counts. Which pose the player has
  when, as measured. The ball is unaffected, contact never bounces the
  player, only the character under control is hurt, protection holds.
  Examples; "Not covered" lists the rest.
- `behavior/thrower.md`, `behavior/knock-out.md`,
  `behavior/losing-a-character.md`: pointers.

## spec-v0.70.0

Answers https://github.com/clamber-engine/spec/issues/26.

- `behavior/holder-breaking.md`: new (`title-1`). When an object holding
  the missing character breaks (frame S) and where it is then. Thrown, it
  moves as an object holding nothing and breaks: against a wall (also a
  move passing a face column), with its `x` unchanged but its vertical
  move done; at a barrier the same way; on its first touch of the floor
  (always falling at least 1 px per frame then), at `y` = `F`; outside
  the picture window of `thrower.md` instead of being removed; on a
  walker by the ranges of `thrown-objects.md`; on the thrower and its
  balls by measured ranges. Never on the lead. During a throw it breaks
  on a walker on Q + 1 to Q + 20; carried, put down or lying on the floor
  it doesn't break; carried through a barrier it falls away and nobody
  joins. Walkers and the thrower don't move from S + 1 to S + 33; a hit
  walker's next step is on S + 34, a hit thrower rises from S + 35.
  Examples; "Not covered" lists the rest.
- `behavior/following.md`, `behavior/thrown-objects.md`,
  `behavior/carrying.md`: pointers.

## spec-v0.69.0

Answers https://github.com/clamber-engine/spec/issues/24.

- `behavior/ground-action-enemies.md`: new (`title-1`). The ground
  action knocks out walkers only on its attack frames, the 18th, 26th
  and 32nd frames of the action counted without pauses, with any
  direction including 0. A walker is knocked out when the offset at the
  end of the frame is within a range that reaches 62 px ahead of the
  player and 10 px behind (small walker; 3 px more each side for the
  large walker). Each knock-out pauses the player and every walker for
  two frames and makes the action 2 frames longer. The walker then moves
  as a bounced-on walker, and the view stays still on the knock-out frame
  and the next. The player is hurt during the action by the usual hurt
  rule, except by a walker it knocks out, and not on the 3 frames after
  a pause starts. The thrower is knocked out the same way and rises at
  5 px per frame. Synthetic example; "Not covered" lists the rest.
- `behavior/ground-action.md`: "Enemies" now points to the new file; the
  action lasts 2 frames longer per pause.
- `behavior/view.md`, `behavior/view-vertical.md`: the view stays still
  on the frame of a ground-action knock-out and the next.
- `behavior/rolling-into-walkers.md`, `behavior/thrower.md`: pointers.

## spec-v0.68.0

Answers https://github.com/clamber-engine/spec/issues/19 (in part).

- `behavior/thrower.md`: new (`title-1`). A thrower sits still where it
  is placed, appears and is removed by the walker window, and throws a
  ball up, right and left on a fixed 173-frame timetable from the frame
  it appears (up on +2, right on +33, left on +144), whatever the player
  does. A ball starts 8 px above the thrower at 7 px per frame upward
  and 3.125 px per frame sideways or none; it falls at 0.4375 px per
  frame per frame up to 8, bounces back up at 5 px per frame on a floor
  within reach, then ignores floors until it is more than 47 px below
  that floor; it is removed once outside the window (31 px left of and
  above the picture, 32 px right of and below it), but not on a frame
  on which it bounced or stopped ignoring floors. Balls placed directly
  in a level. Synthetic example; "Not covered" lists contacts with the
  player and others.

## spec-v0.67.0

Answers https://github.com/clamber-engine/spec/issues/19 (in part).

- `behavior/thrown-objects.md`: new section "Hitting walkers"
  (`title-1`). A thrown object defeats a walker when the walker is within
  a range of offsets that depends on the walker's size and facing and
  the object's direction, larger (25 px further back, 33 px higher) for
  the first 16 frames out of the hands; the object and the player carry
  on unaffected; the walker is knocked back the way the object came at
  4 px per frame, easing toward 2, and falls from 6 px per frame upward
  with no frozen frames; a patrol turn can still happen on the frame
  after the hit. Synthetic example; "Not covered" updated.
- `behavior/walker.md`: "Not covered" points to it.

## spec-v0.66.0

Answers https://github.com/clamber-engine/spec/issues/19 (in part).

- `behavior/thrown-objects.md`: new. A thrown object that holds nothing
  (`title-1`), after the flight in `carrying.md`: touching the floor
  (moving down and less than 5 px above it), bouncing at half the
  speed while it falls at 1 px per frame or more, then rolling on at
  3.5 px per frame along the floor, breaking when it moves into a face
  of solid ground higher than itself (the player isn't held up),
  removal once 385 px or more right of the view as for walkers, and not
  coming back.
- `behavior/carrying.md`: "The object" and "Not covered" point to it.

## spec-v0.65.0

Answers https://github.com/clamber-engine/spec/issues/19 (in part).

- `behavior/level-end.md`: new. Finishing a level (`title-1`): the end
  marker placed by the level's placement list, when the lead passes it
  (Right held, on the ground, at or right of it, feet within 4 px of
  it), the walk-out that follows (2 px per frame, rising 1/8 px per
  frame, through the scenery, inputs ignored, the view still), the
  other character following, and the fade to black that starts once
  the lead is 240 px right of the picture's left edge and ends the
  level 28 frames later.

## spec-v0.64.0

Answers https://github.com/clamber-engine/spec/issues/20 (in part).

- `formats/songs.md`: new. What song data says (`title-1`, all 27
  songs): the header (track addresses, tempo), steps (4 × period audio
  outputs) and beats (tempo sum), reading a track on beats (wait,
  release one beat before the next item), one-byte, two-byte and fixed
  lengths, the pitch of each note number, fine tuning and transposition,
  rests, commands 0 to 48 (samples, volume, envelope settings, jumps,
  calls, branch, tempo, period, echo and noise settings, presets, fade),
  the pitch slide, vibrato and volume sweep with their exact step
  timing, and song-wide settings at the start. How a voice makes sound
  from its settings is not covered yet.
- `formats/sound.md`: points to it; the rate a sample plays at is its
  pitch.
- `formats/README.md`: lists it.

## spec-v0.63.0

Answers https://github.com/clamber-engine/spec/issues/22 (in part).

- `behavior/following.md`: new. How the other character follows the
  lead (`title-1`): the trail of the lead's whole-pixel positions, how
  the follower steps along it and waits, when it rests and where (28 px
  behind the lead), how it walks to its rest spot and back onto the
  trail, its facing, and the pause frames. How the missing character
  joins from the object that holds it (the 32-frame flight, the lead
  standing still meanwhile). Which characters are there, and which one
  leads, when a level begins again.
- `behavior/swap.md`, `behavior/losing-a-character.md`: point to it.

## spec-v0.62.0

Answers https://github.com/clamber-engine/spec/issues/20 (in part).

- `formats/sound.md`: new. Samples: the sample table, sample records
  (loop offset, length, 9-byte blocks with shift, filter, loop and end
  flags), decoding steps into outputs, first and loop passes (outputs
  carry on across the loop), and turning a sample into audio with an
  exact loop point (with the two `title-1` samples whose loop passes
  differ). Which samples a song has (shared list and the song's sample
  set; the first level plays song 0 with set 0), and song blocks (length,
  base address, data). What song data means is not covered yet.
- `formats/locators.md`: new, with the `title-1` rows for the sound
  tables.
- `formats/README.md`: lists both.
- `vectors/sound/`: new.

## spec-v0.61.0

Answers https://github.com/clamber-engine/spec/issues/19 (in part).

- `behavior/letters.md`: new. The first level's four letters: a
  letter's position, when one is taken (the offsets of bunches; the
  pickup ranges of bunches for the states bunches cover, plus the
  Down + Action ground action, measured for letters only), the set of
  letters taken, one more life for all four, and when and where the
  letter row is drawn.
- `behavior/collectibles.md`: "Not covered" points to it.

## spec-v0.60.0

Answers https://github.com/clamber-engine/spec/issues/21 and
https://github.com/clamber-engine/spec/issues/18 (in part).

- `REVIEW.md`: director decision (spec #21). Format specs may give where
  each kind of data starts in a supported image, in `formats/locators.md`
  only, under the rules for locator tables. `formats/README.md` points
  to it.
- `formats/sprites.md`: new. The frame table, short and full frames
  (header, piece positions, tile runs), the frame's sheet, large and
  small pieces, pictures, mirroring, where a frame's picture is drawn
  for an object, and sprite palettes. Vectors: `vectors/sprites/`.
- `formats/README.md`: lists it.
- `behavior/view-vertical.md`: the feet line is where the player's
  pictures are placed (`formats/sprites.md`); the standing player's
  feet line is 160 px below the picture's top row (was: lowest drawn
  row 159).

## spec-v0.59.0

Answers https://github.com/clamber-engine/spec/issues/17 (in part).

- `formats/placements.md`: new. The placement list: 8-byte entries
  (class, level x, height above the bottom edge, reference), the end
  entry, order, classes 1, 2, 5 (groups) and 8, group members, and when
  placed objects appear (the window of `behavior/walker-view.md`, for
  every placed object). Vectors: `vectors/placements/`.
- `formats/README.md`: lists it. `behavior/walker-view.md`: points to it.

## spec-v0.58.0

Answers https://github.com/clamber-engine/spec/issues/16 (in part).

- `formats/graphics.md`: new. Colours, palette rows and sets, tiles,
  packed tile data (pair table and four commands), tile entries, cell
  pictures (types share the ground's cell words; bits 14 and 15 mirror
  the picture), the grid picture, the ground and far layers of the
  first level, and their drawing order. Vectors: `vectors/graphics/`.
- `formats/level-ground.md`: bit 15 of a cell word and cell pictures now
  point to it.
- `formats/README.md`: lists it.

## spec-v0.57.0

Answers https://github.com/clamber-engine/spec/issues/15 (in part).

- `formats/rom-image.md`: new. The supported `title-1` release (size and
  SHA-256), copier headers, the identification outcomes, and locations
  (bank number and address) mapped to image positions. Vectors:
  `vectors/rom-image/`.
- `formats/README.md`, `TITLES.md`: point to it.

## spec-v0.56.0

Coverage work (`title-2`); answers no `spec` issue. Rolling into walkers.

- `behavior/rolling-into-walkers.md`: new section "In `title-2`". Every
  roll frame (S included) is an attack frame, with one defeat range per
  facing at every speed and size; D, the stop and jumps are not. The stop
  hurts over the same range. Two frozen frames after a defeat, for the
  player and every walker, with their inputs lost. Action held on the
  defeat frame itself boosts the roll (size + 1, 31 new roll frames from
  K + 3, always carries on); otherwise the roll resumes and ends in a
  stop 2 frames later. The defeated walker moves as a bounced one.
  Examples, trace rows, evidence and its own "Not covered" list; the
  file's `Titles:` line now lists `title-2`.
- `behavior/walker.md`, `behavior/walker-contact.md`,
  `behavior/roll-input.md`: their `title-2` "Not covered" lists point to
  it.
- `TITLES.md`: `title-2` row lists `rolling-into-walkers.md`.

## spec-v0.55.0

Coverage work (`title-1`); answers no `spec` issue. Crouching on 1-in-4
slopes, and short crouches.

- `behavior/ground-speed.md` "Crouching": a crouch lasts at least 13
  frames (D to D + 12) even if Down is released sooner; U is the first
  frame after the crouch, so a shorter press gives the same speeds as a
  13-frame one. Down held again on U to U + 9 starts a new crouch with
  target 0 on its first frame as well. Short-press example. Holds of 1 to
  40 frames are now measured.
- `behavior/slopes.md`: new section "Crouching on a slope". Crouches on
  1-in-4 slopes are the same as on flat floor, uphill and downhill; no
  sliding while crouched; `y` follows the floor rule throughout. Example
  and evidence. Removed from "Not covered" (the Down + Action ground
  action on slopes stays there).
- `behavior/movement-summary.md`: crouch row updated.

## spec-v0.54.0

Coverage work (`title-1`); answers no `spec` issue. Turning around on
1-in-4 slopes.

- `behavior/slopes.md`: new section "Turning around on a slope". Turns
  start, last and change facing as on flat floor, from standing, walking
  and running, uphill and downhill; `y` follows the floor rule
  throughout. Example and evidence. Removed from "Not covered".
- `behavior/ground-speed.md`: "Not covered (`title-1` modes)" points to
  it for 1-in-4 slopes.

## spec-v0.53.0

Coverage work (`title-1`); answers no `spec` issue. A second kind of
level grid.

- `formats/level-ground.md`: new section "Row grids". These grids are
  stored row by row, 64 cells (2048 px) per row, and can be far taller
  than 16 bands. Level x from 2048 reads into later rows. The ground
  below has no "above the level" case. Step 3 is skipped (no ground) in
  every 16th 32 px column. Checks in the game and a synthetic example.
  The column-grid rules are unchanged; the intro names the kinds. "Not
  covered" now names a third kind and the row-grid edge cases.
- `vectors/level-ground/`: new `rows-grid.hex`, `rows-surfaces.tsv` and
  `rows-ground-below.tsv`, documented in the README.

## spec-v0.52.0

Part of `spec` #11 (movement profiles): walls in the air in `title-2`.
The issue stays open for the rest of `title-2` and for `title-3`.

- `behavior/air-walls.md`: new section "In `title-2`". Only the column
  `x + vx` moves into is compared (columns passed over are not); a face
  column blocks from 1 px above the feet (less than 1 px doesn't), other
  floor blocks from 5 px and is stepped onto from 1 to 4 px (`title-1`:
  8). Rising step-onto lifts by whole pixels and keeps the fraction of
  `y`. Landing with the 3 px margin on face columns and none elsewhere;
  `vy` exactly 0 lands. Synthetic examples. Title-1 "Not covered" list
  marked as `title-1` only.
- `behavior/slopes.md` ("In `title-2`"): the 3 px landing margin follows
  the column, not the piece, at a steep face.
- `behavior/steep-faces.md`, `behavior/air-control.md` ("Not covered in
  `title-2`"): point to `air-walls.md`; jumps from a face stay not
  covered.
- `TITLES.md`: `air-walls.md` added to the `title-2` row.

## spec-v0.51.0

Part of `spec` #11 (movement profiles): steep faces in `title-2`. The
issue stays open for the rest of `title-2` and for `title-3`.

- `behavior/steep-faces.md`: new section "In `title-2`". Two faces of
  the `title-1` shape (4 px per column), both rising to the right. Being
  stopped by a rise is as in `title-1`. There is no "can't start" rule, so
  a player holding toward a face repeats a 3-frame cycle (2 with Action
  held). Face frames (a grounded frame starting in a face column): on
  each frame after one, the target is moved 1 px per frame downhill. On
  a face frame the speed is limited, more than 2.25 downhill becoming 2
  and less than 0.75 downhill becoming 0.75. The player follows drops of
  up to 8 px and leaves the ground on more (`y` + 8, `vy` 1/256, then
  + 0.4375 per frame). Getting onto a face from the floor above: 3 px is
  followed, 7 and 11 px leave as off a ledge. Facing downhill on face
  frames. Examples, Evidence and "Not covered in `title-2`". `Titles:`
  now reads `title-1`, `title-2`.
- `TITLES.md`: `steep-faces.md` added to the `title-2` row.
- `behavior/ground-speed.md`, `roll-input.md`, `roll-speed.md` and
  `walk-off.md` ("Not covered in `title-2`"): point to `steep-faces.md`
  for steep faces.

## spec-v0.50.1

Answers `spec` #13 (walk-off-ledge drop).

- `scenarios/walk-off-ledge.scn`: `requires` now asks for an exact drop
  that matches each reference trace, 176 px for `title-1` and 128 px for
  `title-2`, instead of "at least 96 px". With a smaller drop the player
  lands inside the 30-frame curve, so even an engine that follows
  `walk-off.md` exactly differs from the reference in `y`. Also requires
  a plain edge with no steep face (a face carries the player down before
  `leave_ground`, which changes the drop) and flat ground with nothing in
  the way as far as the player walks (under 200 px past the edge).

## spec-v0.50.0

Part of `spec` #11 (movement profiles): being hurt in `title-2` with one
character (the knock-out). The issue stays open for the rest of
`title-2` and for `title-3`.

- `behavior/knock-out.md`: new section "In `title-2`". The hit frame
  moves as an ordinary frame, then sets `vx` −1 and `vy` −5.5 and faces
  right (knocked left in every measured hit; all walkers measured headed
  left). A 6-frame pause between H + 1 and H + 2 (walkers skip H + 1 and
  move once in the pause). Flight with gravity 0.234375, cap 8, landing
  as in `title-2` falls, sliding left at 1 px per frame after landing. A
  hop (`vy` −2.5) on the first frame from H + 48 on the floor, then `vx`
  loses an eighth per frame (rounded toward 0) and is 0 from P + 32. The
  player leaves after the later of H + 189 and P + 140, and the level
  ends. Trace fields, a worked example on flat floor, Evidence and
  "Not covered in `title-2`". `Titles:` now reads `title-1`, `title-2`.
- `behavior/walker-contact.md` ("In `title-2`"): the measurements were
  made with the lead character alone, not with the other character
  following (corrected). The hurt bullet in "Not covered in `title-2`"
  now points to `knock-out.md`.
- `TITLES.md`: `knock-out.md` added to the `title-2` row.

## spec-v0.49.0

Answers `spec` #12: when a turn starts while the player is moving in
`title-1`.

- `behavior/ground-speed.md`: new section "Standing, walking and
  running" (`title-1`). Outside turns the player is standing, walking
  or running. Walking and running go through a 20-step stride cycle
  whose speed depends on |`vx`|. Turn checks come at the end of set
  steps, and a turn can start only then. The same checks decide when
  the player stops walking or running, and run checks decide when
  walking becomes running. The section also covers mode changes when
  standing, at the end of a turn and when Action is released while
  running, with a worked example, evidence and its own "Not covered"
  list. "Facing and turns" now points to it: "From standing still"
  becomes "While standing" (standing can include sliding), and the
  delay is no longer listed as not covered.
- `behavior/movement-summary.md`: facing row and "Not covered" updated.

## spec-v0.48.0

Part of `spec` #11 (movement profiles): which `title-2` walker contacts
hurt and which bounce. The issue stays open for the rest of `title-2`
and for `title-3`.

- `behavior/walker-contact.md`: new section "In `title-2`". Offsets use
  the player's position after its move on the frame and the walker's
  position from the frame before; the hurt or bounce happens on that
  same frame. Horizontal and vertical ranges per pose (standing,
  crouching, walking, running, in the air, which includes the start of a
  jump and the landing frames), the same ranges for hurts and bounces.
  On the ground a contact hurts. In the air it bounces when `vy` after
  the frame's gravity is at least −1.25 and the player is at most 10 px
  below the walker, otherwise it hurts. Synthetic examples, own Evidence
  and "Not covered in `title-2`" lists. `Titles:` now reads `title-1`,
  `title-2`.
- `behavior/bounce.md` ("Not covered in `title-2`"): walker contacts now
  point to `walker-contact.md`.
- `TITLES.md`: `walker-contact.md` added to the `title-2` row.

## spec-v0.47.0

Part of `spec` #11 (movement profiles): walkers in `title-2`. The issue
stays open for the rest of `title-2` and for `title-3`.

- `behavior/walker.md`: new section "In `title-2`". Walking (moves by
  the previous speed, then speeds up by about 5/64 of the difference;
  full speed 49 frames from rest), staying on the floor, being bounced on
  (doesn't move on B; heads the opposite way to the player's facing, `vy`
  −8, gravity 0.4375 capped at 12) and other walkers freezing on the
  player's two frozen frames. The existing "Not covered" list is marked
  as `title-1` only.
- `TITLES.md`: `walker.md` added to the `title-2` row.

## spec-v0.46.0

Part of `spec` #11 (movement profiles): reference traces for `title-2`.
The issue stays open for the rest of `title-2` and for `title-3`.

- `traces/FORMAT.md`: new section "Where reference traces live".
  `title-1` traces stay at `reference/<scenario-id>.trace`; traces for
  other titles go in `reference/<title-id>/<scenario-id>.trace`. One
  reference trace per scenario and title. Comparing against a title with
  no trace for that scenario is reported as missing, not as a pass or a
  fail. The format stays v1 and no existing path changes.
- Traces: new `reference/title-2/roll-flat.trace` and
  `reference/title-2/walk-off-ledge.trace`. There are no `title-2` traces
  yet for `run-jump-flat`, `bounce-walker` and `slope-dip`.
- `TITLES.md`: the `title-2` row lists its reference traces.
- `behavior/roll-input.md` ("In `title-2`"): which frames of a roll have
  the trace state `roll`, and where `roll_start` and `roll_end` go.

## spec-v0.45.0

Answers the `title-2` slopes part of `spec` #11 (movement profiles).
The issue stays open for the rest of `title-2` and for `title-3`.

- Behavior: `slopes.md` gains "In `title-2`": floor height on 1-in-2 and
  1-in-4 slopes, floor pieces 16 columns wide (flat or sloped), height
  while grounded (as in `title-1`; going downhill never leaves the
  ground), horizontal motion unchanged from flat floor (walking, running,
  stopping, standing, turns, crouching, rolls, air), the first airborne
  frame of a jump starting from the higher of `y` on T and the floor
  under the new `x`, landing (on the floor on flat pieces, from 3 px
  above it on sloped pieces), examples, evidence.
- `ground-speed.md`, `jump-input.md`, `jump-arc.md`, `air-control.md`,
  `roll-input.md`, `roll-speed.md`, `walk-off.md`: `title-2` "Not
  covered" lists point to it.
- `TITLES.md`: the `title-2` row lists `slopes.md`.

## spec-v0.44.0

Answers the `title-2` rolling-off-ledges part of `spec` #11 (movement
profiles). The issue stays open for the rest of `title-2` and for
`title-3`.

- Behavior: `rolling-off-ledges.md` gains "In `title-2`": the roll goes
  on through the fall (roll speed and steering, L included), vertical
  motion (no step to 1.4375 on L + 1), a hover from L + 2 to 12 frames
  after the Action press when the player leaves within 10 frames of it,
  the roll lengthened by 28 frames for each check (S + 28, S + 56, ...)
  at which the player is in the air, a roll ending in the air (stop with
  `vy` set to 1.4375 on D + 15, or carry-on), jumps during the fall and
  during a stop in the air (which frames, horizontal speed and gravity
  before take-off), Action in the fall (a roll on A + 2 after a stop in
  the air), examples, evidence.
- `roll-input.md`, `roll-speed.md`, `walk-off.md`, `air-control.md`,
  `jump-arc.md`: `title-2` "Not covered" lists point to it.
- `TITLES.md`: the `title-2` row lists `rolling-off-ledges.md`.

## spec-v0.43.0

Answers the `title-2` walk-off part of `spec` #11 (movement profiles).
The issue stays open for the rest of `title-2` and for `title-3`.

- Behavior: `walk-off.md` gains "In `title-2`": the leave frame (3.4375
  px), the fall (`vy` 1.4375 on L + 1, then +0.4375 per frame, cap 8),
  landing on reaching the floor (no 5 px margin), horizontal speed and
  facing in the fall, a crouch carried into the fall, a jump grace
  period (presses on L − 1, L and L + 1 still jump), jumps while crouched
  in the air, jumping on landing after a fall (earliest A + 2), evidence.
- `jump-arc.md`, `jump-input.md`, `air-control.md`: `title-2` "Not
  covered" lists and the Down-in-the-air note point to it.
- `TITLES.md`: the `title-2` row lists `walk-off.md`.

## spec-v0.42.0

Answers the `title-2` bouncing part of `spec` #11 (movement profiles).
The issue stays open for the rest of `title-2` and for `title-3`.

- Behavior: `bounce.md` gains "In `title-2`": the bounce frame (`vy` set
  to −8.21875 after the normal move), two frozen frames on which all
  input is ignored (the running target on the next frame is chosen by
  the bounce frame's input), the arc from B + 3 (the `title-2` jump arc
  from take-off B + 2, with jump counted from B + 3), `bounce_height`
  for each hold length (73.125 to 92 px), a worked example.
- `jump-arc.md` and `air-control.md`: the `title-2` "Not covered" lists
  point to it.
- `TITLES.md`: the `title-2` row lists `bounce.md`.

## spec-v0.41.0

Answers the `title-2` rolling part of `spec` #11 (movement profiles).
The issue stays open for the rest of `title-2` and for `title-3`.

- Behavior: `roll-input.md` gains "In `title-2`": when a press starts a
  roll (on the ground, after a landing, a Jump press on the same frame
  wins), presses remembered through a landing (L − 10 to L) and through
  the stop (D + 4 to D + 14), the 31-frame roll, carrying on into running
  or stopping (D to D + 14), and starting the next roll.
- `roll-speed.md` gains "In `title-2`": roll targets 1.125, 3.375 and 4.5;
  slow without a direction, fast after an Action hold of at least 32
  frames released no more than 19 frames before the roll; steering (the
  direction reverses at once, the size never changes), the stop, jumps
  out of a roll and out of the stop, worked values.
- `ground-speed.md` and `air-control.md`: the `title-2` text and "Not
  covered" lists point to the new sections (first frame after a roll,
  frames G to T of a jump out of a roll).
- `TITLES.md`: the `title-2` row lists `roll-input.md` and `roll-speed.md`.

## spec-v0.40.0

Answers the `title-2` air-control part of `spec` #11 (movement
profiles). The issue stays open for the rest of `title-2` and for
`title-3`.

- Behavior: `air-control.md` gains "In `title-2`": the frames before
  take-off (ground rules, no turn starts, no roll from an Action press),
  air targets (2.25 or 3.375, chosen by the previous frame as on the
  ground; Down makes no difference in the air), the unchanged speed rule,
  facing from T + 1 to the landing frame, worked values and a table of
  jump distances.
- `ground-speed.md`: the `title-2` "Not covered" list points to it.
- `TITLES.md`: `title-2` listed as measured for `air-control.md` as well.

## spec-v0.39.0

Answers the `title-2` jumping part of `spec` #11 (movement profiles).
The issue stays open for the rest of `title-2` and for `title-3`.

- Behavior: `jump-arc.md` gains "In `title-2`": take-off two frames
  after the jump starts, take-off speed −8.21875, gravities 0.3515625
  (held, rising) and 0.4375, the lighter gravity stopping at the top of
  the arc even if jump is held, the cap of 8, a table of `apex_height`
  and `airtime` by hold length, the full held arc, and an example.
- Behavior: `jump-input.md` gains "In `title-2`": when a jump starts
  (press made on G − 15 or later), the earliest jump after landing
  (L + 1), presses made before landing, rolls, and jumps from a crouch
  (including when the jump is cancelled or starts over).
- `TITLES.md`: `title-2` listed as measured for `jump-arc.md` and
  `jump-input.md` as well.

## spec-v0.38.0

Answers the `title-2` ground-speed part of `spec` #11 (movement
profiles). The issue stays open for the rest of `title-2` and for
`title-3`.

- Behavior: `ground-speed.md` now covers `title-1` and `title-2`. A new
  section "In `title-2`" gives the `title-2` targets (±2.25 walking,
  ±3.375 running), the rule that run speed depends on the previous
  frame's input, worked values, turns that start without delay
  (including a 7-frame short turn), and crouching (Up alone keeps a
  crouch; no turn for 11 frames after a crouch ends). The speed rule is
  the same in both titles.
- `TITLES.md`: `title-2` listed as measured for `ground-speed.md` only.

## spec-v0.37.0

Answers the `title-1` part of `spec` #11 (movement profiles). The issue
stays open for `title-2` and `title-3`.

- Behavior: new `behavior/rolling-off-ledges.md`. A roll carries on
  through a fall off a ledge, with walk-off vertical motion and the
  roll's speed rule and steering. It ends on G + 29 (D = G + 30)
  however long it ran before, and a jump can start on any frame of the
  fall. The file gives the jump's exact vertical motion.
- Behavior: new `behavior/movement-summary.md`, a one-page index of the
  movement profile with the file that defines each item. It adds no
  new rules.
- Behavior: `walk-off.md` gains "Jumping near the edge". There are no
  grace frames after leaving the ground, and at a ledge with a steep
  face at the top, a press whose next frame starts on the face gives
  no jump.
- Behavior: `roll-speed.md` gains "Jumping out of a roll". The press
  frame is still a roll frame for speed. From take-off the air/ground
  targets apply, starting from the roll's speed, and no stop follows.
- Scenarios with reference traces: `bounce-walker` (a standing jump
  onto a walker and a full-height bounce) and `slope-dip` (walking down
  and up 1-in-4 slopes, a tapped jump from the slope).
- "Not covered" cross-references updated in `jump-arc.md`,
  `air-control.md`, `roll-input.md` and `steep-faces.md`.

## spec-v0.36.0

Coverage work; answers no `spec` issue.

- Behavior: new `behavior/bunches.md`, for bunches (collectibles worth
  ten). Covers a bunch's position and the area its image stays inside,
  when one is taken (the first frame ending with the player in range),
  offsets `I` and `D` as in `collectibles.md`, pickup ranges for the
  first character by state (standing ranges from `T` to `T` + 5, then
  one jump range independent of speed, and landing while standing
  still), a taken bunch staying taken for the visit, releases on `P`,
  `P` + 6, … `P` + 54 with each item either flying (+17 frames) or
  counted at once near or past the picture's left or top edge (using
  the view position from the start of the release frame), a worked
  example of a mixed schedule, and a "Not covered" list.
- Behavior: `behavior/collectibles.md`'s "Not covered" entry for other
  kinds of collectible now points to `bunches.md`.

## spec-v0.35.0

Coverage work; answers no `spec` issue.

- Behavior: new `behavior/collectibles.md`, for single collectibles.
  Covers a collectible's position, when one is taken (the first frame
  ending with the player in range and the collectible in the view
  window), offsets `I` (in the facing direction) and `D` (from the feet
  line), pickup ranges for the first character by state (including
  jumps by frame and vertical speed, and landing while standing still)
  and for the second character when it leads, only the character under
  control taking collectibles, the view window (using the view position
  after the frame's move), when the count goes up (`P` + 33, `P` + 1
  near the right edge, never when taken partly off the left edge), the
  rollover at 100 with one more life, and a "Not covered" list.

## spec-v0.34.0

Coverage work; answers no `spec` issue.

- Format: `formats/level-ground.md` defines bit 6 of a half byte as
  **passable from below** (taken from the half the shape came from, after
  the cell's mirroring; flipping and bit 15 don't change it), and a
  cell's **ground** in a column (the lines below its surface down to the
  bottom of its band). Made-up example extended; "Not covered" updated.
- Behavior: `behavior/platforms-from-below.md` now derives which ground
  is a platform and where its underside is from the level format (the
  bottom line of the top's band), and adds "Ground that isn't passable
  from below": a rising player whose feet line would end a frame in
  such ground is stopped (feet line kept, `vy` 0), with edges, trace
  rows, a synthetic example and measurements on modified ground.
- Vectors: `vectors/level-ground/` gains `passable.tsv`, type 37 and
  column 36 (bit 6 in one half, plain, mirrored and with bit 15), and
  surfaces for x 1152–1183. `N` is now 38; the "type `N` or more" cell
  uses type 16383 so its expected surfaces are unchanged.

## spec-v0.33.0

Coverage work; answers no `spec` issue.

- Format: new `formats/level-ground.md`, the first format spec, for how
  a level's ground is stored (one kind of grid, 16 bands tall). Covers
  level x and lines, the grid of 32 px columns stored column by column
  as 16-bit cell words (type and mirrored flag), the type table of two
  half bytes per type (shape and flipped flag), finding the surface in a
  cell for a 1-px column, the shapes as families with formulas, the
  ground below a point (the feet's band, one band up, two bands down,
  "above the level"), a made-up example and a "Not covered" list.
- Vectors: new `vectors/level-ground/` with a synthetic type table and
  grid, plus the expected surfaces for every column and band and the
  expected ground below for 12 columns and feet lines −8 to 520.
- `formats/README.md`: lists the format files.

## spec-v0.32.0

Coverage work; answers no `spec` issue.

- Behavior: new `behavior/falling-out.md`, for when the player falls
  below the bottom of the level. Covers the level's height `Yb` (storage
  left to the level format), the fall-out frame Z (the first frame
  ending more than 48 px below `Yb`, with the exact edge), input ignored
  from Z, the player out of sight and the view still from Z + 1, no
  pauses, the last frame at Z + 96, the same ending with two characters,
  trace rows for Z to Z + 96, a made-up example and a "Not covered" list.
- `knock-out.md`: points to the new file instead of listing falling out
  of the level as not covered.

## spec-v0.31.0

Coverage work; answers no `spec` issue.

- Behavior: new `behavior/walker-view.md`, for when walkers are in the
  level as the view moves. Covers the view position used (from the start
  of the frame), the window around the picture in which a walker's start
  makes it appear, the first frame after appearing, removal (start
  outside the window and the walker far enough outside the picture),
  removed walkers coming back at their start, defeated walkers never
  coming back, and a group of walkers appearing together (partly
  measured). Includes synthetic examples and a "Not covered" list.
- `walker.md`: the patrol range is described as centred on the walker's
  start, and its "Not covered" list points to the new file.

## spec-v0.30.0

Coverage work; answers no `spec` issue.

- Behavior: new `behavior/view-vertical.md`, for how the view moves
  up and down to follow the player. Covers the view's position and a
  smoothed gap, the feet line, the ground used for aiming (the feet
  line when grounded; in the air, the ground below when within two
  32 px bands of the feet, otherwise none), the target with the feet
  near the top of the picture, the level's vertical limits (a top, a
  bottom and a rate per entry, chosen by `x`; storage left to the level
  format), easing with usual and quick rates and the small-gap rule,
  the shake during the first character's ground action, and the bounce
  frames B and B + 1 on which it stays still. Includes three made-up
  worked examples and a "Not covered" list.
- `view.md`: points to the new file instead of listing the vertical
  motion as not covered.

## spec-v0.29.0

Coverage work; answers no `spec` issue.

- Behavior: new `behavior/view.md`, for how the view moves
  horizontally to follow the player. Covers the view's position and
  speed and the precision each is kept to. The target is the player's
  `x` plus a lead made of a base for each facing, a facing part and a
  speed part, with a table of values. Each frame the speed moves an
  eighth of the way toward the gap ÷ 32 (rounding stated). The view
  stays still on B and B + 1 of a bounce and on K and K + 1 of a roll
  defeat. Includes two made-up worked examples and a "Not covered"
  list.
- `walker.md`: the "Not covered" entry on walkers appearing as the
  view moves points to the new file.

## spec-v0.28.0

Coverage work; answers no `spec` issue.

- Behavior: new `behavior/rolling-into-walkers.md`, for a rolling
  player meeting a walker. Attack frames (every roll frame and the
  first 8 frames of a stop, D to D + 7); defeat ranges for the offset I
  at the end of the frame, for the small walker and for the large
  walker facing right; when the player can be hurt around a roll; a
  2-frame pause on K + 1 and K + 2 that delays the rest of the roll; a
  roll that defeats a walker always carries on into running, fast rolls
  included; a boost when Action is held from K + 3, starting a new
  29-frame roll one size larger per defeat; the defeated walker's
  motion. Includes made-up worked examples, trace notes and a "Not
  covered" list.
- `roll-input.md`, `roll-speed.md`: point to the new file; a fast roll
  always ends in a stop unless it defeats a walker.
- `walker-contact.md`, `walker.md`: "Not covered" entries point to the
  new file.

## spec-v0.27.0

Coverage work; answers no `spec` issue.

- Scenarios: new `scenarios/roll-flat.scn`, walking right on flat
  ground with a one-frame Action press, through the roll and the stop
  after it. Metrics: `roll_distance`, `top_speed`, `final_x` and an
  `x,vx` curve from `roll_start`.
- Traces: new reference trace `traces/reference/roll-flat.trace`
  (`title-1`).
- `roll-input.md`: corrects the stop after a roll. Speed is 0 from D + 3
  to D + 17 (it said D + 16) and normal movement starts on D + 18; a
  direction held on D + 17 doesn't move the player yet, while a roll can
  still start on D + 17. Adds facing during the stop. The worked example
  is corrected to match.
- `roll-speed.md`: `vx` is 0 from D + 4 to D + 17 and the ground speed
  rule restarts from 0 on D + 18; a roll from a new press on D + 17
  keeps speed 0 on its first frame.
- `swap.md`: its note on the stop now says it lasts until D + 17.

## spec-v0.26.0

Coverage work; answers no `spec` issue.

- Behavior: new `behavior/protection.md`, for protection after a hit
  with two characters. The character that takes over can't be hurt from
  H to H + 149, whatever the timing of its hop; a contact that would
  hurt has no effect on either side, and one still there at the end of
  H + 149 hurts on H + 150. It flashes from H + 38 to H + 133 in a
  4-frame cycle (2 drawn, 2 not drawn) with T + 1 always drawn; where
  the cycle starts is left to the engine, and the file states what a
  scenario may and may not check. Includes made-up worked examples,
  trace impact (none) and a "Not covered" list.
- `losing-a-character.md` and `knock-out.md`: their "Not covered" items
  on protection point to the new file; `knock-out.md` states that no
  protection applies after its hit, since it ends the level.

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
