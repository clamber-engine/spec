# Platforms from below

Titles: `title-1`

What happens when a player who is jumping meets ground that is above
them: a platform with open space under it. The player is never stopped
by anything above their head. They rise into the platform from below,
pass up through it, and are lifted onto its top or fall back down
through it. Also what happens when a grounded player walks under a
platform ("Walking under a platform"). Frames are logic frames at 60 Hz, numbered as in a trace.
`x`, `y`, `vx` and `vy` are as in a trace: right and down are positive,
speeds in px per frame. Speeds are whole multiples of 1/256 px per frame
and are written both ways, as in `ground-speed.md`. "Left", "Right" and
"Jump" are the neutral inputs `L`, `R`, `J` of the scenario format.

Columns and face columns are as defined in `steep-faces.md`, and "higher"
means higher on screen (smaller `y`). "How far a surface is above the
feet" means the player's `y` minus that surface's `y`.

## Platforms

A **platform** is ground with a lower floor beneath it in the same
columns and open space between the two. Each column of a platform has a
**top**: the surface the player stands on. The platform also has an
**underside**, a height below its tops. Between the underside and a
column's top is that column's **layer**. Feet exactly level with the
underside are in the layer. Feet exactly level with the top are not:
they are on or above the top.

Which ground is a platform, and where its underside is, follows from
the level's ground (`../formats/level-ground.md`). A platform column's
top is a surface that is **passable from below**, with open space under
its band. The platform's underside in that column is the **bottom line
of the top's band**. So the underside is 0 to 31 px below the top,
depending on where the top is in its band, and a platform's layer never
reaches into the band below its top.

On the platform measured most, the top of the flat part was 15 px above
the bottom of its band, so the underside was 15 px below that top. Each
end of that platform was a face (4 px per column) sloping down from the
flat part to the bottom of the band, all in the same band. So the
underside was at the same height under the whole platform, ends
included, and the lowest column of each end had its top level with the
underside. On a second platform the top was the top line of its band,
and the underside was 31 px below it.

Ground that is **not** passable from below stops a rising player: see
"Ground that isn't passable from below".

## When the rules apply

On every frame that starts with the player airborne, after the frame's
new `vx` (`air-control.md`) and new `vy` (`jump-arc.md`, `walk-off.md`)
are worked out. The rules below replace the use of a platform column's
top as "the floor" in `air-walls.md` and `landing.md` where they say so.
Everywhere else those files apply unchanged, with a platform column's
top as its floor. "Walking under a platform" is the exception: it is
about frames that start with the player grounded.

## Inside a platform

The player is **inside** a platform from the end of a frame that starts
with the feet below the underside and ends with them in the layer of the
column the player is in. They stay inside, frame after frame, while
their feet stay in the layer. They stop being inside at the end of the
first frame on which the feet are below the underside, at or above the
top, or lifted onto the top (below).

While inside:

- **Moving sideways**: the platform doesn't block the player or lift
  them. `air-walls.md` is not applied to the platform's columns, so `x`
  moves by `vx` as usual.
- **Rising** (`vy` < 0 after the frame's speed change): if the feet
  before the frame are **2 px or less below the top** of the column the
  player moves into, the player is lifted onto it. First the player is
  moved up by the smallest whole number of pixels that brings the feet
  level with or above that top. Then `y` moves by the frame's `vy` as
  usual. The fraction of a pixel in `y` doesn't change before the
  vertical move. The player is no longer inside. They stay airborne,
  rise and fall as usual, and land on the top by the rule of
  `landing.md`.
- Feet **more than 2 px below** the top: `y` moves by `vy` and nothing
  else happens, even if the move takes the feet past the top. A player
  who ends such a frame at or above the top is above the platform and
  lands on it by `landing.md` as usual.
- **Falling** (`vy` > 0), or `vy` exactly 0: `y` moves by `vy` and
  nothing else happens. The player never lands on the top from inside.
  They fall back down through the platform and leave it through the
  underside, then land on the floor beneath by `landing.md`.

The 2 px limit is exact to 1/32 px, the finest step seen: feet 2 px
below the top were lifted, and feet 2 1/32 px below were not.

## Moving sideways into a platform from outside

When the player is not inside, a platform column the player would move
into (`x + vx`, as in `air-walls.md`) is treated as follows, using the
feet **before** the frame's vertical move:

| Feet before the frame | The new column counts as |
|---|---|
| below the underside | no platform: only the floor beneath is compared, as in `air-walls.md` |
| level with the underside or higher | a floor at the column's top, compared as in `air-walls.md` |

So with the feet at or above the underside, a face column of the
platform's end whose top is above the feet blocks the player at any
height difference. A column that isn't a face column blocks the player
when its top is more than 8 px above the feet, and the player is stepped
onto it when it is 8 px or less above, except for open sides (below). A
player who passes under the end with the feet below the underside, and
then rises into the layer, is inside.

**Open sides.** A column whose top is given by a half of shape **1** or
**5** (`../formats/level-ground.md`, "Shapes") that is passable from
below doesn't block the player. When its top is more than 8 px above
the feet before the frame, `x` moves by `vx` as usual and the player is
not lifted: the frame goes on as if the column were not part of the
platform, and if it ends with the feet in that column's layer, the
player is inside from the end of that frame. A top 8 px or less above
the feet is stepped onto as in the table. Only these two shapes do this:
the flat shapes 61 and 62, which give the same tops as 1 and 5, block
as in the table, and so do shapes 1 and 5 in ground that isn't passable
from below.

## Falling onto the top

A player falling onto a platform from above lands on its top by the rule
of `landing.md`, with one exception. A falling frame whose new position
is **below the underside** is not a landing on that column's top, even
if the feet started the frame at or above the top and would land by
`landing.md`. The player goes on falling. This was seen on the
lowest column of a sloped end, whose top is level with the underside.
There, feet that started the frame 1 to 2 px above that top and ended
it just below the top were not caught.

A setup method was used at the same kind of end, with the player moving right
into the lowest column at 2 px per frame and falling 3 7/16 px per frame
(vy 3 7/16 after the frame's speed change), in steps of 1/4 px of
starting height: every start from 1/4 px up to 3 1/4 px above the top
ended below it and was not caught; a start 3 1/2 px above the top ended
1/16 px above it and landed on it. The next columns, which are face
columns, blocked the same player (`air-walls.md`) for every start from
the underside up to just under their tops, and a start below the
underside passed through. A run seen by inputs agreed: a player who
rolled and then jumped, falling at about 5 px per frame into the lowest
column of a platform's sloped end with the feet about 1 1/2 px above its
top, fell through to the floor beneath instead of landing.

## Ground that isn't passable from below

Every surface on the first level with open space under it is passable
from below, so a rising player is never stopped there. The rules in this
section were measured on the platform of the first row of "Measured
cases" with its cells changed to not be passable from below (bit 6 of
their half bytes cleared, `../formats/level-ground.md`), and nothing
else changed.

**Feet line** is as in `view-vertical.md`: level y rounded up to a whole
px. A cell's **ground** in a column is as in `../formats/level-ground.md`:
the lines below its surface down to and including the bottom line of its
band.

On a frame that starts with the player airborne, with the feet below the
bottom line of a cell's band, and rising (`vy` < 0 after the frame's
speed change): if the frame's vertical move would end with the feet line
in that cell's ground, in the column the player is in after the frame's
sideways move, and the cell's surface isn't passable from below, the
player is **stopped**:

- The feet line stays what it was before the frame. Let `F0` be the
  feet line before the frame and `F1` the feet line that the move would
  give. The player ends the frame `F0` − `F1` px lower than the move
  would put them (`y` is the moved `y` + (`F0` − `F1`)). So the fraction
  of a pixel in `y` is the one the move gave, not the one from before the
  frame.
- `vy` becomes 0. The next frame's speed change is as usual
  (`jump-arc.md`, with the jump held or not), so the player starts to
  fall.
- `x` moves as usual. The sideways rules are the ones for a platform
  (`air-walls.md` and "Moving sideways into a platform from outside");
  they gave the same results with the cells passable or not, for feet
  below the bottom of the band.

A move that ends with `y` exactly on the bottom line of the band is
stopped (that line is in the cell's ground). A move whose feet line ends
level with or above the surface is not stopped. A column whose surface
is the bottom line of its band (rise 0) has no ground in that cell and
never stops the player.

So a player jumping up under such ground rises until a frame would take
the feet line into the bottom line of the ground's band. On that frame
the player stays at the same feet line and `vy` is 0, and they fall from
there. Their head is not what is stopped: it is above the feet, inside
the ground, as it would be in the layer of a platform.

## Walking under a platform

This section is about a **grounded** player (standing, walking,
running or rolling) on a floor with a platform above it, on every frame
that starts with the player grounded. The rules of `steep-faces.md` and
`ground-speed.md` apply as usual. This section says which surface they
treat as the floor of each column.

For a grounded player the floor of a column is **the ground below** at
the player's feet line, in that column (`../formats/level-ground.md`,
"The ground below a point"). For a platform above the floor, that gives
two cases.

**The floor isn't the top line of its band.** Then the platform's
underside is 2 px or more above the feet, and the platform doesn't count
for the grounded player at all. They stand, walk, run and roll under it
exactly as if it weren't there, at every height measured, with square
or sloped ends.

**The floor is the top line of its band, and the band above has a
surface in the column.** Then that surface, the top of a platform, is
the floor of the column. The platform's underside is the bottom line of
the band above, so it is exactly **1 px above the feet**, and its top is
1 to 32 px above the feet. This holds in every column of the platform,
not only at its ends. Moving into a platform column from the floor
beneath:

| The new column's top, above the feet | What happens |
|---|---|
| 8 px or less, and the column is a face column rising in the direction of the move | **Blocked**, as in `steep-faces.md` ("Stopped by a rise") |
| 8 px or less, not a face column | **Stepped onto**: as in `steep-faces.md` ("Following a face down"), the player stays grounded and `y` becomes the top. From there they are on the platform as on any floor |
| More than 8 px, a face column or not | The player **falls through the floor** (below) |

So a square end 4 or 8 px high is walked up, and a sloped end (a face of
4 px per column) blocks the player as a steep face does, at its first
column, 1 px above the feet. A player who moves fast enough to skip the
low columns of a sloped end, into a column more than 8 px up, falls
through. A single 4 px step is not a face (`steep-faces.md`, "Columns,
faces and steps").

Rolls follow the same table: a roll is blocked by a face column 8 px up
or less (the roll goes on in place, with `vx` 0), steps onto a square end 4 or
8 px high, and falls through at more than 8 px.

"Can't start toward a rise" (`steep-faces.md`) also uses the platform's
top as the floor of the column 5 ahead. A top 8 px or more above the
feet there stops the player from starting, and one 4 px above doesn't.

Walking off the far end of the platform's top, back to the floor 4 or
8 px lower, is following the floor down as in `steep-faces.md`.

### Falling through the floor

On the frame on which the player moves into a platform column whose top
is more than 8 px above the feet (frame **L**), `x` moves as usual, and
the player leaves the ground and starts to fall from the floor they were
on:

- On **L** the player moves down by **3.4375 px**, and `vy` ends at
  3.4375 (880/256).
- On each later frame, `vy` increases by **0.4375** (112/256), never
  above 8, and the player moves down by the new `vy`.

The floor the player was walking on is above them from L on, so it never
catches them. In every measured layout there was no ground lower down,
and the player fell out of the level (`falling-out.md`). Landing on
ground further down wasn't measured.

A roll that falls through within its first 9 frames moves differently
(S is the roll's first frame, as in `roll-input.md`):

| Frame L | Vertical motion |
|---|---|
| S | On L, down 3.4375 px. On S + 1 to S + 8, down 1/256 px each frame (`vy` 1/256). From S + 9, `vy` increases by 0.4375 per frame from 1/256 |
| S + 1 to S + 7 | On L to L + 9 (10 frames), down 1/256 px each frame. From L + 10, `vy` increases by 0.4375 per frame from 1/256 |
| S + 8 | On L, down 1/256 px. From L + 1, `vy` increases by 0.4375 per frame from 1/256 |
| S + 9 or later, and every frame that isn't a roll | As above: 3.4375 on L, then 0.4375 more per frame |

While falling through, `x` keeps moving as before L: walking and running
players with the direction held kept their speed, and rolls went on by
the rules of `roll-speed.md`. Other inputs while falling through aren't
covered.

## Riding

A player riding the animal (`riding.md`) meets platforms by the rules
above, with the rider's `y` as the feet, on every frame that starts with
the rider in the air: a jump's air frames, the hop of a charge, and
falls. The frame's new `vx` and `vy` are the ones `riding.md` gives.
So:

- Nothing overhead stops the rider. A rising rider passes into the
  layer and is inside; while inside it moves sideways freely, is lifted
  onto the top on a rising frame that starts with the feet 2 px or less
  below it ("Inside a platform"), and otherwise falls back down through
  the platform to the floor beneath.
- A rider above the top lands on it by the landing rule of `riding.md`
  ("Jumping"), and then rides on the top as on any floor.
- Moving sideways into a platform column from outside is as in "Moving
  sideways into a platform from outside", open sides included.
- A rider moving sideways out of the platform's columns while inside is
  no longer inside: it goes on rising or falling over the floor beneath.
- Ground that isn't passable from below stops a rising rider exactly as
  in "Ground that isn't passable from below": the feet line stays what
  it was, and `vy` becomes 0.
- A rider on the floor under a platform whose floor isn't the top line
  of its band is not affected by it ("Walking under a platform").

Measured in modified copies of the game with the first level's crate
moved onto the long flat floor near the level's start (as in
`riding.md`) and a platform added over that floor: 160 px wide (5
cells), with square ends, and its top 40, 48, 64 or 80 px above the
floor (underside 17, 17, 49 and 49 px above it; tops given by halves of
shapes 3, 1, 5 and 1), passable from below; and the 40 px and 64 px
platforms not passable from below. Each run played random inputs as in
`riding.md`, starting from standing still under the platform:

- Passable from below: 200 runs on each copy, 800 in all, 413 045
  frames compared. They held 2 504 entries into the layer from below,
  18 086 frames inside, 275 lifts onto the top, 1 711 falls back out
  through the underside, 109 moves out of the columns while inside, 91
  entries through open sides (moving left and right), 14 blocks by the
  ends of the 40 px platform and 18 steps onto an end. Runs were
  compared up to the frame the rider rode off the end of the platform's
  top (riding off a ledge, see "Not covered" in `riding.md`) or rose
  more than 113 px above the floor.
- Not passable from below: 60 runs on each copy, 18 848 frames compared,
  with 204 stops, each run compared up to the landing after its first
  stop (see "Not covered").

In every frame `x`, `y`, `vx`, `vy`, `facing` and the part of riding
were as the rules give. The same random runs on the floor without the
platform (60 runs, 86 636 frames) were as `riding.md` gives. Checked
by `riding.md` alone, without the rules of this file, 199 of the 200
runs on the 40 px passable copy and 199 of the 200 on the 64 px one
went wrong.

## What the trace shows

Rising into, through and out of a platform adds nothing to the trace.
The player is airborne throughout (`ground` 0, state `jump` or `fall` as
usual) and no event is added. The landing on the top, or on the floor
beneath after falling back, is an ordinary landing row with `land`.
Being stopped by ground that isn't passable from below adds no event
either: the row has `vy` 0, `ground` 0 and state `jump` or `fall` as
usual.

Walking under a platform without touching it adds nothing to the trace.
Stepping onto a platform's top is an ordinary grounded row (`ground` 1)
whose `y` is the top. Falling through the floor: from L on the player is
airborne (`ground` 0, state `fall`, or `roll` while the roll lasts), and
L carries `leave_ground`. `vy` is as in "Falling through the floor".

## Measured cases

Measured with the lead character in ordinary jumps, by inputs alone (494
runs from standing, walking and running, about 13 000 airborne frames)
and in controlled single frames (about 4 700), on two platforms, each
80 px above the floor beneath its flat part:

| Platform | Seen by inputs | Seen in controlled frames |
|---|---|---|
| Flat top, faces of 4 px per column at both ends, underside 15 px below the flat top, floor beneath sloping 1 in 4 under the ends | straight-up jumps from floors 76 to 80 px below the top: falling back and being lifted, both sides of the 2 px limit; walking and running jumps from both sides: passing under the ends, blocked by the ends, stepped onto the flat top from the end, rising inside across many columns | moving sideways into every end column and the first flat column, from both sides, rising and falling, feet from below the underside to above the top |
| Flat top, floor beneath flat | straight-up jumps: falling back, being lifted, landing on the top | — |

Inside a platform, `vy` from about −4.5 to +3.7 px per frame was seen,
and horizontal speeds up to 3 px per frame. No frame by inputs or in
controlled frames differed from the rules above. On a third platform,
crossed in play, the underside was also the bottom of its top's band.

Ground that isn't passable from below was measured on the first
platform with its cells changed (see that section), by inputs alone
in 452 runs (straight-up jumps and walking and running jumps from both
sides, as above) and in about 8 900 controlled single frames starting
with the feet below the bottom of the band (`vy` from 6 px per frame up
to 6 px per frame down, sideways speeds of 2 px per frame, in every end
column and on the flat part). In 301 runs the first frame that differed
from the unchanged platform was a stop by the rule above; the other runs
never reached the platform's band and didn't differ at all. Every
controlled frame matched the rule.

Open sides were measured on foot in copies of the first level's opening
floor with a platform 160 px wide over it, its top in the band 49 px
above the floor, square ends, and its cells' halves all of one shape,
passable from below: each of the shapes the first level's ground uses
in surfaces passable from below (1, 5, 9 to 23, 25, 33 and 54 to 62)
and the other flat shapes 2, 3, 4, 6, 7 and 8. In each copy the lead
character walked and ran right with Jump held from 90 different frames
for 6, 12, 24 or 40 frames (720 runs). Of the frames moving into the
platform's first column from the side with the feet at or above the
underside and the top above them, 144 had the top more than 8 px above
the feet in a column of shape 1 or 5: none was blocked or lifted, and
the player was inside at the end of the frame exactly when the feet
ended in the layer. With every other shape, all 1 951 such frames were
blocked as in "Moving sideways into a platform from outside". With
every shape, the 1 257 frames with the top 8 px or less above the feet
were stepped onto. With shapes 1, 5 and 62 not passable from below, all
308 frames with the top more than 8 px above were blocked.

Walking under a platform was measured on the first level's opening
floor with the ground changed: a flat floor and, over it, a platform
128 px wide with its top in the band above the floor's band, passable
from below. Nothing else was changed. The lead character walked, ran,
rolled (from walking, from rest and into a run), coasted to a stop and
started from rest toward both ends, from both sides: 9 640 runs, about
1 550 000 frames. Each run was compared with the same run on the same
floor without the platform.

| Floor in its band | Underside above the feet | Tops above the feet | Ends | Result |
|---|---|---|---|---|
| Not the top line (4 heights) | 5, 9, 17 and 25 px | 8 to 56 px (8 heights each) | square, and faces rising to 15 and 31 px above the underside | Every run the same as without the platform |
| Top line | 1 px | 4, 8, 12, 16, 20, 24, 28 and 32 px | square | 4 and 8 px stepped onto; 12 px and more fall through |
| Top line | 1 px | 16 and 32 px | faces of 4 px per column, 4 and 8 columns long | Blocked at the face; fall through where a move reached a column more than 8 px up |

Every grounded frame near the platform was checked against the rules of
"Walking under a platform", `steep-faces.md` and the ground below: about
1 209 000 frames following the floor or a top, 622 blocked frames,
1 483 frames that started a fall through the floor (132 of them in a
roll's first 9 frames) and 44 437 frames that started from rest with a
direction held, with 0 differences. Runs whose start was placed inside
the platform's end cell were left out. Rolls from rest reached the
platform on every frame from S to S + 28.

## Examples

Synthetic geometry. `y` is down-positive, as in a trace. Floor at
`y` = 0 for columns 100 to 299. A platform with its top at `y` = −78 for
columns 150 to 249 and its underside at `y` = −63. Its left end is a face
of 4 px per column: columns 146, 147, 148 and 149 have tops at −63, −67,
−71 and −75. Jump not held (gravity 112/256 = 0.4375) unless said.
Where a rule needs level y (the feet line), level y is `y` + 351: the
underside is then line 288, the bottom line of band 8, and the flat top
is line 273, 15 px above it, as the level format has it.

**Lifted onto the top.** Inside, `x` = 200.5, `y` = −70.25, `vx` = 0 and
`vy` = −704/256 before frame 1:

| Frame | `x` | `y` | `vx` (1/256) | `vy` (1/256) | `ground` | Note |
|---|---|---|---|---|---|---|
| 1 | 200.5 | −72.5625 | 0 | −592 | 0 | Inside |
| 2 | 200.5 | −74.4375 | 0 | −480 | 0 | Inside |
| 3 | 200.5 | −75.875 | 0 | −368 | 0 | Inside |
| 4 | 200.5 | −76.875 | 0 | −256 | 0 | Started 2.125 px below the top: not lifted |
| 5 | 200.5 | −79.4375 | 0 | −144 | 0 | Started 1.125 px below: up 2 px to −78.875, then `vy` |
| 6 | 200.5 | −79.5625 | 0 | −32 | 0 | |
| 7 | 200.5 | −78 | 0 | 0 | 1 | Lands on the top (`landing.md`) |

**Falling back through.** Inside, `x` = 200.5, `y` = −69.5, `vx` = 0 and
`vy` = −640/256 before frame 1. The highest point is 2.5625 px below the
top, so the player is never lifted:

| Frame | `x` | `y` | `vx` (1/256) | `vy` (1/256) | `ground` | Note |
|---|---|---|---|---|---|---|
| 1 | 200.5 | −71.5625 | 0 | −528 | 0 | |
| 2 | 200.5 | −73.1875 | 0 | −416 | 0 | |
| 3 | 200.5 | −74.375 | 0 | −304 | 0 | |
| 4 | 200.5 | −75.125 | 0 | −192 | 0 | |
| 5 | 200.5 | −75.4375 | 0 | −80 | 0 | Started 2.875 px below the top: not lifted. Highest point |
| 6 | 200.5 | −75.3125 | 0 | 32 | 0 | Falling: not lifted, doesn't land on the top |
| 12 | 200.5 | −65.375 | 0 | 704 | 0 | Still inside (frames 7 to 11 left out) |
| 13 | 200.5 | −62.1875 | 0 | 816 | 0 | Below the underside: no longer inside |

The player goes on falling and lands on the floor at `y` = 0.

**Passing under the end and rising inside.** Not inside, Right and Jump
held (gravity 72/256 = 0.28125), `vx` taken as a steady 512/256,
`x` = 147.25, `y` = −58 and `vy` = −1100/256 before frame 1:

| Frame | `x` | `y` | `vx` (1/256) | `vy` (1/256) | `ground` | Note |
|---|---|---|---|---|---|---|
| 1 | 149.25 | −62.015625 | 512 | −1028 | 0 | Feet below the underside: column 149's top is ignored |
| 2 | 151.25 | −65.75 | 512 | −956 | 0 | Started below the underside, ends in the layer: inside |
| 3 | 153.25 | −69.203125 | 512 | −884 | 0 | Inside: moving sideways freely |
| 4 | 155.25 | −72.375 | 512 | −812 | 0 | |
| 5 | 157.25 | −75.265625 | 512 | −740 | 0 | |
| 6 | 159.25 | −77.875 | 512 | −668 | 0 | |
| 7 | 161.25 | −81.203125 | 512 | −596 | 0 | Started 0.125 px below the top: up 1 px to −78.875, then `vy` |

**Blocked by the end.** Not inside, Right and Jump held, `x` = 145.5,
`y` = −64.5, `vx` = 512/256 and `vy` = −800/256 before frame 1. Column 147
(top −67) is a face column 2.5 px above the feet, and the feet are above
the underside:

| Frame | `x` | `y` | `vx` (1/256) | `vy` (1/256) | `ground` | Note |
|---|---|---|---|---|---|---|
| 1 | 145 | −67.34375 | 0 | −728 | 0 | Blocked (`air-walls.md`) |

**Stepped onto the flat top from the end.** Not inside, Right held,
`vx` taken as a steady 512/256, `x` = 149.5, `y` = −71 and
`vy` = −600/256 before frame 1. Column 151 is not a face column and its
top is 7 px above the feet:

| Frame | `x` | `y` | `vx` (1/256) | `vy` (1/256) | `ground` | Note |
|---|---|---|---|---|---|---|
| 1 | 151.5 | −79.90625 | 512 | −488 | 0 | Stepped onto: up 7 px to −78, then `vy` |
| 2 | 153.5 | −81.375 | 512 | −376 | 0 | |
| 3 | 155.5 | −82.40625 | 512 | −264 | 0 | |
| 4 | 157.5 | −83 | 512 | −152 | 0 | |
| 5 | 159.5 | −83.15625 | 512 | −40 | 0 | |
| 6 | 161.5 | −78 | 512 | 0 | 1 | Lands on the top |

**Stopped by ground that isn't passable from below.** The same
platform, but its cells are not passable from below. `x` = 200.5,
`y` = −58.25, `vx` = 0 and `vy` = −1000/256 before frame 1:

| Frame | `x` | `y` | `vx` (1/256) | `vy` (1/256) | `ground` | Note |
|---|---|---|---|---|---|---|
| 1 | 200.5 | −61.71875 | 0 | −888 | 0 | Feet line 290 (level y 289.28125): below the band |
| 2 | 200.5 | −61.75 | 0 | 0 | 0 | The move to −64.75 would give feet line 287, in the ground: stopped. Down 3 px, back to feet line 290 |
| 3 | 200.5 | −61.3125 | 0 | 112 | 0 | Falling |
| 4 | 200.5 | −60.4375 | 0 | 224 | 0 | |

The player goes on falling and lands on the floor at `y` = 0.

**Walking under a platform, floor at the top of its band.** Floor at
`y` = 0 for columns 100 to 299. Level y is `y` + 353, so the floor is
line 353, the top line of band 11. A platform has its tops in band 10
over columns 200 to 263: at `y` = −4 (a square end, 4 px up), or at
`y` = −12 (12 px up). Its underside is line 352, `y` = −1. Walking right
at a steady 512/256 with Right held, `x` = 195.25 before frame 1:

| Frame | `x` | `y`, top at −4 | `y`, top at −12 | `vy` (1/256), top at −12 | `ground`, top at −12 | Note |
|---|---|---|---|---|---|---|
| 1 | 197.25 | 0 | 0 | 0 | 1 | |
| 2 | 199.25 | 0 | 0 | 0 | 1 | |
| 3 | 201.25 | −4 | 3.4375 | 880 | 0 | Column 201: stepped onto at −4; at −12, falls through (L) |
| 4 | 203.25 | −4 | 7.3125 | 992 | 0 | |
| 5 | 205.25 | −4 | 11.625 | 1104 | 0 | |
| 6 | 207.25 | −4 | 16.375 | 1216 | 0 | Falling on, below the floor |

If the floor is 4 px lower instead, at line 357 (`y` = 4 in the frame
above), it isn't the top line of its band, and the underside is 5 px
above the feet. The player walks on under the platform at `y` = 4 with
either top, as if it weren't there.

## Not covered

- Walking under a platform: ground lower down that a player falling
  through the floor could land on; other inputs while falling through;
  tops found in the band above that aren't passable from below (they were seen to give the same
  first frame of a fall through the floor, and then differ); and a floor
  under a platform 2 bands up or more. Sloped ends with runs of 2 or 3
  face columns. A player already standing on the floor under a
  platform's top in the band above (not reachable by walking).
- Ground that isn't passable from below over a floor whose top line
  isn't the top of its band (a low ceiling): seen to make a difference
  for a grounded player, not measured further.
- Ground that isn't passable from below, in play: none was found on the
  first level. Also a rising frame that would take the feet past the
  whole of such ground at once (to its surface or above), and a player
  whose feet are already in such ground at the start of a frame.
- Platforms whose band has ground right under it in the band below
  (ground thicker than one band), in jumps and for a grounded player.
- In jumps on foot: platforms with square ends (not faces), other than
  the frame that moves into an end column from the side; platforms
  thinner than 15 px, and platforms whose ends slope at other rates.
- Moving sideways out of a platform's columns while inside, and a
  take-off from a floor that is itself in a platform's layer.
- Getting down through a platform from its top.
- Rolls, bounces off enemies (`bounce.md`), carrying (`carrying.md`),
  being knocked back (`knock-out.md`) and the other character inside a
  platform.
- Moving platforms, and entities overhead.
- Riding: the landing after a stop by ground that isn't passable from
  below. Under the 40 px platform the rider rode again 18 or 20 frames
  after the landing frame (9 runs), not 8 as `riding.md` gives; under
  the 64 px one, 8 frames after it (8 runs). Also riding off the end of
  a platform's top.
