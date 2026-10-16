# Balls touching the player

Titles: `title-1`

When a ball thrown by a thrower, or placed in a level (`thrower.md`),
hurts the player. What happens after the hit is in `knock-out.md` (no
other character) and `losing-a-character.md` (two characters), and
protection after losing a character is in `protection.md`. The thrower
itself touching the player is not covered here.

Frames are logic frames at 60 Hz, numbered as in a trace. `x` and `y`
are as in a trace: right and down are positive. Measured on the first
level with the first character under control and alone, near the
level's thrower:

- by setting a ball's position next to the player at the end of a
  frame, one pixel apart, with the player in poses reached by inputs
  (standing, the fidget pose, turning, walking, running, rolling and
  its end, crouching, the ground action, teetering at a ledge, jumping
  and hops from standing, walking, running and a roll, walking off a
  ledge), on every frame of each pose, about 540 000 placements in all;
- by inputs alone, walking, running, rolling, crouching and jumping at
  random near the thrower: 400 runs, 106 397 frames, 188 hits by balls,
  every one on the frame the rules below give and none on any other
  frame (leaving out 58 frames on which the player was bouncing, a pose
  not covered, 3 of them followed by a hit); and the same 400 runs
  again with every ball removed as soon as it appeared, to tell hits by
  balls from hits by the thrower.

Two characters were checked in a few controlled runs (below).

## Offsets

Contact is decided from whole-pixel positions. Using the positions at
the end of a frame, let

- **I** = the player's `x` rounded down minus the ball's `x` rounded
  down;
- **A** = how many pixels the ball is above the player: the player's
  `y` rounded up minus the ball's `y` rounded up (0 when the ball's
  position is level with the player's; positive when the ball is
  higher).

## When a ball hurts

On a frame **F**, once the ball has moved (`thrower.md`, "Each frame",
steps 1 to 3), the player is hurt on frame **F + 1** (the hit frame
H = F + 1 of `knock-out.md` and `losing-a-character.md`) when all of
these hold:

1. the ball didn't bounce on F and didn't stop ignoring floors on F.
   On those two kinds of frame a ball never hurts, whatever its
   position (the same frames on which it isn't removed, `thrower.md`);
2. **I** and **A**, from the player's and the ball's positions at the
   end of F, are within the ranges for the player's **pose on F**
   ("Ranges" below), moved by the ball's **turn as it was at the end of
   F − 1** ("The ball's turn" below);
3. the player isn't protected (`protection.md`).

The player's position is the one after the player's own move on F. A
ball that touches the player only on a frame on which it bounces hurts
one frame later, if it is still within reach at the end of the next
frame.

The ball isn't affected: it goes on moving under `thrower.md` on H and
after (how balls move while the player is knocked out is not covered).
Contact with a ball never makes the player bounce, also when the
player comes down onto it from above: it hurts or does nothing.

Only the character under control is hurt. With two characters, a ball
held on the following character for 30 frames did nothing; a ball on
the character under control hurt on the frame the rules give and the
character was lost as in `losing-a-character.md`. Protection holds
against balls as against walkers: with a ball kept within reach of the
character that took over from frame H + 147, H + 148 or H + 149 on, the
hit came on H + 150; with it within reach from H + 150 on, on H + 151.

## Ranges

For the turn's first part (below). Both ends are included.

| Pose | I, facing right | I, facing left | A |
|---|---|---|---|
| upright | −15 to 19 | −21 to 13 | −3 to 37 |
| airborne | −15 to 16 | −18 to 13 | 18 to 54 |
| rolling | −13 to 19 | −21 to 11 | 1 to 35 |
| crouching | −18 to 10 | −12 to 16 | to 28 |
| fidget | −5 to 22 | −24 to 3 | to 41 |

Facing left, every I range is the facing-right range turned around:
from −(high end) − 2 to −(low end) − 2.

The low end of A was measured with the player in the air. On the
ground, a ball can't be below the floor the player stands on, and every
A from 0 up to the high end was within reach for the upright,
crouching and fidget poses. Rolling, a ball level with the player
(A = 0) is not within reach.

Facing is the player's facing on F, as in the trace: during a turn the
ranges change from one side to the other on the frame the facing
changes.

Within each pose the ranges were the same on every frame measured,
whatever the speed.

## The ball's turn

A ball turns as it flies, in four parts, and its reach moves with it.
Count the ball's **steps** `s`: `s` is 0 for a ball at its start
(`thrower.md`, "A thrown ball"), and goes up by 1 on every frame on
which the ball moves, except a frame on which it bounces or stops
ignoring floors. A ball that appears one frame on (thrown, or placed in
the level) has made its first step on its appearance frame (`s` = 1).

| `s` mod 24 | Part | The ball is drawn | I range | A range |
|---|---|---|---|---|
| 0 to 8 | first | as it is | as in the table | as in the table |
| 9 to 14 | second | upside down | as in the table | both ends + 2 |
| 15 to 20 | third | upside down and mirrored | both ends + 2 | both ends + 2 |
| 21 to 23 | fourth | mirrored | both ends + 2 | as in the table |

So without bounces each part lasts 9, 6, 6 and 3 frames, and the whole
turn 24. A bounce, and the frame a ball stops ignoring floors, each
make the part they fall in one frame longer.

Condition 2 uses the part of the turn the ball was in at the end of
F − 1, before its step on F. Measured: every one of 4 075 frames of 57
ball flights was drawn in the part these rules give.

## Which pose the player has

**Upright**: standing (until the fidget, below), walking, running,
turning, the ground action (from its first frame; `ground-action.md`),
teetering at the edge of a ledge, and a jump up to five frames after
its take-off frame T (`jump-arc.md`): on frames up to T + 5.

**Airborne**: a jump from T + 6 on, until some frames after it lands
(below). A fall after walking off a ledge, from shortly before the
leave frame of `walk-off.md` (2 frames before it in most cases, 1 or 3
in others) until some frames after it lands.

After landing, the airborne pose goes on for a time that depends on the
jump. Measured, with L the landing frame and the player not jumping
again: a full-height jump standing still, airborne up to L + 6 and
upright from L + 7; a hop (jump input held for one frame), up to
L + 3; a jump while walking, up to L + 4; a jump out of a roll, up to
L + 1; a walk-off fall of 15 frames, up to L + 8. In the random runs the
airborne pose ended anywhere from L − 1 to L + 7 after a jump, and up
to L + 8 after a walk-off fall.

**Rolling**: from the frame carrying `roll_start` to the end of the
roll, and then for 3 more frames when Action is held (the roll runs on
into a run), or for 9 more frames when it was released (measured once,
the roll ending at a ledge).

**Crouching**: Down held while grounded, before or without the ground
action.

**Fidget**: a standing player who stands still long enough takes the
fidget pose for 180 frames, and again every 457 frames after that while
still standing. Counting the first frame of the standing pose as 0 (the
standing pose takes over from walking when the slow-down is nearly done,
measured at speeds from 0.07 to 0.14 px per frame), the fidget pose is
on frames 239 to 418 and 696 to 875. Measured facing right and left.

## Examples

Synthetic. Positions are at the end of each frame. A thrown ball has
`s` = 30 at the end of frame 99 (`s` mod 24 = 6: first part), falls
straight down past the player (`vy` 7.0625, 7.5 and 7.9375 on frames
100 to 102), and doesn't bounce. The player stands
facing right at `x` 400.25 (rounded down 400), `y` 300.

| Frame | Ball `x` | Ball `y` | `s` at the end | I | A | |
|---|---|---|---|---|---|---|
| 100 | 390.5 | 255.0625 | 31 | 400 − 390 = 10 | 300 − 256 = 44 | first part (`s` 30 on 99): A above 37 |
| 101 | 390.5 | 262.5625 | 32 | 10 | 300 − 263 = 37 | first part (`s` 31 on 100): within reach |
| 102 | 390.5 | 270.5 | 33 | | | H: hurt |

The same, but the ball started at `s` = 33 on frame 99 (second part on
frame 100: A range −1 to 39): on frame 100 A is 44, above 39; on frame
101 it is 37, within reach; the player is hurt on 102 as before. With
`s` = 45 on frame 99 (fourth part on frame 100: I range −13 to 21, A as
in the table) the outcome is the same, since I = 10 is within both.

A player jumping at a ball: take-off on T. On T + 5 the player is
upright and a ball 40 px above (A = 40) is out of reach (above 37). On
T + 6 the player is airborne: with the ball still 40 px above, A = 40 is
within 18 to 54, and the player is hurt on T + 7.

## Not covered

- The thrower touching the player (in the random runs it knocked the
  player out 168 times, all also without balls), the player bouncing on
  it, and the ground action or thrown objects meeting balls (other than
  in `holder-breaking.md`).
- Poses not listed above: bouncing (`bounce`), carrying an object,
  being hurt, swapping characters; the second character under control.
- Exactly when the airborne pose ends after a landing and when it
  starts before a walk-off, beyond the cases listed; when the standing
  pose starts after slowing down.
- A ball's appearance frame (hurting on it), and balls touching the
  player during a knock-out.
