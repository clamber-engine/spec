# Letters

Titles: `title-1`

The first level holds four **letters**, numbered 1 to 4 by their place
in the word they spell. The player takes a letter by touching it. Each
letter taken is shown in a **letter row** at the top of the picture,
and taking all four gives one more life. This file covers when a letter is
taken, the letters taken so far, the reward, when the letter row is
shown, and what it shows after the fourth letter.

Frames are logic frames at 60 Hz, numbered as in a trace. `x` grows to
the right. Horizontal positions are level positions as in `view.md`.
Vertical positions are **level y** as in `view-vertical.md`: they grow
downward, measured from the top of the level. The player's **feet line**
`F` is as in `view-vertical.md`. Positions in the picture are in px from
its top-left corner (the picture is 256 × 224 px, `view.md`,
`view-vertical.md`).

Measured on the first level with the first character leading and
alone (two characters: see "Two characters" under "Pickup ranges"). The player was placed at chosen offsets from two of the level's
letters, one pixel apart, in poses reached by inputs only: standing,
walking, running, crouching, rolling, the Down + Action ground action,
and frames of jumps from take-off until well after the top (Jump held
or released early, facing right and left). The letters taken, the
reward and the letter row were measured on all four letters.
Separately, play by inputs alone was checked against the rules below:
about 108 000 frames from one route through the level and 160
variations of it with jumps, rolls and crouches added at random, and
about 112 000 frames from 320 short runs of random inputs starting near
one letter. That play included 223 pickups of three different letters
in the states covered here (standing, walking, running, rolling, and
jumps both before and after `T` + 6, facing both ways). Each came on the
frame the rules give, and no letter was taken on a frame the rules
don't give. Another 56 pickups happened in states listed under "Not
covered" and weren't checked.

## A letter's position

A letter does not move. Its **position** is a point (`xl`, `yl`) in the
level. A level's placement list (`../formats/placements.md`) places each
letter with an entry; `xl` is 1 px left of the entry's level x and `yl`
is the entry's level y (see "Positions" there). How to tell that an
entry places a letter, and which of the four it is, is not covered. How
a letter is drawn is not covered.

## When a letter is taken

A letter is taken on frame `n` when both of these hold **at the end of
frame `n`**, after the player has moved on that frame:

1. it hasn't been taken already;
2. the player's offsets from it (below) are inside the ranges for the
   player's state on frame `n` (see "Pickup ranges").

So a letter is taken on the first frame that ends with the player in
range. Call this frame `P`. The offsets are those of bunches
(`bunches.md`), with the letter's position in place of the bunch's; the
ranges are below. Landing was not measured for letters.

From `P` on, the letter is no longer in the level. It never comes back
during the visit to the level: in one case the player was moved about
500 px past a taken letter's place and back, and it did not come back
(an untaken letter did, in the same case). After a knock-out it is there
again only when play resumes at the level's start (`restart-barrel.md`,
"What is there again after a knock-out"); after an exit it stays taken
(`exits.md`, "Coming back to the level"). After finishing the level, not
covered.

### Offsets

Using whole pixels, let

- `X` be the player's `x` rounded down;
- **`I`** be how far the player is past the letter in the direction the
  player is facing: `X − xl` when facing right, `xl − X` when facing
  left;
- **`D`** = `F − yl`, how many px the feet line is below the letter's
  position (negative when it is above).

Because `I` is measured in the facing direction, each range below holds
for both facings. For example, a standing player facing right takes a
letter with `X − xl` from −15 to 21. Facing left, it takes one with
`X − xl` from −21 to 15.

## Pickup ranges

A letter is in range when `I` and `D` are both within the ranges for
the player's state (ends included). For the states that `bunches.md`
covers, they are the ranges of bunches. The Down + Action ground action
(`ground-action.md`) was measured for letters only: it has the standing
ranges. `bunches.md` makes no claim about it, and this file makes none
about bunches.

| State | `I` | `D` |
|---|---|---|
| Standing, walking, running | −15 to 21 | −8 to 40 |
| Down + Action ground action (letters only, see above) | −15 to 21 | −8 to 40 |
| Crouching (Down held on the ground) | −18 to 12 | −12 to 31 |
| Rolling | −13 to 21 | −4 to 38 |
| Jumping, from the take-off frame `T` to `T` + 5 | −15 to 21 | −8 to 40 |
| Jumping, from `T` + 6 on | −15 to 18 | 13 to 57 |

`T` is the frame carrying `jump_start` (`jump-arc.md`). For the first six
frames of a jump, starting with `T`, the ranges are the standing ones.
From `T` + 6 until the jump lands, the last row applies on every frame,
whatever the vertical speed, and whether Jump is still held or not.

For example, a player facing right walking on a floor 33 px below a
letter's position (`D` = 33) takes it on the first frame that ends with
`X − xl` from −15 to 21. Walking right from far left, that is the first
frame that ends with `X` = `xl` − 15 or more.

### Two characters

With two characters there (`second-character.md`), only the character
that leads takes a letter, by its own offsets. The character that
follows never takes one, even with the letter inside its own ranges and
outside the lead's. The ranges above are the first character's when it
leads, with or without the second character following. With the second
character leading, its ranges are those of bunches for the second
character (`bunches.md`, "Two characters"), with one row for its jumps
from take-off until they land.
This was measured with letters and bunches alike, with each character
leading (`bunches.md`, "Two characters", has the measurements).

## The letters taken

The player has a set of **letters taken**, which starts empty. Letters
can be taken in any order.

A letter taken on frame `P` joins the set a little later, after its
copy has flown to the letter row (see "The letter row"). In the two
cases where both were measured, it joined 2 frames after the copy
reached its place. Over all cases measured it joined on `P` + 30 (two
cases), `P` + 31 or `P` + 33, and in the runs of "Another letter while
the row is shown" (letters drawn in other places in the picture) from
`P` + 6 to `P` + 35. The copy's exact path is not covered, so neither is
the exact frame.

## All four letters

On the frame the fourth letter joins the set, the player gets **one
more life**. The order the letters were taken in doesn't matter (each of
the four letters was measured as the last one taken).

After that, the letter row shows the **ending**: the four letters blink,
then move one at a time to the right, to where the number of lives is
shown, and the number of lives shown goes up by one. See "The ending"
below; the life itself counts from the frame the fourth letter joins the
set. The set of letters taken is empty again from then on.

After a knock-out the set is emptied when play resumes at the level's
start and kept when it resumes at the restart point (`restart-barrel.md`,
"What is there again after a knock-out"); an exit keeps it (`exits.md`,
"Coming back to the level"). Whether it survives finishing the level or
entering another level is not covered.

## The letter row

The letter row has four **slots**, one per letter. Slot `k` (for letter
`k`) shows its letter as a 16 × 16 px image whose top-left pixel is at
(84 + 24 × (`k` − 1), 12) in the picture: x 84, 108, 132 and 156. A slot
whose letter isn't in the set is empty. The row is not shown during
ordinary play.

Positions in this section are in the picture **made on frame `n`**: the
one drawn from the state at the end of frame `n`, shown once frame `n`
is over. A row "at y `t`" has the top row of its images at y `t`; parts
above the picture's top edge aren't drawn.

When a letter is taken on frame `P` (for the fourth letter, only up to
the frame it joins the set; see "The ending"):

1. **The copy.** From the picture made on `P` on, a copy of the letter
   is drawn starting about where the letter was and moving toward its
   slot, slowing as it gets near: each frame it covers roughly an eighth
   of the distance left. It reached its slot on `P` + 28 or `P` + 29 in
   the cases measured above (sooner when the letter was drawn nearer
   its slot), and stays there, also while the rest of the row is still
   sliding in.
2. **Sliding in.** The rest of the row (the letters already in the set)
   slides in from the top, 4 px per frame: at y −12 on `P` + 2, −8 on
   `P` + 3, and so on to 16 on `P` + 9, then at y 12 from `P` + 10 on.
3. **Shown.** The row, with the new letter in its slot, stays at y 12
   up to `P` + 121.
4. **Sliding out.** It moves up 4 px per frame: y 8 on `P` + 122, 4 on
   `P` + 123, and so on to −12 on `P` + 127. It isn't drawn from
   `P` + 128 on.

| Frame | Row at y |
|---|---|
| `P`, `P` + 1 | not drawn |
| `P` + 2 to `P` + 9 | −12, −8, −4, 0, 4, 8, 12, 16 |
| `P` + 10 to `P` + 121 | 12 |
| `P` + 122 to `P` + 127 | 8, 4, 0, −4, −8, −12 |
| `P` + 128 on | not drawn |

For example, with letters 1 and 2 already taken, take letter 3 on frame
100. Letters 1 and 2 are drawn with their top-left pixels at (84, 12)
and (108, 12) from frame 110. Letter 3's copy reaches (132, 12) around
frame 128. All three stay there until frame 221, and nothing of the row
is drawn from frame 228.

### Another letter while the row is shown

Take a letter on frame `P1` and another one on `P2` = `P1` + `d`, with
`d` from 2 to 127, while the row for the first is still drawn. When the
second letter is the fourth, what follows holds up to the frame it joins
the set; from then on see "The ending" ("After another letter"). The row's
**end** is its last frame at y 12: `P1` + 121 after the first letter.
What happens depends on whether `P2` comes before the end:

- **`P2` before the end** (`d` from 2 to 120). The row goes on as it
  was, without sliding in again: still sliding in when `d` is 9 or
  less, then at y 12. Its end becomes `P2` + 121: it stays at y 12 up to
  `P2` + 121, slides out on `P2` + 122 to `P2` + 127 (y 8, 4, 0, −4,
  −8, −12) and isn't drawn from `P2` + 128 on.
- **`P2` on the end or later** (`d` from 121 to 127). The first row
  goes on as if the second letter hadn't been taken: it slides out on
  `P1` + 122 to `P1` + 127 and isn't drawn from `P1` + 128. The second
  letter brings in a **second row**, which follows the table above
  from `P2` (not drawn on `P2` and `P2` + 1, y −12 on `P2` + 2, and so
  on). Until the first is gone, both are drawn, each showing the
  letters in the set; where they overlap, the picture shows both. From
  `d` = 128 on only the second row is left, as for a single letter.

Each letter's copy (step 1) flies to its slot and joins the set as for a
single letter, whatever the row does.

For example, with letter 4 already taken, take letter 1 on frame 100.
The row (letter 4) slides in from frame 102 and is at y 12 from 110, and
letter 1 joins the set around frame 130. Taking letter 2 on frame 150,
letters 1, 4 and then 2 stay at y 12 up to frame 271, slide out on 272 to
277, and nothing of the row is drawn from 278. Taking letter 2 on frame
224 instead, the first row (letters 1 and 4) is at y 8, 4, 0, −4, −8 and
−12 on frames 222 to 227, and the second row (letters 1 and 4, joined by
letter 2 when its copy arrives) is at −12, −8, −4, 0 on 226 to 229, at
12 from 234 to 345, and is not drawn from 352.

The first level's letters are 866 px or more apart, so there this needs
the player to get from one letter to the next in under 128 frames. In
the checks, the player and the view were moved next to the second letter
on `P1` + 1, and the player was then put on it on `P2`. Pairs of letters
next to each other in the word were used (1 then 2, 2 then 3, 3 then 4),
each with none or one of the other two letters already in the set, with
`d` from 2 to 140 and without the second pickup: 1 260 runs. In another
13 runs only the player was moved and the view followed by itself
(`d` from 140 to 200). On every frame of each run, to 199 frames after
the second pickup, where each letter in the set was drawn matched the
rules above: 345 631 frames, 0 different. A copy that reached its slot before the
row was at y 12 stayed at its slot (twice). The picture itself was
checked in one run with `d` = 123.

## The ending

`J` is the frame the fourth letter joins the set (the copy of the letter
taken last has reached its slot; "The letters taken"). Up to `J` the
row is as in "The letter row" for the fourth letter too: on `J` all four
letters are in their slots at y 12. From `J` + 1 on, the row doesn't
slide out; this section gives what it shows instead.

### The four letters

The letter taken last is **one frame late**: everything below happens
to it one frame later than to the other three. (When the fourth letter
is taken while the row is shown for another one, which letters are late
can differ: see "After another letter".) For letter `k`, let
`n` = `f` − `J` on frame `f` (`f` − `J` − 1 for a late letter), and let
its **slot place** be (84 + 24 × (`k` − 1), 12), its slot in the row.

| `n` | Letter `k` is |
|---|---|
| 0 | at its slot place |
| 1 to 8, 17 to 24, 33 to 40, 49 to 56 | not drawn |
| 9 to 16, 25 to 32, 41 to 48 | at its slot place |
| 57 to `M` − 1 | at its slot place |
| from `M` = 67 + 24 × (`k` − 1) | moving right, then gone |

So the not-late letters blink: off for 8 frames, on for 8, four times,
and then stay on.

**Moving.** The letter's horizontal position `x` starts at its slot
place's x (84, 108, 132 or 156). On each frame from `M` on, `x` moves
right by an eighth of the distance left to 192, but by at least ½ px;
if less than ½ px would then be left, `x` is 192 instead. The letter
is drawn with its top-left pixel at (`x` rounded down, 12). On the
frame after the one on which `x` is 192, it isn't drawn any more.

A not-late letter starts moving on `J` + 67, `J` + 91, `J` + 115 or
`J` + 139 (letters 1 to 4), is at 192 on `J` + 98, `J` + 120, `J` + 142
or `J` + 162, and is not drawn from the next frame. Letter 1, for
example, is drawn at x 97, 109, 119, 128, 136, 143, 149, 154, 159, 163,
167, 170, 172, 175, 177, 179, 180, 182, 183, 184, 185, 186, 186, 187,
188, 188, 189, 189, 190, 190, 191 and 192 on `J` + 67 to `J` + 98.

### The lives display

`G` is the first frame on which letter 4 is gone: `J` + 163, or `J` +
164 when letter 4 was taken last.

The **lives display** shows the number of lives near the top right,
with the same digits as the count display (`count-display.md`, "What the
count display shows"): one digit (1 to 9) at x 220, two digits (10 to
99) at x 215 (tens) and x 225 (units). An image is drawn to its left;
that image and how it moves are not covered. It shows the number of
lives before the new life up to `G` − 1, and the new number from `G` on.

| Frame | Lives number at y |
|---|---|
| `J` to `J` + 6 | not drawn |
| `J` + 7 to `J` + 12 | −12, −8, −4, 0, 4, 8 |
| `J` + 13 to `G` + 121 | 10 |
| `G` + 122 to `G` + 127 | 6, 2, −2, −6, −10, −14 |
| `G` + 128 on | not drawn |

With 99 or more lives before the new life, the number shown is 99 also
from `G` on (`extra-lives.md`, "More than 99 lives").

### After another letter

When the fourth letter is taken on `P2` while the row is still shown for
another letter taken on `P1` (as in "Another letter while the row is
shown", `d` = `P2` − `P1` from 2 to 127), the row follows that section up
to `J`, and the ending is as above from `J` on, with one difference:
which letters are **late**. Each letter is either on time or late (one
frame late in everything above, as the letter taken last is after a
single letter), but the late ones are not always just the letter taken
last. With `d` from 121 to 140 (the largest measured) they were. With
`d` up to 120, the late
ones were sometimes all four letters, sometimes only the letter taken on
`P1`, and sometimes only the letter taken last:

| Letters (`P1`, then `P2`) | Only the letter taken on `P1` | All four | Only the letter taken last |
|---|---|---|---|
| 1, then 2 | `d` 2 to 4 | `d` 5 and 6 | `d` 7 to 140 |
| 2, then 3 | — | `d` 2 to 120 | `d` 121 to 140 |
| 3, then 4 | `d` 2 | `d` 3 to 120 | `d` 121 to 140 |

What decides this for `d` up to 120 is not covered. `G` is still the
first frame on which letter 4 is gone: `J` + 163, or `J` + 164 when
letter 4 is late. The lives display follows its table from `J` whatever
is late, and the lives go up by one on `J`.

For example, with letters 1 and 2 already taken and 5 lives, take
letter 3 on frame 1 and letter 4 on frame 61 (`d` = 60). Letter 4 joins
the set on frame 89 (`J`), and all four letters are late: all four are
drawn on frame 90, none of them on 91 to 98, all four again from 99, and
letter 1 starts moving on frame 157. The lives display comes in with 5
at y −12 on frame 96, as for a single letter. Letter 4 is gone from
frame 253 (`G` = `J` + 164), and the display shows 6 from then on.

### Example

With letters 1, 2 and 4 already taken and 5 lives, the player takes
letter 3, which joins the set on frame 531 (`J`), and the player has 6 lives
from frame 531. Letter 3 is the late one. Letters 1, 2 and 4 are not
drawn on frames 532 to 539, 548 to 555, 564 to 571 and 580 to 587;
letter 3 on 533 to 540, 549 to 556, 565 to 572 and 581 to 588. The
lives display comes in from the top with 5 at y −12 on frame 538 and is
at y 10 from 544. Letter 1 moves from frame 598 and is gone from 630,
letter 2 moves from 622 and is gone from 652, letter 3 moves from 647
and is gone from 675, and letter 4 moves from 670 and is gone from 694
(`G`). The lives display shows 6 from frame 694, leaves at y 6 on frame
816 and is not drawn from 822.

### Checks

Each of the four letters was taken last, at its place in the level,
with the other three already in the set; for each, with the view in
three places before it was taken (the letter joined the set 18 to 36
frames after it was taken), with no input or with Right, Left, Jump,
Right + Action or Down held from `J` on, and with 1, 5 or 8 lives
before: 216 runs. On every frame from `J` to `J` + 300, where each
letter was drawn, whether and where the lives number was drawn, the
frame its number changed, and the frame the lives went up were as
above: 50 583 frames, 0 different. In 78 of the runs the player was
hit and knocked out, or fell out of the level, 16 to 274 frames after
`J`; those runs were compared up to the frame before. The frames with
the lives number partly above the picture's top edge (y −12, −10 and
−14), the blinking, the late letter and the first frame of a move were
also checked in the picture itself in one run. 10 and 99 lives after
the new life (9 and 98 before) gave the two-digit places, and 99 before
gave 99 shown throughout.

For "After another letter", the fourth letter was taken after the one
before it in the word (1 then 2, 2 then 3, 3 then 4, the other two
letters already in the set), with `d` from 2 to 140, as in the checks of
"Another letter while the row is shown": 417 runs. On every frame from
`P1` to `J` + 300 (to `J` + 299 in 139 runs where the player was knocked
out on `J` + 300), where each letter and the lives number were drawn,
the frame the number changed and the frame the lives went up were as
above, with each letter's lateness taken from the run: 165 936 frames,
0 different, and in every run each letter was either on time or late
throughout.

## Not covered

- With two characters: the second character's landings and running
  state, and the letter row, the letters taken and the reward (only
  which character takes a letter, and on which frame, was measured).
- Being hit, bouncing, carrying, falling without a jump, landing (the
  frames from `L`, the frame carrying `land`, on), turning and the frame
  after a turn, and the stop after a roll.
- Taking two letters on consecutive frames, and taking a letter that
  comes earlier in the word than one whose row is still shown.
- Which letters are late in the ending when the fourth letter is taken
  120 frames or less after another (see "After another letter").
- The ending while the player is knocked out, leaves the level or
  finishes it, or while another lives display (`count-display.md`) is
  shown.
- In one setup, with the player rolling from the pickup on, the lives
  display came in 2 frames later than above (y −12 on `J` + 9); why is
  not covered.
- How a letter and the row's images are drawn; the copy's exact path.
- Other levels, and whether every level has four letters.
