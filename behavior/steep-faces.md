# Steep faces and walls

Titles: `title-1`, `title-2`

What happens when a grounded player meets ground that rises or falls too
steeply to walk on: being stopped by it, not being able to start walking
toward it from a standstill, being carried down a steep face, leaving
the ground on one, and (in `title-1`) landing on or next to one and
jumping on or next to one. Frames are logic frames at 60 Hz, numbered as in a
trace. `x`, `y` and `vx` are as in a trace: right and down are positive,
speeds in px per frame. Speeds are whole multiples of 1/256 px per frame
and are written both ways, as in `ground-speed.md`. "Left", "Right" and
"Action" are the neutral inputs `L`, `R`, `A` of the scenario format.

Measured with the lead character, walking, running, standing and
coasting with nothing held, on four steep faces (going down faces that
fall to the right and to the left, and against faces that rise to the
right and to the left) and against one vertical step up, going left.
Landings after jumps and after falls from a face were measured
separately ("Landing on or next to a face", with its own evidence).
Jumps on a face and in front of a rise are in "Jumping on or next to a
face", also measured separately, and rolls on, onto and against these
surfaces in "Rolling on, onto or into a face".

## Columns, faces and steps

The floor has one height for each 1-px-wide column of `x`. "The
player's column" is the column containing `x` (its whole-pixel part).
"Higher" means higher on screen (smaller `y`).

- A **steep face** is a run of adjacent columns where each column's
  floor is exactly **4 px** higher than the one before it. A **face
  column** is a column whose floor differs by exactly 4 px from a
  neighbouring column's floor, on a face of at least two such steps in
  a row. A single 4 px step between two stretches of other floor is not
  a face, and its columns are not face columns: it was walked up as in
  "Following a face down, or leaving it" (measured on a platform's top,
  `platforms-from-below.md`, "Walking under a platform"). The shortest
  faces measured had 4 columns; faces of 2 steps (3 columns) are not
  covered.
- A face **rises** in the direction its floor goes up and **falls**
  (is downhill) in the other.
- At its ends a face joins the neighbouring floor by a smaller step: its
  lowest column was 1 px above the floor next to it on both faces
  measured from below, and its highest column 3 px below the floor next
  to it on both faces measured from above. Those neighbouring columns
  are not face columns.
- A **vertical step** is two adjacent columns whose floors differ by
  **64 px**. This is the only step height of this kind that was measured.

The rules below were measured near faces and the vertical step. On flat
floor and on slopes of 1 px per 4 columns they never take effect: there
are no face columns or steps, and the column 5 ahead (below) is never
more than 2 px higher.

## Stopped by a rise

On a grounded frame (walking, running or standing, and a jump's
take-off frame; rolls in "Rolling on, onto or into a face"), after
the speed for the frame is worked out (`ground-speed.md`, or the face
rule below), the player is **blocked** if the move would take `x` into a
different column that is either:

- a face column of a face that rises in the direction of the move,
- beyond a vertical step up in the direction of the move, or
- not a face column, with a floor **more than 8 px** higher than the
  floor of the column the player was in, when the move passes over a
  face column of a face that rises in the direction of the move ("Moving
  past a face's top" below).

A blocked frame:

- `x` is set to the left edge of the column the player was in before the
  frame: the fraction of a pixel is dropped and `x` is a whole number.
  The player moves back slightly if moving right and forward slightly if
  moving left.
- `vx` becomes **0**.
- The player stays grounded on the floor they were on.

Even a face column only 1 px above the player's floor blocks: the floor
height of the new column is not what counts, only that it belongs to a
rising face. The one exception measured is a platform's top
more than 8 px above the feet, taken as the floor from the band above:
the player falls through the floor instead (`platforms-from-below.md`,
"Walking under a platform"). Columns in front of a face are walked over normally right up
to its first column.

Measured at walking and running speed, and at the low speeds of starting
from rest, into faces rising to the right and to the left and into the
vertical step going left; the speed and the fraction of `x` before the
frame made no difference.

### Moving past a face's top

A move on the floor can pass over a face's highest columns in one frame
and end in the column beyond them, which isn't a face column (on the
faces measured, the floor at a face's top is 3 px above its highest
column). Only the end column counts for the first case above, so such a
move isn't blocked by it; the third case decides:

- passing over **one** face column, onto floor **7 px** higher: not
  blocked. The player follows the floor up ("Following a face down, or
  leaving it": `y` becomes the new column's floor, `ground` stays 1).
- passing over **two** face columns, onto floor **11 px** higher:
  blocked, as on any blocked frame.

Floor 9 or 10 px higher can't be reached this way on a face of 4 px per
column and wasn't measured. A player is only moving up a face after a
landing ("Landing on or next to a face"), so these moves happen on the
frames after a landing on one of a face's highest columns, with the
player still moving uphill fast enough to cross two or three columns in
a frame.

Measured on a face of 4 columns at the far side of a pit (no floor in
front of its lowest column, flat floor 3 px above its highest), rising
to the right, and on its mirror image rising to the left on the pit's
near side: 3 388 runs of random inputs (directions, Jump, Action) that
landed on or near one of the faces, 2 104 used to find the rule and
1 284 with new seeds to test it. On the frames from L + 1 after those
landings, checked as in "Evidence (landings)" below, 491 moves passed
over one face column onto floor 7 px higher (463 going right, 28 going
left) and all followed it; 15 passed over two onto floor 11 px higher
(11 going right, 4 going left) and all were blocked. All 23 510 checked
frames (14 800 and 8 710 in the two sets) matched the rules of this page
with the third case; with the first case alone, exactly those 15
blocked frames came out wrong. The landing runs of "Evidence (landings)"
agree: 27 moves over one face column onto floor 7 px higher followed,
and the 2 over two face columns onto floor 11 px higher were blocked.
Rolls are blocked by the same rule ("Blocked" under "Rolling on, onto or
into a face").

Example, relative positions: columns 0 to 3 are a face rising to the
right with floors at `y` = 15, 11, 7 and 3, there is no floor left of
column 0, and the floor is at `y` = 0 from column 4 on. Frame numbers
count from the landing frame L of a jump. "Column floor" is the floor of
the column containing `x` at the end of the frame.

**Over one face column, followed.** A jump over the pit, Right held:

| Frame | Held | `x` | Column floor | `y` | `vx` (1/256) | `ground` |
|---|---|---|---|---|---|---|
| −2 | R | −1.44140625 | – | −5.6875 | 512 | 0 |
| −1 | R | 0.55859375 | 15 | 0.4375 | 512 | 0 |
| 0 = L | R | 2.55859375 | 7 | 7 | 512 | 1 |
| 1 | R | 4.30859375 | 0 | 0 | 448 | 1 |
| 2 | R | 6.08984375 | 0 | 0 | 456 | 1 |

On frame 1 the player starts on face column 2 with Right held uphill, so
the target is 0 and `vx` goes from 512 to 448; `x` moves 1.75 px, over
face column 3, into column 4, and the player steps up 7 px.

**Over two face columns, blocked.** A running jump over the pit:

| Frame | Held | `x` | Column floor | `y` | `vx` (1/256) | `ground` |
|---|---|---|---|---|---|---|
| −2 | R A | −3.66015625 | – | 0 | 757 | 0 |
| −1 | R A | −0.69921875 | – | 7 | 758 | 0 |
| 0 = L | A | 1.89453125 | 11 | 11 | 664 | 1 |
| 1 | A | 1 | 11 | 11 | 0 | 1 |
| 2 | A | 0.8125 | 15 | 15 | −48 | 1 |

On frame 1 `vx` would be 581, taking `x` to 4.1640625: over face columns
2 and 3 into column 4, 11 px higher, so the frame is blocked. It ends on
a face column with `vx` 0, so it is the switch frame M, and from frame 2
the player is carried down the face.

## Can't start toward a rise

On a grounded frame that starts with `vx` exactly **0**, with Left or
Right held (with or without Action held, but not on a frame on which an
Action press starts a roll, `roll-input.md`):

1. Take the column **5 columns ahead** of the player's column in the held
   direction.
2. If that column's floor is **8 px or more** higher than the floor of
   the player's column, the player doesn't start moving: `vx` stays 0
   and `x` doesn't change.
3. If it is 5 px higher or less (or lower), the player starts moving as
   usual (`ground-speed.md`). Differences of 6 and 7 px weren't
   measured. (8 px was measured with a platform's top as the floor 5
   columns ahead, `platforms-from-below.md`; the larger differences at
   faces and steps.)

This holds on every frame it applies, however long the direction is held
and however often it is released and pressed again. It is why a player
who has been blocked stays where they are while still holding toward the
rise: after the blocked frame `vx` is 0 and the column 5 ahead is inside
the face or past the step. Holding the other direction starts moving away
as usual.

The same rule decides whether a player standing a few pixels in front of
a rise can walk up to it at all. A player who does start moving is only
stopped by the blocking rule above, so they can end up closer to the rise
than they could have started from.

The rule doesn't apply on the frames after a landing up to and including
the switch frame M ("Landing on or next to a face" below), nor on a
jump's take-off frame ("Jumping on or next to a face").

Measured standing at every 1/16 px across the boundary in front of a
face rising to the right, a face rising to the left and a vertical step
(rising to the left), and at the positions where players came to rest
by themselves. Frames held toward a rise that don't start at `vx` 0 are
in "Holding toward a rise" below; this rule is the case of that one in
which the previous frame's target was 0.

## Holding toward a rise

This section is `title-1` only. It covers grounded frames on which Left
or Right is held toward a rise as in "Can't start toward a rise": the
column 5 columns ahead of the player's column in the held direction is
8 px or more higher than the player's column. It holds whatever `vx` is
when the frame starts.

**Target speed.** On such a frame the target speed (`ground-speed.md`)
doesn't follow the held direction. It is the previous frame's target,
unchanged: −3, −2, 0, 2 or 3 px per frame. Releasing Action doesn't
change it: a player whose target was 3 keeps 3 with only the direction
held. (Pressing Action on the floor starts a roll, `roll-input.md`; a
target of 2 kept with Action held newly wasn't measured.) A frame on which the player was
blocked ("Stopped by a rise") counts as having target 0. A frame that started
on a face column counts as described in "Leaving a face onto floor". When the previous
frame is itself such a frame, its target was decided the same way, so
the target carries over from the last frame on which it followed the
held input. The speed rule then applies as on any grounded frame. So:

- A player who is standing still, or who has just been blocked, has
  target 0 and stays where they are: this is "Can't start toward a
  rise".
- A player walking or running toward the rise keeps walking or running
  toward it until the move is blocked.
- A player moving away from the rise who then holds toward it keeps
  going away, speeding up toward the previous target (2, or 3 if it
  was 3), as long as the column 5 ahead in the held direction stays
  8 px or more higher.
- A player slowing down with nothing held (target 0) who then holds
  toward the rise keeps slowing down under target 0.

This applies while standing, walking, running and during the 8-frame
turn of `ground-speed.md`, but not on the frames on which the target
follows the held input as in the air (`air-control.md`): a jump's
take-off frame ("Jumping on or next to a face" below) and, after a
landing, the landing frame and the frames after it up to and including
the switch frame M ("Landing on or next to a face" below). On those the
target is the one for the held input even toward the rise, and the
frame's target is then the previous target for the next frame. So after
a landing in front of a rise the rule takes over from M + 1 with M's
target, and a player who stood still on L to M (nothing held, or only
Action) and then holds toward the rise stays put, while one who held
toward it from a frame before M (and wasn't blocked) starts moving
toward it and keeps going on M + 1. A press frame P of a jump is a
frame on the floor like any other, and the rule applies on it.

In one run seen, the speed reached exactly 0 under the speed rule while
the target was still away from the rise (the player was reversing). On
the next frame the player started moving away from the rise again,
although that frame started with `vx` 0.

**Facing.** While standing, walking or running (the modes of
`ground-speed.md`, "Standing, walking and running"), holding toward such
a rise turns `facing` to the held side on that same frame, without the
8-frame turn. On the landing frames `facing` follows the input anyway
(`ground-speed.md`, "After a landing"). During a turn, see "Turns next
to a rise" below.

Example, walking left away from a face that rises to the right, then
Right held (`x` relative to the start, 1 px to the left of the left edge
of the face's first column, facing left). Right is held from frame 3,
but the target stays −2 and the player keeps speeding up to the left;
`facing` turns right on frame 3. On frame 5 the player's column is far
enough from the face that the column 5 ahead is no longer 8 px higher,
and the usual rules apply from then on.

```
frame	input	x	y	vx	vy	state	ground	facing	events
0	.	0	0	0	0	idle	1	L	
1	L	-0.25	0	-0.25	0	walk	1	L	
2	L	-0.71875	0	-0.46875	0	walk	1	L	
3	R	-1.37890625	0	-0.66015625	0	walk	1	R	turn
4	R	-2.203125	0	-0.82421875	0	walk	1	R	
```

**Turns next to a rise.** Here T is the first frame of an 8-frame turn
(`ground-speed.md`, "Facing and turns"), and the turn's frames are T + 1
up to its end, T + 8 included (the frame on which a new turn can start,
which then has its own T + 1 to T + 8).

- On each of the turn's frames that is held toward such a rise,
  `facing` turns to the held side at once, as while standing, walking
  or running. On T + 4 the turn's own change of `facing` comes after
  that: on a T + 4 held toward the rise `facing` ends on the side away
  from the rise, whichever side it had before the frame (the `turn`
  event appears only if that differs from it).
- Where `ground-speed.md` starts a turn because the direction opposite
  to `facing` is held (while standing, at a turn check while walking or
  running, and on T + 8 at the end of a turn), a frame held toward such
  a rise starts one instead when the frame's target speed (as decided
  above) is not 0 and is toward the side opposite to `facing`, with
  `facing` already turned to the held side on that frame. As that side
  is the rise's side, a turn starts when the kept target is away from
  the rise, and not when it is 0 or toward the rise. Away from a rise
  the target is on the held side (blocked frames aside, see "Not
  covered"), and this is the same rule.

Example, standing facing right 5.125 px to the right of the foot of a
64 px vertical step rising to the left (`x` relative to the start).
Left on frame 1 starts a turn (T = 1); the column 5 to the left isn't
higher yet. From frame 2 on, the column 5 ahead is the step's while
Left is held: `facing` turns left at once on frame 2. Right on frames 4
to 7: the turn's change on frame 5 (T + 4) turns `facing` right, and the
target becomes 2. Left again on frame 8 (toward the step): `facing`
turns left at once, and the target stays 2. On frame 9 (T + 8) the kept
target is to the right, opposite to `facing`: a new turn starts on
frame 9, and its change on frame 13 turns `facing` right while Left is
held. (From frame 10 the column 5 to the left is no longer higher, and
the target follows Left.) With the turn decided by the held direction,
no turn would start on frame 9 and `facing` would stay left.

```
frame	input	x	y	vx	vy	state	ground	facing	events
0	.	0	0	0	0	idle	1	R	
1	L	-0.25	0	-0.25	0	walk	1	R	
2	L	-0.71875	0	-0.46875	0	walk	1	L	turn
3	L	-1.37890625	0	-0.66015625	0	walk	1	L	
4	R	-1.70703125	0	-0.328125	0	walk	1	L	
5	R	-1.74609375	0	-0.0390625	0	walk	1	R	turn
6	R	-1.53125	0	0.21484375	0	walk	1	R	
7	R	-1.09375	0	0.4375	0	walk	1	R	
8	L	-0.4609375	0	0.6328125	0	walk	1	L	turn
9	L	0.33984375	0	0.80078125	0	walk	1	L	
10	L	0.79296875	0	0.453125	0	walk	1	L	
11	L	0.94140625	0	0.1484375	0	walk	1	L	
12	L	0.82421875	0	-0.1171875	0	walk	1	L	
13	L	0.47265625	0	-0.3515625	0	walk	1	R	turn
```

### Evidence (holding toward a rise)

- 3 000 runs of random Left, Right, Left + Action, Right + Action or
  nothing, 1 to 8 frames at a time, for 60 frames, from standing
  0 to 12 px in front of a rise (a face rising to the right, starting
  facing left or right; a 64 px vertical step rising to the left): on
  153 035 grounded frames the target speed matched this rule and
  `ground-speed.md` with 0 differences (1 710 blocked frames left
  out). 20 987 of these frames were held toward the rise: 15 389 with
  target 0 (14 653 of them starting at `vx` 0), 3 642 with the target
  toward the rise and 1 956 with it away from the rise. With the target
  taken from the held direction alone, 17 336 frames differ.
- `facing` on the 17 077 frames held toward the rise while standing or
  walking: the held side on every one, 621 of them turning on that
  frame.
- Running, turns and landings: 19 200 runs of 120 frames from
  anywhere on two floors in front of a rise (a 112 px floor before a
  face rising to the right, starting facing left or right; a 110 px
  floor before a 64 px vertical step rising to the left). Each run held
  segments of Left, Right or nothing (toward the rise more often), with
  Action held only from a jump's third frame on and then kept or
  released (running without a roll), and jumps of 2 to 16 frames with a
  direction held in the air; half the runs used segments of 1 to 4
  frames. On the 341 666 frames that started and ended on the floor
  (walking, running, standing, turning, landing frames, press and
  take-off frames, 2 519 of them blocked) the target speed matched this
  rule and `ground-speed.md` with 0 differences. 20 342 frames were held
  toward the rise under this rule: 17 231 with target 0, 2 218 with the
  target toward the rise and 893 away from it; 966 of them while
  running, 1 677 during turns and 1 561 on landing frames after M. 719
  frames held toward the rise were take-off frames or landing frames up
  to M, with the target from the held input.
- Controls on the same frames: with the target always from the held
  input, 2 833 frames differ; with the rule applied on the landing
  frames up to M as well, 747; with the kept target's size (2 or 3)
  taken from whether Action is held on the frame (Action released
  while the target is kept), 62.
- `facing` on the 18 061 frames held toward the rise while standing,
  walking or running (1 371 of them running, 299 turning on that
  frame): the held side on every one.
- Turns next to a rise: the same 19 200 runs and 19 200 more of the same
  kind with other random choices. On every frame of every turn that
  started and ended on the floor without a jump press (168 377 frames,
  3 559 of them held toward the rise, 439 of those on a T + 4), `facing`
  matched the rule above with 0 differences. Of the 17 759 ends of a
  turn on T + 8 (995 of them in turns with frames held toward the rise,
  203 of those going on with a new turn) and the 12 106 turn starts while
  walking or running (94 on frames held toward the rise), all matched
  the target rule; 68 973 standing frames (24 448 held toward the rise)
  started a turn exactly when it says. Controls on the same frames:
  with turns started by the held direction, 120 differ; without
  `facing` turning at once during turns, 957; with the turn's T + 4
  change before turning to the held side, 439.

## Speed on a face

A frame **starts on a face** when the player is grounded and their
column before the frame is a face column. On such a frame the target
speed of `ground-speed.md` is replaced by:

| Held (Action not held) | Target, face falling to the right | Target, face falling to the left |
|---|---|---|
| Nothing | +1.5 (+384/256) | −1.5 (−384/256) |
| The uphill direction | +1.5 (+384/256) | −1.5 (−384/256) |
| The downhill direction | +3.5 (+896/256) | −3.5 (−896/256) |

That is 1.5 px per frame downhill, plus the normal walking target when
the downhill direction is held. A held direction that points uphill is
ignored. Then the speed rule of `ground-speed.md` applies unchanged
(gap ÷ 8 rounded toward zero, or the target itself when that is 0) and
`x` changes by the new `vx`.

- The frame on which the player moves **onto** a face from other floor
  doesn't start on a face, so it uses the normal target. The face target
  applies from the next frame.
- The face target also applies on the frame on which the player leaves
  the ground from a face (frame L below), because that frame started on a
  face.
- A player can't stand still on a face: with nothing held, the speed
  moves toward 1.5 px per frame downhill.
- Facing didn't change on any measured frame that started and ended on
  face columns, including up to 10 frames in a row with the uphill
  direction held. Whether a longer hold turns the player on a face isn't
  covered.
- After a landing, the 1.5 px per frame downhill starts later, and a
  held uphill direction is still ignored before it starts: see "Landing
  on or next to a face" below.

## Following a face down, or leaving it

On every grounded frame, after the horizontal move (and if the player
wasn't blocked), compare the floor of the new column with the floor of
the column before the frame:

- **8 px lower or less**: the player stays grounded and `y` becomes the
  new column's floor height. `ground` stays 1 and, as on any grounded
  frame, `vy` in a trace is 0. A new column 1 px higher (as at the end
  of a face, or on a gentle slope) is followed the same way, and so is a
  column 4 or 8 px higher that isn't a face column (measured on a
  platform's top, `platforms-from-below.md`), and the floor 7 px higher
  beyond a face's top reached past one face column ("Moving past a
  face's top"). Other higher floor ahead
  is blocked (above), makes the player fall through the floor (a
  platform's top more than 8 px up, `platforms-from-below.md`), or is
  not covered.
- **11 px lower or more**, or **no floor** in the new column: the player
  leaves the ground. This frame is the leave frame **L** of
  `walk-off.md`: `y` moves down by exactly 3.4375 px whatever the floor
  below, `vy` ends at 0.00390625, and the fall continues as
  `walk-off.md` describes, with horizontal speed by `air-control.md`
  from L + 1 on and the landing rule of `landing.md`.

Drops of 9 and 10 px can't happen on a face whose columns are 4 px
apart and weren't measured. On such a face the rule means: crossing at
most **2 columns** in a frame keeps the player on the face; crossing 3 or
more makes them leave it. At the speeds above that depends on the speed
and on the fraction of `x`, so a player walking onto a face usually
follows it for a few frames and then leaves it.

## Leaving a face onto floor

When a grounded player moves off the low end of a face onto other floor
and stays grounded, the face is a rise behind them: on a face rising
4 px per column whose lowest column is 1 px above the floor beyond it,
the three columns next to the face are the ones whose column 5 ahead,
toward the face, is 8 px or more higher. On frames that start in those
columns with the direction back up the face held, "Holding toward a
rise" above applies as anywhere else: the target speed is the previous
frame's target, unchanged, with the same exceptions (take-off frames,
and landing frames up to the switch frame M).

For this, a frame that started on a face column counts as having the
target of the held direction when that direction points down the face
(the normal walking target, −2 px per frame on a face falling to the
left), and target 0 when nothing or the uphill direction was held. The
1.5 px per frame downhill part of "Speed on a face" isn't kept. So, with
the direction back up the face held on every frame from the last frame
on the face on:

- If nothing or the uphill direction was held on the last frame on the
  face, the target is 0 on every frame that starts in the three columns
  next to the face: the player slows down under target 0 and doesn't
  start moving back toward the face. The normal target applies from the first
  frame that starts 4 or more columns from the face. How many frames
  that takes depends on the speed coming off the face: 2 at the usual
  speed of a player carried down the face, more for a slower player
  (4 in the example below), and the target is 0 on the third and fourth
  frames off the face as well if they still start next to it.
- If the downhill direction was held on the last frame on the face, the
  target stays the downhill walking target on those frames, and the
  player keeps speeding up away from the face until they start a frame
  4 or more columns from it.

A held direction pointing down the face, away from the rise, applies
normally from the first frame off the face, as does any direction once
the player starts a frame 4 or more columns from the face.

**Facing.** On each of the first four frames off the face that starts
next to the face and is held back up it, `facing` ends on the held side,
turning on that frame if it was the other way. Frames further from the
face are not covered.

Example, the player carried down a face falling to the left, Right held
on every frame (`x` relative to the left edge of the face's lowest
column, `y` relative to the floor beyond the face, so the lowest face
column is at `y` −1). Frame 1 starts on the face (target −1.5 with the
uphill direction ignored). Frames 2 to 5 start 1, 2, 2 and 3 columns
from the face, with target 0 (the frame on the face counts as 0), and
frame 6 starts 4 columns away, where the target is +2.

```
frame	input	x	y	vx	facing	target
0	.	0.76171875	-1	-0.9765625	L
1	R	-0.27734375	0	-1.0390625	L	-1.5
2	R	-1.1875	0	-0.91015625	R	0
3	R	-1.984375	0	-0.796875	R	0
4	R	-2.68359375	0	-0.69921875	R	0
5	R	-3.296875	0	-0.61328125	R	0
6	R	-3.5859375	0	-0.2890625	R	2
7	R	-3.58984375	0	-0.00390625	R	2
8	R	-3.34375	0	0.24609375	R	2
```

With Left held on the last frame on the face instead, and Right from the
first frame off it, the target stays −2 on the frames next to the face
(in one run `vx` went from −1.69921875 to −1.734375 on the first frame
off it), and +2 from the first frame 4 columns away.

Measured moving left off the foot of a face falling to the left (see
"Evidence (leaving a face onto floor)" below). Leaving a face moving
right is not covered.

### Evidence (leaving a face onto floor)

- 1 200 runs of 100 frames on a face falling to the left, 4 px per
  column, whose lowest column is 1 px above a flat floor. Half the runs
  started by dropping straight onto one of the face's 11 lowest columns
  (`vx` 0, 4 to 40 px above it), half by creeping onto it from the flat
  floor at its top; on the face the runs held Right, nothing or Left
  (3 : 2 : 1, 1 to 6 frames at a time), and once on the floor below,
  Right, Left or nothing (4 : 1 : 1, 1 to 12 frames at a time) with an
  occasional jump. On the 63 073 frames that started and ended on the
  floor below or beside the face (blocked frames left out), the target
  speed matched "Holding toward a rise" with face frames counted as above,
  and `ground-speed.md`, with 0 differences.
- Of these, 833 frames were held Right on every frame since leaving the
  face, were the third or a later frame off it, started next to the face
  with the player still moving, and had target 0. 1 193 more like them
  were the first or second frame off the face. 288 frames held Right after
  Left on the last frame on the face kept the target −2. 66 second frames
  off the face that started 4 columns or more from it, Right held, had
  target +2.
- Controls on the same frames: with target 0 for exactly the first two
  frames off the face and the rise rule after that, 412 frames differ;
  with target 0 for exactly the first two frames and the normal target
  from the third, 2 204; with a face frame's kept target taken as the
  whole face target (−1.5, or −3.5 with the downhill direction held),
  2 370.
- `facing` on the 2 112 frames held Right among the first four off the
  face that started next to it: the held side on every one, 520 of them
  turning on that frame.

## Landing on or next to a face

This section covers what happens after a jump lands, after a bounce
lands (`bounce.md`), or after a fall that started by leaving the floor
of a face lands (as in "Following a face down, or leaving it"), when
the player is on a face column on the landing frame or reaches one in
the frames after it. Here **L** is the landing frame, the frame
carrying `land` (`landing.md`), not the leave frame above. Frames that
start in the air, the landing frame included, are as in `air-walls.md`
and `landing.md`.

After such a landing two of the rules above wait for a **switch frame
M**, which is L or later:

- the 1.5 px per frame downhill of "Speed on a face" is added only from
  frame M + 1 on, and then by the push rule below;
- "Can't start toward a rise" applies only from frame M + 1 on.

On frames L + 1 to M the player is on the floor and otherwise moves as
on any frame on the floor: a held direction that points uphill is
ignored on a frame that starts on a face (target 0, as in "Speed on a
face"); the speed rule of `ground-speed.md` applies to the target
without the 1.5; "Stopped by a rise" and "Following a face down, or
leaving it" apply as usual. So a player who lands on a face can stay
still on it, or go on moving up it for a few frames, before being
carried down.

### The switch frame M

**After a fall from a face** (no jump): M = L.

**After a bounce** (`bounce.md`): M = L, whatever was held and wherever
the landing is, so the push and "Can't start toward a rise" apply from
L + 1.

**After a jump**, let T be the frame carrying `jump_start`
(`jump-arc.md`). Let **A**, the **approach frame**, be the first frame
from **T + 20** on at whose end there is floor within reach below the
player. In level y (`view-vertical.md`), with the feet line F (the
player's level y rounded up to a whole px) and the floor `Y` of the
column containing `x`: the floor is within reach when it is at or below
the feet and at most **71 px** below them (0 ≤ `Y` − F ≤ 71), and its
32 px band is at most 2 more than the band of F, as in
`view-vertical.md` ("The ground used"). A player on the floor always has
floor within reach, so A is never later than the first frame from T + 20
on which the player is on the floor. A doesn't depend on whether the
player is still rising.

If at the end of A `x` is in a face column, the landing is a **slide
landing**, otherwise a **plain landing**.

- **Plain landing.** Take the seven **stage frames** A, A + 2, A + 4,
  A + 6, A + 8, A + 11 and A + 15 if A = T + 20, or A, A + 1, A + 3,
  A + 5, A + 7, A + 10 and A + 14 if A is later than T + 20.
  - If L < A: M = A + 4.
  - Otherwise, with n the number of stage frames that are L or earlier:
    M = L if n = 7; L + 1 if n = 6; L + 2 if n is 5 or 4; L + 3 if n is
    3 or 2; L + 4 if n = 1. For example, with A = T + 20, L = A + 8
    gives n = 5 and M = L + 2, and L = A + 12 gives n = 6 and M = L + 1.
- **Slide landing.** Let R be the first frame from L or A on, whichever
  is later, that ends on the floor of a column that isn't a face column.
  Then M = R + 11. While the player stays on face columns, there is no
  such R and only the next rule ends the wait. If the player lands on
  floor that isn't a face column and stays on such floor, M is
  max(L, A + 3) + 11. This is later than R + 11 when L is A + 1 or A + 2,
  and it sets when the next jump can start (`jump-input.md`, "After a
  jump over a face's top").

In every case, M is never later than the first frame from L on that
ends on the floor of a face column with `vx` 0 or `vx` pointing downhill
(for example a frame blocked by a rise, or one on which the speed comes
to 0). If that frame comes first, it is M.

Of the 1 047 landings on a face column in the first set of runs below,
685 were slide landings; M was L on 337 of them, L + 1 on 461, and later
(or not before the check ended) on the other 249.

### The push

From M on, the 1.5 px per frame downhill is a **push**, worked out at
the end of every frame and added to the next frame's target speed
before the speed rule:

1. If the player ended the frame in the air, or the frame is earlier
   than M, the push becomes 0.
2. Otherwise, if the column containing `x` at the end of the frame isn't
   a face column, the push becomes 0.
3. Otherwise, on a face falling to the right: if the frame's target
   speed (after the uphill rule, before any push) is 0 or more **and**
   the new `vx` is 0 or more, the push becomes +1.5 (+384/256). If either
   points left, the push keeps the value it had. On a face falling to
   the left the same holds mirrored: target 0 or less and `vx` 0 or less
   give −1.5 (−384/256), otherwise the push is kept.

Away from landings (where there is no M to wait for) this gives exactly
the targets of "Speed on a face": a player can only be moving uphill on
a face column after a landing.
After a landing, rule 3 means that a player still moving uphill on M
gets the push only once `vx` has stopped or turned.

### Examples (landings)

Measured, with positions made relative. `y` is level y (`view-vertical.md`)
of a synthetic floor: `y` = 81 for columns up to 99; a face rising to the
right at columns 100 to 115 (`y` = 80, 76, 72, …, 20; column 100 is 1 px
above the floor before it); `y` = 17 from column 116 on. Frame numbers
count from the jump's T. "Column floor" is the floor of the column
containing `x` at the end of the frame.

**A plain landing, downhill direction held.** A jump from standing at
`x` = 95.75 on the floor at `y` = 81, T = frame 0, Jump not held after
frame 2:

| Frame | Held | `x` | Column floor | `y` | `vx` (1/256) | `vy` (1/256) | `ground` |
|---|---|---|---|---|---|---|---|
| 19 | R | 97.953125 | 81 | 28.15625 | 211 | 296 | 0 |
| 20 = A | R | 98.921875 | 81 | 29.75 | 248 | 408 | 0 |
| 21 | R | 100.01953125 | 80 | 31.78125 | 281 | 520 | 0 |
| 22 | – | 100.98046875 | 80 | 34.25 | 246 | 632 | 0 |
| 23 | – | 101.82421875 | 76 | 37.15625 | 216 | 744 | 0 |
| 24 | L | 102.3125 | 72 | 40.5 | 125 | 856 | 0 |
| 25 | R | 102.98828125 | 72 | 44.28125 | 173 | 968 | 0 |
| 26 | R | 103.828125 | 68 | 48.5 | 215 | 1080 | 0 |
| 27 | R | 104.8125 | 64 | 53.15625 | 252 | 1192 | 0 |
| 28 = L | L | 105.42578125 | 60 | 60 | 157 | 0 | 1 |
| 29 | L | 105.71484375 | 60 | 60 | 74 | 0 | 1 |
| 30 = M | L | 105.71875 | 60 | 60 | 1 | 0 | 1 |
| 31 | L | 105.47265625 | 60 | 60 | −63 | 0 | 1 |
| 32 | L | 104.8203125 | 64 | 64 | −167 | 0 | 1 |
| 33 | L | 103.8125 | 68 | 68 | −258 | 0 | 1 |

- Frame 20 is the first frame from T + 20 on with floor within reach:
  feet line 30, floor 81 of column 98, 51 px below; band of 30 is 0,
  band of 81 is 2. So A = 20. Column 98 isn't a face column: a plain
  landing.
- Frame 28 lands on face column 105 (`landing.md`). A = T + 20 and
  L = A + 8, so five stage frames (A to A + 8) are L or earlier and
  M = L + 2 = 30. Frames 28 to 30 end with `vx` pointing uphill
  (right), so the last rule doesn't make M earlier.
- Frames 29 and 30 start on the face, Left points downhill: target −512,
  no push. Step (−512 − 157) ÷ 8 = −83, `vx` 74; then −73, `vx` 1.
- Frame 30 is M, but its `vx` (+1) points uphill, so the push keeps its
  value 0.
- Frame 31: step (−512 − 1) ÷ 8 = −64, `vx` −63. Target and `vx` now
  both point left (downhill): the push becomes −1.5.
- Frame 32: target −512 − 384 = −896, step −104, `vx` −167. `x` moves
  into column 104, 4 px lower: followed.

With the 1.5 added from frame 29 on, as "Speed on a face" alone would
give, `vx` on frame 29 would be 26 instead of 74.

**A slide landing, uphill direction held.** Right held on every frame,
falling past the face after a jump (T = frame 0):

| Frame | Held | `x` | Column floor | `y` | `vx` (1/256) | `vy` (1/256) | `ground` |
|---|---|---|---|---|---|---|---|
| 11 | R | 111.7578125 | 36 | 32.875 | 407 | −560 | 0 |
| 12 | R | 111 | 36 | 31.125 | 0 | −448 | 0 |
| 13 | R | 111.25 | 36 | 29.8125 | 64 | −336 | 0 |
| 14 | R | 111.71875 | 36 | 28.9375 | 120 | −224 | 0 |
| 15 | R | 112.37890625 | 32 | 28.5 | 169 | −112 | 0 |
| 16 | R | 112 | 32 | 28.5 | 0 | 0 | 0 |
| 17 = L | R | 112.25 | 32 | 32 | 64 | 0 | 1 |
| 18 | R | 112.46875 | 32 | 32 | 56 | 0 | 1 |
| 19 | R | 112.66015625 | 32 | 32 | 49 | 0 | 1 |
| 20 = A | R | 112.828125 | 32 | 32 | 43 | 0 | 1 |
| 21 | R | 112.9765625 | 32 | 32 | 38 | 0 | 1 |
| 22 = M | R | 112 | 32 | 32 | 0 | 0 | 1 |
| 23 | R | 111.8125 | 36 | 36 | −48 | 0 | 1 |
| 24 | R | 111.4609375 | 36 | 36 | −90 | 0 | 1 |

- Frames 12 and 16 are blocked in the air by face columns above the feet
  (`air-walls.md`). Frame 17 lands on face column 112.
- A is frame 20, the first frame from T + 20 on; the player is on the
  floor of face column 112, so this is a slide landing. The player never
  leaves the face, so M comes from the last rule.
- Frames 18 to 21 start on the face: Right points uphill and is ignored
  (target 0), and there is no push, so `vx` falls by an 8th of itself
  each frame and the player creeps up the face.
- Frame 22: `vx` 33 would take `x` into face column 113, which rises to
  the right: blocked, `x` 112, `vx` 0. It ends on a face column with `vx`
  0, so M = 22, and the push becomes −1.5 at its end.
- Frame 23: target 0 − 384, step −48. The player is carried down.

### Evidence (landings)

Measured with the lead character, by inputs only, objects nearby moved
out of the way, on a face falling to the right whose low end meets a
drop, the face rising to the right on the drop's far side, and a
64 px face rising to the right from the floor beyond. Random inputs
(Left, Right, Down, Jump and Action, held and released at random), from
a start on the floor before the first face and from one on the floor
before the 64 px face:

- 800 runs, 296 494 frames, used to find the rules: 1 682 landings
  followed onto or near a face (1 047 of them on a face column), 12 088
  frames checked after them, 1 480 of them from L + 1 to M;
- 800 further runs with new seeds, 293 557 frames, recorded afterwards
  to test them: 1 753 landings (1 056 on a face column), 12 866 frames
  checked, 1 582 from L + 1 to M.

`x`, `y`, `vx` and `ground` matched the rules on every checked frame
except one in each set, both a move across two face columns onto the
floor 11 px higher beyond the face's top; the third case of "Stopped by
a rise" gives both. Applying the push and "Can't start toward a
rise" from L + 1 instead would have given 609 and 622 different frames.
The approach frame was also checked directly against the player's pose,
which changes on A: it matched in 4 952 of 4 954 jumps in the first set
and 4 819 of 4 821 in the second, and so did the choice between a slide
and a plain landing. The four others fell into the drop, below the
floor. The push rule matched on every frame of the runs that wasn't
part of a roll, or a jump's press frame or T: 589 883 frames.

Landings after a bounce were measured separately, in the first level
with the lead alone and the walkers in place: one face falling to the
left, 28 px high (7 columns), with gently sloping floor below it and
flat floor at its top, and a walker to bounce off on each side of it.
Random inputs (Left, Right and Jump, and Action in some runs), started
at different frames before and during the bounces:

- 3 200 runs used to find the rule: 2 185 landings after a bounce, 878
  of them followed onto or near the face (253 on a face column on L),
  14 106 frames checked after them;
- 1 000 further runs with new seeds and start frames: 651 landings, 319
  near the face (70 on a face column on L), 4 630 frames checked.

`x`, `y`, `vx` and `ground` matched M = L on every checked frame.
M = L + 1 would have given 253 and 70 different frames, M = L + 2 749
in the first set, and the rule "After a jump" counted from the bounce
frame 584.

## Jumping on or next to a face

How a jump press works on a face and in front of a rise. **P** is the
press frame and **T** the take-off frame, the row carrying `jump_start`
(`jump-input.md`, `jump-arc.md`). As in `walk-off.md`, a frame **starts
on the face** if the frame before it ended with the player on the floor
on a face column, being carried down the face. The frame that moves the
player onto a face from other floor doesn't start on it.

### The press frame

P is a frame on the floor like any other: every rule above applies to
it. A player standing in front of a rise with a direction held toward it
stays put on P ("Can't start toward a rise"), a move into a rising face
column on P is blocked, and on a face the player is carried down it on P
as usual.

### When the jump takes off

A press on P that starts on the floor takes off on **P + 1**, as in
`jump-input.md`, unless P is a **carried press**: P starts on the face,
or P + 1 does. So a press on the frame that moves the player onto a
falling face, or on any frame while they are carried down it, doesn't
take off on P + 1. The one exception measured: on the frame that moves
the player onto a face from other floor with only the face's uphill
direction held (Left on a face that falls to the right, Right on one
that falls to the left), the next frame doesn't count as starting on
the face for this rule, and a press on that frame takes off on P + 1.

A carried press works like a press made in the air (`jump-input.md`,
"Pressing before landing"): it is remembered only while Jump stays held,
for a limited time.

- Let **Q** be the first frame after P that starts with the player on
  the floor on a column that isn't a face column. Walking or coasting
  off the low end of a face, Q is the first frame that starts beyond the
  face's last column. If the player leaves the ground on the face and
  lands on other floor, Q is the frame after the landing frame
  (Q = L + 1, so T = L + 2).
- If Jump is held on every frame from P to Q, the jump takes off on
  **T = Q + 1**, provided that is at most 12 frames after the press
  (T ≤ P + 12, the same window as a press made in the air).
- Otherwise no jump starts. A new press is needed.

Until T the press changes nothing: the frames from P to Q move exactly
as they would without it (the face rules above, and for a fall from the
face the rules of `walk-off.md`, including the gravity rule there for a
press that counted). Q is an ordinary frame on the floor; "Leaving a
face onto floor" applies to it as usual.

### The take-off frame

On T the player is still on the floor, `ground` is 1 and `vy` becomes
−7 (`jump-arc.md`). Horizontally T is a frame of the jump
(`air-control.md`), next to faces as well:

- The target speed is the one for the held input, from the speed at the
  end of P (or Q): with Right held from a standstill, `vx` is 0.25 on T.
- "Can't start toward a rise" doesn't apply on T, even when `vx` is 0
  before it. So a player who stood still on P in front of a rise moves
  on T.
- "Stopped by a rise" does apply: if the move on T would take `x` into a
  face column that rises in the direction of the move, `x` is set to the
  left edge of the column the player was in and `vx` becomes 0, as on
  any frame on the floor.
- No 1.5 px per frame is added on a face, and "Leaving a face onto floor"
  doesn't apply on T: after a carried press, a held direction pointing
  back up the face counts normally on T, though it was ignored on Q.
  If T itself starts on a face column (after the exception above), a
  held uphill direction is ignored on T, as in "Speed on a face".
- `y` follows the floor of the new column, as on any frame on the floor:
  moving onto a face column on T, the player is placed on it (falls of
  3, 4 and 7 px and a rise of 1 px were measured on T). The take-off
  then starts from there on T + 1.

From T + 1 the jump follows `jump-arc.md` and `air-control.md`, with the
lighter gravity counted from P (Jump has been held since P). Contact
with faces and walls in the air is in `air-walls.md`.

### Examples (jumps)

Synthetic floors, as in "Examples" below. `y` is down-positive.

**A press while carried down a face.** Flat floor at `y` = 0 for
columns up to 299, a face falling to the right on columns 300 to 311
(`y` = 2, 6, …, 46) and flat floor at `y` = 47 from column 312. Nothing
held, `x` = 299 and `vx` = 1 (256/256) before frame 1; Jump pressed on
frame 4 and held:

| Frame | Column before the frame | `x` | `y` | `vx` (1/256) | Note |
|---|---|---|---|---|---|
| 3 | 300 | 301.49609375 | 6 | 219 | Carried down the face |
| 4 | 301 | 302.4296875 | 10 | 239 | P: starts on the face, no take-off on 5 |
| 5 | 302 | 303.43359375 | 14 | 257 | Still carried, as without the press |
| 12 | 310 | 311.78515625 | 46 | 332 | |
| 13 | 311 | 313.10546875 | 47 | 338 | Off the face's low end: 1 px drop, followed |
| 14 | 313 | 314.26171875 | 47 | 296 | Q: first frame starting off the face; normal target 0 |
| 15 | 314 | 315.2734375 | 47 | 259 | T = Q + 1 = P + 11: `jump_start`, `vy` −7 |
| 16 | 315 | 316.16015625 | 40.28125 | 227 | In the air: `vy` −6.71875 |

With the press on frame 2 instead (the frame that moves onto the face,
so frame 3 starts on it), Q is still frame 14 and Q + 1 = 15 = P + 13:
too late, and no jump starts. Frames 2 to 17 are the same as without the
press.

**Standing in front of a rise** (the rise of "Examples" below: flat
floor at `y` = 0 up to column 199, a face rising to the right from
column 200). Right and Jump pressed together on frame 1:

| Start `x` | Frame 1 (P) | Frame 2 (T) |
|---|---|---|
| 199 | Stays at 199, `vx` 0 (column 204 is 17 px higher) | `x` 199.25, `vx` 64/256, `y` 0, `vy` −7 |
| 199.875 | Stays at 199.875, `vx` 0 | The move to 200.125 is into face column 200: blocked, `x` 199, `vx` 0, `vy` −7 |

### Evidence (jumps)

Measured with the lead character on the faces of "Evidence (landings)"
and on a longer face (32 columns) falling to the right, by inputs from
the floor near each face, and with the player placed on the floor in
front of a rise:

- standing in front of a face rising to the right, at every 1/16 px over
  8 px (128 starts), with five press variants (Right with Jump, Jump
  then Right, Right and Action with Jump, Jump alone, one frame of Right
  first): 640 presses;
- walking toward the same rise from 40 starts, pressing after 14 to 22
  frames, with Right or with Right and Action: 720 presses;
- coasting, walking left, or holding Right while moving left, onto a
  face falling to the left from its top, presses 0 to 11 frames later
  with nothing, Left, Right, or Left and Action held: 1 728 cases;
- walking or coasting onto a face falling to the right at the top of a
  pit: 576 cases;
- coasting down the 32-column face, Jump held 3, 13, 30 or 60 frames:
  1 152 cases;
- jumping onto and over the face rising to the right, pressing 0 to 8
  frames after the landing: 1 080 cases.

Of 4 468 presses on frames that started on the floor (presses in the
air, during a roll, presses after a landing on flat floor, which fall in
the landing lockout of `jump-input.md`, and frames where `ground` read 1
against a wall in the air left out), 1 236 were carried presses: 307
took off on Q + 1, from Q + 1 = P + 2 to P + 12 (56 of them after a fall
from the face), and 929 gave no jump (Jump released before Q, or
Q + 1 later than P + 12: 164 held through Q with Q + 1 from P + 13 to
P + 29). The other 3 232 presses took off on P + 1. The take-off frame
matched the rule for every press. Counting a press as carried only when
P itself ends on a face column, or dropping the uphill exception, gives
27 and 28 wrong take-offs; a window of 11 or 13 frames gives 64 and 12;
taking off on P + 1 for every press gives 1 236.

On every one of the 5 540 take-off frames recorded, `x` and `vx` matched
the target rule and the blocking rule above (194 blocked), and `y`
matched the floor of the new column. On the 144 presses in front of the
rise where "Can't start toward a rise" applied on P, the player stayed
put on P and moved on T.

The face rules of this file were rechecked on the frames on the floor
from each press on, on the face falling to the left and the one at the
top of the pit (38 230 frames checked for speed): every frame matched except frames that check doesn't model
("Leaving a face onto floor", frames L + 1 to M after a landing, rolls)
and the case under "Not covered" of moving left off a face's low end at a
drop. The leave frames of falls from a face moved down 3.4375 px after a
press that started on the face (476) and 3.28125 px after one that
counted and was still held (122), as in `walk-off.md`.

## Rolling on, onto or into a face

`title-1` only. What happens to a roll (`roll-input.md`, `roll-speed.md`)
on a face, moving onto one, and against a rise. S is the roll's press
frame and the roll target is as in `roll-speed.md`: a direction times a
size of 1, 3 or 4 (256/256, 768/256, 1024/256), or 2 (512/256) after a
push-off ("Stopped rolls" below). "Starts on a face" is as
in "Jumping on or next to a face". The rules below were checked on the
frames on the floor from S to S + 28; every other rule of `roll-speed.md`,
`roll-input.md` and `rolling-off-ledges.md` applies unchanged. Down
held during a roll or the stop after one makes no difference on or next to
a face either, for either character (`roll-input.md`, "Down during a
roll"; `second-character.md`, "Rolls"). One rule of
`roll-input.md` changes on a face: a roll never starts on a frame that
starts on a face. A press that would start one there is carried like a
jump press until the player is off the face ("When a roll starts on a
face" below).

### When a roll starts on a face

Call **S₀** the frame on which `roll-input.md` would start the roll:

- for a press on a frame P on the floor, S₀ = P;
- for a press made in the air and remembered through the landing
  (`roll-input.md`, "In the air"), S₀ = L + 1, where L is the landing
  frame;
- for a press on D + 2 or D + 3 after a roll that carried on into
  running, S₀ = D + 4 (`roll-input.md`, "Starting the next roll").

If S₀ starts on a face, no roll starts on S₀. Unlike a jump press, a press
on the frame that moves the player onto a face from other floor isn't
carried: that frame doesn't start on the face, and the roll starts on it
as usual. A press in the air is carried when the landing frame L ends with
the player on the floor of a face column.

A carried press works like a carried jump press ("When the jump takes
off"):

- Let **Q** be the first frame after S₀ that starts with the player on
  the floor on a column that isn't a face column. Walking or coasting off
  the low end of the face, Q is the first frame that starts beyond its
  last column; if the player leaves the ground on the face and lands on
  other floor, Q is the frame after the landing frame.
- If Action is held on every frame from P to Q and Q ≤ **P + 7**, a roll
  starts on Q (`roll_start` on Q).
- Otherwise no roll starts. A new press is needed; a press on Q or later
  starts a roll on its own frame as usual.

The window counts from the press, also for a press made in the air: a
press on L − 1 rolls only if the player is off the face by L + 6.

Example: a bounce (`bounce.md`) lands on a face rising to the right whose
lowest column is c₀ (1 px above the floor at its foot; each column 4 px
higher than the one before), on its fifth column, with Right held until
the landing and nothing after it. `x − c₀` and `vx` are at the end of each
frame:

| Frame | x − c₀ | Floor (px above the foot) | vx | |
|---|---|---|---|---|
| L | 4 | 17 | 0 | `land` |
| L + 1 | 3.8125 | 13 | −0.1875 (−48/256) | carried down |
| L + 2 | 3.4609375 | 13 | −0.3515625 (−90/256) | |
| … | | | | |
| L + 6 | 0.8046875 | 1 | −0.8203125 (−210/256) | |
| L + 7 | −0.09765625 | 0 | −0.90234375 (−231/256) | at the foot |

L + 7 ends beyond the face's lowest column, so Q = L + 8. With Action
pressed on L − 1 and held, the frames from L to L + 7 are as in the table
and no roll starts on L + 8 (Q = P + 9); the player coasts on. With
Action pressed on L + 1 instead and held, the frames from L to L + 7 are
the same and the roll starts on L + 8 (Q = P + 7).

### Steering on a face

On a frame that starts on a face, Left and Right do nothing to the roll:
the roll's direction doesn't reverse, and a slow roll doesn't become a
normal one ("Steering during the roll" in `roll-speed.md`). On every other
frame, in the air as well, steering works as usual. So a roll pointing
up a face keeps pointing up it while the player is on the face, whatever
is held.

### Speed on a face

On a frame that starts on a face, the target of the speed rule for a
roll that isn't stopped ("Stopped rolls" below) is:

- if the roll points **downhill**: the roll target plus 1.5 (384/256)
  downhill. A normal roll's target is 4.5 (1152/256), a slow roll's 2.5
  (640/256), a fast roll's 5.5 (1408/256), in the downhill direction;
- if the roll points **uphill**: the roll target alone. There is no
  downhill part, so a roll can move up a face.

The frame that moves the player onto a face from other floor uses the roll
target alone. Then `x` changes by the new `vx`, and the player follows the
face down or leaves it as in "Following a face down, or leaving it". At
these speeds a normal or fast roll pointing downhill crosses three or
more columns in a frame and so leaves the ground on its first frame on
the face; slow rolls can follow it.

### Blocked

A roll frame on the floor is blocked as in "Stopped by a rise": when the
move would take `x` into a different column that is a face column of a
face rising in the direction of the move. It is also blocked when the new
column isn't a face column but its floor is **more than 8 px** higher
than the floor before the frame: a roll moving up off a face's top over
two face columns, onto floor 11 px higher, was blocked; one moving over
one face column onto floor 7 px higher followed it. On a blocked frame
`x` is set to the left edge of the column the player was in, `vx`
becomes 0, and the player stays on that floor.

"Can't start toward a rise" doesn't apply to roll frames: a roll pressed
while standing in front of a rise starts moving on S as usual and is
blocked when it reaches the face. What keeps a roll at a rise is the
next rule.

### Stopped rolls

A roll is **stopped** from a blocked frame, or from a stopped frame of
`rolling-off-ledges.md` ("Stopping before a ledge"), until it **pushes
off**. While a roll is stopped:

- its own target is 0: on other floor it doesn't move (`vx` stays 0);
- on a frame that starts on a face its target is 1.5 (384/256) downhill
  alone, whichever way the roll points: the player is carried down the
  face like a player standing on it, with `vx` 0.1875, 0.3515625,
  0.4921875, … downhill from a standstill.

A stopped roll pushes off on a frame that doesn't start on a face, on
which exactly one of Left or Right is held and it is the roll's direction
(after any reversal on that frame, so holding the other direction turns
the roll and pushes off at once). Whatever `vx` was, it becomes **2**
(512/256) in the roll's direction, and the player moves by it, unless the
move is blocked, in which case the roll is stopped again. After a
push-off the roll is no longer stopped and its size is 2 (512/256):
holding its direction on a later frame makes it a normal roll (3), as a
slow roll becomes normal; with nothing held it goes on at 2.

So:

- A roll that reaches a rise stops at the face's foot. Holding the
  direction toward the rise, every frame is a push-off that is blocked:
  the player stays put, `vx` 0, until the roll ends. Holding the other
  direction rolls away at once at 2 and then toward 3.
- A roll pointing up a face moves up it with its own target until it
  enters a higher face column (blocked), and is then carried down to the
  foot. Reaching the floor at the foot with the uphill direction held,
  its first frame there is a push-off into the face, blocked: `x` loses
  its fraction, `vx` becomes 0, and the player stays at the foot. With no
  direction held it keeps moving away from the face, slowing toward 0
  (its target is 0).
- A roll that stopped before a ledge and is then reversed by holding the
  other direction pushes off at 2 on that frame.

### Examples (rolls)

Synthetic examples on a face rising to the right whose first column is
c₀: the floor is flat up to column c₀ − 1, and the face columns are 1, 5,
9, … px higher. `x − c₀` and `vx` are at the end of each frame. Frame k is
the last frame before the rows (a frame of the roll on which it was
stopped), with `vx` 0.

Stopped at the foot, `x` = c₀ − 1, the roll pointing right; Right held on
k + 1 to k + 3, Left on k + 4 to k + 6, then nothing:

| Frame | Held | x − c₀ | vx | |
|---|---|---|---|---|
| k + 1 | Right | −1 | 0 | push-off, blocked |
| k + 2 | Right | −1 | 0 | push-off, blocked |
| k + 3 | Right | −1 | 0 | push-off, blocked |
| k + 4 | Left | −3 | −2 (−512/256) | push-off (the roll turns) |
| k + 5 | Left | −5.125 | −2.125 (−544/256) | normal roll |
| k + 6 | Left | −7.359375 | −2.234375 (−572/256) | |
| k + 7 | nothing | −9.6875 | −2.328125 (−596/256) | |
| k + 8 | nothing | −12.09765625 | −2.41015625 (−617/256) | |

The same start with Left held only on k + 1: `vx` is −2 on k + 1 and stays
−2 on the following frames (`x − c₀` = −3, −5, −7, …).

Stopped on the face at `x` = c₀ + 6 (a floor 25 px up), the roll pointing
right, Right held throughout:

| Frame | x − c₀ | Floor (px above the foot) | vx | |
|---|---|---|---|---|
| k + 1 | 5.8125 | 21 | −0.1875 (−48/256) | carried down |
| k + 2 | 5.4609375 | 21 | −0.3515625 (−90/256) | |
| k + 3 | 4.96875 | 17 | −0.4921875 (−126/256) | |
| … | | | | |
| k + 8 | 0.92578125 | 1 | −0.9765625 (−250/256) | |
| k + 9 | −0.11328125 | 0 | −1.0390625 (−266/256) | at the foot |
| k + 10 | −1 | 0 | 0 | push-off, blocked |
| k + 11 | −1 | 0 | 0 | push-off, blocked |

### Evidence (rolls)

Measured with the lead character on the faces of "Evidence (jumps)", by
inputs from the floor near each face, and with the player placed on the
floor in front of the faces:

- standing and walking toward a face rising to the right from 60 places,
  with Action pressed alone, with the direction toward the face (then
  that direction held, with or without Action), with the other direction,
  with Action held after, or with the direction toward the face and then
  the other one held: 1 380 cases;
- walking, coasting or running left onto a face falling to the left from
  its top, presses before and on the face: 1 350 cases;
- walking, coasting or running onto the 32-column face falling to the
  right, presses before and on it: 782 cases;
- standing or walking on the floor at the foot of that face and rolling
  into it (it rises to the left): 272 cases;
- rolling into the face rising to the right and, after the first blocked
  frame, holding nothing, either direction, Action, Up, or Left and
  Action for 1, 2, 4 or 9 frames, then nothing or a direction: 432
  cases.

On the 64 628 frames on the floor from S to S + 28 of 2 660 of these
rolls, every frame matched the rules above, including 1 116 frames that
started on a face (559 downhill, 120 uphill with 24 of them blocked, 437
stopped and carried down), 9 515 push-offs that were blocked and 492 that
weren't. 1 600 more runs with random inputs, not used to find the rules,
gave 23 544 frames on the floor of 1 161 rolls with 875 face frames: 6
frames differed (in 4 rolls: a roll landing on a face, see "Not
covered", one roll stuck where the floor outside the measured columns
wasn't modelled, and two rolls pressed with Left held while facing right
at the foot of a face whose first frame already went left). Without the stopped-roll rule 4 769
and 1 559 frames differ; with steering on face frames 15 and 64, with
only the uphill direction ignored there 0 and 50, a slow roll becoming
normal on face frames 2 and 46, a push on uphill rolls 110 and 109, a
normal size after a push-off 190 and 140, push-offs only from `vx` 0 14
and 16, no block onto floor more than 8 px higher 1 and 50.

"When a roll starts on a face" was measured separately with the lead
character, from Action presses (no Jump held from the frame before the
press to 13 frames after it, no Down from the press to the roll) at two places in the first
level: a pit with an 8-column face falling into it on one side and a
4-column face rising out of it on the other, followed by a 16-column face
rising to the right; and the face falling to the left used for the
bounce landings in "Evidence (landings)", with walkers in place there:

- random walks, jumps and falls around the pit faces, and the same runs
  played again with Action taken out and then pressed once from L − 7 to
  L + 2 around each landing near a face, held 1 to 12 frames or longer:
  334 presses;
- bounces off a walker onto, beside and past that face, random
  directions and Action, and the same two-pass replays: 341 presses;
- walking and coasting down that face from its top with random
  directions and Action presses, no landing: 646 presses.

Of these 1 321 presses, 84 were made on the floor on frames starting on
a face and 85 in the air with a landing ending on a face column. Every
press matched the rule except 4 (see "Not covered"): a carried press held
to Q rolled on Q in all 43 cases with Q from P + 1 to P + 7 and in none of
the 10 with Q = P + 8. The roll rules of `roll-input.md` alone (a roll on
P, or on L + 1) give 175 wrong presses; carrying a press on the frame that
moves onto a face as well gives 22; a window of P + 6 or P + 8 gives 15
and 14. A bounce onto the face at many points with presses from L − 8 to
L + 12, with and without Jump, gave the same: with Jump pressed together
with Action, the jump went on and no roll started.

## Examples

Synthetic floors. `y` is down-positive, as in a trace.

**Walking into a rise.** Flat floor at `y` = 0 for columns up to 199. A
face rises to the right from column 200: column 200 at `y` = −1, 201 at
−5, 202 at −9, and so on (4 px per column). Walking right at +2 with
Right held, `x` = 193.5 before frame 1:

| Frame | `x` | `vx` (1/256) | Note |
|---|---|---|---|
| 1 | 195.5 | 512 | |
| 2 | 197.5 | 512 | |
| 3 | 199.5 | 512 | |
| 4 | 199 | 0 | Blocked: 201.5 is in face column 201 |
| 5 on | 199 | 0 | Right still held: column 204 is 17 px higher |

**Standing in front of the same rise**, Right held from frame 1:

- At `x` = 196.875 (column 196): column 201 is 5 px higher, so the
  player starts walking, as on flat floor: `x` is 197.125, 197.59375,
  198.25390625 and 199.078125 on frames 1 to 4 (`vx` 64, 120, 169, 211 in
  1/256). On frame 5 the move would reach 200.046875 (column 200), so the
  player is blocked: `x` 199, `vx` 0. They now stand closer to the rise
  than they could have started from.
- At `x` = 197 (column 197): column 202 is 9 px higher, so `vx` stays 0
  and `x` stays 197 on every frame.

**Coasting down a face.** Flat floor at `y` = 0 for columns up to 299; a
face falling to the right from column 300 (`y` = 2, then 6, 10, … for
columns 300, 301, 302, …). Nothing held, `x` = 299 and `vx` = 1
(256/256) before frame 1:

| Frame | Column before the frame | `vx` (1/256) | `x` | `y` | Note |
|---|---|---|---|---|---|
| 1 | 299 | 224 | 299.875 | 0 | Normal target 0 |
| 2 | 299 | 196 | 300.640625 | 2 | Onto the face: normal target 0 |
| 3 | 300 | 219 | 301.49609375 | 6 | Face target +384 |
| 4 | 301 | 239 | 302.4296875 | 10 | |
| 9 | 306 | 308 | 307.98046875 | 30 | |
| 10 | 307 | 317 | 309.21875 | 38 | Crossed 2 columns: 8 px drop, followed |
| 11 | 309 | 325 | 310.48828125 | 42 | |

`vx` keeps rising toward 384 and the player follows the face, crossing
one or two columns per frame.

**Walking down the same face.** Right held, `x` = 297.75 and `vx` = 512
before frame 1:

| Frame | `vx` (1/256) | `x` | `y` | `ground` | Note |
|---|---|---|---|---|---|
| 1 | 512 | 299.75 | 0 | 1 | |
| 2 | 512 | 301.75 | 6 | 1 | Onto the face: normal target |
| 3 | 560 | 303.9375 | 14 | 1 | Face target +896; 8 px drop, followed |
| 4 | 602 | 306.2890625 | 17.4375 | 0 | 3 columns: leaves (L), `y` + 3.4375 |
| 5 | 591 | 308.59765625 | 17.87890625 | 0 | Air: normal target +512 |

From frame 5 on, `vy` and the landing follow `walk-off.md` and
`landing.md`.

## Not covered

- Action held on a face while walking or standing (with or without a
  direction), except on the frames after a Down + Action press on or
  onto a face (`ground-action.md`, "At a steep face"). The ground action
  going down a face, against a rising face or a vertical step up, off a
  vertical step down and the jumps during it are in that section too.
- Air presses remembered through a landing on floor that isn't a face
  column just after the player passed low over a face's columns in the
  air (jumping up past a face's top, falling from a face across a pit
  onto the top of another): in 3 of 24 such landings no roll started on
  L + 1 (the other 21 followed "In the air"), and one press carried from such a landing on a face's top
  column started none on Q. Presses with Down held between the press and
  the roll near a face.
- Rolls started while the player is carried down a face or on the frames
  just after leaving one (up to about 12 frames): their direction and
  size on S didn't always follow `roll-speed.md` (the held direction
  won, and a recent running frame didn't always make them fast). Rolls
  that land on a face pointing downhill: in the one run seen, the first
  frame after the landing had no downhill part, and when the roll then
  slowed below 1.0625 (272/256) with no floor ahead as in "Stopping
  before a ledge", its speed went back to 0 at the start of every frame
  and the downhill part moved it 0.1875 px per frame. A roll stopped
  against a wall in the air acted as stopped and pushed off in the air
  in the runs seen; air contacts weren't checked. The roll's frames after
  S + 28 (its end, and rolls that go on in the air) weren't checked next
  to faces.
- Jump presses during a roll on or onto a face: some took off on the
  next frame and some didn't. Presses with the second character on a
  face. A take-off frame that would cross two or more face columns, or
  drop 8 px or more (falls of 3, 4 and 7 px were measured on T). The
  uphill exception in "When the jump takes off" was measured on one face
  (falling to the left, 16 presses).
- Landings on or next to a face after anything other than a jump, a
  bounce or a fall from a face (falls from ledges without a face, jumps
  that start in the air; rolls only as in "Rolling on, onto or into a
  face"), and jumps that take off from a face. Landings after a bounce
  were measured on one face falling to the left, with the lead alone,
  and none of them after bouncing again before landing.
  Contact with faces and walls while airborne is in `air-walls.md`,
  except falls into pits that carry the player below the floor of the
  column they are in.
- Moves past a face column of a rising face into a column that isn't a
  face column with floor 9 or 10 px higher, or onto floor beyond a face
  whose top joins it by a step other than 3 px.
- Which of standing, walking, running or a turn starts after a landing
  on or next to a face (`ground-speed.md`, "After a landing"): the
  landing checks above took the target speed from the game and tested
  only how the face rules change it.
- Moving left off the low end of a face that ends at a drop rather than
  at floor: the player was seen to leave the ground while still over the
  face's last column. (Moving right off such an end, the rule above
  holds.)
- Leaving a face onto floor while moving right; `facing` on frames off a
  face other than the first four next to it (35 of 948 frames held back
  up the face among the first four but 4 or more columns away didn't
  turn); Action held on the last frame on the face.
- Vertical steps of other heights, steps up of 9 to 63 px in general,
  faces steeper or less steep than 4 px per column, and a vertical step
  rising to the right.
- Differences of 6 and 7 px in the "can't start" rule.
- "Holding toward a rise": landings after falls and bounces, and slide
  landings, in front of a rise (only plain landings after jumps were
  measured). Turn checks while walking or running were not tracked
  frame by frame: every turn start seen had the target toward the side
  opposite to `facing`, but whether every turn check with such a target
  started a turn wasn't tested; no standing player held toward a rise
  had a target away from it. Whether a blocked frame ("Stopped by a
  rise") with the direction away from the rise held can start a turn.
- How a trace's `state` names frames on a face (the vocabulary has no
  name for being carried down a face).
- Entities, moving platforms and the other character.

## In `title-2`

Everything in this section was measured on `title-2` only, with the lead
character alone and no enemies nearby. Two steep faces were measured, both
rising to the right: one **64 px** high (16 face columns, with flat floor
at its foot and at its top) and one **96 px** high (24 face columns, flat
floor at its foot). The player walked, ran, rolled, turned, stopped and
started again against both faces from the left. They went down the 64 px
face to the left from the floor at its top, walking, coasting with
nothing held and holding the uphill direction. There were also random
sequences of Left, Right and nothing on and around both faces. Landings
on the 64 px face and every speed on its face frames were also measured
one frame at a time, from positions and speeds set before the frame.
Steps down from floor that isn't a face (3 to 10 px, between pieces of
every steepness) were measured on test floors that the levels don't
have, built next to the 64 px face, moving right and moving left. Speeds
are whole multiples of 1/256 px per frame, written both ways. "The
speed rule" is the one of `ground-speed.md` ("In `title-2`"): the
target is chosen from the frame's input, and the gap is divided by 8,
rounded toward zero, or the target itself is taken when that is 0.

### Faces (`title-2`)

Columns, steep faces, face columns, rising and falling are as defined
above in "Columns, faces and steps". Both measured faces have the
`title-1` shape. Each face column is exactly 4 px higher than the one
below it. The lowest face column is 1 px above the floor at the foot,
and the highest face column is 3 px below the floor at the top.

In `title-2` the floor is made of pieces 16 columns wide, each flat or
sloped (`slopes.md`, "In `title-2`"). The columns of a face are the
columns of **steep-face pieces**: pieces whose floor changes by 4 px from
each column to the next. On the measured faces these are exactly the
columns that the definition above names. A single 4 px step between
pieces of other kinds doesn't make face columns. A 4 px step down onto a
1-in-4 or 1-in-2 slope was followed (see "Following the floor down"
below). On the next frame the limits of "Speed on a face" didn't apply,
and on the frame after that there was no pull (1,755 frames).

### Stopped by a rise (`title-2`)

The `title-1` rule ("Stopped by a rise") holds unchanged. On a grounded
frame the player is **blocked** if the move would take `x` into a
different column that is a face column of a face rising in the direction
of the move. Then:

- `x` is set to the left edge of the column the player was in before the
  frame, a whole number;
- `vx` becomes **0**;
- the player stays grounded on the floor they were on.

A face column only 1 px above the player's floor blocks, as in
`title-1`. This held at every speed measured: starting from rest,
walking, running, turning, rolling (the roll goes on and is blocked again)
and on the first frame after landing on a face. On a face frame the move
that is tested uses the speed **after** the limits of "Speed on a face"
below. On any other frame it uses the speed from the speed rule. See
"Blocked face frames" below.

### Starting toward a rise (`title-2`)

`title-1`'s rule "Can't start toward a rise" does **not** apply. A player
with `vx` 0 who holds toward a rise starts moving as on flat floor and is
blocked again as soon as a move would cross into the face column. So a
player holding toward a face repeats a short cycle at its foot. After a
block at whole-pixel `x`:

| Held | Frames of the cycle | `x` on those frames | `vx` (1/256) |
|---|---|---|---|
| The direction of the rise | 3 | `x`, `x` + 0.28125, `x` + 0.80859375 | 0, 72, 135 |
| That direction and Action | 2 | `x`, `x` + 0.421875 | 0, 108 |

The frame after the last one in the table is blocked again and starts the
cycle over. Releasing the direction and pressing it again starts the same
cycle from rest. The cycle held for as long as the direction was held,
up to 80 frames in the measurements.

### Face frames (`title-2`)

A **face frame** is a frame that starts with the player grounded (the
frame right after a landing included) and `x` in a face column. The
rules below are written for the measured faces, which fall to the left,
so "downhill" is to the left there.

### Speed on a face (`title-2`)

1. **Pull.** On every frame that **follows** a face frame, the target of
   the speed rule is moved **1 px per frame (256/256) downhill**. This
   applies whatever is held, whether or not the frame is itself a face
   frame, and also if the player has just left the ground. Then the speed
   rule applies as usual. The first face frame on a face therefore uses
   the normal target, and the first frame after the last face frame
   still has the pull.

   | Held (Action not held) | Target with the pull |
   |---|---|
   | Nothing | 1 downhill (256/256) |
   | The downhill direction | 3.25 downhill (832/256) |
   | The uphill direction | 1.25 uphill (320/256) |

2. **Limits.** On a face frame, after the speed rule and before the
   blocking test above, the speed is limited. Write it as `s` in 1/256 px
   per frame, negative downhill as on the measured faces (they fall to the
   left). Let `r` be the remainder of `s` divided by 256, from 0 to 255.
   For example, `r` is 216 for −40, 192 for −64 and 44 for 300.
   - If `s` is from −575 to −192 (at least 0.75 px per frame downhill and
     less than 2.25), it is kept.
   - Otherwise, if `r` is 0 or from 193 to 255, it is kept.
   - Otherwise it becomes **2** downhill (−512) if `s` is −576 or less,
     and **0.75** downhill (−192) if not. Every uphill speed that isn't
     kept becomes 0.75 downhill.

   Then comes the blocking test, with this speed. If the frame isn't
   blocked, `x` changes by the new `vx`.

   | `s` (1/256 px per frame) | After the limits |
   |---|---|
   | −1154 to −1088 | −512 |
   | −1087 to −1024 | kept |
   | −1023 to −832 | −512 |
   | −831 to −768 | kept |
   | −767 to −576 | −512 |
   | −575 to −192 | kept |
   | −191 to −64 | −192 |
   | −63 to 0 | kept |
   | 1 to 192 | −192 |
   | 193 to 256 | kept |
   | 257 to 448 | −192 |
   | 449 to 512 | kept |
   | 513 to 704 | −192 |
   | 769 to 960 | −192 |
   | 1025 to 1090 | −192 |

   These are all the speeds measured. In pixels, a speed is kept when it
   is 0.75 to 2.25 downhill (2.25 itself excluded), or when the part of it
   above the whole number below it is 0 or more than 0.75. So downhill
   speeds of less than 0.25 px per frame are kept, and so are uphill
   speeds such as 0.8 or 1.9. Speeds whose fraction is 0.75 exactly, like
   0.25 downhill (−64) or 0.75 uphill (192), are limited.

So a player can't stand still on a face or walk up it:

- **Holding downhill**, `vx` settles into alternating **2** and
  **2.15625** (512/256 and 552/256): from 2, the pull gives 2.15625; from
  2.15625, the speed rule gives more than 2.25, which the limit turns
  into 2.
- **Holding nothing**, `vx` moves toward 1 downhill by the speed rule and
  reaches it exactly, from either side.
- **Holding uphill**, `vx` drops by the speed rule toward 1.25 uphill and
  is held at 0.75 downhill by the limit.

None of these settled speeds falls in a kept band. A slow or uphill
speed that is kept comes from what happened before the face frame, such
as a landing.

On a face built for the step measurements that falls to the right,
downhill speeds of 19/256 to 448/256 behaved in the same way with the
directions swapped: kept up to 62/256, 192/256 from 68 to 190, kept from
196 on.

### Blocked face frames (`title-2`)

A face frame is blocked only when the speed left by the limits still
takes `x` into a higher face column. Every uphill speed that the limits
don't keep becomes 0.75 downhill, so a blocked face frame needs an
uphill speed that is kept: one whose fraction of a pixel is more than
0.75, or a whole number of pixels. On the measured faces this happened
only on the first frame after landing on the face while moving uphill.
The speed on that frame comes from the speed rule applied to the
speed at landing, without the pull (the landing frame started in the
air).

A blocked face frame ends like any other blocked frame. `x` is set to the
left edge of the column the player was in before the frame and `vx`
becomes 0. The limits are not applied again and `x` doesn't move further,
so the player is still on that frame. The next frame is a face frame
from `vx` 0, with the pull. Holding the uphill direction, that gives a
target of 1.25 uphill and a speed of 40/256, which the limits turn into
0.75 downhill.

When the limits turn the speed downhill on the first frame after a
landing, the frame isn't blocked. The player moves 0.75 px downhill on
it, and follows the face down if the new column is lower.

### Following a face down, or leaving it (`title-2`)

On a face frame, after the horizontal move, compare the floor of the new
column with the floor of the column before the frame:

- **8 px lower or less** (at most two face columns crossed, or onto the
  floor at the foot, 1 or 5 px lower): the player stays grounded and `y`
  becomes the new column's floor.
- **More than 8 px lower** (9 and 12 px were measured): the player leaves
  the ground. On this frame (L) `y` moves down by **exactly 8 px**, not
  by 3.4375 as in `walk-off.md`, and `vy` ends at **0.00390625** (1/256).
  From L + 1 `vy` increases by **0.4375** (112/256) each frame, so it is
  113/256 after L + 1 (not 368/256 as after a walk-off in `title-2`). The
  player moves down by the new `vy`. Horizontal speed follows
  `air-control.md` ("In `title-2`"), with the pull on L + 1 because L was
  a face frame. The player lands by the `title-2` landing rule of
  `walk-off.md`.

Because the alternation of 2 and 2.15625 lowers the fraction of `x` by
0.15625 every two frames, a player holding downhill sooner or later
crosses three columns in one frame on a long enough face, and leaves the
ground there. All four departures from a face that were measured
happened this way. Three crossed three face columns (12 px). One went
from the third face column from the bottom onto the floor at the foot
(9 px).

For staying grounded, this is the rule of "Following the floor down"
below with the reach of a steep face (8 px). Onto other face columns
that rule would allow 11 px, but the floors of face columns differ by
multiples of 4 px, so 8 is the most that is followed. On leaving, `y`
moves down by exactly 8 px, as above, not 8.4375.

### Following the floor down (`title-2`)

On a grounded frame that isn't a face frame, the floor of the column
after the horizontal move is compared with the floor the player stood on
before the frame. The **drop** is how much lower the new floor is. A
higher floor ahead is followed or blocked as above. For a drop, each
piece of floor has a **reach** that goes with its steepness:

| Piece | Its floor changes by | Reach |
|---|---|---|
| Flat | 0 | 3 px |
| 1-in-4 slope | 1 px every 4 columns | 3 px |
| 1-in-2 slope | 1 px every 2 columns | 3 px |
| 2-in-3 slope | 2 px every 3 columns | 4 px |
| 1-in-1 slope | 1 px every column | 5 px |
| 2-in-1 slope | 2 px every column | 6 px |
| Steep face | 4 px every column | 8 px |

The player stays grounded, with `y` the new column's floor, if the drop
is at most:

- the reach of the piece they stood on, when the new column is on a
  **flat** piece;
- that reach plus **3 px**, when the new column is on a **sloped** piece
  of any steepness, a steep face included.

Otherwise they leave the ground. On this frame (L) `y` moves down by the
reach of the piece they stood on, plus one frame of gravity: reach +
0.4375 px. That is 3.4375 from flat floor and from 1-in-4 and 1-in-2
slopes, as in `walk-off.md`. It is 4.4375 from 2-in-3 slopes, 5.4375
from 1-in-1 slopes and 6.4375 from 2-in-1 slopes. `vy` ends at 1/256, and
the fall goes on as in `walk-off.md` ("In `title-2`"). From flat floor
and gentle slopes, a 4 px step down onto a flat piece is therefore fallen
off, while a step of up to 6 px onto a slope or a face is walked down.

Only the floor the player stood on and the floor of the new column
count. The columns crossed in between, the speed, the direction and the
fraction of `x` don't. From every steepness in the table, steps onto
flat floor and onto slopes were measured. For each, a drop of the limit
was followed and a drop 1 px larger left the ground, so every reach is
exact. For the steep face that is "Following a face down" above.

Getting onto a face from above follows the same rule. The frame that
moves the player from the floor at the top onto the face isn't a face
frame. The top floor is flat, and the highest face column is 3 px below
it and on a sloped piece. So a move that ends there keeps the player
grounded, and the next frame is a face frame. A move that ends in a
lower face column (7 or 11 px below) leaves the ground, with frame L
moving down 3.4375 px. Walking at the top speed of 2.25 px per frame,
whether the move ends in the highest face column depends on the
fraction of `x`. Running (Action held), 20 of 21 measured moves skipped
it and left the ground.

### Facing (`title-2`)

On face frames the player faces downhill. A player facing uphill turns
round on that frame, without the turn of `ground-speed.md` ("In
`title-2`"). The one exception measured is the first frame
after landing on a face: facing doesn't change on it, and the player turns
on the next frame (seen in 3 landings). Holding the uphill direction on a
face never turned the player uphill.

### Examples (`title-2`)

Synthetic floor, `y` down-positive: `y` = 0 for columns up to 199. A face
rises to the right over columns 200 to 215 (`y` = −1, −5, −9, …, −61,
that is −1 − 4k for column 200 + k). Flat floor at `y` = −64 from column
216.

**Walking into the face.** Right held, `x` = 193.5 and `vx` = 576/256
before frame 1:

| Frame | `x` | `vx` (1/256) | Note |
|---|---|---|---|
| 1 | 195.75 | 576 | |
| 2 | 198 | 576 | |
| 3 | 198 | 0 | Blocked: 200.25 is in face column 200 |
| 4 | 198.28125 | 72 | Starts again from rest |
| 5 | 198.80859375 | 135 | |
| 6 | 199.55078125 | 190 | Still column 199 |
| 7 | 199 | 0 | Blocked |
| 8 | 199.28125 | 72 | |
| 9 | 199.80859375 | 135 | |
| 10 | 199 | 0 | Blocked, and so on every 3 frames |

**Running into the face.** Right and Action held, `x` = 192.25 and
`vx` = 864/256 before frame 1: `x` 195.625 and 199 on frames 1 and 2, then
blocked at 199 on frame 3, 199.421875 (`vx` 108) on frame 4, blocked on
frame 5, and so on every 2 frames.

**Coasting down.** Nothing held, `x` = 217.5 (on the top floor), `vx` =
−320/256 before frame 1:

| Frame | `x` | `y` | `vx` (1/256) | Note |
|---|---|---|---|---|
| 1 | 216.40625 | −64 | −280 | |
| 2 | 215.44921875 | −61 | −245 | Onto the highest face column (3 px) |
| 3 | 214.609375 | −57 | −215 | Face frame, normal target 0 |
| 4 | 213.75 | −53 | −220 | Pull: target −256 |
| 5 | 212.875 | −49 | −224 | |
| 6 | 211.984375 | −45 | −228 | |
| 7 | 211.08203125 | −45 | −231 | Same column |
| 18 | 200.6796875 | −1 | −248 | |
| 19 | 199.70703125 | 0 | −249 | Onto the floor at the foot |
| 20 | 198.70703125 | 0 | −256 | Pull once more: gap 7 ÷ 8 is 0, so the target |
| 21 | 197.83203125 | 0 | −224 | Normal target 0 |

**Walking down, then holding uphill.** Left held for frames 1 to 4, then
Right; `x` = 219.75 (top floor) and `vx` = −576/256 before frame 1:

| Frame | `x` | `y` | `vx` (1/256) | Note |
|---|---|---|---|---|
| 1 | 217.5 | −64 | −576 | |
| 2 | 215.25 | −61 | −576 | Onto the highest face column |
| 3 | 213.25 | −53 | −512 | Face frame: normal target, −576 limited to −512; 8 px down, followed |
| 4 | 211.09375 | −45 | −552 | Pull: target −832, −552 kept |
| 5 | 209.36328125 | −37 | −443 | Right held: target +320 |
| 6 | 208.00390625 | −33 | −348 | |
| 7 | 206.96875 | −25 | −265 | |
| 8 | 206.21875 | −25 | −192 | Same column |
| 9 to 17 | … | | −192 | Down the face at 0.75 px per frame |
| 17 | 199.46875 | 0 | −192 | Onto the floor at the foot |
| 18 | 198.96875 | 0 | −128 | Pull once more: target +320 |
| 19 | 198.8125 | 0 | −40 | |
| 20 | 198.95703125 | 0 | 37 | |
| 21 | 199.36328125 | 0 | 104 | |
| 22 | 199 | 0 | 0 | Blocked: back at the foot |

The first face frame after walking onto the face from the top floor is
limited like any other face frame. If Left had stayed held, frame 5's
speed would have been −587, limited to −512, moving 2 px to
`x` = 209.09375 (−37, 8 px down, followed); frame 6 (−552, kept) would
have moved 2.15625 px to 206.9375, three columns and 12 px down, and the
player would have left the ground there, 8 px lower (−29).

**Landing on the face moving uphill.** Same floor. Two landings, each
followed by a frame with Right held (target 2.25, no pull on the first
frame after a landing):

| End of the landing frame | Next frame: speed rule | After the limits | Result |
|---|---|---|---|
| `x` = 206.21875, `y` = −25, `vx` 184/256 | 233/256 (`r` 233) | kept | 206.21875 + 0.91015625 is in column 207 (−29, higher): blocked. `x` = 206, `vx` 0 |
| `x` = 206.65625, `y` = −25, `vx` 296/256 | 331/256 (`r` 75) | −192 | `x` = 205.90625, column 205 (`y` −21, 4 px lower, followed) |

After the blocked frame, Right still held: the pull makes the target
320/256, the speed rule gives 40/256, the limits make it −192, and `x`
is 205.25 with `y` −21.

**Steps down from flat floor** (synthetic, `y` down-positive). Flat
floor at `y` = 0 up to column 111. From column 112 there is a flat piece
4 px lower (`y` = 4). Walking right (Right held) with `x` = 110.37109375
and `vx` = 457/256 before frame 1:

| Frame | `x` | `y` | `vy` (1/256) | `ground` | Note |
|---|---|---|---|---|---|
| 1 | 112.2109375 | 3.4375 | 1 | 0 | Drop 4 onto a flat piece: more than the reach 3. Leaves (L) |
| 2 | 114.1015625 | 3.87890625 | 113 | 0 | Floor 0.5625 px below: no 368 step (`walk-off.md`) |
| 3 | 116.03515625 | 4 | 0 | 1 | Lands |

With a piece 8 px lower instead (`y` = 8 from column 112): L as above,
then `y` 4.875 (`vy` 368/256), 6.75 (480/256), and a landing on 8 on
frame 4. With a 1-in-4 slope from column 112 whose first columns are
4 px lower, or with a 2-in-3 slope whose first column is 6 px lower,
frame 1 keeps the player grounded on the new column's floor instead
(drop at most 3 + 3).

### Evidence (`title-2`)

527 runs (70 312 frames) at the two faces. 30 813 frames that started
grounded inside the mapped floor were checked frame by frame against the
rules above (`x`, `vx`, `y` and whether grounded), with 0 differences.
That is 3 250 blocked frames, 1 650 face frames, 161 frames after a face
frame that weren't face frames, 34 departures from the ground and 25 718
frames on ordinary floor near the faces. Among the face frames, 364 met
the upper limit and 339 the lower one. 120 of the runs were random input
recorded after the rules were first written. On first check they showed
2 differences. Both were corrected at the time: the blocking test was
put before the limits (revised below), and it applies on the first frame
after a landing.
Frames in a turn weren't checked frame by frame. More than 300 blocked
frames were seen during turns, with the result the blocking rule gives.

The limits and the order of limits and blocking were then measured
directly, and the rules above were corrected. The earlier version said
the block test came before the limits and that every uphill speed became
0.75 downhill. Frames set one at a time:

- 43,218 face frames following a face frame, holding nothing, Right or
  Left, at every speed from −1154/256 to +1090/256: 0 differences.
- 77,660 first frames after a landing on the face: 5,532 landings from
  0.25 to 4.5 px above the floor, and two sweeps of 36,064 over the
  speed at landing, holding Right or nothing: 0 differences. 1,390 of
  them were blocked, 46,426 limited and 29,844 kept.

Checked frame by frame from inputs alone, with the corrected rules:

- the 527 runs above (30,813 frames): 0 differences;
- 624 runs of jumps and falls onto both faces, recorded for
  `air-walls.md` (34,187 grounded frames, among them the frames after
  the landings): 2 differences, both rolls (not covered). The earlier
  rules gave 95 differences on these frames.

For "Following the floor down", 44 test floors were built next to the
64 px face, with steps of 3 to 10 px between pieces of every steepness
in the table. Each was scanned for its floor heights. From a real stand
on the upper column, the player was given speeds of 16/256 to 1040/256
at 16 fractions of `x` (90,816 frames, crossing 1 to 4 columns): 0
differences in `y`, `ground` and `vy`. Fifteen walking runs over 5 of
the steps, and 720 frames at the real top of the 64 px face (3, 7 and
11 px), agreed.

### Not covered in `title-2`

- Faces that rise to the left, faces steeper or less steep than 4 px per
  column, and vertical steps. On a face that falls to the right, only
  downhill speeds from 19/256 to 448/256 were seen (above), and only on
  test floors.
- Steps of 6 px onto a steep face (no test floor had one), and steps
  onto or from columns of flat floor that share a piece with sloped
  columns, like the 8 flat columns at the top of the 96 px face
  (`slopes.md`, "In `title-2`").
- Horizontal motion on slopes steeper than 1-in-2 (`slopes.md`).
- Facing on the frames after a landing on a face (see "Facing" above:
  those landings were blocked; other landings were seen to turn the
  player on their first frame or later).
- Action held or rolling on a face. Rolls and runs that reach the face
  from the top floor were seen to leave the ground there, as above.
- Jumps from a face (the face's pull and limits were seen to carry into
  the first two airborne frames). Jumps and falls that meet a face are in
  `air-walls.md` ("In `title-2`").
- Longer falls after leaving a face than the four measured, which landed
  on the floor at the foot within 11 frames.
- The second character, enemies and moving platforms.
- How a trace's `state` names face frames.
