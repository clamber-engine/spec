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
frames.

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

**Where.** A character's picture is drawn with its origin at (`x` − 1,
256 − `h`) in the picture, where `x` is the character's whole-pixel
position on its page (0 to 255, "Pages") and `h` its whole-pixel height.
It moves with the position, a pixel at a time, as the character walks.

**Which.** The lead shows `map-lead-1` to `map-lead-10` in turn, then
`map-lead-1` again, each picture on 4 counted frames in a row (lag frames
aren't counted), so its cycle is 40 counted frames. The other character
shows `map-other-1` to `map-other-8` in turn, then `map-other-1` again,
each on 4 counted frames, so its cycle is 32. Both cycles go on all the
time: they don't depend on whether the character stands or walks, on
the walk's speed or direction, or on the character's position. The two
characters change their pictures on the same frames.

**Facing.** A character is drawn mirrored left–right (as
`../formats/sprites.md` says, "Mirroring") exactly when it faces left. It
faces left from the first frame on which its `x` is less than on the
frame before, and right from the first frame on which it is more; frames
on which `x` doesn't change leave the facing as it was, and a page
change (where `x` jumps from 255 to 0 or back) isn't a change of `x`.
A new game starts with the lead facing right, and the lead that walked
on by itself after a finish stood facing the way it had walked.

**Page changes.** On the pictures for frames `P` − 1 and `P`, for the
lag frame that follows `P` and for `P` + 1, where `P` is a frame on which
the lead's position passes onto another page ("Pages"), no lead is drawn.
The pictures for `P` − 2 and `P` + 2 show it as above, and its cycle goes
on through the four without a shift (the picture after the page change
is the one the cycle gives, counting the frames of the page change but
not the lag frame).

**When a hash isn't found.** The eighteen hashes belong together: if
any of them isn't found, the engine shows its own art for the characters
on the map, on the same frames and with the same timing as given here.

### Example

Synthetic. The lead stands at a stop facing right and shows
`map-lead-9` on frames 10 to 13, `map-lead-10` on 14 to 17 and
`map-lead-1` on 18 to 21. On frame 20 it starts a walk to the left (its
`x` is 1 less than on frame 19). Frames 18 and 19 show `map-lead-1` as
they were, facing right; frames 20 and 21 show `map-lead-1` mirrored
left–right, and 22 to 25 `map-lead-2` mirrored. When the walk ends at
frame 80 and the lead stands, it goes on showing the cycle's pictures
mirrored.

### Measurements

Measured on screen in the first level's map (sprites only, as for the
player, `player-frames.md`), by inputs alone (with the lead's stop and
the open paths set up where a walk needed them): five runs of 400 to
700 frames, a walk down to the second stop, the same walk with both
characters, a walk back up to the first stop, and a walk onto the next
page, with one and with both characters. The lead's picture was
looked for on every frame (2 500 pictures, 2 of them lag frames): 2 490
were the one given here, 8 were not found (the four page-change
pictures in each of the two runs that crossed a page), and on 2 another
picture of a much larger kind fitted the screen as well. The other
character's picture was looked for on 1 200 pictures: 1 073 were as
given, 126 were not found and 1 was one of the large pictures again. Of
the 126, 86 were frames on which the two characters were less than 17
px apart horizontally and 10 px vertically, so the lead covered part of
the other's picture; the other 40 were frames on which the other was on
the page before the lead's (nothing was found at its position within
the lead's page). Facing agreed with the rule on every found picture
(3 563). Every picture of the lead
lasted 4 counted frames after the first, and so did the other
character's, with one exception: once, after the lead's page change, the
other character changed its picture one frame later than the lead and
kept that offset to the end of the run (over 400 frames).

## Not covered

- Progress other than at a new game and after the first level's first
  finish (`../formats/level-map.md`, "Progress").
- The second character on the map (where it walks, and where it is when the map appears), the pictures when the second character leads, which picture the cycles start with when the map appears, and what moves the other character's cycle against the lead's at a page change, and the other character's picture while it is on another page than the lead.
- What follows a press at a place, at the start point, and at link
  points other than the first level's; the time from an entering press
  to the level's first frame.
- After finishing a level other than the first: which direction is
  given, and the timings. What the still frames are for (the picture
  just stops).
- Opposite directions held together.
- Other titles.
