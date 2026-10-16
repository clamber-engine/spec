# The view

Titles: `title-1`

The **view** is the part of the level shown on screen. It is 256 px wide.
This file covers how the view moves horizontally to follow the player:
where it aims, how it eases toward that point, and the frames on which
it stays still. The view's vertical motion is in `view-vertical.md`.

Frames are logic frames at 60 Hz, numbered as in a trace. `x` grows to
the right. `x`, `vx` and facing are the player's values for the frame,
as in a trace. They are taken after the player has moved on that frame.
Positions are level positions in px. Only differences between them
matter here.

This was measured with the first character leading, alone, on the first
level. It was measured at least 256 px from the level's left end, while
the player stood, walked, ran, turned, jumped, fell, rolled, used the
Down + Action ground action, bounced off walkers, and picked up and
carried objects. The view was measured over about 31 000 frames.

## The view's state

The view has:

- a **position** `V`: the level `x` of the left edge of the picture,
  kept to 1/256 px. The picture is drawn with its left edge at `V`
  rounded down to a whole pixel. The ground layer moves exactly with
  it. Background layers that move at other rates aren't covered.
- a **speed** `S` in px per frame, kept to 1/8192 px per frame.

## The target

On each frame the view aims at a **target** position:

    target = x + lead

`lead` is a whole number of px. It is the sum of three parts:

1. **Base:** −71 when the player faces right, and −127 when the player
   faces left.
2. **Facing part:** let `g` = ⌊4 × |vx|⌋ + ⌊2 × |vx|⌋. Add `g` when the
   player faces right, and subtract it when the player faces left. This
   part uses the player's facing, not the direction of motion. During a
   turn the player can move one way while facing the other.
3. **Speed part:** ⌈16 × vx⌉ + ⌈8 × vx⌉, with `vx` signed and both
   values rounded up (toward +∞).

Some values of `lead`:

| `vx` (px per frame) | facing right | facing left |
|---|---|---|
| 0 | −71 | −127 |
| 0.0625 | −69 | −125 |
| 0.125 | −68 | −124 |
| 0.25 | −64 | −122 |
| 0.5 | −56 | −118 |
| 1 | −41 | −109 |
| 2 | −11 | −91 |
| 3 | 19 | −73 |
| −0.0625 | −72 | −128 |
| −2 | −107 | −187 |
| −3 | −125 | −217 |

So the view settles with a player who is standing still 71 px from its
left edge when the player faces right, and about 127 px from it when
the player faces left. A moving player is placed further back from the
direction of motion.

## Following the target

On each frame, after the player has moved:

1. Work out the target from this frame's `x`, `vx` and facing.
2. Let `D` = target − `V`, using the view's position from before this
   frame.
3. Move `S` one eighth of the way toward `D / 32`:
   `S` ← `S` + (`D` / 32 − `S`) / 8, rounded down (toward −∞) to a
   multiple of 1/8192 px per frame.
4. `V` ← `V` + `S`, with `S` rounded toward zero to a multiple of
   1/256 px.

The target is a whole number of px plus the fraction of `x`, so `D` is a
multiple of 1/256 px. `D` / 32 is therefore always a multiple of 1/8192.

Because of the rounding, the view doesn't always settle exactly on the
target. At a steady 2 px per frame to the right, `S` settles at
16408/8192 and the view moves exactly 2 px per frame, about 62 px behind
the target (the player is about 73 px from the left edge). With a player
who is standing still, the view can stop up to 38/256 px from the
target. A small `S` (under 32/8192) can be left over that moves it 0 px
per frame. Example (b) ends this way.

## Frames on which the view stays still

On the frame of a bounce off an enemy (**B**, `bounce.md`) and on
**B + 1**, the view does nothing. The target, `V` and `S` all keep their
values from the frame before. This also applies to a bounce made while
already bouncing. It continues normally from **B + 2**, using that
frame's `x` (the player is still frozen on B + 2).

The same applies on the frame of a roll defeat (**K**,
`rolling-into-walkers.md`) and on **K + 1**, and on the frame of a
knock-out during the ground action (**K**, `ground-action-enemies.md`)
and on **K + 1**. The view continues from **K + 2**.

## Examples

Synthetic, computed from the rules above. The view starts settled on a
player standing at `x` = 1000 and facing right (`V` = 929, `S` = 0).

**(a)** From frame 1 the player moves right at exactly 2 px per frame.
This isn't how the player speeds up (`ground-speed.md`); it only shows
the view.

| Frame | `x` | target | `V` | `S` | `x` − `V` |
|---|---|---|---|---|---|
| 1 | 1002 | 991 | 929.2421875 | 1984/8192 | 72.7578125 |
| 2 | 1004 | 993 | 929.703125 | 3776/8192 | 74.296875 |
| 3 | 1006 | 995 | 930.359375 | 5393/8192 | 75.640625 |
| 10 | 1020 | 1009 | 939.0546875 | 12972/8192 | 80.9453125 |
| 20 | 1040 | 1029 | 958.19140625 | 17147/8192 | 81.80859375 |
| 30 | 1060 | 1049 | 979.90625 | 18078/8192 | 80.09375 |
| 60 | 1120 | 1109 | 1044.73828125 | 17212/8192 | 75.26171875 |
| 100 | 1200 | 1189 | 1126.59375 | 16535/8192 | 73.40625 |
| 200 | 1400 | 1389 | 1326.90625 | 16408/8192 | 73.09375 |

Frame 1 worked through: the target is 1002 − 11 = 991, so `D` = 62 and
`D` / 32 = 15872/8192. `S` = 0 + 15872/8 = 1984 (in 1/8192). `V` moves
by 1984/8192 = 62/256 px.

**(b)** The player stays at `x` = 1000 with `vx` = 0 and faces left
from frame 1.

| Frame | target | `V` | `S` | `x` − `V` |
|---|---|---|---|---|
| 1 | 873 | 928.78125 | −1792/8192 | 71.21875 |
| 2 | 873 | 928.375 | −3353/8192 | 71.625 |
| 3 | 873 | 927.80078125 | −4706/8192 | 72.19921875 |
| 10 | 873 | 920.82421875 | −9876/8192 | 79.17578125 |
| 20 | 873 | 908.03515625 | −10279/8192 | 91.96484375 |
| 30 | 873 | 896.875 | −8100/8192 | 103.125 |
| 60 | 873 | 879.296875 | −2481/8192 | 120.703125 |
| 100 | 873 | 873.94921875 | −388/8192 | 126.05078125 |
| 200 | 873 | 873.06640625 | −24/8192 | 126.93359375 |

From frame 200 nothing changes. `D` = −17/256 px, so `D` / 32 =
−17/8192, and `D` / 32 − `S` = 7/8192. An eighth of that rounds down to
0. `S` = −24/8192 rounds toward zero to 0 px.

## Not covered

- Near the ends of a level. Within about 210 px of the left end, the
  target was seen to differ from the rule by −1, +2 or +4 px. The view
  was also seen to go past the left end. The right end, and any limits
  on the view, weren't measured.
- Where the view starts when a level begins, and after the player falls
  out of a level.
- While the player is knocked out (`knock-out.md`), loses a character
  (`losing-a-character.md`), throws an object, or falls into a pit. In
  all of these the target was seen to stop following the rule.
- The second character leading (`swap.md`).
- Layers that move at other rates than the ground.
- Which frame's view the picture of a frame is drawn with, relative to
  the sprites drawn in it.
