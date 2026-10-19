# The view: vertical motion

Titles: `title-1`, `title-2`

How the view (`view.md`) moves up and down to follow the player: where
it aims, the level's limits on it, how it eases toward its target, and
how it shakes during the ground action. `view.md` covers the horizontal
motion; the two are independent. Everything outside the section
"In `title-2`" is for `title-1`.

Frames are logic frames at 60 Hz, numbered as in a trace. `vy` and
`ground` are the player's values for the frame, as in a trace, taken
after the player has moved on that frame. Positions here are **level y**
in px, growing downward, measured from the top of the level. Unlike the
horizontal motion, the vertical motion depends on where the 32 px bands
of the level fall (see "The ground used"), so level y is absolute, not
relative.

This was measured with the first character leading, alone, on the first
level. It was measured at least 256 px from the level's left end, while
the player stood, walked, ran, turned, jumped, fell, rolled, used the
Down + Action ground action, bounced off walkers, picked up and carried
objects, and walked and fell off ledges onto lower ground. That came to
279 runs, and 76 396 frames were checked against the rules below.

## The view's state

The view is 224 px tall. Vertically it has:

- a **position** `V`: the level y of the top row of the picture, kept
  to 1/256 px. The picture's top row is at `V` rounded down to a whole
  px. The ground layer moves exactly with it. In 897 pairs of
  consecutive pictures, the ground layer's vertical shift matched the
  change of `V` rounded down every time.
- a **smoothed gap** `Q`: a whole number of 1/256 px. It follows the gap
  between the target and `V` (see "Following the target").

## The player's feet line

The **feet line** `F` is the player's level y rounded up to a whole px.
The player's pictures are drawn with their frame's origin on the feet
line (`../formats/sprites.md`). A player standing still with the view
settled has its feet line 160 px below the picture's top row (row 160,
counting the top row as 0).

## The ground used

Each frame the target is worked out from a **ground line** `G`, the level
y of a ground surface, or from no ground at all:

- **Grounded** (`ground` is 1): `G` = `F`.
- **In the air** (`ground` is 0): `G` is the line of the ground directly
  below the player, if it is close enough; otherwise there is no ground.

"Close enough" uses **bands**. The level is divided into bands 32 px
tall. A line `L` is in band ⌊(`L` − 1) / 32⌋, so lines 1 to 32 are band
0, lines 33 to 64 are band 1, and so on. The ground below is close
enough when its band is at most 2 more than the band of `F`. That means
it is in the feet's band or one of the two bands under it.

So how far the player can rise above the ground before the view stops
using it depends on where the ground is in its band. It is 64 to 95 px:

| Ground line `G` | Last `F` that still uses it | Height above `G` |
|---|---|---|
| 353 (band 11) | 289 (band 9) | 64 |
| 337 (band 10) | 257 (band 8) | 80 |
| 320 (band 9) | 225 (band 7) | 95 |

A jump from the ground therefore keeps aiming at the take-off ground
until the player rises out of reach. It then follows the player, and goes
back to the ground when the player comes down within reach again. A
player who walks or falls off a ledge has no ground in reach until the
lower ground is within two bands. The ground below is then the ground
the player will land on.

The ground used was checked against 1800 frames of jumps in place, where
the ground below is the take-off ground: 1793 matched. The other 7 were
in the air against a wall (see "Not covered"). On the last frames of
falls and jumps that ended in a landing, the landing ground was used
6338 times, and every time it was within reach.

## The target

From `G`, the view's position `V` from before this frame, and `F`:

1. **No ground:** `T` = `F` − 160.
2. **Feet near the top:** otherwise, if `F` − ⌊`V`⌋ is from 0 to 47
   (the feet line is in the top 48 rows of the picture), `T` = ⌊`V`⌋ −
   160.
3. **Otherwise:** `T` = `G` − 160.

Then the level's limits apply (next section), and the **target** is
`T` + 1/2 px.

So with the view settled on a standing player, the feet line is
159.5 px below `V`.

## The level's limits

A level has a list of **vertical limits**. Each entry has:

- a starting `x` (whole px): the entry is in use while the player's `x`,
  rounded down, is at least its start and less than the next entry's
  start. The first entry is also in use to the left of its start.
- a **top** and a **bottom**: whole px values of `T`.
- a **rate**: usual or quick (see "Following the target").

The entry is chosen from the player's `x` after moving on this frame.
Then `T` ← max(`T`, top), and then `T` ← min(`T`, bottom). Every entry
seen had top less than bottom.

On the first level the limits change at several places along the level,
and both rates occur. The entries are stored in the level's view-limit
record (`../formats/view-limits.md`).

With the limits, the target matched on all but 81 of the 76 396 frames
(see "Not covered"). On 3660 of them, case 2 of "The target" applied.

## Following the target

On each frame, after the player has moved:

1. Work out the target, as above.
2. Choose the **rates** `a` and `b`. Let `P` = `F` − ⌊`V`⌋, with `V`
   from before this frame (how far down the picture the feet line is).
   If case 2 of "The target" applied, `P` counts as 0.
   - If `P` ≥ 208: quick.
   - If `P` is from 64 to 207 and `vy` > 6 (falling faster than 6 px per
     frame): quick.
   - Otherwise: the rate of the limit entry in use.

   Usual is `a` = 16, `b` = 64. Quick is `a` = 8, `b` = 16.
3. Let `D` = target − `V`, in 1/256 px, using `V` from before this
   frame. If `D` is from 0 to 31 (the target is less than 1/8 px below
   `V`), `D` counts as 0. A small gap above `V` is not set to 0.
4. `Q` ← `Q` + ⌊(`D` − `Q`) / `a`⌋.
5. `V` ← `V` + ⌊`Q` / `b`⌋ / 256 px.

All rounding is down (toward −∞).

These steps matched `V` and `Q` on every checked frame except the shake
frames (next section). The quick rates were in use on 1017 of them: 109
with `P` ≥ 208, and 908 falling faster than 6 px per frame. The rule for
small gaps made a difference on 838.

Because of the rounding, the view doesn't always settle exactly on the
target. For example, with `D` = 39 and `Q` = 24, `Q` stays 24 and `V`
doesn't move, 39/256 px above the target.

## The ground action's shake

During the first character's ground action (`ground-action.md`), the
view shakes. On frame **H** and every 4th frame after it (H, H + 4, …,
H + 84; 22 frames), an extra offset is added to `V`, after step 5:

| Frame | H | H + 4 | H + 8 | H + 12 | H + 16 | H + 20 | … | H + 80 | H + 84 |
|---|---|---|---|---|---|---|---|---|---|
| Offset (px) | +4 | −4 | +2 | −2 | +1 | −1 | … | +1/256 | −1/256 |

The size halves every 8 frames, and the sign alternates, starting
downward. `Q` isn't changed by the offset. On the next frames `D`
includes it, and the view eases back.

H was 25 to 28 frames after the press frame P (13 runs). Which of these
it is isn't specified. In those 13 runs all 22 offsets matched this
pattern exactly.

## Frames on which the view stays still

On the frame of a bounce off an enemy (**B**, `bounce.md`) and on
**B + 1**, the vertical motion also does nothing: the target, `V` and
`Q` keep their values from the frame before. It continues from **B + 2**.
This was seen on 6 bounces. The same applies on the frame of a
knock-out during the ground action (**K**, `ground-action-enemies.md`)
and on **K + 1**; it continues from **K + 2** (156 knock-outs, in 63 of
which the view moved again on K + 2). Roll defeats (`view.md`) weren't
measured for the vertical motion.

While the player is knocked out (`knock-out.md`, hit frame **H**), the
vertical motion does nothing on **H to H + 9**, like the horizontal
one: the target, `V` and `Q` keep their values from the end of H − 1.
It moves again from H + 10 (`view.md`, "Frames on which the view stays
still", has the measurements; in 129 of those knock-outs the vertical
motion would have moved on H without the hit).

## Examples

Synthetic, computed from the rules above, with no limits in effect.
`Q` is in 1/256 px.

**(a)** A player stands with `F` = 400 and the view is settled (`V` =
240.5, `Q` = 0). From frame 1 the player stands on ground 32 px higher
(`F` = `G` = 368). This isn't how the player gets there; it only shows
the view.

| Frame | target | `V` | `Q` |
|---|---|---|---|
| 1 | 208.5 | 240.46875 | −512 |
| 2 | 208.5 | 240.40625 | −992 |
| 3 | 208.5 | 240.31640625 | −1441 |
| 10 | 208.5 | 239.05859375 | −3833 |
| 30 | 208.5 | 232.4140625 | −6052 |
| 60 | 208.5 | 222.25390625 | −4653 |
| 100 | 208.5 | 214.10546875 | −2190 |
| 200 | 208.5 | 208.8359375 | −175 |

Frame 1 worked through: `T` = 368 − 160 = 208, so the target is 208.5,
and `D` = 208.5 − 240.5 = −32 px = −8192/256. `Q` = 0 + ⌊−8192 / 16⌋ =
−512. `V` moves by ⌊−512 / 64⌋ = −8/256 px.

**(b)** The same player, settled at `F` = 400, jumps in place. `F` rises
6 px per frame for 15 frames, then comes back down the same way, and the
player lands on frame 30 (simplified, not the real jump arc of
`jump-arc.md`). Line 400 is in band 12. `F` = 322 is in band 10, so the
ground is still in reach. `F` = 316 is in band 9, out of reach.

| Frame | `F` | `G` | target | `V` | `Q` |
|---|---|---|---|---|---|
| 1 | 394 | 400 | 240.5 | 240.5 | 0 |
| 13 | 322 | 400 | 240.5 | 240.5 | 0 |
| 14 | 316 | none | 156.5 | 240.41796875 | −1344 |
| 15 | 310 | none | 150.5 | 240.25 | −2699 |
| 16 | 316 | none | 156.5 | 240.01171875 | −3871 |
| 17 | 322 | 400 | 240.5 | 239.7890625 | −3622 |
| 30 | 400 | 400 | 240.5 | 237.984375 | −1306 |
| 46 | 400 | 400 | 240.5 | 237.45703125 | 10 |
| 60 | 400 | 400 | 240.5 | 237.66796875 | 450 |

**(c)** Falling at 8 px per frame with no ground in reach, from a view
settled at `F` = 300 (`V` = 140.5, `Q` = 0). `P` is from 64 to 207 and
`vy` > 6, so the rates are quick.

| Frame | `F` | target | `V` | `Q` |
|---|---|---|---|---|
| 1 | 308 | 148.5 | 140.5625 | 256 |
| 2 | 316 | 156.5 | 140.73828125 | 734 |
| 5 | 340 | 180.5 | 142.3984375 | 3197 |
| 10 | 380 | 220.5 | 150.58984375 | 9264 |
| 20 | 460 | 300.5 | 190.61328125 | 21824 |
| 30 | 540 | 380.5 | 255.71875 | 29845 |

## In `title-2`

Everything in this section was measured on `title-2` only, with the
lead character alone, on the first level. It was measured away from the
top and bottom of the view's range, while the player stood, walked, ran,
turned, jumped, fell, rolled, walked and rolled off ledges, defeated
walkers by rolling and bounced off them. The vertical motion works like
`title-2`'s horizontal motion (`view.md`, "In `title-2`"): the view
aims at a point, and its speed changes by limited amounts toward it.
What differs is where it aims, which depends on the ground below the
player, and how quickly it eases, which depends on where the player's
feet are in the picture.

Positions are level y in px, growing downward, kept to 1/256 px, as in
`../formats/level-ground.md` ("Positions (`title-2`)"). `x` and `y` are
the player's values for the frame, as in a trace, taken after the
player has moved on that frame. `y` is the level y of the player's
feet. Speeds are in 1/256 px per frame. ⌊ ⌋ rounds down (toward −∞).

### The view's state (`title-2`)

The view is 224 px tall. Vertically it has:

- a **position** `V`: the level y of the top row of the picture, kept
  to 1/256 px. The picture's top row is at ⌊`V`⌋. The ground layer
  moves exactly with it.
- a **speed** `S`, a whole number of 1/256 px per frame, signed.
- the **aim** of the previous frame, `P`.
- the **ground aim** `E`, a whole number of px: ⌊aim⌋ on the last frame
  on which the ground below was in reach (see the next sections).

### The ground below (`title-2`)

Each frame, the **ground below** is found as in
`../formats/level-ground.md` ("The ground below a point (`title-2`)"),
at the player's ⌊`x`⌋ and ⌊`y`⌋. It is **in reach** if a surface was
found and it is at or below the feet (its level y `G` ≥ ⌊`y`⌋). It is
out of reach if there is no ground, if the ground below is above the
level, or if it is above the feet (the feet are inside ground that is
passable from below). While the player stands on the ground, `G` =
⌊`y`⌋.

Because the ground below is found at most two bands under the feet's
band, a jump straight up keeps the take-off ground in reach until the
feet are 64 to 95 px above it, depending on where the ground is in its
band.

### The sway (`title-2`)

On the first level, the vertical aim also sways by `sway` px, a whole
number from 0 to 15. It uses the same steps as the horizontal sway
(`view.md`, "The aim (`title-2`)"): up from 0 to 15, 15 a second time,
down to 0, 0 a second time, 32 steps in all. Like the horizontal sway,
its steps follow logic frames, but each step lasts 5 or 6 logic frames
instead of 8: every 16 logic frames it goes through 3 steps, lasting 6,
5 and 5 frames in that order. So it goes through 3 cycles while the
horizontal sway goes through 2, and the two together repeat every 512
logic frames.

They line up like this. Let `F` be a frame on which the horizontal sway
becomes 1 on its way up. The vertical sway moved to its current step on
`F` − 2 (a 6-frame step) and moves to the next step on `F` + 4, `F` + 9,
`F` + 14, `F` + 20, `F` + 25 and so on. On `F` it is 5 on its way up in
one horizontal cycle and 10 on its way down in the next, alternating.

Off the first level, `sway` is 0 (not measured; see "Not covered in
`title-2`").

### The aim (`title-2`)

Let `Y` = `y` − `sway`. On each frame:

1. **Ground in reach:** aim = `Y` + (`G` − ⌊`y`⌋) − 156. That is the
   ground's line, with `y`'s fraction, 156 px below the aim, less the
   sway. `E` ← ⌊aim⌋.
2. **Rising out of reach:** otherwise, if ⌊`Y`⌋ − 64 < `E`, aim = `Y` −
   64.
3. **Held:** otherwise, if ⌊`Y`⌋ − 200 < `E`, aim = `P` (the aim doesn't
   change).
4. **Falling out of reach:** otherwise, aim = `Y` − 200.

So with the view settled on a standing player and no sway, the feet are
156 px below the picture's top row (row 156, counting the top row as 0).
After the ground goes out of reach, the aim stays where it was while
⌊`Y`⌋ is from `E` + 64 to `E` + 199. With no sway and `E` taken from
standing on ground at level y `G0`, that is until the feet are more than
92 px above `G0`, or 44 px or more below it. The aim then follows the
player with the feet 64 px (rising) or 200 px (falling) below it.

### The level's limits (`title-2`)

The level can keep the aim between a **top** and a **bottom**, whole px
values of the aim. After the aim is worked out: if ⌊aim⌋ is less than
the top, its whole px part becomes the top, and if it is more than the
bottom, its whole px part becomes the bottom. The fraction was usually
kept; for some limits it became 0 (see "Not covered in `title-2`").
`E` keeps the value from before the limits; `P` (on the next frame)
is the limited aim.

On the first level the limits change from one part of the level to the
next. Inside each part a single top and a single bottom were seen. Some
parts have them as little as 8 px apart, so the view hardly moves
vertically there. Where the parts are and how the limits are stored
belong to the level format (not yet specified).

### Following the aim (`title-2`)

On each frame, after the player has moved:

1. Work out the aim, as above, with the limits.
2. Choose the **rates**. Let `a` = ⌊aim⌋, `p` = ⌊`P`⌋, `v` = ⌊`V`⌋
   with `V` from before this frame, the **feet row** `r` = ⌊`y`⌋ − `v`,
   and `dy` = ⌊`y`⌋ minus ⌊`y`⌋ on the frame before.
   - If `a` < `p` and `a` < `v` (the aim moves up and is above the
     picture's top): **usual** if `r` ≥ 32. With `r` < 32, see "Not
     covered in `title-2`".
   - Otherwise:
     - `r` < 192: **usual**.
     - `r` ≥ 216: **quick**.
     - `r` from 192 to 215: **usual** if `dy` ≤ 2, **medium** if `dy` is
       from 3 to 7, **quick** if `dy` ≥ 8.

   | Rates | pull divisor | pull limit | lag divisor |
   |---|---|---|---|
   | usual | 32 | 64 | 16 |
   | medium | 8 | 192 | 8 |
   | quick | 4 | 576 | 4 |

3. **Pull:** (aim − `V`) / pull divisor, rounded down, then limited to
   the range −limit to +limit.
4. **Lag:** `L` = `P` − `V`. If `L` is from −32 to 31, keep it.
   Otherwise divide it by the lag divisor, rounded down. Then raise the
   result to at least 8 if `L` was positive, or lower it to at most −8 if
   `L` was negative.
5. **Goal** and **speed:** as steps 4 and 5 of "Following the aim
   (`title-2`)" in `view.md`: `D` = (aim − `P`) + the lag; if `D` is 0,
   `S` becomes 0; otherwise add the pull to `S`, halve `S` (rounding
   down) if `S` and `D` now have different signs (0 counts as positive),
   and otherwise keep `S` from going past `D`.
6. `V` ← `V` + `S`, but by at most 2048 (8 px) either way. `S` itself
   isn't limited: it can stay above 2048, and `V` then moves 8 px per
   frame.
7. `P` ← aim.

The usual rates are the slow ones: the pull adds at most 1/4 px per
frame to `S`. The quick rates apply while the feet are low in the
picture: on row 216 or below (the bottom 8 rows, or below the picture),
or in the 24 rows above them while the player moves down 8 px or more
per frame.

### Frames on which the view stays still (`title-2`)

The vertical motion stays still on exactly the frames on which the
horizontal motion does (`view.md`, "Frames on which the view stays
still (`title-2`)"): on those frames `V` and `S` keep their values from
the frame before. The aim and `E` are worked out as usual, and `P`
becomes the aim.

### Examples (`title-2`)

Synthetic, computed from the rules above with `sway` 0 and no limits.
In each, the view starts settled on a player standing on ground at
level y `G0`: `V` = `P` = `G0` − 156, `E` = `G0` − 156, `S` = 0.

**(a)** `G0` = 400. From frame 1 the player stands on ground 32 px
higher (`y` = `G` = 368). This isn't how the player gets there; it only
shows the view.

| Frame | aim | `V` | `S` |
|---|---|---|---|
| 1 | 212 | 243.75 | −64 |
| 2 | 212 | 243.25 | −128 |
| 3 | 212 | 242.5 | −192 |
| 10 | 212 | 232.65625 | −353 |
| 30 | 212 | 217.65625 | −97 |
| 60 | 212 | 212.79296875 | −14 |
| 100 | 212 | 212 | 0 |

The view is exactly on the aim from frame 100 on.

Frame 1 worked through: `a` = 212 is less than `p` = 244 and `v` = 244,
and `r` = 368 − 244 = 124 ≥ 32, so the rates are usual. The pull is
(212 − 244) × 256 / 32 = −256, limited to −64. The lag is `L` = 0. `D`
= (212 − 244) × 256 = −8192. `S` = 0 − 64 = −64, which doesn't go past
`D`. `V` moves by −64/256 px.

Frame 2: the pull is ⌊(212 − 243.75) × 256 / 32⌋ = −254, limited to
−64. `L` = (212 − 243.75) × 256 = −8128, outside −32 to 31, so the lag
is −8128 / 16 = −508. `D` = 0 − 508 = −508, and `S` = −64 − 64 = −128.

**(b)** `G0` = 400. The player jumps: `y` goes up 8 px per frame for 15
frames (to 280), then back down 8 px per frame, landing on frame 30
(simplified, not the real jump arc of `jump-arc.md`). For this example
the ground below is taken to be in reach while the feet are at most
72 px above it.

| Frame | `y` | ground | aim | `V` | `S` |
|---|---|---|---|---|---|
| 1 | 392 | 400 | 244 | 244 | 0 |
| 9 | 328 | 400 | 244 | 244 | 0 |
| 10 | 320 | none | 244 | 244 | 0 |
| 11 | 312 | none | 244 | 244 | 0 |
| 12 | 304 | none | 240 | 243.875 | −32 |
| 15 | 280 | none | 216 | 242 | −224 |
| 18 | 304 | none | 240 | 240.80859375 | −57 |
| 19 | 312 | none | 240 | 240.7578125 | −13 |
| 21 | 328 | 400 | 244 | 240.7578125 | 13 |
| 22 | 336 | 400 | 244 | 240.90625 | 38 |
| 30 | 400 | 400 | 244 | 242.140625 | 31 |
| 60 | 400 | 400 | 244 | 243.77734375 | 8 |

On frames 10 and 11 the aim is held: ⌊`Y`⌋ − 64 = 256 and 248 are not
less than `E` = 244. From frame 12 (304 − 64 = 240) it rises with the
player. On frame 19 it is held again, at 240, the aim of frame 18. All
rates are usual.

**(c)** `G0` = 300. The player walks off the ground and falls 8 px per
frame, with no ground in reach.

| Frame | `y` | aim | `r` | rates | `V` | `S` |
|---|---|---|---|---|---|---|
| 1 | 308 | 144 | 164 | usual | 144 | 0 |
| 5 | 340 | 144 | 196 | quick | 144 | 0 |
| 6 | 348 | 148 | 204 | quick | 145 | 256 |
| 7 | 356 | 156 | 211 | quick | 148.25 | 832 |
| 8 | 364 | 164 | 216 | quick | 153.75 | 1408 |
| 10 | 380 | 180 | 219 | quick | 169.5 | 2560 |
| 20 | 460 | 260 | 219 | quick | 249.5 | 2720 |
| 40 | 620 | 420 | 219 | quick | 409.5 | 2720 |

Up to frame 5 the aim is held. From frame 6 (348 − 200 = 148 ≥ 144) it
falls with the player. From frame 10 `S` is above 2048, so `V` moves the
full 8 px per frame and the feet stay on row 219.

### Evidence (`title-2`)

Random walking, running, turning, jumping, rolling and standing from 10
places in the first level (120 runs of 300 frames), and the same with
the player moved up by 48 to 240 px at random moments to make long
falls (120 runs). The model was started from the measured view at the
start of each stretch and given only the player's `x`, `y`, the ground
below (worked out from the level's ground as in
`../formats/level-ground.md`), the sway and the limits. On 49 874
frames (358 stretches) the aim, `S` and `V` matched exactly, with 0
mismatches. The frames on which the player was moved, and the frame
after, were left out.

The checked frames include 2288 frames rising out of reach, 5413 held,
17 falling out of reach, 124 with the ground below above the feet, 67
frames on which the view stayed still, 162 with `S` beyond 8 px per
frame, and 21 115 limited (215 of them with the fraction set to 0). The
rates were usual on 49 359 frames (420 of them with the feet in rows
192 to 215), medium on 101 and quick on 347 (210 with the feet in row
216 or below). Only one top and one bottom were seen in each part of
the level. With the sway taken one frame earlier, 4027 frames don't
match. Without the sway, 23 412 don't. With the ground below taken at
the previous frame's `x`, 2827 don't.

With only the ground layer drawn, the picture moved vertically by
exactly the change of ⌊`V`⌋ (one picture later, as for the horizontal
motion) on all 1192 pairs of consecutive pictures from 4 places, 374 of
them with a vertical move.

### Not covered in `title-2`

- Levels other than the first, including whether they sway and other
  ways of following the player vertically.
- Where the sway's cycle starts (as for the horizontal sway).
- Whether the two sways keep this alignment after long play: it was
  seen at every point of the 512-frame pattern, but only within the
  first few thousand logic frames of play.
- Near the top and bottom of the view's range: the view stayed at least
  16 px inside it on every checked frame. Where the level's limits are,
  how they change from one part of the level to the next, and which
  limits set the aim's fraction to 0. A part shorter than the view.
  Where the view starts when a level begins, and the ground aim `E`
  then.
- The aim moving up above the picture's top with the feet in the top 32
  rows (`r` < 32 in step 2). This was seen on one frame, with quick
  rates.
- While the player is hurt or knocked out, carries or throws an object,
  or is in or shot from a barrel: the view was seen to stop following
  these rules.
- Underwater, and the second character or two characters together.

## Not covered

This list is for `title-1`; `title-2` has its own list above.

- Other levels and other kinds of level, where the view may move
  differently. Only the first level was measured.
- Within 256 px of the level's left end, where the view was seen not to
  follow these rules. The start of a level, after the player falls out
  of a level, and the end of a level (the view stops while the player
  walks out).
- While the player is knocked out from H + 10 on (`knock-out.md`; H to
  H + 9 are in "Frames on which the view stays still"), loses a
  character (`losing-a-character.md`), throws an object, or the partner
  runs off.
  In all of these the target was seen to stop following the rules, as
  in `view.md`.
- The second character leading (`swap.md`).
- Roll defeats (see above).
- Gaps larger than about 128 px between the target and `V`. They weren't
  reached, and the view may not move more than a limited distance per
  frame.
- In the air against a wall: when the player jumps or falls against a
  wall, `ground` can read 1 on some frames while the player is in the
  air, and the target then differed from these rules (81 frames).
- Rising up through a platform from below (`platforms-from-below.md`):
  while `ground` is 1 inside the platform, the target used the platform's
  top line as `G` (85 frames, 12 times; once the line changed partway
  through).
- Which point under the player the ground below is taken at, near the
  edge of a floor or on slopes.
- How a knock-out during the ground action (`ground-action-enemies.md`)
  affects the shake. In one run that started next to an object, the
  shake started on P + 36 and started again 24 frames later, and the
  view moved an extra 2/256 px per frame from P to P + 32.
- Layers that move at other rates than the ground, other than the first
  level's (`../formats/graphics.md`, "The layers").
