# The metal barrel

Titles: `title-1`

The metal barrel is the object a `hidden-metal-barrel` leaves behind
(`hidden-objects.md`). It is picked up and carried as in `carrying.md`.
This file says what it does once it is thrown: how it flies, lands and
rolls, what it does at a wall, that it defeats walkers, and what it does
with the two characters (passing through, catching and carrying them)
and to the thrower and its balls. The other things a thrown object can
meet (other enemies) are not covered here; see "Not covered".

Frames are logic frames at 60 Hz, numbered as in a trace. `x`, `y`, `vx`
and `vy` are as in a trace: right and down are positive, speeds in px
per frame and written both ways, as in `thrown-objects.md` (896/256 =
3.5 px per frame). Q is the frame Action is let go, as in `carrying.md`.

## Like a barrel

From Q + 21, a thrown metal barrel moves exactly as `thrown-objects.md`
and `carrying.md` say for a barrel that holds nothing:

- the throw, its speed and how the speed settles (the speed rule), in the
  air and on the floor;
- falling, bouncing and rolling ("In the air", "The floor of a column",
  "Rolling"): it rolls at 3.5 px per frame and never stops on its own;
- slopes, ground passable from below, and the 8 px limits;
- being removed once it is far enough outside the picture ("Leaving the
  picture"), with the same limits and the same frame.

Where the two differ is a wall.

## Turning back at a wall

A **wall frame** is a frame on which a barrel would break: the move would
take the object into a face column, or into a column whose floor is more
than 8 px higher, as `thrown-objects.md` describes it under "Breaking
against a wall", "A floor more than 8 px higher" and "Breaking at a
vertical step". The conditions are the same for the metal barrel, in the
air and on the floor, going left or right, and an object below the
height limits there is removed as a barrel is.

On a wall frame the metal barrel **does not break**. It

- does not move horizontally: its `x` is its `x` at the start of the
  frame rounded down to a whole pixel, so a fraction of a pixel is lost;
- gets `vx` = 3.5 px per frame (896/256) **away from the wall**,
  whatever its speed was before (11.7, 8 and 1.6 px per frame were all
  measured, and all left at 3.5);
- goes on in `y` as it would have: the same fall, bounce or roll on
  that frame and the frames after it as for the barrel when it isn't at
  a wall.

From the next frame it moves away from the wall at 3.5 px per frame and
is an ordinary thrown metal barrel again: it falls if the floor is lower,
rolls on, and turns back again at the next wall it meets. It was seen
coming back from a wall at the left end of the first level, and rolling
on through slopes and past where it was thrown.

### Example

Synthetic. A step: the floor is at `y` = 300 for columns 112 and to the
right, and 64 px higher (`y` = 236) for columns 111 and to the left. A
metal barrel rolls left on the floor at `y` = 300 with `vx` = −3.5 and
`x` = 115.75 at the start of frame 1.

| Frame | `x` at end | `vx` at end | Notes |
|---|---|---|---|
| 1 | 112.25 | −3.5 | moves 3.5; stays in column 112 |
| 2 | 112 | +3.5 | the move would end in column 108, whose floor is more than 8 px higher: wall frame; no move, fraction dropped, turns |
| 3 | 115.5 | +3.5 | moves away |
| 4 | 119 | +3.5 | |

A barrel in the same place breaks on frame 2.

## Evidence

Measured on the first level with the first character alone. With a
setup method, a metal barrel stood at three places on the first level:
on flat floor near the middle of the level, at the foot of the 64 px step
at the level's left end, and on a floor with faces rising to the right
and to the left further on. No metal barrel is there in ordinary play,
so **none of the rules above has been seen in ordinary play**; every
situation below was arranged, with the barrel on or above the floor
where a thrown object can be. Every throw was repeated with a plain
barrel in the same place and the same inputs as a control.

- 650 throws by inputs alone (Action let go on frames 25 to 70 of the
  pick-up, walking, standing, turning and jumping either way): 518
  matched the control on every frame (`x`, `y`, `vx`, `vy` and the frame
  it was removed) to the end of the run. In 129 the control broke on
  one frame and the metal barrel turned on that same frame, as above:
  `x` unchanged but for its fraction, `vx` = ±3.5 px per frame away
  from the wall; 38 of them moving right and 91 moving left, at five
  different faces (the 64 px step at the first level's left end, and
  four faces of 4 px per column). No other difference was seen except
  two throws released inside a wall (see "Not covered") and one run
  that ended early.
- 2 275 starts at the 64 px step at the first level's left end: the
  metal barrel's `x` from 111 to 117 in steps of 0.25 (25 values) and
  its height from 0 to 90 px above the floor's top in steps of 1 px
  (91 values), going left at 3.5 px per frame. On 1 611 the control
  broke, and on every one the metal barrel turned on the same frame; on
  the other 664 both did the same.
- 576 starts near the same step: `x` 112, 113 and 114, 16 heights from 0
  to 60 px above the floor's top in steps of 4 px, `vy` −3, 0 and +3 px
  per frame, and `vx` −11.7, −8, −3.5 and −1.6 px per frame. In 462 the
  wall was met on the first frame, and `vx` was +3.5 on that frame
  whatever the starting speed. In 60 the object slowed and met the wall
  on a later frame, and in 54 it was removed first (above the height
  limits); these were not compared further.

## Walkers

A thrown metal barrel defeats the walkers it touches (`walker.md`) in
the same way as a thrown barrel does (`thrown-objects.md`, "Hitting
walkers"), for small, medium and large walkers, with the walker heading
either way and the object thrown either way, when it is thrown from the
flat floor at a walker standing on that floor ahead of the thrower. On
the frame of a hit the walker's knock-off (its speed, rise and fall) and
its removal are the same as for the barrel, and the metal barrel goes on
as if the walker weren't there, as the barrel does. Nothing in the
object's own motion differs from the barrel's when a walker is hit. Other
heights and throws made in the air, other floors, and hits while the
object is rolling are not covered (see "Not covered").

- Walkers, by a setup method: 204 throws left or right from the flat
  floor in the first level (about 230 to 545 px along the level), each
  once with the metal barrel and once with a plain barrel, same inputs.
  A small, a medium or a large walker, heading left or right, stood
  ahead of the thrower on the same floor at distances of 46 to 238 px
  in steps of 12 (17 distances, 3 kinds, 2 headings, 2 throw
  directions). In 177 of the
  204 pairs every row (the walker's `x`, `y`, speeds and removal, the
  object's `x`, `y`, speeds and removal frame) was the same; the walker
  was knocked off in 142 of those 177, in every kind and both throw
  directions, and the other 35 had it out of reach. The other 27 pairs
  differ only in the object, which was overlapping a wall at the
  release (see "Not covered"); they were not compared further.

## The second character throwing it

Thrown by the second character (`second-character.md`, `carrying.md`,
"The object, second character"), a metal barrel behaves as follows,
from the frame it leaves the second character's hands (it leaves the
hands on Q + 18 and first moves on Q + 19, as in `thrown-objects.md`):

- for the throws measured (below; slopes were not singled out), until the frame it meets a wall it
  moves row for row as a plain barrel thrown by the second character
  does, in `x`, `y`, `vx`, `vy` and the frame it is removed;
- on a wall frame it turns back as in "Turning back at a wall": `x` is
  the whole part of its `x` before the frame and `vx` = 3.5 px per frame
  (896/256) away from the wall. Its `y` after that frame was not
  compared with a plain barrel (the plain barrel is broken there);
- the second character's own motion and timing during the pick-up, the
  carry and the throw are the same as with a plain barrel (pictures were
  not compared).

A walker reached by the metal barrel thrown by the second character,
after a carry on flat floor, with the walker on the same flat floor
ahead of the object, is defeated exactly
as when a plain barrel hits it (`thrown-objects.md`, "Hitting walkers";
the second character's frames are in `carrying.md`): the walker's rows
and the second character's rows are the same as for the plain barrel,
for small, medium and large walkers heading either way, the object
thrown either way. When the walker reaches the second character while
the object is still in the hands, the object is not broken: it stays,
with `vx` 0 and `vy` −5 px per frame (1280/256, up) on the first frame
that differs from a plain barrel (which is removed), as for a metal
barrel left in the hands of a hit thrower ("The thrower and its
balls"). It was followed for 37 frames, until it was gone; what it does
between is not described (see "Not covered").

### Evidence for the second character's throw

Measured on the first level with the second character leading, in
arranged situations only: a setup method started the second character at
the start and made a metal barrel available ahead on the flat floor (no
metal barrel is there in ordinary play). Every run was played twice with
the same inputs, once with the metal barrel and once with a plain object
in the same place as a control.

- 600 throws by inputs (walking, standing and jumping either way while
  carrying, Action let go with Left, Right, Up or nothing held, then
  random inputs in flight, 200 frames or fewer): 285 matched the control
  on every frame of the object and the second character to the end; in
  all 285 the control was removed outside the picture before the end
  (whether the flat floor only was met was not recorded). In the other
  315 the control broke at a wall (the 64 px step at the left end of the
  level) on one frame, with the object moving left;
  on the frame before, its `y` speed was upward in 46 and zero or
  downward in 269, and whether it was rolling or in the air was not
  recorded. On that frame the metal barrel kept an `x` equal to the whole
  part of its `x` before, with `vx` +3.5 px per frame (896/256) in all
  315, and was still there 10 frames later in all 315; the runs were not
  followed further. Before that frame the object's rows matched the
  control in every run, and the second character's rows matched on every
  frame.
- 576 throws at a walker, by inputs, on flat floor, for small, medium and
  large walkers heading left or right, thrown left or right, Action let
  go after a carry of 12, 17 or 22 frames, the walker standing 40 to 160
  px (steps of 8) ahead of the thrown object on the frame before the
  release; whether the object met the walker in the air or rolling was
  not recorded, and no throw was made in the air. In all 576 the
  walker's and the second character's rows were equal to the control's
  on every frame. The object's rows were equal in 510.
  In the other 66 (walker 40 to 64 px ahead) the walker reached the
  second character with the object in its hands: the plain object was
  removed and the metal barrel was kept as above, and was gone 37 frames
  later in all 66.

## The characters

Measured with the first character alone, on flat floor (and, for the
end of a ride, at one face), with a metal barrel **rolling right** at
3.5 px per frame on the floor; the barrel's `y` is the floor's. Rolling
left was not measured. Here `y` is the character's feet and down is
positive, as in a trace; "above" means a smaller `y`. The situations
were arranged (see "Evidence for the characters" at the end of this
section).

**On the floor, and rising.** A character on the floor, standing,
walking either way or crouching, is not touched by the barrel: it rolls
through the character, the character's pose does not change because of
it, and no hurt was seen. For a standing or crouching character `x` is
unchanged too; for walking, `x` was not compared with a run without the
barrel. A character who is rising in a jump is not caught either.

**Coming down onto it.** A character falling in the air is caught by the
barrel on a frame on which the fall would end with the character's `y`
17.5 px above the barrel's `y` or lower, while the horizontal distance
**D** is in range. D is the character's `x` minus the barrel's `x`, the
barrel's `x` being as it is at the end of that frame (the barrel moves
first). The character's `x` is its value on that frame; it does not
change during a fall that starts from standing still. On the frame
before the catch the fall would have ended 24.8 px above the barrel's
`y`, and the character was not caught then; where between 17.5 and 24.8
the limit lies was not measured. D is a fractional distance. The ranges
(px; not caught outside them):

| Character faces | Caught from D = | to D = | Not caught at D = | and at D = |
|---|---|---|---|---|
| left | −26.152 | 23.926 | −26.496 or less | 24.426 or more |
| right | −22.848 | 27.227 | −23.32 or less | 27.727 or more |

Every tried jump with D inside a range was caught (212 distinct
left-facing and 295 right-facing values of D were tried inside the
ranges, ends included; the gaps between neighbouring values were up
to 0.97 px on the left and 0.50 px on the right). So each end lies
between the last caught value and the first not-caught one in the
table, no closer.

A character who comes down outside the range lands on the floor as usual
and the barrel rolls through.

**Riding.** On the catch frame C the character's `y` becomes the
barrel's `y` − 16.5 (between 16.0 and 17.0 above in the 85 catches
checked for this, see below) and its `x` the barrel's `x` (at the end of
C) minus a distance between 7.3 and 8.2 px that differs from catch to
catch. The character is then riding. On C + 1 the character's `x` does
not change, so the barrel is 10.8 to 11.7 ahead of it; from C + 2 on, the
character moves with the barrel (3.5 px per frame, and the trace shows a
speed of 3.5 px per frame) at the same distance. The height wobbles by
less than 1 px from frame to frame; riding over slopes was not checked.
Left, Right, Down and Action do nothing while riding (Action does not
pick the barrel up); each was held for 10 frames, 10 frames after the
catch, and the ride was frame for frame the same as without the input.

**Jumping off.** A Jump press on frame C + 2 (tried up to C + 6) starts
an ordinary jump (`jump-arc.md`) from the riding height. On the press
frame `vx` is the barrel's 3.5 px per frame, and then it falls by one
eighth each frame (3.5, 3.06, 2.68, 2.35, …); the character comes down
onto the floor behind the barrel. A press that
begins on C + 1 or earlier does nothing and is not remembered (tried down
to C − 4), except that a press that began on C, C − 1 or C − 2 and is
still held on C + 2 starts the jump on C + 2, as if pressed then (held
presses that began earlier were not tried). One that begins on C + 1 and
is held does not jump (1 to 6 frames tried).

**The ride ends** when the character would walk into a wall or a face
that the barrel itself meets (the barrel turns on its own rules above):
the character is put on the floor at its own `x` in the same frame and
walks on with the ordinary rules. This was seen once, with the barrel
rolling right at a face that rises to the right. One ride was followed
for 913 frames, from the catch to that face; the barrel was not removed
while the character was on it, and no other end was seen in that ride.

**Picking it up.** A rolling metal barrel can be picked up with Action
from the floor, by a character who is not riding it; the pick-up
starts as for a barrel (`carrying.md`). Tried twice, with the barrel
37 and 60 px away when Action was pressed.

### Evidence for the characters

By a setup method a metal barrel was made available to pick up at a
chosen place on flat floor in the first level. From then on the character
stood at its own place and received inputs only: it picked the barrel up,
carried it left and threw it left (Action let go with Left held), so
that the barrel turned back at the wall and rolled right toward the
character. That arrangement is not seen in ordinary play.

- Catch ranges: 2 112 jumps, each a standing wait of 3 to 24 frames
  followed by a jump straight up (Jump held for 20 frames, nothing
  else), after pressing Left or Right for 1 to 12 frames to set the
  facing and the place: 12 press lengths × 2 directions × 4 barrel
  start places (2 px apart) × 22 waits, 1 056 per direction. Facing was
  taken at the jump, not from the direction pressed: a Right press of 1
  frame left the character facing left in all 88 such jumps, and of 2
  frames in 4 of 88. So 1 148 jumps were left-facing (481 caught) and
  964 right-facing (613 caught).
- Riding values: 85 further catches (left and right facing, 5 walk
  lengths, 3 barrel start places, 4 waits), each with the distance and
  the height on the catch frame, the next two frames and the ride after.
- A repeat of 100 of the 2 112 jumps (50 caught, 50 not) gave the same
  result on every one, every catch on a frame on which the character was
  falling, and no pose other than standing, walking, jumping, crouching
  or riding.
- Passing: 3 barrel start places for each of standing, crouching,
  walking right, walking left and jumping straight up (15 runs of
  about 120 frames). In the 11 standing, crouching and walking runs in
  which the barrel reached the character, the barrel's `x` came within
  0.7 px of the character's `x` at the closest; the character's `x` (when
  standing or crouching) and pose were those of the same inputs without
  a barrel, and no hurt pose appeared. The jumps in this list are not
  counted as rising evidence; the rising claim rests on the 2 112 jumps
  above, in none of which a catch came on a rising frame.
- Jump-off: presses from C − 4 to C + 1 (6 runs) did nothing and were not
  remembered; presses at C + 2 to C + 6 (5 runs) each started a jump.
  Jump held for 1 to 6 frames starting on C − 2 to C + 2 (30 runs): a
  start on C + 2 jumped on its first frame; a start on C + 1 never
  jumped; a start on C, C − 1 or C − 2 that was still held on C + 2
  (3, 4 or 5 frames or more) jumped on C + 2.
- Ride: one ride of 913 frames to the face; 4 runs holding Left, Right,
  Down or Action for 10 frames (and 2 with Jump).
- Pick-up: two runs (see above).

## The thrower and its balls

A ball (`thrower.md`) is removed by a metal barrel, and the thrower
(`thrower.md`) is knocked out by it, in exactly the places and on exactly
the frames where they are for a plain barrel (`thrown-objects.md`,
"Hitting a ball" and "Hitting the thrower", with the same ranges). For
the balls this was measured for a thrown object, one still in the
player's hands (Q + 1 to Q + 20) and one that is carried. For the
thrower it was measured for a thrown object (thrown right) and one in
the hands (Q + 1 to Q + 20, thrown right or left); see "Not covered" for
the rest. The thrower's knock-out, its removal and the player's frames
are the same as with a plain barrel.

What differs is what happens to the metal barrel when it is in the
hands or carried. A barrel that is hit there breaks; the metal barrel
does not:

- Its pieces do not appear; the flash of the hit does.
- It stays in the level with `vx` = 0 and `vy` = −5 px per frame (moving
  up) on the frame of the hit, and from then on falls as an object in the
  air falls (`vy` gaining 0.4375 px per frame, to 8 px per frame down). It
  keeps its `x`, apart from the move the throw gave it on that frame.
- It does not stop on the floor, even on the floor of the ledge it was
  held over, and falls straight down. It was removed 39 to 45 frames
  after the hit, once it was about 250 px below the top of the picture
  (250 to 254 on its last frame).
- What it was held by is let go of as when a barrel breaks: a throw in
  progress goes on and a carried object is no longer carried (`x`, `y`
  and action of the player were the same as with a plain barrel for 70
  frames in four runs).

A metal barrel that was thrown (from Q + 22 on) is not touched by the
hit and goes on as a barrel does, as in `thrown-objects.md`.

### Evidence

Measured on the first level with the first character alone. With a
setup method, a metal barrel stood where the plain barrel stood in the
same situation (beside the ledge the thrower stands on, in the player's
hands). Every run was repeated with a plain barrel in the same place and
with the same inputs.

- The thrower: 102 pairs of runs of 70 frames. In each the player stood
  on the thrower's ledge (a setup method), from 110 px left to 40 px
  right of the thrower in steps of 3 px (51 positions), and then threw
  right or left by inputs (2 directions). The thrower was hit in 40
  pairs, on the same frame in each run of a pair: 20 thrown right, and
  20 in the hands (7 when throwing right, 13 when throwing left). In all
  102 pairs the thrower's `x`, `y` and speeds were the same on every
  frame, and so were the player's. The 20 thrown objects were the same
  as the plain barrel on every frame; the 20 in the hands were as above.
  No run had the metal barrel carried, or thrown left.
- Balls, arranged one frame at a time with a setup method (these
  situations are not reachable in play): 14 grids of 6 561 positions
  of a ball beside the object, offsets from −80 to 80 px in `x` and
  −75 to 85 px in `y` in steps of 2. The grids were: 7 throws to the
  right, at frames Q + 1, Q + 10, Q + 20, Q + 22, Q + 37, Q + 38 and
  Q + 51 (the one at Q + 21 was not run); 4 throws to the left, at four
  other frames from the start of the throw's hold to its flight (not
  counted from Q, so not listed); and 3 carried states: standing still
  right after the barrel was picked up, standing still 20 frames later,
  and 8 frames into a jump with the barrel carried. The ball was removed
  in 22 498 positions, the same ones as with the plain barrel in every
  grid. A thrown metal barrel was the same as the plain barrel after the
  frame in 11 138 of them (all of its speeds, `x` and `y`); the other
  11 360 were in the hands or carried, with the metal barrel left as
  above and no pieces.
- After a carried barrel was hit by a ball at its position (a setup
  method), the player was followed for 70 frames in 4 runs: standing
  still, walking right, walking left, and jumping. In each the player
  was the same as with the plain barrel on every frame.

## The second character

Measured with the second character under control (the first following
close behind), in the same arrangement and under the same conditions as
"The characters": flat floor, a metal barrel rolling right at 3.5 px per
frame, the barrel's `y` the floor's. The second character carried the
barrel left and threw it left (Action let go with Left held); it turned
back at the wall and rolled right toward the character, as in "Turning
back at a wall" (compared with a plain barrel for the second
character's throw in "The second character throwing it"). `y` and
"above" are as in "The characters". Each paragraph below says what was measured for the second
character; what it does not mention was not measured.

**On the floor, and rising.** As for the first character: a second
character standing, crouching or walking either way is not touched; the
barrel rolls through and no hurt pose was seen (standing and crouching:
`x` unchanged).
A character who is rising in a jump is not caught.

**Coming down onto it.** A second character falling in the air is caught
on a frame if D is in range (D as for the first character: the
character's `x` minus the barrel's `x` at the end of that frame) and the
character's `y` at the start of that frame, **H**, is between two
limits above the barrel's `y`. Caught frames had H from 14.11 up to
45.89 px above; not caught at 47.14 px or more above, nor at 11.48 or
less; between 11.48 and 14.11 and between 45.89 and 47.14 was not
measured. H is the character's `y` at the start of the frame, whereas
the first character's limit above is given for where the fall would end,
so the two are not directly comparable. A fall covers 6 to 8 px per frame
there, so a character who comes down with D out of range and has D in
range on a later frame of the band is caught on that later frame. The
ranges (px; not caught outside them):

| Character faces | Caught from D = | to D = | Not caught at D = | and at D = |
|---|---|---|---|---|
| left | −20.152 | 23.168 | −20.914 or less | 24.16 or more |
| right | −25.984 | 17.789 | −26.238 or less | 18.062 or more |

These are not the first character's ranges: 43.3 px wide facing left
and 43.8 facing right, against 50.1 for both. Every tried jump with D inside a range on a frame with H inside the
band was caught (98 distinct left-facing and 138 distinct right-facing
values of D, ends included; gaps between neighbouring values up to 1.18
px on the left and 0.63 px on the right), so each end lies between the
last caught value and the first not-caught one in the table, no closer.

**Riding.** On the catch frame C the character's `y` becomes the
barrel's `y` − 16.6 to 16.8 (in the 50 catches checked for this) and its `x` the barrel's `x` (at the end of C) minus a
distance between 7.8 and 8.5 px that differs from catch to catch. On
C + 1 the character's `x` does not change; from then on the barrel is
11.3 to 12.0 px ahead of it (the same distance for the whole ride in each
catch) and the character moves with the barrel, 3.5 px per frame. The
height stays between 16.0 and 17.0 px above the barrel's `y`, wobbling
by less than 1 px from frame to frame on flat floor (17.9 once the
floor began to rise, in the one arrangement that reached it in the 60
frames followed). Left, Right, Down, Up and Action held for 10 frames,
10 frames after the catch, change nothing: the ride is frame for frame
the same as without the input.

**Jumping off.** As for the first character: a Jump press that begins
on C + 2 or later starts an ordinary jump from the riding height on that
frame, with `vx` the barrel's 3.5 px per frame falling by one eighth
each frame (3.06, 2.68, 2.35 on the next frames). A press that began on
C, C − 1 or C − 2 and is still held on C + 2 jumps on C + 2 (earlier
starts were not tried); a press that began on C + 1 does nothing, however
long held (1 to 6 frames tried, and once 40).

**The ride ends** as for the first character: one ride was followed for
933 frames, from the catch to a face rising to the right; on the last of
them the barrel was 11.3 px ahead, and on the next frame the character
stood on the floor at its `x` of the last frame, no longer riding, and
walked on at its ordinary speed. The barrel was
not removed during the ride.

**Picking it up.** Action pressed with the rolling barrel 57, 53, 46, 39,
36, 29 or 18 px from the character, standing and facing it, picked it up
(walking to it first when the barrel was out of reach), as in
`carrying.md`.

### Evidence for the second character

By the same setup method as for the first character, then with the
second character under control (the first following). Inputs only from
then on, after the character had picked the barrel up, carried it left
and thrown it left. That arrangement is not seen in ordinary play, so
**none of this was seen in ordinary play.**

- Catch ranges and height band: 1 536 jumps, each a standing wait of 3
  to 40 frames, then a jump straight up, after pressing Left or Right
  for 1 to 12 frames to set the facing and the place. 624 had Jump held
  for 20 frames (waits 3 to 28; 12 press lengths × 2 directions × 26
  waits) and 912 held it for 4, 8, 12 or 16 frames (waits 3 to 40, 6
  Left press lengths, 38 waits each) to put the fall at other heights.
  Facing was taken at the jump, not from the direction pressed: a Right
  press of 1 frame left the character facing left in all 26 such jumps
  (one per wait), and of 2 frames in 1 of 26. So 1 251 jumps were
  left-facing (485 caught) and 285 right-facing (179 caught). On 5 534 frames, in 699 jumps, the
  character's `y` was going up with D inside the range; none was a
  catch.
- Riding values: the 50 caught jumps of a grid of 2 directions × 5
  press lengths (1, 3, 5, 7, 9 frames) × 7 waits (8 to 20 frames in steps
  of 2), each with the distance and the height on the catch frame,
  the next two frames and the following 60 frames.
- Passing: the barrel rolling toward a second character who stood,
  crouched, walked left for 50 frames or walked right for 160 frames
  (one run each, repeated once): no hurt pose appeared and the barrel
  came within 0.85 px of the character's `x` (standing, crouching) or
  overtook it (walking right) or met it (walking left). Walking was not
  compared with the same inputs without a barrel. Two jumps straight up
  after waits of 40 and 60 frames were not caught.
- Ride: one ride of 933 frames to the face; 5 runs holding Left, Right,
  Down, Up or Action for 10 frames.
- Jump-off: Jump held for 1 to 6 frames starting on C − 2 to C + 4 (42
  runs). Starting on C + 2, C + 3 and C + 4, each jumped on its first
  frame; starting on C + 1 none jumped; starting on C, C − 1 and C − 2,
  held through C + 2 (3, 4 and 5 frames or more), the jump came on C + 2.
  One more run held Jump from C + 1 for 40 frames: no jump.
  The same grid with the first character (30 runs, C − 2 to C + 2, in
  its own Evidence) gave the same pattern.
- Pick-up: 7 runs.

## Not covered

- Ordinary play: the rules above were seen only in arranged situations
  (see "Evidence").
- Objects with their lowest point under the floor's top at a wall:
  an object inside the ground is lifted on a normal frame, and what
  happens on a wall frame was not measured.
- The ride's pictures and sounds, and a ride that meets a wall other
  than the one measured, or a barrel moving left: not measured. Riding
  the barrel with both characters on the way, or the second character
  with the first leading, was not measured.
- The other enemies, and whether it defeats them or turns back from
  them. The thrower and its balls for the second character, and a metal
  barrel rolling on the floor or just turned back at a wall meeting
  them. Walkers hit by a metal barrel thrown from other heights or in
  the air, from other floors, while it is rolling, or just after it
  turned back at a wall were not measured; nor were the second
  character's walker hits.
- Q + 21 (neither in the hands nor thrown by the frames measured): not
  measured.
- The thrower with a carried metal barrel, and with one already thrown
  to the left (after Q + 21): not measured.
- The characters: a barrel still in the air (flying) meeting a
  character; whether it passes through or hurts the second character; a
  barrel rolling left; catching a character who comes down while moving
  sideways at the catch frame (the ranges above are for a character
  standing still before the jump); where between 17.5 and 24.8 px above
  the barrel the catch height limit lies.
- The second character throwing it other than the throws described
  above (whether the metal barrel's turn at the step differs between
  being in the air and rolling, which was not recorded, walls other than
  the step, throws in the air, hits on walkers other
  than those described, the metal barrel in the hands after a hit and
  what it does later, slopes for a second-character throw); a throw by either character with the
  metal barrel inside a wall at the release (seen twice: no break, `vx`
  0 and the object moving up at 5 px per frame; not measured).
- Its pictures and how it is drawn when it turns, and what sounds play.
