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
level, including next to the level's left end, while
the player stood, walked, ran, turned, jumped, fell, rolled, used the
Down + Action ground action, bounced off walkers, and picked up and
carried objects. The view was measured over about 39 000 frames. Where
the view starts when the level or another area begins is in "When an
area begins", measured over another 43 500 frames.

## The view's state

The view has:

- a **position** `V`: the level `x` of the left edge of the picture,
  kept to 1/256 px. The picture is drawn with its left edge at `V`
  rounded down to a whole pixel, kept within the level's left and right
  limits (`level-ends.md`, "The picture's left edge"). The ground layer
  moves exactly with it. The first level's far layer and horizon layer
  move at other rates (`../formats/graphics.md`, "The layers").
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

**Near the left end:** when ⌊`x`⌋ is less than 128 minus the speed
part, `lead` is 1 px less than this sum. This only happens close to the
level's left end: for a player standing still, at `x` below 128; for a
player running right at 3 px per frame (speed part 72), below 56.

Some values of `lead`, away from the left end:

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

When the player is knocked out (`knock-out.md`, hit frame **H**), the
view does nothing on **H to H + 9**, ten logic frames (the 32-frame
pause between H + 7 and H + 8 has no logic frames). The target, `V` and
`S` keep their values from the end of H − 1, whatever the view was
doing then, and so does the vertical motion (`view-vertical.md`). The
view moves again from **H + 10**, the last frame on which the player
is still frozen; how it moves from then on, through the knock-back and
after, isn't covered.

Measured on the first level: 164 knock-outs of the player alone in
random play from 8 places along the level. In 162 of
them the view had moved on H − 1, and in 159 the same play with the hit
prevented moved it on H. In all 164, the target, `V` and `S`, and the
vertical target, `V` and `Q`, were exactly as at the end of H − 1 on
every frame from H to H + 9 (1 640 frames, to 1/256 px and 1/8192 px
per frame), and the view had moved again by the end of H + 10.

## When an area begins

Frames here are numbered from the area's first logic frame, frame 0, as
in `level-start.md` (the level's start), `restart-barrel.md` (resuming
at the restart point) and `exits.md` ("Walking in"). `x` is the lead's
`x`; with two characters, nothing about the view changes. `A` and `B`
are the level's view limits (`level-ends.md`).

**Frame 0.** Let `w` = ⌊`x`⌋ on frame 0. Let `t` = `w` − 80 if `w` is
from 0 to 127, and `w` − 79 otherwise. Then `V` is `t` kept from `A` to
`B`, plus 1/2 px, and `S` = 0. The player's speed and facing play no
part. So a level that begins with the lead at `x` = 1000 has `V` =
921.5 on frame 0, and one that begins with the lead at `x` = 24 has
`V` = `A` + 1/2 if `A` is 0.

From frame 1 the view follows the rules above ("Following the target"),
except as given below. Two things change on some of these frames:

- the **base and facing part** of `lead` together can have another
  value, −79 or −127, the same for both facings and whatever `vx` is
  (the speed part and "Near the left end" still apply);
- on **fast frames**, steps 3 and 4 of "Following the target" change.
  On the `k`-th fast frame in a row:
  3. `S` ← `S` + (`D` / 32 − `S`) / 2, rounded down to a multiple of
     1/8192 px per frame (halfway, instead of an eighth of the way).
  4. `V` ← `V` + `m` × `S`, rounded toward zero to a multiple of
     1/256 px, with `m` = 1 for `k` from 1 to 4, 2 for `k` from 5 to 10,
     4 for `k` from 11 to 14, and 8 from `k` = 15 on.

**The level's start** (`level-start.md`, the frames T = 51 and the
landing frame, called `G` here). This also applies when the first level
begins again from the room-return record (`exits.md`).

| Frames | The view |
|---|---|
| 1 to T | Base and facing part −79. |
| T + 1 to T + 5 | Base and facing part −127. |
| T + 6 to `G` | Base and facing part −127, and fast frames: `k` = 1 on T + 6. With T = 51: `m` = 1 on frames 57 to 60, 2 on 61 to 66, 4 on 67 to 70, and 8 from 71 to `G`. |
| `G` + 1 on | The usual rules. |

**Walking in** (`exits.md`, "Walking in"): on frames 1 to 26 `V` and `S`
don't change. Frame 27 is a usual frame except that the base and facing part are
−79, and from
frame 28 on the usual rules apply. This was measured walking back into
the first level after the scene, and walking into the area behind the
first wall exit (`wall-exits.md`), where the lead begins left of the
area's left limit, so `V` on frame 0 is that limit + 1/2. In the room
and the scene the picture
doesn't move (`level-ends.md`), whatever the view's position, so it is
not covered there.

**Resuming at the restart point** (`restart-barrel.md`): the usual
rules from frame 1, also while the player is inside the barrel.

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

**(c)** A level starts with the entrance (`level-start.md`) at `xs` =
100, with `A` = 0 and `B` = 5000. Nothing is held. The lead faces right
throughout and is still in the air on frame 80.

| Frame | `x` | `vx` | target | `V` | `S` |
|---|---|---|---|---|---|
| 0 | 100 | 0 | | 20.5 | 0 |
| 1 | 100 | 0 | 20 | 20.5 | −16/8192 |
| 2 | 100 | 0 | 20 | 20.5 | −30/8192 |
| 50 | 100 | 0 | 20 | 20.13671875 | −50/8192 |
| 51 (T) | 100 | 2.375 | 78 | 20.35546875 | 1807/8192 |
| 52 | 102.375 | 2.375 | 32.375 | 20.59375 | 1965/8192 |
| 56 | 111.875 | 2.375 | 41.875 | 21.84375 | 3019/8192 |
| 57 | 114.25 | 2.375 | 44.25 | 22.375 | 4377/8192 |
| 58 | 116.625 | 2.375 | 46.625 | 23.01953125 | 5292/8192 |
| 60 | 121.375 | 2.375 | 51.375 | 24.5390625 | 6522/8192 |
| 61 | 123.75 | 2.375 | 53.75 | 26.24609375 | 7000/8192 |
| 66 | 135.625 | 2.375 | 65.625 | 35.65625 | 8039/8192 |
| 67 | 138 | 2.375 | 68 | 39.63671875 | 8159/8192 |
| 70 | 145.125 | 2.375 | 75.125 | 50.953125 | 7430/8192 |
| 71 | 147.5 | 2.375 | 77.5 | 57.8984375 | 7113/8192 |
| 80 | 168.875 | 2.375 | 98.875 | 92.01953125 | 2489/8192 |

Frame 0: `w` = 100 is below 128, so `t` = 20 and `V` = 20.5. Frame 1:
base and facing part −79, speed part 0, and ⌊`x`⌋ = 100 is below 128,
so `lead` = −79 + 0 − 1 = −80 and the target is 20. Frame 51: the speed
part is 38 + 19 = 57 and 100 is not below 128 − 57 = 71, so `lead` =
−79 + 57 = −22. Frame 52: base and facing part −127, `lead` = −70. Frame 57 is the
first fast frame: `D` = 44.25 − 21.84375 = 22.40625, `D` / 32 =
5736/8192, `S` = 3019 + (5736 − 3019) / 2 = 4377/8192 (rounded down),
and `V` moves by 4377/8192, rounded toward zero to 136/256. Frame 61 is
the fifth: `V` moves by 2 × 7000/8192, rounded to 437/256.

**(d)** Walking into a level (`exits.md`, "Walking in") from a start
record point at `xs` = 300, with `A` = 0, `d` = +1, and nothing held. On frame 0 the lead is at `x` = 220 (`exits.md`), so `V` = 220 −
79 + 0.5 = 141.5.

| Frame | `x` | `vx` | target | `V` | `S` |
|---|---|---|---|---|---|
| 0 | 220 | 3 | | 141.5 | 0 |
| 1 to 26 | 223 to 298 | 3 | | 141.5 | 0 |
| 27 | 304 | 3 | 297 | 142.10546875 | 4976/8192 |
| 28 | 306.625 | 2.625 | 313.625 | 143.3046875 | 9842/8192 |

Frame 27: base and facing part −79 and speed part 72, so `lead` = −7.
Frame 28: base −71, facing part 15, speed part 63, `lead` = 7.

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
- The view's vertical motion: see `view-vertical.md`, "In `title-2`".
- While the player is hurt or knocked out, carries or throws an object,
  or anything else that freezes the player for longer. In these, the
  view was seen to stop for other numbers of frames.
- The second character, and two characters together.

## Not covered

This list is for `title-1`; `title-2` has its own list above.

- Near the first level's left end the view stops moving altogether
  once the player has walked past the exit there (`exits.md`, "Leaving
  the area"). The view's position going past the level's limits, and
  the picture staying within them, are in `level-ends.md`.
- The view while the player falls out of a level. The view of other
  levels, and of areas other than the first level, when they begin
  ("When an area begins" was measured for the first level, its restart
  point and walking back into it after the scene); the view's position
  in the first level's room and scene.
- Input held or pressed on frame 1 of a resume (`restart-barrel.md`,
  "Not covered"): with Right held from frame 0, the facing part kept a
  value from the player's move on frame 1 on later frames.
- Faster view moves: in the measured runs the view never moved by more
  than 7 px in a frame (the fastest were fast frames of the level's
  start).
- While the player is knocked out from H + 10 on (`knock-out.md`; H to
  H + 9 are in "Frames on which the view stays still"), loses a
  character (`losing-a-character.md`), throws an object, or falls into
  a pit. In all of these the target was seen to stop following the
  rule.
- The second character leading (`swap.md`).
- Layers that move at other rates than the ground, other than the first
  level's (`../formats/graphics.md`, "The layers").
- Which frame's view the picture of a frame is drawn with, relative to
  the sprites drawn in it.
