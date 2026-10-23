# Riding on slopes

Titles: `title-1`

How the player riding the animal (`riding.md`) moves on gentle slopes
that rise or fall **1 px for every 4 px** across (`slopes.md`), uphill
and downhill, moving right and left: riding, turns, jumps, charges and
their hops, charge ends and landings. Riding on flat floor is in
`riding.md`; steeper floor is listed under "Not covered".

Frames are logic frames at 60 Hz, numbered as in a trace. `x` and `vx`
are as in a trace: right is positive, `vx` in px per frame. Vertical
positions are **level y** as in `view-vertical.md`: they grow downward,
and `vy` is positive when moving down. "Higher" means a smaller `y`.
Speeds are whole multiples of 1/256 px per frame. The parts of riding
(riding, turn, a jump's crouch, take-off, air and landing frames,
charge, the hop, charge end, settling frame), the frames P, T, L, C and
"steered" are as in `riding.md`. Floor heights and "the column
containing `x`" are as in `slopes.md` ("Floor height").

Measured with the first character alone, by inputs only:

- In a modified copy of the game (the first level's crate moved onto the
  long flat floor near the level's start, and the walkers near the
  1-in-4 slopes next to that floor removed), random inputs as in
  `riding.md` while riding on that floor and over the slopes: 400 runs,
  593 437 frames, 161 742 of them over slope columns.
- Unmodified, on the 1-in-4 slopes near the first level's own crate,
  steering back before the steep floor further on: 100 runs,
  142 647 frames, 68 411 of them over slope columns.

In every frame of every run, `x`, `y`, `vx`, `vy`, `ground`, `facing`
and the part of riding were exactly as the rules below give. The runs
held 10 274 jumps, 11 513 charges, 5 165 hops and 4 125 turns (on flat
floor and slopes together). On slope columns there were 3 571 landings,
and 1 145 take-offs (jumps and hops) whose first airborne frame moved
into a column of a different height (556 higher, 589 lower).

## Summary

Riding on a 1-in-4 slope is riding on flat floor (`riding.md`) with the
floor height taken column by column. Slopes don't change speeds, turns,
the timing of jumps and charges, or any other rule of `riding.md`. The
rules below say where the floor height comes in.

## On the floor

On every frame that the rider spends on the floor (riding, a turn, a
jump's crouch frames and take-off frame T, its landing frames, a charge
on the floor including the hop's take-off frame, a charge end, the
settling frame):

- `y` is the floor height of the column containing `x`, taken after the
  frame's horizontal move. `y` is a whole number of pixels.
- `vy` is 0, even on frames where `y` changes, and `ground` stays 1,
  except that on T and on the hop's take-off frame `vy` is already −7 or
  −4 (`riding.md`). The rider hasn't left the floor on those frames, so
  `y` still follows the column there too.
- Following a 1-in-4 slope down never made the rider leave the floor, at
  any speed up to the fastest riding speed, 3.5 (896/256) px per frame,
  which was reached on slope columns. The rider leaves the floor only by
  a jump or a hop.

## Speed, turns and charges

The speed rule of `riding.md` applies unchanged on slopes, uphill and
downhill, steered or not: the same targets, the same steps, no slower
uphill and no faster downhill. A rider at rest on a slope stays where it
is (5 102 frames at rest on slope columns). Turns, jumps, charges, hops,
charge ends and settling frames start and last exactly as on flat floor.

## Leaving the floor

The first airborne frame of a jump (T + 1) or of a hop (the frame after
the hop's take-off frame) starts from the **higher** of two heights:

- `y` at the end of the frame before (on the floor of its column), and
- the floor height of the column containing `x` after this frame's
  horizontal move.

The rider then moves by this frame's `vy`, as in `riding.md`. So:

- moving uphill, if this frame's move reaches a higher column, the
  rider starts from that column's height and ends the frame 1 px higher
  than on flat floor;
- moving downhill, a lower column makes no difference: the rider starts
  from the height of the frame before.

This is the take-off rule of `slopes.md` ("Jumping from a slope") for
the walking player, applied to the riding jump and to the hop.

## Landing

Landing is as in `riding.md` and `landing.md`, against the floor of the
column containing `x` after the frame's horizontal move. A frame that
ends at or below that floor height lands too, and the rider is placed on
the floor (492 + 219 of the landings on slope columns ended below it).
The landing frames, the charge end and the jump rules that follow a
landing are as on flat floor.

## Examples

Measured, with positions made relative. The floor columns are those of
a synthetic 1-in-4 slope; each row gives the floor height of the column
containing `x` at the end of the frame.

**A jump going downhill.** Floor at `y` = 0 up to column 99, then 1 px
lower every 4 columns (columns 100 to 103 at `y` = 1, 104 to 107 at 2,
and so on). Riding right at `vx` 512/256 with Right held, Jump pressed on
frame 146 only:

| Frame | Part | `x` | Floor | `y` | `vy` |
|---|---|---|---|---|---|
| 145 | riding | 115.640625 | 4 | 4 | 0 |
| 146 = P | crouch | 117.640625 | 5 | 5 | 0 |
| 150 | crouch | 125.640625 | 7 | 7 | 0 |
| 151 = T | take-off | 127.640625 | 7 | 7 | −7 |
| 152 = T + 1 | air | 129.640625 | 8 | 0.4375 | −6.5625 |
| 153 | air | 131.640625 | 8 | −5.6875 | −6.125 |

On T + 1 the new column (8) is lower than the height of
T (7), so the rider starts from 7: 7 − 6.5625 = 0.4375.

**A charge going uphill.** A slope rising to the right, 1 px every 4
columns (columns 208 to 211 at `y` = 11, 212 to 215 at 10, 216 to 219 at
9, and so on). Riding right at `vx` 512/256 with Right held, Action
pressed on frame 183 only (C = 183):

| Frame | Part | `x` | Floor | `y` | `vy` |
|---|---|---|---|---|---|
| 183 = C | charge | 191.6875 | 16 | 16 | 0 |
| 192 | charge | 209.6875 | 11 | 11 | 0 |
| 193 = C + 10 | hop take-off | 211.6875 | 11 | 11 | −4 |
| 194 | charge, air | 213.6875 | 10 | 6.4375 | −3.5625 |
| 195 | charge, air | 215.6875 | 10 | 3.3125 | −3.125 |
| 206 | charge, air | 237.6875 | 4 | −2.1875 | 1.6875 |
| 207 = C + 24 | charge, landed | 239.6875 | 4 | 4 | 0 |
| 211 = C + 28 | charge end | 247.6875 | 2 | 2 | 0 |

`vx` is 524/256 on frame 183 (Action held: target 3.5) and 512/256 from
frame 184. The hop starts from the higher column on frame 194: 10 −
3.5625 = 6.4375 (from 11 it would be 7.4375). Frame 207 would end at
−0.0625, 4.0625 px above the floor of its column, so the rider lands and
is placed at 4. Going uphill the hop lands on C + 24 instead of C + 26.
The charge end starts on C + 28 and lasts 12 frames, and C + 40 is the
settling frame, as on flat floor.

## The second character

With the second character as the player (leading, the first character
following), riding on 1-in-4 slopes follows every rule of this file. The
floor height is taken column by column on the same frames, a jump or hop
that leaves the floor starts from the higher of the old height and the new
column's floor, and landings are as in "Landing". The only differences are
the speed targets of `riding.md` ("The second character"), which apply on
slopes as on flat floor; the targets, the divisors, the turns, the vertical
speeds and the steered frames are used as that file gives them.

Observed with a setup method, on the same long flat floor and the same
1-in-4 slopes as above (no walkers on screen near the slopes): 300 runs of
random inputs as in `riding.md` with the second character leading and the
first following, 359 878 frames, 112 337 of them over slope columns. They
held 5 041 jumps, 5 677 charges, 2 567 hops and 2 063 turns, 1 752
landings on slope columns, and 606 take-offs whose first airborne frame
moved into a column of a different height (328 higher, 278 lower). In every
frame of every run, `x`, `y`, `vx`, `vy`, `ground`, `facing` and the part
of riding were exactly as the rules give, with the second character's
targets. With the first character's targets, every run differed from the
first moving frame.

Not checked: these rules in ordinary play, at the level's own animal and
slopes, and with the first character leading and the second following.

## Not covered

- Steep faces (`steep-faces.md`), walls and ledges while riding. The
  rider was seen to be pushed down a steep face and blocked by a rising
  one, as a walking player is, but with different timing. It also left
  the floor while still over the last column of a face that ends at a
  drop, and jumps and turns started on a face didn't behave as on other
  floor. None of this is covered.
- Slopes other than 1 px per 4 columns.
- Walkers and other objects on slopes while riding (`riding-walkers.md`
  covers flat floor only).
- Trace state names for riding (as in `riding.md`).
