# Moving on the level map

Titles: `title-1`

Between levels the player chooses where to go on a **level map**. The
lead character (the **lead**) stands at a **stop** (a level or another place on the map); a
direction makes it **walk** along a path to another stop. This file says
how a walk moves, frame by frame, when a direction starts one, where
the lead stands when the map appears, how a new game begins on the map,
what a press at a stop does, and what happens on the map after a level
is finished.

Where the stops and paths are and which directions take which path come
from the map's data: `../formats/level-map.md`. Which paths are open
comes from the player's progress (`../formats/level-map.md`,
"Progress").

Frames are logic frames at 60 Hz. The map has occasional lag frames
("Pages"); they aren't counted.

## Positions

A map position is `x` (to the right) and `h` (height, upward), in
pixels. The lead's position has a whole-pixel part and a fraction in
steps of 1/256 px, kept separately for each axis. "Whole pixel" means
the value rounded down.

The map is wider than the screen. It is drawn as **pages** 256 px wide,
side by side, and `x` runs on across the pages without a break.

## Paths and points

A path leads from one stop to another. It is a list of **points**, in
whole pixels: the corners where the path turns, and last, the end of
the path at the destination stop. The lead doesn't stop at the corners.

A path is taken with one or more of the four directions from the stop
it leaves; two directions can take the same path. A path can be walked
only while it is **open**. A walk always follows one path from the stop
to its end.

## Walking a path

The walk goes from point to point. Each stretch is a **leg**. The first
leg starts at the lead's whole-pixel position when the walk starts; each
later leg starts at the point the previous one ended at.

For a leg from (`x0`, `h0`) to (`x1`, `h1`) the lead's speed is

- across: (`x1` − `x0`) / 64 px per frame,
- up: (`h1` − `h0`) / 64 px per frame,

exactly; in steps of 1/256 px that is 4(`x1` − `x0`) and 4(`h1` − `h0`).
The two axes move independently. On every frame of the leg, for each
axis:

1. If the axis has already arrived, it doesn't change.
2. Otherwise its speed is added to it (whole pixel and fraction together).
3. An axis moving toward larger values has **arrived** once its whole
   pixel is the end's or more. An axis moving toward smaller values has
   arrived once its whole pixel is less than the end's; its whole pixel
   is then put back to the end's, and its fraction is kept.

An axis whose speed is 0 has arrived from the start of the leg.

The leg ends on the frame on which both axes have arrived. On the next
frame the next leg moves. The frame on which the last leg ends is the
walk's **last frame**, `L`. The lead then stands at the destination
stop.

On an arrival frame the position can stay the same. For example, an
axis moving at exactly −1 px per frame keeps its fraction, and its
whole pixel is put back to where it was.

The fractions are never reset by walking. They carry from leg to leg
and from walk to walk. The position doesn't snap to the points. So a leg
takes about 64 frames, whatever its length, but not exactly: legs of 63
to 72 frames were measured. A leg is longest when an axis moves toward
smaller values slowly from a large fraction. Moving down at 1/8 px per
frame from a fraction of 7/8, it has 8 7/8 px to cover before its whole
pixel drops below the end's: 72 frames.

### Worked example

These values are made up. The lead stands at `x` = 100 + 128/256 and
`h` = 40 + 192/256 and walks a path with the points (130, 52) and then
(126, 20). Leg 1 speeds are +120/256 across and +48/256 up. Leg 2 speeds
are −16/256 across and −128/256 up. Frame 0 is the first frame of the
walk. Positions are after the frame.

| Frame | `x` | `h` | |
|---|---|---|---|
| 0 | 100 + 248/256 | 40 + 240/256 | leg 1 |
| 1 | 101 + 112/256 | 41 + 32/256 | |
| 58 | 128 + 40/256 | 51 + 208/256 | |
| 59 | 128 + 160/256 | 52 | `h` arrives (52 or more) |
| 60 | 129 + 24/256 | 52 | |
| 61 | 129 + 144/256 | 52 | |
| 62 | 130 + 8/256 | 52 | `x` arrives; leg 1 ends |
| 63 | 129 + 248/256 | 51 + 128/256 | leg 2 |
| 64 | 129 + 232/256 | 51 | |
| 126 | 126 + 8/256 | 20 | whole pixels equal the end's, not less |
| 127 | 126 + 248/256 | 20 + 128/256 | both below 126 and 20: put back, fractions kept; `L` = 127 |

Leg 2 takes 65 frames (63 to 127).

## Starting a walk

### Standing frames

A walk can start only on a **standing frame**: a frame on which the
lead is at a stop and not walking (from `L` + 1 after a walk), except
the frames ruled out in "Pages" and "When the map appears".

### The remembered press

On a frame on which some buttons are **newly pressed** (held on this
frame, not on the previous one), those buttons, and only those, become
the **remembered press**. It lasts for that frame and the next 30
frames, unless a later new press replaces it. After that there is none.
A button that is only kept held is never remembered.

### The direction

On a standing frame, take the buttons held on that frame together with
the remembered press. The direction is the first of **Right, Left, Up,
Down** among them; other buttons don't count here. If the stop has an
open path for that direction, the walk starts on this frame, and the
lead already moves on it (frame 0 of the walk). Otherwise nothing
happens on this frame, even if another of the buttons has an open path.

So:

- A one-frame press at a stop starts a walk on that frame.
- Presses during a walk don't change it. A walk can't be turned back or
  stopped.
- A direction pressed during a walk starts the next walk at `L` + 1 if
  it was pressed on `L` − 29 or later (and nothing replaced it).
  Pressed earlier, it has been forgotten by then. A direction held on
  `L` + 1 also starts it.
- A held direction with no path can block a remembered one: with Right
  held and Down remembered, Right is chosen, and if Right has no path,
  nothing happens until Right is released.
- A new press of any button replaces the remembered press, so pressing
  A after Down during a walk makes Down forgotten.

Opposite directions held together (Up with Down, Left with Right) are
not covered. A standard pad can't hold them.

## Pages

The map view shows one page at a time: the page that holds the lead's
whole-pixel `x`. On the frame on which a walk takes the lead's whole
pixel onto another page, the view changes to that page. That frame is
followed by one lag frame.

If a page change happens on frame `P`, no walk can start before
`P` + 64. A walk that arrives soon after a page change therefore waits.
For example, with the page change on frame 39 and the walk's last frame
63, a held direction starts the next walk on frame 103.

## The map's picture

The map's picture is the **page picture** of the page that the view
shows ("Pages"), as `../formats/level-map.md` ("The pages' pictures")
builds it: 256 × 224 px, the whole screen, and the only background
layer. It doesn't move or scroll while its page is shown: its pixel
column `c` and row `r` are at screen column `c` and row `r`, which are
the picture coordinates of "Pictures" ("Where"). Its transparent pixels
show the backdrop, colour 0 of row 0 of the page's palette set.

The lead, and the name shown at the top of the screen for the stop the
lead stands at ("The stop's name"), are drawn over the picture, in front of all of it: no
part of the picture is drawn over them. The other character is drawn
the same way: no part of the picture is drawn over it, at the
distances from the lead measured ("Measurements").

**Where the two characters overlap**, the other character is in front:
every pixel of the other character's picture shows, and the lead's
pixels under it don't, on whichever side of the lead the other stands.
This holds with the second character leading as well: the other is then
the first character, and it is the one in front.

**A page change.** The picture of the new page replaces the old one at
once, with no part of the old picture dimmed, and fades in. The picture
for frame `P` − 1 (`P` as in "Pages") is black: the old picture is
already gone. From frame `P` the new picture fades in as the map does
when it appears ("When the map appears"): brightness `j` (15 full, 0
black) on frames `P` + 2`j` and `P` + 2`j` + 1, for `j` from 0 to 15,
and 15 from `P` + 32. The lag frame that follows `P` isn't counted, so
the pictures for `P`, for the lag frame and for `P` + 1 are black as well
(brightness 0), and `P` + 2 and `P` + 3 are the first dimmed ones. The
fade changes nothing else: the lead's position, the walk and the rule
that no walk starts before `P` + 64 are as in "Pages".

### Example

Synthetic. The lead's walk takes its position onto the next page on
frame 100 (`P` = 100). The pictures for frame 99, for 100, for the lag
frame after it and for 101 are black. Frames 102 and 103 show the new
page's picture at brightness 1, frames 104 and 105 at 2, and so on up to
frames 130 and 131 at 15. From frame 132 the picture is at full
brightness.

### Measurements

Measured on screen in the first level's map. A setup method (research
tools) made the map start with the picture of each page in turn and put
the lead beside a page edge; no modified copy of the game was used.

- The picture on each of the 13 pages, at full brightness and with the
  background layer alone, was the one `../formats/level-map.md` gives:
  13 pages of 57 344 px, 0 differing. Drawn over it were 500 to 1 237 px
  of sprites a page (the lead and the stop's name), all of them
  as the sprite layer alone draws them: 0 differing.
- Both characters on the map: at 18 places of the first level's map
  (every third of the places with a position), with the lead at the
  place and the other (a setup method placed it) at the same height as
  the lead and at six horizontal distances from it: 40, 24 and 8 px to
  its left, 0 px (both characters at the same place), and 8 and 24 px
  to its right. 18 places times 6 distances is 108 screens at full
  brightness, 90 frames after the placement. Of 905 to 1 428 px of
  sprites each (121 178 in all; the characters and the place's name)
  every one was, with the picture off, the same colour as with it on:
  0 differing.
- The two characters overlapping, at one place (the first level's, with
  the other put by a setup method at the same height as the lead). The screen with the sprite layer alone
  was taken with the other 70 px to the left and 70 px to the right of the lead
  (90 frames after the placement, and 30 more), which gives the lead's
  pixels (those the same in both) and the other's (those that change),
  and then with the other at 24, 16, 12, 8 and 4 px to the left of the
  lead, at 0 px (both at the same place), and 4, 8, 12, 16 and 24 px
  to the right, 90
  frames after the placement. The two characters' pictures overlapped
  by 17 to 97 px for the other 16 to 4 px to the left, 0 px, and
  4 px to the right (6 distances); at 24 px to the left and
  from 8 px to the right they didn't overlap. Of the other's pixels, 0
  were different on the screen in every one of the 11 distances. Of the
  lead's, 35, 67, 88, 76, 45 and 15 px were different at the 6
  overlapping distances (16, 12, 8, 4 px left, 0, and 4 px right): at
  most 9 fewer than the overlap in each, the pixels where both were the
  same colour.
- The same with the second character leading, at the same place and with
  the other put at the same height as the lead (a setup method), and the
  screens taken 20, 25 and 33 frames after the placement (the map
  walks the lead away a little later, so these are earlier than above).
  The lead's pixels and the other's were found as above, with the other
  70 px to either side. Distances of the other from the lead: 40, 24, 8
  px to its left, and 8 and 24 px to its right in all three, and 16 px
  to the left, 0 px and 4 px to the right in the first two (21 screens).
  The pictures overlapped by 7 to 9 px at 24 px to the left, 79 to 83 at
  16, 122 to 132 at 8, 51 to 55 at 0 and 7 to 15 at 4 px to the right;
  at the other distances they didn't overlap. Of the other's pixels, 0
  were different on any of the 21 screens. Of the lead's, 6 to 9, 75 to
  81, 113 to 119, 43 to 46 and 6 to 14 px were different at those five
  overlapping distances: at most 13 fewer than the overlap in each.
- Three page changes were played with the direction held, 200 frames
  each: from the page numbered 7 to the one numbered 8 and back, and from
  2 to 1. In each, the screens for frames up to `P` − 2 showed the old
  picture whole (3 584 of 3 584 sampled pixels), the screen for `P` − 1
  was a single colour whose channel sum was 0 (black), and from the screen for `P` the picture's
  brightness, measured as the sum of the screen's colour channels over
  the new picture's, was `j`/15 to within 0.02 for every frame from `P`
  to `P` + 40 (`j` as above); the new picture was whole from `P` + 32.

## The stop's name

A **name** is drawn at the top of the map's screen, in front of the
picture, for the stop the lead stands at or is walking to. This section
says where it is, which names are the same, and which name is on which
frame. The letters of a name aren't given: the engine draws the stop's
name in its own text.

**Where.** A name is a line of letters inside rows 16 to 23 of the
screen (the line's box is 8 rows tall; the letters of 48 stops fill rows
16 to 23, those of 13 stops rows 16 to 22 because nothing hangs below
the line, and those of five stops rows 17 to 23). It is centred on the
screen: the first and last columns of its letters add up to 255 (63 of
the 66 stops measured; 256 for two and 253 for one). The letters are drawn in
the palette's brightest colour (white), edged by one darker colour,
with the sprite layer; the page picture has no letters of its own.

**Which name.** Each stop has one name. Stops with the same name are:

- stop 1 and stop 234;
- stops 235 and 236;
- stops 238 to 243;
- stops 244 to 249;
- stops 250 to 255.

Every other stop measured has a name of its own (44 stops), so the 66
stops have 49 different names.

**Which frame.** On a frame on which the lead stands at a stop, the name
is that stop's. A walk is made of legs ("Walking a path"), and on each
frame of a leg the name is that of the point the leg ends at. A corner
has the name of a stop it lies next to (a corner whose record names that
stop as destination and as next point, `../formats/level-map.md`,
"Routes"). Corners 112 and 113 lie next to one stop each. The records of
corners 114 and 115 name both of their stops that way, and which of the
two gives the name could not be told from the data, so the table gives the
names as measured in this image:

| Corner | Name of |
|---|---|
| 112 | stop 238 |
| 113 | stop 234 |
| 114 | stop 12 |
| 115 | stop 244 |

So in a walk of one leg the name is the destination's from frame 0 of
the walk, and the departure stop's name is shown up to the frame before
it. In a walk of two legs, the first leg ends at a corner. If the corner has
the destination's name, the name changes on frame 0 (stop 22's walk to
stop 12); if it has the departure's name, the name changes on the first
frame of the second leg (the walk from 12 to 22). In a walk of three legs, through two
corners (the walks between stops 234 and 238), the first leg ends at the
corner next to the departure stop and the second at the corner next to the
destination, so the name is the departure's on the first leg, the
destination's from the first frame of the second leg, and the
destination's on the third. The name changes on no other frame of a walk. The
frame counts of "Walking a path" are used (the lag frames of "Pages"
aren't counted), and the picture of a frame is as defined in "Pictures".

When the map appears, the name is that of the stop the lead stands at,
and it is on every picture that isn't black ("When the map appears"),
including the first and the dimmed ones. It is part of the
picture, so it is black where the picture is black ("A page change").

### Example

Synthetic. Stop A (name `NA`) and stop B (name `NB`) are joined by a path
of two legs through a corner that lies next to B (so it has the name `NB`),
and each walk's first leg takes 65 frames (frames 0 to 64 of the walk). The lead
stands at A and walks to B with Right pressed on frame 500. Frames up to 499
show `NA`. Frame 500 is frame 0 of the walk: it and every frame after it show
`NB`, up to the last frame of the walk and while the lead stands at B. The lead then walks back
from B to A, pressed on frame 800. The frames up to 864 show `NB` (the first
leg ends at the corner, which is named `NB`), and from 865, the first frame of the
second leg, the name is `NA`.

### Measurements

Measured on screen in the first level's map, with the sprite layer alone
(a setup method was used for the start of each run; the lead stood at the
stop with every path to a neighbour open, and no modified copy of the game
was used). Frames are counted from the first frame of the run.

- The name of each of the 66 stops, with the lead standing at the stop, 120
  frames into the run. The name's letters were taken to be the pixels of
  the brightest colour in rows 12 to 28. The letters of 48 stops spanned
  rows 16 to 23, those of 13 stops rows 16 to 22 and those of five stops
  rows 17 to 23. The pictures of the letters were the same for stops 1 and
  234, 235 and 236, and the three groups of six listed above, and differed
  for all other pairs (49 different names). The letters' first and last
  columns added up to 255 for 63 stops, 256 for two and 253 for one.
- Every walk the map's data gives, 128 in all (every stop with each
  direction that has a neighbour), 130 frames into the run, with
  the direction pressed for 6 frames and the run going on until the lead
  stood still for 20 frames. On each of 11 350 pictures (the one for the
  frame before the press and those for frame 0 to the frame before the last of each walk; 56 more, in the 14 walks
  over a page edge, were black), the name on screen was the one the rule gives: 0
  differing. The six walks through a corner changed names where the rule says. Four
  had two legs (stops 22 and 12 both ways, 191 and 244 both ways): 22 to 12
  and 191 to 244 changed on frame 0, 12 to 22 and 244 to 191 on the first
  frame of the second leg. Two had three legs (234 to 238 and 238 to 234,
  through two corners): both changed once, on the first frame of the second
  leg. The first leg ended on frame 63 in the walk from 12 to 22 and on
  frame 64 in the other five, so the second leg's first frame was 64 in one
  walk and 65 in the others, as "Walking a path" gives.
- The first level's stop in a new game, and the walk to stop 12 that
  follows a first finish ("After finishing a level"), each played from a
  saved state (a setup method was used for the start) with no change made during the run: the name was stop 22's
  on every picture that wasn't black, and stop 12's from the first frame
  of the walk. With the second character on the map, the walk from stop 22
  (a setup method was used for the start; the path to stop 12 was open) showed the same names on the same frames.

## When the map appears

When the map appears (at the start, or after leaving a level), the lead
stands at its stop's appearance point (`../formats/level-map.md`), 8 px
to the right of the end of a path into that stop, with both fractions 0.
So the first leg of a walk can differ depending on whether the lead has
just arrived or the map has just appeared. In either case it starts
from where the lead stands.

The map fades in. No walk can start before 64 frames after the first
frame of the fade-in (measured when coming back to the map after
leaving a level, with a direction held all the time). A press at a stop
("Pressing at a stop") does nothing before then either, and isn't
remembered (measured when the map appears after a new game's press at
the link point, "A new game").

After a level is finished, the lead walks on to the next stop without
any input, by the rules above, starting from the appearance point with
fractions 0. When that walk starts, and what can stop or replace it, is
in "After finishing a level".

## Pressing at a stop

On a standing frame, a new press (held on this frame, not on the
previous one) of Jump, Action or Pause does what the stop's kind says
(`../formats/level-map.md`, "Kinds of stops"):

- At a **level stop** the lead enters the stop's level. The map fades
  out and the level begins (`level-start.md`). The fade-out is as in
  "Entering the level again" below. An entering press beats a direction
  with a path pressed or held on the same frame (measured at the first
  level's stop after its finish).
- At a **link point** the map fades out and appears again with the lead
  at another stop's appearance point, or a level begins. The timing is
  measured for the first level's link point ("A new game").
- At a **place** a screen that isn't a level opens. What it does is not
  covered. When the lead leaves it, the map appears with the lead at
  the place's appearance point ("When the map appears").

Tandem (`T`) and the pad's top face button (`controls.md`) do the same
as Jump here. A button that is kept held from before is not a new
press, and a press before the standing frames (during a walk, or before
the gates of "Pages", "When the map appears" and "After finishing a
level") does nothing and isn't remembered for later: on a walk from the
first level's Down neighbour into the first level's stop with last frame
`L`, Jump pressed for one frame on `L` − 20, `L` − 5, `L` − 1 or `L`
did nothing, and on `L` + 1 or `L` + 2 it entered the level on that
frame. Swap does nothing at a stop except replace the remembered press
("The remembered press").

## A new game

When a new game begins (after the title screens, which aren't covered),
the map appears with the lead at the **start point**
(`../formats/level-map.md`, "A new game"). In this section frames are
counted at 60 Hz from the map's first frame, frame 0, and none are left
out, as in "After finishing a level".

1. On frame 0 the lead stands at the start point's appearance point,
   with both fractions 0. The map fades in as in "After finishing a
   level" ("Up to the map"): brightness `j` on frames 2`j` and 2`j` +
   1, full from frame 32.
2. On frame 64 the lead starts walking, with no input, along the path
   to the start point's Right neighbour, the first level's **link
   point**, by the rules of "Walking a path". Nothing pressed or held
   from frame 0 on changes when it starts or how it goes. In `title-1`
   the walk is one leg, and its last frame is `L` = 127.
3. From `L` + 1 the lead stands at the link point. No direction has an
   open path there (`../formats/level-map.md`, "Progress"). Call **`P`**
   the first frame from `L` + 1 on with a new press of Jump, Action or
   Pause. Without one, the lead stays at the link point.
4. From frame `P` the map fades out: brightness 15 − `j` on frames `P`
   + 2`j` and `P` + 2`j` + 1, for `j` from 0 to 14. Frames `P` + 30 and
   `P` + 31 are black.
5. Frame **`W`** = `P` + 32 is the first frame of the map again ("When
   the map appears"), now with the lead at the first level's stop's
   appearance point, with both fractions 0. The map fades in as on
   frame 0: brightness `j` on frames `W` + 2`j` and `W` + 2`j` + 1, full
   from `W` + 32.
6. The first level's stop has no open path. From `W` + 64, a new press
   of Jump, Action or Pause enters the first level ("Pressing at a
   stop"): the map starts fading out on the frame of that press, as in
   "Entering the level again". Before `W` + 64 such a press does
   nothing.

### Example

Made up from the measured rules. Jump is pressed on frames 100 and 140
(one frame each), Down is held from 180 to 200, and Jump is pressed
again on frames 230 and 240.

| Frame | What happens |
|---|---|
| 0 to 31 | fade-in, the lead at the start point |
| 64 to 127 | the walk to the link point (the press on 100 does nothing) |
| 140 = `P` | Jump: the fade-out starts |
| 170, 171 | black |
| 172 = `W` | the map again, the lead at the first level's stop; fade-in |
| 180 to 200 | Down does nothing (no open path) |
| 230 | Jump does nothing: before `W` + 64 = 236 |
| 240 | Jump: the lead enters the first level; the map is black from 270 |

### Measurements

- 872 runs from the same new game, every one following these rules (0
  mismatches): 400 with 1 to 5 random presses or holds (a direction,
  Jump, Action, Pause, Swap, the other buttons, or a direction with
  Jump, Action or Pause; 1 to 60 frames long) starting between 20
  frames before frame 0 and frame 330, and 400 more starting up to
  frame 600; then 72 with one-frame presses of Jump, Action, Pause,
  Swap, Tandem and the top face button on `L` − 1 to `L` + 2,
  and on `W` + 62 to `W` + 65, there also with the button held from 5
  frames before. In 578 of them the lead was moved to the first level's
  stop, and in 122 it then entered the first level.
- The walk to the link point was compared frame by frame with "Walking
  a path".

## After finishing a level

What follows the last frame of a finished level (`level-end.md`).
Measured on the first level, finished for the first time and again.

In this section frames are counted at 60 Hz from the map's first frame,
frame 0, and none are left out: the lag frames on the map and the still
frames below are counted too.

### Up to the map

After the level's last frame the screen stays black. The map's first
frame came 49 frames after the level's last frame on a first finish, and
52 or 53 frames after it when the level had been finished before. Those
frames are part of no trace.

On frame 0 the lead stands at the finished level's stop, at its
appearance point, with both fractions 0 ("When the map appears"). The
map fades in: its brightness (on the scale of `level-end.md`, 15 full, 0
black) is `j` on frames 2`j` and 2`j` + 1, for `j` from 0 to 15, and
stays 15 from frame 32 on.

### Still frames

The first time a level is finished, frames 163 to 212 are **still**:
the picture doesn't change and nothing on the map happens. That is 50
frames. When the level had been finished before, there are no still
frames. The map's song is silent on all of them but the last, 212, which is the
frame it is first heard on (`music.md`).

### The gate

Call **`G`** the first frame on which a walk can start:

| The level | `G` |
|---|---|
| finished for the first time | 242 (192 frames that aren't still, then the 50 still ones) |
| finished before | 64 |

No walk starts before `G`, whatever is held or pressed, and nothing
pressed before `G` is remembered on `G` or after it (see below).

### The given direction

On frame `G` the remembered press ("The remembered press") becomes the
direction of the path from the finished level's stop to the next
level's stop, as if that direction had been newly pressed on `G`. It
replaces whatever was pressed on `G` itself, and it lasts, like any
remembered press, for frame `G` and the next 30 frames, unless a later
new press replaces it. For the first level, the next level's stop is
the stop that its first finish opens a path to: its Down neighbour
(`../formats/level-map.md`, "Progress"), so the given direction is
Down.

From `G` on, the frames are standing frames and the rules of "The
direction" apply. So:

- With nothing held, the walk starts on `G`.
- A direction held on `G` that comes before the given one in the order
  Right, Left, Up, Down and has no path blocks it. When that direction
  is released on frame `G` + `k`, the walk starts on that frame if `k`
  is 30 or less. Released later, the given direction has been forgotten
  and the lead stays at the stop.
- A new press of any button from `G` + 1 on replaces the given
  direction, as for any remembered press.

### Entering the level again

From `G` + 1 on, while the lead still stands at the finished level's
stop, a new press (held on this frame, not on the previous one) of
Jump, Action or Pause makes the lead enter that level again, instead of
any walk. This holds even when a
direction with a path is pressed or held on the same frame. Swap does
not enter: it only replaces the remembered press. On `G` a new press of
Jump, Action or Pause does nothing (the given direction replaces it),
and a button that is kept held from before is not a new press.

On the frame `P` of the entering press the map starts fading out: its
brightness is 15 − `j` on frames `P` + 2`j` and `P` + 2`j` + 1, for `j`
from 0 to 14, and the picture is black from `P` + 30. The level then
begins again (`level-start.md`); the time until its first frame is not
covered.

### Example

Made up from the measured rules. The level is finished for the first
time, so `G` = 242, and the given direction is Down. Right is held from
frame 220 to frame 250 and has no path from the stop.

| Frame | Held | What happens |
|---|---|---|
| 0 to 31 | | fade-in |
| 163 to 212 | | still |
| 220 to 241 | Right | nothing: before `G` |
| 242 = `G` | Right | Down is given; Right comes first and has no path: nothing |
| 243 to 250 | Right | nothing |
| 251 | | Down is still remembered (`G` + 9 ≤ `G` + 30): the walk starts |

If Right were held up to frame 271 instead, the walk would start on 272
(`G` + 30). Held up to 272, it would be released on 273 = `G` + 31,
when Down has been forgotten, and the lead would stay at the stop. If
Jump were newly pressed on frame 245, the lead would enter the level on
245 and the map would be black from 275.

### Measurements

- 1 073 runs, every one following these rules (0 mismatches). With
  random inputs on the map (1 to 5 times a direction, Jump, Action,
  Pause, Swap or a direction with one of them, held 1 to 60 frames,
  starting between 20 frames before frame 0 and 330 frames after it):
  400 with the first finish, 100 with two characters at the first
  finish, and 400 with the level counting as finished before. Then 168
  runs, half of each finish, with Right held from `G` − 30 to `G` + 40
  and a one-frame press of one button on `G` − 2 to `G` + 3 or `G` + 28
  to `G` + 35, and 5 more around `G`.
- Of those, 895 walks started and 100 entries; in the other 78 the lead
  stayed at the stop.
- One run finished the level a second time after entering it again
  from the map: the same timings as the runs counting the level as
  finished before (`G` = 64, no still frames, 52 frames from the
  level's last frame to the map).
- Two characters at the finish changed none of the timings.

## After a knock-out as the lead alone

What follows the last frame of a level in which the lead, alone, was
knocked out (`knock-out.md`). Measured on the first level, on a try that
left tries over and on one that left none.

Frames are counted at 60 Hz from the map's first frame, frame 0, and
none are left out, as in "After finishing a level".

### With a try left

After the level's last frame the screen stays black. The map's first
frame came 52 or 53 frames after it, as after a level finished before.
Those frames are part of no trace.

- **The lead.** On frame 0 it stands at the level's stop, at its
  appearance point, with both fractions 0 ("When the map appears"). The
  map fades in as in "Up to the map": brightness `j` on frames 2`j` and
  2`j` + 1, full from frame 32. The map shows no number of tries.
- **No still frames.** The frames are all like those of a level finished
  before.
- **Pictures.** The lead shows `map-lead-1` on frames 1 to 4 and goes
  on through the cycle as when the level had been finished before
  ("Pictures"). The pictures a first finish begins with are not shown.
- **No walk.** The lead doesn't walk, whatever is held. With nothing
  held it stayed where it was for over 600 frames, and with each of Up,
  Down, Left and Right held from frame 64 to frame 300 it stayed too.
  The level isn't counted as finished: no path from its stop is open, as
  at the first level's stop in a new game ("A new game", step 6).
- **Entering again.** From frame 64 on, a new press of a button that
  enters a level ("Pressing at a stop") makes the lead enter the level
  again. A press on frame 63 or earlier does nothing. On the frame of
  the press the map fades out as in "Entering the level again".
- **How the level begins.** Entered again with no input, the level began
  as when it is entered from a new game's map (`level-start.md`): the
  lead's position and action and the level's logic frames were the same
  on every frame compared. The level's song starts by the rule of
  `music.md`. The lives display shows the number of tries left after the
  loss (`count-display.md`, "When play starts").

### With no tries left

When the knocked-out lead had no try left, the map doesn't appear. A
**game-over picture** (the two characters, with a caption) appears
instead. Its first frame, frame 0, comes 41 frames after the level's
last frame, and it fades in with the same brightness steps as the map
("Up to the map"). Then the picture stays: nothing changed for 2 500
frames with nothing pressed.

The picture is the game-over picture of `../formats/level-map.md`
("The game-over picture"): 256 × 224 px, the whole screen, placed as the
map's picture is ("The map's picture"), its pixel column `c` and row `r`
at screen column `c` and row `r`. The two characters and the caption are
in it. Nothing else is drawn: no sprite, no other layer. Song 15 plays
while it shows (`music.md`).

A new press (of `T`, Jump or Pause; Action was not tried) on frame 1
or later starts a fade-out; call the frame of the press `P`. From frame
30 on the picture is at full brightness, and for a press on frame 30 or
later the fade-out is the one of "Entering the level again"
(brightness 15 − `j` on frames `P` + 2`j` and `P` + 2`j` + 1, black
from `P` + 30). A press during the fade-in starts the fade-out at the
brightness the fade-in had reached. For a press on frame 8, 16, 24 or
28 the picture is black from frame 2`P`; for a press on frame 1 it is
black on frame 3. A press on frame 0 does nothing.

Let `B` be the first frame on which the picture is black after the
press, as just given. After the fade-out the game leaves the map's
screens: the screen stays black until `B` + 65, and from `B` + 66
another screen fades in, taking 3 frames for each brightness step and at
full brightness from `B` + 110. The sound changes while the screen is
black (`music.md`): song 15 is cut on `B` + 1 or `B` + 2, and another
song starts on `B` + 59 or `B` + 60. What the other screen shows and
does, and which song it is, is not covered.

These no-tries-left rules were not checked where the last try was lost
in ordinary play; see "Measurements" and "Not covered".

### Example

The lead is knocked out as the lead alone with a try left, and the map
appears. Right is held from frame 64 to frame 70, and Jump is newly
pressed on frame 80.

| Frame | Held | What happens |
|---|---|---|
| 0 to 31 | | fade-in; the lead at the stop's appearance point |
| 64 to 70 | Right | nothing: no path |
| 80 | Jump (new press) | the lead enters the level again; fade-out starts |
| 110 | | black |

### Measurements

- Nine runs ended in a lone lead's knock-out, three from each of three
  places in the first level (seeded random walks), the lead having lost
  one try. In each, a second run was made with no tries left (a setup
  method was used to reach that).
- Frames from the level's last frame to frame 0: 53 in 7 runs and 52 in
  2; to the game-over picture, 41 in all 9.
- No walk: each of the four directions held from frame 64 to frame 300
  in all 9 runs, and nothing held for 640 frames: the lead stayed in
  every one.
- Entering: a press of `T` on frame 63 did nothing and one on frame 64
  entered in all 9 runs; one run each with a press on frame 30, 65, 66 and 80
  (nothing; entered, entered, entered).
- Pictures: three runs of 509 frames each, the same picture on every
  frame as in that cycle (0 mismatches, 0 mirrored).
- How the level begins: one run, compared on 700 video frames with
  entering from a new game's map, with no difference in the lead or the
  logic frames; the song start (`music.md`) was seen in 6 runs.
- No tries left: every run here was reached with a setup method, none by
  losing the last try in ordinary play. Nothing pressed for 2 500 frames
  (one run); a press of `T` on frame 0 did nothing and presses on frames
  1, 2, 8, 16, 24, 28, 30, 31, 40, 63, 64, 65 and 100 each started the
  fade-out (one run each); one run each with Jump and with Pause pressed
  on frame 305.
- After the fade-out: a press on each of frames 8, 16, 24, 28, 30, 31,
  63 and 100 (one run each), with the pictures taken to the other
  screen's full brightness: `B` as above in every run, the other
  screen's first picture 66 frames after `B` and full brightness 110
  after it. A press on frame 1 (one run): black on frame 3, the other
  screen's first picture on frame 69. The song's cut and start were
  also timed with presses on frames 1, 40 and 100 (one run each).

## The other character

When there are two characters, the one that isn't the lead (the
**other**) is on the map too. It never takes input. It walks the lead's
legs a little later, and stands a little to the side of the lead.

**When the map appears at a new game.** On frame 0 the other stands
16 px to the left of the lead (`x` less by 16) at the same height, with
both fractions 0.

**When the map appears after a level.** Where the map appears after a
level was finished (the lead then standing at the place "When the map
appears" gives), the other stands 16 px to the left of the lead at the
same height, with both fractions 0 as at a new game, on the first frame
on which both are there; this is the same as at a new game. It holds
whichever of the two characters led in the level. It was measured with
the map appearing after the first level with the first and with the
second character leading, and for 18 other stops, covering the whole
length of the map from its left end to its right end, with the first
character leading. For those 18 a setup method (research tools) was used so that the
level just finished counted as a different place. In every one the other was 16 px to
the left of the lead at the same height. Where it stands when the map
appears after a level was left in other ways is not covered.

**Legs.** The other walks each of the lead's legs ("Walking a path") by
the same rule as the lead, as a walker of its own with its own position
and fractions, but:

- It starts the leg 11 counted frames after the lead started it. The
  frame on which the lead's leg moves first is `a`; the other's leg
  moves first on frame `a` + 11.
- Its height end is the lead's height end for the leg: so its height on
  a frame is the lead's height 11 counted frames before.
- Its end across is the lead's whole-pixel `x` on the frame the leg
  ends (the frame on which both of the lead's axes have arrived), less
  24 if the lead's `x` on that frame is more than on the frame before
  the leg's first frame, and more by 24 if it is less. So when the
  lead has walked to the right, the other stands 24 px to its left, and
  when it has walked to the left, 24 px to its right. Here `x` is the
  position across the pages ("Pages"), so a leg to the right over a page
  edge counts as a leg to the right although the position on the page
  gets smaller. A leg to the left over a page edge (the lead's position
  on the page wraps from the page's left edge to its right edge during
  the leg) follows this only if the wrap comes before the other's leg
  starts to move: see "Legs over a page edge".
  Legs on which the lead's `x` doesn't change are not covered: see
  "Legs with little sideways change".
- Its speed across is 4 × (its end − its whole-pixel `x` at the leg's
  start) in steps of 1/256 px per frame, as for the lead; its fraction
  carries on from leg to leg and walk to walk like the lead's.

**Legs with little sideways change.** In the first level's map every leg
measured changed the lead's `x`, including the legs between places that
look straight above each other on the map: those moved it 16 to 24 px
sideways, and the other followed the rule above on them. No leg that
changes only the height was seen.

Every pair of neighbouring places in the first level's map was looked at
in the positions and neighbours of `../formats/level-map.md`. The legs
whose two end places lie at the same `x` are the two vertical pairs of
Run E; walked from where the lead stood, they moved it 16 to 24 px, as
said above. Three more legs were picked as ones that might leave the
lead's `x` unchanged when walked from a place where the map has just
appeared (the lead standing at the appearance point of "When the map
appears"): all three are walks upwards. Two of them were walked (Run H);
the lead's `x` changed on each, by 16 px to the right, and the other
followed the rule. Why these changed when the places' positions suggested
otherwise isn't explained. The third was walked only from its start place
(a setup method was used; Run I), not from the point where the map has
just appeared: it moved the lead 24 px to the right and the other
followed the rule. No leg with an unchanged `x` was found in the first
level's map, but this leg wasn't walked under the condition that made it
a candidate.

After its last leg ends the other stands at that spot until the lead
walks again. In the runs, each of the other's legs ended before the next
one began. Its end depends only on the lead's leg, whose end is fixed
when the lead's leg starts; so the other's whole leg can be worked out
on frame `a`, 11 frames before it moves.

**Page change.** The other is drawn on its own page ("Pages"). On the
frame `P` on which the lead's position passes onto the other page (the
frame before the lag frame in a walk to the right; in a walk to the
left, the first frame after the lead's position on the page has
wrapped from the page's left edge to its right edge), the other doesn't move if its leg is going on; from `P` + 1 the
leg goes on as before, one frame later than it would have. This was
measured over the boundary between the two pages of the map's first
screens, one walk to the right and two to the left.

### Example

Synthetic. The lead stands at `x` = 200, height 100, with the other at
`x` = 176 (the lead last walked to the right); both fractions are 0.
On frame 10 the lead starts a leg to (100, 60) with its speed across
−400 steps (−1.5625 px) per frame and up −160 steps (−0.625 px) per
frame; it arrives on frame 74. Its `x` on that frame is 100 (fraction
0.4375, 98.4375 put back to 100). The lead's `x` on frame 74 is less
than on frame 9, so the other's end across is 100 + 24 = 124. On frame 21 the other starts its leg: from (176,
100) to (124, 60), speed across 4 × (124 − 176) = −208 steps
(−0.8125 px) per frame. Its `x` after frame 21 is 175.1875, whole 175;
after frame 85, the 65th frame, 123.1875, which is less than 124: it
arrived, its whole pixel goes back to 124 with the fraction 0.1875 kept. On
every frame from 21 to 85 its height is the lead's height from 11 frames
before.

### Measurements

Measured in the first level's map with both characters, from the new
game's map state and by inputs alone (runs A–D; runs E and F below are set
up differently); which paths were open was arranged with a setup method
before the inputs began, not reached by play (as for the pictures). Every position is that of the character's
sprite on screen, in whole pixels, on every counted frame. Four runs,
each with the same first two legs (the first walk down from the start
point):

- Run A, 800 frames: that walk, then the walk back up. 2 walks, 4 legs.
- Run B, a chain of nine walks along the first level's neighbouring
  paths, one after the other, 1 500 frames: 9 walks, 13 legs, one walk
  across a page boundary to the right. No walk to the left over a
  boundary.
- Runs C and D: some of B's first walks, then one walk to the left
  over the page boundary that B crossed to the right.

Results:

- Runs A and B together hold 15 different legs in 10 different walks
  (the first walk is in both). In B the other moved on 833 frames and in
  A on 256, with 0 mismatches of its `x` against the rule above and 0
  of its height against the lead's 11 frames before, on and after
  the frame `P` of the walk across the boundary (with its one stalled
  frame). Those frames include the frames at rest up to 70 frames
  after each leg. 9 of the 15 legs went right, 6 went left.
- The other at rest: 24 px to the lead's left after each leg to the
  right, 24 px to its right after each leg to the left (all 15 legs of
  A and B). At the new game's map 16 px to its left.
- Run E: four legs, walked in both directions on the two pairs of
  places with the smallest sideways change (the lead's `x` changed by
  16 px on three of the legs and 24 px on one). The other's `x` and
  height matched the rule on every frame of each leg, 0 mismatches over
  64 frames per leg. Three legs started with the lead and the other
  placed on the map as at a new game, the other 16 px to the lead's
  left (a setup method was used); the
  fourth was walked after finishing the first level, where the map
  appears with the same 16 px gap. The other's end across depends only
  on the lead's leg, and its speed across followed the rule from the
  16 px start in every case, so the starting gap made no difference to
  the rule. One of the pairs was also walked there and back after
  finishing the level (the fourth leg and a repeat of another), 0
  mismatches.
- Run F: every pair of neighbouring places in the first level's map whose
  walk goes over a page edge, in both directions: 14 walks. Each was
  started by an input from the first place of the pair, after the lead
  and the other were placed with a setup method, the other once 16 px to
  the lead's left and once 24 px to its right; each walk was run from both starts (28 runs).
- Run G: two of run F's walks to the left, with the lead started at
  different places along the page (a setup method was used), so that
  its position wrapped on different frames: 24 walks, 16 of one pair
  and 8 of the other, with the other 24 px to the lead's right at the
  start.

- Run H: two walks upwards from the appearance point of a place, to a
  place whose position is 8 px to the right of that point (the lead and
  the other placed as at a new game; a setup method was used). The
  lead's `x` changed by 16 px to the right on both, the other came to
  rest 24 px to its left, and its `x` and height matched the rule on
  every frame, 0 mismatches over 64 frames per leg.

- Run I: the third candidate of "Legs with little sideways change",
  walked upwards from its start place (the lead and the other placed
  there; a setup method was used). The lead's `x` changed by 24 px to
  the right and its height by 88 px, the other came to rest 24 px to its
  left, and its `x` and height matched the rule on every frame, 0
  mismatches over 64 frames.

**Legs over a page edge** (runs B, C, D and F):

- Walks to the right over an edge (7 in run F, from both starts, and
  one in B): the other came to rest 24 px to the lead's left, as the
  rule says, in all 7 of F.
- Walks to the left over an edge (7 in run F, from both starts; one in
  each of C and D): the lead's position on the page wrapped during the
  leg in every one. In 6 of F's 7 the other came to rest on the lead's
  `x`, with no 24 added, from either start; in the seventh it came to
  rest 24 px to the lead's right, as in a leg not over an edge.
  What separates them is the frame of the wrap: the seventh wrapped
  while the other was still waiting to start.
- The frame of the wrap, `P` as in "Page change", against `a` + 11,
  the frame on which the other's leg first moves (all counted frames):
  in F, the seventh had `P` = `a` + 8 and the other six `P` between
  `a` + 16 and `a` + 42. In G, `P` took the values `a` + 2, 3, 4, 6, 7,
  8, 9, 10 (twice), 11 (twice), 12, 13 (twice), 15, 16, 17, 21, 27, 31,
  35, 37, 39 and 40. For every `P` up to `a` + 10 (9 walks) the other
  came to rest 24 px to the lead's right, and for every `P` from
  `a` + 11 on (15 walks) on the lead's `x`. So in a leg to the left
  over a page edge the other's end across is the lead's `x` itself if
  the wrap comes on the frame the other's leg first moves or later
  (`P` ≥ `a` + 11), and the lead's `x` plus 24, as for any leg to the
  left, if it came earlier.
- The speed across in C and D, with the end across taken as the lead's
  `x` itself and the stalled frame at `P` counted: 1 px per frame in
  one (the other's start 64 px from that end) and 1.375 px per frame in
  the other (88 px from it), each as the rule gives for its distance.
  The other's `x` matched on every frame of both runs (0 mismatches).

## Pictures

What the characters look like on the map in `title-1`: the lead, and the
other character when there are two. The pictures are named by their
picture hashes (`../formats/sprites.md`, "Picture hashes"). The picture
given for a frame is the one on screen with that frame's view.

| Picture | Picture hash (SHA-256) |
|---|---|
| `map-lead-1` | `d04c96c11202572daf17c9452bd108a3e913c1e7804a7d32e7465411e003ee5c` |
| `map-lead-2` | `624ce46eb019ebc2e0480359824a6c368de59a3b7f94ed322fccbd14e26ea43a` |
| `map-lead-3` | `1f44c8d65217e4e2dc61d784c748632ff32319e838f12245c8c02e5c31f96f14` |
| `map-lead-4` | `3be7370c1bb673ad00129964f14522db3e48859051c2011bcd4132f1cd51977a` |
| `map-lead-5` | `f3be04419a891ddd876982c456dd0ee7b324193099c1fd9ff18be3046e327a72` |
| `map-lead-6` | `d2f845667dfcb290cfeb5b52220b5252f3c4e27c81fd5756b0b786335e5457d8` |
| `map-lead-7` | `2d56620e6b88a64c10dca3394bd2f261cd5b102f0e4787fdebb0e813ec7bb1cb` |
| `map-lead-8` | `f3fda78649ddb25d4ecdd77c7eedd9409a76b78b1c35d009ad4828b7f6d62b4f` |
| `map-lead-9` | `90042a3a02f9905068dcf7d91449bc6b0fe88d72bed7f21328d32ab3edcb0c7e` |
| `map-lead-10` | `9f4d8a0c16a19596a82ce47e11cd7a0e9953e25efcc28a7048813905e521b0f2` |
| `map-other-1` | `70c4929658e8baba52ca2b02fc36903d1d0a61aa8f13e7bd5141a3b1e6d8e9cd` |
| `map-other-2` | `e4c158c7d2ae49f1cbe9ce97f0ee3a43c56afcf3831f9dfb570564f2bd6c404c` |
| `map-other-3` | `4a9176f66de94895fff1139c9cf21c12f8b28d4c87a2648f73799284880ab2c0` |
| `map-other-4` | `fb91e71d2e990b4e82fa5473a44c9fdc95f0098a051daaae0c031de291082b5f` |
| `map-other-5` | `feeff0479a169ea6a17b8cc582e2e405169b7fd3ae83121b1a71f91f2e4b5604` |
| `map-other-6` | `2350ffc0ea6ca2713f5e30229bf5f8564d9f91d0e8b4e6befc95c0919adc2153` |
| `map-other-7` | `612f58cf9802d54c9ffad81af186fe3a442f6c5bd1fbd00b6b27ebe79fae9e24` |
| `map-other-8` | `b266d2713e5a9834051e67e350f8ad988be5354c3cdc94a3ea5862cd51374f48` |
| `map-lead-finish-1` | `0e9a0893656a47577598fde6f06a4666700f6b32a1016873a358a4dc5f4e9e16` |
| `map-lead-finish-2` | `86b8a6f76a899398f2e756ba68bc86ea1ac9c82c01cfea90b9255bdbfa8be9f2` |
| `map-lead-finish-3` | `28e14d5f93ca9765f98e4b95fb518efb9b25162789944b06c030ff5d6fd33e60` |
| `map-lead-finish-4` | `b723e18c62d93dd163fe6dd826a5da9a24d8ed52170ca3176b9a6795fb705cdf` |
| `map-lead-finish-5` | `997ad879bf2543529ed8a18695f799c52a76f8039a7188c200edc1bfb56d8ba6` |
| `map-lead-finish-6` | `bf776bf48204dbb0db0750f6101e93159b01b836bed7c6e99848f0d40e6a8cbd` |
| `map-lead-finish-7` | `3b80bb3862c080c7294ca5eb6b9abd080c932b81c7ee24251d871a20038a1afe` |
| `map-lead-finish-8` | `b4db8907f2ce92edfbc370946fb64e69a93c0f13fb7fb4794d970d44ee3deff3` |
| `map-lead-finish-9` | `c86fcefcc1d39e3026e75442f797a1d0571ccf773bfccfbe32172205e6784d85` |
| `map-lead-finish-10` | `a0a19ca32fcb0b5cdfcf801de63ecf94cc9bb11c0ffdb54d1b70ea429b049105` |
| `map-lead-finish-11` | `e90170600442f1f7416f2b83ba5ad936fdda85c72bb0ffe9cbc7c15c5470a811` |
| `map-other-finish-1` | `0fd451df8a014bf791b370fdbddaf584d88bbb3e77d4eb6e7195b1301f5003e8` |
| `map-other-finish-2` | `8d67b9fa4dc5e17940cbf1437cad136a3d35f08d0a7974bacc1d7dd45c416cc4` |
| `map-other-finish-3` | `4378267b16e19877da0e85fb876d4a13bf3d683a2d96fc62343ee84a2e3cf11a` |
| `map-other-finish-4` | `9dcdd705577f69e9c458eb7dad5a10c5efdbd8d1ad64bfff43d39ab76f29858b` |
| `map-other-finish-5` | `0a94ed0c9589a8d4f618d62fd367dd836fc0178a3a5005248b69bc574f4d8212` |
| `map-other-finish-6` | `cf788812e5bc0a443a1cfeed3b93e14aa8aaeb039b4114708aabc1f0fe3faae4` |
| `map-other-finish-7` | `22955006d275fce3e605de48b8320c363ea6f50fca6c68c75af68401db6ca88a` |
| `map-other-finish-8` | `7198c9feb1ecc36540d7049c197f5ffeb19525e131f329a7c7f47137473bd6b4` |
| `map-other-finish-9` | `6fc8a0928158f4f206032ef1d24d41f008209bdc34bc76a8cc53123bd604c60d` |
| `map-other-finish-10` | `3a4a7b92a3413717ecb48f6a2a38fd09e733cdcce41e9e01a76d97ab327c6926` |
| `map-other-finish-11` | `b3fc77a98f178973358c5c5332af87ab937fff2bce63fb78fd8b8d5f9e7aee8d` |
| `map-start-1` | `ddc0355dffabe444a1f5b7c0c0293bcdfbdb6c1514341f3a116ad4faadd06753` |
| `map-start-2` | `e9e9e812ce528dd004336be296165e850051f4fc364cbee14e596921ad88022b` |
| `map-start-3` | `37df88582985aaa0efb966c94592cabcd621c174fda785c88bb8ee72e255ffc9` |
| `map-start-4` | `40c0273c31f3ff0630d1e88e9e6192d07f48f78ee5d3e8298136a22f24686b67` |
| `map-start-5` | `3260617f228e045afbaa9e3ff824062b0ee9efce102e752c52de06496dc73e6b` |
| `map-start-6` | `66c3ed8f19a6b86b094a7daba3885a26a47d8d9aa8a7b25208d77bfe98ff98e0` |
| `map-start-7` | `752b0759437f2e627a19eb5d92a8c42d166d25e5c09f25cb58620f8427bc571d` |
| `map-start-8` | `e04409d35f39a6ea7731cc010a708ec0ba02bd8ab04acad673cdb54811dfc870` |
| `map-start-9` | `2021ebbb70e00956135ed0df51ff0a7ef4b7604652f9dcfe531b5bbba719bc45` |

**Where.** A character's picture is drawn with its origin at (`x` − 1,
256 − `h`) in the picture, where `x` is the character's whole-pixel
position on its page (0 to 255, "Pages") and `h` its whole-pixel height.
It moves with the position, a pixel at a time, as the character walks.

**Which.** The lead shows `map-lead-1` to `map-lead-10` in turn, then
`map-lead-1` again, each picture on 4 counted frames in a row (lag frames
aren't counted, nor are the still frames after a first finish), so its cycle is 40 counted frames. The other character
shows `map-other-1` to `map-other-8` in turn, then `map-other-1` again,
each on 4 counted frames, so its cycle is 32. Both cycles go on all the
time from the frames given in "When the map appears" on: they don't depend on whether the character stands or walks, on
the walk's speed or direction, or on the character's position. While
both characters are on the lead's page and the lead hasn't changed page
since the map appeared, the two change their pictures on the same
frames. After the lead's page change, one run showed the other character
changing one frame later than the lead for the rest of the run, and what
decides that is not known ("Not covered").

**When the second character leads.** The pictures belong to the
characters, not to the lead's and the other's places: the names
`map-lead-1` to `map-lead-10` and `map-other-1` to `map-other-8` follow
the character (the first, the second), not whether it leads. When the
second character is the lead, it shows the eight pictures
`map-other-1` to `map-other-8` as above (cycle 32) and the other
character, the first, shows the ten pictures `map-lead-1` to
`map-lead-10` (cycle 40). Positions, legs, facing, page changes and
the timing of the picture changes follow the rules above unchanged, with
the second character as the lead.

**When the map appears.** Frames are numbered as in "A new game" and
"After finishing a level" (all frames, none left out): frame 0 is the
map's first frame, the one on which the fade-in starts, and the picture
is black on frames 0 and 1. When the map appears again after a new
game's press, frame 0 is `W`. "Counted frames" in "Which" are these
frames without the lag frames, and after a first finish also without the
still frames; none of the frames 1 to 161 were lag frames in the runs, so
the finish pictures below are given by frame number alone. The characters are on
the picture from frame 2. What a character shows first depends on how the
map appears:

- *When the level had been finished before, when the map appears
  again after a new game's first press* ("A new game"), *and after a
  lone lead's knock-out* ("After a knock-out as the lead alone"): the cycle of
  "Which" starts at frame 1 with its first picture, so `map-lead-1` is
  shown on frames 1 to 4 (seen on 2 to 4), `map-lead-2` on 5 to 8, and so
  on, and the other character's `map-other-1` is shown on the same frames
  when there are two. With the second character leading the same holds
  for its pictures, `map-other-1` first.
- *When a level is finished for the first time* (the case with 50 still
  frames, "Still frames"): from frame 1 to frame 161 the lead shows the
  pictures `map-lead-finish-1` to `map-lead-finish-11` (the second
  character, `map-other-finish-1` to `map-other-finish-11`), not those of
  "Which". For *i* from 0 to 77 it shows, on frames 1 + 2*i* and
  2 + 2*i*, picture *k* + 1 of them when *k* = *i* mod 14 is 0 to 7 and
  picture 15 − *k* when *k* is 8 to 13, so the pictures 1, 2 up to 8,
  7 down to 2, 1, 2 and so on follow one another, each on 2 frames.
  Picture 8 of the last run up (*i* = 77) is on frame 155 only; then
  `map-lead-finish-9` is shown on frames 156 and 157,
  `map-lead-finish-10` on 158 and 159 and `map-lead-finish-11` on 160 and
  161. On frame 162 the lead shows `map-lead-1`, and goes on showing it
  through the still frames (163 to 212) and on frames 213 to 215; its 4
  counted frames are 162 and 213 to 215, and `map-lead-2` is shown from
  frame 216 on, with the cycle of "Which" going on from there. With two
  characters the other character's pictures were found only on frames 158
  to 161, where they were its pictures 10 and 11 (the same numbers as the
  lead's); on the frames before, they were not found.
- *At a new game, before the first walk* ("A new game"): the lead shows
  nine pictures of their own, `map-start-1` to `map-start-9`, in turn
  and then `map-start-1` again, each on 4 counted frames from frame 1
  (`map-start-1` on frames 1 to 4, seen on 2 to 4). They go on while the
  lead walks to the link point and stands there, and through the
  fade-out that follows the press there ("A new game", step 4): every
  frame from the press `P` to `P` + 29 shows the next picture of the
  same cycle, with the same timing, and none is found on the two black
  frames `P` + 30 and `P` + 31. From `W` the map shows the cycle of
  "Which", from `map-lead-1`.

A character is not mirrored on any of these frames.

**Facing.** A character is drawn mirrored left–right (as
`../formats/sprites.md` says, "Mirroring") exactly when it faces left. It
faces left from the first frame on which its `x` is less than on the
frame before, and right from the first frame on which it is more; frames
on which `x` doesn't change leave the facing as it was, and a page
change (where `x` jumps from 255 to 0 or back) isn't a change of `x`.
In the runs, a new game's map showed the lead facing right until its
first move to the left, and the map after the first level's first finish
showed the lead, after it had walked on by itself, facing left (the
direction it had walked). The facing at the start of other maps, and
after a finish whose walk goes right, was not seen.

**Page changes.** On the pictures for frames `P` − 1 and `P`, for the
lag frame that follows `P` and for `P` + 1, where `P` is a frame on which
the lead's position passes onto another page ("Pages"), no lead is drawn.
The pictures for `P` − 2 and `P` + 2 show it as above, and its cycle goes
on through the four without a shift (the picture after the page change
is the one the cycle gives, counting the frames of the page change but
not the lag frame).

**The other character on the previous page.** After a walk to the right
over a page edge the other is still on the previous page for a time
("Page change"). It isn't drawn on the new page's screens until its
position on that page reaches 248, that is, until its origin ("Where") is
9 px left of the screen's edge: up to position 245 (12 px left of the
edge) nothing of it is in view, and the screens show the lead alone (and
no figure at all on the four screens of "Page changes"). From 248 it is
drawn at the screen's left edge as a picture cut off on the left, and
from the frame on which its position on the new page is 0 the whole
picture is on screen. A cut-off picture has no hash of its own: a hash is
given for a frame only when the whole picture is on screen. Measured in
one walk, over the edge between the first level's first two screens,
from the place before the edge to the place after it; a setup method
(research tools) was used. A walk to the left, which leaves the other on
the next page, wasn't measured with the two apart ("Not covered").

**When a hash isn't found.** The eighteen hashes of `map-lead-1` to
`map-lead-10` and `map-other-1` to `map-other-8` belong together: if
any of them isn't found, the engine shows its own art for the characters
on the map, on the same frames and with the same timing as given here.
The same holds for the other thirty-one ("When the map appears"),
`map-lead-finish-1` to `map-lead-finish-11`, `map-other-finish-1` to
`map-other-finish-11` and `map-start-1` to `map-start-9`: if any of the
pictures of one of these sets isn't found, the engine shows its own art
for the whole set, on the same frames; the other sets keep their
pictures.
The colours the pictures are drawn with are the level map's palette
(`../formats/sprites.md`, "Sprite palettes").

### Example

Synthetic. The lead stands at a stop facing right and shows
`map-lead-9` on frames 10 to 13, `map-lead-10` on 14 to 17 and
`map-lead-1` on 18 to 21. On frame 20 it starts a walk to the left (its
`x` is 1 less than on frame 19). Frames 18 and 19 show `map-lead-1` as
they were, facing right; frames 20 and 21 show `map-lead-1` mirrored
left–right, and 22 to 25 `map-lead-2` mirrored. When the walk ends at
frame 80 and the lead stands, it goes on showing the cycle's pictures
mirrored.

Synthetic. A level is finished for the first time. The picture is black
on frames 0 and 1, the lead shows `map-lead-finish-1` on frame 2 (as
on frame 1, which is black), `map-lead-finish-2` on 3 and 4, and so on up
to `map-lead-finish-8` on 15 and 16, then `map-lead-finish-7` on 17 and
18 down to `map-lead-finish-1` on 29 and 30 and `map-lead-finish-2` on 31
and 32. It shows `map-lead-finish-8` on 155 alone, `map-lead-finish-9` on
156 and 157, `map-lead-finish-10` on 158 and 159, `map-lead-finish-11` on
160 and 161, and `map-lead-1` on frames 162 to 215 (50 of them still),
then `map-lead-2` on 216 to 219.

### Measurements

Measured on screen in the first level's map (sprites only, as for the
player, `player-frames.md`). Each run started from a saved emulator state
on the map (at a new game, or shortly before the first level's end, from
which the finish and the lead's walk on were played by inputs) and went
on by inputs alone. Some runs needed more than that, and for those a setup
method (research tools) was used before the inputs began, so that the
lead stood where the run needed it and the paths not yet open were
open: the walks that needed paths not yet open (down to the second stop, with
one and with both characters), and the runs that put the lead beside a
page edge (the walk onto the next page, with one and with both
characters). No modified copy of the game was used. Five runs of 400 to
700 frames, a walk down to the second stop, the same walk with both
characters, a walk back up to the first stop, and a walk onto the next
page, with one and with both characters. The lead's picture was
looked for on every frame (2 500 pictures, 2 of them lag frames): 2 490
were the one given here, 8 were not found (the four page-change
pictures in each of the two runs that crossed a page, with nothing on
screen at the lead's position), and on 2 another
picture of a much larger kind fitted the screen as well. The other
character's picture was looked for on 1 200 pictures: 1 073 were as
given, 126 were not found and 1 was one of the large pictures again. Of
the 126, 86 were frames on which the two characters were less than 17
px apart horizontally and 10 px vertically, so the lead covered part of
the other's picture; the other 40 were frames on which the other was on
the page before the lead's (nothing was on screen at its position
within the lead's page). Facing agreed with the rule on every found picture
(3 563). Every picture of the lead
lasted 4 counted frames after the first, and so did the other
character's, with one exception: once, after the lead's page change, the
other character changed its picture one frame later than the lead and
kept that offset to the end of the run (over 400 frames).

Second character leading: a map reached from a level whose
second character led to the end was played down the same 1 500-frame
chain of walks as the first character's (nine walks over the
neighbouring paths, 13 legs). The other's `x` matched the rule on every
frame (0 mismatches over 1 486 frames, the one page-change lag frame
excepted as above). The pictures were looked for on 260 frames of a walk
down. The lead's were found on all 260, and every one was the picture
named above for its character. The other's were found on 163, again
every one the picture named above. Of the 97 frames where they were not
found, 13 were the video-lag frames at the start, and the other 84 were
frames on which the two figures stood 16 px or less apart with a height
difference under 24 px, so that one covered the other: on the 162
logic frames with the figures further apart the other's picture was
found on all 162, and on the 85 with them that close on 1. The paths not
yet open were made open by the setup method, as above.
Over the whole chain the lead's eight and the other's ten changed in
order, each on 4 counted frames (739 runs of 4 and three of other
lengths, at the start and at the page change).

Where the pictures start: the map was run from its first frame, with
the pictures found on screen as above, in 11 runs. A level finished for the
first time: the first character leading alone (1 run), with the second
character as the other (1), the second character leading with the first
as the other (1), and the first character leading alone at three other
stops than the first level's (3; the stop was made different by a setup
method, so these are not real finishes at those stops). The lead's pictures were found on all of
1 325 frames from frame 2 on and agreed with "When the map appears"
(frames 2 to 161 the eleven finish pictures, 162 on the cycle's); the
other character's were found on 58 of 221 frames with the first
character leading and on 2 of 239 with the second leading, never differing
from the rule (the rest were covered by the lead). Finished before
(made so by a setup method, which reproduced the second finish's gate
and absence of still frames): the first character alone (216 frames), with
the second as the other (77 frames; the other found on 39), the second
leading (145; the other found on 4): all agreed. New game: 250 frames of
the nine start pictures (frames 2 to 251), with the map shown again after
the press at the link point (76 frames of the cycle of "Which") all
agreed. No character was mirrored on any frame.

The new game's fade-out: 21 runs from the same new game, with a new
press of Jump (17 runs), Action (2) or another button (2) on the first
frame on which a press acts or on one of 1 to 39 frames later, each played
through the fade-out and the map's next appearance. The lead's picture
was found on all 3 516 frames from frame 2 to `P` + 29 in the runs, every
time the cycle's picture for its frame (0 mismatches), and on none of the
two black frames. Two more runs, with Jump pressed on the one and on the
two frames before the first acting one, did not start the fade-out (the
lead stayed at the link point), as "Pressing at a stop" says.

## Not covered

- Which stop gives a corner its name when its record names both stops
  as destination and next point (as the records of corners 114 and 115
  do), for an image other than this one; the letters of the names, and anything else drawn over the picture
  besides the two characters and the name (for example marks along
  open paths: none was seen in the measured maps). The name where a
  character's picture is in the same rows (a stop high on the map, or
  a walk past it), and the name in ordinary play for walks other than
  the first level's stop 22 to 12: the 128 walks of "The stop's name"
  were started from places set up by a setup method.
- Which of the two characters is in front where they overlap at other
  places than the first level's.
- The page change while the other character is on the previous page,
  with a second character walking: the fade was measured with the lead
  alone.
- Progress other than at a new game and after the first level's first
  finish (`../formats/level-map.md`, "Progress").
- The second character on the map: where it is when the map appears
  after a level left in other ways than finished; legs on which the
  lead's `x` doesn't change (none was found in the first level's map; the
  third candidate leg wasn't walked from where the map has just
  appeared); the other's stalled frame in walks over page edges other
  than the first level's first.
- Which pictures the characters show when the map appears after a first
  finish of a level other than the first, and after a level left in other
  ways than finished or a lone lead's knock-out; the other character's
  pictures on the first 157 frames of a first finish (it was covered by
  the lead); a new game with the
  second character; what decides how long the first finish's
  pictures last (they ran to frame 161 in every run).
- What moves the other character's cycle against the lead's at a page
  change.
- The other character's picture while it is on another page than the
  lead's after a walk to the left over a page edge.
- The facing at the start of other maps, and after a finish whose walk
  goes right.
- What follows a press at a place, at the start point, and at link
  points other than the first level's; the time from an entering press
  to the level's first frame.
- After finishing a level other than the first: which direction is
  given, and the timings.
- After a knock-out as the lead alone: the rules of "With no tries left"
  where the last try was lost in ordinary play; levels other than the first, a
  knock-out of the second character or with both there, a knock-out that
  loses the last try with a second character, what follows the game-over
  picture's fade-out beyond its first frames, presses on the other
  frames below 30 (2 to 7, 9 to 15, 17 to 23, 25 to 27 and 29), whether a first finish after such a knock-out
  still has its still frames, and the time from an entering press to the
  level's first frame.
- Opposite directions held together.
- Other titles.
