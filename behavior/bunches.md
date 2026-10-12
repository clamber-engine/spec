# Bunches

Titles: `title-1`

A **bunch** is a collectible worth ten. It is placed in a level, and the
player takes it by touching it, as with single collectibles
(`collectibles.md`). After that, its ten **items** are released one at a
time, and each one adds one to the collectible count. This file covers
when a bunch is taken, when each item is released and when each one
reaches the count.

Frames are logic frames at 60 Hz, numbered as in a trace. `x` grows to
the right. Horizontal positions are level positions as in `view.md`.
Vertical positions are **level y** as in `view-vertical.md`: they grow
downward, measured from the top of the level. The player's **feet line**
`F` is as in `view-vertical.md`.

Measured on the first level with the first character leading and alone.
The bunch was moved to chosen offsets from the player, one pixel apart.
The player was in poses reached by inputs only: standing, walking,
running, crouching, rolling, every frame of jumps (Jump held, released
early, from a run, facing left), and landing. Separately, the bunch was
moved to chosen places in and out of the picture as its items were
released. About 16 000 frames of play by inputs alone were then checked
against the rules below. These came from one route past one bunch and 80
variations of its approach jump (start, length, extra jumps, rolls,
crouches). That play included 56 pickups, all mid-jump, and each came on
the frame the rules give. In all 56, every item reached the count on the
frame the rules give, including 35 where some items flew and some were
counted at once.

## A bunch's position

A bunch does not move. Its **position** is a point (`xb`, `yb`) in the
level. Its image is drawn around that point and spins, so the image's
size changes from frame to frame. Every frame measured fell inside
columns `xb` − 13 to `xb` + 15 and rows `yb` − 15 to `yb` + 8. How a bunch
is stored in a level's data is not covered.

## When a bunch is taken

A bunch is taken on frame `n` when both of these hold **at the end of
frame `n`**, after the player has moved on that frame:

1. it hasn't been taken already;
2. the player's offsets from it (below) are inside the ranges for the
   player's state on frame `n` (see "Pickup ranges").

So a bunch is taken on the first frame that ends with the player in
range. Call this frame `P`.

A taken bunch stays taken for the rest of the visit to the level. In
one case the view was moved back over a taken bunch's place, and the bunch
did not come back. Whether it stays taken when the level is played again
is not covered.

### Offsets

Using whole pixels, let

- `X` be the player's `x` rounded down;
- **`I`** be how far the player is past the bunch in the direction the
  player is facing: `X − xb` when facing right, `xb − X` when facing
  left;
- **`D`** = `F − yb`, how many px the feet line is below the bunch's
  position (negative when it is above).

Because `I` is measured in the facing direction, each range below holds
for both facings. For example, a standing player facing right takes a
bunch with `X − xb` from −15 to 21. Facing left, it takes one with
`X − xb` from −21 to 15.

## Pickup ranges

A bunch is in range when `I` and `D` are both within the ranges for the
player's state (ends included).

| State | `I` | `D` |
|---|---|---|
| Standing, walking, running | −15 to 21 | −8 to 40 |
| Crouching (Down held on the ground) | −18 to 12 | −12 to 31 |
| Rolling | −13 to 21 | −4 to 38 |
| Jumping, from the take-off frame `T` to `T` + 5 | −15 to 21 | −8 to 40 |
| Jumping, from `T` + 6 on | −15 to 18 | 13 to 57 |

`T` is the frame carrying `jump_start` (`jump-arc.md`). For the first six
frames of a jump, starting with `T`, the ranges are the standing ones.
From `T` + 6 until the jump lands, the last row applies on every frame,
whatever the vertical speed and the horizontal speed, and whether Jump
is still held or not. (Single collectibles are different. Their jump
ranges depend on the vertical speed.)

**Landing.** When a jump lands on frame `L` (the frame carrying `land`)
and the player then stands still, the jump row still applies from `L`
to `L` + 3. The standing row applies from `L` + 4. Landing while
walking or running is not covered.

## Releasing the items

A taken bunch releases its ten items one at a time, on frames `P`,
`P` + 6, `P` + 12, … `P` + 54. Each item adds one to the collectible
count, either on the frame it is released or 17 frames later:

- An item **flies** to the count and arrives on its release frame + 17
  when, on its release frame, the bunch is both
  - at least 9 px right of the picture's left edge: `xb − V` ≥ 9; and
  - at least 11 px below the picture's top: `yb − W` ≥ 11.
- Otherwise the item is **counted at once**, on its release frame.

Here `V` is the view's horizontal position (`view.md`) and `W` its
vertical position (`view-vertical.md`), both as they were at the
**start** of the release frame (before the view moves on that frame),
rounded down to a whole px. The view position from the end of the frame
does not fit: in one natural run the bunch had `xb − V` = 8 by the end
of the release frame's view and its item still flew.

There is no matching edge on the right or at the bottom. Items flew
from bunches placed up to 999 px right of the picture's left edge and
up to 260 px below its top. So, as the view scrolls on, a bunch
whose place leaves the picture to the left while it is releasing items
flies its first items and counts the rest at once.

For example, a bunch taken on frame `P` while the view stays still
with the bunch well inside the picture adds one to the count on each of
`P` + 17, `P` + 23, `P` + 29, … `P` + 71. If the player then keeps
running right and the bunch's `xb − V` drops below 9 just before frame
`P` + 30, the first five items arrive on `P` + 17, `P` + 23, `P` + 29,
`P` + 35 and `P` + 41. The other five are counted on `P` + 30, `P` + 36,
`P` + 42, `P` + 48 and `P` + 54. So the count goes up twice in some
6-frame steps.

Each item adds one as in `collectibles.md`, including the change from
99 to 0 that gives one more life. The count going up past 99 from a
bunch was not measured.

## Not covered

- The second character, leading or following, touching a bunch.
- The ranges while bouncing off a walker (`bounce.md`), while being
  carried down a steep slope or a rounded ledge (`steep-faces.md`), while
  passing through a platform (`platforms-from-below.md`), while carrying
  an object (`carrying.md`), while falling without a jump, during the
  Down + Action ground action (`ground-action.md`), while turning, during the stop
  after a roll, and while being hit.
- Landing while walking or running.
- When a bunch that hasn't been taken appears and disappears as the
  view moves.
- How a bunch is drawn while its items are released, and the path the
  flying items take.
- How bunches are placed in a level's data.
