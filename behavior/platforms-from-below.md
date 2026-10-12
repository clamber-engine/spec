# Platforms from below

Titles: `title-1`

What happens when a player who is jumping meets ground that is above
them: a platform with open space under it. The player is never stopped
by anything above their head. They rise into the platform from below,
pass up through it, and are lifted onto its top or fall back down
through it. Frames are logic frames at 60 Hz, numbered as in a trace.
`x`, `y`, `vx` and `vy` are as in a trace: right and down are positive,
speeds in px per frame. Speeds are whole multiples of 1/256 px per frame
and are written both ways, as in `ground-speed.md`. "Left", "Right" and
"Jump" are the neutral inputs `L`, `R`, `J` of the scenario format.

Columns and face columns are as defined in `steep-faces.md`, and "higher"
means higher on screen (smaller `y`). "How far a surface is above the
feet" means the player's `y` minus that surface's `y`.

## Platforms

A **platform** is ground with a lower floor beneath it in the same
columns and open space between the two. Each column of a platform has a
**top**: the surface the player stands on. The platform also has an
**underside**, a height below its tops. Between the underside and a
column's top is that column's **layer**. Feet exactly level with the
underside are in the layer. Feet exactly level with the top are not:
they are on or above the top.

The underside is a property of the platform. It is not at a fixed
distance below the top. On the platform where it was measured, the
underside was 15 px below the top of the platform's flat part. Each end
of that platform was a face (4 px per column) sloping down from the flat
part to the underside. The underside was at the same height under the
whole platform, ends included, so the lowest column of each end had its
top level with the underside.

## When the rules apply

On every frame that starts with the player airborne, after the frame's
new `vx` (`air-control.md`) and new `vy` (`jump-arc.md`, `walk-off.md`)
are worked out. The rules below replace the use of a platform column's
top as "the floor" in `air-walls.md` and `landing.md` where they say so.
Everywhere else those files apply unchanged, with a platform column's
top as its floor.

## Inside a platform

The player is **inside** a platform from the end of a frame that starts
with the feet below the underside and ends with them in the layer of the
column the player is in. They stay inside, frame after frame, while
their feet stay in the layer. They stop being inside at the end of the
first frame on which the feet are below the underside, at or above the
top, or lifted onto the top (below).

While inside:

- **Moving sideways**: the platform doesn't block the player or lift
  them. `air-walls.md` is not applied to the platform's columns, so `x`
  moves by `vx` as usual.
- **Rising** (`vy` < 0 after the frame's speed change): if the feet
  before the frame are **2 px or less below the top** of the column the
  player moves into, the player is lifted onto it. First the player is
  moved up by the smallest whole number of pixels that brings the feet
  level with or above that top. Then `y` moves by the frame's `vy` as
  usual. The fraction of a pixel in `y` doesn't change before the
  vertical move. The player is no longer inside. They stay airborne,
  rise and fall as usual, and land on the top by the rule of
  `landing.md`.
- Feet **more than 2 px below** the top: `y` moves by `vy` and nothing
  else happens, even if the move takes the feet past the top. A player
  who ends such a frame at or above the top is above the platform and
  lands on it by `landing.md` as usual.
- **Falling** (`vy` > 0), or `vy` exactly 0: `y` moves by `vy` and
  nothing else happens. The player never lands on the top from inside.
  They fall back down through the platform and leave it through the
  underside, then land on the floor beneath by `landing.md`.

The 2 px limit is exact to 1/32 px, the finest step seen: feet 2 px
below the top were lifted, and feet 2 1/32 px below were not.

## Moving sideways into a platform from outside

When the player is not inside, a platform column the player would move
into (`x + vx`, as in `air-walls.md`) is treated as follows, using the
feet **before** the frame's vertical move:

| Feet before the frame | The new column counts as |
|---|---|
| below the underside | no platform: only the floor beneath is compared, as in `air-walls.md` |
| level with the underside or higher | a floor at the column's top, compared as in `air-walls.md` |

So with the feet at or above the underside, a face column of the
platform's end whose top is above the feet blocks the player at any
height difference. A column that isn't a face column blocks the player
when its top is more than 8 px above the feet, and the player is stepped
onto it when it is 8 px or less above. A player who passes under the end
with the feet below the underside, and then rises into the layer, is
inside.

## Falling onto the top

A player falling onto a platform from above lands on its top by the rule
of `landing.md`, with one exception. A falling frame whose new position
is **below the underside** is not a landing on that column's top, even
if the feet started the frame at or above the top and would land by
`landing.md`. The player goes on falling. This was seen only in
controlled frames, on the lowest column of a sloped end, whose top is
level with the underside.
There, feet that started the frame 1 to 2 px above that top and ended
it just below the top were not caught.

## What the trace shows

Rising into, through and out of a platform adds nothing to the trace.
The player is airborne throughout (`ground` 0, state `jump` or `fall` as
usual) and no event is added. The landing on the top, or on the floor
beneath after falling back, is an ordinary landing row with `land`.

## Measured cases

Measured with the lead character in ordinary jumps, by inputs alone (494
runs from standing, walking and running, about 13 000 airborne frames)
and in controlled single frames (about 4 700), on two platforms, each
80 px above the floor beneath its flat part:

| Platform | Seen by inputs | Seen in controlled frames |
|---|---|---|
| Flat top, faces of 4 px per column at both ends, underside 15 px below the flat top, floor beneath sloping 1 in 4 under the ends | straight-up jumps from floors 76 to 80 px below the top: falling back and being lifted, both sides of the 2 px limit; walking and running jumps from both sides: passing under the ends, blocked by the ends, stepped onto the flat top from the end, rising inside across many columns | moving sideways into every end column and the first flat column, from both sides, rising and falling, feet from below the underside to above the top |
| Flat top, floor beneath flat | straight-up jumps: falling back, being lifted, landing on the top | — |

Inside a platform, `vy` from about −4.5 to +3.7 px per frame was seen,
and horizontal speeds up to 3 px per frame. No frame by inputs or in
controlled frames differed from the rules above.

## Examples

Synthetic geometry. `y` is down-positive, as in a trace. Floor at
`y` = 0 for columns 100 to 299. A platform with its top at `y` = −78 for
columns 150 to 249 and its underside at `y` = −63. Its left end is a face
of 4 px per column: columns 146, 147, 148 and 149 have tops at −63, −67,
−71 and −75. Jump not held (gravity 112/256 = 0.4375) unless said.

**Lifted onto the top.** Inside, `x` = 200.5, `y` = −70.25, `vx` = 0 and
`vy` = −704/256 before frame 1:

| Frame | `x` | `y` | `vx` (1/256) | `vy` (1/256) | `ground` | Note |
|---|---|---|---|---|---|---|
| 1 | 200.5 | −72.5625 | 0 | −592 | 0 | Inside |
| 2 | 200.5 | −74.4375 | 0 | −480 | 0 | Inside |
| 3 | 200.5 | −75.875 | 0 | −368 | 0 | Inside |
| 4 | 200.5 | −76.875 | 0 | −256 | 0 | Started 2.125 px below the top: not lifted |
| 5 | 200.5 | −79.4375 | 0 | −144 | 0 | Started 1.125 px below: up 2 px to −78.875, then `vy` |
| 6 | 200.5 | −79.5625 | 0 | −32 | 0 | |
| 7 | 200.5 | −78 | 0 | 0 | 1 | Lands on the top (`landing.md`) |

**Falling back through.** Inside, `x` = 200.5, `y` = −69.5, `vx` = 0 and
`vy` = −640/256 before frame 1. The highest point is 2.5625 px below the
top, so the player is never lifted:

| Frame | `x` | `y` | `vx` (1/256) | `vy` (1/256) | `ground` | Note |
|---|---|---|---|---|---|---|
| 1 | 200.5 | −71.5625 | 0 | −528 | 0 | |
| 2 | 200.5 | −73.1875 | 0 | −416 | 0 | |
| 3 | 200.5 | −74.375 | 0 | −304 | 0 | |
| 4 | 200.5 | −75.125 | 0 | −192 | 0 | |
| 5 | 200.5 | −75.4375 | 0 | −80 | 0 | Started 2.875 px below the top: not lifted. Highest point |
| 6 | 200.5 | −75.3125 | 0 | 32 | 0 | Falling: not lifted, doesn't land on the top |
| 12 | 200.5 | −65.375 | 0 | 704 | 0 | Still inside (frames 7 to 11 left out) |
| 13 | 200.5 | −62.1875 | 0 | 816 | 0 | Below the underside: no longer inside |

The player goes on falling and lands on the floor at `y` = 0.

**Passing under the end and rising inside.** Not inside, Right and Jump
held (gravity 72/256 = 0.28125), `vx` taken as a steady 512/256,
`x` = 147.25, `y` = −58 and `vy` = −1100/256 before frame 1:

| Frame | `x` | `y` | `vx` (1/256) | `vy` (1/256) | `ground` | Note |
|---|---|---|---|---|---|---|
| 1 | 149.25 | −62.015625 | 512 | −1028 | 0 | Feet below the underside: column 149's top is ignored |
| 2 | 151.25 | −65.75 | 512 | −956 | 0 | Started below the underside, ends in the layer: inside |
| 3 | 153.25 | −69.203125 | 512 | −884 | 0 | Inside: moving sideways freely |
| 4 | 155.25 | −72.375 | 512 | −812 | 0 | |
| 5 | 157.25 | −75.265625 | 512 | −740 | 0 | |
| 6 | 159.25 | −77.875 | 512 | −668 | 0 | |
| 7 | 161.25 | −81.203125 | 512 | −596 | 0 | Started 0.125 px below the top: up 1 px to −78.875, then `vy` |

**Blocked by the end.** Not inside, Right and Jump held, `x` = 145.5,
`y` = −64.5, `vx` = 512/256 and `vy` = −800/256 before frame 1. Column 147
(top −67) is a face column 2.5 px above the feet, and the feet are above
the underside:

| Frame | `x` | `y` | `vx` (1/256) | `vy` (1/256) | `ground` | Note |
|---|---|---|---|---|---|---|
| 1 | 145 | −67.34375 | 0 | −728 | 0 | Blocked (`air-walls.md`) |

**Stepped onto the flat top from the end.** Not inside, Right held,
`vx` taken as a steady 512/256, `x` = 149.5, `y` = −71 and
`vy` = −600/256 before frame 1. Column 151 is not a face column and its
top is 7 px above the feet:

| Frame | `x` | `y` | `vx` (1/256) | `vy` (1/256) | `ground` | Note |
|---|---|---|---|---|---|---|
| 1 | 151.5 | −79.90625 | 512 | −488 | 0 | Stepped onto: up 7 px to −78, then `vy` |
| 2 | 153.5 | −81.375 | 512 | −376 | 0 | |
| 3 | 155.5 | −82.40625 | 512 | −264 | 0 | |
| 4 | 157.5 | −83 | 512 | −152 | 0 | |
| 5 | 159.5 | −83.15625 | 512 | −40 | 0 | |
| 6 | 161.5 | −78 | 512 | 0 | 1 | Lands on the top |

## Not covered

- Ceilings that stop a rising player. None was found on the measured
  level: everything overhead was a platform that could be passed from
  below.
- Where the underside of other platforms is, and how it follows from
  level data. On the second platform only straight-up jumps were
  measured, and they don't show where the underside is.
- Platforms with square ends (not faces), platforms thinner than 15 px,
  and platforms whose ends slope at other rates.
- Moving sideways out of a platform's columns while inside, and a
  take-off from a floor that is itself in a platform's layer.
- Getting down through a platform from its top.
- Rolls, bounces off enemies (`bounce.md`), carrying (`carrying.md`),
  being knocked back (`knock-out.md`) and the other character inside a
  platform.
- Moving platforms, and entities overhead.
