# Dimming near the first level's end

Titles: `title-1`

Near the right end of the first level the background's colours change,
step by step, to darker and bluer ones: the **dimming**. This file says
when it starts, how fast it goes and which colour every background
colour has at each step: the palette rows, the far layer, the backdrop,
the horizon layer and the sprites' palettes. Colours, palette rows and the palette set are
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
  the top of the backdrop, black lower down. The table is stored as a
  **night colour table** (`../formats/graphics.md`, "Night colour
  tables"); `../formats/locators.md` gives where the first level's starts.

The rows each entry covers don't change: the backdrop's frame-to-frame
alternation (`../formats/graphics.md`, "The backdrop") goes on as
before, with each entry's colour after the last step taken.

## Sprite colours

Some of the sprites' palettes (`../formats/sprites.md`, "Sprite
palettes") dim on the same cycle of step frames, but by a rule of their
own: the shared palette (`../formats/sprites.md`, "The other objects'
palettes"), the characters' palettes (`../formats/sprites.md`, "The
characters' palettes") and the animal's palette. Colour 0 is never drawn. Colours 1 to 15 of the
shared palette and 1 to 14 of each character's palettes and of the animal's change by the
table below, for steps 1 to 32 (the dimming's last step); a character's
colour 15 does not follow it (the first character's leading colour 15
changes more slowly; the other palettes' colour 15 is not covered). A
colour is only seen while a sprite using it is on the screen; steps 9 to
32 are covered for the colours that stayed on the screen (see
"Evidence"), and pixels of earlier steps' colours also showed at some
steps (see "Not covered").

**The characters' palettes.** Both characters' palettes dim, in both
roles, by the same table: the player's leading palette and the other
character's following palette. The second character's colours do not stay
at their daytime colours while the first character's go to night. What
the screen showed:

| Palette | Steps covered |
|---|---|
| First character's leading | 1 to 8, then 9 to 32 for the colours that stayed |
| Second character's following (first character the player) | 1 to 32, the characters standing and in motion |
| Second character's leading (second character the player since before the dimming) | 1 to 32, the characters standing |
| First character's following (same run) | 1 to 32, the characters standing |

**The animal's palette.** The animal's colours dim too, by the table
of the characters' palettes, in the runs listed below (all set-up runs;
see "Evidence"): the animal did not keep its daytime colours when the
level dimmed. This was seen for the animal waiting for the
first character after its crate is broken, for the animal ridden by the
first character (leading, the second character following) and for the
animal ridden by the second character (leading, the first character
following). While an animal is ridden, the character sitting on it dims
by the same table as when it is not riding. What the screen showed:

| Palette | Steps covered |
|---|---|
| The animal's, waiting (12 colours) | 1 to 32, 372 of 384 colour-steps (the other 12: see "Not covered") |
| The animal's, ridden by the first character (10 colours) | 1 to 32, all 320 |
| The animal's, ridden by the second character (10 colours) | 1 to 32, all 320 |
| First character's leading, while riding (10 colours) | 1 to 32, all 320 |
| Second character's leading, while riding (7 colours) | 1 to 32, all 224 |

**Colours.** Take a colour of one of those palettes (not the first
character's colour 15, nor the animal's colour 15, which is not covered) with red `R`, green `G` and blue `B` (0 to 31
each) before step 1. After step `k` (`k` = 0 before step 1, up to 32; the
steps are those of rows 1 to 7) the colour shows

| Channel | Value after step `k` |
|---|---|
| Red | ⌊`R` − `k` × `R` / 64⌋ |
| Green | ⌊`G` − `k` × ⌊`G` / 2⌋ / 32⌋ |
| Blue | ⌊`B` + `k` × (`G` − `B`) / 64⌋ |

with ⌊ ⌋ rounding down. So red loses `R` / 64 per step, green ⌊`G` / 2⌋
/ 32 per step, and blue moves towards `G` by (`G` − `B`) / 64 per step; a
colour whose blue is already equal to its green keeps its blue. A colour
with `G` < `B` is not covered.

**Pictures.** A step's colours show on the pictures of the shared
palette from the picture of the step frame on, and on about half of that
palette's pixels already on the picture of the frame before. The first
character's colours show, at the latest, from the picture three frames
after the step frame; where between is not covered. The second
character's following palette (first character the player) shows a step
on one picture, all its pixels at once, and never earlier: the picture of
the step frame + 2; up to the picture before that it shows the colours of
the step before (16 runs, steps 1 to 8; see "Evidence"). The second
character's leading palette and the first character's following palette
(second character the player) were seen in one run, with the characters
standing: the step showed on most of the palette's pixels from the
picture of the step frame + 1 (leading) and + 2 (following), and before
that the colours of the step before showed on most of them; some pixels of
the other kind were seen on the pictures around, not explained (see
"Evidence"). Tests check the timing only for the following palette of
the first case.

Most of the animal's colour-steps (about two thirds in the riding
runs, more than four fifths for the waiting animal) first showed on the picture of
the step frame + 4, all of that palette's pixels at once, and up to the
picture before that showed the colours of the step before. The other
colour-steps showed on other pictures or not at all, and earlier steps'
colours showed in most windows (see "Evidence"); these are not explained,
and tests do not check the animal's timing.

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

A sprite colour with `R` = 20, `G` = 12, `B` = 6: after step 1 (⌊20 −
20/64⌋, ⌊12 − 6/32⌋, ⌊6 + 6/64⌋) = (19, 11, 6); after step 8 (17, 10,
6). A white (31, 31, 31): after step 1 (30, 30, 31), after step 8 (27,
27, 31).

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
  rows 1 to 7;
- that the colours of the shared palette and of the first character's
  leading palette, on every picture from three frames after a step
  frame, up to the 8th step, are those of "Sprite colours" for the last
  step taken, except the first character's colour 15. (Past the 8th
  step, pictures showed earlier steps' colours too, see "Not covered",
  so tests check only the table's colours showing, not the others'
  absence.)
- that the second character's following palette (first character the
  player), and the second character's leading and the first character's
  following palettes (second character the player since before the
  dimming), show the colours of "Sprite colours" for the last step taken,
  except colour 15; the timing of the step on the pictures only for the
  first of the three (see "Pictures" above). The same
  caution about earlier steps' colours past the 8th step applies.

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

Sprite colours: the screen with only the sprites drawn, in 16 runs of
the route used for the rows above, waiting 0 to 15 frames so that the
steps fell on all 8 positions of the cycle, with the first character
leading and alone; 140 to 156 pictures per run, 8 to 10 steps each, of
which steps 1 to 8 were used. For each step the window is the pictures
from three frames after the step frame to the frame before the next step
frame: 5 pictures, 640 in all, but 628 because in 8 runs (those with
exactly 8 steps) the log ends 2 to 5 pictures after step 8's window
began, so that window is shorter (by 3, 2, 1 and 0 pictures, in two
runs each). The pairs are a step and a colour (not the first character's
colour 15) that was on the screen before step 1: 2 688; the colour the
table gives for that step showed in the window in 2 576. In the other
112 no picture of the window showed that colour, nor the colour before
step 1 or one the table gives for an earlier step (one colour with a
constant 256 or 512 pixels on every picture, from a sprite that did not
change, was left out). Of those 112, 56 are colours that showed in the
table's colour in the window of another step in the same run, and 56
never did after step 1: no pixel of those colours, in any form, showed
in those windows (whether the sprites using them were off the screen or
drawn without them is not covered). On the pictures of the windows no pixel showed a colour
that the table gives for an earlier step only. At step 1, on the
picture of the step frame and later ones, no pixel of the shared
palette's earlier colours showed, in any run; on the picture of the
frame before, 8 089 of
the 16 759 pixels of the shared palette's earlier colours (16 runs
together) already showed the new ones.

Steps 9 to 32: two of those runs went on to the last step, waiting 0 and
15 frames, with the same pictures and windows (the last window of the
second run has 3 pictures, as its log ends 3 pictures after step 32's
window began; the first run's has 5). The pairs are a step from 9 to 32
and a colour (not the first character's colour 15) that was on the screen
before step 1: 21 colours, so 504 per run, 1 008 in all, on 238 pictures.
The table's colour for the step showed in the window in 380 (first run)
and 374 (second run) of them. Of the others, 6 colours per run stopped
showing for good from some step on, and no earlier or later table colour
of theirs showed either: in the first run one colour first missing at
step 9 and five at step 13, in the second one at step 9 and five at
step 12 (24 + 5 × 20 = 124 and 24 + 5 × 21 = 129 pairs); a sprite using
them leaving the screen is the likely cause, not established. In the
second run one more pair is missing: another colour, one of those that
stayed, at step 26 only (shown at steps 25 and 27; 129 + 1 = 130). So 15
colours showed the table's colour at every step from 9 to 32 in the
first run and 14 in the second. The
view stopped moving from step 25 on in both runs (its right limit), and
the sprites' colours still followed the table up to step 32.

Second character's palettes: the screen with only the sprites drawn.
Following: 16 runs of the two characters walking the end of the first
level together (the second following the first, jumping and standing),
waiting 0 to 15 frames so that T fell on all positions of the cycle; the
palette's 14 colours (not colour 15) on the screen before step 1, 32
steps, windows as above (3 frames after the step frame to the frame
before the next step frame; step 32's window is missing in 5 runs, whose
log ended on it, so 7 168 pairs less 70): 7 098 pairs of a step and a colour on 2 515 pictures
(16 × 32 windows of 5 pictures is 2 560, less 25 for the 5 missing windows,
less 20 because in 8 other runs the log ends 1 to 4 pictures into step
32's window, so that window is shorter by 4, 3, 2 and 1 pictures, two
runs each; those windows still count their pairs),
the table's colour showed in the window in 7 091. The other 7 are one
colour at step 29 in 7 of the runs (it showed at steps 28 and 30). On
the pictures, with `F` the step frame: for steps 1 to 8 (128 pictures
per frame in the 16 runs) the pictures of `F` − 1, `F` and `F` + 1 showed
only the colours of the step before (18 936 to 19 216 pixels per frame,
none of the new colours), and those of `F` + 2 to `F` + 7 only the new
ones (18 748 to 19 486 pixels, none of the old); for step 1 alone the
same, with 9 454 to 9 608 and 9 302 to 9 747 pixels. Pixels of colours
the table gives only for earlier steps showed in the windows of steps 4
to 6, 12 to 16 and 18 to 20 (256 to 1 280 pixels per picture; the same
in all runs), as in the paragraph above for the other palettes.
Leading: one run with a change of player, made while the characters
stood still and before the dimming, then 360 frames standing (so steps 1
to 32 and the window of each, 160 pictures): the second character's
leading palette, 14 colours, 448 pairs, all showed the table's colour
in the window; the first character's following palette, 13 colours, 416
pairs, all showed it. Pictures, steps 1 to 8 (8 pictures per frame):
the second character's leading palette showed mostly the step before's
colours on `F` − 2 to `F` (1 835 to 1 965 pixels per frame), and the new
ones on `F` + 1 to `F` + 6 (2 103 to 2 310 pixels; 55 to 60 pixels of
the old ones remained from `F` + 2 on, and 256 pixels of the new ones
showed on `F` − 2 to `F`, neither explained); the first character's
following palette showed mostly the step before's colours on `F` − 1 to
`F` + 1 (509 to 517 pixels; 108 and 116 pixels of the new ones on the
first two, not explained) and mostly the new ones on `F` + 2 to `F` + 7
(715 to 764 pixels). In both runs pixels of
earlier steps' colours showed in some windows (steps 4 to 6, 28 and 29;
steps 10, 11, 15 to 17).

The animal's palette: three runs of 420 logic frames each, all set up
(a setup method was used; the rule was not checked in ordinary play, for
example an animal reached and ridden normally while the level dims). The crate (`animal-crate.md`) was
broken before the observed part began, at a place near the end of the
first level that is not where the first level has its own crate; the
arrangement is a limit of the sample, like the rule's not having been
checked in ordinary play. The observed part began with the characters on
the final stretch and all 32 steps still to come, in three kinds of run:
the animal waiting beside the characters after its crate was broken and
not mounted (the animal 12 colours), the first character already riding
it (the animal 10 colours, the character on it 10) and the second
character, leading, already riding it (the animal 10 colours, the
character on it 7). Per step the window is the 6 pictures from the step
frame + 6 to the step frame + 11. The pairs are a step and a colour of the
palette (not colour 15, and not a colour equal to colour 0) that was on
the screen before step 1: 384 for the waiting animal, 320 for each
animal ridden and for the first character on it, 224 for the second
character on it, 1 568 in all; the colour the table gives showed in the
window in 1 556. The 12 that did not were all of the waiting animal: one
colour at steps 2 to 10 and another at 3 steps, which showed in the
colour the table gives at other steps. The picture on which each step
first showed, for the animal's palette: the colour-steps whose colour
differs from the step before's and from every other palette's colours
were 186 in each run; in the first riding run 123 first showed on the
picture of the step frame + 4, of which 121 no longer showed the step
before's colour from that picture and 2 still showed it on a picture up
to the step frame + 7; in the second riding run 120 (118 and 2); for the
waiting animal 161 (158 and 3). The others (63, 66 and 25) first showed
on pictures from 2 before the step frame to 1 or 6 after it, or at all
only in the step before's colour, or not at all in the pictures from 2
before to 7 after the step frame; not explained. Pixels of earlier
steps' colours showed in the windows of most steps from the second on,
in the same amounts in the three runs (for example 6 144 pixels on the
6 pictures of step 8 and 21 504 of step 23); not explained.

## Not covered

- The waiting animal's 12 colour-steps that did not show the table's
  colour (one colour at steps 2 to 10 and another at 3 steps; they showed
  in the table's colour at other steps): "by the table" holds for the
  waiting animal except for those.
- Sprite colours of other palettes than those in "Sprite colours" (the
  thrown objects' and the others; the metal barrel, which is drawn with
  the animal's palette, was not looked at), the animal's colour 15, the
  animal ridden or waiting while the characters are in motion during the
  dimming or the roles change during it, the characters' palettes with
  the second character leading in motion, with the characters' roles changed during the dimming (in
  one run with a change of player at step 13, the pictures after it
  showed the colours of the same two palettes as before it, in dimmed
  colours, and no daytime colours; which character each was drawn
  with is not covered), or for a colour with green below blue; the
  characters' colour 15 (the first character's leading one changes more
  slowly than the table says; the other palettes' is not covered);
  the colours after the last step; the pictures that use a copy of the
  first character's palette; on which step frame each palette changes, to
  the frame; why the 6 colours per run stop showing; why, at
  steps 10, 11, 13 and 15 to 18, pixels of colours the table gives only
  for earlier steps showed on every picture of the window (768, 1 280,
  256, 512, 768, 1 280 and 768 pixels per picture, the same in both runs;
  at no other step from 9 to 32), nor which sprites they belong to.
- On which picture a step first shows, to the frame (except for the second
  character's following palette, see "Pictures" under "Sprite colours"). In the captured
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
