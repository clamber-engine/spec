# Changelog

Spec releases are tagged `spec-vMAJOR.MINOR.PATCH`. The engine pins one.

## spec-v0.146.0

Removal of placed objects in lists with sections.

Part of clamber-engine/spec#17 (the first level's layout). The issue
stays open for the parts not covered yet.

- `formats/placements.md`, "When placed objects are removed": new "In
  lists with sections". Objects in the shared list are removed by the
  first level's table; in the underwater lists they are also removed by
  the vertical window alone, with per-kind side margins. Not covered
  narrowed. (spec#17)

## spec-v0.145.0

A thrower knocked out by the ground action while the player is knocked
out.

Part of clamber-engine/spec#19 (the first level's remaining entities).
The issue stays open for the parts not covered yet.

- `behavior/thrower.md`, "While the player is knocked out": a thrower
  knocked out by the ground action (including when the player is hit
  while still in it) does the same as one knocked out by a bounce or a
  roll. Not covered narrowed. (spec#19)

## spec-v0.144.0

Defeated walkers falling out of the window, and a thrower knocked out by
a roll while the player is knocked out.

Part of clamber-engine/spec#19 (the first level's remaining entities).
The issue stays open for the parts not covered yet.

- `behavior/walker-view.md`, new section "Defeated walkers falling out":
  a walker that was bounced on, rolled into or hit by a thrown object is
  removed on the first frame its own position is outside the window,
  checked from a fixed number of frames after the defeat; measurements
  and edge tests. "Coming back" now also covers walkers hit by a thrown
  object. Not covered narrowed. Pointers added from `walker.md`,
  `rolling-into-walkers.md` and `thrown-objects.md` (whose Not covered
  item on removal is gone). (spec#19)
- `behavior/thrower.md`, "While the player is knocked out": a thrower
  knocked out by a roll does the same as one knocked out by a bounce.
  Not covered narrowed. (spec#19)

## spec-v0.143.0

An object in the lead's hands at the level end, and a thrower already
knocked out while the player is knocked out.

Part of clamber-engine/spec#17 (the first level's layout) and
clamber-engine/spec#19 (the first level's remaining entities). Both
issues stay open for the parts not covered yet.

- `behavior/level-end.md`, new section "An object in the lead's hands":
  a barrel or holder still in the lead's hands when the end marker is
  passed is let go on the first walk-out frame and falls away without
  touching anything until it is removed; when it is removed;
  measurements. Not covered narrowed, with a new item for carrying with
  the second character leading or the other character following.
  (spec#17)
- `behavior/thrower.md`, "While the player is knocked out": a thrower
  already knocked out and falling stops for 10 frames and then goes on
  10 frames late, with removal checked against each frame's view. Not
  covered narrowed. (spec#19)

## spec-v0.142.0

Finishing a level with the second character leading, and the placed
ball while the player is knocked out.

Part of clamber-engine/spec#17 (the first level's layout) and
clamber-engine/spec#19 (the first level's remaining entities). Both
issues stay open for the parts not covered yet.

- `behavior/level-end.md`, new section "The second character leading":
  passing the end marker, the walk-out and the fade are the same with
  the second character leading, including the walk-out speed; checks.
  Not covered narrowed, with a new item for the follower's walk-out
  movement. (spec#17)
- `behavior/thrower.md`, "While the player is knocked out": a ball
  placed in the level freezes and goes on 10 frames late like a thrown
  ball. Not covered narrowed. (spec#19)

## spec-v0.141.0

An object in the hands at a knock-out, and the thrower and its balls
while the player is knocked out.

Part of clamber-engine/spec#17 (the first level's layout) and
clamber-engine/spec#19 (the first level's remaining entities). Both
issues stay open for the parts not covered yet.

- `behavior/knock-out.md`, new section "An object in the hands": a
  barrel or holder still in the hands at a knock-out is let go on the
  hit frame and falls away without touching anything until it is
  removed; when each kind starts moving, worked table, measurements.
  `behavior/holder-breaking.md` and `behavior/carrying.md` point to it.
  `behavior/restart-barrel.md`: such an object is at its entry's place
  again after the resume. Not covered narrowed. (spec#17)
- `behavior/thrower.md`, new section "While the player is knocked out":
  the thrower and every ball stop for 10 frames after the hit frame,
  then go on 10 frames late. Not covered narrowed. (spec#19)

## spec-v0.140.0

Holders and who leads after a knock-out, more kinds there again, and the
placed ball after a level-start resume.

Part of clamber-engine/spec#17 (the first level's layout) and
clamber-engine/spec#19 (the first level's remaining entities). Both
issues stay open for the parts not covered yet.

- `behavior/restart-barrel.md`, "What is there again after a
  knock-out": the list now includes a small walker bounced on, the
  barrel and a holder put down unbroken elsewhere (back at their
  entries' places) and a broken holder. New paragraph "Holders, and who
  leads": after the resume, at the level's start or the restart point,
  the character that was leading at the knock-out leads alone, the other
  is missing again and both holders are back. More measured runs; Not
  covered narrowed. `behavior/following.md`, "When a level begins
  again": new row and a pointer to the new paragraph. (spec#17)
- `behavior/thrower.md`, "Balls placed in a level": after a knock-out
  before the restart point is reached, the placed ball appears again
  when the view comes to it, as after an exit. Not covered narrowed.
  (spec#19)

## spec-v0.139.0

The knocked-out thrower.

Part of clamber-engine/spec#19 (the first level's remaining entities).
The issue stays open for the parts not covered yet.

- `behavior/thrower-contact.md`, new section "The knocked-out thrower"
  (first level, lead alone): however it was knocked out (bounce, roll or
  ground action), it falls through every floor below its ledge, never
  hurts the player or gives a bounce from K + 4 on, is removed once it
  leaves the picture by the same edges as a ball, and never comes back.
  Synthetic example, new checks; Not covered narrowed. Pointers from the
  bounce and roll sections and from `behavior/ground-action-enemies.md`
  "The thrower". (spec#19)

## spec-v0.138.0

More of what is there again after a knock-out, and resuming at the
restart point.

Part of clamber-engine/spec#17 (the first level's layout). The issue
stays open for the parts not covered yet.

- `behavior/restart-barrel.md`, "What is there again after a
  knock-out": the list of objects there again after a knock-out now
  includes large walkers bounced on, the thrower knocked out by the
  ground action, `extra-life-3` items taken (their lives are given
  again) and a `hidden-barrel` set off. New paragraph: after resuming at
  the restart point, every listed kind except the letters is there
  again, on either side of the restart point. More measured runs; Not
  covered narrowed. `behavior/extra-lives.md` Not covered updated to
  match. (spec#17)

## spec-v0.137.0

The first level's room-return entrance, the thrower and placed ball
after an exit, and what is there again after a knock-out.

Part of clamber-engine/spec#17 (the first level's layout) and
clamber-engine/spec#19 (the first level's remaining entities). Both
issues stay open for the parts not covered yet.

- `behavior/level-start.md`, new section "Coming back from the room":
  the first level's second entry at the start point is of the new kind
  `room-return-entrance`. Coming back from the room, it bursts and
  throws the characters as the `entrance` does from the level map; the
  other entry is there but does nothing and isn't drawn. New kind row
  and checks in `formats/placements.md` (every reference the first
  level uses now has a fingerprint row), one kind fingerprint row in
  `formats/locators.md`, pointers in `behavior/exits.md` and
  `formats/level-start.md`. (spec#19)
- `behavior/exits.md`, "Coming back to the level": a thrower knocked
  out by a bounce or a roll is there again after either exit, and the
  level's placed ball appears again at the same position after either
  exit (not without one). New checks; Not covered narrowed.
  `behavior/thrower.md` "Balls placed in a level" updated to match.
  (spec#19)
- `behavior/restart-barrel.md`, new section "What is there again after
  a knock-out" (first level, lead alone): the collectible count, figure
  count and restart point are kept; the set of letters taken is emptied
  on a resume at the level's start and kept at the restart point; which
  taken or broken objects are there again. Pointers replace Not covered
  sentences in `bunches.md`, `letters.md`, `figure.md`,
  `extra-lives.md` and `exits.md`. (spec#17)

## spec-v0.136.0

Rolling into the thrower, and an object carried into an exit during the
walk-out.

Part of clamber-engine/spec#17 (the first level's layout) and
clamber-engine/spec#19 (the first level's remaining entities). Both
issues stay open for the parts not covered yet.

- `behavior/exits.md`, "The walk-out": new rule "An object in the lead's
  hands": a barrel or holder still carried (or mid-throw) when the exit
  takes over is let go on `T` + 1, then rises and falls on its own,
  touching nothing, until it is removed off the left end or far below
  the view, or the area changes. Two new reach rows for standing still
  while carrying. "Coming back to the level" points to the new rule.
  New checks; Not covered narrowed. (spec#17)
- `behavior/thrower-contact.md`, new section "Rolling into the thrower":
  when a roll defeats the thrower (attack frames as for walkers, the
  thrower's own range), being hurt around a roll, and what follows a
  defeat (the player's pause, boost and speeds as for walkers; the
  thrower knocked out as by a bounce). Example and checks; Not covered
  narrowed. Pointers in `thrower.md` and `rolling-into-walkers.md`.
  (spec#19)

## spec-v0.135.0

More of what stays gone after coming back through an exit, and whether
placed balls come back.

Part of clamber-engine/spec#17 (the first level's layout) and
clamber-engine/spec#19 (the first level's remaining entities). Both
issues stay open for the parts not covered yet.

- `behavior/exits.md`, "Coming back to the level": the extra-life-3
  items once taken stay gone after coming back (`extra-lives.md`
  pointers updated); hidden objects set off by a fall stay gone; and the
  item a falling kind released stays gone whatever was done with it
  before the exit (left at rest, put down elsewhere, thrown or still
  carried; a carried item isn't in the scene or the room). New checks;
  Not covered narrowed. (spec#17)
- `behavior/thrower.md`, "Balls placed in a level": a placed ball
  appears only once and doesn't come back however the view moves, until
  play resumes after a knock-out, when it appears again by the same
  rule at the same position. Not covered narrowed. (spec#19)

## spec-v0.134.0

The animal after a knock-off, objects carried far from their entry, and
touching the thrower.

Part of clamber-engine/spec#17 (the first level's layout) and
clamber-engine/spec#19 (the first level's remaining entities). Both
issues stay open for the parts not covered yet.

- `behavior/exits.md`, "Coming back to the level": an animal whose rider
  was knocked off by a walker before the exit isn't there after coming
  back, whether or not it was still in the level when the exit began,
  and neither are the crate's boards. New check "Knocked off before the
  exit"; Not covered narrowed. (spec#17)
- `formats/placements.md`, "Objects put down elsewhere": a carried
  `barrel` or `holder` is there on every frame, however far it is from
  its entry, and the entry places nothing else while it is carried; once
  it is put down, the existing rule applies from where it was put down.
  New check "Checks, carried far"; Not covered narrowed. (spec#17)
- New `behavior/thrower-contact.md`: when touching the thrower hurts the
  player (offset ranges by pose and facing, vertical reach, the ledge
  and landing rules, no hurt on the frame before a throw) and when it
  gives a bounce (offset ranges, the up-speed limit, landing first on
  the ledge). A bounce knocks the thrower out, as the ground action
  does, and it throws no more balls. Synthetic examples, evidence and
  Not covered. Pointers from `thrower.md`, `ball-contact.md`,
  `knock-out.md`, `losing-a-character.md`, `bounce.md` and
  `movement-summary.md`. (spec#19)

## spec-v0.133.0

Riding the animal into an exit.

Part of clamber-engine/spec#17 (the first level's layout). The issue
stays open for the parts not covered yet.

- `behavior/exits.md`, "Coming back to the level": a lead riding the
  animal keeps it through the exit. The walk-out is ridden and the same
  frame for frame as on foot; in the scene and the room the lead is on
  foot and the animal isn't there. Back in the level the lead rides from
  frame 0, the walk-in (after the scene) or the level start (after the
  room) is unchanged up to the hand-over, and the speed then follows
  `riding.md`. No second animal is at the crate's place. New check
  "Riding into the exit"; Not covered narrowed.

## spec-v0.132.0

Holders moved or carried before an exit.

Part of clamber-engine/spec#17 (the first level's layout). The issue
stays open for the parts not covered yet.

- `behavior/exits.md`, "Coming back to the level": a `holder` picked up
  and put down unbroken elsewhere, or still carried when the exit takes
  over, is at its place again after coming back through either exit,
  still holding the missing character; nothing is where it was put
  down. A carried holder is still in the level as the walk-out begins,
  doesn't break, and isn't in the scene or the room. New check "Coming
  back, holders moved"; Not covered narrowed.

## spec-v0.131.0

The thrower and the holders after an exit, and other objects on a
bounce's frozen frames.

Part of clamber-engine/spec#17 (the first level's layout) and part of
clamber-engine/spec#19 (the first level's entities). Both issues stay
open for the parts not covered yet.

- `behavior/exits.md`, "Coming back to the level": a `thrower` knocked
  out by the ground action before the exit is there again after coming
  back; a broken `holder` stays gone and the character it freed still
  follows the lead. Without an exit neither came back within the same
  visit. New checks "Coming back, the thrower" and "Coming back, the
  holders"; Not covered narrowed.
- `behavior/bounce.md`: new subsection "Everything else on the frozen
  frames": on B + 1 and B + 2 no other object moves or turns, and steady
  movers take their next step on B + 3. New Not covered bullets.
  `behavior/walker.md` and `behavior/movement-summary.md` updated to
  match; the walker Not covered item about other enemies freezing is
  removed.

## spec-v0.130.0

Large walkers after an exit.

Part of clamber-engine/spec#17 (the first level's layout). The issue
stays open for the parts not covered yet.

- `behavior/exits.md`, "Coming back to the level": a `large-walker`
  bounced on before the exit is there again after coming back, as the
  small and medium walkers are; without an exit it was not there again
  within the same visit (one run). New checks "Coming back, large
  walkers"; Not covered narrowed.

## spec-v0.129.0

The first level's scene's sign, and moved barrels after an exit.

Part of clamber-engine/spec#17 (the first level's layout) and part of
clamber-engine/spec#19 (the first level's entities). Both issues stay
open for the parts not covered yet.

- `behavior/signs.md`: new section "The scene's sign": the scene's
  class 1 entry is a `sign`, there at its entry's position from the
  scene's frame 1 on, never moving or removed; placed in the first level
  it behaves as the other signs. New checks bullet.
  `formats/locators.md`: one kind fingerprint row (`sign`).
  `formats/placements.md` ("In the first level's scene") and
  `behavior/exits.md` ("The scene", Not covered) narrowed to the
  remaining class 2 entry.
- `behavior/exits.md`, "Coming back to the level": a `barrel` put down
  unbroken elsewhere, or still carried when the exit takes over, is at
  its place again after coming back (the carried barrel is not in the
  scene or the room). New checks "Coming back, moved barrels"; Not
  covered narrowed.

## spec-v0.128.0

Single collectibles in the level data, and more of what comes back after
an exit.

Part of clamber-engine/spec#17 (the first level's layout) and part of
clamber-engine/spec#19 (the first level's entities). Both issues stay
open for the parts not covered yet.

- `formats/collectibles.md` (new): where a level's single collectibles
  are: the group list of 4-byte records, the shared shape table and its
  cell bits, and the items a group places; the first level's counts;
  synthetic example, checks and Not covered. Vectors:
  `vectors/collectibles/` (new). `formats/locators.md`: two rows (the
  first level's group list and the shape table). Pointers in
  `behavior/collectibles.md`, `formats/placements.md` and
  `formats/README.md`.
- `behavior/exits.md`, "Coming back to the level": the figure count is
  kept; a taken figure, taken `rising-extra-life-1` and
  `rising-extra-life-2` items, a struck `hidden-bunch` and its bunch,
  and a broken `animal-crate` with its unmounted animal stay gone; a
  `medium-walker` bounced on and a `barrel` broken against a wall are
  there again. New checks "Coming back, more kinds" and "Coming back,
  barrel and crate"; Not covered narrowed.

## spec-v0.127.0

Entries far above or below the view, and the barrel that shoots down.

Part of clamber-engine/spec#17 (the first level's layout) and part of
clamber-engine/spec#19 (the first level's entities). Both issues stay
open for the parts not covered yet.

- `formats/placements.md`: in lists without sections, class 2 entries,
  like class 1 entries, need only the window, not the vertical window,
  and appear however far above or below the view they are. New checks
  "Far above or below the view"; the matching Not covered item is
  removed.
- `behavior/launch-barrel.md`: new section "The barrel that shoots
  down", kind `down-launch-barrel`: catch, a 28-frame hold, the shot
  straight down as the barrel breaks, and the flight. Checks and Not
  covered updated.
- `behavior/wall-exits.md`: new section "Coming back from the second
  wall exit's area": the lead starts at the second wall-return record's
  point and is caught by the level's `down-launch-barrel`; the record and
  the barrel's entry in moved copies; the fade; checks.
- `formats/level-start.md`: the second wall-return record.
  `formats/locators.md`: its locator row and a `down-launch-barrel`
  fingerprint row. `formats/placements.md`: the new kind row and the
  first level's counts (36 of 37 references, 66 entries).

## spec-v0.126.0

Barrels and holders put down elsewhere.

Part of clamber-engine/spec#17 (the first level's layout). The issue
stays open for the parts not covered yet.

- `formats/placements.md`: new subsection "Objects put down elsewhere"
  under "When placed objects are removed": a `barrel` or `holder` that
  was carried and put down is removed using its entry's position for
  the window and its own position for the margin; while it is there its
  entry places nothing else, and once removed it comes back at its
  entry. Synthetic example and checks. The section intro points to it,
  and the matching Not covered item is narrowed.

## spec-v0.125.0

How many objects can be there, and the wall-return entrance.

Part of clamber-engine/spec#17 (the first level's layout) and part of
clamber-engine/spec#19 (the first level's entities). Both issues stay
open for the parts not covered yet.

- `formats/placements.md`: new section "How many objects can be there":
  the limits on places and pictures that can keep an entry that comes
  up from appearing, the order in which waiting entries are taken, when
  a freed place or picture can be used again, a first-level note, a
  synthetic example and checks. The matching Not covered item is
  narrowed, and the "Stored order" checks point to the new section.
- `behavior/wall-exits.md`: new section "Coming back from the first
  wall exit's area": the lead starts hidden at the wall-return record's
  point and is thrown out of the `wall-return-entrance` entry: the
  burst, the throw, steering from its seventh frame, facing, the fade,
  the entry in other cases, checks and new Not covered items.
- `formats/level-start.md`: the wall-return record.
  `formats/placements.md`: kind row `wall-return-entrance`, the first
  level's counts and the check. `formats/locators.md`: the wall-return
  record row and one kind fingerprint row.

## spec-v0.124.0

Noise, and the launch barrel.

Part of clamber-engine/spec#20 (sound for the first level) and part of
clamber-engine/spec#19 (the first level's entities). Both issues stay
open for the parts not covered yet.

- `formats/voice-output.md` (`title-1`): new section "Noise": the
  shared noise register and noise rate, how the register steps (with
  the same timing as envelope steps), the noise value, and a voice with
  noise on using it in place of its blend, with everything else
  unchanged. Worked example, "When handed values count" extended,
  measurements and new Not covered items. `formats/echo.md` and
  `formats/README.md` updated to point to it.
- `vectors/noise/` (new): synthetic cases and expected outputs, with a
  new `first_noise` column and noise events.
- `behavior/launch-barrel.md` (new, `title-1`): kind
  `left-launch-barrel`, a barrel that catches the player, holds them
  and shoots them to the left: catch ranges per state, the catch frame,
  the hold, the shot, the flight until landing, when it is ready again,
  an example, checks and Not covered.
- `formats/placements.md`: kind row `left-launch-barrel`, the first
  level's counts and the check. `formats/locators.md`: one kind
  fingerprint row.

## spec-v0.123.0

When exits, the entrance and the end marker are there.

Part of clamber-engine/spec#17 (the first level's layout). The issue
stays open for the parts not covered yet.

- `formats/placements.md`, "When placed objects are removed" (`title-1`):
  exits also need the vertical window, appearing and being removed by
  it (measured limits, with a narrow band near the top edge under Not
  covered), except before the characters come out of the entrance; the
  entrance is removed on frame T and doesn't come back while the level
  goes on; whether the end marker is there outside the window makes no
  difference to play. Class 2: moving references between class 1 and
  class 2 entries of the first level changed nothing measured. New
  checks, including the exit's height, and the Not covered list
  updated.

## spec-v0.122.0

The stiff tyre.

Part of clamber-engine/spec#19 (the first level's entities). The issue
stays open for the parts not covered yet.

- `behavior/tyre.md` (`title-1`): new section "The stiff tyre", kind
  `stiff-tyre`, a tyre that never squashes or wobbles. Its top stays
  fixed, with its own touch ranges by facing and its own throw range;
  throws with no input are always caught a frame later than on the
  other tyre, so every cycle is 41 frames. Everything else as for the
  tyre. Measurements and two new Not covered items.
- `formats/placements.md`: the new kind name, the first level's updated
  counts, and the swap check.
- `formats/locators.md`: one kind fingerprint row.

## spec-v0.121.0

Exits in walls.

Part of clamber-engine/spec#19 (the first level's entities). The issue
stays open for the parts not covered yet.

- New `behavior/wall-exits.md` (`title-1`): kind `wall-exit`, a closed
  exit leading right, hidden at the foot of a rise. It is opened only
  by a thrown barrel moving right, with hit ranges for early and late
  frames out of the hands, opening on the hit frame or the next. Once
  open it takes over like `exits.md` turned around (each reach 2 px
  shorter), followed by the walk-out through the ground, the fade and
  the last frame in the area. Synthetic example, checks and Not covered.
- `formats/placements.md`: the new kind name, the first level's updated
  counts, and the swap check.
- `formats/locators.md`: two kind fingerprint rows.
- `behavior/exits.md`, `behavior/thrown-objects.md`: pointers to the new
  file.

## spec-v0.120.0

Stored order in lists without sections, and the spec's license.

Answers clamber-engine/spec#43. Part of clamber-engine/spec#17; that
issue stays open for the parts not covered yet.

- `LICENSE` (new): the spec is dedicated to the public domain under
  CC0 1.0 Universal (unmodified legal code). `README.md`: new License
  section. `GOVERNANCE.md`: new Contributions section, saying
  contributions are made under CC0 1.0.
- `formats/placements.md`: new section "Stored order". In a list
  without sections, the "When an entry appears" rules hold with the
  whole list as one open section, so an entry stored after one with a
  larger `x` waits for that entry. Class 8 entries in the window now
  count as clear. The example gains an entry stored out of order, with
  its first entry moved to fit the window rule. Checks, and an updated
  Not covered list (the "too many objects" limit now applies to any
  list).
- `vectors/placements/`: `appear.tsv` now includes the out-of-order
  entry; README updated.

## spec-v0.119.0

When the first level's placed objects are removed.

Part of clamber-engine/spec#17. The issue stays open for the parts not
covered yet.

- `formats/placements.md`: new section "When placed objects are
  removed" for the first level's non-walker kinds. Removal once the
  entry is out of the window, the kinds that get an extra margin (only
  while inside the vertical window) and those that don't, and coming
  back any number of times. Synthetic example, checks and a narrowed
  Not covered list.
- `behavior/signs.md`, `behavior/animal-crate.md`, `behavior/figure.md`:
  pointers to the new section.

## spec-v0.118.0

Extra-life items.

Part of clamber-engine/spec#19 (extra lives). The issue stays open for
the parts not covered yet.

- New `behavior/extra-lives.md` (`title-1`): four kinds of extra-life
  item, giving 1, 2 or 3 lives, that either stay where they are placed
  or rise once they appear (speed growing by 1/256 px per frame each
  frame, exact), and their removal at the level's top. Pickup ranges by
  state and facing, the lives added on the pickup frame, the lives
  display and its timing, and removal once the entry leaves the window,
  with no coming back. Worked example, checks and Not covered.
- `formats/placements.md`: the new kind names, the first level's
  updated counts, the room's `extra-life-1`, and a pointer for how the
  items are removed.
- `formats/locators.md`: four kind fingerprint rows.
- `behavior/count-display.md`: pointer to the new lives display.

## spec-v0.117.0

Default controls per title, a new neutral input `T` (Tandem), and
holding Swap.

Answers clamber-engine/spec#42 for `title-1` and `title-2`. `title-3`
is not covered yet, so the issue stays open.

- New `behavior/controls.md` (`title-1`, `title-2`): which pad button,
  by position, gives each neutral input in each title. The right face
  button is the new input `T` (Tandem). In `title-1` it is a press-only
  swap with stated differences from `S`, gets off a ridden animal, and
  does what Jump does on the level map. In `title-2` it lifts the other
  character. Measurements and Not covered.
- `behavior/swap.md`: new "Holding Swap" replaces "A refused press is
  forgotten": a press refused for a roll or a crouch is kept while `S`
  stays held. Example and measurements.
- `behavior/level-map.md`, `behavior/riding.md`: pointers to Tandem and
  the top face button.
- `scenarios/FORMAT.md`, `traces/FORMAT.md`: new input code `T`; the
  trace `input` column order is now `LRUDJASPT` (existing traces are
  unchanged).
- `TITLES.md`: `controls.md` added for `title-2`.

## spec-v0.116.0

Where the view starts when an area begins, and critters, in `title-1`.

Answers part of clamber-engine/spec#17 and part of clamber-engine/spec#19.
The rest of each is not covered yet, so both stay open.

- `behavior/view.md`: the target is 1 px less near the level's left
  end. New "When an area begins" (the view on frame 0; the level's
  start, also from the room-return record, with fast frames; walking
  in; resuming at the restart point; two synthetic examples). Not
  covered updated.
- `behavior/restart-barrel.md`: the view on frame 0 of a resume is
  1/2 px further right than stated before; the example is corrected.
- `behavior/exits.md`, `behavior/level-start.md`: pointers to the new
  view section; Not covered updated.
- New `behavior/critter.md`: the critter (scenery), when and where it
  appears, when it is removed, and that it has no effect on play;
  checks.
- `formats/placements.md`: new `critter` kind; the first level's
  references with kind rows updated; the appearing exception and
  removal now point to `critter.md`.
- `formats/locators.md`: one `critter` kind fingerprint row for
  `title-1`.

## spec-v0.115.0

How the first level's room is left, and signs, in `title-1`.

Answers part of clamber-engine/spec#17 and part of clamber-engine/spec#19.
The rest of each is not covered yet, so both stay open.

- `behavior/level-end.md`: an entry places an end marker when its
  reference's kind is `end-marker`. New "In the first level's room"
  (the room is left by passing its end marker; when the fade starts
  there, the last frame, and play going on in the first level from the
  room-return record; Right with Down also passes; what follows depends
  on the area, not the reference; a synthetic example and checks). Not
  covered updated.
- `behavior/exits.md`: the room is left past its end marker, not
  through an exit. Not covered updated.
- `behavior/level-ends.md`: the room's right stop with `B` = 0, with
  the end marker before it. Checks and Not covered updated.
- New `behavior/signs.md`: the sign (scenery), its position, when it
  appears and is removed, and that it has no effect on play; checks.
- `formats/placements.md`: new `sign` kind; the `end-marker` kind also
  covers the room's way out; the first level's, the room's and the
  scene's references with kind rows updated.
- `formats/locators.md`: one `end-marker` and two `sign` kind
  fingerprint rows for `title-1`.

## spec-v0.114.0

The ending after all four letters, and the data of the first level's
scene, in `title-1`.

Answers part of clamber-engine/spec#19 and part of clamber-engine/spec#17.
The rest of each is not covered yet, so both stay open.

- `behavior/letters.md`: new "The ending" (the four letters blink, then
  move right one at a time, with the letter taken last one frame late;
  the lives display coming in, changing to the new number and leaving;
  an example and checks). "All four letters" and "The letter row" point
  to it. Not covered updated.
- `behavior/count-display.md`: "Reaching 100" corrected. The lives
  number is also drawn, partly above the picture's top edge, on one
  more frame at the start and two more at the end.
- `formats/graphics.md`: new "The first level's scene" (what the
  importer needs; colours of two-plane tiles; the front, far and ground
  pictures; where each layer is on screen; drawing order with the front
  layer's raised tiles in front). Evidence and Not covered updated.
- `formats/level-ground.md`, `formats/placements.md`,
  `formats/level-start.md`, `formats/view-limits.md`: the scene's grid
  and type table, placement list, start record and view-limit record.
  View-limit words are unsigned.
- `behavior/level-ends.md`: new "In the first level's scene" (the view
  doesn't move). `behavior/exits.md` points to the new sections.
- `formats/locators.md`: 10 rows for the scene's data.
- `vectors/graphics/`: synthetic front-layer vectors (`front-tiles.hex`,
  `front.hex`, `front-pixels.tsv`).

## spec-v0.113.0

The level map in `title-1`: kinds of stops, how a new game begins, and
which paths are open.

Answers clamber-engine/spec#40.

- `formats/level-map.md`: new "Kinds of stops" (level stops, link
  points and three kinds of places by id; the first level's stop; where
  a press at each link point moves the lead), "A new game" (the start
  point and its link point) and "Progress" (paths open per direction of
  travel; the paths open at a new game, and the path the first level's
  first finish opens). Measurements and Not covered updated.
- `behavior/level-map.md`: new "Pressing at a stop" (what Jump, Action
  or Pause does at each kind of stop; presses before the standing frames
  aren't remembered) and "A new game" (frame by frame from the map's
  first frame to entering the first level, with an example and
  measurements). After the first finish the given direction is Down.
  Not covered updated.

## spec-v0.112.0

The data of the first level's room in `title-1`.

Answers part of clamber-engine/spec#17. The rest is not covered yet, so
it stays open.

- `formats/graphics.md`: new "Screen pictures" section (a 32 × 32
  tile-entry screen map, an unpacked 1 024-tile set and a palette set;
  the only background layer, fixed to level coordinates; backdrop and
  drawing order) and "The first level's room" (the tile set stored in
  two parts, screen map, palette set). Example, evidence and Not
  covered updated.
- `formats/level-ground.md`: the room's own grid (16 columns) and type
  table (`N` = 17), which give ground only. Checks.
- `formats/placements.md`: the room's own placement list (classes 1 and
  2 only, one `tyre` reference); every entry's object is there from the
  room's frame 1.
- `formats/level-start.md`: the room's start record.
- `behavior/level-ends.md`, `formats/view-limits.md`: the room uses no
  view-limit record (`A` = `B` = 0), its picture doesn't move, and the
  left stop holds.
- `behavior/exits.md`: Not covered narrowed to the scene's data and the
  kinds of the room's other objects.
- `formats/locators.md`: 8 rows for the room's data.
- `vectors/graphics/`: synthetic screen-picture vectors (`screen.hex`,
  `screen.tsv`, `screen-pixels.tsv`).

## spec-v0.111.0

Figures in `title-1`.

Answers part of clamber-engine/spec#19. The rest is not covered yet, so
it stays open.

- `behavior/figure.md` (new): a figure's position (its placement
  entry's level x and y), when it is taken (offsets in the facing
  direction and from the feet line, with pickup ranges for standing,
  walking, running, the ground action, crouching, rolling and two
  stages of a jump), the figure count going up by one, taken figures
  staying gone for the visit, and how the taken figure is shown
  (flight to a resting place, rest, rising out of the picture). Checks
  and Not covered.
- `formats/placements.md`: new `figure` kind; first-level counts and
  checks updated.
- `formats/locators.md`: one kind fingerprint row for `figure`.
- `behavior/collectibles.md`: pointers to the other collectible files.

## spec-v0.110.0

What is kept when play comes back to the first level in `title-1`.

Answers part of clamber-engine/spec#17. The rest is not covered yet, so
it stays open.

- `behavior/exits.md`: new "Coming back to the level": after the scene
  or the room, the collectible count, the lives, the set of letters
  taken and whether the restart point was reached are kept as on
  leaving; bunches, letters and the restart barrel once taken or
  touched stay gone; single collectibles taken before and a small
  walker bounced on before are there again. Checks and Not covered
  updated (other kinds of object, two characters, bunches and letters
  after a knock-out).

## spec-v0.109.0

The count display and the level map after a finish in `title-1`.

Answers part of clamber-engine/spec#19 and part of
clamber-engine/spec#17. The rest of each is not covered yet, so both
stay open.

- `behavior/count-display.md` (new): the count display at the top left
  (a collectible image and the number shown, one or two digits), how
  the number shown follows the count one step per frame, when the
  display is shown (from a pickup's start until its steps have happened
  and up to 59 frames after the latest step; not when a level begins),
  and the lives display when the count reaches 100 (slide in, hold,
  slide out, new number from `S` + 25). Synthetic example, checks and
  Not covered.
- `behavior/collectibles.md`, `behavior/bunches.md`: pointers to the
  count display.
- `behavior/level-map.md`: new "After finishing a level": the black
  frames up to the map (49 on a first finish, 52 or 53 otherwise), the
  fade-in, 50 still frames on a first finish only, the gate `G` (242 or
  64), the given direction on `G` and how held and new presses act on
  it, and entering the level again with Jump, Action or Pause from
  `G` + 1, with its fade-out. Synthetic example, measurements and
  Not covered updated.
- `behavior/level-end.md`: the last frame points to the new section;
  "what comes after" removed from Not covered.

## spec-v0.108.0

Exits in `title-1`: where the first level's two exits lead, and the
records for coming back to the level.

Answers part of clamber-engine/spec#17 (the level's bounds). The rest
of spec#17 is not covered yet, so it stays open.

- `behavior/exits.md`: where the first level's exits lead (the floor
  exit to a short scene and back into the level; the upper exit to a
  room, then the level begins as from the level map). New "Walking in"
  (27 frames at 3 px per frame, then 6 px, then control from frame 28
  at `vx` = 3`d`; inputs ignored; the other character 28 px behind;
  fade-in; view unchanged) and "The scene" (lead still from frame 29,
  fade-out, last frame 396). Synthetic walk-in example, checks and
  Not covered updated.
- `formats/level-start.md`: the scene-return and room-return records.
- `formats/locators.md`: two locator rows for these records.
- `formats/placements.md`, `behavior/level-start.md`: pointers updated.

## spec-v0.107.0

Exits in `title-1`: the object at the first level's left end (new
behavior spec).

Answers part of clamber-engine/spec#17 (the level's bounds). The rest
of spec#17 is not covered yet, so it stays open.

- New `behavior/exits.md`: kind `exit`, placed and not drawn at its
  entry's position. When it takes over the lead (on the ground, not
  moving right, within a vertical range and a per-state reach), the
  walk-out (fixed `y`, 2 px left per frame, inputs ignored, no ground,
  walls or left-end stop, turn for a lead facing right), and leaving
  the area (view frozen, a 30-frame fade, last frame). Synthetic
  example, checks and Not covered (where an exit leads, exits leading
  right, other states, the other character, the trace).
- `formats/locators.md`: two kind fingerprint rows for `exit`.
- `formats/placements.md`: `exit` kind name; first-level counts now 23
  references and 50 entries; checks and Not covered updated.
- `behavior/level-ends.md`, `behavior/view.md`: Not covered items about
  the left-end object now point to `exits.md`.

## spec-v0.106.0

View limits and the level's left and right ends in `title-1` (new
format and behavior specs).

Answers part of clamber-engine/spec#17 (the level's bounds and the
view's limits). The rest of spec#17 is not covered yet, so it stays
open.

- New `formats/view-limits.md`: the view-limit record (left limit `A`,
  right limit `B`, the vertical limit entries of `view-vertical.md`,
  and an end word), the first level's record (new row in
  `locators.md`; the restart point uses the same record), a synthetic
  example, checks and Not covered. Synthetic vectors in
  `vectors/view-limits/` (records, entries, entry in use, picture
  edge).
- New `behavior/level-ends.md`: the picture's left edge `E` is ⌊`V`⌋
  kept from `A` to `B`, while `V` itself can go past the limits; the
  ground layer, sprites, placement window and level-end fade use `E`.
  The player is stopped 18 px inside the range the picture can show:
  moves toward an end from at or past that point get `vx` 0 and `x`
  doesn't change. Synthetic examples, checks and Not covered.
- Pointers updated in `behavior/view.md`, `behavior/view-vertical.md`,
  `formats/placements.md`, `formats/level-ground.md` and
  `formats/README.md`.

## spec-v0.105.0

Trace vocabulary for holding a rope in `title-1`.

Answers clamber-engine/spec#39.

- `traces/VOCABULARY.md`: new state `rope` and new events `rope_catch`
  and `rope_slide_off`.
- `behavior/ropes.md`, new section "In a trace": `rope` covers the catch
  frame through the last holding frame, with `ground` and `vx` 0 on those
  rows. The catch frame carries `rope_catch`. The jump-off frame is
  `jump` with `jump_start` and is followed by ordinary jump rows. The
  slide-off frame is `fall` with `rope_slide_off` (not `leave_ground`).
  Includes a synthetic example and a check against recorded runs.

## spec-v0.104.0

Level start in `title-1`: start records and the first level's entrance
(new format and behavior specs).

Answers part of clamber-engine/spec#17 (the characters' start). The
rest of spec#17 is not covered yet, so it stays open.

- New `formats/level-start.md`: the start record (two 16-bit words,
  level x and height above `Yb`), the first level's start and restart
  records, and checks. Synthetic vectors in `vectors/level-start/`.
- New `behavior/level-start.md`: the characters stay hidden at the
  start point until the entrance bursts (frame 41) and throws them out
  (T = 51) at +2.375 / −2.375 px per frame for 14 frames, then gravity
  0.4375 up to 8. Inputs are ignored through T + 38, and from T + 39 to
  the landing the speed moves toward the input's target by gap ÷ 32.
  The second character is one frame behind. Also covers the picture's
  fade-in, a synthetic example, evidence and Not covered.
- New kind `entrance`: a fingerprint row in `formats/locators.md`, plus
  the kind table and counts in `formats/placements.md`. Two new locator
  rows for the start and restart records.
- Pointers in `formats/placements.md`, `behavior/restart-barrel.md`,
  `behavior/following.md` and `formats/README.md`.

## spec-v0.103.0

Rolls on, onto and into steep faces in `title-1` (coverage).

- `behavior/steep-faces.md`: new section "Rolling on, onto or into a
  face". Steering does nothing on frames that start on a face; a roll
  pointing downhill there gets 1.5 added downhill to its target, one
  pointing uphill doesn't. Roll frames are blocked as in "Stopped by a
  rise", and also onto non-face floor more than 8 px higher. A blocked
  roll is stopped (target 0, carried down a face like a standing player)
  until it pushes off at 2 with its direction held off a face; its size
  stays 2 until the direction is held again. Synthetic examples and
  evidence; intro, "Stopped by a rise" and Not covered updated.
- `behavior/rolling-off-ledges.md`: how far below missing ground counts
  for "Stopping before a ledge" (47 px counts, 49 px doesn't); a stopped
  frame makes a stopped roll, and push-off follows the frame's steering.
- Pointers in `roll-speed.md`, `roll-input.md` and `movement-summary.md`.

## spec-v0.102.0

Jumping after a bounce's landing in `title-1` (coverage).

- `behavior/jump-input.md`: new subsection "After a bounce" under
  "Landing lockout". A bounce landing's switch frame is the landing frame
  (M = L), so E = L + 2, and the buffered-press window counts back from
  that E. Table of cases and evidence. Bounces are removed from
  "Landings this doesn't cover".
- `behavior/bounce.md`: pointer to the new subsection; "Jumping on the
  landing frame after a bounce" dropped from Not covered.

## spec-v0.101.0

Jumping on a steep face and in front of a rise in `title-1` (coverage).

- `behavior/steep-faces.md`: new section "Jumping on or next to a face".
  The press frame is an ordinary frame on the floor. A press whose frame
  or next frame starts on the face (a carried press) doesn't take off on
  the next frame; held, it takes off on the frame after the player is
  first on other floor, if that is at most 12 frames after the press,
  and otherwise not at all. One measured exception for the frame that
  steps onto a face with only the uphill direction held. On the take-off
  frame "Can't start toward a rise" doesn't apply, "Stopped by a rise"
  does, and `y` follows the new column's floor. Synthetic examples and
  evidence; rolls onto faces and the second character stay not covered.
- `behavior/walk-off.md`, `behavior/jump-input.md`,
  `behavior/jump-arc.md`: pointers to the new section.

## spec-v0.100.1

Repeated tyre throws with no input in `title-1` (clarification).

Answers clamber-engine/spec#37.

- `behavior/tyre.md`: the rules are unchanged. "The cycle repeats
  every 40 frames" held only for the first cycle. The new subsection
  "Throws with no input" covers the pixel fraction kept from each catch,
  which decides whether the next catch is on L + 25 or L + 26. Cycles
  are 40 or 41 frames, and from a whole-pixel start a group of five
  cycles (204 frames) repeats after the first. The example now continues
  to the next throw and catch.

## spec-v0.100.0

Walking off a ledge with the second character, and jump presses at
ledges with a steep face, in `title-1` (coverage).

- `behavior/walk-off.md`: "Jumping near the edge" restated as three
  rules for both characters. A press counts only if its frame starts
  grounded and not on the face. A press that counts sets the jump
  gravity for the fall, including L, even if it gives no jump. It jumps
  on the next frame unless that frame starts on the face. At running
  speed the player can pass the face without a frame starting on it, and
  then presses on L − 1 and L still jump (`coyote_frames` 1). Also an
  updated example, evidence for both characters, a pointer in "Falling"
  and an updated "Not covered".
- `behavior/second-character.md`: new section "Walking off a ledge" with
  this character's values, walking and running examples and evidence.
  "Not covered" updated.
- `behavior/jump-input.md`: the pointer to faced ledges now says when a
  press on L doesn't jump.

## spec-v0.99.0

The mode after falls and bounces in `title-1`.

Answers clamber-engine/spec#36.

- `behavior/ground-speed.md`: new section "After a fall or a bounce".
  After walking or running off a ledge without a face, the mode and
  stride cycle go on through the fall and the landing as on the ground,
  and there are no landing frames. After a fall from a steep face there
  are 11 landing frames (no mode, facing fixed, no turn), then a mode
  starts as at the end of a turn. After a bounce, facing stays fixed up
  to the landing and a mode starts on the landing frame. A roll off a
  ledge ends as "After a roll" says. Also examples, evidence and an
  updated "Not covered". The M = L + 1 default in "After a landing" now
  applies only to jumps that started from a roll.
- `behavior/air-control.md`: facing stays fixed during a walk-off fall
  only at ledges with a steep face. At a ledge without a face, turns go
  on in the air.
- `behavior/bounce.md`: facing doesn't change from the bounce to the
  landing, frozen frames included.
- `behavior/jump-input.md`, `behavior/walk-off.md`: pointers to the new
  section.

## spec-v0.98.0

Coverage: the frames after a landing in `title-2`.

Answers no `spec` issue.

- `behavior/ground-speed.md`: new section "After a landing (`title-2`)".
  It defines the first landing frame (one frame later after a fall than
  after a jump), the landing's length (9 frames if Action and a net
  direction are held on that frame, else 18), air rules on its first
  frame, the grounded target afterwards with facing fixed and no turn
  until the landing ends, and crouches, rolls and jumps during it. Also
  worked examples, evidence, and updates to the Turns rule, the Target
  note and "Not covered in `title-2`".
- `behavior/air-control.md`: the not-covered note points to the new
  section.
- `behavior/roll-input.md`: rolls after a fall start one frame later
  than after a jump.

## spec-v0.97.0

Coverage: swimming in `title-2` against walls, floors and ceilings.

Answers no `spec` issue.

- `behavior/swimming.md`: new section "Walls, floors and ceilings
  (`title-2`)" with synthetic examples. It covers moving across, landing,
  resting, leaving the floor, ceiling bumps (ground passable from below
  doesn't stop a rise) and the hold that keeps the swimmer 16 px inside
  each side of the picture. Also new checks with negative controls, and
  updates to the intro, the open-water check and "Not covered in
  `title-2`".
- `formats/level-ground.md`: the "Passable from below (`title-2`)" note
  now points to the swimming rule.

## spec-v0.96.0

Coverage: `title-2` level ground beyond the first level.

Answers no `spec` issue.

- `formats/level-ground.md`, "In `title-2`": new sections "Areas and
  layouts" (the area record: map width and height, then each area's
  rectangle and layout; layout 0 is a column-by-column map with a
  16-band column grid, layouts 4 to 7 are row-by-row maps with a row
  grid; the area's grid is its rectangle of the map), "Row grids" (bands
  and cells from the rectangle; the ground below, with `title-1`'s
  row-grid change to step 2 and step 3 never skipped), "The grid's first
  cell" (grid column 0, band 0 never has ground) and "Shapes 63 to 79"
  (a rise table, some shapes depending on the feet line's height in its
  band). Also a synthetic row-grid example, checks, a note that some
  levels set bit 8 of a half word, and an updated "Not covered in
  `title-2`" list (layouts 1 to 3 and 8, searches leaving the
  rectangle).
- `vectors/level-ground/title-2/`: new `rows-*` vectors (types, unpacked
  map, area record, expected grid, surfaces at each height, ground below)
  and a README section.

## spec-v0.95.0

Coverage: single collectibles in `title-2`.

Answers no `spec` issue.

- `behavior/collectibles.md`: new section "In `title-2`". A
  collectible's position on the player's scale; it is taken on the
  first frame that ends with the player in range, with offsets from the
  player's whole-pixel position; one range per pose (standing, walking,
  running, turning, rolling, crouching, in the air), the same for both
  facings and at any speed, with one air range for the whole jump or
  fall through the frames after landing; which frames count as each
  pose; the count goes up 33 frames after the pickup, also for images
  partly off the left edge of the picture, with the rollover from 99
  giving a life. Also a synthetic example, evidence and a "Not covered
  in `title-2`" list; the existing "Not covered" list is marked as
  `title-1`'s.
- `TITLES.md`: the `title-2` row lists `collectibles.md`.

## spec-v0.94.0

A grounded player under a low platform (`title-1`).

Answers `spec` #14.

- `behavior/platforms-from-below.md`: new section "Walking under a
  platform", with "Falling through the floor". For a grounded player a
  column's floor is the ground below at the feet line. A platform only
  counts when the floor is the top line of its band; then a platform
  column whose top is 8 px up or less is blocked (face column) or
  stepped onto, and one higher than that makes the player fall through
  the floor, with a separate table for rolls in their first 9 frames.
  Also the can't-start rule, trace rows, a synthetic example, evidence
  and an updated "Not covered".
- `behavior/steep-faces.md`: a single 4 px step is not a face; columns
  4 or 8 px higher that aren't face columns are followed; "can't start"
  applies from 8 px (6 and 7 px not measured); the platform exception
  to "blocked at any height".

## spec-v0.93.0

Coverage: the view's vertical motion in `title-2`.

Answers no `spec` issue.

- `behavior/view-vertical.md`: new "In `title-2`": the view's state;
  the ground below the player and when it is in reach; the first
  level's vertical sway as steps and their alignment with the
  horizontal sway; the aim on the ground, rising, held and falling;
  the level's top and bottom limits; the rates and the step; still
  frames; three synthetic examples, evidence and "Not covered in
  `title-2`".
- `TITLES.md`: `view-vertical.md` added to the `title-2` files.
- `behavior/view.md`: the `title-2` not-covered line points to the new
  section.

## spec-v0.92.0

Coverage: a `title-2` reference trace for the swim-strokes scenario.

Answers no `spec` issue.

- `traces/reference/title-2/swim-strokes.trace` (new): 160 logic
  frames of open-water swimming following the `title-2` rules.
- `behavior/swimming.md`: describes the `title-2` trace in "In a trace
  (`title-2`)", points to it next to the `title-1` example, and removes
  it from "Not covered in `title-2`".
- `TITLES.md`: the `title-2` row lists `swim-strokes`.

## spec-v0.91.0

Coverage: open-water swimming in `title-2`.

Answers no `spec` issue.

- `behavior/swimming.md`: lists `title-2`. New "In `title-2`": the
  horizontal targets, including the faster targets after a frame with
  Action and a direction; the Up stroke and the deeper sinking limit
  with Down (kept while Up follows straight after); facing changes
  through a turn, with a table for a stroke during a turn; worked
  values, trace notes, checks and "Not covered in `title-2`".
- `TITLES.md`: `swimming.md` added to the `title-2` files.

## spec-v0.90.0

Coverage: placement lists with sections, and placement class 15.

Answers no `spec` issue.

- `formats/placements.md`: classes 9 and 15 in the class table; class 15
  references are matched by kind fingerprint like class 1's. New "Lists
  with sections": a class 9 first entry locates a section table whose
  8-byte records divide the list into sections and join each pair at a
  point, with the structure seen in the underwater lists and one shared
  list. "When placed objects appear": the window while `V` is less than
  32, and the class 15 vertical window. New "In a list with sections":
  which sections are open (the home section, a crossing record, the edge
  the view leaves by), when an entry appears (lead, clear entries, the
  home section waiting), what was seen of class 1 and class 2 entries
  outside the vertical window, an example, Checks and "Not covered"
  updates.
- `vectors/placements/`: a made-up list with sections, its records, a
  view path, and the entries that appear on each frame.

## spec-v0.89.0

Which areas the player swims in.

Answers `spec` #35.

- `behavior/swimming.md`: new section "Where the player swims".
  Swimming belongs to a whole area and is decided when the area begins;
  in a swimming area the player swims everywhere, with no water surface
  or boundary. In `title-1` every swimming area has a ceiling grid, no
  column-grid or row-grid area swims, and one ceiling-grid area is a
  riding bonus area where the player never swims. Adds the rule an
  importer can use, a Checks bullet, and updates "Not covered".
- `formats/level-ground.md`: "Ceiling grids" points to that section.

## spec-v0.88.0

Coverage: a swimming scenario with a `title-1` reference trace.

Answers no `spec` issue.

- `scenarios/swim-strokes.scn` (new): 160 frames in open water from a
  sinking start, with a plain, an Up and a Down stroke, sinking with and
  without Down, speeding up, slowing down and turning.
- `traces/reference/swim-strokes.trace` (new): the `title-1` reference.
- `behavior/movement-summary.md`: the new scenario in "Scenarios with
  reference traces".
- `behavior/swimming.md`: "In a trace" points to the scenario.

## spec-v0.87.0

Trace vocabulary for swimming.

Answers `spec` #34.

- `traces/VOCABULARY.md`: new state `swim` (swimming underwater: in open
  water, at a wall or on the floor) and new event `stroke` (a swimming
  stroke began this frame).
- `behavior/swimming.md`: new "In a trace" section: the state on every
  swimming frame, when `ground` is 1, `vy` on resting and leaving frames,
  which events appear while swimming and which never do, and a synthetic
  example. A new trace check under "Checks", and an updated "Not
  covered" list.

## spec-v0.86.0

Coverage: falls without a jump, and the airborne pose against walkers
(`title-1`).

Answers no `spec` issue (closes an item from the "Not covered" list).

- `behavior/walker-contact.md`: the vertical reach for being hurt now
  follows the player's pose (upright or airborne, as in
  `ball-contact.md`) and whether the player is on the ground, with
  corrected values for the medium walker in the airborne pose and for
  the frames after a landing. The airborne pose shortens the hurt range
  on the side behind the player. New "Poses" and "Falls without a jump"
  sections, worked examples, the evidence, and an updated "Not covered"
  list.
- `behavior/ball-contact.md`: a walk-off from a ledge without a face
  stays upright; the airborne pose after the same kind of jump can end
  on different frames.

## spec-v0.85.0

Coverage: the large walker against the fidget pose (`title-1`).

Answers no `spec` issue (closes an item from the "Not covered" list).

- `behavior/walker-contact.md`: the fidget pose shifts the large
  walker's hurt range the same way as for the small and medium walkers
  (both facings, both headings). A worked example for the large walker,
  the evidence, and the item dropped from "Not covered".

## spec-v0.84.0

Coverage: the medium walker on foot, and the fidget pose (`title-1`).

Answers spec#33 (the medium walker's contact with a player on foot).

- `behavior/walker-contact.md`: the medium walker joins the small and
  large walkers, with its own hurt and bounce ranges for I (both facings,
  both headings) and its vertical reach (early jump, later jump and
  bounce). The fidget pose (as in `ball-contact.md`) shifts the hurt
  range, measured for small and medium walkers. A worked example, the
  evidence, and an updated "Not covered" list (the medium walker on
  slopes, the large walker against the fidget pose).
- `behavior/riding-walkers.md`: the medium walker on foot is no longer
  listed as not covered.
- `formats/placements.md`: `medium-walker` now points to
  `walker-contact.md`.

## spec-v0.83.0

New specs (the echo, hidden objects) and three new object kinds.

In part for spec#20 (sound for the first level, `title-1`). spec#20 stays
open.

- `formats/echo.md` (new): the echo memory and its position, the filter
  over the 8-value history, feedback, the echo volume and how the echo
  is mixed into each output. When handed values take effect. In
  `title-1`: which song commands hand which values. Worked example,
  measurements and what isn't covered.
- `vectors/echo/` (new): 9 made-up cases with every output.
- `formats/README.md`: entry for `echo.md`.

Coverage (`title-1`). Answers no open `spec` issue.

- `behavior/hidden-objects.md` (new): placed objects that aren't drawn.
  One kind is struck by the ground action, two are set off by a long
  fall onto them. Covers the ranges, the pause, the released item's arc
  and what it becomes (a bunch, a barrel or a metal barrel), with an
  example, evidence and what isn't covered.
- New kinds `hidden-bunch`, `hidden-barrel` and `hidden-metal-barrel`,
  with fingerprint rows in `formats/locators.md`. `formats/placements.md`
  has the kind table and the updated first-level counts. Pointers were
  added in `behavior/ground-action-enemies.md`, `behavior/bounce.md` and
  `behavior/bunches.md`.

## spec-v0.82.0

New specs (`title-2` nibble packing and level ground, riding the animal
into walkers) and a new walker kind. Answers no open `spec` issue.

Coverage (`title-2`). Answers no open `spec` issue.

- `formats/nibble-packing.md` (new): how `title-2` packs its level maps:
  a header (run bytes, single bytes, a pair, stored pairs), then a nibble
  stream read high nibble first, with 16 commands (literals, runs, header
  bytes and pairs, copies from four kinds of distance, repeats). Worked
  example.
- `vectors/nibble-packing/` (new): 7 synthetic blocks.
- `formats/level-ground.md`: new section "In `title-2`": positions, the
  packed map columns, the type table (passable-from-below and flipped
  bits), shapes as in `title-1`, the ground below, a synthetic example,
  checks, the first level's data and what isn't covered.
  Vectors: `vectors/level-ground/title-2/`.
- `formats/rom-image.md`: `title-2`'s image added to the supported
  releases.
- `formats/locators.md`: new `title-2` section with the first level's
  packed map and type table.

Coverage (`title-1`). Answers no open `spec` issue.

- `behavior/riding-walkers.md` (new): the ridden animal and walkers on
  flat floor: attack range and heights by walker kind and facing, the
  automatic charge, throws (rider and walker), knock-off ranges and
  heights, the knock-off itself (frozen frames, flight, the animal
  running off), examples and what isn't covered.
- New walker kind `medium-walker`: two fingerprint rows that said
  `small-walker` now say `medium-walker`. `formats/placements.md` kind
  table and first-level counts updated; `behavior/walker-contact.md`
  says it doesn't cover medium walkers.

## spec-v0.81.0

New specs (`title-1` voice output, ropes, tyres, restart barrel, riding
the animal on flat floor, slopes and steep faces) and coverage
(`title-1` landings on or next to steep faces, landing switch frame).
Answers `spec` issue #20 in part.

Importer: sound (`title-1`), in part for spec#20
(https://github.com/clamber-engine/spec/issues/20). Uses the level from
`formats/voices.md` (spec-staging #87).

- `formats/voice-output.md` (new): how the voices become the console's
  audio output: a note's points and position (position 0 on the 7th
  output after the start's check, then up by the pitch mod 16 384 on
  each output), blending four points with weights from a formula (a
  windowed bell; the console's own weights are described, not copied,
  per the director's decision on spec#31,
  https://github.com/clamber-engine/spec/issues/31; they differ by at
  most 3), the voice's sound from the level of two outputs
  before, what is heard around a start, when blocks are taken (the loop
  block of the sample the voice has then; a one-shot sample ends when
  its last block is taken, so that block is never heard), volumes,
  mixing with limits and main volume; when handed values count (within
  three outputs, exact output not covered). Echo and noise not covered.
- `vectors/voice-output/` (new): 11 made-up cases with every output,
  computed with the formula's weights.

Coverage (`title-1`). Answers no open `spec` issue.

- `behavior/ropes.md` (new): ropes. The rope's sway (target flips past
  centre ± swing limit, speed moves 1/32 of the gap per frame); catching
  from a jump (feet line no more than 24 px below the rope's end, within
  12 px, or 4 px at speed 0.25 or more; the rope last held is skipped
  until out of reach for a frame); holding (whole px of the rope's `x`,
  climbing targets 1.5 up / 2.5 down, 2.5 / 3.5 with Action, Jump alone
  counts as Up, an eighth of the gap per frame, held down to 41 px below
  the view's top); turning (8 frames, facing on the 5th, remembered rope
  direction turns on a catch); jumping off (Jump press with a direction,
  `vy` −6.125 plus the climb step from Up/Down, run memory, air targets
  ±3); sliding off the end (24 px below it; fall with gravity 0.4375,
  facing kept); worked values; not covered.
- `behavior/movement-summary.md`: new section "Ropes".

Coverage (`title-1`). Answers no open `spec` issue.

- `behavior/tyre.md` (new): tyres. A tyre stays where placed; touching by
  I/U ranges against its top T (16 at rest); being caught after the
  player's move (on the ground and not taking off, or moving down and
  less than 16 px below the standing height), lifted to T + 2 with the
  fraction kept; the squash (T per frame, any touch puts the player back
  on top); walking off the top (8 px, then a shorter leave frame); the
  throw on C + 15 (`vy` −6, one frame with nothing changing, then 0.4375
  whatever the input; no catching for 8 frames); the wobble after it (T
  per frame, held while touched without a catch); the tyre jump (−10 on
  the press frame, 0.3125 while held, apex table; on the press frame
  after arriving on the ground, from C + 2 after arriving from the air,
  with a 9-frame window for presses made in the air); frozen frames;
  worked values; not covered.
- `formats/placements.md`: kind name `tyre`.
- `formats/locators.md`: 3 kind fingerprint rows for `tyre`.

Coverage (first level, `title-1`). Answers no open `spec` issue.

- `behavior/restart-barrel.md` (new): the restart barrel. Touching it
  (ranges of `I` and `D` by pose and facing, the poses of
  `ball-contact.md`); breaking (four pieces and a flash); the level's
  restart point (in the first level the barrel's position; it belongs
  to the level, not to the barrel); after a knock-out or a fall-out,
  play resumes there, also after a second loss, until the level is
  finished; resuming (frames 0 to 62 inside a barrel that shakes and
  bursts, the fall from frame 63, inputs from 63, the view on frame 0);
  example; not covered.
- `formats/placements.md`, `formats/locators.md`: kind
  `restart-barrel` and its kind fingerprint row (16 of the first
  level's 37 references now have one).
- `behavior/knock-out.md`, `behavior/falling-out.md`,
  `behavior/following.md`: point to it.

Coverage (`title-1`). Answers no open `spec` issue.

- `behavior/riding.md` (new): riding the animal on flat floor (after
  mounting, `animal-crate.md`). Target speeds 2, and 3.5 with Action
  held; the speed rule with a 32nd of the gap, or an 8th on steered
  frames (after a jump, until it lands, or through a charge started in
  the jump); turns at any speed; jumps (when a press starts one, presses
  remembered for 11 frames, 5 crouch frames, take-off on P + 5 with
  `vy` −7, `jump-arc.md` gravity, 8 landing frames); the Action charge
  (hop with `vy` −4 on C + 10, charge end from the first frame on the
  floor from C + 28, 12 frames, then a settling frame); Jump and Action
  together; examples; not covered.

Coverage (`title-1`). Answers no open `spec` issue.

- `behavior/riding-slopes.md` (new): riding the animal on 1-in-4 slopes.
  On the floor `y` follows the column under `x` on every frame, the rider
  never leaves the floor going downhill, and speeds, turns, jumps, charges
  and hops are as on flat floor. The first airborne frame of a jump or a
  hop starts from the higher of the last height and the new column's
  floor, and landings use the new column. Two measured examples and what
  isn't covered (steep faces, walls, ledges).
- `behavior/slopes.md`: "Jumping from a slope" (`title-1`) corrected:
  T + 1 starts from the higher of `y` on T and the floor of T + 1's
  column, as in `title-2`, not always from `y` on T.

Coverage (`title-1`). Answers no open `spec` issue.

- `behavior/riding-faces.md` (new): riding the animal on and near steep
  faces. On a frame that starts on a face (the take-off frame counts as
  ending on the floor), a held direction pointing uphill is ignored for
  the target speed and for turns. A push of 1.5 px per frame downhill is
  set at the end of each frame on a face (0 after an airborne or steered
  frame; kept when the target or `vx` points uphill) and added to the
  next frame's target unless that frame is steered. No jump starts on a
  face. Rising faces block as in `steep-faces.md`. The rider leaves the
  floor when the next column has no floor or is 12 px or more lower, and
  on every frame that starts on a face's low end over a drop; the leave
  frame drops 3 px plus the riding gravity. Walls in the air as in
  `air-walls.md`. A jump's landing takes effect on max(L, A + 5), with
  the approach frame A from the floor below (within 71 px and 2 rows of
  32 px), and riding resumes on max(L + 8, A + 14). Evidence, two worked
  examples, and its own "Not covered" list.

Coverage (`title-1`). Answers no open `spec` issue.

- `behavior/steep-faces.md`, "Landing on or next to a face" (new): after
  a jump, or a fall from a face, lands on or next to a face, the 1.5 px
  per frame downhill and "Can't start toward a rise" wait for a switch
  frame M (as in `jump-input.md`; M = L after a fall from a face). Plain
  landings use M from the stage frames; slide landings (A over a face
  column) have M 11 frames after reaching other floor; at the latest M is
  the first frame ending on a face column with `vx` 0 or downhill. The
  downhill 1.5 as a push set at the end of each frame and kept while the
  target or `vx` points uphill. Two measured examples, evidence, not
  covered. "Can't start toward a rise" and "Speed on a face" point to it;
  intro and "Not covered" updated. `behavior/movement-summary.md`: steep
  faces row.

Coverage (`title-1`). Answers no open `spec` issue.

- `behavior/jump-input.md`, "Landing lockout": E = M + 2, with the
  switch frame M (new section): the approach frame A (first frame from
  T + 20 with floor within 71 px and 2 bands below the feet), seven stage
  frames from A (spacing depends on whether A = T + 20), M from how many
  stage frames are L or earlier, or A + 4 for a landing before A.
  Examples for flat standing jumps; measurements. Landings not covered
  (after a roll jump, falls, bounces) keep E = L + 3 with ±1 tolerance.
  The buffered-press window counts back from E.
- `behavior/ground-speed.md`, "After a landing": running starts on M;
  otherwise the mode starts on K (M + 6 if M > L; L + 10 or L + 9 if
  M = L). The "Variation in the original" paragraph is replaced;
  tolerances kept for landings not covered.

## spec-v0.80.0

New specs and traces (`title-1` level map; `title-2` reference traces),
and a correction (`title-2` steep faces). Answers `spec` issue #30.

- `behavior/level-map.md` (new): walking on the level map. Map
  positions with fractions, paths as lists of points, legs at
  (end − start) / 64 px per frame per axis, each axis arriving on its
  own (moving toward smaller values: put back to the end's whole pixel,
  fraction kept), fractions carried from walk to walk; a worked example;
  standing frames, the remembered press (new presses only, 31 frames),
  the direction as the first of Right, Left, Up, Down among held and
  remembered buttons (no fallback); 256 px pages, no walk before 64
  frames after a page change or the map's fade-in; where the lead
  stands when the map appears. Progress not covered.
- `formats/level-map.md` (new): the level map's format. Point ids
  (corner ids 112–143, the rest stops), seven 256-byte point tables
  (page, `x` on the page, `h`, and each stop's neighbour for Right,
  Left, Up and Down), the appearance point 8 px right of a stop's
  position, corner records (two destination/next pairs each, `K` = 4 in
  `title-1`), and the route of a walk (first corner by its record
  naming the start, then next points toward the destination); example,
  measurements, not covered. Vectors: `vectors/level-map/` (new).
  `behavior/level-map.md` points to it. `formats/locators.md`: two
  `title-1` rows; `formats/README.md` lists it.
- Traces: new `reference/title-2/run-jump-flat.trace`,
  `reference/title-2/bounce-walker.trace` and
  `reference/title-2/slope-dip.trace`. `title-2` now has a trace for
  every scenario. `TITLES.md`: the `title-2` row lists them.
- `behavior/jump-arc.md` ("Example (`title-2`)"), `behavior/bounce.md`
  ("Example (`title-2`)") and `behavior/slopes.md` ("Examples
  (`title-2`)"): point to them. `slopes.md` notes that the `slope-dip`
  summary describes `title-1`, and how far a `title-2` test level
  continues the floor.
- `behavior/bounce.md` ("In `title-2`"): the landing frame's row is
  grounded with `land`, as in `title-1` (it said the state stays `bounce`
  on that frame); "Not covered" notes the trace's landing.

- `behavior/steep-faces.md`, "In `title-2`": the face limits are
  corrected. They come before the blocking test, and keep downhill
  speeds of 0.75 to 2.25 px per frame and any speed whose remainder
  modulo 256/256 is 0 or 193/256 to 255/256. Others become 2 downhill
  (576/256 or more downhill) or 0.75 downhill, every uphill speed
  included. New "Blocked face frames": a blocked face frame ends with `x`
  at the column edge and `vx` 0, and needs a kept uphill speed (seen on
  the first frame after a landing). Face columns are the columns of
  steep-face pieces; a lone 4 px step makes none. "Getting onto a face
  from above" becomes "Following the floor down": each piece's steepness
  gives a reach (3 px flat, 1-in-4, 1-in-2; 4 for 2-in-3; 5 for 1-in-1;
  6 for 2-in-1; 8 for a steep face). The floor is followed down by up to
  the reach, plus 3 px onto a sloped piece; otherwise L moves reach +
  0.4375. Examples, evidence and "Not covered" updated.
- `behavior/walk-off.md`, "In `title-2`": leaving at a step down; on
  L + 1 `vy` is 113/256 instead of 368/256 when the floor is 2.5625 px
  or less below.
- `behavior/slopes.md`, "In `title-2`": point to the new rule.

## spec-v0.79.0

New spec (`title-1` music). Answers `spec` issue #20 in part.

- `formats/voices.md` (new): a voice's level (0 to 2047) on each audio
  output: a count shared by all voices; rates 1 to 31 with their periods
  and when their steps are due; starts and releases taken at checks
  every other output (level held, then 0 for 4 outputs, then attack; a
  start right after a start is lost; release −8 each output); the fixed
  level 16 × gain; attack, decay and sustain from the envelope settings,
  with part changes worked out on every output. Vectors:
  `vectors/voices/`.
- `formats/songs.md`: points to it; "Not covered" updated.
- `formats/README.md`: lists it.

## spec-v0.78.1

Clarification (`title-2`). Answers `spec` issue #29.

- `behavior/roll-speed.md`, "Worked values (`title-2`)": the fast row's
  lead-in is spelled out (Action alone on frames 0 to 45, a slow roll
  that carries on and slows toward 0; Action and Right on frames 46 to
  65, `vx` 3.12890625 on frame 65; Right alone on frame 66, `vx`
  3.15625; S = 67). Added the contrast case with Action and Right held
  from frame 0 (`vx` 3.375 from frame 40; fast roll from 3.375). No rule
  changes.

## spec-v0.78.0

Coverage (`title-1`). Answers no open `spec` issue.

- `behavior/ground-speed.md`: new sections "After a landing" and "After
  a roll" under "Standing, walking and running". After a jump lands on
  L, frames L to L + 6 are in no mode: no turns, facing follows the
  input as in the air; running starts on L + 1 with Action and one
  direction held (no roll starting); otherwise on L + 7 running,
  walking or standing by what is held. The original sometimes runs on
  L and starts the mode on L + 9 or L + 10, by the hidden timing of the
  landing lockout; the spec defines L + 1 and L + 7, with tolerances.
  After a roll's stop the player is standing on D + 17; after carrying
  on, walking starts on D + 3 at step 1, whatever is held. Example,
  evidence; "Not covered" updated.
- `behavior/jump-input.md`: points to it.
- `behavior/movement-summary.md`: rows for both; "Not covered" updated.

## spec-v0.77.0

Coverage (`title-1`). Answers no open `spec` issue.

- `behavior/walk-off.md`: new section "Ledges without a face": the
  player is grounded exactly while their column has ground (nothing
  slows, stops or lowers them before the end); L is the first frame
  ending with the player's column past the end; `vx` on L follows the
  speed rule; a Jump press on L still starts a jump (T = L + 1, moving
  down 3 + g on L and 1/256 + g on T), a press on L − 1 takes off on
  L, presses from L + 1 do nothing (`coyote_frames` 1); examples,
  evidence. "Jumping near the edge" now says it is for ledges with a
  face; "Not covered" updated.
- `behavior/jump-input.md`, `behavior/air-control.md`,
  `behavior/movement-summary.md`: point to it.

## spec-v0.76.0

Coverage (first level, `title-1`). Answers no open `spec` issue.

- `behavior/animal-crate.md` (new): the animal crate and its animal.
  Breaking the crate (falling onto it, not rolling; ranges of `I` by
  facing and of `D` after the frame's move); the player bounces off it
  as in `bounce.md`; the animal comes out 18 frames later and hops for
  four frames; mounting (falling from a jump or a bounce, ranges taken
  from where the player would have been without the animal; snapped by
  whole pixels onto the animal, 22 still frames, riding from the 23rd);
  example; not covered (riding itself among them).
- `formats/placements.md`, `formats/locators.md`: kind `animal-crate`
  and its kind fingerprint row (16 of the first level's 37 references
  now have one).
- `behavior/bounce.md`: points to it.

## spec-v0.75.0

Answers https://github.com/clamber-engine/spec/issues/31.

- `REVIEW.md`: director decision (spec #31). Constant tables or other
  data measured from the console's own hardware are not transcribed into
  the spec; a spec describes what the hardware does in words, so the
  engine can compute an equivalent. A change that transcribes such a
  table fails.

Coverage (`title-2`). Answers no open `spec` issue.

- `behavior/view.md`: new section "In `title-2`": the view aims 56 px
  behind a player facing right and 128 px behind one facing left, and
  on the first level a sway of 0 to 15 px repeats every 256 frames. The
  view's speed takes a pull of (aim − view) / 16, limited to 96/256 px
  per frame, and is held to the frame's goal: how far the aim moved,
  plus a sixteenth of the lag. It stays still on B, B + 1, K and K + 1.
  Worked examples, evidence, and its own "Not covered" list.
- `TITLES.md`: `view.md` added to the `title-2` row.

Coverage (`title-1`). Answers no open `spec` issue.

- `behavior/stinger.md` (new): stingers, a flying enemy. Moving (per
  axis: past a turning point (start ± reach) put half a pixel inside it
  and the target speed changes sign; speed moves 1/32 of the gap to the
  target, horizontal before the move and vertical after it; floors don't
  stop it); appearing and removal by the walkers' window rule, coming
  back afresh; touching the player (I/A ranges per pose, the balls'
  ranges widened, facing left from 5 − high end to 5 − low end; never a
  bounce); still from H + 1 to H + 10 after a hit; worked values; not
  covered.
- `formats/placements.md`: kind name `stinger`.
- `formats/locators.md`: 14 kind fingerprint rows for `stinger`.
- `behavior/second-character.md`: new section "Rolls": the second
  character's rolls last 24 frames (25 when started on D + 17), sizes
  1.125, 3.375 and 4.5, a slow roll steered in its direction goes toward
  3, carrying on lasts to D + 6 (`roll_end`) and the next roll then
  starts no earlier than D + 7; the single-running-frame exception
  differs; facing in the stop; worked values. "Not covered" updated.
- `behavior/roll-input.md`, `behavior/roll-speed.md`,
  `behavior/movement-summary.md`: point to it.
- `behavior/swimming.md` (new): swimming in underwater levels. Target
  speed ±2 or 0 with steps of one thirty-second of the gap (rounded
  toward zero, the target when that is 0); facing follows Left or Right
  at once; a Jump press sets `vy` to −2, −2.5 with Up or −1 with Down;
  `vy` grows by 12/256 per frame up to 1.25, or 2 while Down is held;
  landing on, resting on and leaving the floor; walls (a move that
  would end 4 px or more in the ground), steps and ceiling pushes (1 to
  3 px), ceiling bumps, the level's left end; facing doesn't change on a
  frame stopped by a wall or the left end; worked values. The second
  character not covered.
- `behavior/movement-summary.md`: new section "Swimming".

Coverage (`title-1`). Answers no open `spec` issue.

- `behavior/rolling-off-ledges.md`: rolls off a ledge where the ground
  simply ends, both directions, slow, normal and fast: speed on L
  follows the roll; a hover when L is S + 8 or earlier (table by
  L − S); the roll ends on landing when the fall lasts exactly 30
  frames (D = G), otherwise D = G + 30 as before; new section
  "Stopping before a ledge": roll frames on the ground starting with
  `|vx|` ≤ 271/256 and no ground 15 px ahead are stopped, with a push
  of 2 px per frame from standstill when the roll's direction is held
  (the roll then goes on as a normal roll); examples, evidence, "Not
  covered" updated.
- `behavior/roll-speed.md`, `behavior/movement-summary.md`: point to it.

Coverage (`title-1`). Answers no open `spec` issue.

- `formats/level-ground.md`: new section "Ceiling grids", the third kind
  of grid (the levels where the player swims): stored like a row grid;
  its own floor shapes 1–30 (table) and ceiling shapes 32–63 (floor
  shape `n` − 32 upside down, ceiling value `u` = 31 − `v`, can be below
  0); the ground below a point in four steps (a ceiling at the feet, a
  floor at the feet, a floor at the top of the band with a ceiling,
  floor or nothing above, nothing at the feet with the kinds of the
  band below compared); no skipped columns, nothing "above the level";
  checks, example. Vectors `ceiling-*` in `vectors/level-ground/`.
  "Not covered" updated; the intro and `formats/README.md` name the
  third kind.

Importer: sound (`title-1`), in part for spec#20
(https://github.com/clamber-engine/spec/issues/20). Builds on
`formats/songs.md`.

- `formats/sound-effects.md` (new): the effect block (laid out like a
  song block) and its table (119 effects in `title-1`), the effect tempo
  (song header byte 17), effect slots, starting
  an effect (what is reset and what is kept), the order of channels and
  slots in a step, what a music channel does while its voice is taken,
  the end of an effect (release, noise off, echo from the channel's
  echo flag), song starts, and the voices the game uses (4 to 7).
- `formats/songs.md`: header byte 17 is the effect tempo; channels hand
  a taken voice nothing; "Not covered" updated.
- `formats/locators.md`: row "Sound: sound-effect block".
- `formats/README.md`: lists it.
- `vectors/sound-effects/` (new): a made-up song and 6 made-up effects,
  16 starts, and the events of 700 steps.

## spec-v0.74.0

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
