# Moving on the level map

Titles: `title-1`

Between levels the player chooses where to go on a **level map**. The
lead character (the **lead**) stands at a **stop** (a level or another place on the map); a
direction makes it **walk** along a path to another stop. This file says
how a walk moves, frame by frame, when a direction starts one, and where
the lead stands when the map appears.

Where the stops and paths are, which directions take which path, and
which paths are open come from the map's data and the player's progress.
They are not covered here yet; a later format file will give them in the
terms used below.

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
stands at the point of its stop with both fractions 0. This point can
differ from the end of a path into that stop, so the first leg of a walk
can differ depending on whether the lead has just arrived or the map has
just appeared. In either case it starts from where the lead stands.

The map fades in. No walk can start before 64 frames after the first
frame of the fade-in (measured when coming back to the map after
leaving a level, with a direction held all the time).

After a level is finished for the first time, the lead walks without any
input along the path that finishing opened, by the rules above,
starting from the stop's point with fractions 0. When this walk starts
is not covered.

## Not covered

- Where the stops and points are, which directions take which path, and
  which paths are open (the map's format, and progress).
- The second character on the map, and the figures' animation.
- Buttons other than the directions at a stop (entering a level, other
  menus), and when the walk after a first finish starts.
- Opposite directions held together.
- Other titles.
