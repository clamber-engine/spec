# Extra-life items

Titles: `title-1`

An **extra-life item** is an object placed in a level that gives the
player one or more lives when touched. Some extra-life items stay where
they are placed; others rise from the moment they appear until they
reach the level's top edge and are gone. This file covers the kinds of
extra-life items, how they move, when one is taken, the lives it gives
and how the new lives are shown, and when an item goes away.

Frames are logic frames at 60 Hz, numbered as in a trace. `x` grows to
the right. Horizontal positions are level positions as in `view.md`.
Vertical positions are **level y** as in `view-vertical.md`: they grow
downward, measured from the top of the level. The player's **feet line**
`F` is as in `view-vertical.md`. Positions in the picture are in px from
its top-left corner (the picture is 256 × 224 px, `view.md`,
`view-vertical.md`).

Everything here was measured with the first character leading and
alone. The pickup ranges were measured with a copy of an item placed
near the first level's start in a copy of the image, 60 px above the
floor there: the player was placed at offsets from it one pixel apart,
in poses reached by inputs only (standing, walking, running, crouching,
rolling, the Down + Action ground action, and frames of jumps from
take-off to well after the top, Jump held or released early), facing
right and facing left, and the item was or wasn't taken on the next
frame. The rules were then checked by inputs alone (see "Checks").

## Kinds

There are four kinds. A level's placement list places each item with an
entry of one of these kinds (`../formats/placements.md`, "Kinds"):

| Kind | Lives given | Moves |
|---|---|---|
| `extra-life-1` | 1 | stays where it is placed |
| `extra-life-3` | 3 | stays where it is placed |
| `rising-extra-life-1` | 1 | rises (see "Rising") |
| `rising-extra-life-2` | 2 | rises |

The first level has two `rising-extra-life-1` items, one
`rising-extra-life-2` and two `extra-life-3`; its room
(`exits.md`) has one `extra-life-1`. Everything below holds for all four
kinds unless it says otherwise. How an item is drawn is not covered.

## Position

An item's **position** is a point (`xf`, `yf`) in the level. It appears
at its entry's position (`../formats/placements.md`, "When placed
objects appear"): `xf` is its entry's level x and, on the frame it
appears, `yf` is its entry's level y. `xf` never changes.

An item that stays where it is placed keeps that position until it is
taken or goes away.

### Rising

Call `a` the frame a rising item appears and `y0` its entry's level y.
At the end of frame `a` + `n` (`n` = 0, 1, 2, …) its level y is

    y0 − n (n + 1) / 512

so it rises 1/256 px on frame `a` + 1, 2/256 px on frame `a` + 2, and
1/256 px per frame more on each frame after that: its speed grows by
1/256 px per frame on every frame, from 0 on frame `a`. No limit to the
speed was seen (it was still growing at 1.62 px per frame, 414 frames
after appearing). The values are exact multiples of 1/256 px.

| Frame | Risen since `a` (px) |
|---|---|
| `a` | 0 |
| `a` + 1 | 1/256 |
| `a` + 2 | 3/256 |
| `a` + 10 | 55/256 (about 0.21) |
| `a` + 60 | 1 830/256 (about 7.15) |
| `a` + 120 | 7 260/256 (about 28.36) |
| `a` + 414 | 85 905/256 (about 335.57) |

A rising item passes up through floors: one placed 75 px below the floor
rose through it with no change to the rule. It rose the same with the
player standing still about 180 px away and with the player walking
toward it and away from it.

**At the top.** A rising item is removed on the first frame on which its
level y would be 0 or less: at the end of that frame it is no longer in
the level. In the runs (17 runs, 14 heights of appearance), the last frame on
which an item was still there had it at level y from 0.20 to 1.77, and
the step that removed it would have taken it to between −0.02 and
−1.59.

Exactly 0 counts: an item whose step takes it to level y 0 exactly is
removed on that frame, and one taken to 1/256 px is still there and is
removed on the next frame. For an item that appears at a whole level y
this happens only when `y0` is 511, on frame `a` + 511; the first
level has no such item.

For example, an item appearing at level y 337 rises for 414 frames: at
the end of frame `a` + 414 it is at level y about 1.43, and on frame
`a` + 415 it is removed.

## When an item is taken

An item is taken on frame `n` when both of these hold **at the end of
frame `n`**, after the player and the item have moved on that frame:

1. it is in the level (it has appeared and hasn't been taken, risen out
   of the level or gone away);
2. the player's offsets from it (below) are inside the ranges for the
   player's state on frame `n` (see "Pickup ranges").

So an item is taken on the first frame that ends with the player in
range. Call this frame `P`. For a rising item, its position on frame `n`
is where the rule in "Rising" puts it at the end of frame `n`, not
where it was at the end of the frame before.

### Offsets

Using whole pixels, let

- `X` be the player's `x` rounded down;
- `Y` be the item's level y rounded up (for an item that stays where it
  is placed, its entry's level y);
- **`I`** be how far the player is past the item in the direction the
  player is facing: `X − xf` when facing right, and `xf − 3 − X` when
  facing left;
- **`D`** = `F − Y`, how many px the feet line is below the item's
  position (negative when it is above).

Because `I` is measured in the facing direction, each range below holds
for both facings, but the ranges are not centred on `xf`: facing left,
they are 3 px further left than the mirror image of the facing-right
ones. For example, a standing player facing right takes an item with
`X − xf` from −20 to 23; facing left, with `X − xf` from −26 to 17.

## Pickup ranges

An item is in range when `I` and `D` are both within the ranges for the
player's state (ends included):

| State | `I` | `D` |
|---|---|---|
| Standing, walking, running, the Down + Action ground action | −20 to 23 | −29 to 35 |
| Crouching (Down held on the ground) | −23 to 14 | −33 to 26 |
| Rolling | −18 to 23 | −25 to 33 |
| Jumping, from the take-off frame `T` to `T` + 5 | −20 to 23 | −29 to 35 |
| Jumping, from `T` + 6 on | −20 to 20 | −8 to 52 |

`T` is the frame carrying `jump_start` (`jump-arc.md`). For the first six
frames of a jump, starting with `T`, the ranges are the standing ones.
From `T` + 6 until the jump lands, the last row applies on every frame,
whatever the vertical speed, and whether Jump is still held or not.

For example, a player standing on a floor 40 px below an item that stays
where it is placed (`D` = 40) doesn't take it: 40 is outside −29 to 35.
If the player jumps straight up from there with `I` from −20 to 20, the
item is taken on `T` + 6 at the latest, since `D` is then from −8 to 52.

## What taking an item does

On `P`:

- the number of lives goes up by the kind's number of lives (1, 2 or 3,
  "Kinds");
- the item is no longer in the level. It doesn't come back during the
  visit to the level: in the runs, a player who took one, walked back
  left until its entry was out of the window and then right again over
  its position, found no item there.

How the taken item is drawn after `P` (an image moves from where it was
toward the lives display) is not covered.

### The lives display

`P` starts a **lives display** at the top right, as for a new life from
the collectible count (`count-display.md`, "Reaching 100") but with its
own timing. It uses the same digits as the count display
(`count-display.md`, "What the count display shows"): one digit (1 to 9)
at x 220, two digits (10 to 99) at x 215 (tens) and x 225 (units). An
image is drawn to its left; that image and how it moves are not covered.

With `k` the kind's number of lives and `L` the number of lives before
`P`, the number shown is `L` up to `S` − 1, and goes up by one every 16
frames: `L` + `j` from `S` + 16 (`j` − 1), for `j` = 1 to `k`. **`S`**,
the frame of the first step, comes after the taken item's image has
flown to the lives display, so it depends on where the item was in the
picture: in 190 pickups it was from `P` + 41 to `P` + 47, later the
further left (and, less so, the lower) the item was in the picture, for
items from x 50 to 141 and y 33 to 170 in the picture. How the image
flies, and so `S` exactly, is not covered. Call **`E`** = `S` + 16
(`k` − 1) the frame it shows the new number of lives.

| Frame | Lives number at y |
|---|---|
| `P` to `P` + 6 | not drawn |
| `P` + 7 to `P` + 12 | −12, −8, −4, 0, 4, 8 |
| `P` + 13 to `E` + 121 | 10 |
| `E` + 122 to `E` + 127 | 6, 2, −2, −6, −10, −14 |
| `E` + 128 on | not drawn |

On `P` + 7, `E` + 126 and `E` + 127 the number is partly above the
picture's top edge, and only its lowest rows are drawn. In one of the
190 pickups below, one step came 18 frames after the one before instead
of 16 (not covered).

The table was measured with 5 lives before `P` for each of the four
kinds (walking into the item, or with the player placed on it), and with 9 lives before
an `extra-life-3` (shown 9, then 10, 11 and 12 with two digits).

## More than 99 lives

The number of lives can go past 99, but no lives display shows more
than 99. This is the same however the number changes: taking an item
here, the count reaching 100 (`count-display.md`, "Reaching 100"), all
four letters (`letters.md`, "The lives display"), and a life lost by a
knock-out (`knock-out.md`) or a fall-out (`falling-out.md`).

- **What is kept.** A gain adds to the whole number of lives and a lost
  life takes one from it, with no limit at 99: with 97 lives an
  `extra-life-3` gives 100, and with 255 it gives 258; with 101 lives a
  knock-out or a fall-out leaves 100, and with 100 it leaves 99.
- **What is shown.** Wherever a lives display would show a number `n`
  (the number before a change, each step, and the new number), it shows
  `n` when `n` is 99 or less and 99 otherwise, as two digits at x 215
  and x 225. Its timing and positions are the same as for smaller
  numbers.

So with 99 or more lives before a gain, the display shows 99 all the
time it is drawn; with 97 lives before an `extra-life-3` its steps show
98, 99 and 99. With 100 or more lives before a lost life it shows 99
both before and after the life is taken off.

## Going away untaken

An item that hasn't been taken is removed on the first frame on which its
entry is no longer in the window of `../formats/placements.md` ("When
placed objects appear"), and doesn't come back when its entry is in the
window again. This was seen past the window's right edge, in three runs:
an item that stays where it is placed and a rising item placed near the
first level's start, and the first level's own second
`rising-extra-life-1`. In the first two the view came back over the
item's position and no item appeared again. A rising item that rose out
of the level didn't appear again either.

## Example

A `rising-extra-life-2` item's entry has level x 400 and level y 337.
The view's position goes from 111 to 113 on frame 39, so the entry is in
the window at the start of frame 40 (400 − 113 = 287) and the item
appears there: `a` = 40, at (400, 337). A player walking right on the
floor at level y 337 (`F` = 337) reaches `X` = 380 on frame 132. By then
the item has risen 92 × 93 / 512 ≈ 16.71 px: it is at level y about
320.29, so `Y` = 321 and `D` = 337 − 321 = 16. `I` = 380 − 400 = −20,
inside the walking ranges, so `P` = 132 (frame 131 ended with `X` = 378,
`I` = −22). With 5 lives before, the player has 7 lives from frame 132.
The lives number comes in from the top on frames 139 to 144 and stays at
y 10; in this case `S` = 177 (`P` + 45), so it shows 5 up to frame 176, 6
from 177 and 7 from 193 (`E` = 193); it moves up on frames 315 to 320
and is not drawn from frame 321 on.

## Checks

- Pickup ranges: 80 grids of placements (75 of an `extra-life-3`
  copy, 1 of each rising kind and 4 of an `extra-life-1` copy, in all
  the poses above and both facings), 959 040 placements, 210 951 of them
  taken: every one taken or not as the ranges say.
- By inputs only: 2 000 runs of random input (walking, running,
  crouching, rolling, jumping, the ground action, standing) with an item
  near the first level's start: `extra-life-3` 60 px above the floor and
  on it, `rising-extra-life-1` appearing on the floor and 60 px above it,
  and `rising-extra-life-2` on the floor; 1 054 865 frames. All 168
  pickups were on the frame the rules give, and the rules gave no other
  pickup. 25 more pickups were in states not covered here (landing,
  turning, falling without a jump, the stop after a roll; see "Not
  covered"). With the figure's ranges (`figure.md`) instead, 166 frames
  came out wrong in the same runs; with the left-facing ranges 3 px
  further right instead of left, 30; with a rising item's position from
  the end of the frame before, 5; with the jump's last row from `T` + 7,
  1.
- Rising: 17 runs of rising items from appearing to their removal at
  the top, from 14 heights of appearance, and 2 runs to their removal
  past the window's edge, all following the rule in "Rising" exactly.
- At the top: 154 runs, 77 with each rising kind, with the item moved
  partway through its rise (0 to 413 frames after appearing) to between
  3/256 px below and 3/256 px above the position from which its next
  step takes it to level y 0 exactly. In all of them the item was
  removed on the frame "At the top" gives, and in the 22 runs whose step
  reached level y 0 exactly it was removed on that frame. With removal
  only below level y 0 instead, 26 runs came out wrong.
- Lives and the lives display: in every pickup in the runs above, the
  lives went up on `P` by the kind's number of lives, and the shown
  number stepped as above in 189 of the 190 pickups whose steps were
  seen (`S` from `P` + 41 to `P` + 47; the other one is under "The lives
  display"). The display's positions were measured frame by
  frame in 8 pickups (the four kinds, with 5 and 9 lives before).
- More than 99 lives: 43 runs with the lives set beforehand. An
  `extra-life-3` taken with 24 numbers of lives from 0 to 255 (including
  every tens digit, and 96 to 100, 150 and 253 to 255); a fall-out with
  2, 10, 99, 100, 101, 102 and 150 lives; two knock-outs each with 100
  and 101; the count reaching 100 and all four letters, each with 98,
  99, 100 and 150. In every run the lives after were the lives before
  plus the gain or minus one, and on every frame on which the lives
  number was drawn it showed the number above (the number capped at 99,
  one digit at x 220 for 0 to 9).

## Not covered

- The second character, leading or following, and riding the animal
  (`riding.md`).
- Taking an item while landing (the frames from `L`, the frame carrying
  `land`, to `L` + 6), turning and the frame after a turn, falling
  without a jump, the stop after a roll, being hit, bouncing, carrying.
- Taking an item while a lives display is still shown (from another
  item, the count reaching 100, or all four letters), and two items on
  one frame.
- More than 258 lives, and whether a number of lives over 99 is shown
  anywhere other than the lives display (for example on the map).
- Going away past the window's left edge; whether an item that went
  away is there when the level is played again (after a knock-out, or
  coming back after an exit). An item taken is there again after a
  knock-out (`restart-barrel.md`, "What is there again after a
  knock-out"; rising items only measured with play resuming at the
  level's start); an item taken before an exit stays gone after coming
  back (`exits.md`, "Coming back to the level").
- Rising items while the game is paused or during a knock-out.
- Entries far below the picture. With the player on the floor near the
  first level's start (level y 337), an item whose entry was at level y
  433 appeared as usual, and none appeared from entries at 17 places
  from level y 434 to 511 (a rising kind at all of them, a still kind at
  one); how this depends on the view is not covered.
- How an item is drawn, how the taken item's image flies to the lives
  display (and so `S` exactly), and the sound.
- Other levels, and how the items in them are chosen.
