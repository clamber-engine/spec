# Slopes

Titles: `title-1`, `title-2`

How a player moves on sloped floor: horizontal speed, height while
grounded, stopping, jumping off and landing on a slope, and rolling.
Frames are logic frames at 60 Hz, numbered as in a trace. `x`, `y` and
`vy` are as in a trace: down is positive, speeds in px per frame.
Measured with the lead character on gentle slopes that rise or fall
**1 px for every 4 px** across, both uphill and downhill, moving right and
left: walking, running, stopping, standing, turning, crouching, jumping
(standing, walking and running jumps, short and long holds) and rolling (normal
and fast rolls, as named in `roll-speed.md`).

The sections below up to "In `title-2`" give the `title-1` values. "In
`title-2`" gives the `title-2` values.

## Floor height

The floor's surface has one height for each 1-px-wide column of `x`. On
flat floor every column has the same height. On a 1-in-4 slope the height
changes by **1 px every 4 columns**: four columns at one height, then four
at the next, with no columns in between. Where along the slope each 1-px
step falls is part of the level's geometry.

## Grounded on a slope

While the player is grounded, on every frame:

- `y` is the floor height of the column containing `x` (the column of the
  whole-pixel part of `x`), taken after the frame's horizontal move. `y`
  is always a whole number of pixels, with no fraction.
- Nothing else matters: the same column gives the same `y` whatever the
  speed, direction, facing or the fraction of `x`.
- So `y` changes by whole pixels on the frames where `x` crosses into a
  column of a different height, and doesn't change on other frames.
- In a trace, `vy` is 0 on these frames, even though `y` changes, and
  `ground` stays 1. Following a 1-in-4 slope down never made the player
  airborne at any speed measured: walking, running, and normal and fast
  rolls, up to 1000/256 px per frame (just under 4) going downhill. There
  is no `leave_ground`. The fastest grounded speed measured anywhere on a
  slope was 1014/256 px per frame, in a fast roll.

## Horizontal speed

Slopes don't change horizontal motion. The rules in `ground-speed.md`
(target speeds, the speed rule, stopping) and `roll-speed.md`
apply on 1-in-4 slopes exactly as on flat floor, uphill and downhill:

- Walking and running speed is the same as on flat floor: no slower
  uphill, no faster downhill.
- Releasing all input on a slope stops the player in the same distance
  as on flat floor.
- A player standing still on a slope stays still. There is no sliding.
- Roll speeds and roll lengths are the same as on flat floor, for
  normal and fast rolls, uphill and downhill, rolling right and left.

## Turning around on a slope

Turns on 1-in-4 slopes are the same as on flat floor. Everything in
`ground-speed.md` "Facing and turns" and "Standing, walking and running"
applies unchanged, uphill and downhill, facing either way:

- A turn starts on the same frame as on flat floor: at once while
  standing, and only on a turn check of the stride cycle while walking or
  running. The stride cycle runs at the same rate as on flat floor.
- The turn lasts 8 frames, facing changes on T + 4, and the speed rule
  applies on every frame of it, as on flat floor.
- It makes no difference whether the turn ends facing uphill or
  downhill, or whether the player crosses between flat floor and slope
  during it.
- While grounded during a turn, `y` follows "Grounded on a slope": the
  floor height of the column containing `x`, a whole number, with `vy` 0
  and `ground` 1. Turning never made the player airborne.

**Example.** On the synthetic floor of "Examples" below (flat to column
99, then down 1 px every 4 columns): standing still facing right at
`x` = 94.26953125, `y` = 0, Right held from frame 0 to 23, then Left
held from frame 24 on. Frame 24 is a turn check, so the turn starts there
(T = 24). Every row has `ground` 1, `vy` 0 and `state` `walk`.

| Frame | Input | `x` | `y` | `vx` | `facing` | `events` |
|---|---|---|---|---|---|---|
| 23 | R | 128.65234375 | 8 | 1.90625 | R | |
| 24 = T | L | 130.0703125 | 8 | 1.41796875 | R | |
| 25 | L | 131.0625 | 8 | 0.9921875 | R | |
| 26 | L | 131.68359375 | 8 | 0.62109375 | R | |
| 27 | L | 131.98046875 | 8 | 0.296875 | R | |
| 28 = T + 4 | L | 131.9921875 | 8 | 0.01171875 | L | `turn` |
| 29 | L | 131.75390625 | 8 | −0.23828125 | L | |
| 30 | L | 131.296875 | 8 | −0.45703125 | L | |
| 31 = T + 7 | L | 130.6484375 | 8 | −0.6484375 | L | |
| 32 | L | 129.83203125 | 8 | −0.81640625 | L | |
| 33 | L | 128.87109375 | 8 | −0.9609375 | L | |
| 34 | L | 127.78125 | 7 | −1.08984375 | L | |

The same inputs from standing still on flat floor give the same `vx`,
`facing` and `events` and the same change in `x` on every frame; only
`y` differs, and on the slope it is the floor height of each frame's
column (columns 128 to 131: 8; 124 to 127: 7).

Measured by playing the same input sequences from standing still on
flat floor and from 16 points on and to the left of a 1-in-4 dip (down,
a flat bottom, up), and comparing them frame by frame: 1,745 runs,
128,121 frames, 58,161 of them on slope columns, with no difference. 758
turns started on slope columns: 311 from standing, 408 from walking speed
and 39 from above walking speed; 415 ended facing downhill and 343
uphill.
The inputs were turns from standing, walking and running (running after
a roll with Action held), pauses before a turn, quick alternations of
Left and Right, and random sequences of Left, Right, Action and nothing.
Runs of the same inputs from different points on flat floor were also
identical to each other (190 runs).

## Crouching on a slope

Crouching on 1-in-4 slopes is the same as on flat floor. Everything in
`ground-speed.md` "Crouching" applies unchanged, uphill and downhill,
facing and moving either way: the target on D, the crouch of at least 13
frames with target 0, the target 0 on U to U + 9, and Down pressed again
during U to U + 9.

- A player crouching while standing still on a slope stays where it is:
  `vx` stays 0 and the player doesn't slide.
- A player that crouches while moving slows down under the speed rule
  exactly as on flat floor, and the slope doesn't change the speeds.
- While grounded during a crouch and the frames after it, `y` follows
  "Grounded on a slope": the floor height of the column containing `x`,
  a whole number, with `vy` 0 and `ground` 1. Crouching never made the
  player airborne.

**Example.** On the synthetic floor of "Examples" below (flat to column
99, then down 1 px every 4 columns): standing still facing right at
`x` = 94.26953125, `y` = 0, Right held from frame 0 on, and Down held as
well on frames 20 to 24. The crouch lasts to frame 32 although Down is
released on frame 25. Every row has `ground` 1 and `vy` 0.

| Frame | Input | `x` | `y` | `vx` |
|---|---|---|---|---|
| 19 | R | 121.1015625 | 6 | 1.8515625 |
| 20 = D | D+R | 122.96875 | 6 | 1.8671875 |
| 21 | D+R | 124.60546875 | 7 | 1.63671875 |
| 22 | D+R | 126.0390625 | 7 | 1.43359375 |
| 23 | D+R | 127.296875 | 7 | 1.2578125 |
| 24 | D+R | 128.3984375 | 8 | 1.1015625 |
| 25 | R | 129.36328125 | 8 | 0.96484375 |
| 26 | R | 130.2109375 | 8 | 0.84765625 |
| 31 | R | 133.125 | 9 | 0.44140625 |
| 32 = D + 12 | R | 133.51171875 | 9 | 0.38671875 |
| 33 = U = D + 13 | R | 133.8515625 | 9 | 0.33984375 |
| 34 | R | 134.15234375 | 9 | 0.30078125 |
| 41 | R | 135.48046875 | 9 | 0.12890625 |
| 42 = U + 9 | R | 135.59375 | 9 | 0.11328125 |
| 43 = U + 10 | R | 135.94140625 | 9 | 0.34765625 |
| 44 | R | 136.4921875 | 10 | 0.55078125 |
| 45 | R | 137.22265625 | 10 | 0.73046875 |

The same inputs from standing still on flat floor give the same `vx` and
the same change in `x` on every frame; only `y` differs, and on the slope
it is the floor height of each frame's column.

Measured by playing the same input sequences from standing still on
flat floor and from 16 points on and to the left of a 1-in-4 dip (down,
a flat bottom, up), and comparing them frame by frame: 6,194 runs,
393,588 frames, 178,281 of them on slope columns, with no difference.
2,902 crouches started on slope columns: 928 from standing still, 1,789
from walking speed and 185 from above walking speed (running after a
roll). The inputs were Down held for 1 to 40 frames from standing,
walking and running, with Right, Left or neither held during and after
it, turns into and out of a crouch, Down pressed again just after a
crouch, and random sequences of Left, Right, Down, Action held and
nothing. Runs of the same inputs from different points on flat floor
were also identical to each other (679 runs).

## Jumping from a slope

The take-off frame **T** (`jump-arc.md`) is still a grounded frame, so
the rule above applies on it: on T the player moves horizontally as usual
and `y` becomes the floor height of the column under the new `x`. On flat
floor that means `y` is unchanged, as `jump-arc.md` states; on a slope `y`
changes by 1 px on T if `x` crosses into the next 4-column step.

From T + 1 the arc is the one in `jump-arc.md`, starting from `y` on T:
the same `vy` on every frame, the same gravity and the same effect of
holding jump. The slope only changes where the jump starts and where it
ends.

## Landing on a slope

The landing rule in `landing.md` applies, measuring the height above the
floor of the column containing `x` **after** the frame's horizontal move:

- The player lands if they are moving down and are now less than 5 px
  above that floor, or at or below it.
- On landing, `y` becomes that column's floor height, `vy` 0, `ground` 1.

When `x` crosses into a column with a different floor height on the
landing frame, the player lands on the new column's height, not the one
under the previous frame's `x`.

## Examples

A synthetic floor: columns up to 99 at `y` = 0, then going down 1 px every
4 columns to the right (columns 100 to 103 at `y` = 1, 104 to 107 at
`y` = 2, 108 to 111 at `y` = 3, and so on).

**Walking down.** Walking right at 2 px per frame, `x` = 96.5 and `y` = 0
before frame 1:

| Frame | `x` | `y` | `vy` | `ground` |
|---|---|---|---|---|
| 1 | 98.5 | 0 | 0 | 1 |
| 2 | 100.5 | 1 | 0 | 1 |
| 3 | 102.5 | 1 | 0 | 1 |
| 4 | 104.5 | 2 | 0 | 1 |
| 5 | 106.5 | 2 | 0 | 1 |
| 6 | 108.5 | 3 | 0 | 1 |

Walking left back up the same slope from 108.5 at −2 px per frame gives
the same `y` for each column, in reverse: 106.5 → 2, 104.5 → 2,
102.5 → 1, 100.5 → 1, 98.5 → 0.

**Jumping from the slope.** Same walk, jump pressed and held so that T is
frame 6 (jump held through these frames):

| Frame | `x` | `y` | `vy` | `ground` |
|---|---|---|---|---|
| T − 1 = 5 | 106.5 | 2 | 0 | 1 |
| T = 6 | 108.5 | 3 | −7 | 1 |
| T + 1 | 110.5 | −3.71875 | −6.71875 | 0 |
| T + 2 | 112.5 | −10.15625 | −6.4375 | 0 |
| T + 3 | 114.5 | −16.3125 | −6.15625 | 0 |

**Landing across a step.** Falling, before the frame: `x` = 115.5
(column 115, floor `y` = 4), `y` = −3.25, `vy` = 5.5. On the frame `vy`
becomes 5.9375 (gravity 0.4375), `x` moves to 117.5 (column 117, floor
`y` = 5) and `y` to 2.6875, which is 2.3125 px above that column's floor.
The player lands with `y` = 5 (not 4), `vy` 0, `ground` 1.

## In `title-2`

Everything in this section was measured on `title-2` only, with the lead
character, on two slopes that rise or fall **1 px for every 2 px** across
and on short stretches that rise or fall **1 px for every 4 px**, with
flat floor between them. They were crossed uphill and downhill, moving
right and left: walking, running, stopping and standing at many points,
turning, crouching, slow, normal and fast rolls (`roll-speed.md`, "In
`title-2`"), standing, walking and running jumps with short and long
holds, jumps out of rolls, and random input sequences. Every input
sequence was also played on flat floor and the two were compared frame by
frame. Speeds are whole multiples of 1/256 px per frame, written both
ways. A jump starts on **G** and takes off on **T = G + 2**, as in
`jump-input.md` and `jump-arc.md` ("In `title-2`").

### Floor height (`title-2`)

As in `title-1`, the floor's surface has one height for each 1-px-wide
column of `x`, always a whole number of pixels. On a 1-in-2 slope the
height changes by **1 px every 2 columns**, on a 1-in-4 slope by **1 px
every 4 columns**.

The floor is made of **pieces** 16 columns wide. In the levels measured
a piece starts at a level `x` that is a multiple of 16. A piece is
either flat (all 16 columns at one height) or sloped (its columns are not
all at one height). A slope is a run of sloped pieces. Each slope
measured started and ended on a piece boundary. Whether a column belongs
to a sloped piece matters for landing (below), even when that column has
the same height as its neighbours: for example the columns at the top of
a short rise, where a piece going up meets a piece going down, or the
first columns of a slope that are still at the height of the flat floor
before it.

### Grounded on a slope (`title-2`)

The `title-1` rule holds. While the player is grounded, on every frame
`y` is the floor height of the column containing `x` (the column of the
whole-pixel part of `x`), taken after the frame's horizontal move. Only
the column matters, not the speed, direction, facing or the fraction of
`x`. In a trace `vy` is 0 and `ground` 1 on these frames.

Following a slope down never made the player airborne, on 1-in-2 slopes
included, at any speed measured. The fastest grounded speed measured on
a slope was **1138/256** px per frame (about 4.45) going downhill, in a
fast roll. There is no `leave_ground` on a slope. Steps down where
pieces meet, and how far down the floor is followed from slopes of any
steepness, are in `steep-faces.md` ("Following the floor down
(`title-2`)").

### Horizontal speed (`title-2`)

Slopes don't change horizontal motion. On 1-in-2 and 1-in-4 slopes,
uphill and downhill, everything in `ground-speed.md`, `roll-input.md`,
`roll-speed.md` and `air-control.md` ("In `title-2`") holds exactly as on
flat floor:

- walking and running speed (no slower uphill, no faster downhill),
  stopping distance, turns, and crouching (getting down, crouched,
  getting up);
- a player standing still on a slope stays still. There is no sliding;
- roll sizes, steering, lengths and stops, for slow, normal and fast
  rolls, and jumps out of rolls;
- horizontal speed in the air.

On every frame up to the first one where the player is grounded on one
floor and not on the other (a jump landing earlier or later because the
floor is higher or lower), the slope run and the flat run had the same
`vx`, target speed, state and facing. That includes the frames on which
a jump starts.

### Jumping from a slope (`title-2`)

On G, G + 1 and T the player is still grounded, so the rule above applies
on these frames too. `y` follows the floor under the new `x` on each of
them, including T, as `jump-arc.md` describes for flat floor ("`y`
unchanged").

**T + 1**, the first airborne frame, starts from the **higher** of two
heights: the player's `y` on T, and the floor height of the column
containing `x` after T + 1's horizontal move. The player then moves by
T + 1's `vy`, as in `jump-arc.md` ("Each frame after T"). So:

- going uphill, if T + 1's move reaches a higher column, the jump starts
  from that column's height: the player ends T + 1 up to 1 px higher than
  they would on flat floor;
- going downhill, the jump starts from the height on T, as on flat floor.

From T + 2 the arc is the one in `jump-arc.md`: the same `vy` on every
frame, the same gravity and the same effect of holding jump. The slope
only changes where the jump starts and where it ends.

### Landing (`title-2`)

On each falling frame, after the frame's horizontal move and its gravity,
take the column containing the new `x` and its floor height:

- If the column belongs to a **flat piece**, the player lands if moving
  by the frame's `vy` would take them to the floor or below it, as in
  `walk-off.md` ("In `title-2`").
- If the column belongs to a **sloped piece**, the player lands if moving
  by `vy` would take them to **3 px above the floor** or lower.

On landing, `y` becomes that column's floor height, `vy` 0, `ground` 1,
and the row carries `land`. Landing on a sloped piece can therefore move
the player down by up to 3 px more than `vy`. Reaching the limit exactly
(the floor on a flat piece, 3 px above it on a sloped piece) lands, and
falling 1/256 px short of it doesn't. When `x` crosses into a column of a
different height on the landing frame, the new column's height is the
one used, as in `title-1`.

The 3 px applies only on sloped pieces. Falls that ended a frame within
3 px above flat floor without reaching it didn't land on that frame,
including on the first flat column after a slope.

This was measured on 1-in-2 and 1-in-4 slopes. At a steep face the
margin was seen to follow the column instead of the piece: 3 px on face
columns, and 0 on 8 columns of flat floor that share a piece with the top
of a face (`air-walls.md`, "In `title-2`").

### Examples (`title-2`)

A synthetic floor of four pieces: columns 0 to 15 flat at `y` = 0;
columns 16 to 31 sloped, going down 1 px every 2 columns to the right
(16 and 17 at `y` = 0, 18 and 19 at 1, ..., 30 and 31 at 7); columns 32
to 47 sloped the same way (32 and 33 at 8, ..., 46 and 47 at 15);
columns 48 to 63 flat at `y` = 16.

**Walking down.** Walking right at 2.25 px per frame, `x` = 30.5 before
the frame:

| Frame | `x` | Column | `y` |
|---|---|---|---|
| 1 | 32.75 | 32 | 8 |
| 2 | 35 | 35 | 9 |
| 3 | 37.25 | 37 | 10 |
| 4 | 39.5 | 39 | 11 |

**Taking off.** Jump held from G. Running right (downhill) at 3.375 px
per frame: on T, `x` = 36 and `y` = 10 (column 36), `vy` −8.21875. On
T + 1, `vy` is −7.8671875 and `x` moves to 39.375 (column 39, floor 11).
The higher height is the 10 of T, so `y` = 10 − 7.8671875 = 2.1328125.

Running left (uphill) at −3.375 px per frame: on T, `x` = 39.375 and
`y` = 11. On T + 1, `x` moves to 36 (column 36, floor 10). The higher
height is the floor's 10, so `y` = 2.1328125, not 3.1328125.

**Landing on a sloped piece.** Before the frame: `x` = 40.5, `y` = 4,
`vy` 8 (the cap), moving right at 2.25. The frame moves `x` to 42.75:
column 42, floor 13, on a sloped piece. Moving by 8 would take the player
to 12, which is 1 px above the floor and within 3 px of it. The player
lands with `y` = 13.

**Not landing on a flat piece.** Before the frame: `x` = 50.5, `y` = 7.5,
`vy` 8. The frame moves `x` to 52.75: column 52, floor 16, on a flat
piece. Moving by 8 takes the player to 15.5, 0.5 px above the floor. They
don't land on this frame. On the next frame they land with `y` = 16.

Scenario `slope-dip` taps jump on frame 225 while walking right at 2.25
px per frame. Its summary describes `title-1`, where the tap comes on
the way down and the landing is on the flat bottom. `title-2` differs on
both: in its reference trace the player is already on the flat bottom by
then, so `jump_start` (T) is frame 227, 16 px below the start floor, and
the landing is past the slope up, at the start floor's height. The top
of the arc is 73.125 px above the bottom. On frame 261 the player ends
4.125 px above the floor under them (column 573, 1 px below the start
floor), more than 3 px, so they don't land. They land on frame 262 in
column 576, at the height of the start floor. The walk then goes on to
`x` 704, past column 650, the last one the scenario's `requires`
describes. The floor where the trace was recorded stays flat at the
start floor's height up to column 905. To match this trace's `final_x`
and `final_y`, a `title-2` test level built from `requires` continues
the floor at 0 px, with nothing above it, to at least column 704.

### Evidence (`title-2`)

2,220 runs on the slopes, each also played on flat floor, 0 mismatches:

- 226,830 frames with the same `vx`, target, state, facing and `ground`
  as on flat floor (97,047 of them on sloped pieces: 30,996 walking,
  6,171 running, 11,401 rolling, 11,050 standing, 919 turning, 4,757
  crouching or getting up, 1,238 before take-off, 28,523 rising or
  falling in a jump, 1,992 landing), and the same vertical move and
  `vy` on 72,144 airborne frames before any difference in landing;
- `y` equal to the floor height on 173,353 grounded frames;
- 2,820 take-offs, 540 of them with T + 1's column at a different height
  from `y` on T;
- 1,174 landings (561 on sloped pieces, 195 within the 3 px, 238 across a
  step on the landing frame), and 22,325 falling frames that didn't land
  (201 of them ending within 3 px above flat floor);
- 525 falling frames after a landing difference.

The floor heights came from the grounded frames themselves: no column was
ever seen at two heights. The take-off and landing rules were found on
the first 1,620 runs, which include one set of random input sequences.
600 further runs of random input sequences were recorded afterwards and
matched with 0 mismatches.

At the limit itself: 90 falls (46 over sloped pieces, 44 over flat ones)
were each replayed three times with the player moved, between two frames
and without changing anything else, so that the next frame's move ended
exactly at the limit, 1/256 px past it, or 1/256 px short of it. All 180
falls that reached or passed the limit landed and none of the 90 that
fell short did.

Which columns have the margin: 323 falls from standing jumps across
the site (109 ending over sloped pieces, 214 over flat ones, some of the
flat ones on top of a raised stretch of ground) were each replayed so
that the last move ended exactly 3 px above the floor, 2 px above it,
1/256 px above it, or on it (and also 1 px above it for 21 of them).
Over sloped pieces all of these landed; over flat pieces only the one
reaching the floor did. This
includes a 1-in-4 piece whose first columns are at the height of the flat
piece before it (margin) and the flat pieces on both sides of it (no
margin). It also rules out pieces 32 columns wide: the flat piece just
after that 1-in-4 piece starts 16 columns past a multiple of 32, and it
has no margin.

### Not covered in `title-2`

- Slopes of other steepness, apart from how far down their floor is
  followed (`steep-faces.md`, "Following the floor down (`title-2`)"),
  and slopes that meet a wall, a ceiling or a ledge. Walking or rolling
  off the end of the ground onto a slope.
- Whether pieces could be 8 columns wide rather than 16: every 8 columns
  of the slopes measured contain a change of height, so the two fit the
  measurements equally. (32 is ruled out, see "Evidence".)
- Horizontal motion on and after a landing frame that comes at a
  different time than on flat floor (it wasn't compared).
- Carrying objects on slopes (picking one up ended the runs), enemies on
  slopes, bounces that land on a slope, the Down + Action ground action,
  and the second character.

## Not covered

This list is for `title-1`; `title-2` has its own list above.

- Slow rolls on a slope, and grounded speeds above 1014/256 px per frame
  on a slope.
- Slopes steeper or shallower than 1 in 4, and where a slope meets a
  ledge, wall or ceiling.
- Whether the 5 px landing margin is ever decided by the previous
  column's floor (landings measured here were placed on the new column's
  floor in every case; none fell exactly between the two rules).
- The Down + Action ground action on slopes.
- Walking off the end of a slope, moving or tilting floors, and enemies
  or objects on slopes.
- Carrying objects is in `carrying.md`. Its pick-up, carrying and throw
  rules held on 1-in-4 slopes as measured; putting an object down on a
  slope isn't covered.
- The other character.
