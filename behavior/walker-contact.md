# Touching a walker: hurt or bounce

Titles: `title-1`

When contact between the player and a walker (`walker.md`) hurts the
player (`knock-out.md`, `losing-a-character.md`) and when it gives a
bounce (`bounce.md`). What happens after the hit or the bounce is in
those files.

Frames are logic frames at 60 Hz, numbered as in a trace. `x` grows to
the right. Measured with the first character alone, on two walkers of
different kinds at several points of a level, by inputs alone (about
21 000 frames near a walker, 668 hits and bounces, walking, standing,
jumping and falling onto them, facing toward and away from them), and by
placing the player at chosen offsets from a walker in poses reached by
inputs, one pixel apart, to find the exact limits.

## Kinds of walker

Two kinds of walker were measured. They move the same way (`walker.md`)
but differ in size:

- the **small walker**,
- the **large walker**: its ranges below are those of the small walker
  widened by 3 px on each side and raised by 26 px at the top.

## Offsets

Contact is decided from whole-pixel positions. Round each position down:
`x` toward the left, and the vertical position toward the floor (down).
Using the rounded positions, let

- **I** = the player's `x` minus the walker's `x`;
- **U** = how many pixels the player's position is above the walker's
  (0 when both stand on the same flat floor; positive when the player is
  higher).

The player's **facing** is as in a trace. The walker's **heading** is the
way it is moving. All values below were measured with walkers walking at
full speed (1 px per frame); walkers that are turning, speeding up, stopped
or frozen are not covered.

## Being hurt

The player is hurt on frame **H** (the hit frame of `knock-out.md` and
`losing-a-character.md`) when, with the positions **at the end of
H − 1**, both of these hold:

1. **I** is within the hurt range for the walker's kind, the player's
   facing and the walker's heading (table below);
2. **U** is within the vertical reach for the player's state at the end
   of H − 1:
   - **on the ground** (standing, walking or running; not the take-off
     frame of a jump, which counts as jumping): the player and the
     walker on the same floor or slope. Measured with U from −6 to 4; no
     other vertical limit was seen.
   - **jumping, early**: from the take-off frame T (the frame carrying
     `jump_start`, `jump-arc.md`) up to T + 5: U at most **11**
     (small walker) or **37** (large walker).
   - **jumping, later**: from T + 6 on: U at most **16** (large walker).
     A small walker never hurts a player in this state for U from −4 up
     (lower U wasn't measured), so on flat ground a jump that is past
     T + 5 when it reaches a small walker isn't hurt by it.

So a hit is always decided one frame ahead: the frame on which the
player first overlaps the walker is an ordinary frame, and the player is
hurt on the next one (where, as `knock-out.md` says, the player doesn't
move).

If the conditions above hold at the end of a frame, the player is hurt
on the next frame even if that frame would otherwise give a bounce.

Hurt ranges for I (both ends included):

| Walker | Player facing | Walker heading left | Walker heading right |
|---|---|---|---|
| small | right | −19 to 24 | −18 to 25 |
| small | left | −25 to 18 | −24 to 19 |
| large | right | −22 to 27 | −21 to 28 |
| large | left | −28 to 21 | −27 to 22 |

The ranges were the same standing, walking and in the air (measured on
both ends for the small walker, and at the low end for the large walker
in the air).

## Bouncing

On frame **B** the player bounces (`bounce.md`) when, after the player
has moved on B (`bounce.md`, the bounce frame, step 1), all of these
hold:

1. the player is airborne: B isn't a landing frame (`landing.md`);
2. the player isn't rising fast: the `vy` the player has on B after this
   frame's gravity, before the bounce replaces it, is **greater than
   −1.25**. A player still rising slowly can bounce (−1.1875 bounces;
   −1.25 doesn't);
3. **I** is within the bounce range below, using the player's position
   at the end of B and the walker's position at the end of **B − 1**
   (before its own move on B);
4. **U**, from the same two positions, is at most **24** (small walker)
   or **50** (large walker).

There is no lower limit in the cases measured: on the walker's own floor
the landing rule (`landing.md`) comes first, so a player who would end B
less than 5 px above the floor lands instead of bouncing.

Bounce ranges for I (both ends included):

| Walker | Player facing | Walker heading left | Walker heading right |
|---|---|---|---|
| small | right | −21 to 34 | −20 to 35 |
| small | left | −35 to 20 | −34 to 21 |
| large | right | −24 to 37 | −23 to 38 |
| large | left | −38 to 23 | −37 to 24 |

The bounce ranges are wider than the hurt ranges on both sides, and a
player who is falling or rising slowly is checked for a bounce on every
frame, so a jump that comes down onto a walker bounces before it can be
hurt.

## Examples

Synthetic. Positions are at the end of each frame.

A player facing right walks at 1.25 px per frame into a small walker
heading left:

| Frame | Player `x` | Walker `x` | I | Notes |
|---|---|---|---|---|
| 1 | 80.5 | 101.75 | 80 − 101 = −21 | outside −19 to 24 |
| 2 | 81.75 | 100.75 | 81 − 100 = −19 | inside: hurt on the next frame |
| 3 | 81.75 | 99.75 | | H: hurt, the player doesn't move |

A player facing right falls onto a small walker heading left, standing
on the same floor:

| Frame | Player `x` | Height above the floor | Walker `x` | Notes |
|---|---|---|---|---|
| 10 | 177.5 | 30.25 | 200.5 | |
| 11 | 179 | 22.625 | 199.5 | I = 179 − 200 = −21 (walker as on frame 10), U = 22: bounce |

A player facing right jumps beside a large walker heading left, I = 0 on
every frame, take-off on frame T:

| End of frame | U | Next frame |
|---|---|---|
| T + 5 | 30 | hurt (early reach: at most 37) |
| T + 6 | 30 | hurt only if U ≤ 16 (later reach): not hurt |

## Not covered

- Walkers that aren't walking at full speed (turning, speeding up,
  stopped at a barrier, frozen) and walkers already bounced on.
- Contact while crouching, carrying, after a bounce, or being knocked
  back. Rolls into walkers are in `rolling-into-walkers.md`.
- Falls without a jump (walking or rolling off a ledge): the player's
  vertical reach was seen to differ from that of a jump.
- Walkers above the player (U below −6 on the ground, below −4 in the
  air), and the lower end of the vertical reach in the air.
- The other character, and hits while two characters are present.
- Other kinds of enemies, and how to tell the two kinds of walker apart
  other than by these sizes.
