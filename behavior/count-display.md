# The count display

Titles: `title-1`

While a level is played, the **count display** at the top left of the
picture shows the collectible count (`collectibles.md`) for a while
after the player takes a collectible. It is not shown otherwise. When
the count goes from 99 to 0 and the player gets one more life, the
number of lives is also shown for a while at the top right, and it is
shown when the player loses a life by being knocked out or falling out
of the level, and each time play starts: on a level's first frame and on
the first frame after a restart. This file covers what the two displays
show, where, on which frames, and the pictures they are drawn with.

Frames are logic frames at 60 Hz, numbered as in a trace. Positions are
in px in the picture, from its top-left corner (the picture is
256 × 224 px, `view.md`, `view-vertical.md`). As in `letters.md`, the
picture **made on frame `n`** is the one drawn from the state at the
end of frame `n`, shown once frame `n` is over; "shown on `n`" means
shown in that picture. An image "at y `t`" has its top row at y `t`;
parts above the picture's top edge aren't drawn.

Measured on the first level. The count was taken from chosen values
(0, 3, 9, 57, 98 and 99, set before a pickup as a change made for the
test), with single collectibles counted 33 frames after the pickup and
1 frame after it (`collectibles.md`, "When the count goes up"), with a
second pickup on 11 different frames during and after the first one's
display, and with a bunch (`bunches.md`). Separately, play by inputs
alone was checked against the rules below (see "Checks").

## What the count display shows

- An image of a single collectible, 16 × 16 px, with its top-left
  pixel at (16, 8).
- The **number shown** `N`, in decimal, with no leading zero: digits
  8 px wide and 16 px tall, at y 8. One digit (0 to 9) is at x 32. Two
  digits (10 to 99) are at x 32 (tens) and x 40 (units).

The pictures the image and the digits are drawn with are in "Pictures".

## The number shown

`N` follows the collectible count one step at a time. On every frame on
which `N` differs from the count (after the count's change on that
frame, if any), `N` goes up by one; the change from 99 to 0 is one
step. So when the count goes up by one, `N` goes up on the same frame;
when it goes up by two on one frame (two collectibles counted
together), `N` goes up by one on that frame and by one on the next.
Each frame on which `N` goes up is a **step**.

## When the count display is shown

A pickup **starts**:

- a single collectible taken on frame `P`: on `P` + 1;
- a bunch taken on frame `P`: on `P`.

A started pickup is **waiting** until the steps it is owed have
happened: one for a single collectible, ten for a bunch. Steps go to
waiting pickups in the order they were taken.

The count display is shown on frame `n` when either

- a pickup that started on or before `n` is still waiting after `n`'s
  step (if any), or
- the latest step `S` up to `n` has `n` ≤ `S` + 59.

Otherwise it isn't shown. So a single collectible counted 33 frames
after its pickup on `P` is shown from `P` + 1 to `P` + 92 (`S` = `P` +
33), with the old number up to `P` + 32 and the new one from `P` + 33. A
single collectible counted on `P` + 1 is shown from `P` + 1 to `P` +
60. Another pickup while the display is shown keeps it shown until 59
frames after its own step. A single collectible taken on the first
frame on which the display is no longer shown makes it shown again from
the next frame (its start).

The count display is not shown when a level begins: from the first
level's start it stayed hidden for 900 frames with no pickup.

## Reaching 100

When a step takes `N` from 99 to 0 on frame `S` (the count reaches 100,
starts again from 0, and the player gets one more life,
`collectibles.md`), the count display shows 0 and follows the rules
above. In addition, the **lives display** shows the number of lives
near the top right:

- The number is drawn with the same digits as the count, its digit at
  x 220. An image is drawn to its left (its pictures are in "Pictures").
- It shows the number of lives before the new life up to `S` + 24, and
  the new number from `S` + 25 on.
- It slides in from the top and back out:

| Frame | Lives number at y |
|---|---|
| up to `S` + 7 | not drawn |
| `S` + 8 to `S` + 13 | −12, −8, −4, 0, 4, 8 |
| `S` + 14 to `S` + 146 | 10 |
| `S` + 147 to `S` + 152 | 6, 2, −2, −6, −10, −14 |
| `S` + 153 on | not drawn |

This was measured with 5 lives before the new one, for `S` 33 frames
after the pickup and 1 frame after it; both gave the table above. On `S` + 8,
`S` + 151 and `S` + 152 the number is partly above the picture's top
edge, and only its lowest rows are drawn.

The lives display after all four letters are taken is in `letters.md`,
"The ending", and the one after an extra-life item is taken in
`extra-lives.md`, "The lives display". The count reaching 100 while the
lives display of an extra-life item is shown, or before an item is
taken while its own is shown, is in `extra-lives.md`, "The count
reaching 100".

## When play starts

The lives display is also shown each time play starts: from **frame 0
of a level** and from **frame 0 after a restart** (after a knock-out
or a fall-out, `restart-barrel.md`, "Resuming"; frames are numbered
from 0 again there, as in `collectibles.md`, "Counting the frames").
Pauses don't count. It shows the number of lives at that moment (so after
a restart it is the number after the loss), drawn as in "Reaching 100"
at x 220 for one digit and at x 215 and x 225 for two, and its image to
the left. It slides in and out like the others, on these frames:

| Frame | Lives number at y |
|---|---|
| 0 to 7 | not drawn |
| 8 to 13 | −12, −8, −4, 0, 4, 8 |
| 14 to 135 | 10 |
| 136 to 141 | 6, 2, −2, −6, −10, −14 |
| 142 on | not drawn |

So it stays at y 10 for 122 frames, 11 fewer than after reaching 100,
and the slide-in is the same as there. On frame 8 and on frames 140 and
141 the number is partly above the picture's top edge and only its lowest
rows are drawn. It starts on the same frames after a restart from the
level's start and after one from the restart point.

Measured on screen on 395 starts: 128 level starts (entered from the
map) and 267 restarts after knock-outs and fall-outs. On every frame on
which no other lives display was shown (after a knock-out or a fall-out,
after the count reached 100, or after a life was gained by letters or an
item), the number was drawn at the table's y on 47 976 frames and not
drawn on the 166 310 frames the table gives none for, and on no frame
otherwise. How a display that starts while this one is shown, or a
knock-out during it, shows is not covered.

## Losing a life

When the player is knocked out (`knock-out.md`, hit with no other
character) or falls out of the level (`falling-out.md`, with one
character or two), the player loses one life and the lives display is
shown twice: first with the number of lives **before** the loss, then,
after the image beside it bursts, with the number **after** it. Call
**`L`** the level's last frame (`knock-out.md`, "Lying still";
`falling-out.md`, "The end of the level"). Two frames set the timing:

| | First slide-in **`A`** | Burst **`G`** |
|---|---|---|
| Knocked out (hit on `H`) | `H` + 18 | `L` − 122 |
| Falling out (fall-out frame `Z`) | `Z` + 7 | `Z` + 35 (`L` − 61) |

With the last frame at `F` + 153 as in `knock-out.md`, a knock-out's `G`
is `F` + 31: 31 frames after the third landing.

| Frame | Lives number at y | Number shown | Image to its left |
|---|---|---|---|
| up to `A` − 1 | not drawn | | not drawn |
| `A` to `A` + 5 | −12, −8, −4, 0, 4, 8 | before the loss | drawn |
| `A` + 6 to `G` − 1 | 10 | before the loss | drawn |
| `G` to `G` + 2 | 10 | before the loss | not drawn |
| `G` + 3 to `G` + 10 | not drawn | | not drawn |
| `G` + 11 to `G` + 16 | −12, −8, −4, 0, 4, 8 | after the loss | drawn |
| `G` + 17 to `L` | 10 | after the loss | drawn |

On `G` to `G` + 2 small pieces are drawn around the image's place (the
burst); their pictures are not covered (see "Pictures" for the frames
before `G`). The
image's top row moves with the number's as it slides in. The second
display is still at y 10 on the last frame; it doesn't slide out.

The number shown is the number of lives, 0 included: with one life
before the loss, the first display shows 1 and the second 0. A number of
10 or more is drawn with two digits, tens at x 215 and units at x 225;
a number from 0 to 9 has its one digit at x 220, as in "Reaching 100".
So with 10 lives before the loss the first display has two digits and
the second one digit (9).

**A lives display already shown.** If, on `H`, a lives display from
reaching 100 is shown at y 10, it doesn't slide in again or out: it
stays at y 10, with the number it shows, up to `G` + 2, and the rest
follows the table from `G`. In one of the two runs measured, that
display had started 93 frames before `H`, so it would have slid out
(on `S` + 147, "Reaching 100") 54 frames after `H`; it didn't.

**The count display** goes on following "When the count display is
shown" from `H` to `L`: collectibles taken during the knock-back flight
(`knock-out.md`) start it and are counted as usual, and it is shown up
to 59 frames after its latest step.

## Pictures

What the two displays' images and digits look like in `title-1`. The
places and frames are those above; this section gives the pictures. The
frame count `C` is the one of `collectibles.md` ("Counting the frames"):
the frame's number plus one, counted from frame 0 of the level or of the
restart, pauses not counted.

### The count display's image

The image at (16, 8) is the collectible's spinning picture: on frame `C`
it is `spin-k` with

    k = (⌊C / 4⌋ mod 8) + 1

with `spin-1` to `spin-8`, their hashes and the fallback for the cycle
as in `collectibles.md` ("Pictures"; `spin-6` to `spin-8` have no
hash). It is drawn **as the picture is**, not mirrored left–right as
the collectibles in the level are, with its 16 × 16 square's top-left
at (16, 8) and its origin 8 px right and 9 px below that
(`../formats/sprites.md`, "Where the picture is drawn"). The picture
doesn't depend on when the display started: it keeps to `C`.

### The digits

Each digit 0 to 9 is one fixed picture, 8 px wide and 16 tall, drawn as
two 8 × 8 halves one above the other: the top half at the y given for
the number, the bottom half 8 px lower. A digit looks the same in the
count display and in the lives display, at every place given above. The
ten pictures are not full frames that `../formats/sprites.md` can
decode, so they have no hashes: the engine shows its own digits, drawn
at those places.

### The lives image

The image to the left of the lives number shows one of eight pictures,
`lives-1` to `lives-8`, in a cycle of 14 steps of 3 frames each. Call
**`D`** the frame on which the number is first drawn, at y −12
(frame 8 of "When play starts", `S` + 8 of "Reaching 100", `A` and
`G` + 11 of "Losing a life"). Step 1 starts on `D` + 7:

| Step | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Picture | `lives-1` | `lives-2` | `lives-3` | `lives-2` | `lives-1` | `lives-4` | `lives-5` | `lives-6` | `lives-7` | `lives-8` | `lives-7` | `lives-6` | `lives-5` | `lives-4` |

Step *s* is shown on frames `D` + 7 + 3(*s* − 1) to `D` + 9 + 3(*s* −
1), and step 1 follows step 14 again, for as long as the display is shown.
The three frames before step 1, `D` + 4 to `D` + 6, show the picture of
step 14 (`lives-4`), except in the display "When play starts":

- on a level's first frame and after a restart that followed a
  knock-out, `D` + 4 to `D` + 6 show another picture, `lives-9`, and
  step 1 starts on `D` + 7 as above;
- after a restart that followed a fall-out, `D` + 4 to `D` + 6 show
  `lives-4`, and `D` + 7 to `D` + 9 show another picture, `lives-10`,
  instead of `lives-1`; step 2 starts on `D` + 10 as above.

Before `D` + 4 the image is partly above the picture's top edge. Each
picture is the whole image. They are not full frames of the frame table, so they have no hashes: the
engine shows its own art for `lives-1` to `lives-10`, with the timing
given here. The cycle runs the same in every lives display
(after the count reached 100, a knock-out or a fall-out, and when play
starts).

At the end of a knock-out's or a fall-out's first display, the image
shows three other pictures on `G` − 3 to `G` − 1, one on each frame, and
the burst's pieces on `G` to `G` + 2 (three more pictures, one on each
frame); these six are not covered. The image of the second display runs
the cycle to the last frame.

### Evidence for the pictures

Measured on screen in the first level, with only the sprites drawn, by
inputs alone: 128 runs, 315 606 frames. The runs were 8 recorded routes
from the level's start, each played as recorded and 15 more times with
Jump, Action and Down presses added at random and with a different
start count and number of lives set for each; in half of them the game
was paused now and then. 267 restarts and 616 frames in the area after
the level's exit (left out) were among them. Frames on which the last
frame before a pause or a stall was shown, and frames with another
sprite over the image or the digits or part of them above the picture's
top edge, were left out.

- **The count image.** On 79 727 frames the image was the `spin-k`
  the rule gives, drawn as the picture is (not mirrored), 0 other (12 119
  frames with another sprite over it were left out).
- **Digits.** On 167 385 frames of the count display and 126 175 frames
  of the lives display each digit's two halves were found: for each of
  the ten digits and each half, one picture on every frame, in both
  displays and at every place, and the 20 pictures all different from each
  other.
- **The lives image, cycle.** 98 567 frames showed the picture the
  table gives and 1 069 another (all in the 18 displays named below).
  Of the 395 starts (128 levels and 267 restarts), 391 lasted to frame 17
  or later (the other 4 restarts ended first): 335 showed `lives-9` on
  `D` + 4 to `D` + 6 and 56 showed `lives-4` and `lives-10`, as given,
  and 390 of the 391 followed the table on every frame compared.
  Knock-outs and fall-outs (first and second displays): 603 of 609, and
  10 more lives displays that none of those causes explain, all 10
  following it. After the count reached 100: 12
  of 19 (the eight routes share their first parts, so several of these
  are the same place). In 11 displays one step lasted longer than 3
  frames, once in a display (5 frames in 8 of them, 15 in 2, 16 in 1),
  and the steps after it were shifted by the extra frames; what causes it
  is not known. In 3 more, other pictures were seen on some frames with
  something else drawn near the image. The 4 displays after a life
  gained by letters or an item did not follow the table (not covered).
- **The burst.** In 279 first displays of knock-outs and fall-outs the
  image's last six frames, `G` − 3 to `G` + 2, were pictures not in
  the cycle, one per frame (the display's other first displays were not
  compared there: something else was drawn near the image).

## Example

The count is 9 and no display is shown. A single collectible is taken on
frame 200 near the middle of the picture, and the count becomes 10 on
frame 233. The count display is shown from frame 201: the image and a 9
at x 32 up to frame 232, then the image and 10 (1 at x 32, 0 at x 40)
from frame 233. A second collectible taken on frame 250 is counted on
frame 283, so the display shows 11 from frame 283 and is shown up to
frame 342; it isn't shown from frame 343.

## Checks

- **Route and variations.** The route through the first level and 300
  variations of it with jumps, Action presses and Down added at random
  (inputs only), each until the route ended, the area changed, or the
  player was knocked out or left the level: 214 840 frames, with 865 single collectibles
  and 16 bunches taken, 971 steps, and 88 frames on which the count went
  up by two. On every frame, whether the count display was shown was as
  above, and `N` was as in "The number shown". On every frame on which
  it was shown, the number of digits drawn was that of `N`, except 6
  frames on which another image was passing over the digits' place. 11
  of the steps came from items other than placed collectibles and
  bunches (released by hidden objects, `hidden-objects.md`); they were
  not attributed to a pickup, and the frames around them matched.
- **Second pickups.** With the first pickup on `P` and its step on
  `P` + 33, a second single collectible taken on `P` + 9, + 19, + 32,
  + 33, + 34, + 59, + 91, + 92, + 93, + 94 or + 119: the display was
  shown up to 59 frames after the later step in each case, and with the
  second pickup on `P` + 93 or later it was hidden on `P` + 93 and shown
  again from the second pickup's start.

- **Losing a life.** 89 knock-outs: after random play from 7 places in
  the first level (65 runs, lives 5), from one of those places with the
  lives set to 1, 2, 10, 11 and 12 (4 runs each) and with the count set
  to 98, so that it reached 100 shortly before the hit (4 runs, 2 of
  them with that lives display still shown on `H`); and 38 falls out of
  the level at one gap, 30 with one character and 8 with two; of those
  with one character, 5 with the lives set to 1, 2, 10, 11 and 12 and 1
  with the count set to 98. On every frame from `H` or `Z` to `L`
  (20 180 and 3 686 frames), the lives number's y, whether it and the
  image were drawn, and its digit places were as in "Losing a life"; the
  numbers shown were checked in pictures of a knock-out's first and
  second displays with 5, 1 and 10 lives. In the knock-outs, the count
  display stopped being shown 51 times, each 60 frames after its latest
  step, and was shown again 17 times, each with a step 32 frames later,
  as for a single collectible taken during the knock-back flight on the
  frame before. In one knock-out the player's last movement came
  152 frames before `L` instead of 153 (`knock-out.md`); `G` was still
  `L` − 122.

## Not covered

- Collectibles that are taken but never counted (`collectibles.md`,
  "When the count goes up"), and items released by hidden objects (when
  they start).
- Letters (`letters.md` has the letter row and the lives display after
  all four letters).
- The displays when the other character is lost and the lead goes on
  (`losing-a-character.md`), during the end of a level, and in other
  areas. While falling out of the level, the count display was measured
  only with no pickup waiting and none shown on `Z` (in play it was once
  seen to be shown again around a knock-out or a fall-out with no
  pickup). A lives display that is sliding in or out on `H`, or that is
  shown on `Z`, and a knock-out whose `G` would come before `A` + 6.
- A count step from 99 to 0 together with another one on the same frame,
  a lives display started while one is still shown (other than with
  extra-life items, `extra-lives.md`, "The count reaching 100"), and 10
  or more lives, other than in "Losing a life".
- The pictures of the digits and of the lives image have no hashes, so
  what they look like is not covered (the engine draws its own); nor is
  their colour, nor which of two overlapping images is in front (a taken
  item's flying copy passes over the digits' place), nor the six burst
  pictures (`G` − 3 to `G` + 2), `lives-9` and `lives-10` beyond their
  frames, or why one step of the cycle sometimes lasts longer.
- The lives display after the four letters or an extra-life item: its
  image did not follow the cycle's table in the 4 displays seen.
- A lives display that starts, or a knock-out or fall-out that comes,
  while the display of "When play starts" is shown; that display for the
  second character alone, after a game over, in other areas and in other
  levels.
- Other levels, and pausing.
