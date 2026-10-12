# The view: vertical motion

Titles: `title-1`

How the view (`view.md`) moves up and down to follow the player: where
it aims, the level's limits on it, how it eases toward its target, and
how it shakes during the ground action. `view.md` covers the horizontal
motion; the two are independent.

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
The player's level y is placed so that, for a player standing on the
ground, `F` is one more than the level y of the lowest pixel row drawn
for the player. A player standing still with the view settled has its
lowest drawn row 159 px below the picture's top row (row 159, counting
the top row as 0).

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
and both rates occur. How the limits are stored belongs to the level
format (not yet specified).

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
This was seen on 6 bounces. Roll defeats (`view.md`) weren't measured
for the vertical motion.

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

## Not covered

- Other levels and other kinds of level, where the view may move
  differently. Only the first level was measured.
- Within 256 px of the level's left end, where the view was seen not to
  follow these rules. The start of a level, after the player falls out
  of a level, and the end of a level (the view stops while the player
  walks out).
- While the player is knocked out (`knock-out.md`), loses a character
  (`losing-a-character.md`), throws an object, or the partner runs off.
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
- Hits during the ground action. In one run that started next to an
  object, the shake started on P + 36 and started again 24 frames
  later, and the view moved an extra 2/256 px per frame from P to
  P + 32.
- Layers that move at other rates than the ground.
