# Single collectibles

Titles: `title-1`

A **single collectible** is a small item placed in a level that the
player takes by touching it. Each one taken adds one to the
**collectible count**. When the count reaches 100, it starts again from
0 and the player gets one more life. This file covers when a single
collectible is taken, which ones can be taken depending on the view, and
when the count goes up. Other kinds of collectible are not covered.

Frames are logic frames at 60 Hz, numbered as in a trace. `x` grows to
the right. `vy` is as in a trace: down is positive, in px per frame.
Positions are in px. Horizontal positions are level positions as in
`view.md`. Vertical positions are **level y** as in `view-vertical.md`:
they grow downward, measured from the top of the level.

Measured on the first level with the first character leading, alone and
with the second character following, and with the second character
leading. The player was placed at chosen offsets from a collectible, one
pixel apart, in poses reached by inputs: standing, walking, running,
crouching, rolling, the Down + Action ground action, and every frame of
jumps whose Jump input was held or released early. Separately, about
107 000 frames of play by inputs alone were checked against the rules
below. These came from one route through the level and 150 variations
of it with jumps, rolls and crouches added at random, all on the first
level. That play included 226 pickups in the states covered here, by
both characters, and each one came on the frame the rules give. Another
206 pickups happened in states listed under "Not covered" and weren't
checked.

## A collectible's position

A single collectible is drawn as a 16 × 16 px **image** and does not move.
Its **position** is the point (`xc`, `yc`) where

- `xc` is the level `x` of the image's leftmost column, plus 8;
- `yc` is the level y of the image's top row, plus 8.

All collectibles measured had their images at level positions that
are multiples of 8 px.

## When a collectible is taken

A collectible is taken on frame `n` when all of these hold **at the end
of frame `n`**, after the player and the view have moved on that frame:

1. it hasn't been taken already;
2. it is inside the view window (see "The view window");
3. the player's offsets from it (below) are inside the ranges for the
   player's character and state on frame `n` (see "Pickup ranges").

So a collectible is taken on the first frame that ends with the player
in range. For example, a player running right at 3 px per frame whose
offset `I` goes from −11 at the end of one frame to −8 at the end of the
next, with a range starting at −8, takes the collectible on the second
frame.

Only the character under control takes collectibles. A following
character standing on a collectible doesn't take it, whichever of the
two characters is following.

A taken collectible is no longer drawn and stays taken for the rest of
the visit to the level, including when it leaves the view and comes back.
Whether it stays taken when the level is played again is not covered.

### Offsets

Using whole pixels, let

- `X` be the player's `x` rounded down;
- `F` be the player's **feet line** as in `view-vertical.md` (the
  player's level y rounded up);
- **`I`** be how far the player is past the collectible in the direction
  the player is facing: `X − xc` when facing right, `xc − X` when facing
  left;
- **`D`** = `F − yc`, how many px the feet line is below the
  collectible's position (negative when it is above).

Because `I` is measured in the facing direction, each range below holds
for both facings. For example, a first character standing still and
facing right takes a collectible with `X − xc` from −16 to 22. Facing
left, it takes one with `X − xc` from −22 to 16.

## Pickup ranges

A collectible is in range when `I` and `D` are both within the ranges
for the player's character and state (ends included).

### First character

| State | `I` | `D` |
|---|---|---|
| Standing, walking, running, Down + Action ground action | −16 to 22 | −7 to 41 |
| Crouching (Down held on the ground) | −19 to 13 | −11 to 32 |
| Rolling | −14 to 22 | −3 to 39 |
| Jumping, from the take-off frame `T` to `T` + 6 | −16 to 22 | −7 to 41 |
| Jumping, from `T` + 7 on | −16 to 19 | see below to 58 |

`T` is the frame carrying `jump_start` (`jump-arc.md`). For the first
seven frames of a jump, starting with `T`, the ranges are the standing
ones, whatever the speed. From `T` + 7 on, `I` runs from −16 to 19 and
`D` runs up to 58. Its lower end depends on the vertical speed at the
end of the frame:

| `vy` at the end of the frame | Lower end of `D` |
|---|---|
| −4.5 or less (rising at 4.5 px per frame or faster) | 20 |
| more than −4.5, up to −3.5 | 21 |
| more than −3.5, up to −2.5 | 22 |
| more than −2.5, up to 0 | 14 |
| more than 0 (falling) | 16 |

The boundary at −4.5 wasn't measured exactly. Rising speeds of 4.5625 px
per frame and faster gave 20, and 4.46875 or slower gave 21. A speed of
exactly 4.5 wasn't seen. The other boundaries were seen on both sides:
3.5 gave 21, 2.5 gave 22, 0 gave 14.

The table holds for jumps whose Jump input was held throughout and for
jumps where it was released early. In both, the frames from `T` + 7 on
follow the speed in the same way.

**Landing.** When a jump lands on frame `L` (the frame carrying `land`)
and the player then stands still, the ranges of the last row above,
with a falling speed (`I` −16 to 19, `D` 16 to 58), still apply from
`L` to `L` + 6. The standing ranges apply from `L` + 7. When the player
keeps walking or running after landing, the switch comes earlier. In
the one case measured, the standing ranges applied from `L` + 5. Landing
while moving is not covered.

### Second character (leading)

| State | `I` | `D` |
|---|---|---|
| Standing | −13 to 12 | −9 to 39 |
| Walking, rolling, jumping (all frames) | −19 to 13 | −5 to 39 |
| Crouching | −20 to 15 | −8 to 16 |

With the second character leading, Down + Action gives a roll, so it
uses the rolling row. Its jumps have no early or speed-dependent ranges.
The same ranges applied on every frame measured, from the take-off frame
until the frame before landing. Its landings are not covered. In one
case, landing while walking, it took a collectible at `I` = −17 and not
at −19 on the frame before, 12 frames after landing.

## The view window

A collectible can only be taken while it is near the view. With `V` the
view's position (`view.md`) at the end of the frame, after the view has
moved on that frame, rounded down to a whole pixel, the collectible is
**inside the view window** when

    −39 ≤ xc − V ≤ 263

That is, the image's leftmost column is anywhere from 47 px left of the
picture's left edge up to the picture's last column (the picture is
256 px wide, `view.md`). A player in range of a collectible outside the window
doesn't take it. The collectible stays in the level and can be taken
later, once it is inside the window.

The view position from the start of the frame (before the view's move
on that frame) does not fit the measurements. Only horizontal positions
were measured. The window's vertical limits are not covered.

## When the count goes up

Call `P` the frame on which a collectible is taken, and `V` the view
position used for it (as in "The view window"). The count goes up by one:

| `xc − V` on frame `P` | Count goes up on |
|---|---|
| 8 to 255 (the image's leftmost column in the picture's columns 0 to 247) | `P` + 33 |
| 256 to 263 (the image's leftmost column in the picture's columns 248 to 255) | `P` + 1 |
| −39 to 7 (the image's leftmost column left of the picture's column 0) | never |

So a collectible taken while its image is partly off the left edge of
the picture is taken but doesn't count. In ordinary play this is rare,
because the player is near the middle of the picture. It was measured by
placing the player there.

The 33-frame delay is the same everywhere from 8 to 255. It was measured
with the collectible about 80 px below the top of the picture. Other
heights weren't measured.

When the count goes up from 99, it becomes 0 instead, and on the same
frame the number of lives goes up by one. Two counts arriving on the
same frame and counts above 100 are not covered.

## Not covered

- Other kinds of collectible. Bunches, worth ten, are in `bunches.md`;
  letters are in `letters.md`.
- The ranges while bouncing off a walker (`bounce.md`), while being
  carried down a steep slope or a rounded ledge (`steep-faces.md`), while
  passing up or down through a platform (`platforms-from-below.md`),
  while carrying an object (`carrying.md`), while falling without a
  jump, while turning, during the stop after a roll, while being hit or
  knocked out, and while the second character takes over
  (`losing-a-character.md`) or during a swap (`swap.md`).
- Landing while walking or running (see "Landing"), and any landing of
  the second character.
- The first frame after a turn ends. In one case the second character
  didn't take a collectible at `I` = −19 on that frame and took it at
  −17 on the next.
- The second character's running state, which wasn't reached in the
  runs measured. Its rolls were measured.
- The view window's vertical limits.
- How collectibles are placed in a level's data.
