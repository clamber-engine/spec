# Figures

Titles: `title-1`

A **figure** is a small golden image of an animal placed in a level. The
player takes it by touching it. Each figure taken adds one to the
**figure count** of its animal; it changes neither the collectible count
(`collectibles.md`) nor the number of lives. The taken figure is shown
flying up to the top of the picture, resting there, and leaving. This
file covers where a figure is, when it is taken, the figure count, and
how the taken figure is shown.

Frames are logic frames at 60 Hz, numbered as in a trace. `x` grows to
the right. Horizontal positions are level positions as in `view.md`.
Vertical positions are **level y** as in `view-vertical.md`: they grow
downward, measured from the top of the level. The player's **feet line**
`F` is as in `view-vertical.md`. Positions in the picture are in px from
its top-left corner (the picture is 256 × 224 px, `view.md`,
`view-vertical.md`).

The first level holds one figure. It was measured with the first
character leading and alone, in two ways; with two characters, see "Two
characters" below. The pickup ranges were measured
with a copy of the figure placed near the level's start in a copy of the
image, 60 px above the floor there: the player was placed at offsets
from it one pixel apart, in poses reached by inputs only (standing,
walking, running, crouching, rolling, the Down + Action ground action,
and frames of jumps from take-off to well after the top, Jump held or
released early), facing right and facing left, and the figure was or
wasn't taken on the next frame. The level's own figure was then used
for play by inputs alone (see "Checks").

## A figure's position

A figure does not move until it is taken. Its **position** is a point
(`xf`, `yf`) in the level: `xf` is its entry's level x and `yf` its
entry's level y (`../formats/placements.md`, "Positions"). A level's
placement list places each figure with an entry of kind `figure`
(`../formats/placements.md`, "Kinds"). How a figure is drawn before it
is taken is not covered.

## When a figure is taken

A figure is taken on frame `n` when both of these hold **at the end of
frame `n`**, after the player has moved on that frame:

1. it hasn't been taken already;
2. the player's offsets from it (below) are inside the ranges for the
   player's state on frame `n` (see "Pickup ranges").

So a figure is taken on the first frame that ends with the player in
range. Call this frame `P`.

### Offsets

Using whole pixels, let

- `X` be the player's `x` rounded down;
- **`I`** be how far the player is past the figure in the direction the
  player is facing: `X − xf` when facing right, and `xf + 3 − X` when
  facing left;
- **`D`** = `F − yf`, how many px the feet line is below the figure's
  position (negative when it is above).

Because `I` is measured in the facing direction, each range below holds
for both facings, but the ranges are not centred on `xf`: facing left,
they are 3 px further right than the mirror image of the facing-right
ones. For example, a standing player facing right takes a figure with
`X − xf` from −22 to 31; facing left, it takes one with `X − xf` from
−28 to 25.

## Pickup ranges

A figure is in range when `I` and `D` are both within the ranges for the
player's state (ends included):

| State | `I` | `D` |
|---|---|---|
| Standing, walking, running, the Down + Action ground action | −22 to 31 | −27 to 37 |
| Crouching (Down held on the ground) | −25 to 22 | −31 to 28 |
| Rolling | −20 to 31 | −23 to 35 |
| Jumping, from the take-off frame `T` to `T` + 5 | −22 to 31 | −27 to 37 |
| Jumping, from `T` + 6 on | −22 to 28 | −6 to 54 |

`T` is the frame carrying `jump_start` (`jump-arc.md`). For the first six
frames of a jump, starting with `T`, the ranges are the standing ones.
From `T` + 6 until the jump lands, the last row applies on every frame,
whatever the vertical speed, and whether Jump is still held or not.

For example, a player walking right on a floor 4 px below a figure's
position (`D` = 4) takes it on the first frame that ends with `X − xf`
from −22 to 31: walking right from far left, the first frame that ends
with `X` = `xf` − 22 or more.

### Two characters

With two characters there (`second-character.md`), one leads and the
other follows it. The ranges above are the first character's when it
leads, with or without the second character following.

**Only the lead takes.** Whether the figure is taken depends on the
leading character's offsets alone. The character that follows never
takes it, even with the figure inside its own ranges and outside the
lead's. This holds with either character leading.

**Second character leading.** With the second character leading, its
own ranges apply (`I` and `D` as in "Offsets", including the 3 px
shift facing left):

| State | `I` | `D` |
|---|---|---|
| Standing | −19 to 21 | −29 to 35 |
| Walking, running, rolling | −25 to 22 | −25 to 35 |
| Crouching (Down held on the ground) | −26 to 24 | −28 to 22 |
| Jumping, from the take-off frame `T` until it lands | −25 to 22 | −25 to 35 |
| Landing, from `L` to `L` + 16 | −25 to 22 | −25 to 35 |
| Standing again, from `L` + 17 | −19 to 21 | −29 to 35 |

Its jumps have one row: unlike the first character's, there is no
change at `T` + 6. With the second character leading, Down + Action
gives a roll and uses the rolling row. Its running state was reached in
play after landing from a jump taken while running.

**Landing.** `L` is the frame carrying `land` (`jump-arc.md`). The
jumping row goes on through the landing, for 17 frames, `L` to `L` + 16;
on `L` + 17 the standing row applies. The change is the same whatever the
length of the jump (Jump let go early or held to the landing), and
facing either way. A figure at `I` = −25, `D` = −25, for example, is
taken on `L` + 16 but not on `L` + 17.

For example, the second character standing facing left takes the figure
with `xf` + 3 − `X` from −19 to 21, so with `X` from `xf` − 18 to
`xf` + 22.

Measured on the first level, with its own figure. The figure was moved
to chosen offsets from one character, one pixel apart (`I` from about
−40 to 40, `D` from −40 to 64), with both characters in poses reached by inputs
only: standing, walking, running, crouching, rolling, and frames
of jumps from take-off to landing (Jump held, released early, from a
run, facing left).

- With the second character leading, every pose gave exactly the ranges
  above, facing either way (82 grids, 629 370 placements, 0 wrong).
- With the second character leading, standing jumps (Jump let go after
  4, 10 or 20 frames, held to the landing facing right, and held facing
  left) were followed from before the landing to at least 20 frames
  after it,
  with the figure at each offset from `I` −34 to 30 and `D` −50 to 60
  (267 grids, over 1.7 million placements): it was taken exactly inside
  the jumping row up to `L` + 16 and inside the standing row from
  `L` + 17.
- With the first character leading and the second following, the
  first character's ranges above held unchanged, including the change
  at `T` + 6 (14 grids, 119 070 placements, 0 wrong).
- With the figure moved to offsets from the character that follows, in
  nine poses of the lead with either character leading (153 090
  placements), it was taken on exactly the placements that put it
  inside the lead's ranges. 40 341 of those placements put it inside
  the follower's own ranges and outside the lead's; none of them took
  it.

Separately, play by random inputs alone, 400 runs of up to 500 frames from a
point shortly before the figure with each character leading (each run
stopped when a character was hit), was checked against the rules on
every frame: 181 832 frames with 138 pickups by the lead in covered
states (57 with the second character leading: 7 running, 5 walking, 21
rolling, 3 crouching, 21 jumping; 81 with the first: 11
walking or running, 33 rolling, 3 crouching, 34 jumping), each on the
frame the rules give, and no pickup on a frame they don't give. The
follower was never inside its own ranges with the lead outside the
lead's. Another 62 pickups came in states listed under "Not covered"
(mostly landing and falling without a jump) and weren't checked. With
the first character's ranges used for the second character, 54 of its
pickups were missed and 33 frames gave a pickup that didn't happen.

## After a figure is taken

From `P` on, the figure is no longer in the level, and it doesn't come
back during the visit to the level: in the runs, a player who took it,
went about 400 px further right and came back found no figure there. A
figure that hasn't been taken does come back after leaving the view: it
was seen to go when the view moved away from it and to be there again
when the view came back (`../formats/placements.md`, "When placed
objects are removed"). Whether a taken figure is there when the
level is played again after finishing it is not covered; after a
knock-out it is there again and the figure count is kept
(`restart-barrel.md`, "What is there again after a knock-out"), and
after an exit it stays taken (`exits.md`, "Coming back to the level").

### The figure count

Each figure is of one of four **animals**, numbered 1 to 4 as in
`picture-game.md` ("The animals"), and the player has a **figure count**
for each animal. Taking a figure adds one to its animal's count on `P`,
and changes no other count. The first level's own figure is of animal
2; "the count" elsewhere in this file is animal 2's. Taking it with the
count at 0 made it 1; with the count at 2, it made it 3. What a count of
3 leads to is not covered (see "Not covered"). The picture game's
figures (`picture-game.md`, "A won game") can be of any of the four
animals: taking one of animal 1, 2, 3 or 4, with animal 2's count at 1
and the others at 0, raised that animal's count by one and left the
other three as they were.

### How the taken figure is shown

Positions in this section are in the picture **made on frame `n`**: the
one drawn from the state at the end of frame `n`, shown once frame `n`
is over. The figure's image fits in a 34 × 34 px box; "the image at
(`a`, `b`)" means that box has its top-left pixel at (`a`, `b`).

Each figure count from 1 to 3 has a **place** at the top of the picture:
place `c` is the image at (74 + 40 (`c` − 1), 2), so (74, 2), (114, 2)
and (154, 2). Let `c` be the figure count after the pickup on `P`.

1. **The flight.** From the picture made on `P` + 1, the image moves
   from where the figure was drawn toward its **resting place**, place
   `c`. Each frame it covers about an eighth of the distance left,
   horizontally and vertically, slowing as it gets near. Call **`A`**
   the first frame on which it is at its resting place; in the cases
   measured `A` was from `P` + 29 to `P` + 33 (the further the image has
   to travel, the later).
2. **Resting.** It stays at place `c` from `A` to `A` + 62.
3. **Leaving.** From `A` + 63 it moves up 4 px per frame: the bottom row
   of its box is at y 31 on `A` + 63, 27 on `A` + 64, and so on to 3 on
   `A` + 70; parts above the picture's top edge aren't drawn. From
   `A` + 71 on, nothing of it is drawn.

| Frame | Image at |
|---|---|
| `P` + 1 to `A` − 1 | moving toward place `c` |
| `A` to `A` + 62 | place `c` |
| `A` + 63 to `A` + 70 | (74 + 40 (`c` − 1), −2 − 4`k`) on `A` + 63 + `k` |
| `A` + 71 on | not drawn |

For example, with `A` = `P` + 33 for a figure taken on frame 100 with
the count at 0, the image rests at (74, 2) from frame 133 to frame 195,
rises from frame 196, and is not drawn from frame 204 on.

#### The earlier figures

When `c` is 2 or 3, the pictures from `P` on also show an image at each
place from 1 to `c` − 1, one for each figure counted before. These
images come down from above the picture and then go with the new one:

| Frame | Image at each place `j` < `c` |
|---|---|
| `P` to `P` + 3 | not drawn |
| `P` + `k`, `k` = 4 to 11 | (74 + 40 (`j` − 1), 4`k` − 46) |
| `P` + 12 to `A` + 62 | place `j` |
| `A` + 63 to `A` + 70 | (74 + 40 (`j` − 1), −2 − 4`k`) on `A` + 63 + `k` |
| `A` + 71 on | not drawn |

So the bottom row of each earlier image is at y 3 on `P` + 4, 7 on
`P` + 5, and so on to 31 on `P` + 11, the leaving steps in reverse; as
there, parts above the picture's top edge aren't drawn. They rest while
the new image is still on its way, and from `A` + 63 the whole row
rises together, on the new image's frames.

This doesn't depend on what is shown of an earlier figure when the next
one is taken. Its image may still be flying, resting, leaving or gone:
from `P` on, only the images of this pickup are drawn, as above, and the
earlier pickup's own resting and leaving frames don't happen. For
example, with the count at 1, a second figure taken on frame 100 while
the first figure's image rests at (74, 2) makes that image vanish on
frame 100; it comes back down from frame 104, rests at (74, 2) from
frame 112, and, with `A` = 133, rises with the new image at (114, 2)
from frame 196.

With `c` = 3, the rows above hold from `P` to `A` + 17. From `A` + 18
the three images are shown differently, as part of what a figure count
of 3 leads to (see "Not covered").

## Checks

- **Pickup ranges.** For each state in the table, the player was placed
  at every `X − xf` from −55 to 55 and every height from 62 px below the
  figure's position to 45 px above it: about 12 000 placements per
  state, frame of a jump and facing, 74 sets, 887 112 placements in
  all. In every set the figure was taken exactly on the placements whose
  end-of-frame offsets are inside the table's ranges (241 272 of them),
  and on no other. The jump frames were checked from `T` to `T` + 39
  with Jump held and at several frames after an early release. With the
  jump's second row starting on `T` + 7 instead, 4 266 placements were
  wrong.
- **Natural play.** 3 000 runs of random inputs (walking and running
  both ways, crouching, rolling, jumping, the ground action, standing
  still) of 600 frames each, from three points shortly before the first
  level's figure, about 982 000 frames: the figure was taken 884 times.
  718 of these were in the states covered here (131 walking, 13
  running, 2 standing, 53 crouching, 417 rolling, 42 jumping before
  `T` + 6 and 60 from `T` + 6 on; 112 facing left). Each came on the
  frame the rules give, and the rules gave no other frame. 3 of the
  rolling pickups were on the frames on which a roll carries on into
  running (`roll-speed.md`), which had the rolling ranges. The other 166
  happened in states listed under "Not covered" and weren't checked.
- **Controls.** The same runs with the letters' ranges (`letters.md`)
  in place of these got 679 of the 718 pickups wrong; with the
  facing-left ranges taken as the plain mirror image of the facing-right
  ones, 172 frames were wrong; with the jump's second row starting on
  `T` + 7, 33 pickups were wrong.
- **The image.** The flight, the resting place and the frames of leaving
  were measured from pictures with only sprites drawn, in four pickups:
  walking and running right and jumping left into a copy of the figure,
  and walking right into the level's own figure. `A` was `P` + 33,
  `P` + 33, `P` + 29 and `P` + 32.
- **The earlier figures.** In a copy of the image with two copies of
  the figure on the floor 94 px apart near the level's start, the
  player walked or ran right into both, standing still for 0 to 160
  frames after the first, so that the second was taken 30 to 207 frames
  after the first: while the first image was still flying (2 runs),
  resting (44), leaving (6) or gone (61). 21 of these 113 runs started
  with the figure count at 1 (the second pickup made it 3); 3 more took
  one figure with the count at 0, 1 and 2. In pictures with only
  sprites drawn, each place's 34 px column was compared with the tables
  from `P` to `A` + 80 (to `A` + 17 for a count of 3), apart from the new
  image before `A`: 59 585 place-frames over 229 pickups. 59 313 were
  as described and none different; on the other 272 the new image was
  passing through another place's column and the picture held the
  predicted rows and those of the passing image. Each new image arrived
  on `P` + 33, except the 2 still flying when the next figure was taken.

## Not covered

- The second character leading: its landings other than after a jump
  from standing onto the floor it started from (moving jumps, lower
  floor, slopes, after a bounce), and the states listed in the next
  item. No pickup in the random-input runs fell on a landing frame, so
  the landing rule rests on the figure-offset grids only. With the
  second character following, the first character's running and
  Down + Action ground action weren't measured.
- Being hit, bouncing, carrying, riding, falling without a jump, landing
  (the frames from `L`, the frame carrying `land`, on; except with the
  second character leading, after a jump from standing onto the floor it
  started from, which the Landing rule above covers), turning and the
  frame after a turn, and the stop after a roll.
- What a figure count of 3 leads to. In the one case measured, play
  left the level for another area about 260 frames after the third
  figure was taken.
- Whether the figure counts survive losing a life, leaving the level or
  entering another level, and figures in other levels.
- How a figure from the picture game is shown once it is taken, and
  whether the counts of animals 1, 3 and 4 lead to anything.
- How a figure is drawn before it is taken, the exact path of the
  flight, and the sound.
- Figure counts above 3, and a pickup with the count already at 3.
- Other levels.
