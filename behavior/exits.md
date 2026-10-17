# Exits

Titles: `title-1`

An **exit** is a placed object that takes the lead out of the area: once
the lead comes within its reach, the player loses control, the lead
walks away through the exit on its own, the picture fades to black and
play goes on in another area. This file covers when an exit takes over,
how the lead moves afterwards, when the area is left, and where the
first level's two exits lead: how the characters walk into the next
area, the short scene one exit leads to, how play comes back to the
level, and what of the level is kept then.

Frames are logic frames at 60 Hz, numbered as in a trace. Horizontal
positions are level x as in `view.md`; vertical positions are **level y**
as in `view-vertical.md`, growing downward. `x`, `y` and `vx` are the
lead's values as in a trace; `X` is `x` rounded down. "Right", "Left",
"Down", "Jump" and "Action" are the neutral inputs `R`, `L`, `D`, `J`,
`A` of the scenario format.

Measured on the first level, which has two exits, both leading left: one
on the level's floor near its left end, and one on higher ground above
it, reached by climbing. Most runs used the one on the floor. The runs
started with the lead standing a little right of it after the level's
entrance (`level-start.md`), alone or with the other character
following, and used random inputs (see "Checks").

## The exit object

A level's placement list (`../formats/placements.md`) places each exit
with an entry of kind `exit`. The exit doesn't move and is not drawn.
Its **position** (`xe`, `ye`) is its entry's level x and level y
("Positions" there).

Every exit measured **leads left**. What follows is for an exit leading
left; an exit leading right is not covered. Where an exit leads (which
area comes next) is a setting of the object; the first level's two
exits lead to different areas ("Where the first level's exits lead").

## When an exit takes over

Let `I` = `X` − `xe` at the end of a frame (how far right of the exit
the lead is, in whole px). The exit **takes over** on the first frame
that ends with all of these true:

1. the lead is on the ground (the landing frame of a jump or fall
   counts);
2. `vx` is 0 or less (the lead is still or moving left);
3. the lead's `y` is from 39 px above the exit's `ye` to 35 px below it
   (`y` − `ye` from −39 to 35);
4. `I` is at most the **reach** for the lead's state and facing on that
   frame:

| Lead's state at the end of the frame | Facing | Reach (largest `I`) |
|---|---|---|
| Standing, walking or running; the landing frame of a jump or fall | left | 31 |
| Rolling | left | 29 |
| Crouching (Down held) while still sliding | left | 34 |
| Standing, or turning (`ground-speed.md`) | right | 37 |

Call that frame **`T`**. Only the lead is taken over. In the runs with
two characters, the other character (`following.md`) was never taken
over; it went on following the lead.

A player moving right is never taken over, however close: a lead that
walks right past an exit and stops in its reach is taken over on the
frame `vx` becomes 0. A lead in the air is not taken over until the
frame it lands. Frames on which the lead is on a steep face above the
exit's floor (`steep-faces.md`), being carried down it, were not taken
over even with `y` − `ye` within the range of item 3.

How far left of the exit the reach goes is not covered (see "Not
covered"). In the first level, a lead coming from the right is taken
over before it gets there.

## The walk-out

From `T` on, the lead walks left on its own:

- **On `T`** the lead moves as usual for that frame (its input counts).
  At the end of `T`, `y` becomes `ye` + 1, whatever it was, with no
  fraction.
- **On `T` + 1** `vx` becomes **−2**, and `x` doesn't change.
- **From `T` + 2** `x` goes down by exactly 2 every frame. `y` doesn't
  change. The lead shows the walking pose.

From `T` + 1 on, inputs have no effect: no direction, Jump, Action or
Down held or pressed changes anything above. The walk takes no account
of the ground or of walls: the lead keeps the same `y` and passes through
whatever is in its way, and it is not stopped at the level's left end
(`level-ends.md`): `x` can go below 0.

**Facing.** A lead facing left on `T` keeps facing left. A lead facing
right on `T` (it was standing or turning) shows the pictures of a turn
from `T` + 2 and faces left from `T` + 6.

## Leaving the area

Call **`F`** the first frame after `T` that ends with

    X ≤ xe − 4

that is, with the lead at least 4 px left of the exit. In the runs `F`
was from `T` + 9 to `T` + 22, mostly `T` + 17 to `T` + 19.

- **The view.** The view's position `V` (`view.md`, `view-vertical.md`)
  and the picture's left edge (`level-ends.md`) move by their usual
  rules up to `F` − 1, and don't change from `F` on.
- **The fade.** The **brightness** of the whole picture, on a scale from
  15 (full) to 0 (black), is 15 on `F`, and goes down by 1 every second
  frame: frames `F` + 2`j` − 1 and `F` + 2`j` have brightness 15 − `j`.
  It reaches 0 on `F` + 29.
- **The end.** `F` + 30 is the last frame in the area. The lead goes on
  walking left by 2 px every frame up to it. On the next logic frame,
  play is in another area. For the first level's exits, which area that
  is and what happens there is in "Where the first level's exits lead".

## Where the first level's exits lead

The next area's frames are numbered from **0**, its first logic frame:
the logic frame after `F` + 30.

- **The floor exit** leads to a short **scene**: the characters walk in
  from the right ("Walking in"), the lead stops, and after a fixed time
  the picture fades out ("The scene"). Play then goes on in the first
  level again: the characters walk in from the left, starting from the
  first level's **scene-return record** (`../formats/level-start.md`),
  and the player has control from frame 28 as below. The floor exit is
  still there, a little left of that point, and it leads to the scene
  again each time it is reached.
- **The upper exit** leads to a **room**, a small area of its own where
  the player has control. The characters walk in from the right
  ("Walking in"). The room is left by walking right past its end marker
  (`level-end.md`, "In the first level's room"), not through an exit.
  After it, play goes on in the first level, which begins exactly as it
  does from the level map
  (`level-start.md`), at the first level's **room-return record**
  (`../formats/level-start.md`) instead of its start record.

Coming back from the room was checked frame by frame against the level's
own start: the lead's position, speed, state and facing were the same
on every one of the first 90 frames (see "Checks").

## Walking in

When the scene, the room or the level after the scene begins, the
characters walk in. Let (`xs`, `ys`) be the point of the area's start
record (`../formats/level-start.md`), and `d` the direction of the
walk: −1 (left) into the scene and the room, +1 (right) back into the
level after the scene.

- **Frames 0 to 26.** On frame `k` the lead is at `x` = `xs` + `d` ×
  (3`k` − 80), `y` = `ys`, with no fraction of a px: it walks 3 px per frame
  in the direction `d`, facing that way from frame 1 (frame 0 is black,
  see "Fade-in").
- **Frame 27.** The lead moves 6 px: `x` = `xs` + 4`d`.
- **Frame 28 on.** The player has control. On frame 28 the lead is on
  the ground with a speed of 3 px per frame in the direction `d` (`vx` =
  3`d`, that is ±768/256) and moves by the usual rules
  (`ground-speed.md`), with that frame's input: with nothing held, for
  example, `vx` becomes ±2.625 on frame 28.
- **Input.** Nothing held or pressed on frames 1 to 27 has any effect
  (frame 0 was not tried with input).
  A Jump press on frame 28 or later works as usual (`jump-input.md`);
  Jump or Action held down from before frame 28 is not a press on 28.
- **The other character**, if there is one (`following.md`), walks in
  with the lead: on frames 0 to 26 it is 28 px behind (`x` − 28`d`,
  same `y`) and moves the same 3 px per frame, and on frame 27 it
  doesn't move. Afterwards it follows the lead (not checked frame by
  frame).
- **Fade-in.** The brightness (as in "Leaving the area") is `j` on
  frames 3`j`, 3`j` + 1 and 3`j` + 2 for `j` from 0 to 14, and 15 from
  frame 45 on.
- **The view** doesn't change on frames 0 to 26. Where it is on frame 0
  and how it moves from frame 27 on are in `view.md` ("When an area
  begins"), measured walking back into the first level. In the room and
  the scene the picture doesn't move (`level-ends.md`).

In the scene the lead stops after frame 28 ("The scene"); in the room
and in the level, play simply goes on.

## The scene

- **Frames 0 to 28** are as in "Walking in", walking left.
- **From frame 29** the lead stands still, facing left, at the `x` it
  had at the end of frame 28, to the end of the scene. Nothing held or
  pressed has any effect. The other character, if there is one, stays
  where it was on frame 27.
- **Fade-out.** The brightness is 15 on frame 366 and goes down by 1
  every second frame: frames 365 + 2`j` and 366 + 2`j` have brightness
  15 − `j`. It is 0 on frames 395 and 396.
- **Frame 396** is the scene's last frame, whatever was pressed. On the
  next logic frame, play is back in the first level (frame 0 of
  "Walking in", walking right from the scene-return record).

The scene's data is in `../formats/graphics.md`,
`../formats/level-ground.md`, `../formats/placements.md` and
`../formats/view-limits.md` ("The first level's scene"), its start
record in `../formats/level-start.md` ("the scene's start record"), and
its view in `level-ends.md` ("In the first level's scene"). Of the
scene's placed objects, one is the room's end marker and one a sign
(`signs.md`, "The scene's sign"); what the third is, and the pictures
the characters show in the scene, are not covered.

## Coming back to the level

When play comes back to the first level after the scene or the room
(frame 0 of the walk-in from the scene-return record, or of the level's
start from the room-return record), some of what happened in the level
before the exit took over is kept and some is not.

Kept as it was when the area was left:

- the collectible count (`collectibles.md`, `count-display.md`) and the
  lives;
- the set of letters taken (`letters.md`);
- the figure count (`figure.md`);
- whether the level's restart point has been reached
  (`restart-barrel.md`): after coming back, a knock-out resumes at the
  restart point if the barrel was touched before the exit took over, and
  play begins at the level's start if it wasn't.

Still gone: a **bunch** taken (`bunches.md`), a **letter** taken, the
**restart barrel** once touched, the **figure** taken (`figure.md`), an
**extra-life item** taken (kinds `rising-extra-life-1` and
`rising-extra-life-2`, `extra-lives.md`), and a **hidden bunch** (kind
`hidden-bunch`, `hidden-objects.md`) once struck, together with the
bunch it released, whether that bunch was taken or not. The **animal
crate** (kind `animal-crate`, `animal-crate.md`) once broken is also
still gone, and so is the animal that came out of it if it was left
there and not mounted: neither the crate, its broken pieces nor the
animal is at its place after coming back, so nothing is there to land on
or to mount. A **holder** (kind `holder`, `holder-breaking.md`) once
broken is still gone too, and the character it freed is still there:
after coming back that character follows the lead as it did before the
exit (`following.md`).
They stay gone
after coming back. They don't appear when the view comes back over their
places, and nothing is taken or struck there again: the count, the
lives, the figure count and the set of letters don't change. For these
objects, "the rest of the visit to the level" in those files goes on
through the scene and the room.

There again, as when the level begins:

- **single collectibles** (`collectibles.md`) taken before: they are
  drawn again and can be taken again, each adding one to the count
  again. For them, coming back starts a new visit to the level;
- a **small walker** (kind `small-walker`) bounced on before
  (`walker.md`, "Being bounced on"): it is there again when the view
  comes to its place, walking as the level places it;
- a **medium walker** (kind `medium-walker`) bounced on before: it is
  there again in the same way;
- a **large walker** (kind `large-walker`) bounced on before: it is
  there again in the same way. This held for both large walkers of the
  first level, each bounced on before leaving. Without an exit, a large
  walker bounced on was not there again when the lead went far away and
  came back within the same visit (one run);
- the **thrower** (kind `thrower`, `thrower.md`) knocked out by the
  ground action before (`ground-action-enemies.md`, "The thrower"): it
  is there again when the view comes to its place, as the level places
  it. Without an exit, the thrower knocked out was not there again when
  the lead went far away and came back within the same visit (one run);
- a **barrel** (kind `barrel`, `carrying.md`) picked up, thrown and
  broken against a wall before (`thrown-objects.md`, "Breaking against a
  wall"): it is at its place again when the view comes there, standing
  as the level places it;
- a **barrel** picked up and put down unbroken somewhere else
  (`carrying.md`, "Putting down"): it is at its place again in the same
  way, and nothing is where it was put down;
- a **barrel** still carried when the exit takes over: the exit takes
  over as usual and the barrel is still there, in the level, as the
  walk-out begins (how it moves during the walk-out is not covered). It
  is not in the scene or the room, and the lead carries nothing when the
  next area begins. After coming back it is at its place again in the
  same way.
- a **holder** (kind `holder`, `holder-breaking.md`) picked up and put
  down unbroken somewhere else: it is at its place again in the same
  way, still holding the missing character, and nothing is where it was
  put down. The missing character is still missing after coming back;
- a **holder** still carried when the exit takes over: as for the
  barrel, the exit takes over as usual and the holder is still there, in
  the level, as the walk-out begins (how it moves during the walk-out is
  not covered). It is not in the scene or the room, it doesn't break,
  and the lead carries nothing when the next area begins. After coming
  back it is at its place again, still holding the missing character.

**Riding into the exit.** A lead riding the animal (`riding.md`) when
the exit takes over keeps it through the exit and after coming back:

- **The walk-out** is ridden, and is the same frame for frame as on
  foot ("The walk-out"): at the end of `T`, `y` becomes `ye` + 1; on
  `T` + 1, `vx` becomes −2 and `x` doesn't change; from `T` + 2, `x` goes
  down by exactly 2 every frame. The area's last frame comes as many
  frames after `T` as for a lead on foot. The animal stays under the lead
  all the way, at the lead's `x` in whole px.
- **In the scene and the room** the lead is on foot and the animal isn't
  there. Every frame there is as for a lead that left on foot.
- **Back in the level** the lead is riding the animal from frame 0 of
  the next area, and the animal is at the lead's `x` in whole px from
  frame 1. After the scene, frames 0 to 27 of "Walking in" are exactly
  as given there. From frame 28 the player has control and the speed
  follows `riding.md` ("Horizontal speed"), not `ground-speed.md`: with
  nothing held, `vx` goes from 768/256 to 744/256 on frame 28 and to
  721/256 on frame 29. After the room, the level begins as in
  `level-start.md`, with the same position and speed on every frame up
  to and including the landing frame. From the next frame the speed
  follows `riding.md` in the same way: with nothing held, 350/256 on the
  landing frame became 340/256.
- After either exit, Right and Jump then worked as in `riding.md`
  (a ridden jump). There is no second animal: neither the crate, its
  broken pieces nor another animal is at the crate's place.

**Knocked off before the exit.** If the rider was knocked off the animal
(`riding-walkers.md`, "Being knocked off") before the exit took over,
the animal is gone after coming back through either exit: it is neither
at the crate's place nor where it was running, and no crate or broken
pieces are there either. This holds whether the animal was still in the
level, running away in the view, when the exit took over, or had already
run out of it. The animal is not in the walk-out, the scene or the room,
and the lead is on foot throughout.

Other kinds of object are not covered (see "Not covered").

## Example

Synthetic, computed from the rules above, with an exit at `xe` = 300,
`ye` = 200.

A lead walking left with `vx` = −1.75 ends a frame at `x` = 333.5 (`I` =
33, not in reach while walking), and the next frame at `x` = 331.75
(`I` = 31): that frame is `T`. At the end of `T`, `y` = 201. On `T` + 1,
`x` stays 331.75 and `vx` = −2; on `T` + 2 it is 329.75, and on `T` + `k`
it is 331.75 − 2(`k` − 1). The first frame with `X` ≤ 296 is `T` + 19
(`x` = 297.75 on `T` + 18, 295.75 on `T` + 19), so `F` = `T` + 19. The
brightness is 15 on `T` + 19, 14 on `T` + 20 and `T` + 21, 1 on
`T` + 46 and `T` + 47, and 0 on `T` + 48 and `T` + 49. `T` + 49, with
`x` = 235.75, is the last frame in the area.

Walking in, synthetic, with a start record point (`xs`, `ys`) = (400,
150), walking right (`d` = +1): the lead is at `x` = 320 on frame 0, 323
on frame 1, 398 on frame 26 and 404 on frame 27, always at `y` = 150. An
other character is at 292 on frame 0 and 370 on frames 26 and 27. The
brightness is 0 on frames 0 to 2, 1 on frames 3 to 5, 14 on frames 42 to
44 and 15 from frame 45. With nothing held, frame 28 has `vx` = 2.625
and `x` = 406.625; with Right held, `vx` = 2.875 and `x` = 406.875.

## Checks

- **Takeover.** 400 runs from a random point 40 to 92 px right of the
  floor exit, with random inputs biased to the left (walking, running,
  rolling, jumping, crouching, stopping, short presses of Right):
  37 680 frames judged by the rules above, 329 takeovers (288 by the
  floor exit, 41 by the upper one, reached by climbing), no frame
  different. A second, held-out set of 200 runs from 34 to 116 px right
  of it: 21 575 frames, 144 takeovers, none different. Frames in states
  not in the table, with `I` in reach, were not judged (see "Not
  covered").
- **Reach edges.** For each state in the table, the lead was put at
  every whole-px `I` around the edge, with fractions 0 to 0.99, after
  being brought into the state away from the exit: taken over up to the
  reach, never at reach + 1. A lead moving right (walking, or turning
  with `vx` still above 0) was never taken over. A lead put 30 px right
  of the exit moving right at 0.6 px per frame, with no input, slowed
  down and was taken over on the first frame its `vx` was 0, 34 px
  right of it.
- **Vertical range.** With the lead standing on the floor facing right
  at `I` = 36, and the exit moved up and down in steps of 1 px (a change
  made for the test while the game ran), the exit took over with
  `y` − `ye` from −39 to 35 and not at −40 or 36.
- **Walk-out and leaving.** 325 runs that went on until the area changed
  (273 with the lead alone, 52 with the other character following; 289
  through the floor exit, 36 through the upper one), with random inputs
  (Left, Right, Jump, Action, Up, Down, Right with Jump or Action) on
  12 193 of the 15 466 frames from `T` + 1 on: every frame was as above
  (35 726 frames). Three runs started a turn: the lead faced right on `T`.
- **Other places.** In copies of the image in which an entry far from
  the left end was given one of the exits' references, as a class 1 or a
  class 2 entry (three copies), an exit was there: a lead walking right
  past it went on, and walking back left it was taken over with `I` =
  30, walked out, and left the area on `F` + 31, `F` being the first frame
  with `X` ≤ `xe` − 4. Each reference led to the same area as in its own
  place.
- **Walking in and the scene.** 700 runs with random inputs (Left,
  Right, Jump, Action, Up, Down, and Left or Right with Jump or Action)
  from frame 1 of the next area: 300 through the floor exit with the lead
  alone and 100 with the other character following, each through the
  scene and back into the level, and 300 through the upper exit into the
  room. 1 100 walk-ins: every frame 0 to 27 of the lead (29 700 frames)
  and of the other character (5 400 frames) was as in "Walking in", and
  the brightness of frames 0 to 47 as well (52 800 frames). The view
  didn't change on frames 0 to 26 in any of them. Frame 28 was judged
  in the 621 walk-ins with only Left, Right or Action held on it (and
  no new Action press): `vx` and `x` were as given by `ground-speed.md`
  from `vx` = 3`d` in all of them. In all 400 scenes the lead stood
  still from frame 29, the fade-out was as above, frame 396 was the
  last, and play went on in the level after it. In some runs the lead
  walked back into the floor exit afterwards and went through the scene
  again.
- **The records decide where.** In a copy of the image with the room's
  start record moved 16 px left, the scene-return record moved 40 px
  right and the room-return record moved to another point, each arrival
  started from the moved record (frames 0 and 27 as in "Walking in", and
  the level's start as in `level-start.md` from the moved room-return
  point); the first level's own start record was left as it was.
- **Coming back from the room.** 41 runs leaving the room with Right
  held, one with no input afterwards and 40 with random inputs from
  frame 1: the lead was the same as at the level's own start from the
  level map (`level-start.md`, no input) on every frame 0 to 89 (3 690
  frames).

- **Coming back.** 16 runs with the lead alone, eight through each
  exit, each run going through the scene or the room and back. Four sets
  of objects: one single collectible, a bunch and the first letter; a
  second bunch, the second letter and the restart barrel; the third and
  fourth letters and a third bunch; one small walker. For each exit and
  set, one run took the set (or bounced on the walker) before leaving
  and one, the control, didn't; after coming back, both runs went to
  each object again. In all 16 the count, the lives, the set of letters
  and the restart point were the same on coming back as on leaving. Runs
  that had taken a set took nothing again except the single
  collectible (the count went up by one); each control took the whole
  set. The restart barrel was there again only in the controls, and a
  knock-out afterwards resumed at the restart point only in the runs
  that had touched it. The small walker was there again after both
  exits. 88 checks in all, none different.
- **Coming back, more kinds.** 26 more runs with the lead alone, 13
  through each exit, in five sets: two hidden bunches on different
  floors, each struck by the ground action and its bunch either left
  where it landed or taken; the two rising extra-life items; the figure
  (with the thrower and its balls taken out of the level for these runs,
  so that the lead wasn't hurt); one medium walker bounced on. Each set
  had a control that did nothing before leaving. After coming back, each
  run struck, took or went to the same objects again. The count, the
  lives, the figure count, the set of letters and the restart point were
  the same on coming back as on leaving in all 26. The runs that had
  struck a hidden bunch got nothing from it again; each control got its
  bunch. The runs that had taken the extra-life items or the figure got
  no lives or figure again; the controls got 3 lives and one figure. The
  medium walker, gone after the bounce, was there again after both
  exits, as in its control. 146 checks in all, none different.
- **Coming back, barrel and crate.** 8 more runs with the lead alone, 4
  through each exit: the barrel picked up, carried left and thrown into
  the wall left of it, where it broke; the animal crate landed on, so
  that it broke and the animal came out and was left waiting; and a
  control for each that did nothing before leaving. After coming back,
  each run went to the barrel's place, or to the crate's place to land
  on the crate again if it was there. The count, the lives, the set of letters and the
  restart point were the same on coming back as on leaving in all 8. The
  barrel was at its place again in all 4 barrel runs. In the 2 runs that
  had broken the crate, neither the crate nor the animal was there, so
  there was nothing to land on and no animal came out; in the 2 controls
  the crate was there and broke, and its animal came out. The same four
  runs without an exit (going straight to the places instead): the
  broken barrel was not there again, and the animal was still waiting by
  the broken crate. 47 checks in all, none different.
- **Coming back, moved barrels.** 6 more runs with the lead alone, 3
  through each exit: the barrel picked up, carried left and put down on
  flat floor; the barrel picked up and still carried, with Action held,
  into the exit (the lead standing still within reach when the exit took
  over); and a control that did nothing before leaving. The count, the
  lives, the set of letters and the restart point were the same on
  coming back as on leaving in all 6. In both carried runs the barrel
  was there on every frame up to the walk-out's first frame and was not
  in the scene or the room, and the lead wasn't carrying when the next
  area began. After coming back, the barrel was at its place again in
  all 6, and nothing was at the put-down place. The put-down run without
  an exit (the lead going far enough away for the barrel to be removed,
  then back) also found the barrel at its place again, as in
  `../formats/placements.md` ("Objects put down elsewhere"). 45 checks in all, none different.
- **Coming back, large walkers.** 5 more runs with the lead alone, 2
  through each exit and one without an exit: the lead standing in the
  path of the first large walker until it came near and then bounced on
  it, then bounced on the second as soon as it came into view, so that
  both were gone without the lead being hurt; and a control for each
  exit that did nothing before leaving. After coming back, each run went
  to both walkers' places. The count, the lives, the set of letters and
  the restart point were the same on coming back as on leaving in all 4
  exit runs. Both large walkers were there again after both exits, as in
  the controls. In the run without an exit (the lead going far left for
  120 frames, then back) neither was there. 29 checks in all, none
  different.
- **Coming back, the thrower.** 5 more runs with the lead alone, 2
  through each exit and one without an exit: the lead put on the
  thrower's ledge, facing it, 45 px left of it, and making the ground
  action, so that the thrower was knocked out without the lead being
  hurt; and a control for each exit that did nothing before leaving. In
  all 5 the balls were taken out of the level, so that they couldn't
  hurt the lead. After coming back, each run went to the thrower's
  place. The count, the lives, the set of letters and the restart point
  were the same on coming back as on leaving in all 4 exit runs. The
  thrower was there again after both exits, as in the controls. In the
  run without an exit (the lead going far left for 120 frames, then
  back) it was not there. 29 checks in all, none different.
- **Coming back, the holders.** 8 more runs, each starting with the
  lead alone: for each of the first level's two holders, one run through
  each exit and one without an exit in which the lead picked the holder
  up and threw it, so that it broke and the missing character joined;
  and a control for each exit that did nothing before leaving. In all 8
  the balls were taken out of the level, so that they couldn't hurt the
  lead. After coming back, each run went to both holders' places. The
  count, the lives, the set of letters and the restart point were the
  same on coming back as on leaving in all 6 exit runs, and the lead was
  never hurt. In the 4 exit runs with a holder broken, the character it
  freed was following the lead before leaving, was next to the lead as
  the next area began and was still following it after coming back; the
  broken holder was not there again, and the other holder was. In the
  controls both holders were there and the character stayed missing. In
  the 2 runs without an exit (the lead going far away for 120
  frames, then back) the broken holder was not there either. 100 checks in all,
  none different.
- **Coming back, holders moved.** 12 more runs, each starting with the
  lead alone and with the balls taken out of the level. For each of the
  first level's two holders: one run through each exit in which the
  lead picked the holder up, carried it some way off and put it down
  unbroken; one run through each exit in which the lead was still
  carrying it as the exit took over; and one run without an exit in
  which it was put down, the lead went far away for 120 frames and came
  back to the holder's place. Two controls did nothing before leaving.
  In the 10 exit runs the count, the lives, the set of letters and the
  restart point were the same on coming back as on leaving, the lead was
  never hurt before leaving, and the missing character stayed missing
  throughout. In the 4 carried runs the walk-out began with the holder
  still there, and no holder was in the scene or the room. After coming
  back, in every run, both holders were at their places and nothing was
  where a holder had been put down. In the 2 runs without an exit the
  holder put down was back at its place when the lead came back to it,
  as "Objects put down elsewhere" in `../formats/placements.md` gives.
  156 checks in all, none different.
- **Riding into the exit.** 5 more runs, each starting with the lead
  alone and with the balls taken out of the level. In each, the lead
  broke the animal crate and mounted the animal with a jump from the
  boards. It rode through each exit with nothing held, or stayed in the
  level without an exit. Two controls for the exits broke the crate and
  left the animal waiting. In both exit runs, the walk-out frames were
  as above, with the rider and the animal together every frame. The
  walk-out lasted as many frames as in the control, and the scene or room
  frames were the same as in the control. Every frame of the first 200
  after coming back was riding, with one animal. Positions and speeds
  matched the control up to frame 27 (scene) or the landing frame (room),
  and on every later frame the speed was as `riding.md` gives with
  nothing held. Then the rider rode right for 40 frames and jumped. The
  lives were kept. In the controls no animal was there after coming
  back. Without an exit, the lead was still riding and the boards were
  still there. 54 checks in all, none different.
- **Knocked off before the exit.** 5 more runs, each starting with the
  lead alone and with the balls taken out of the level. In each, the
  lead broke the animal crate, mounted the animal, rode for 30 frames
  and was then put, riding, right next to a small walker, so that the
  rider was knocked off. In 2 runs the
  exit took over 60 frames after the knock-off, with the animal still
  in the level about 150 px from the lead and running away; in 3 runs
  the lead was first held still out of the walkers' reach for 400
  frames, and by then the animal was gone. These 3 were one through
  each exit and one without an exit. In every run the lives were kept,
  no animal was there on any frame while leaving, in the scene or room
  or in the first 200 frames after coming back, and the lead was never
  riding. Neither an animal nor the crate's boards were at the crate's
  place or along the animal's run, after either exit or without one.
  59 checks in all, none different.

## Not covered

- Exits leading right (other than the wall exits of `wall-exits.md`),
  exits in other levels, and where an exit leads in general: which area comes next is a setting of
  the object (its definition, `../formats/placements.md`), and how an
  area is entered (walking in, and which way, or as a level begins) is
  given above only for the first level's areas.
- The kind of the scene's class 2 entry that isn't the end marker, and
  the pictures the characters show in the scene ("The scene"). The room's
  data is in `../formats/graphics.md`, `../formats/level-ground.md`,
  `../formats/placements.md` and `../formats/level-start.md` ("The
  first level's room" or "the room's start record"), and its view in
  `level-ends.md` ("In the first level's room"); the kinds of the
  room's placed objects other than the tyre and the end marker are not
  covered.
- What else is kept when play comes back to the level after the scene
  or the room: tyres moved, the
  hidden objects set off by a fall and their items,
  an animal left after being mounted other than by a knock-off by a
  walker, a thrower bounced on
  or rolled into, the `extra-life-3` items, objects of kinds the spec doesn't
  name, and objects other than a barrel or a holder carried through the
  exit. How a barrel or a holder carried into the exit moves during the
  walk-out. Whether a broken barrel
  ever comes back during the same visit without an exit (it didn't in
  the one run above). Coming back with two characters, other than
  the character freed by a holder broken in the same visit (the other
  runs in "Coming back to the level" had the lead alone).
- Whether bunches and letters taken before a knock-out are there when
  play resumes (`restart-barrel.md`).
- Riding into the exit with anything held during the walk-out or the
  walk-in, or with the second character; whether the animal is drawn
  on frames 0 to T − 1 of the level's start after the room
  (`level-start.md`), and how the rider and the animal are drawn during
  the walk-out and the walk-in.
- The other character after frame 27; two characters walking into the
  room (only the lead alone was measured there).
- How far left of the exit the reach goes, and the reach of states not
  in the table: rolls stopping or getting up (taken over at `I` up to 28
  to 30, by picture), crouching after the slide has stopped (up to 28 in
  the cases measured), turning while facing left, carrying an object,
  riding, and the frames of a landing after the landing frame. How far
  the vertical range changes with the lead's state.
- What the other character does from `T` on. In the runs it went on
  following the lead (`following.md`) and was in the next area with it;
  this was not checked frame by frame.
- What a trace shows from `T` on (`../traces/VOCABULARY.md` has no state
  for the walk-out), and which picture shows each step of the fade.
- Walkers, thrown objects or other objects meeting the lead during the
  walk-out, and the lead being hurt or knocked out then.
- Pausing during the walk-out.
