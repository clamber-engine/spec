# Walkers and the view

Titles: `title-1`

A level places each walker at a **start**: a level position in whole px,
with a heading. A walker isn't in the level all the time. It appears when
its start comes near the view and is removed when it and its start are
both far enough from the view. This file covers when a walker appears,
when it is removed and when it comes back. How a walker moves is in
`walker.md`, and how the view moves is in `view.md`.

Frames are logic frames at 60 Hz, numbered as in a trace. `x` grows to the
right. Positions are level positions in px. Only differences between them
matter here. A walker's `x` is its horizontal position as in `walker.md`.

Measured on the first level, with the first character leading, over
about 11 800 frames. The player went through the level and also walked
or ran back and forth, so walkers left the view and came back on both
sides. The runs included 12 walkers, small and large, with 49
appearances, 24 removals and 17 walkers defeated. Separately, walkers were
placed at chosen distances from a view that was standing still or
moving, to find where removal starts.

## The view position used

`V` below is the view's position (`view.md`), rounded down to a whole
pixel, **as it was at the start of the frame**. That is the position the
view had at the end of the previous frame, before it moves on this frame.
If the view moves 3 px on frame n, frame n still uses the position from
before that move.

## The window

A walker's start `S` is **in the window** when

    −31 ≤ S − V ≤ 288

The window runs from 31 px left of the picture's left edge to 32 px right
of its right edge. The picture is 256 px wide (`view.md`). Only horizontal
positions count in the runs measured. Walkers far above or below the
view weren't tested.

## Appearing

A walker that isn't in the level and hasn't been defeated appears on the
first frame whose `V` puts its start in the window. While the view moves
right, that is the frame on which `S − V` first becomes 288 or less. While
the view moves left, it is the frame on which `S − V` first becomes −31 or
more.

It appears at its start, on the floor there, at rest (`vx` = 0), with the
heading it was placed with. It then speeds up and walks as in `walker.md`.
Some walkers take their first step on the frame they appear. Their `x` on
that frame is already `S − 1/8` (or `S + 1/8` heading right), which is the
first step of speeding up from rest. Other walkers stay at `S` on that
frame and take the same first step on the next frame. A given walker
does the same thing every time it appears. Which walkers do which belongs
to how they are placed. It doesn't follow from size or heading: both
were seen among small walkers, and all three large walkers measured
waited a frame.

## Being removed

A walker is removed on a frame when **both** of these hold:

1. its start is outside the window (`S − V < −31` or `S − V > 288`), and
2. its `x` after that frame's move, rounded down to `X`, has
   `X − V ≤ −128` or `X − V ≥ 385`. That means it is at least 128 px left
   of the picture's left edge, or at least 129 px right of the first
   pixel past its right edge.

So a walker that walks far out of the picture isn't removed while its
start is still in the window. In the runs measured, such walkers were up
to 320 px left of the picture's left edge and stayed in the level. A
walker whose start has dropped out of the window isn't removed while it
is near or in the picture either. It is removed on the first frame on
which it is also past one of the two limits in point 2. That can happen
on a frame when the walker crosses the limit, or on a frame when the start
leaves the window while the walker is already past the limit.

## Coming back

A removed walker comes back. It appears again, as described in
"Appearing", on the first frame its start is in the window again. The
view can bring the start back in from either side. A walker that comes
back starts afresh: it is at its start, at rest, with its placed heading.

A walker that has been **defeated** never comes back. That covers a walker
bounced on (`walker.md`), rolled into (`rolling-into-walkers.md`) or hit
by a thrown object (`thrown-objects.md`, "Hitting walkers"). It
stays away however the view moves afterwards, including when its start
leaves the window and comes back in. This held in every run measured:
2685 frames in which a defeated walker's start was in the window, and
three runs in which the player walked on after a hit until the start of
the walker hit had left the window and then walked back until it was in
the window again.

## Defeated walkers falling out

A defeated walker falls through the floor (`walker.md`, "Being bounced
on"; `rolling-into-walkers.md`; `thrown-objects.md`, "The walker") and is
removed when its own position leaves the window. Where its start is
doesn't matter.

From its first falling frame on, it is removed on the first frame on
which, after that frame's move,

- `X` − `V` < −31 or `X` − `V` > 288, with `X` its `x` rounded down and
  `V` as in "The view position used", or
- its `y` is more than 256 px below the picture's top row: `y` − `U` >
  256, with `U` the view's vertical position (`view-vertical.md`: the
  level y of the picture's top row), rounded down to a whole pixel.

These are the edges of the window above and the bottom edge of the
vertical window in `../formats/placements.md`, applied to the walker
itself. Its `y` is compared with its fraction: 256 below `U` stays,
256.0625 below is removed.

The first falling frame is:

| Defeated by | Not removed on | Checked from |
|---|---|---|
| a bounce (`walker.md`) or a roll (`rolling-into-walkers.md`), defeat frame B | B, B + 1, B + 2 | B + 3 |
| a thrown object (`thrown-objects.md`), hit frame K | K, K + 1 | K + 2 |

On the frames before, the walker stayed even when it was far outside
the window: with `X` − `V` of −200 and 300 on B + 1 and B + 2, and with
`X` − `V` of −204 and 396 or `y` − `U` of 303 on K + 1. On the first
checked frame it is removed if it is already outside, so a walker hit
while well left or right of the picture is there for the last time on
K + 1.

So a walker defeated near the picture usually leaves through the bottom
edge. In the first level, on flat floor and with the view standing
still, a small walker bounced on is there for the last time on B + 41,
and a small walker hit by a thrown object on K + 40, both 95 px below
the floor they had walked on.

Measured on the first level: 434 walkers hit by thrown objects (small
and large; 13 038 falling frames; 321 removed at the bottom edge, 81 at
the left edge and 32 at the right), 21 small walkers bounced on (812
frames) and 33 rolled into (1 231 frames), with no frame differing from
the rule. Then, walkers were put at chosen places before their first
checked frame and on later frames: `X` − `V` of −31 and 288 stayed, −32
and 289 were removed; with the view moving right, on 24 frames `X` − `V`
was −31 with the view position from the start of the frame and −32 or
−33 with the one from its end, and the walker stayed.

## Groups

Walkers can also be placed as a **group**. In the one group measured,
three walkers stood in a row with the same heading. All three appeared
on the same frame, the frame on which the start of the first one (the
one nearest the view) entered the window. The other two starts were
still outside the window, further from the view. All three were then
defeated, so how a group is removed wasn't seen. One member wasn't
removed on a frame when the rule above would have removed a single
walker.

## Examples

These examples are synthetic. A walker has its start at `S` = 1000, heads
left and takes its first step on the frame it appears.

| Frame | `V` (start of frame) | `S − V` | Walker `x` after the frame | Result |
|---|---|---|---|---|
| n | 711 | 289 | (none) | outside the window: not there |
| n + 1 | 712 | 288 | 999.875 | appears and steps |
| n + 2 | 714 | 286 | 999.640625 | walks (`walker.md`) |

Later the view has moved on, and the same walker has walked left to
`x` = 880.5:

| Frame | `V` | `S − V` | `X` after the move | `X − V` | Result |
|---|---|---|---|---|---|
| m | 1031 | −31 | 879 | −152 | start in the window: stays |
| m + 1 | 1032 | −32 | 878 | −154 | both hold: removed |

If the view then moves back left and `V` is 1031 again at the start of a
frame, the walker appears again at `x` = 1000. It isn't at 878, where it
was removed.

Another walker, also with its start at 1000, heads right and walks
ahead of a view standing at 1500. Its start is far outside the window
(`S − V` = −500):

| Frame | `V` | `X` after the move | `X − V` | Result |
|---|---|---|---|---|
| k | 1500 | 1884 | 384 | stays |
| k + 1 | 1500 | 1885 | 385 | removed |

## Not covered

- Other objects and other kinds of enemies.
- Walkers whose start is in the window when the level begins.
- How a group is removed and whether a group can come back. How walkers
  and groups are placed is in `../formats/placements.md`.
- Walkers far above or below the view. Levels that scroll mostly
  vertically.
- A defeated walker above the picture, and `U` changing while a defeated
  walker falls: in every run measured the view's vertical position stayed
  the same while defeated walkers fell, so whether `U` is taken at the
  start or at the end of the frame wasn't seen. Large walkers bounced on
  or rolled into (only small ones were measured for these).
- Whether defeated walkers come back after the player is knocked out or
  falls out of the level and the level starts again.
- Any limit on how many walkers or objects can be in the level at once.
  None was reached in the runs measured.
