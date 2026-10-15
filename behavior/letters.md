# Letters

Titles: `title-1`

The first level holds four **letters**, numbered 1 to 4 by their place
in the word they spell. The player takes a letter by touching it. Each
letter taken is shown in a **letter row** at the top of the picture,
and taking all four gives one more life. This file covers when a letter is
taken, the letters taken so far, the reward, and when the letter row is
shown.

Frames are logic frames at 60 Hz, numbered as in a trace. `x` grows to
the right. Horizontal positions are level positions as in `view.md`.
Vertical positions are **level y** as in `view-vertical.md`: they grow
downward, measured from the top of the level. The player's **feet line**
`F` is as in `view-vertical.md`. Positions in the picture are in px from
its top-left corner (the picture is 256 × 224 px, `view.md`,
`view-vertical.md`).

Measured on the first level with the first character leading and
alone. The player was placed at chosen offsets from two of the level's
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
range. Call this frame `P`. The offsets and ranges are those of bunches
(`bunches.md`), with the letter's position in place of the bunch's.
Landing was not measured for letters.

From `P` on, the letter is no longer in the level. It never comes back
during the visit to the level: in one case the player was moved about
500 px past a taken letter's place and back, and it did not come back
(an untaken letter did, in the same case). Whether it is there when the
level is played again is not covered.

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
the player's state (ends included). They are the ranges of bunches.

| State | `I` | `D` |
|---|---|---|
| Standing, walking, running, Down + Action ground action | −15 to 21 | −8 to 40 |
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

## The letters taken

The player has a set of **letters taken**, which starts empty. Letters
can be taken in any order.

A letter taken on frame `P` joins the set a little later, after its
copy has flown to the letter row (see "The letter row"). In the two
cases where both were measured, it joined 2 frames after the copy
reached its place. Over all cases measured it joined on `P` + 30 (two
cases), `P` + 31 or `P` + 33. The copy's exact path is not covered, so
neither is the exact frame.

## All four letters

On the frame the fourth letter joins the set, the player gets **one
more life**. The order the letters were taken in doesn't matter (letter
4 last and letter 1 last both measured).

After that, the letter row shows a sequence (not covered in detail):
the four letters blink, then move one at a time to the right, to where
the number of lives is shown, and the number of lives shown goes up by
one about 194 frames after `P` (the life itself counts from the frame
the fourth letter joins the set). The set of letters taken is empty
again from then on.

Whether the set of letters taken survives losing a life, leaving the
level or entering another level is not covered.

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

When a letter is taken on frame `P` (not the fourth; see "All four
letters"):

1. **The copy.** From the picture made on `P` on, a copy of the letter
   is drawn starting about where the letter was and moving toward its
   slot, slowing as it gets near: each frame it covers roughly an eighth
   of the distance left. It reached its slot on `P` + 28 or `P` + 29 in
   the cases measured, and stays there.
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

## Not covered

- The second character, leading or following.
- Being hit, bouncing, carrying, falling without a jump, landing (the
  frames from `L`, the frame carrying `land`, on), turning and the frame
  after a turn, and the stop after a roll.
- Taking a letter while the row is still shown for another one.
- How a letter and the row's images are drawn; the copy's exact path.
- Other levels, and whether every level has four letters.
