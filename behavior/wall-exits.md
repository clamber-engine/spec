# Exits in walls

Titles: `title-1`

A **wall exit** is an exit (`exits.md`) hidden in a wall: it does
nothing until a thrown object hits it, which **opens** it, and from then
on it takes the lead out of the area like an exit, walking into the
wall. The first level has two, both leading right. This file covers what
opens a wall exit, when an open one takes over, how the lead walks
out, and when the area is left, and how the lead comes back into the
first level from the area behind its first wall exit. What is in the
areas behind the wall exits is not covered.

Frames are logic frames at 60 Hz, numbered as in a trace. Horizontal
positions are level x as in `view.md`; vertical positions are **level
y** as in `view-vertical.md`, growing downward. `x`, `y` and `vx` are
the lead's values as in a trace; `X` is `x` rounded down. "Right",
"Left", "Down", "Jump" and "Action" are the neutral inputs `R`, `L`,
`D`, `J`, `A` of the scenario format.

Measured on the first level, with the lead alone. To have a barrel at
hand, the runs used copies of the image in which one entry of the
placement list was moved and given the reference of a `barrel`
(`../formats/placements.md`), placing a barrel on the floor a little
left of the wall exit (nothing else changed). Some runs started with the wall exit already opened that
way. See "Checks".

## The wall exit object

A level's placement list (`../formats/placements.md`) places each wall
exit with an entry of kind `wall-exit`. It doesn't move and is not
drawn. Its **position** (`xe`, `ye`) is its entry's level x and level y
("Positions" there).

In the first level each wall exit is at the foot of a rise of the
ground too high to walk up, a few px right of where the rise starts. The
floor left of it is 2 px above the exit's position (level y `ye` − 2).
The rise is ordinary ground: at the first wall exit, closed, a lead
walking right stops against it, as it does with the wall exit object
taken away.

Both of the first level's wall exits **lead right**. Which area comes
next is a setting of the object; the two lead to different areas.

## Closed

A wall exit is **closed** when it appears. A closed wall exit never
takes over, whatever the lead does next to it or how close it gets:
standing, walking, running, rolling, jumping, crouching, landing, or
carrying an object (`carrying.md`) up to the rise. Only a hit opens it.

## Opening

A closed wall exit is **hit** by an object of kind `barrel` that the
player has thrown (`thrown-objects.md`) and that is moving right. Let
**t** be the number of frames since the object left the hands, as in
`thrown-objects.md` ("When a walker is hit": t = 1 on Q + 22). Let
**I** be the object's `x` rounded down minus `xe`, and **U** how many
pixels the object's `y` is above the exit's (`ye` minus the object's
`y`), both at the end of the frame. The wall exit is hit on a frame
when the object is still there at the end of it (it hasn't broken) and

| t | I | U |
|---|---|---|
| 1 to 16 | from −49 to 25 | at least −50 and less than 67 |
| 17 or more | from −24 to 24 | at least −26 and less than 34 |

Both ends of the I ranges are included. So for its first 16 frames out
of the hands the object hits over a larger area, as for walkers.

An object carried by the player (not yet thrown) never opens a wall
exit, even in that range.

**When it opens.** The wall exit opens on the frame of the hit or on
the next frame. In every run at the first level's first wall exit it
opened on the frame of the hit, and in every run at the second one on
the next frame; what decides this in general is not covered.

**What happens.** When it opens, pieces are shown flying out of the
foot of the wall (how they move and are drawn is not covered). Nothing
in the ground changes. The object is not stopped by the hit: it flies
or rolls on, and can then break against the rise (`thrown-objects.md`,
"Breaking against a wall"). The wall exit is then **open**.

## When an open wall exit takes over

Let `D` = `xe` − `X` at the end of a frame (how far left of the exit
the lead is, in whole px). An open wall exit **takes over** on the
first frame that ends with all of these true:

1. the lead is on the ground (the landing frame of a jump or fall
   counts);
2. `vx` is 0 or more (the lead is still or moving right);
3. the lead's `y` is from 39 px above the exit's `ye` to 35 px below it
   (`y` − `ye` from −39 to 35);
4. `D` is at most the **reach** for the lead's state and facing on that
   frame:

| Lead's state at the end of the frame | Facing | Reach (largest `D`) |
|---|---|---|
| Standing, walking or running; the landing frame of a jump | right | 29 |
| Rolling | right | 27 |
| Crouching (Down held), still sliding or not | right | 32 |
| Standing, or turning (`ground-speed.md`) | left | 35 |

Call that frame **`T`**. A lead moving left is never taken over, however
close. A lead in the air is not taken over until the frame it lands.

This is the rule of `exits.md` ("When an exit takes over") turned
around for an exit leading right, with every reach 2 px shorter.

## The walk-out

From `T` on, the lead walks right on its own, as in `exits.md` ("The
walk-out") with the directions turned around:

- **On `T`** the lead moves as usual for that frame (its input counts).
  At the end of `T`, `y` becomes `ye` + 1, whatever it was, with no
  fraction.
- **On `T` + 1** `vx` becomes **2**, and `x` doesn't change.
- **From `T` + 2** `x` goes up by exactly 2 every frame. `y` doesn't
  change. The lead shows the walking pose.

From `T` + 1 on, inputs have no effect. The walk takes no account of
the ground: the lead keeps the same `y` and walks into the rise and
through the ground behind it.

**Facing.** A lead facing right on `T` keeps facing right. A lead facing
left on `T` (it was standing or turning) shows the pictures of a turn
from `T` + 2 and faces right from `T` + 6.

## Leaving the area

Call **`F`** the first frame after `T` that ends with

    X ≥ xe + 4

that is, with the lead at least 4 px right of the exit. In the runs `F`
was from `T` + 4 to `T` + 21, mostly `T` + 16 to `T` + 18.

- **The view.** The view's position `V` (`view.md`, `view-vertical.md`)
  and the picture's left edge (`level-ends.md`) move by their usual
  rules up to `F` − 1, and don't change from `F` on.
- **The fade.** The brightness of the whole picture (as in `exits.md`,
  "Leaving the area") is 15 on `F`, and frames `F` + 2`j` − 1 and `F` +
  2`j` have brightness 15 − `j`. It reaches 0 on `F` + 29.
- **The end.** `F` + 30 is the last frame in the area. The lead goes on
  walking right by 2 px every frame up to it. On the next logic frame,
  play is in another area (not covered).

## Coming back from the first wall exit's area

The first level's first wall exit (the one further left) leads to an
area of its own. What is in that area is not covered; walking right
through it to its right end brings play back to the first level. Frames
here are counted from 0, the first frame back in the first level, and
the lead was alone (as in all the runs of this file).

The lead starts at the point of the first level's **wall-return
record** (`../formats/level-start.md`), call it (`xw`, `yw`), and is
thrown out of the level's entry of kind `wall-return-entrance`
(`../formats/placements.md`, "Kinds"). That entry is not at the point:
in the first level it is 16 px left of it and 2 px lower. The record,
not the entry, decides where the lead is.

| Frame | What happens |
|---|---|
| 0 to T − 1 | The lead is at `x` = `xw`, `y` = `yw`, not drawn, and doesn't move. Inputs have no effect. |
| 41 | The entry's object bursts: a flash and four pieces appear, as for the level's entrance (`level-start.md`). |
| T | The entry's object is removed. The lead is drawn from this frame, but doesn't move yet: `vx` becomes **+3.75** (to the right) and `vy` **−2** (up). |
| T + 1 to T + 6 | The lead moves by `vx` and `vy` every frame, which don't change. |
| T + 7 on, up to and including the landing frame | Each frame, `vy` first increases by **0.4375** (7/16); then `vx` changes with the input; then the lead moves by `vx` and `vy`. |
| The landing frame | As in `landing.md`. |

**T** was frame **51** in every run, also with the record moved.

On frame T + `n`, for `n` from 0 to 6:

- `x` = `xw` + 3.75 `n`
- `y` = `yw` − 2 `n`

The top of the arc is on T + 10: `vy` is −0.25 on T + 10 and +0.1875 on
T + 11. In the first level, the lead landed on T + 23 in 199 of the 201
runs below and on T + 24 in the other two, 21 px below the point (`y` =
`yw` + 21), on the ground. `vy` didn't reach 8 in any run, so whether
it is held at 8 as in `level-start.md` is not covered.

From T to T + 6 every input is ignored. From T + 7, `vx` changes as in
`level-start.md` ("Steering"): each frame the lead has a target speed
set by what is held on that frame (±2 with Right or Left, ±3 with Action
too, 0 with no direction), and the speed moves toward it by the gap ÷ 32,
rounded toward zero, in 1/256 px per frame (the target itself if that is
0). So, unlike the level's entrance, the throw can be steered from its
seventh frame. With nothing held, the speed goes from 3.75 (960/256) to
930/256 on T + 7, then 901/256 on T + 8. The lead faces right from frame
0 up to and including the landing frame, whatever is held. From the
frame after the landing the usual rules apply.

The picture fades in from black as at the start of a level
(`level-start.md`, "The picture"): after frame `f` the brightness is
⌊`f` / 3⌋ fifteenths of full, full from frame 45.

### The entry in other cases

- **Without the entry.** In a copy of the image in which the
  `wall-return-entrance` entry was given the reference of a hidden
  object (`hidden-objects.md`), coming back left the lead hidden at the
  point, not moving, up to frame 110 (11 runs).
- **The level's other entries.** The `entrance` and the
  `room-return-entrance` (`level-start.md`) take no part: with both
  given a hidden object's reference, coming back was the same on every
  frame (11 runs).
- **Entering from the level map.** The entry's object appears as the
  view reaches it, like other placed objects, and is drawn: it covers
  part of the scenery, and in the copy without it that part of the
  scenery looked different while the object would have been in the
  picture. It never bursts, and it doesn't touch the lead: a lead
  walking and jumping past it (from about 400 px left of it to about 480
  px right of it) moved exactly as in the copy without it.

### Checks

- 201 runs coming back from the area, one with no input and 200 with
  random inputs from frame 0 (directions, Jump, Action, Down, Up, and
  Right or Left with Action, held for 1 to 20 frames each): every frame
  from 0 up to the frame before the landing (position, speed, facing)
  and the landing frame (`x`, `vx`, facing) as above, 15 077 frames.
- 11 runs each in three copies of the image: without the
  `wall-return-entrance`, with the `entrance` and the
  `room-return-entrance` given a hidden object's reference, and with the
  wall-return record moved (`../formats/level-start.md`), as above.
- With no input, the entry's object was there up to and including
  frame 50 and gone on frame 51, and the burst's flash and pieces were
  there from frame 41.
- Entering from the level map: a run along the level with and without
  the entry (in a copy of the image as above), the lead's position the
  same on every video frame from about 400 px left of the entry to about
  480 px right of it; the pictures differed only while the entry's
  object was in the view.

## Example

Synthetic, computed from the rules above, with a wall exit at `xe` =
500, `ye` = 300.

A barrel thrown right ends frame t = 3 at `x` = 449.6, `y` = 250: I =
449 − 500 = −51, out of range. It ends t = 4 at `x` = 460.4, `y` = 252:
I = −40 and U = 300 − 252 = 48, so the wall exit is hit on t = 4 and
opens on that frame or the next. The same position on t = 20 (I = −40)
would not be a hit: from t = 17 on, I must be −24 or more.

Later, a lead walking right with `vx` = 1.75 ends a frame at `x` =
470.5 (`D` = 30, out of reach while walking), and the next frame at `x`
= 472.25 (`D` = 28): that frame is `T`. At the end of `T`, `y` = 301. On
`T` + 1, `x` stays 472.25 and `vx` = 2; on `T` + `k` it is 472.25 +
2(`k` − 1). The first frame with `X` ≥ 504 is `T` + 17 (`x` = 502.25 on
`T` + 16, 504.25 on `T` + 17), so `F` = `T` + 17. The brightness is 15
on `T` + 17, 14 on `T` + 18 and `T` + 19, and 0 on `T` + 46 and
`T` + 47. `T` + 47, with `x` = 564.25, is the last frame in the area.

## Checks

- **Hit ranges, controlled frames.** A thrown barrel was put at a
  chosen position next to each closed wall exit for one frame (a change
  made for the test while the game ran), at t from 1 to 60, over 22 939
  positions 1, ¼ or 1/16 px apart: the wall exit opened exactly when
  the end-of-frame position was in the ranges above, and the ranges'
  edges were found there (I from −49, not −50, for t up to 16, and from
  −24, not −25, from t = 17, at both wall exits; U from −50 to 66.9375,
  not −50.0625 or 67, and from −26 to 33.9375, not −26.0625 or 34, at
  both; I up to 25, not 26, early at the second and up to 24, not 25,
  late at the first). The other ends of the I ranges couldn't be
  measured further at the other wall exit: there the object is inside
  the ground, where it breaks.
- **Throws by inputs alone.** 1 800 runs, 900 at each wall exit: the
  barrel picked up, carried right for a random time with random inputs,
  and thrown. 465 objects thrown right opened the wall exit, each on the
  frame the rule gives (232 at the first, on the frame of the hit; 233
  at the second, on the next frame), and 760 didn't, with no frame in
  range (36 174 frames of thrown objects judged). 120 objects thrown left
  opened nothing.
- **Closed.** 300 runs at the first wall exit, closed, with random inputs
  biased to the right (walking, running, rolling, jumping, crouching,
  stopping): 90 599 frames, 26 859 of them on the floor with `D` from 9
  to 29, 9 273 of those rolling; it never opened. 200 runs carrying
  the barrel right with random inputs: 74 978 frames, 12 827 with the
  carried object inside the first range above; it never opened.
- **Takeover.** 4 000 runs, 2 000 at each wall exit, already open, from
  a random point 32 to 95 px left of it, with random inputs biased to the
  right: 3 602 takeovers, every one on a frame ending on the ground with
  `vx` 0 or more; for each state in the table, `D` was at most the reach
  on every takeover and never at reach + 1 on a frame that met the other
  conditions. At the first wall exit, with the lead standing at `D` = 20
  and the exit moved up and down in steps of 1 px, the exit took over with `y` − `ye` from −39
  to 35 and not at −40 or 36.
- **Walk-out and leaving.** All 3 602 runs went on, with random inputs,
  until the area changed: 167 915 frames from `T` on. Every frame was as
  above except in 39 runs (see "Not covered"). 78 runs started with the
  lead facing left on `T`.
- **Other places.** Each of the two references given, in a copy of the
  image, to a class 1 entry near the first level's start placed a closed
  wall exit there: a lead walking right through it went on.

## Not covered

- What else opens a wall exit: objects thrown left (none of 120 opened
  one), other kinds of objects, the other character, and the animal
  (`riding.md`): riding the animal into the first wall exit was seen to
  open it, during a charge (in 18 runs), and the ridden animal then went
  in; when, and the walk-out of a lead riding the animal, are not
  covered.
- The other ends of the hit ranges where they couldn't be reached (see
  "Checks"), and what decides whether the wall exit opens on the frame
  of the hit or on the next frame.
- A lead taken over on the landing frame of a fall with `vx` 0 (it had
  landed against the rise): in 39 such runs `vx` was 2 on `T` + 1 but the
  lead then moved less than 2 px a frame, less on each frame, and `F` came
  later. The landing frame was judged only with `vx` above 0.
- The rolling stop (`roll-speed.md`) and other states not in the table.
- How far right of the exit the reach goes. In the first level a lead
  can't get there from the left: the rise is in the way.
- What is in the areas behind the wall exits and what happens there,
  how play comes back from the second wall exit's area, and what is
  kept when play comes back to the level.
- Coming back from the first wall exit's area with two characters, how
  the view moves then, and which picture the `wall-return-entrance`
  shows. Throws that land after T + 24 or meet a wall or a ceiling.
- Whether a wall exit opened before is still open when the view comes
  back to it after it was removed.
- The second character, and wall exits leading left.
- How the pieces are drawn, and the sounds.
