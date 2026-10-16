# Thrown objects: bouncing, rolling and breaking

Titles: `title-1`

What a thrown object that holds nothing does from the moment it is
thrown until it is gone: how it bounces on the floor, how it then rolls,
how it breaks against a wall, and when it is removed after leaving the
picture. `carrying.md` covers the throw itself and the flight up to the
first touch; this file carries on from there. An object that holds the
missing character breaks the first time it touches anything
(`following.md`, "Joining") and is not covered here.

Frames are logic frames at 60 Hz, numbered as in a trace. `x`, `y`,
`vx` and `vy` are as in a trace: right and down are positive, speeds in
px per frame. `y` is a level y as in `view-vertical.md`. Speeds are
whole multiples of 1/256 px per frame and are written both ways, as in
`ground-speed.md`. **The speed rule** is the rule of `ground-speed.md`
(gap to the target, step = gap ÷ 8 rounded toward zero, the target
itself once the step is 0). Columns, face columns and "higher" are as in
`steep-faces.md`: the floor has one height per 1-px-wide column of `x`,
the object's column is the one containing its `x`, and higher means
higher on screen (smaller `y`). Q is the frame Action is let go, as in
`carrying.md`: the object leaves the player's hands on Q + 21.

Measured on the first level with the lead character alone: 135 throws by
inputs alone (standing, after walking, after turning, and with Action
let go on every frame from 3 to 40 after a jump press, both ways), 6 978
frames of thrown objects in all, ending in 62 breaks against walls and
73 removals; and about 800 controlled single frames in which the
object's height, position or speeds were set just before it reached
flat floor or a face, to find the limits below. The floor there was
flat, slopes of 1 px per 4 columns rising and falling, and faces of 4 px
per column.

## Each frame

From Q + 22 on, every frame, in this order:

1. `vx` follows the speed rule toward a target of **3.5** (896/256) in
   the direction the object was thrown. This goes on through bounces
   and rolling; nothing else changes `vx`.
2. **Breaking**: if the object would break against a wall (below), it
   breaks on this frame and nothing else happens.
3. `x` moves by the new `vx`.
4. The object moves vertically: in the air as in "In the air" below,
   rolling as in "Rolling".
5. **Leaving the picture**: if it is now far enough outside the picture
   (below), it is removed.

## In the air

While it hasn't started rolling:

1. The new `vy` is `vy` + **0.4375** (112/256), but never above **8**
   (2048/256), as in `carrying.md`.
2. Take the new `y` = `y` + new `vy`, and the floor `F` (a level y) of
   the column of the object's new `x`.
3. It **touches the floor** if the new `vy` is above 0 (moving down) and
   the new `y` is less than **5 px** above the floor: `y` > `F` − 5.
   Otherwise it simply moves to the new `y` with the new `vy`. A new
   `vy` of exactly 0 never touches.
4. When it touches, its `y` becomes `F` exactly (a whole number), and
   what happens depends on its `vy` from **before** this frame (call it
   `v`, without this frame's 0.4375):
   - `v` of **1** (256/256) or more, that is moving down at least 1 px
     per frame: it **bounces**. Its `vy` becomes −⌊`v` ÷ 2⌋, in 1/256
     px (moving up at half the speed, the half rounded down: 257/256
     gives −128/256, 259/256 gives −129/256).
   - Otherwise, including when `v` was 0 or upward: it **starts
     rolling**.

So a falling object bounces every time it lands while it still had at
least 1 px per frame of speed, each bounce half as fast as the fall
before it, and the first landing slower than that makes it roll. A
throw from standing on flat floor bounces twice (see "Example").

The 5-px margin is measured: on flat floor it touched when the new `y`
was 4.98 px above the floor and didn't at 5 px exactly.

## Rolling

Once rolling, the object stays on the floor: each frame its `y` is the
floor of the column of its new `x`, a whole number. It doesn't bounce
or leave the floor again on the floors measured (flat, and slopes of
1 px per 4 columns going up and going down). It keeps rolling at 3.5 px
per frame until it breaks or is removed; it never stops on its own.

## Breaking against a wall

On a frame whose move would take `x` into a **different column** that

- is a face column of a face rising in the direction the object is
  moving, of ground that is not passable from below
  (`../formats/level-ground.md`), and
- has its floor higher than the object's `y` at the start of the frame
  (before this frame's move, up or down),

the object **breaks**. It doesn't move on that frame and is gone: it is
never seen in that column. Pieces fly from where it was (how they move
is not covered). The player isn't held up: walking or standing goes on
as usual on that frame and after it.

This holds rolling and in the air. In the air, an object whose `y` is
at or above the face column's floor isn't stopped: it moves into the
column and can land on it as in "In the air" (measured: with the face
column's floor 1 px higher than the floor before it, breaking up to
0.5 px below the face column's floor, landing from level with it; with
a face column 13 px higher, breaking up to 0.5 px below, landing from
level).

Even a face column only 1 px higher than the object breaks it, while a
slope of 1 px per 4 columns (no face columns) doesn't. Rolling into a
face, the object breaks on the frame whose move would take it into the
face's first column, not before.

A broken object doesn't come back: in the runs measured, the view was
moved until the object's starting place was well out of the picture and
back, and nothing appeared there again.

## Leaving the picture

Let `V` be the view's position rounded down to a whole pixel (as in
`walker-view.md`) and `X` the object's new `x` rounded down. The object
was removed on frames where `X` − `V` was **385** or **386**, and never
while `X` − `V` was 382 or less; 383 and 384 weren't seen. This is the
limit `walker-view.md` gives for walkers (`X` − `V` ≥ 385), so at least
129 px right of the first pixel past the picture's right edge. Being
removed on the left wasn't seen. A removed object didn't come back
while its starting place stayed near the picture (the only case
measured).

## Other objects

A thrown object rolls past an object holding the missing character
without either one being affected (seen in every run that rolled past
one).

## Example

All values are computed from the rules above. Thrown to the right while
standing on flat floor; `x` and `y` from the object's position on
Q + 21, so the floor is at `y` = 54. A face rising to the right starts
200 px from there: its first column (`x` 200) has its floor at 53, 1 px
higher than the flat floor, and each column after it is 4 px higher
again. Speeds in 1/256 px per frame.

| Frame | `x` | `y` | `vx` | `vy` | |
|---|---|---|---|---|---|
| Q + 21 | 0 | 0 | 4096 | 256 | leaves the hands |
| Q + 22 | 14.4375 | 1.4375 | 3696 | 368 | |
| Q + 30 | 92.71875 | 28.6875 | 1860 | 1264 | |
| Q + 33 | 111.9296875 | 46.125 | 1543 | 1600 | |
| Q + 34 | 117.64453125 | 54 | 1463 | −800 | new `y` 52.8125 is less than 5 px above: bounces (1600 → −800) |
| Q + 35 | 123.0859375 | 51.3125 | 1393 | −688 | |
| Q + 41 | 151.60546875 | 44.375 | 1121 | −16 | top of the bounce |
| Q + 42 | 155.875 | 44.75 | 1093 | 96 | |
| Q + 45 | 168.1640625 | 48.5 | 1029 | 432 | |
| Q + 46 | 172.12109375 | 54 | 1013 | −216 | bounces (432 → −216) |
| Q + 47 | 176.0234375 | 53.59375 | 999 | −104 | |
| Q + 48 | 179.87890625 | 54 | 987 | — | touches with `v` = −104: starts rolling |
| Q + 49 | 183.69140625 | 54 | 976 | — | |
| Q + 53 | 198.61328125 | 54 | 945 | — | |
| Q + 54 | — | — | — | — | the move would take `x` to 202.28, a face column whose floor (45) is higher than `y` 54: breaks |

## Not covered

- Hitting a character or a walker (a thrown object was seen to defeat a
  walker, with the game holding the player still for about half a
  second), and other enemies.
- Being in the air over a slope: rising over floor that rises under it,
  the object was sometimes lifted 1 or 2 px higher than the rule above
  gives (all 158 such frames were above slopes; none on flat floor).
- A move that carries `x` past face columns into a column that isn't
  one (seen three times, the object broke each time), ground other than
  faces that is higher than the object, such as the edge of a platform
  that is passable from below (seen to break it in some cases and not in
  others), and faces of other steepness.
- Landing on floor up to 8 px higher than the object, seen in controlled
  frames at the edge of a platform; whether this holds generally.
- Rolling off a ledge or down a vertical step, and slopes other than
  1 px per 4 columns.
- How the pieces of a broken object move and are drawn, and how the
  object is drawn while bouncing and rolling.
- Removal on the left of the picture, and `X` − `V` of 383 and 384.
- Objects thrown by the second character, and the object holding the
  missing character after it touches something.
