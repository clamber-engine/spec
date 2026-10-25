# The metal barrel

Titles: `title-1`

The metal barrel is the object a `hidden-metal-barrel` leaves behind
(`hidden-objects.md`). It is picked up and carried as in `carrying.md`.
This file says what it does once it is thrown: how it flies, lands and
rolls, and what it does at a wall. The other things a thrown object can
meet (walkers, the thrower, balls, the characters) are not covered here;
see "Not covered".

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

## Not covered

- Ordinary play: the rules above were seen only in arranged situations
  (see "Evidence").
- Objects with their lowest point under the floor's top at a wall:
  an object inside the ground is lifted on a normal frame, and what
  happens on a wall frame was not measured.
- Riding or standing on it, and whether it takes anything with it: not
  measured.
- Walkers, the thrower, balls and the other enemies, and whether it
  defeats them or turns back from them.
- The characters: whether it passes through them or hurts them.
- The second character throwing it; a throw by either character with the
  metal barrel inside a wall at the release (seen twice: no break, `vx`
  0 and the object moving up at 5 px per frame; not measured).
- Its pictures and how it is drawn when it turns, and what sounds play.
