# Slopes

How a player moves on sloped floor: horizontal speed, height while
grounded, stopping, jumping off and landing on a slope, and rolling.
Frames are logic frames at 60 Hz, numbered as in a trace. `x`, `y` and
`vy` are as in a trace: down is positive, speeds in px per frame.
Measured with the lead character on gentle slopes that rise or fall
**1 px for every 4 px** across, both uphill and downhill, moving right and
left: walking, running, stopping, standing, jumping (standing,
walking and running jumps, short and long holds) and rolling (normal
and fast rolls, as named in `roll-speed.md`).

## Floor height

The floor's surface has one height for each 1-px-wide column of `x`. On
flat floor every column has the same height. On a 1-in-4 slope the height
changes by **1 px every 4 columns**: four columns at one height, then four
at the next, with no columns in between. Where along the slope each 1-px
step falls is part of the level's geometry.

## Grounded on a slope

While the player is grounded, on every frame:

- `y` is the floor height of the column containing `x` (the column of the
  whole-pixel part of `x`), taken after the frame's horizontal move. `y`
  is always a whole number of pixels, with no fraction.
- Nothing else matters: the same column gives the same `y` whatever the
  speed, direction, facing or the fraction of `x`.
- So `y` changes by whole pixels on the frames where `x` crosses into a
  column of a different height, and doesn't change on other frames.
- In a trace, `vy` is 0 on these frames, even though `y` changes, and
  `ground` stays 1. Following a 1-in-4 slope down never made the player
  airborne at any speed measured: walking, running, and normal and fast
  rolls, up to 1000/256 px per frame (just under 4) going downhill. There
  is no `leave_ground`. The fastest grounded speed measured anywhere on a
  slope was 1014/256 px per frame, in a fast roll.

## Horizontal speed

Slopes don't change horizontal motion. The rules in `ground-speed.md`
(target speeds, the speed rule, stopping) and `roll-speed.md`
apply on 1-in-4 slopes exactly as on flat floor, uphill and downhill:

- Walking and running speed is the same as on flat floor: no slower
  uphill, no faster downhill.
- Releasing all input on a slope stops the player in the same distance
  as on flat floor.
- A player standing still on a slope stays still. There is no sliding.
- Roll speeds and roll lengths are the same as on flat floor, for
  normal and fast rolls, uphill and downhill, rolling right and left.

## Jumping from a slope

The take-off frame **T** (`jump-arc.md`) is still a grounded frame, so
the rule above applies on it: on T the player moves horizontally as usual
and `y` becomes the floor height of the column under the new `x`. On flat
floor that means `y` is unchanged, as `jump-arc.md` states; on a slope `y`
changes by 1 px on T if `x` crosses into the next 4-column step.

From T + 1 the arc is the one in `jump-arc.md`, starting from `y` on T:
the same `vy` on every frame, the same gravity and the same effect of
holding jump. The slope only changes where the jump starts and where it
ends.

## Landing on a slope

The landing rule in `landing.md` applies, measuring the height above the
floor of the column containing `x` **after** the frame's horizontal move:

- The player lands if they are moving down and are now less than 5 px
  above that floor, or at or below it.
- On landing, `y` becomes that column's floor height, `vy` 0, `ground` 1.

When `x` crosses into a column with a different floor height on the
landing frame, the player lands on the new column's height, not the one
under the previous frame's `x`.

## Examples

A synthetic floor: columns up to 99 at `y` = 0, then going down 1 px every
4 columns to the right (columns 100 to 103 at `y` = 1, 104 to 107 at
`y` = 2, 108 to 111 at `y` = 3, and so on).

**Walking down.** Walking right at 2 px per frame, `x` = 96.5 and `y` = 0
before frame 1:

| Frame | `x` | `y` | `vy` | `ground` |
|---|---|---|---|---|
| 1 | 98.5 | 0 | 0 | 1 |
| 2 | 100.5 | 1 | 0 | 1 |
| 3 | 102.5 | 1 | 0 | 1 |
| 4 | 104.5 | 2 | 0 | 1 |
| 5 | 106.5 | 2 | 0 | 1 |
| 6 | 108.5 | 3 | 0 | 1 |

Walking left back up the same slope from 108.5 at −2 px per frame gives
the same `y` for each column, in reverse: 106.5 → 2, 104.5 → 2,
102.5 → 1, 100.5 → 1, 98.5 → 0.

**Jumping from the slope.** Same walk, jump pressed and held so that T is
frame 6 (jump held through these frames):

| Frame | `x` | `y` | `vy` | `ground` |
|---|---|---|---|---|
| T − 1 = 5 | 106.5 | 2 | 0 | 1 |
| T = 6 | 108.5 | 3 | −7 | 1 |
| T + 1 | 110.5 | −3.71875 | −6.71875 | 0 |
| T + 2 | 112.5 | −10.15625 | −6.4375 | 0 |
| T + 3 | 114.5 | −16.3125 | −6.15625 | 0 |

**Landing across a step.** Falling, before the frame: `x` = 115.5
(column 115, floor `y` = 4), `y` = −3.25, `vy` = 5.5. On the frame `vy`
becomes 5.9375 (gravity 0.4375), `x` moves to 117.5 (column 117, floor
`y` = 5) and `y` to 2.6875, which is 2.3125 px above that column's floor.
The player lands with `y` = 5 (not 4), `vy` 0, `ground` 1.

## Not covered

- Turning around on a slope (all turns measured were on flat floor).
- Slow rolls on a slope, and grounded speeds above 1014/256 px per frame
  on a slope.
- Slopes steeper or shallower than 1 in 4, and where a slope meets a
  ledge, wall or ceiling.
- Whether the 5 px landing margin is ever decided by the previous
  column's floor (landings measured here were placed on the new column's
  floor in every case; none fell exactly between the two rules).
- Crouching, and the Down + Action ground action, on slopes.
- Walking off the end of a slope, moving or tilting floors, and enemies
  or objects on slopes.
- Carrying objects: holding Action while moving into some objects picks
  them up (and doesn't start a roll), which changes speed. No spec covers
  this yet.
- The other character.
