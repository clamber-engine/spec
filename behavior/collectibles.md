# Single collectibles

Titles: `title-1`, `title-2`

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

What the picture shows of the count, and of the new life, is in
`count-display.md`.

## In `title-2`

Everything in this section was measured on `title-2` only, on the first
level, with the lead character alone (the other character hadn't joined
yet). `x`, `y` and `vy` are as in a trace (`y` grows downward). The jump
frames G and L are as in `jump-input.md` and `jump-arc.md` ("In
`title-2`"), the roll frames S and D as in `roll-speed.md` ("In
`title-2`"), and turns and the crouch frame U as in `ground-speed.md`
("Turns (`title-2`)", "Crouching (`title-2`)").

### A collectible's position (`title-2`)

As in `title-1`, a single collectible is drawn as a 16 × 16 px image and
doesn't move. Its position (`xc`, `yc`) is the level `x` of the image's
leftmost column plus 8, and the level `y` of its top row plus 8. Level
positions use the same units as the player's `x` and `y`: a player
standing still on the ground has `y` whole, and the lowest row drawn for
the player is at level `y` − 1. All collectibles measured had their
images at level positions that are multiples of 8 px.

### When a collectible is taken (`title-2`)

A collectible is taken on frame `n` when, **at the end of frame `n`**,
after the player has moved on that frame:

1. it hasn't been taken already, and
2. the player's offsets from it (below) are inside the ranges for the
   player's pose on `n` (see "Poses (`title-2`)" and "Ranges
   (`title-2`)").

So, as in `title-1`, a collectible is taken on the first frame that ends
with the player in range. A taken collectible is no longer drawn.

### Offsets (`title-2`)

Round the player's position down to whole pixels: `X` = `x` rounded
down, and `Y` = `y` rounded down (the whole-pixel part of `y`, since `y`
grows downward). Then

- **`I`** = `X` − `xc` when facing right, `xc` − `X` when facing left
  (how far the player is past the collectible in the facing direction);
- **`D`** = `Y` − `yc` (how many px the player's position is below the
  collectible's; negative when it is above).

On the ground `y` is whole, so `D` is the same as in `title-1`. In the
air, where `y` has a fraction, `D` is one less than the `title-1`
formula (which rounds up) would give.

### Poses (`title-2`)

The ranges depend on the player's **pose on frame `n`**, the pose the
player has at the end of `n`:

- **In the air**: from the frame G on which a jump starts (G and G + 1
  count, although the player is still on the ground) to the jump's
  landing frame, and then for 17 more frames (landing frame + 17). If a
  direction and Action are held through the landing, so that the player
  runs on, it ends at landing frame + 8 instead (as in
  `walker-contact.md`).
- **In the air, after walking off a ledge**: with the leave frame L and
  the landing frame A named as in `walk-off.md` ("In `title-2`"), from
  L + 1 to A + 18. On L itself the walking ranges still apply.
- After a jump's landing, Down, Action or Jump during the frames after
  landing starts a crouch, roll or jump at once, and that pose applies
  from its first frame. A direction alone (walking on) doesn't end them,
  after a jump or a walk-off.
- **Rolling**: S to D + 13. That is the roll's 31 frames, D, and, when
  the roll ends in a stop, the stop's first 13 frames (D + 1 to D + 13).
  The standing ranges apply from D + 14, one frame before the stop ends.
  When the roll carries on into running with Action held, the running
  ranges apply from D.
- **Turning**: the frames of a turn, T to T + 7 (short turns weren't
  measured).
- **Crouching**: from the frame a crouch starts to U + 10.
- **Running**, **walking** and **standing**: as in `walker-contact.md`
  ("Poses (`title-2`)"), including while slowing down. The ranges switch
  from walking to standing, and from running to walking, at the same
  points as there.

### Ranges (`title-2`)

A collectible is in range when `I` and `D` are both within the ranges
for the pose (ends included). The same ranges hold facing left and
facing right, because `I` is measured in the facing direction.

| Pose | `I` | `D` |
|---|---|---|
| Standing | −16 to 16 | −4 to 41 |
| Walking | −18 to 14 | −4 to 41 |
| Running | −21 to 15 | −1 to 37 |
| Turning | −14 to 14 | −4 to 38 |
| Rolling | −18 to 13 | −4 to 36 |
| Crouching | −16 to 16 | −6 to 27 |
| In the air | −19 to 15 | −5 to 33 |

Unlike `title-1`, the ranges in the air don't change with the time since
take-off or with `vy`. One range applies from G to the end of the
frames after landing, for held and released jumps, standing and moving
jumps, and for falls. The ranges don't depend on speed: walking at its first
frame and at full speed, and running at any speed, gave the same ranges.

Example (synthetic, positions made up): a player standing still facing
right at `x` = 1000.5, `y` = 200, and a collectible at (`xc`, `yc`) =
(1010, 184). Then `X` = 1000, `I` = −10 and `D` = 16, so the
collectible is taken on the first frame. Facing left, `I` = 10 and it
is taken as well. A collectible at (1022, 184) gives `I` = −22 facing
right: out of range standing, walking, rolling or turning, and still out
of range running (from −21). At (1018, 184), `I` = −18: out of range
standing (from −16), in range walking or rolling (from −18) and running
(from −21).

### The count (`title-2`)

Call `P` the frame on which a collectible is taken. The collectible
count goes up by one on `P` + 33. When it goes up from 99, it becomes 0
instead, and the number of lives goes up by one on the same frame.

This held for every pickup measured, with the image's leftmost column
from 5 px left of the picture's left edge to 246 px right of it (the
picture is 256 px wide). So, unlike `title-1`, a collectible whose image
is partly off the left edge of the picture still counts, at least down
to 5 px off. No pickup was refused because of the collectible's place in
the picture, within that span. Places further out weren't reached: in
these measurements the player was always at least 16 px inside the
picture's left and right edges.

### Evidence (`title-2`)

- Placement: the player's pose came from inputs (standing, walking from
  its first frame, running, turning, rolls ending in a stop and carrying
  on into running, crouching down, crouched and getting up, jumps held,
  released after 3 and 8 frames, and with a direction held, the landing
  frames, falls after walking off a ledge). Then a collectible was put at
  every whole-pixel offset in a grid of 69 `I` × 103 `D` values around
  the player, and one frame was run. About 3.4 million placements, both
  facings for standing, walking, running, rolling and jumps. On every
  frame measured, the taken offsets filled the range for the pose
  exactly, with nothing taken outside it.
- The count: 567 pickups at different places in the picture, by moving
  the player and the collectible together, and pickups by walking into
  collectibles. Every one counted on `P` + 33. The rollover was checked
  from 99.
- Falls after walking off a ledge: from the 13th frame in the air on,
  the whole range was measured, through the frames after landing. On earlier
  frames of the fall only part of the range, or none of it, could be
  measured, because collectibles placed that high weren't drawn in that
  part of the level. What was measured there matched the range.
- Natural play: about 148 000 frames of play by inputs along one route
  through the first level and 250 variations of it with jumps, rolls,
  crouches, turns and stops added at random. There were 431 pickups in
  the poses covered here, and each came on the frame the rules give,
  with no other frame predicted. Another 54 happened in states listed
  under "Not covered in `title-2`" and weren't checked.

### Not covered in `title-2`

- The view window: places in the picture outside the span above, and
  collectibles above or below the picture.
- Going up or down the stair-like steps of a level.
- The exact frames of the switches from walking or running to standing
  and from running to walking (as in `walker-contact.md`), and turns
  started during the frames after landing.
- Bouncing off a walker and being hit or knocked out (the 54 unchecked
  pickups in natural play happened in these), carrying, the second
  character, and other kinds of collectible.
- Two counts arriving on the same frame, and whether taken collectibles
  stay taken when the level is played again.

## Not covered

This list is for `title-1`; `title-2` has its own list above.

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
