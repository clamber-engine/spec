# The metal barrel

Titles: `title-1`

The metal barrel is the object a `hidden-metal-barrel` leaves behind
(`hidden-objects.md`). It is picked up and carried as in `carrying.md`.
This file says what it does once it is thrown: how it flies, lands and
rolls, what it does at a wall, that it defeats walkers, and what it does
with the first character (passing through, catching and carrying it).
The other things a thrown object can meet (the thrower, balls) are not
covered here; see "Not covered".

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
onto the floor behind the barrel. Presses on
C + 1 and earlier (tried down to C − 4) do nothing and are not remembered.

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
- Ride: one ride of 913 frames to the face; 4 runs holding Left, Right,
  Down or Action for 10 frames (and 2 with Jump).
- Pick-up: two runs (see above).

## Not covered

- Ordinary play: the rules above were seen only in arranged situations
  (see "Evidence").
- Objects with their lowest point under the floor's top at a wall:
  an object inside the ground is lifted on a normal frame, and what
  happens on a wall frame was not measured.
- Riding or standing on it by the second character, the ride's pictures
  and sounds, and a ride that meets a wall other than the one measured,
  or a barrel moving left: not measured.
- The thrower, balls and the other enemies, and whether it defeats them
  or turns back from them. Walkers hit by a metal barrel thrown from
  other heights or in the air, from other floors, while it is rolling, or
  just after it turned back at a wall were not measured; nor were the
  second character's walker hits.
- The characters: a barrel still in the air (flying) meeting a
  character; whether it passes through or hurts the second character; a
  barrel rolling left; catching a character who comes down while moving
  sideways at the catch frame (the ranges above are for a character
  standing still before the jump); where between 17.5 and 24.8 px above
  the barrel the catch height limit lies.
- The second character throwing it; a throw by either character with the
  metal barrel inside a wall at the release (seen twice: no break, `vx`
  0 and the object moving up at 5 px per frame; not measured).
- Its pictures and how it is drawn when it turns, and what sounds play.
