# Figures

Titles: `title-1`

A **figure** is a small golden image of an animal placed in a level. The
player takes it by touching it. Each figure taken adds one to the
**figure count**; it changes neither the collectible count
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
character leading and alone, in two ways. The pickup ranges were measured
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

The player has a **figure count**, which goes up by one on `P`. Taking
the first level's figure with the count at 0 made it 1; with the count
at 2, it made it 3. What a count of 3 leads to is not covered (see
"Not covered").

### How the taken figure is shown

Positions in this section are in the picture **made on frame `n`**: the
one drawn from the state at the end of frame `n`, shown once frame `n`
is over. The figure's image fits in a 34 × 34 px box; "the image at
(`a`, `b`)" means that box has its top-left pixel at (`a`, `b`).

1. **The flight.** From the picture made on `P` + 1, the image moves
   from where the figure was drawn toward its **resting place**, the
   image at (74, 2). Each frame it covers about an eighth of the
   distance left, horizontally and vertically, slowing as it gets near.
   Call **`A`** the first frame on which it is at its resting place; in
   the cases measured `A` was from `P` + 29 to `P` + 33 (the further the
   image has to travel, the later).
2. **Resting.** It stays at (74, 2) from `A` to `A` + 62.
3. **Leaving.** From `A` + 63 it moves up 4 px per frame: the bottom row
   of its box is at y 31 on `A` + 63, 27 on `A` + 64, and so on to 3 on
   `A` + 70; parts above the picture's top edge aren't drawn. From
   `A` + 71 on, nothing of it is drawn.

| Frame | Image at |
|---|---|
| `P` + 1 to `A` − 1 | moving toward (74, 2) |
| `A` to `A` + 62 | (74, 2) |
| `A` + 63 to `A` + 70 | (74, −2 − 4`k`) on `A` + 63 + `k` |
| `A` + 71 on | not drawn |

For example, with `A` = `P` + 33 for a figure taken on frame 100, the
image rests at (74, 2) from frame 133 to frame 195, rises from frame
196, and is not drawn from frame 204 on.

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

## Not covered

- The second character, leading or following.
- Being hit, bouncing, carrying, riding, falling without a jump, landing
  (the frames from `L`, the frame carrying `land`, on), turning and the
  frame after a turn, and the stop after a roll.
- What a figure count of 3 leads to. In the one case measured, play
  left the level for another area about 260 frames after the third
  figure was taken.
- Whether the figure count survives losing a life, leaving the level or
  entering another level, and other kinds of figure (other levels) and
  whether they share the count.
- How a figure is drawn before it is taken, the exact path of the
  flight, and the sound.
- Taking a figure while the image of an earlier one is still shown.
- Other levels.
