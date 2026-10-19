# Finishing a level

Titles: `title-1`

A level is finished by walking past its **end marker**. From then on the
player has no control: the lead walks right on its own, the picture
fades to black, and the level is over. This file covers when the
marker is passed, how the characters move afterwards, when the fade
starts and how long it lasts.

Frames are logic frames at 60 Hz, numbered as in a trace. `x` grows to
the right. Horizontal positions are level positions as in `view.md`;
`V` is the view's position there (the level x of the picture's left
column). Vertical positions are **level y** as in `view-vertical.md`:
they grow downward, measured from the top of the level. The lead's
**feet line** `F` is as in `view-vertical.md`. "Right", "Left", "Up",
"Down", "Jump" and "Action" are the neutral inputs `R`, `L`, `U`, `D`,
`J`, `A` of the scenario format.

Measured on the end of the first level with the first character
leading, alone and (one run) with the second character following. The
lead reached the marker by inputs only: walking, running, rolling,
landing from a jump, about 100 runs of small steps from standing that
ended at positions 1/8 to 2 px apart, and standing past the marker. For
the marker's range the marker was also moved, up to 9 px up or down and
up to 400 px left, with the lead walking or standing still; for the
walk and the fade it was moved so that the lead passed it with the view
at the level's end and 96 px before it. About 240 runs in all. With
the second character leading and the first following, 320 runs with
random inputs, 78 runs of small steps and 10 with the marker moved ("The
second character leading").

## The end marker

A level's placement list (`../formats/placements.md`) places its end
marker with an entry. The marker does not move and is not drawn. Its
**position** (`xm`, `ym`) is the entry's level x and level y (see
"Positions" there). An entry places an end marker when its reference's
kind is `end-marker` ("Kinds" there).

The first level's room has an end marker too: it is the room's way out
("In the first level's room"). So has the area behind the first
level's first wall exit ("In the area behind the first wall exit").

## When the marker is passed

Using whole pixels, let `X` be the lead's `x` rounded down.

The lead passes the marker on frame `n` when all of these hold **at the
end of frame `n`**, after the lead has moved on that frame:

1. Right was held on frame `n` and Left wasn't (with or without other
   inputs; see below);
2. the lead is on the ground (`ground` is 1);
3. `X` ≥ `xm`;
4. `F` − `ym` is from −4 to 4 (the lead's feet line is at most 4 px
   above or below the marker's position).

There is no limit on the right: a lead standing 128 px right of the
marker passed it on the first frame Right was held.

Call the next frame, `n` + 1, **`T`**: the first frame of the walk-out.

Things that do **not** pass the marker:

- Going past it with no direction held, for example sliding to a stop
  after Right is released: the lead stood 7 px past the marker without
  passing it, then passed it on the first frame it held Right.
- Holding only Up, or only Left, standing past the marker.
- Holding Left and Right together, with or without other inputs, even
  while the lead is on the ground past the marker and moving right
  (sliding to a stop, rolling or crouching). The lead passed it on the
  first frame after that with Right held and Left not.
- Being in the air: a lead jumping past the marker with Right held
  passed it on the frame it landed (`n` = the landing frame).
- Passing above or below the marker by more than 4 px: a lead walking
  on a higher floor over the marker, Right held, never passed it.

Right held together with Up, Jump (a jump already held through a
landing) or Action (running) counts. A roll counts: the lead passed the
marker on a frame of a roll with Right held.

Right held together with **Down** counts too, whatever Down is doing
(`ground-speed.md`, "Crouching"; `ground-action.md`): a lead crouching
and still past the marker passed it on the first frame Right was added,
a lead sliding while crouched passed it on the first frame ending with
`X` ≥ `xm`, and so did a lead in the Down + Action ground action with
Right held. Down held without Right, or with Left and Right together,
doesn't pass it.

**Jumps.** The frame a jump is pressed and its take-off frame (on which
`ground` is still 1; `jump-arc.md`, "Take-off frame") are grounded
frames like any other: with Right held on one of them and the rest of
the rules met, the lead passes the marker on that frame. The jump then
never happens: `T` and the walk-out are exactly as in "The walk-out"
below, starting from where the lead stood on the floor. A jump
pressed with Left and Right both held, or with no direction, and then
Right held on the take-off frame passes the marker on the take-off
frame. With Right not held on either frame, the lead leaves the ground
and can only pass the marker on landing, as above.

**Hits and bounces.** The frame on which the lead is hurt (`H` in
`knock-out.md` and `losing-a-character.md`, "The hit frame") never
passes the marker, even with every rule above met: on `H` the lead
doesn't move, so a lead on the ground past the marker stays there, with
Right held, and doesn't pass it. Nor does any later frame of that
character, since its input is ignored from `H` on (the knock-out, or the
hit character's flight and run out of the level, go on as usual). So
when a walker's contact is due to hurt the lead on the next frame
(`walker-contact.md`, "Being hurt"), Right held on this frame, with the
rules met, passes the marker and the hurt never happens ("Walkers and
bunches" below), while Right held from `H` on does nothing.

With the other character there, it becomes the lead after the hit
(`losing-a-character.md`). It can pass the marker only once it is under
control again, from its hop's landing ("Control returns" there): during
its run to the hit spot it was on the ground past the marker with Right
held and didn't pass it. In the runs, its hop came down on the walker
that had hurt the hit character and bounced off it; it passed the marker
on the frame it landed after that bounce, Right held.

A bounce off an enemy (`bounce.md`) is a time in the air like a jump: no
frame of it passes the marker, including the two frozen frames and
frames past the marker, and the lead passes it at the earliest on the
frame it lands, as above.

The boundaries were measured as follows: frames ending 0.13 px left of
`xm` did not pass the marker, and frames ending 0.26 px right of it did;
nothing in between was measured. `F` − `ym` of −4 and 4 passed it and −5
and 5 did not, with the lead's feet on whole px.

Left and Right together, Down and jumps were measured at the first
level's end, with the first character leading and with the second
character leading (the first following):

- **Scripted inputs.** 107 runs with the first character leading, from
  standing 7 px past the marker and from standing 113 px left of it:
  Left and Right together (alone and with Action, Up, Down or Jump) from
  a stand and after walking, running or a roll, switched to at 7 to 9
  points on the way in; Down with and without Right, from a stand, after
  a crouch and switched to at 7 to 9 points on the way in; Down with
  Action; jumps pressed at 5 to 8 points up to the frame the walk
  reaches the marker, with Right, Right with Down or Up, or Left and
  Right on the press frame and Right or nothing after it. 35 runs of the
  same kinds with the second character leading. Every frame up to `T`
  (24 213 and 6 970) was judged by the rules above: the marker was
  passed on exactly the frame they give in the 63 + 22 runs that passed
  it, and in no other run. 9 + 3 of those passes were on a take-off
  frame and 10 + 3 on a frame with Jump held.
- **Random inputs.** 400 runs, 200 with each character leading, from
  standing left of the marker, of random inputs biased to the right,
  with Left and Right together (alone and with Action, Jump or Down),
  Down with Right (alone and with Jump or Action), Down, short Jump
  presses with Right, Right with Up, Jump alone, Left and nothing. Every
  frame up to `T` (54 830) was judged by the rules above: every run
  passed the marker, on exactly the frame they give. 278 frames met every
  rule except that Left was held with Right and didn't pass it; 175
  passes had Down held (61 of them with Action, 11 with Jump), 6 were on
  a take-off frame and 25 on a press frame. Every frame from `T` to
  `F0` + 28 (24 071) was as in "The walk-out" and "The fade".

Hits and bounces were measured at the first level's end, with a medium
walker that had walked to the wall 15 px right of the marker and stayed
there (a change made for the test):

- **Hits.** With the marker's entry moved 1 to 11 px left, the lead
  walked right, let go of Right before the marker and slid past it with
  nothing held into the walker's reach: 93 runs with the first
  character alone and 85 with the second following, from 4 slides each,
  holding Right from one of the frames from the slide's start to `H` +
  16, or never. With Right first
  held before `H` the marker was passed on that frame and the lead was
  never hurt (21 + 13 runs). In the other 72 + 72 runs (Right first
  held on `H` or later in 68 + 68, never in 4 + 4) the hit character
  didn't pass it (264 + 459 frames met every rule but came on or after
  `H`). With the second character following, none of the 221 frames
  from `H` to its hop's start that met the other rules passed it, and
  in all 72 of those runs it landed past the marker after the bounce:
  it passed the marker on that landing frame in the 68 with Right held,
  and didn't pass it in the 4 with Right never held.
- **Bounces.** 81 runs with the lead alone jumping onto the walker,
  Right held, from 21 points with 3 jump lengths, the marker where it
  was and 6 px left. No frame of the 18 + 18 bounces passed the marker;
  each of those runs passed it on the frame it landed. Of the others,
  34 were hurt by the walker and didn't pass it, 4 landed on the higher
  floor over it and didn't pass it, and 7 passed it on the floor before
  reaching the walker.

Every frame up to `T` (101 515) was judged by the rules above, with no
pass from `H` on for the hit character and none before its hop's landing
for the other character: the marker was passed on exactly the frame
they give in every run that passed it, and in no other run.

## The walk-out

From `T` on, nothing held has any effect: holding Left, Jump and
Action, or Down and Action, or nothing, on every frame from `T` gave
exactly the same frames as holding Right.

On every frame from `T` on:

- **Speed.** `vx` is +2 (512/256) from `T` on, whatever it was before
  (2.89 during a roll, 3 running, 0.25 in a small step).
- **Moving.** On `T` the lead doesn't move. From `T` + 1 on, `x` grows
  by exactly 2 every frame.
- **Height.** From `T` + 1 on `ground` is 0, and the lead goes up by
  1/8 px (32/256) every frame, until the fade starts (next section);
  from then on it goes neither up nor down. This takes no account of
  the ground: the lead passes through walls and doesn't follow the
  floor. At the first level's end the lead walked through the wall that
  ends the level on the right.
- **Facing.** A lead facing right keeps facing right. A lead facing
  left on `T` (one run: a turn to the right was in progress when the
  marker was passed) stopped that turn on `T` and started a new turn
  (`ground-speed.md`, 8 frames) on `T` + 1; its facing became right on
  `T` + 5. The lead's pictures are those of walking.
- **The view stops.** `V` keeps the value it had on `T` until the level
  is over.
- **Drawn behind the scenery.** From `T` on, the characters are drawn
  behind the level's scenery: at the first level's end they disappear
  behind the rock face on the right as they walk on.

### The other character

In the one run with both characters, the other character went on
following the lead (`following.md`): it moved right by exactly 2 px on
every frame from `T` on, staying 35.83 px behind the lead (its distance
on `T`), and its height followed the lead's earlier positions along the
trail, going up as the lead had. It was drawn behind the scenery too.

### An object in the lead's hands

A `barrel` or a `holder` still in the lead's hands at the end of frame
`n` (the frame on which the marker is passed) is let go on `T` and
falls away on its own. This includes an object carried with Action held
and one whose throw (`carrying.md`, "Throwing") began on a frame `Q`
with `Q` + 21 equal to `T` or later, so that it hadn't left the hands by
the end of `n`; that throw never happens. With `Q` + 21 equal to `n` or
earlier the object is thrown as usual before the marker is passed.
Letting go of Action on `T` or later makes no difference.

- **On `T`** the object doesn't move: it is where it was held at the
  end of `n`. Its `vx` becomes **1** (256/256) in the direction the lead
  faces on `T` (left for a lead that is turning toward the right on
  `T`, "Facing" above), and its `vy` becomes **−4** (−1024/256, moving
  up).
- **From `T` + 1**, every frame, `vy` increases by **0.4375** (112/256),
  but never above **8** (2048/256); then `x` moves by `vx` (exactly 1 px)
  and `y` by the new `vy`. Nothing else changes `vx`.
- It **touches nothing**: it passes through floors and walls (at the
  first level's end, the wall that ends the level on the right) without
  landing, bouncing, rolling or breaking, and nothing held changes its
  motion.
- It is **removed** on the first frame on which, after the move, its `x`
  is less than 0, or its `x` rounded down minus `V` rounded down is 289
  or more, or its `y` rounded down is 256 or more below `U` or 33 or
  more above `U`, with `U` the view's vertical position rounded down as
  in `../formats/placements.md` ("When placed objects are removed"). If
  none of these happens first, it is gone when the level is over.

This is the movement of an object carried into an exit (`exits.md`, "An
object in the lead's hands"), starting on `T` instead of `T` + 1. The
lead's own walk-out and the fade are unchanged.

So a barrel let go by a lead facing right rises for 9 frames, from `T` +
1 to `T` + 9, and falls from `T` + 10, at 8 px per frame from `T` + 28.
With the view at the first level's end it was removed below the picture
36 to 38 frames after `T`, before the fade had ended.

Measured in 105 runs with the lead alone. The lead picked up the first
level's barrel or one of its two holders and was moved near the level's
end while carrying it (a change made for the test while the game ran),
then came to the marker: walking in with Action held from 7 points (one
in small steps), landing on the marker from 4 jumps, stopping short and
walking on (3 runs), turning toward the right on the marker (3 runs),
letting go of Action 1 to 25 frames before `T`, on `T` or on `T` + 1
(10 runs), standing past the marker and letting go of Action 1 to 30
frames before Right was held (9 runs), holding Right, Jump, Down, Up,
Left, Left with Jump, Left with Action or nothing from `T` + 1 (8 runs),
and with each holder (3 runs). In 58 more runs the barrel was moved
during the walk-out to find where it is removed: 20 to 26 px down on
`T` + 12, 60 to 400 px right on `T` + 5, 140 to 300 px up on one of `T`
+ 3 to `T` + 8, or left of `x` 0. In every run the marker was passed on
the frame "When the marker is passed" gives, and the lead's walk-out and
the fade were as above on all 6 340 frames from `T` on. In 96 runs the
object was still in the hands at the end of `n` (8 of them in a throw);
over the 2 855 frames from `T` to its removal, `x`, `y`, `vx` and `vy`
were as the rules above give on every frame, to 1/256 px, and it was
removed on the frame they give. In the 9 others the throw had already
let it go. The removal edges, after the move: still there with `y`
255.375 below `U`, removed at 256.375; still there with `x` − `V` 288,
removed at 289; still there with `y` 31.3125 above `U` (rounded down,
32), removed at 32.25 (rounded down, 33).

### Walkers and bunches

From `T` on, walkers and bunches go on as they would without the
walk-out, except that a walker no longer hurts the lead:

- **Walkers move as usual.** A walker (`walker.md`) keeps moving as it
  would if the marker hadn't been passed, at the same position on every
  frame, until the level is over. So does a
  walker defeated before `T` (`rolling-into-walkers.md`) as it falls
  away.
- **No hurt from `T` on.** A walker whose contact would hurt the lead on
  `T` (`walker-contact.md`, "Being hurt": the positions at the end of
  `n` in range) doesn't hurt it, and nothing changes in the walk-out or
  the fade. The lead goes on walking through the walker's place. A
  walker that hurts the lead before `T` hurts it as usual, and the lead
  doesn't pass the marker on that frame or later ("Hits and bounces"
  above).
  No bounce off a walker was seen during the walk-out; the lead never
  moves down then.
- **Bunches are still taken.** A bunch (`bunches.md`) whose offsets
  come into range during the walk-out is taken on that frame, with the
  ranges of the standing row of "Pickup ranges" there (`I` −15 to 21,
  `D` −8 to 40), whatever the frame of the walk-out. `X` and `F` are the
  lead's on that frame, as in "When a bunch is taken" there. Its items
  are released and reach the count as "Releasing the items" there
  gives, with `V` the view's position held from `T`, on every frame
  until the level is over.

For example, take a lead facing right with `x` = `xm` + 0.05 on `T`, and
a bunch with `xb` = `xm` + 33 and `yb` 20 px above the lead's feet line.
The lead's `x` is `xm` + 16.05 at the end of `T` + 8 (`I` = −17) and
`xm` + 18.05 at the end of `T` + 9 (`I` = −15), so the bunch is taken on
`T` + 9. Its items reach the count on `T` + 26, `T` + 32, `T` + 38, and
so on every 6 frames until the level is over.

Measured in copies of the image in which the first level's sign entry
left of the marker was given the reference of a small walker (patrol
both ways, heading left) or a medium walker (heading right, no patrol),
or the entry right of the marker the reference of a `bunch`, at chosen
positions:

- **Walkers moving.** 40 runs in which the lead waited on the last
  platform for 0 to 60 frames, ran and jumped over a small walker
  walking left toward it, and came to the marker. Each was compared, on
  every frame from `T` to the level's last frame, with the same inputs
  in a copy in which the marker's entry was given an unreachable bunch
  instead (so the marker was never passed). Every object other than the
  marker and that bunch had the same position on all 2 459 frames,
  1 692 of them with the walker there (walking, stopped, or falling
  after the lead rolled into it). In 3 more runs, medium walkers that
  had fallen below the level's floor went on walking 1 px per frame
  through the walk-out and the fade.
- **Walkers in the way.** A medium walker that had walked to the wall
  15 px right of the marker and stayed there: 8 approaches (walking,
  running, waiting, jumping, small steps), each with the same
  comparison. In the 4 approaches that passed the marker, the lead
  without the marker was hurt on exactly `T` and the lead with it was
  not hurt on any frame. In the other 4 the lead was hurt on the same
  frame in both copies, before reaching the marker. With the marker's
  entry moved to 10 places from 6 px left to 6 px right of where it was,
  the walking lead passed it, and wasn't hurt, with the entry where it
  was or 1 to 6 px left of there (5 places), and was hurt without
  passing it with the entry 1 to 6 px right of there (5 places).
- **Bunches.** 24 runs, the lead walking in: bunches with `xb` from
  `xm` − 7 to `xm` + 58 and `yb` 20 px above the feet line (one taken
  before `T`, the others during the walk-out, on different frames), and
  bunches with `xb` = `xm` + 33 and `yb` from 11 px below to 60 px above
  the lead's feet line on `T`. Every one with `I` and `D` in
  the standing ranges on a frame from `T` on was taken on the first
  such frame (found from the frames its items reached the count, 17
  frames after `P` and every 6 frames after that); a bunch whose `D` was
  −9 or less, or 41 or more, on every frame with `I` in range was never
  taken. So the jumping row from `T` + 6 on (`D` 13 to 57) doesn't apply:
  bunches with `D` from 41 to 59 on those frames stayed there.

## The fade

The fade starts on the frame after the first frame from `T` on that
ends with

    X − ⌊V⌋ ≥ 240

that is, with the lead 240 px or more right of the picture's left edge
(16 px from its right edge, the picture being 256 px wide). Call the
first frame of the fade **`F0`**. It depends only on where the picture
is, not on the marker: moving the marker left or right changed `T` but
left `F0` on the same `x` of the lead.

The **brightness** of the whole picture is on a scale from 15 (full) to
0 (black), and is 15 until the fade. On `F0` it becomes 14, and it goes
down by 1 every second frame: frames `F0` + 2`j` and `F0` + 2`j` + 1
have brightness 14 − `j`. It reaches 0 on `F0` + 28.

`F0` + 28 is the last frame of the level. The level map follows
(`level-map.md`, "After finishing a level"). Who is there when a level
begins again is in `following.md`.

The lead's rise stops on `F0`: its height on `F0` is that of `F0` − 1.

### A bunch's items at the level's end

A bunch taken shortly before the level's last frame (during the walk-out,
"Walkers and bunches", or before it) may still have items that haven't
reached the collectible count by `F0` + 28. Take its items' arrival
frames from `bunches.md` ("Releasing the items"), as if the level went
on:

- An item that would reach the count on **`F0` + 29**, the frame after
  the level's last frame (it belongs to no trace), is counted.
- Items that would reach it on `F0` + 30 or later, and items not yet
  released by `F0` + 28, are **never counted**.

So at most one item is added after the last frame. The count the game
goes on with (on the level map that follows, and when the next level
begins) is the count on `F0` + 28 plus that item, if there is one.

For example, a bunch taken on `P` = `F0` − 6 whose items all fly
releases them on `F0` − 6, `F0`, `F0` + 6, … `F0` + 24 (six by the last
frame; the other four are never released), and they would reach the
count on `F0` + 11, + 17, + 23, + 29, + 35, … The first three reach it
during the fade, the fourth is counted after the last frame, and the
other six are never counted: the count after the level is 4 more than
before the bunch was taken.

Measured on the first level's end, finished for the first time, the
lead alone, in a copy of the image in which the entry right of the
marker was given the reference of a `bunch` with `yb` = `ym` − 17, at
145 places with `xb` from `xm` − 1 to `xm` + 143. The lead came to the
marker walking (125 places), running (145) or walking after standing
still for 37 frames (125), so that the level's last frame came at three
different places relative to the bunch (395 runs). The bunch was taken in 339 runs, from 70 to 13 frames before the
last frame. In every run each count step in the level came on the frame
"Releasing the items" gives, and on every one of the 600 frames
following the last frame, through the black frames and onto the level
map, the count was the count on `F0` + 28 plus 1 in the 59 runs with an
item due on `F0` + 29 and plus 0 in the others; up to 10 items were
never counted. In one run played on into the next level from the map,
the count was still the same there.

## The second character leading

Everything above holds when the second character is the lead (after a
swap, `swap.md`) and the first character follows it. "When the marker is
passed" uses the second character's position, ground and the input
held; the walk-out and the fade are as above, frame for frame. In
particular `vx` is +2 from `T` on and `x` grows by 2 a frame from
`T` + 1, although the second character walks at 2.25
(`second-character.md`). `F0` + 28 is the last frame of the level, as
above. Right held together with Down passes the marker too, as it does
in the room, and Left and Right held together, the frame a jump is
pressed and its take-off frame are as above (the second character's
`vy` on its take-off frame is its own, `second-character.md`).

The first character went on following the lead from `T` on. Its
movement during the walk-out is not covered.

### Checks with the second character leading

At the first level's end, with the second character leading and both
characters standing left of the marker first:

- **Random inputs.** 320 runs of random inputs biased to the right
  (Right alone and with Action, Jump, Up or Down; Action, Down, Down with
  Action, Jump, Left, Left with Action, nothing), held through the walk-out
  and the fade too, for up to 1 500 frames. Every frame before `T`
  (96 343 in all) was judged by "When the marker is passed", including
  every frame of the 35 runs that never passed it (34 ended on the
  higher floor over the marker, one left of it). In each of the 285 runs
  that passed it, it was passed on exactly the frame the rules give,
  with `X` − `xm` from 0 to 15 on that frame, 19 times on the landing
  frame of a jump or fall, 7 times with Down and Right held. Every frame from `T` to `F0` + 28 (17 173,
  with other inputs than Right alone held on 11 482 of them; one run
  ran out of frames 19 frames after `T`) was as in "The walk-out" and
  "The fade". No frame was different. In 4 runs played on, no logic
  frame followed `F0` + 28 in the level.
- **The marker's range.** 78 runs of small steps from standing near the
  marker: frames ending 0.26 px left of `xm` with Right held didn't pass
  it, and frames ending 0.67 px right of it did. With the marker moved
  up or down by 3 or 4 px the lead passed it, and by 5, 6 or 7 px it
  didn't.

## In the first level's room

The room the first level's upper exit leads to (`exits.md`) has an end
marker of its own: passing it is how the room is left. Everything above
holds for it, with the picture's left edge always 0 (`level-ends.md`,
"In the first level's room"), except for the fade's start, the last
frame and what follows:

- **The fade** starts on the frame after the first frame from `T` + 1
  on that ends with `X` ≥ 240. When the lead's `X` is less than 240 on
  `T`, this is the same frame as in "The fade". When it is 240 or more
  on `T`, the fade starts on `T` + 2: the lead rises by 1/8 px on
  `T` + 1 only.
- **The last frame.** The brightness is 0 on `F0` + 28 and on `F0` + 29,
  and `F0` + 29 is the room's last frame. The lead walks on by 2 px on
  both.
- **What follows.** The level isn't finished and the level map doesn't
  follow. On the next logic frame, play is in the first level, which
  begins as it does from the level map, at the first level's
  room-return record (`exits.md`, "Where the first level's exits lead").
  That logic frame is the level's frame 0.

Synthetic example: the lead runs right with Right held and passes the
marker on frame `n`, ending it at `x` = 241.25. On `T` it stays at
241.25 (`X` = 241 ≥ 240 already, but `T` doesn't count), and `vx` = 2.
`T` + 1 ends at 243.25, 1/8 px higher, and is the first frame from
`T` + 1 with `X` ≥ 240, so `F0` = `T` + 2: brightness 14 on `T` + 2 and
`T` + 3, 0 on `T` + 30 and `T` + 31. `T` + 31, with `x` = 303.25, is the
room's last frame, and the next logic frame is the first level's frame
0.

Also measured in the room: Right held together with Down passed the
marker (37 runs), as did Right with Jump, Action or Up.

What follows the walk-out depends on the area the marker is in, not on
which `end-marker` reference placed it. In copies of the image, the
room's marker was given the first level's end-marker reference, and the
first level's end marker was given the room's; in a third copy, a class 1
entry near the first level's start became a class 2 entry with the
room's reference. Every frame was the same as with the marker's own
reference: in the room, play went back to the level; in the level, the
level was finished and the level map followed.

### Checks in the room

- 500 runs with random inputs biased to the right (Right alone and with
  Action, Jump, Up or Down; Action, Down, Down with Action, Jump, Left,
  Left with Action, nothing): 200 from a standing start in the room, 200
  with the lead alone put at a random point left of the marker first,
  and 100 with the other character following (it went on following the
  lead; not checked frame by frame).
  Every frame before `T` (46 521) was judged by "When the marker is
  passed": the marker was passed on exactly the frame the rules give, in
  every run, with `X` − `xm` from 0 to 8 on that frame, 100 times on the
  landing frame of a jump or fall. Every frame from `T` to the last
  (16 982, inputs held on 15 001 of those from `T` + 1) was as in "The
  walk-out" and "The fade" with the changes above, the last frame was
  `F0` + 29 in every run, and the next was the first level's frame 0.
  The first 91 frames there, with nothing held from frame 1, were the same
  as the level's own start from the level map in every run (45 500
  frames).
- Three runs passed the marker while turning toward the right: the turn
  went as in "The walk-out" ("Facing").
- With the marker moved up or down by 4 px (a change made for the test
  while the game ran), a lead walking right on the floor passed it; by
  5 px it didn't, and was stopped at the room's right end. With the
  marker moved 8, 40 or 100 px left, `T` changed and the fade started
  on the frame after the first frame ending with `X` ≥ 240, as above.

## In the area behind the first wall exit

The area behind the first level's first wall exit (`wall-exits.md`,
"The area behind the first wall exit") has an end marker too: passing
it is how the area is left. Everything above holds for it, with the
picture's left edge of `level-ends.md` (the area's limits are its own,
`../formats/view-limits.md`), except for the last frame and what
follows:

- **The fade** starts as in "The fade". In every run the picture's
  left edge on `T` was the area's right limit, and the lead was less
  than 240 px right of it on `T`; `F0` came 10 to 17 frames after `T`.
- **The last frame.** As in the room: the brightness is 0 on `F0` + 28
  and on `F0` + 29, and `F0` + 29 is the area's last frame. The lead
  walks on by 2 px on both.
- **What follows.** The level isn't finished and the level map doesn't
  follow. On the next logic frame, play is in the first level, at
  frame 0 of `wall-exits.md`, "Coming back from the first wall exit's
  area".

### Checks in the area behind the first wall exit

- 800 runs with random inputs biased to the right, the lead alone (see
  `wall-exits.md`, "Checks"). Every frame from the lead's control to
  `T` (674 220) was judged by "When the marker is passed": the marker was
  passed on exactly the frame the rules give, in every run, with `X` −
  `xm` from 0 to 15 on that frame, 141 times on the landing frame of a
  jump or fall. Every frame from `T` to the last (35 890, with the
  random inputs going on) was as in "The walk-out" (position,
  speed, height, the view) and "The fade" with the change above, the
  last frame was `F0` + 29 in every run, and the next was the first
  level's frame 0 there.

## Example

The marker is at `xm` = 1000, `ym` = 449. The lead walks right on a flat
floor with its feet line at 449, Right held, `vx` 2, the view at
`V` = 800. Frames end with `x` = 997.5, then 999.5 (`X` = 999 < 1000:
not passed), then 1001.5 on frame `n` (`X` = 1001: passed). `T` = `n` +
1.

| Frame | `x` | `vx` | Height change | `ground` | Brightness |
|---|---|---|---|---|---|
| `T` | 1001.5 | 2 | none | 1 | 15 |
| `T` + 1 | 1003.5 | 2 | up 1/8 | 0 | 15 |
| `T` + `k` (1 ≤ `k` ≤ 20) | 1001.5 + 2`k` | 2 | up 1/8 | 0 | 15 |
| `T` + 21 = `F0` | 1043.5 | 2 | none | 0 | 14 |
| `F0` + 1 | 1045.5 | 2 | none | 0 | 14 |
| `F0` + 2 | 1047.5 | 2 | none | 0 | 13 |
| `F0` + 28 | 1099.5 | 2 | none | 0 | 0 |

Frame `T` + 20 ends with `x` = 1041.5: `X` − `V` = 241 ≥ 240, so the
fade starts on `T` + 21 (`T` + 19 ended at 1039.5: 239). The lead rose
on `T` + 1 to `T` + 20, 2.5 px in all. The level's last frame is `F0` +
28 = `T` + 49.

At the first level's end, walking in at 2 px per frame, the level's
last frame came 61 frames after `T`.

## Not covered

- Other levels' end markers, and areas other than the first level, its
  room and the area behind its first wall exit.
- Left and Right held together at the room's marker (measured at the
  first level's end only). Hits and bounces from enemies other than
  walkers, and a hop that lands without a bounce after losing a
  character at the marker.
- The first character's movement during the walk-out when it follows
  the second character.
- Whether the other character can pass the marker, and the other
  character when it is not following (joining, being carried).
- Enemies other than walkers, and objects other than bunches and an
  object in the lead's hands, during the walk-out.
- A bunch's items at the level's end ("A bunch's items at the level's
  end") with two bunches releasing at once, with the second character
  leading, when the level has been finished before, and items counted at
  once (`bunches.md`) whose release would come on `F0` + 29. How the
  count display shows an item counted after the last frame.
- An object in the lead's hands with the second character leading, or
  with the other character following.
- A lead that is already 240 px or more right of the picture's left
  edge on `T`, other than in the room ("In the first level's room"). In
  the area behind the first wall exit this wasn't seen.
- The sound during the walk-out and the fade.
