# The view

Titles: `title-1`, `title-2`

The **view** is the part of the level shown on screen. It is 256 px wide.
This file covers how the view moves horizontally to follow the player:
where it aims, how it eases toward that point, and the frames on which
it stays still. The view's vertical motion is in `view-vertical.md`.

Frames are logic frames at 60 Hz, numbered as in a trace. `x` grows to
the right. `x`, `vx` and facing are the player's values for the frame,
as in a trace. They are taken after the player has moved on that frame.
Positions are level positions in px. Only differences between them
matter here.

The sections below up to "In `title-2`" give the `title-1` rule.
"In `title-2`" gives the `title-2` rule, which works differently.

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

## In `title-2`

Everything in this section was measured on `title-2` only, with the
lead character alone, on the first level. It was measured away from the
ends of the view's range, while the player stood, walked, ran, turned,
jumped, fell, rolled, defeated walkers by rolling and bounced off them.
The view works differently from `title-1`. It aims at a point a fixed
distance behind the player, which moves in one step when the player
turns. The view's speed then changes by limited amounts toward that
point. The first level also adds a slow sway.

`x` and facing are the player's values for the frame, as in a trace,
taken after the player has moved on that frame. Positions are level
positions in px, kept to 1/256 px. Only differences between them matter
here. Speeds are in 1/256 px per frame.

### The view's state (`title-2`)

The view has:

- a **position** `V`: the level `x` of the left edge of the picture,
  kept to 1/256 px. The picture is drawn with its left edge at `V`
  rounded down to a whole pixel. The ground layer moves exactly with
  it.
- a **speed** `S`, a whole number of 1/256 px per frame, signed.
- the **aim** of the previous frame, `P`.

### The aim (`title-2`)

On each frame the view aims at:

    aim = x − offset − sway

- `offset` is **56** px when the player faces right and **128** px when
  the player faces left. Unlike `title-1`, it doesn't depend on the
  player's speed. On the frame a turn changes the facing, the aim moves
  by 72 px at once. The view itself then follows by the rule below.
- `sway` is 0 except on the first level. There it is a whole number of
  px that changes by 1 every 8 frames: it goes up from 0 to 15, stays at
  15 for a second 8 frames, goes back down to 0, and stays at 0 for a
  second 8 frames, so it repeats every 256 logic frames. The steps follow
  logic frames: the count stops on frames on which no logic frame runs,
  and it goes on through the frozen frames of a bounce or a roll
  defeat. While the player stands still, the
  view drifts back and forth by up to 15 px.

### Following the aim (`title-2`)

On each frame, after the player has moved, with every value in 1/256 px
(or 1/256 px per frame for `S`):

1. Work out this frame's `aim`.
2. **Pull:** (`aim` − `V`) / 16, rounded down (toward −∞), then limited
   to the range −96 to +96.
3. **Lag:** `G` = `P` − `V`. If `G` is from −32 to 31, keep it.
   Otherwise divide it by 16, rounded down. Then raise the result to at
   least 8 if `G` was positive, or lower it to at most −8 if `G` was
   negative.
4. **Goal:** `D` = (`aim` − `P`) + the lag from step 3. This is how much
   the aim moved on this frame, plus part of how far the view was behind
   the previous aim.
5. **Speed:**
   - If `D` is 0, `S` becomes 0.
   - Otherwise add the pull to `S`. If `S` and `D` now have different
     signs (0 counts as positive), halve `S`, rounding down (toward
     −∞). If they have the same sign, `S` mustn't go past `D`: if `D`
     is positive, `S` becomes the smaller of `S` and `D`, and if `D` is
     negative, the larger.
6. `V` ← `V` + `S`.
7. `P` ← `aim`.

So `S` builds up by at most 96/256 px per frame on each frame. `S` stays
within the goal `D`, so the view keeps pace with the aim and closes the
rest of the gap by about one sixteenth of it per frame. When it has
caught up, it stops exactly on the aim. Unlike `title-1`, the view
settles exactly: on the aim when the aim is still, and exactly
`offset` + `sway` behind the player when the player moves at a steady
speed.

### Frames on which the view stays still (`title-2`)

On the frame of a bounce off an enemy (**B**, `bounce.md`, "In
`title-2`") and on **B + 1**, `V` and `S` keep their values from the
frame before. This also applies to a bounce made while already
bouncing. The same applies on the frame of a roll defeat (**K**,
`rolling-into-walkers.md`, "In `title-2`") and on **K + 1**. The aim is
worked out as usual on these frames, and `P` becomes it. The view
continues normally from B + 2 (or K + 2), using that frame's `x` (the
player is still frozen on that frame).

### Examples (`title-2`)

Synthetic, computed from the rules above with `sway` 0. The view starts
settled on a player standing at `x` = 1000 and facing right (`V` and
`P` = 944, `S` = 0).

**(a)** From frame 1 the player moves right at exactly 2 px per frame.
This isn't how the player speeds up (`ground-speed.md`); it only shows
the view.

| Frame | `x` | aim | `V` | `S` | `x` − `V` |
|---|---|---|---|---|---|
| 1 | 1002 | 946 | 944.125 | 32 | 57.875 |
| 2 | 1004 | 948 | 944.4921875 | 94 | 59.5078125 |
| 3 | 1006 | 950 | 945.203125 | 182 | 60.796875 |
| 5 | 1010 | 954 | 947.75 | 374 | 62.25 |
| 10 | 1020 | 964 | 958.88671875 | 599 | 61.11328125 |
| 20 | 1040 | 984 | 981.3046875 | 557 | 58.6953125 |
| 40 | 1080 | 1024 | 1023.23046875 | 525 | 56.76953125 |
| 60 | 1120 | 1064 | 1063.90625 | 520 | 56.09375 |
| 100 | 1200 | 1144 | 1144 | 512 | 56 |

From frame 61 the view is exactly 56 px behind the player, and from
frame 62 it moves exactly 2 px per frame (`S` = 512).

Frame 2 worked through: the pull is (948 − 944.125) × 256 / 16 = 62. The
lag is `G` = (946 − 944.125) × 256 = 480, which is outside −32 to 31,
so it becomes 480 / 16 = 30. The goal is `D` = 2 × 256 + 30 = 542. `S`
= 32 + 62 = 94, which doesn't go past 542. `V` moves by 94/256 px.

**(b)** The player stays at `x` = 1000 and faces left from frame 1. The
aim moves to 872 at once.

| Frame | `V` | `S` | `x` − `V` |
|---|---|---|---|
| 1 | 943.625 | −96 | 56.375 |
| 2 | 942.875 | −192 | 57.125 |
| 3 | 941.75 | −288 | 58.25 |
| 5 | 938.375 | −480 | 61.625 |
| 10 | 923.6796875 | −882 | 76.3203125 |
| 20 | 899.0859375 | −463 | 100.9140625 |
| 30 | 886.19140625 | −243 | 113.80859375 |
| 40 | 879.4296875 | −127 | 120.5703125 |
| 60 | 874.0234375 | −35 | 125.9765625 |
| 100 | 872 | 0 | 128 |

From frame 96 the view is exactly on the aim, 128 px behind the
player, and `S` is 0 from frame 97.

Frame 2 worked through: the pull is limited to −96. The lag is `G` =
(872 − 943.625) × 256 = −18336, which becomes −18336 / 16 = −1146. The
aim didn't move, so `D` = −1146. `S` = −96 − 96 = −192, which doesn't go
past −1146.

### Evidence (`title-2`)

96 runs of random walking, running, turning, jumping, rolling and
standing from 8 places in the first level, and 3 scripted runs. The
model was started from the measured view at the start of each stretch
and given only the player's `x`, facing and the sway. On 20,506 frames
(107 stretches) the aim, `S` and `V` matched exactly, with 0 mismatches.
With only the ground layer drawn, the picture moved by exactly the
change of `V`'s whole-pixel part on 828 pairs of consecutive pictures
from 3 places. The other 6 pairs were
during a hurt or a knock-out. The checked frames
include 25 two-frame stops (bounces, bounces while bouncing, roll
defeats), 12,345 frames with the pull at its limit, 1,025 frames on
which `S` was halved, and 12,274 frames on which `S` was held to `D`.
With the sway taken one frame earlier, 2,401 frames don't match, and
with the previous frame's `x`, 18,323.

### Not covered in `title-2`

- Levels other than the first, including whether they sway.
- Where in the sway's cycle the first level starts. The cycle doesn't
  restart when the level starts, so it depends on what happened
  before the level.
- Near the ends of the view's range, and anything that limits the aim
  or the view (both stayed at least 16 px inside the range on every
  checked frame). Where the view starts when a level begins.
- Speeds above 8 px per frame. In the measured runs `S` stayed far
  below this.
- The view's vertical motion (`view-vertical.md` is `title-1` only).
- While the player is hurt or knocked out, carries or throws an object,
  or anything else that freezes the player for longer. In these, the
  view was seen to stop for other numbers of frames.
- The second character, and two characters together.

## Not covered

This list is for `title-1`; `title-2` has its own list above.

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
