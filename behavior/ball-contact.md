# Balls touching the player

Titles: `title-1`

When a ball thrown by a thrower, or placed in a level (`thrower.md`),
hurts the player. What happens after the hit is in `knock-out.md` (no
other character) and `losing-a-character.md` (two characters), and
protection after losing a character is in `protection.md`. The thrower
itself touching the player is in `thrower-contact.md`, and an object
the player throws or carries meeting a ball in `thrown-objects.md`,
"Hitting a ball".

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

Two characters were checked in a few controlled runs (below). The
second character under control is measured in its own section
("The second character under control").

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
teetering at the edge of a ledge, a jump up to five frames after its
take-off frame T (`jump-arc.md`): on frames up to T + 5, and a fall
after walking off a ledge without a face (`walk-off.md`, "Ledges
without a face"): the walking pose goes on through the fall and the
landing (seen with walkers, `walker-contact.md`).

**Airborne**: a jump from T + 6 on, until some frames after it lands
(below). A fall after walking off a ledge with a face, from shortly
before the leave frame of `walk-off.md` (2 frames before it in most
cases, 1 or 3 in others; 4 on a ledge measured with walkers) until some
frames after it lands.

After landing, the airborne pose goes on for a time that depends on the
jump. Measured, with L the landing frame and the player not jumping
again: a full-height jump standing still, airborne up to L + 6 and
upright from L + 7; a hop (jump input held for one frame), up to
L + 3; a jump while walking, up to L + 4; a jump out of a roll, up to
L + 1; a walk-off fall of 15 frames (from a ledge with a face), up to
L + 8. In the random runs the airborne pose ended anywhere from L − 1 to
L + 7 after a jump, and up to L + 8 after a walk-off fall. The same kind
of jump doesn't always end its airborne pose on the same frame: in 24
standing and walking jumps on flat floor measured with walkers
(`walker-contact.md`, "Poses"), 16 were upright from L + 4 (full-height
jumps among them) and 8 from L + 7.

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

## The second character under control

Measured on the same level, near the same thrower, with the second
character under control after the swap of `second-character.md` and the
first character following:

- with a setup method used to arrange the situations: a ball one pixel
  apart from the player in every position around them at the end of a
  frame, with the player standing, in the fidget pose, turning,
  walking, running, rolling and the end of a roll, crouching and
  standing up from a crouch, jumping and hopping from standing and
  walking, and landing from them, on every frame of each pose, about
  500 000 situations in all;
- by inputs alone, 400 runs near the thrower, 132 562 frames, 249 hits
  by balls, every one on the frame the rules above give and none on any
  other frame; the same 400 runs seen again with no ball on the screen
  (a setup method was used) showed that 248 of the 249 hits did not
  happen on that frame without balls (the other one coincided with the
  thrower), and that 94 hits next to the thrower happened on the same
  frame without balls (157 frames with the player in a pose not listed
  below were left out, among them the bounce).

Everything under "Offsets", "When a ball hurts" and "The ball's turn"
holds for the second character: the same I and A, a hit on F + 1 from
the pose on F and the ball's turn at the end of F − 1, no hit on the
frame the ball bounces or stops ignoring floors. What differs is the
reach, and that it changes with fewer poses. Ranges, facing right, with
facing left turned around as above:

| Pose | I, facing right | I, facing left | A |
|---|---|---|---|
| standing, turning, fidget | −12 to 9 | −11 to 10 | to 35 |
| walking, running, rolling, airborne | −18 to 10 | −12 to 16 | −1 to 35 |
| crouching | −19 to 12 | −14 to 17 | to 22 |

The low end of A was measured with the player in the air. On the
ground a ball can't be below the floor, so lower values were not
measured there. The fidget pose has the same range as standing.

Which pose the player has, measured with the second character:

- **Standing** (with turning and the fidget pose): until the player
  starts to walk, and again once the slow-down after walking is over. A
  turn keeps the standing range through all its frames, also while the
  player is already moving in the new direction; the range switches to
  the walking one on the first frame of walking.
- **Walking**, **running** and **rolling** share one range. A roll that
  ends and runs on into walking, or into the stop after a roll, doesn't
  change it.
- **Jumping**: the airborne range holds from the press frame P of the
  jump (`jump-input.md`; measured from standing), through the whole
  flight and the landing. After the landing frame L (the first frame on
  which the player is on the ground again) it goes on to L + 16, and a
  player who is standing again has the standing range from L + 17. This
  was the same for a hop (jump input held for one frame) and a
  full-height jump standing still; a jump while walking carries straight
  on into the walking range.
- **Crouching**: from the first frame Down is held on the ground, and on
  the first 12 frames on which Down is no longer held (the recovery);
  the standing range follows.
- **Teetering** (`player-frames.md`, "Standing still", "Second
  character"): the walking range, from the first frame with a teetering
  picture (`s-teeter-1`, S + 4) through the whole teetering loop, for as
  long as the player teeters. The frames before it, S to S + 3, have the
  standing range. A turn away from the ledge has the standing range from
  its first frame, and a jump the airborne range from its press frame, as
  elsewhere.
- **Walking off a ledge with a face**: the walking range on every frame
  from F, the first frame that ends on the face, through the fall and the
  landing frames G to G + 6 (`player-frames.md`, "A fall from a steep
  face", "Second character"). On G + 7, when the player is standing
  again, it has the standing range, and with Left still held and the
  player walking on it has the walking range.

Teetering and walking off a ledge with a face were measured at one
ledge, the left-hand end of a floor, with the player facing left (the
ranges for facing right are those of the table, turned around, and were
not measured). With a setup method used to arrange the situations (a
ball one pixel apart from the player in every position around them at the
end of a frame, with the player standing still at the ledge, walking,
jumping, or turning at it, and the fall ending on the floor 145 px
below), the edges were those of the table on every frame seen:
629 frames with the player teetering (every picture `s-teeter-1` to
`s-teeter-18`, over the first 250 frames of a teeter),
325 on the face, in the fall and on its landing frames, and the
standing range on the frames after them. The vertical range was that of
walking: on the ground its low end was not measured, as above.

A ball hitting the second character hurts it with the same result as a
hit on the first character in the same situation: with the first
character following, the second character is lost as in
`losing-a-character.md`.

## Not covered

- The ground action meeting balls. Thrown and carried objects meeting
  balls are in `thrown-objects.md`, "Hitting a ball", and in
  `holder-breaking.md`.
- These teetering and walk-off ranges were seen only in arranged
  situations, not in play by inputs alone with balls coming at the
  player.
- Poses not listed above: bouncing (`bounce`), carrying an object,
  being hurt, swapping characters. For the second character under
  control, also: the ground action, walking off ledges without a face,
  carrying an object, and the first frames after a swap.
- Exactly when the airborne pose ends after a landing and when it
  starts before a walk-off, beyond the cases listed; when the standing
  pose starts after slowing down.
- A ball's appearance frame (hurting on it), and balls touching the
  player during a knock-out.
