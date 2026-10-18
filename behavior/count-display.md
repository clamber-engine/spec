# The count display

Titles: `title-1`

While a level is played, the **count display** at the top left of the
picture shows the collectible count (`collectibles.md`) for a while
after the player takes a collectible. It is not shown otherwise. When
the count goes from 99 to 0 and the player gets one more life, the
number of lives is also shown for a while at the top right, and it is
shown when the player loses a life by being knocked out or falling out
of the level. This file covers what the two displays show, where, and on
which frames.

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

How the image and the digits are drawn is not covered.

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
  x 220. An image is drawn to its left; that image and how it moves are
  not covered.
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
`extra-lives.md`, "The lives display".

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
burst); how they and the image are drawn and move is not covered. The
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
  a lives display started while one is still shown, and 10 or more
  lives, other than in "Losing a life".
- How the images and digits are drawn and animated.
- Other levels, and pausing.
