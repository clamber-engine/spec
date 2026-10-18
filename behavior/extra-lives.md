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

**While the game is paused.** A press of the Pause input (`controls.md`)
pauses the game, and no logic frames run until the pause ends, so a
trace has no rows for the pause and a rising item doesn't move during
it. The frame of the press that pauses is an ordinary frame: the item
rises on it as usual. From the next 60 Hz frame through the frame of the
press that ends the pause, the item stays where it was at the end of
that frame. On the frame after that it rises again with the speed it
had, so in a trace its rise is the same as if there had been no pause.
An item that would have appeared, or gone past the window's left edge
("Going away untaken"), on the frame after the pausing press does so on
the frame after the pause.

In the runs, a second press 33 or more frames (at 60 Hz) after the
pausing press, with no press in between, ended the pause; one 1 to 32
frames after it did not, and the game stayed paused. Holding the input
for 4 frames on either press made no difference, and holding it after
the press that ended the pause did not pause the game again.

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

### While the player is knocked out

When the player is knocked out (`knock-out.md`, hit frame H), a rising
item that is already in the level rises as usual on H, doesn't move on
**H + 1 to H + 10**, and rises again from H + 11. Its speed is held
with it: on H + 11 it rises 1/256 px more than on H, as if the ten
frames hadn't happened. So from H + 11 on, at the end of frame
`a` + `n` it is where the rule above puts it at the end of `a` + `n` − 10.
No logic frames run in the 32-frame pause between H + 7 and H + 8
(`knock-out.md`), so the item doesn't move then either.

After H + 10 it goes on rising while the player flies back and lies
still. It is removed at the top as above (on the first frame its level
y would be 0 or less, counted with the held frames), or when its entry
leaves the window ("Going away untaken"). No item was removed on
H + 1 to H + 10.

For example, an item that appeared on frame `a` = H − 100 has risen
99 × 100 / 512 ≈ 19.34 px at the end of H − 1 and rises 100/256 px on
H. It is at the same place from H to H + 10 and rises 101/256 px on
H + 11, where it would have risen 111/256 px with no knock-out.

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

### Several items

What follows holds when an item is taken while the lives display of
another extra-life item is still shown, and when two items are taken on
the same frame. Items keep their own `P` and give their lives on it
("What taking an item does"); there is one lives display, and the
number shown goes up one step at a time until it reaches the number of
lives.

For each taken item, call **`A`** its **arrival**: the frame its image
reaches the lives display. For an item taken alone `S` = `A` + 10, so `A`
is `S` − 10 as above. The frames `P` to `A` − 1 of any taken item are
**held**, and the other frames are **free**. The number shown changes
only on free frames, by one (a **step**) each time. Counting free frames
only, and not the frame counted from:

- After a step that leaves the number shown below the number of lives,
  the next step comes on the 16th free frame after it.
- After the step that makes the number shown equal to the number of
  lives, on frame `E`: if an item is taken on `E` + 1, the next step
  comes on the first free frame after `E` (that item's `A`, or later if
  another taken item holds it).
- Otherwise (the first step of all, or after `E` when no item is taken
  on `E` + 1), the next step comes on the 10th free frame after the first
  free frame on which the number shown is less than the number of lives.

For a single item this gives the steps above (`S` = `A` + 10, then every
16 frames). So:

- An item taken before an earlier one's image has arrived: the steps of
  both start 10 frames after the later arrival, 16 frames apart, as many
  as the two give together. Two items taken on the same frame are the
  same case.
- An item taken while the earlier one's steps are still due: the step
  due next comes later by the frames held for the new item, and the
  steps that remain follow 16 frames apart.
- An item taken after the last step: the next step comes 10 frames after
  its arrival, as for a single item, except when it is taken on the
  frame right after the last step: then the first step comes on its
  arrival frame `A`.

Call **`E`** the frame of the last step (the number shown then equals
the number of lives). The lives number's height:

- An item taken when no lives display is shown starts one, as in the
  table above.
- Taking an item while the number is coming in, or at y 10, changes
  nothing; the number stays at y 10 up to `E` + 121, with `E` the last
  step of all the items taken.
- From `E` + 122 it moves up 4 px per frame: y 6 on `E` + 122, then 2,
  −2, −6, −10, −14 up to `E` + 127, and on through −18, −22 and −26 on
  `E` + 128 to `E` + 130, where it isn't drawn. The display ends on
  `E` + 131.
- An item taken on a frame `Q` from `E` + 122 to `E` + 131 turns it
  back: on `Q` its y is 4 px lower than on `Q` − 1, and it keeps moving
  down 4 px per frame until it is at y 10 (taken on `E` + 122, it stays
  at 10). It then stays at 10 up to 121 frames after the new last step.
- An item taken on `E` + 132 or later starts a new lives display, as in
  the table above.

The number is drawn when its y is −16 or more (at −16 none of its rows
are in the picture).

Three or more items in one lives display (three were measured) follow
the same rules.

#### Late items

A taken item can be **late**. A late item counts, in all the rules of
this section, as if it were taken on `P` + 1 and its image arrived on
`A` + 1:

- its held frames are `P` + 1 to `A`;
- on `P`, the number of lives these rules compare with is the one
  before the late item was taken (the lives themselves still go up on
  `P`, as in "What taking an item does");
- if it starts a lives display, the number comes in one frame later
  than in the table: not drawn up to `P` + 7, −12 to 8 on `P` + 8 to
  `P` + 13, at y 10 from `P` + 14. Taken alone, its `S` is `A` + 11;
- the cases above that depend on `P` (taken on the frame right after
  the last step, taken from `E` + 122 on, and so on) take `P` + 1 for
  it.

An item that isn't late counts as described above. Of the first level's
own two `extra-life-3` items, taken one after the other in real play
(from the shot of the launch barrel, `launch-barrel.md`), the right one
is late and the left one isn't; the right one is also late when it is
taken alone, and the left one taken alone isn't. Which other items are
late is not covered ("Not covered").

**Example.** No lives display is shown, and the player, with 11 lives,
takes a late `extra-life-3` item on frame 457; the lives are 14 from
then on. Its image arrives on frame 493: frames 458 to 493 are held, and
on 457 the number shown (11) is compared with 11 lives, so the first
free frame on which the number shown is less than the lives is 494, not
493. The number shows 12 from frame 504, the 10th free frame after it,
then 13 and 14 from 520 and 536 (`E` = 536); it is drawn from frame 465
(y −12) and is at y 10 from 471. Had the item not been late, the number
would come in on 464 and show 12 from 503.

#### Example

Two `extra-life-3` items are on the floor 94 px apart, and the player
has 5 lives. The player walks into the first one on frame 100 and the
lives become 8; its image arrives on frame 136, the first free frame
on which the number shown (5) is less than the lives, and the number
shows 6 from frame 146, the 10th free frame after it. The player takes
the second item on frame 147 (the lives become 11), and its image
arrives on frame 183: frames 147 to 182 are held, so the 16th free frame
after frame 146 is frame 198. The number shows 7 from frame 198, then 8,
9, 10 and 11 from frames 214, 230, 246
and 262 (`E` = 262). The number came in on frames 107 to 112 and stays at
y 10 up to frame 383; it moves up from frame 384 and isn't drawn from
frame 390 on. Had the second item been taken on frame 179 instead, one
frame after the step to 8 on frame 178, its image would have arrived on
frame 215 and the number would have shown 9 from that frame.

#### The count reaching 100

The lives display of the count reaching 100 (`count-display.md`,
"Reaching 100") is the same lives display, and the rules above take in
its life. Let `S` be the frame of the count's step from 99 to 0. For the
lives display:

- The new life counts toward the number of lives from `S` + 1 on (on
  `S` itself the number of lives that the rules compare with is one
  less).
- On `S` + 1 the number's height does what an item taken on `S` + 1
  would do: it starts a lives display as in the table above if none is
  shown, turns it back if it is moving up, and changes nothing if it is
  coming in or at y 10.
- For the steps, the count acts like an item taken on `S` + 1 whose
  arrival is `S` + 1: it holds no frames.
- In the last rule above ("Otherwise"), the frame counted from is the
  first free frame on which the number shown is less than the number of
  lives and on whose frame before the number was at y 10 and the number
  shown was less than the number of lives then. For items alone this
  changes nothing: the frame before an item's arrival is held, with the
  number at y 10 and short of the lives.

So:

- The count reaching 100 with no lives display shown: as in
  `count-display.md`. The number comes in on `S` + 8 to `S` + 14 (at
  y 10 from `S` + 14), `S` + 15 is the frame counted from, and the
  step comes on `S` + 25.
- The count reaching 100 while an item's image is on its way, or while
  steps are still due: the steps come when they would have, with one
  more step 16 frames after the last.
- On the frame of the last step `E`: this is like an item taken on
  `E` + 1 with its arrival on `E` + 1, so the step comes on `E` + 1.
- Later, with the number at y 10: the step comes on `S` + 12.
- On `E` + 121, the last frame at y 10: the number doesn't move up on
  `E` + 122, and the step comes on `S` + 12.
- While the number moves up (`S` from `E` + 122 to `E` + 130): it turns
  back on `S` + 1, and the step comes 11 frames after the first frame on
  which it is at y 10 again. For example, with `S` = `E` + 122 the
  number is at y 6 on `S` and at 10 on `S` + 1, and the step comes on
  `S` + 12.
- On `E` + 131 or later: a new lives display starts, as with no lives
  display shown.
- An item taken while the count's lives display is shown, or with its
  step still due, follows the rules above with the count's life counted
  in the number of lives.

For example, the player has 5 lives and takes an `extra-life-3` on frame
147 (the lives become 8); its image arrives on frame 183 and the number
shows 6, 7 and 8 from frames 193, 209 and 225 (`E` = 225). The count
goes from 99 to 0 on frame 226 (the lives become 9). Frame 227 is the
first frame on which the number shown (8) is less than the lives (9),
but on frame 226 the number shown equalled the 8 lives counted then; so
the frame counted from is 228, and the number shows 9 from frame 238,
the 10th free frame after it. It moves up from frame 360 and isn't drawn from frame 366 on.
Had the count reached 100 on frame 121 instead, before the item, the
number would have come in on frames 129 to 135 and shown 6 from frame
146; the item taken on frame 147, the frame after that last step, would
then have made it show 7, 8 and 9 from frames 183, 199 and 215.

## More than 99 lives

The number of lives can go past 99, but no lives display shows more
than 99. This is the same however the number changes: taking an item
here, the count reaching 100 (`count-display.md`, "Reaching 100"), all
four letters (`letters.md`, "The lives display"), and a life lost by a
knock-out (`knock-out.md`) or a fall-out (`falling-out.md`).

- **What is shown.** Wherever a lives display would show a number `n`
  (the number before a change, each step, and the new number), it shows
  `n` when `n` is 99 or less and 99 otherwise, as two digits at x 215
  and x 225. Its timing and positions are the same as for smaller
  numbers.
- **What is kept.** A gain adds to the whole number of lives, with no
  limit at 99, and each lost life later takes one from that number. This
  is seen when lives are lost afterwards: the display keeps showing 99
  until the number is below 100, and then counts down from 98.

So with 99 or more lives before a gain, the display shows 99 all the
time it is drawn; with 97 lives before an `extra-life-3` its steps show
98, 99 and 99. With 100 or more lives before a lost life it shows 99
both before and after the life is taken off.

### Example

Three runs, each taking an `extra-life-3` and then losing lives one
after another (each lost life is a fall-out; the level is entered again
from the map before the next one). The display after each lost life
shows:

| Lives before the item | After the item | 1st lost life | 2nd | 3rd |
|---|---|---|---|---|
| 96 | 99 | 98 | 97 | |
| 97 | 99 | 99 | 98 | 97 |
| 98 | 99 | 99 | 99 | 98 |

All three show 99 after the item; the number of lost lives it takes
before 98 is shown tells them apart (the item gave 99, 100 and 101).

## Going away untaken

An item that hasn't been taken is removed on the first frame on which its
entry is no longer in the window of `../formats/placements.md` ("When
placed objects appear"), past either edge, and doesn't come back when
its entry is in the window again. A rising item that rose out of the
level didn't appear again either.

For example, an item whose entry has level x 400 is there on every frame
that starts with the view's position `V` (rounded down) at 431 or less
(400 − 431 = −31), and is removed on the first frame that starts with
`V` at 432 or more. If the view then moves back left over the item's
position, no item appears.

Past the right edge this was seen in four runs: an item that stays where
it is placed (two runs) and a rising item placed near the first level's
start, and the first level's own second `rising-extra-life-1`. In one
run of each placed kind the view came back over the item's position and
no item appeared again. Past the left edge it was seen in 23 runs, with
an item of each kind placed near the first level's start and the view
moving right, by walking, running and pausing, until the entry had left
the window (see "Checks"). In all of them the view then moved back left
over the item's position and on past it, and no item appeared again.
The rising items there went away this way before rising out of the
level.

After a knock-out, a rising item that went away untaken is in the level
again (`restart-barrel.md`, "What is there again after a knock-out").
After the level is left through an exit and play comes back to it, a
rising item that went away untaken is in the level again (`exits.md`,
"Coming back to the level").

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
- While the player is knocked out: 186 knock-outs in the first level,
  forced by inputs and by moving a walker onto the player, with one of
  its `rising-extra-life-1` items (94 runs) or its `rising-extra-life-2`
  (92 runs) in the level from 80 to 211 frames before H, with Right or
  nothing held from H − 1 on. Every one of the 16 523 frames of the
  item's rise from H − 1 to its removal followed "While the player is
  knocked out", 1 860 of them held. 181 items were removed at the top
  and 5 past the window's left edge. Up to H the items were where they
  were in the same runs with no hit.
- Lives and the lives display: in every pickup in the runs above, the
  lives went up on `P` by the kind's number of lives, and the shown
  number stepped as above in 189 of the 190 pickups whose steps were
  seen (`S` from `P` + 41 to `P` + 47; the other one is under "The lives
  display"). The display's positions were measured frame by
  frame in 8 pickups (the four kinds, with 5 and 9 lives before).
- More than 99 lives, what is shown: 43 runs with the lives set
  beforehand. An `extra-life-3` taken with 24 numbers of lives from 0 to
  255 (including every tens digit, and 96 to 100, 150 and 253 to 255); a
  fall-out with 2, 10, 99, 100, 101, 102 and 150 lives; two knock-outs
  each with 100 and 101; the count reaching 100 and all four letters,
  each with 98, 99, 100 and 150. On every frame on which the lives number
  was drawn it showed the number expected from the gain or the lost life,
  capped at 99 (one digit at x 220 for 0 to 9).
- More than 99 lives, what is kept: 4 runs taking an `extra-life-3`
  with 96, 97, 98 and 150 lives, then losing 2, 3, 4 and 3 lives in a
  row by fall-outs (entering the level again from the map in between).
  The display after each lost life matched the example; with 150 lives
  it showed 99 after all three.
- Several items ("Several items"): 500 runs with two items near the
  first level's start in a copy of the image: an `extra-life-3` on the
  floor and a second item 0 to 94 px to its right, either an
  `extra-life-3` on the floor or 30 to 60 px above it (taken in a jump),
  or a `rising-extra-life-1` or `rising-extra-life-2`; walking or
  running, with 5 or 9 lives before. The second item was taken 0 to 287
  frames after the first, every gap in that range at least once. Also
  36 runs of the first level's own two `extra-life-3` items taken from
  the launch barrel's shot, 2 frames apart, with 0, 5, 9 and 98 lives
  before. Images arrived 34 to 42 frames after their pickups. On all
  362 418 frames the number shown and the number's height were as
  described, with the right one of the first level's own items late
  ("Late items"). With the next step always 10 frames after each arrival
  instead, 125 of the 500 runs came out different.
- Three items and late items ("Late items"): 471 runs in a copy of the
  image with three `extra-life-3` items near the first level's start:
  the first on the floor, the second 0 to 94 px to its right on the
  floor or 30 or 50 px above it, the third 0 to 389 px to the first
  one's right, on the floor or 30 or 50 px above it; walking, running
  or jumping, waiting 0 to 400 frames between pickups (so that some
  third items started a second lives display after the first had
  ended), with 1, 5 or 90 lives before. With 334 of the 1 361 pickups
  late, 0 of the 454 158 frames differed from the rules; the 36 runs
  of the level's own items above (19 848 frames) were run again with the
  same result. With no item ever late, 3 296 frames in 231 runs came out
  different (and in the 36 runs the number came in a frame early). In 71
  runs a walker later reached the player, who stood still after the
  third pickup; those runs were checked up to 20 frames before the lives
  went down (`knock-out.md`).
- The count reaching 100 with an item ("The count reaching 100"): 856
  runs in the copy of the image of "Several items", with an `extra-life-3` on the
  floor taken on frame 147 walking (120 running) and the count set to
  99 at the start and to 100 on a chosen frame, as a change made for the
  test: the count's step on every frame from 0 to 460 walking (from 147
  frames before the pickup to 313 after it), every second frame from 0
  to 400 running, every fifth frame walking with 9 lives before, and
  101 runs with two more collectibles counted 1 and 34 frames after the
  step. On all 796 076 frames the number shown and the number's height
  were as described. With the count's life counted from `S` instead, 698 runs
  came out different; without the condition on the frame before, 694.
  The 500 runs of "Several items" came out the same with that
  condition.
- Going away: 24 runs with an item placed near the first level's start
  (12 of an `extra-life-3`, 6 of each rising kind), the view moving
  right past the item by walking, running and pausing at random and then
  back left; 12 158 frames. In 23 runs the item went away past the left
  edge and in one past the right edge; the item was there or not as
  "Going away untaken" says on every frame. With the left limit 1 px
  further left, 16 frames came out wrong; with the view's position at the
  end of the frame instead of its start, 24.
- While the game is paused: 7 344 pauses in the first level, started on
  one of 3 frames at each of 68 points from the level's start on, with
  Right or nothing held, a `rising-extra-life-1` or `rising-extra-life-2`
  item in the level on the pausing frame in 6 804 of them, and ended by a
  press 33 to 204 frames after the pausing press or not ended within 400
  frames. In every one the trace was the same as with no pause (173 550
  item frames after the pause compared), and the item didn't move on any
  of the 1 561 014 frames it was paused. In 626 of them the item appeared
  or went past the window's left edge on the pausing frame or within 5
  frames after the pause. A second press from 2 to 101 frames after the
  pausing press (800 runs) ended the pause exactly when it came 33 or
  more frames after it.

## Not covered

- The second character, leading or following, and riding the animal
  (`riding.md`).
- Taking an item while landing (the frames from `L`, the frame carrying
  `land`, to `L` + 6), turning and the frame after a turn, falling
  without a jump, the stop after a roll, being hit, bouncing, carrying.
- Taking an item while a lives display from all four letters is still
  shown, the count reaching 100 more than once in one lives display, and
  rising items with the count reaching 100.
- When an item is late ("Late items"), other than the first level's own
  two `extra-life-3` items. In the three-item runs of "Checks" it
  depended on where the items were placed and not on how long the player
  waited between pickups: with the second item 94 px to the first one's
  right, a third item walked into was late when placed 95 to 134 or 194
  to 263 px to the first one's right (every 3 px) and not at 137 to 191
  or 266 to 389. Whether a lives display being shown can keep a placed
  object from appearing when the limit of `../formats/placements.md`
  ("How many objects can be there") is reached.
- Whether the number kept above 99 counts down the same way after the
  count reaching 100 or all four letters (only an `extra-life-3` was
  followed by lost lives), how far above 99 it can go, and whether a
  number of lives over 99 is shown anywhere other than the lives display
  (for example on the map).
- Whether an item of kind `extra-life-3` that went away is there when
  the level is played again after coming back through an exit (a rising
  item that went away untaken is there again: `exits.md`, "Coming back
  to the level"). After a knock-out, a rising item that went away
  untaken is there again, and so is an item taken (`restart-barrel.md`,
  "What is there again after a knock-out"; rising items taken measured
  with play resuming at the level's start and at the restart point); an
  item taken before an exit stays gone after coming back (`exits.md`,
  "Coming back to the level").
- Rising items during a knock-out: an item that appears on H or later,
  an item touched by the player, items that stay where they are placed,
  and the second character being the one hit (`losing-a-character.md`).
  Pauses started while the player is in a launch barrel or shot from it
  (`launch-barrel.md`): on some of those frames a press did not pause
  the game, and some of those pauses changed what followed. A rising
  item removed at the top next to a pause. Which presses end a pause
  other than a single second press as above (after a press that did not
  end it, one 17 frames later did, even sooner than 33 frames after the
  pausing press), and what the game shows while paused.
- Entries far below the picture. With the player on the floor near the
  first level's start (level y 337), an item whose entry was at level y
  433 appeared as usual, and none appeared from entries at 17 places
  from level y 434 to 511 (a rising kind at all of them, a still kind at
  one); how this depends on the view is not covered.
- How an item is drawn, how the taken item's image flies to the lives
  display (and so `S` exactly), and the sound.
- Other levels, and how the items in them are chosen.
