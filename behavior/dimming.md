# Dimming near the first level's end

Titles: `title-1`

Near the right end of the first level the background's colours change,
step by step, to darker and bluer ones: the **dimming**. This file says
when it starts, how fast it goes and which colour every background
colour has at each step: the palette rows, the far layer, the backdrop
and the horizon layer. Colours, palette rows and the palette set are
as in `../formats/graphics.md`; the placement list, its classes and the
window are as in `../formats/placements.md`.

Frames are logic frames at 60 Hz, numbered as in a trace.

## Start

The dimming is started by the first level's class 8 entry. Call **T**
the frame on which that entry is first in the window, by the same rule
as for a class 1 entry ("When placed objects appear"): the first frame
that starts with the view's left edge `V` (rounded down to a whole
pixel) at most 288 px left of the entry's `x`. In the first level that
is the first frame that starts with `V` at 4 704 or more.

Nothing changes on T itself. The dimming then always runs to the end,
whatever happens next: the view moving back left so that the entry
leaves the window, standing still, or the player falling out
(`falling-out.md`) don't stop it or undo it. It goes on until the
level's last frame.

## Steps

The dimming has **32 steps**, numbered 1 to 32, one every 8 frames. The
steps fall on a repeating cycle of 8 frames, the same cycle all through
the dimming: every eighth frame is a **step frame**. Step 1 is on the
first step frame that is T + 4 or later, so on one of T + 4 to T + 11;
step `k` is on that frame + 8 (`k` − 1). After step 32 the colours stay
as they are until the level ends.

Where the cycle is placed is **not specified**. It isn't tied to T:
across 16 runs whose T fell on every one of the 8 positions in the
cycle, step 1 came 4, 5, … or 11 frames after T, always on the same
cycle. An engine may place the cycle anywhere, as long as it keeps it
through the dimming.

A step's colours are already the colours of its frame's picture. On
frames between steps, colours are as after the last step (or, before
step 1, the palette set's colours).

## Colours at each step

Only rows 1 to 7, the background's rows, are covered here. Colour 0 of
a row is never drawn (`../formats/graphics.md`), so only colours 1 to
15 of each row matter.

Each colour changes on its own, from its own palette set value. Take a
colour whose palette set value has red `R`, green `G` and blue `B` (0 to
31 each). Call `M` = ⌊5`G` / 8⌋. After step `k` (`k` = 0 before step 1)
the colour shows

| Channel | Value after step `k` |
|---|---|
| Red | ⌊`R` − `k` × ⌊11`R` / 2⌋ / 256⌋ |
| Green | ⌊`G` + `k` × (`M` − `G`) / 32⌋ |
| Blue | ⌊`B` + `k` × (`M` − `B`) / 32⌋ |

with ⌊ ⌋ rounding down (towards minus infinity, also for green or blue
values on their way down). So:

- green goes in equal steps from `G` to `M`, about 5/8 of itself;
- blue goes in equal steps from `B` to the same `M`: blue ends equal to
  green, whatever it was, so blue can go up as well as down;
- red goes down by ⌊11`R` / 2⌋ / 256 on each step. That is a little less
  than (`R` − 5`R` / 16) / 32, so red ends at ⌊`R` − 32 × ⌊11`R` / 2⌋ /
  256⌋, which can be 1 more than ⌊5`R` / 16⌋ (for example `R` = 3 ends at
  1, not 0).

After step 32 the colour is (red as above, `M`, `M`).

## The far layer

In the first level every far-layer pixel has a colour of rows 1 to 7
(`../formats/graphics.md`, "The layers"), so the far layer dims with
them: a far-layer pixel shows its palette colour as in "Colours at each
step" for the last step taken, then made darker as in
`../formats/graphics.md` ("The far layer's colours"). With `r`, `g`, `b` the colour after step `k`, the pixel shows
⌊max(0, `r` − 4) / 2⌋, ⌊max(0, `g` − 4) / 2⌋, ⌊max(0, `b` − 4) / 2⌋.

## The backdrop and the horizon layer

The colours of the backdrop's and the horizon layer's row-colour lists
(`../formats/graphics.md`, "Row-colour lists") dim too, on the same
step frames as rows 1 to 7, but by another rule. Each list entry's
colour goes in 32 equal steps from its own colour to a **night
colour**. Take an entry whose colour has red, green and blue `R`, `G`
and `B` and whose night colour has `Rn`, `Gn` and `Bn`. After step `k`
(`k` = 0 before step 1) the entry's rows show

| Channel | Value after step `k` |
|---|---|
| Red | ⌊`R` + `k` × (`Rn` − `R`) / 32⌋ |
| Green | ⌊`G` + `k` × (`Gn` − `G`) / 32⌋ |
| Blue | ⌊`B` + `k` × (`Bn` − `B`) / 32⌋ |

with ⌊ ⌋ rounding down as above. After step 32 the entry shows its night
colour exactly.

- **Horizon layer:** every entry's night colour is black (0, 0, 0). This
  is the colour of the horizon layer's colour number 1; the first
  level's horizon map uses only colour numbers 0 and 1.
- **Backdrop:** the first level's backdrop list has a night colour
  table: its first 71 entries (entries 0 to 70, entry 0 the first) each
  have their own night colour, and every later entry's night colour is
  black. In the first level every night colour has red 0 and green 0,
  and blue 4 or less, never more than the entry before's: a dark blue at
  the top of the backdrop, black lower down. Where the night colour
  table is stored is not covered yet.

The rows each entry covers don't change: the backdrop's frame-to-frame
alternation (`../formats/graphics.md`, "The backdrop") goes on as
before, with each entry's colour after the last step taken.

## Example (made-up colours)

A colour with `R` = 20, `G` = 12, `B` = 6: `M` = ⌊60 / 8⌋ = 7, and red
goes down by ⌊220 / 2⌋ / 256 = 110 / 256 each step.

| After step | Red | Green | Blue |
|---|---|---|---|
| 0 | 20 | 12 | 6 |
| 1 | ⌊20 − 110/256⌋ = 19 | ⌊12 − 5/32⌋ = 11 | ⌊6 + 1/32⌋ = 6 |
| 2 | 19 | 11 | 6 |
| 16 | ⌊20 − 1 760/256⌋ = 13 | ⌊12 − 80/32⌋ = 9 | ⌊6 + 16/32⌋ = 6 |
| 31 | 6 | 7 | 6 |
| 32 | ⌊20 − 3 520/256⌋ = 6 | 7 | 7 |

A colour with `R` = 3, `G` = 25, `B` = 28: `M` = 15, red goes down by
16 / 256 a step: after step 1 (2, 24, 27), after step 16 (2, 20, 21),
after step 32 (1, 15, 15).

A list entry with colour (20, 12, 6) and night colour (0, 0, 3): after
step 1 (⌊20 − 20/32⌋, ⌊12 − 12/32⌋, ⌊6 − 3/32⌋) = (19, 11, 5), after
step 2 (18, 11, 5), after step 16 (10, 6, 4), after steps 31 and 32
(0, 0, 3).

A far-layer pixel whose palette colour is (20, 12, 6), as in the first
table: (8, 4, 1) before step 1, (7, 3, 1) after step 1 (from (19, 11,
6)), (4, 2, 1) after step 16 (from (13, 9, 6)), (1, 1, 1) after step 32
(from (6, 7, 7)).

Timing, with the cycle placed (as an engine may choose) so that frames
1 003, 1 011, 1 019, … are step frames: if T is frame 1 000, step 1 is
on 1 011 (1 003 is before T + 4) and step 32 on 1 011 + 248 = 1 259. If
T is frame 999, step 1 is on 1 003 (T + 4).

## A scenario may check

- that the background colours are the palette set's colours on every
  frame up to T + 3;
- that step 1 is on one of T + 4 to T + 11, the steps 8 frames apart,
  and the colours on every frame as in "Colours at each step" for the
  last step taken;
- that nothing undoes the dimming before the level ends;
- that the far layer, the backdrop and the horizon layer show, on every
  frame, the colours of "The far layer" and "The backdrop and the
  horizon layer" for the last step taken, with the same step frames as
  rows 1 to 7.

It may not check which of the 8 placements of the cycle is used, beyond
step 1 being on T + 4 to T + 11.

## In a trace

Nothing in a trace shows colours. The dimming gives no event and
changes no field.

## Evidence

Colour memory read after every frame in 22 runs past the start, all
with one character: 16 waiting 0 to 15 frames before the same route to
the level's end, so that T fell on every position of the cycle; two
with the class 8 entry moved, in a copy of the image, 42 px left and
18 px right, which moved T by the view as the rule says; two turning
back left at steps 2 and 17 (the view went back far enough for the
entry to leave the window, and the dimming went on); one standing
still from step 7; one walking into a pit and falling out during the dimming. Colours
1 to 15 of rows 1 to 7 on every logic frame, 585 585 colour values, all
as the table says. The colours of the six saved positions near the
level's end in `../formats/graphics.md` are steps 26, 31 and 32 (four
times) of the table. The picture of a step frame was checked to show
that step's colours.

Sky pictures: 16 runs of one route to the level's end, waiting 0 to 15
frames before the dimming starts so that T fell on all 8 positions of
the cycle. In each, the picture of every frame from at least 58 frames
before T to the start of the level's end fade was captured with only the
backdrop, the far layer and the horizon layer drawn, and each pixel was
paired with the list entry or the palette colour it comes from. Step
frames of the lists were the step frames of rows 1 to 7 in all 16 runs.
Of 8 367 501 horizon pixels, none differed from the rules above. Of
373 171 411 far-layer pixels, none differed once each step is taken to
show one picture earlier (see "Not covered"). Of 169 951 759 backdrop
pixels, 1 131 differed, on 36 pictures, all in the list's lowest
coloured rows (entries 87 to 97), and each of them already showed the
next step's colour on the picture of the frame before a step frame.

## Not covered

- Sprite colours. They change too during the dimming, on frames of the
  same cycle, but not as the table says.
- Where the backdrop's night colour table is stored in the image, and
  its colours.
- On which picture a step first shows, to the frame. In the captured
  pictures, the far layer's colours of a step already showed on the
  picture of the frame before the step frame, the ground layer's colours
  partly, and the backdrop's lowest rows sometimes (Evidence); the
  backdrop and the horizon layer otherwise showed a step from the step
  frame's picture on. These pictures were taken in an emulator, and
  colour changes during a picture may differ on the console.
- Horizon colour numbers 2 and 3 during the dimming (the first level
  doesn't show them).
- What class 8 entries in other levels do, and what a class 8 entry's
  `h` and reference mean.
- The view coming back into the window after the entry has left it.
- Frames after the level's last frame, and the colours when the level
  is started again.
- Two characters: two saved positions with both were at steps of the
  table, but no run with two characters was checked frame by frame.
