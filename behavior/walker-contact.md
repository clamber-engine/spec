# Touching a walker: hurt or bounce

Titles: `title-1`, `title-2`

When contact between the player and a walker (`walker.md`) hurts the
player (`knock-out.md`, `losing-a-character.md`) and when it gives a
bounce (`bounce.md`). What happens after the hit or the bounce is in
those files.

The sections below up to "In `title-2`" give the `title-1` values.
"In `title-2`" gives the `title-2` rules: the offsets are taken at
different moments, the ranges depend on the player's pose, and the same
ranges give hurts and bounces.

Frames are logic frames at 60 Hz, numbered as in a trace. `x` grows to
the right. Measured with the first character alone, on two walkers of
different kinds at several points of a level, by inputs alone (about
21 000 frames near a walker, 668 hits and bounces, walking, standing,
jumping and falling onto them, facing toward and away from them), and by
placing the player at chosen offsets from a walker in poses reached by
inputs, one pixel apart, to find the exact limits. The medium walker and
the fidget pose were measured the same way (see "Evidence for the medium
walker and the fidget pose"), and so were the airborne pose and falls
without a jump (see "Evidence for the airborne pose and falls without a
jump").

## Kinds of walker

Three kinds of walker were measured. They move the same way
(`walker.md`) but differ in size:

- the **small walker**,
- the **medium walker**: its reach at the top is 13 px higher than the
  small walker's, and its horizontal ranges are its own (tables below);
  unlike the other two kinds, they move 10 px with the walker's heading,
- the **large walker**: its ranges below are those of the small walker
  widened by 3 px on each side and raised by 26 px at the top.

How an importer tells the kinds apart is in `../formats/placements.md`
("Kinds").

## Offsets

Contact is decided from whole-pixel positions. Round each position down:
`x` toward the left, and the vertical position toward the floor (down).
Using the rounded positions, let

- **I** = the player's `x` minus the walker's `x`;
- **U** = how many pixels the player's position is above the walker's
  (0 when both stand on the same flat floor; positive when the player is
  higher).

The player's **facing** is as in a trace. The walker's **heading** is its
facing (`walker.md`) at the moment its position is taken for the rule:
the end of H − 1 for a hurt, the end of B − 1 for a bounce. A walker
walking at full speed (1 px per frame) faces the way it is moving, so
for it the heading is simply its direction. A walker that is speeding
up, turning or stopped can face one way while it moves the other way or
doesn't move at all; then its facing counts, not its motion ("Walkers
that aren't at full speed" below).

## Being hurt

The player is hurt on frame **H** (the hit frame of `knock-out.md` and
`losing-a-character.md`) when, with the positions **at the end of
H − 1**, both of these hold:

1. **I** is within the hurt range for the walker's kind, the player's
   facing and the walker's heading (table below);
2. **U** is within the vertical reach for the player's **pose** at the
   end of H − 1 (the poses of `ball-contact.md`, "Which pose the player
   has"; "Poses" below gives the parts that matter here) and for whether
   the player is on the ground then:
   - **upright, in the air**: U at most **11** (small walker), **24**
     (medium walker) or **37** (large walker).
   - **upright, on the ground** (standing, walking or running; not the
     take-off frame of a jump, which is in the air for this rule): the
     player and the walker on the same floor or slope. Measured with U
     from −6 to 4; no other vertical limit was seen.
   - **airborne, in the air**: U at most **16** (large walker) or **3**
     (medium walker). A small walker never hurts a player in this pose
     for U from −4 up. So on flat ground a small walker never hurts a
     player in the air in the airborne pose, and a medium walker only
     one who is at most 3 px above its floor.
   - **airborne, on the ground** (the frames after a landing on which
     the airborne pose goes on, and the frames before leaving a ledge
     with a face, "Falls without a jump"): the player and the walker on
     the same floor (measured with U 0 and −1). A medium or large walker
     hurts the player on any of these frames. A small walker hurts only
     on the frames before leaving a ledge with a face and on those that
     show the **landing pictures**; never on a frame that shows one of
     the **closing pictures** of the airborne pose ("Poses" below).
   - **crouching** (on the ground, "Crouching" below): the player and the
     walker on the same floor (measured with U 0).
   - **carrying** (on the ground or in the air, "Carrying" below): as
     upright: on the ground the player and the walker on the same floor
     (measured with U 0), in the air U at most **11** (small walker),
     **24** (medium walker) or **37** (large walker). These tops were
     measured 5 frames after the Jump press; at 5 px above the walker's
     floor the player is reached on every frame of the jump measured.

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
| medium | right | −26 to 22 | −16 to 32 |
| medium | left | −32 to 16 | −22 to 26 |
| large | right | −22 to 27 | −21 to 28 |
| large | left | −28 to 21 | −27 to 22 |

The ranges are those of the upright pose. They were the same standing,
walking and in the air (measured on both ends for the small and medium
walkers, and at the low end for the large walker in the air). The
airborne, fidget, crouching and carrying poses change them.

**Airborne pose.** In the airborne pose the end of the hurt range on the
side behind the player moves 3 px in:

- facing right: the high end is 3 px lower;
- facing left: the low end is 3 px higher.

For example, a player facing right in the airborne pose is hurt by a
large walker heading right for I from −21 to 25, and a player facing
left by a medium walker heading left for I from −29 to 16. Measured for
all three kinds of walker, both facings and both headings: in the air
for the large walker, and on the ground after a landing for all three.

**Fidget pose.** A player who stands still long enough takes the fidget
pose (`ball-contact.md`, "Which pose the player has": for 180 frames,
and again every 457 frames while the player keeps standing). The pose counts at the end of
H − 1, like the positions. In the fidget pose the hurt range for I
shifts:

- facing right: the low end is 10 px higher and the high end 3 px
  higher;
- facing left: the low end is 3 px lower and the high end 10 px lower.

For example, a player facing right in the fidget pose is hurt by a small
walker heading left for I from −9 to 27, by a medium walker heading
right for I from −6 to 35, and by a large walker heading left for I from
−12 to 30. Measured for all three kinds of walker, both facings and both
headings.

**Crouching.** A player who holds Down on the ground crouches
(`ground-speed.md`, "Crouching", which also says when a crouch can't
start). With D the first frame Down is held and U the first frame after
the crouch, as there, the player has the crouching pose on every frame
from D to **U + 8**: while going down and crouched, and on the first
nine frames of getting up (U to U + 8), also when a short press makes
the crouch last its 13 frames. From U + 9 the player is upright again.
The pose counts at the end of H − 1, like the positions: Down first held
on H − 1 already gives the crouching ranges, Down first held on H
doesn't. Crouching while still sliding from a walk is the same. In the
crouching pose the hurt range for I shifts:

- facing right: the low end is 3 px lower and the high end 9 px lower;
- facing left: the low end is 9 px higher and the high end 3 px higher.

So a crouching player is reached 3 px sooner by a walker in front and
9 px later by one from behind. For example, a player facing right and
crouching is hurt by a small walker heading left for I from −22 to 15,
by a medium walker heading right for I from −19 to 23, and a player
facing left by a large walker heading left for I from −19 to 24.
Measured for all three kinds of walker, both facings and both headings
("Evidence for crouching").

**Carrying.** A player who carries an object (`carrying.md`: from
G + 15 of the pick-up, while Action stays held) has the carrying pose on
every frame: standing, walking, turning, in the air through the whole
jump, landing and after it. A jump made while carrying never takes the
airborne pose. With G and Q as in `carrying.md` (the first frame of a
pick-up, and the frame Action is let go), the player also has the
carrying pose on every frame of:

- a pick-up on the ground, G to G + 14 (a pick-up in the air is under
  "Not covered");
- a throw, on the ground or in the air, Q to Q + 42 (also after landing,
  for the frames of the throw that are left);
- a put-down, Q to Q + 12.

From Q + 43 after a throw and from Q + 13 after a put-down the pose is
upright again. A jump that ends a throw early (`carrying.md`,
"Throwing") has the poses of any jump, and a roll that ends it is in
`rolling-into-walkers.md`.
In the carrying pose the hurt range for I shifts:

- facing right: the low end is 1 px higher and the high end 5 px lower;
- facing left: the low end is 5 px higher and the high end 1 px lower.

So the carrying range is 6 px narrower than the upright one, and the
player is reached 1 px later by a walker in front and 5 px later by one
from behind. The facing is the player's facing at the end of H − 1, as
for any hurt; in a turn it changes on the fifth frame of the turn
(`carrying.md`, "Carrying"). For example, a player facing right and
carrying is hurt by a small walker heading left for I from −18 to 19,
and a player facing left by a medium walker heading right for I from
−17 to 25 and by a large walker heading left for I from −23 to 20.
Measured for all three kinds of walker, both facings and both headings
("Evidence for carrying"). Bounces while carrying use the bounce ranges
and limits below unchanged, with one exception: **during a throw the
player doesn't bounce on Q + 1 to Q + 21**, the frames after Q up to
the one on which the object leaves the hands (`carrying.md`, "The
object"). A player falling onto a walker on those
frames goes on falling and is hurt or not by the rule above. A bounce
can happen on Q itself, and from Q + 22 on bounces use the ranges below
again ("Evidence for pick-ups, throws and put-downs").

A medium or large walker that meets the object the player carries can be
hit by it (`thrown-objects.md`); the rule above still decides whether the
player is hurt on the next frame, from the positions at the end of the
frame of the hit. A small walker was never hit by the carried object in
these measurements (player on its floor or up to 45 px above it), but
during a pick-up, a throw or a put-down walkers of all three kinds,
small ones too, were hit by the object in the hands (frames and ranges
in `thrown-objects.md`, "During a pick-up, a throw or a put-down"), and
there too the rule above decided whether the player was hurt on the
next frame.

The same carrying ranges hold when the object holds the missing
character (`holder-breaking.md`). But a medium or large walker that
meets that object breaks it, and then **doesn't hurt the player on the
next frame**: the frame after the hit is the first frame of the pause
while the character joins. A hurt that would have come on that frame
with an ordinary object comes when play goes on instead, with both
characters there (`holder-breaking.md`, "Carried into a walker"); in
the other cases the walker has been hit by then. A walker that
reaches the player from behind without meeting the object hurts as
above. Measured with the placements of "Evidence for carrying" for all
three kinds, at two of its three frames and without the extra heights
in the air (63 336 placements): the walker met the object on exactly
the same placements as with an ordinary object, and every placement
without a hit gave the same hurt or bounce as with an ordinary object.
On the 21 144 placements with a hit the player was never hurt on the
next frame; with an ordinary object, 16 020 of them were hurts on the
next frame.

### Poses

The rules above use the poses of `ball-contact.md`. The parts that
matter for walkers:

- **A jump** is upright from its take-off frame T (the frame carrying
  `jump_start`, `jump-arc.md`) up to T + 5, and airborne from T + 6 on.
  So a jump that reaches a walker by T + 5 has the upright reach (11,
  24 or 37) and the upright ranges, and a later one the airborne reach
  and ranges. The switch is by time: forcing a different upward speed
  at T + 5 or T + 6 doesn't move it.
- **Pictures of the airborne pose.** The airborne pose shows its
  pictures in order: first the pictures of the rise and the fall, then
  six **closing pictures**, then two **landing pictures**, and then the
  player is upright again. The closing pictures and the landing
  pictures show one after the other, each for one frame or more.
- **After a jump** the closing pictures show in the air, before the
  landing frame L; the last of them can still be showing on L. Measured
  in 24 jumps on flat floor, standing and walking, with Jump held from 1
  to 40 frames: 16 still showed the last closing picture on L, showed
  the landing pictures on L + 1 to L + 3 and were upright from L + 4; the
  other 8 (Jump held 8 to 15 frames) showed the landing pictures from L
  to L + 6 and were upright from L + 7. So after a jump a small walker
  can't hurt the player on L + 1 for the first 16 (the landing frame
  still shows a closing picture) and can for the other 8.
- **After a fall from a ledge with a face** the fall shows one picture
  of the rise and fall all the way down, and the closing pictures come
  after landing. Measured for a 13-frame fall: closing pictures on L to
  L + 5, landing pictures on L + 6 to L + 8, upright from L + 9. So a
  small walker can hurt the player on the ledge before L (on the ground
  in the airborne pose, but not in a closing picture), and after landing
  only from L + 7 (positions at the end of L + 6) on.
- **Falls without a jump**: see "Falls without a jump" below.

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
4. **U**, from the same two positions, is at most **24** (small walker),
   **37** (medium walker) or **50** (large walker).

There is no lower limit in the cases measured: on the walker's own floor
the landing rule (`landing.md`) comes first, so a player who would end B
less than 5 px above the floor lands instead of bouncing.

Bounce ranges for I (both ends included):

| Walker | Player facing | Walker heading left | Walker heading right |
|---|---|---|---|
| small | right | −21 to 34 | −20 to 35 |
| small | left | −35 to 20 | −34 to 21 |
| medium | right | −28 to 32 | −18 to 42 |
| medium | left | −42 to 18 | −32 to 28 |
| large | right | −24 to 37 | −23 to 38 |
| large | left | −38 to 23 | −37 to 24 |

The bounce ranges are wider than the hurt ranges on both sides, and a
player who is falling or rising slowly is checked for a bounce on every
frame, so a jump that comes down onto a walker bounces before it can be
hurt.

The bounce doesn't depend on the pose: the ranges and limits above were
measured with the player in the airborne pose (jumps, falls from a ledge
with a face) and in the upright pose (falls from a ledge without a face),
for all three kinds of walker.

## Falls without a jump

A player who walks off a ledge (`walk-off.md`) falls without a jump. The
rules above apply with the pose the player has:

- **Off a ledge with a face**: the player takes the airborne pose shortly
  before the leave frame of `walk-off.md` (`ball-contact.md`; on the
  ledge measured here 4 frames before it, still on the ledge) and keeps
  it through the fall and for some frames after landing ("Poses" above:
  the closing pictures come after landing). Every one of these frames
  follows the airborne rules, the ones on the ledge before the leave
  frame too (airborne, on the ground).
- **Off a ledge without a face** (`walk-off.md`, "Ledges without a
  face"): the player keeps the upright pose (the walking pose goes on)
  through the whole fall and the landing. In the air the upright reach
  applies: 11, 24 or 37.

Either way the player is falling from the leave frame on, so the bounce
check comes first: on any frame in the air on which the hurt conditions
hold, the bounce conditions hold too, and the player bounces on that
frame instead of being hurt on the next. In a fall without a jump the
player is hurt by a walker only from the ground: on the landing frame
and after it, with the ranges of the pose then.

## Walkers that aren't at full speed

The rules above hold for a walker at any speed: one speeding up from rest
after it appears, one easing through a turn at the end of its patrol, and
one stopped at a barrier and speeding up the other way (`walker.md`). The
ranges and limits are the same; only the walker's position and facing
at the moments given above count, never its speed or the way it moves.

So during a turn the column of the tables changes on the frame on which
the walker's facing changes (T + 4 at the end of a patrol, S + 5 at a
barrier, `walker.md`), not when its `vx` changes sign:

- at the end of a patrol the walker still moves the old way for a few
  frames after T + 4 while it already faces the new way, and the new
  column is used from then;
- at a barrier the walker stands still on S and moves away from the
  barrier from S + 1, but faces the barrier up to S + 4, and the old
  column is used until then.

For the small and large walkers the two columns differ by 1 px; for the
medium walker by 10 px.

For example, a medium walker heading left stops at a barrier on S and
from S + 1 speeds up to the right, still facing left until S + 5. A
player standing to its right and facing right is hurt on S + 3 if, at
the end of S + 2, I is between −26 and 22 (the column for a walker
heading left; measured up to its upper end there, as the barrier is at
the floor's end): at I 23 to 32 the player isn't hurt, although those
offsets hurt next to a medium walker moving right at full speed. A
player falling onto it on S + 3, facing right, bounces with I up to 32
(against the walker at the end of S + 2), not up to 42.

## Examples

Synthetic. Positions are at the end of each frame.

A player facing right walks at 1.25 px per frame into a small walker
heading left:

| Frame | Player `x` | Walker `x` | I | Notes |
|---|---|---|---|---|
| 1 | 80.5 | 101.75 | 80 − 101 = −21 | outside −19 to 24 |
| 2 | 81.75 | 100.75 | 81 − 100 = −19 | inside: hurt on the next frame |
| 3 | 81.75 | 99.75 | | H: hurt, the player doesn't move |

The same walker meets a player facing right who has been crouching since
frame 1, standing still at `x` 100.5:

| Frame | Walker `x` | I | Notes |
|---|---|---|---|
| 40 | 123.25 | 100 − 123 = −23 | outside −22 to 15 |
| 41 | 122.25 | 100 − 122 = −22 | inside: hurt on the next frame |
| 42 | 121.25 | | H: hurt |

Upright, the same player would have been hurt only on frame 45 (I = −19
at the end of frame 44). If instead Down had been released on frame 32
(U = 32), frame 40 is U + 8 and frame 41 is U + 9: the player is upright
at the end of frame 41 (I = −22, outside −19 to 24), and is hurt on
frame 45.

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
| T + 5 | 30 | hurt (upright reach: at most 37) |
| T + 6 | 30 | hurt only if U ≤ 16 (airborne reach): not hurt |

A player facing right stands still at `x` 120.25 on the floor of a
medium walker heading right that comes up from behind:

| Frame | Player pose | Walker `x` | I | Notes |
|---|---|---|---|---|
| 1 | standing | 86.5 | 120 − 86 = 34 | outside −16 to 32 |
| 2 | standing | 87.5 | 120 − 87 = 33 | outside |
| 3 | standing | 88.5 | 120 − 88 = 32 | inside: hurt on the next frame |

If the player is in the fidget pose instead, the range is −6 to 35 and
frame 1 (I = 34) is already inside: the player is hurt on frame 2.

The same player, standing at `x` 120.25 just after landing from a jump
on frame L, in the airborne pose up to L + 3 and upright from L + 4,
with the medium walker heading right coming up from behind:

| Frame | Player pose | Walker `x` | I | Notes |
|---|---|---|---|---|
| L + 2 | airborne (landing) | 89.5 | 120 − 89 = 31 | outside −16 to 29 |
| L + 3 | airborne (landing) | 90.5 | 120 − 90 = 30 | outside −16 to 29 |
| L + 4 | upright | 91.5 | 120 − 91 = 29 | inside −16 to 32: hurt on L + 5 |

A small walker at I = −10 on the landing frame L of a jump, with the
player still showing the last closing picture on L, doesn't hurt the
player on L + 1; with the player showing a landing picture on L + 1 and
I still within the range then (−19 to 21 facing right, walker heading
left), the player is hurt on L + 2.

A player facing right walks off a ledge without a face, 48 px above the
floor of a large walker heading left, and falls straight onto it in the
upright pose. At the end of the leave frame U is 44 (outside the upright
hurt reach of 37, inside the bounce limit of 50) and I is 2: the player
bounces on the leave frame. Had the walker come within reach only lower
down, at U 37 or less, the bounce conditions would still have held on
that frame, and the player would have bounced there.

## Evidence for the medium walker and the fidget pose

A medium walker put in the place of the first small walker of the first
level, on flat floor, with the first character alone:

- placements of the player at chosen offsets, one pixel apart, in poses
  reached by inputs (standing facing either way, rising and falling in
  a jump, with both walker headings; heights up to 90 px above the
  walker; rising speeds 1/256 apart at the bounce limit), and the same
  for the fidget pose with small and medium walkers: about 8 000
  placements, all as this file says; a small walker in the same place
  gave the small walker's ranges above;
- by inputs alone (standing, walking, running, jumps of every length
  toward, over and away from the walker, turning to face it, waiting
  for it to come back from behind, standing until the fidget pose):
  27 645 frames near a medium walker with 507 hurts and 328 bounces,
  and 14 009 frames near a small walker with 209 hurts and 109 bounces;
  128 of the hurts were in the fidget pose. 0 mismatches.
- the large walker against a standing player in the fidget pose: a
  large walker put in the same place, the player standing out of its
  reach until the pose began, the walker turned around at chosen moments
  so that it stayed on flat floor at full speed (the last turn at least
  8 frames before the placement), then the player placed at
  offsets one pixel apart, for both facings and both headings: 2 548
  placements, on 24 frames spread over the pose and, as a control, 4
  frames before it (these gave the large walker's ranges above). 0
  mismatches.

## Evidence for the airborne pose and falls without a jump

With the first character alone, on flat floor, in modified copies of the
game: the first small walker of the first level, or a medium or large
walker put in its place, and a flat ledge added beside it: 48 or 112 px
above the walker's floor without a face, or 48 px with a steep face at
its end:

- walk-offs from the ledges, facing right and (without a face) left, with
  the player placed at offsets one pixel apart from the walker on every
  frame from just before the leave frame to the landing: with the fall's
  own speed, no hurt in the air and the bounce ranges and limits above;
  with an upward speed of 2 px per frame given at the placement (so that
  no bounce can happen), the upright reach and ranges after a ledge
  without a face and the airborne ones after a ledge with a face, from
  4 frames before the leave frame. About 1 620 000 placements.
- standing jumps, the walker made to walk to and fro on the flat at full
  speed: placements in the air on frames T + 6 to T + 38 with the jump's
  own upward speed and with upward speeds from 0.25 to 3 px per frame,
  and on the ground on every frame from L − 1 to L + 8 after a landing,
  for all three kinds, both facings and both headings: about 277 000
  placements. Of all the placements, the 459 830 in which no bounce
  could happen (the player on the walker's floor after the placement
  frame, or given an upward speed of 2 px per frame or more) were
  checked one by one against the rules of this file: 0 mismatches.
- by inputs alone: walk-offs from the ledges at every start delay up to
  90 frames, both directions, onto all three kinds (88 528 frames, 154
  bounces, 146 hurts after landing), and jumps of 11 lengths at every
  start from frame 20 to 75 next to an approaching walker of each kind
  (73 543 frames, 1 137 bounces, 699 hurts, 119 of them in the airborne
  pose on the ground). Earlier surveys of this file, checked again with
  the pose rule: 60 160 frames. 0 mismatches.

On flat floor the bounce comes first in almost every case where the
airborne and upright rules differ, so in the runs by inputs alone the
reach by time since take-off with the standing ranges would have given
the same results as the pose rule. The differences showed in the
placements; with the player and the walker on different floors they
weren't measured by inputs alone.

## Evidence for crouching

On the opening floor of the first level, with the first character alone,
in the same modified copies as "Evidence for walkers that aren't at full
speed" (a large walker with a patrol, small walkers with and without a
patrol, a medium walker), at frames where the walker walks at full speed
heading left and, after its turn, heading right. On each frame the player
was placed on the walker's floor at offsets one pixel apart (−45 to 45),
facing right and left, with H − 1 the frame of the positions, after: no
Down; Down held from H − 1 on, or from 2 to 91 frames before it; Down
held for 30 frames with U 1 to 13 frames before H − 1; one-frame presses
of Down 2 to 23 frames before H − 1; walking with Down held from 3, 7 or
14 frames before H − 1; and Down held on H alone.
2 342 frames of play, 213 122 placements: 172 991 in the crouching pose
(77 493 hurts) and 40 131 upright (20 619 hurts). The rules of this file
with the crouching ranges from D to U + 8 and the upright ranges
otherwise: 0 mismatches. The crouching ranges were the same on every
frame of the crouch, from D to U + 8, however long Down was held.

As a holdout, by inputs alone: 6 000 random runs near the four walkers,
walking toward them, away or neither, with Down held in 40% of the
stretches of play (all through, or for 1 to 3 frames) and Jump in a
sixth (128 874 frames with a walker close; 35 330 of them crouching,
including 774 hurts). 0 mismatches while crouching. The only 4
mismatches were landing frames with a small walker, the case listed
under "Not covered" (which picture shows on the landing frame).

## Evidence for carrying

On the opening floor of the first level, with the first character alone,
carrying an object (in modified copies that put an object at the start
of the floor, and replace the first walker as in "Evidence for walkers
that aren't at full speed": a large walker, small walkers with and
without a patrol, a medium walker). At three frames with the walker
walking at full speed, heading left, and with its heading set to the
right 30 frames earlier, the player was placed at offsets one pixel apart
(−45 to 45), facing right and left, with Action held all the time:
standing, walking toward the facing side, turning (the new direction
first held 0 to 10 frames before H − 1), early and late in a jump (Jump
held from 1 to 20 frames before H − 1; 5 px above the walker's floor),
landing after a short jump (Jump pressed 10 to 40 frames before H − 1,
on the walker's floor), and falling onto the walker (bounces, 15 px
above). In the air, at one frame per heading, heights 6 to 45 px above
the walker's floor were also tried. 1 408 frames of play, 184 912
placements: 54 768 hurts on the walker's floor and early in jumps,
8 768 hurts at the heights tried in the air, 2 820 bounces. The rules of
this file with the carrying ranges and the upright vertical reach for
hurts, and the bounce ranges unchanged: 0 mismatches. The upright ranges
instead: 8 064 mismatches.

As a holdout, by inputs alone: 6 000 random runs carrying near the four
walkers (walking toward them, away or neither, Jump in a sixth of the
stretches of play), 88 858 frames carrying with a walker close,
including 1 260 hurts and 1 566 bounces. 2 mismatches, both bounces that
happened with the player in the air pressed against the wall at the left
end of the floor (listed under "Not covered").

## Evidence for pick-ups, throws and put-downs

On the opening floor of the first level, with the first character alone,
in the modified copies of "Evidence for carrying" (an object at the
start of the floor; the first walker replaced by a large walker, small
walkers with and without a patrol, a medium walker), with the walker
heading left, and heading right (its heading set to the right 30 frames
earlier). The player walked back toward the start of the floor while
carrying, facing left or turned to face right, and then:

- put the object down and picked it up again, so that one frame of the
  pick-up, G to G + 14, was the frame before H (for H = G + 1, the
  object was moved along with the player when placing it);
- threw it on the ground or put it down, so that one frame from Q to
  Q + 43 (throw) or Q to Q + 14 (put-down) was the frame before H;
- threw it in the air (Jump pressed 5 frames before Q), placed 6 to 45 px
  above the walker's floor on 12 frames from Q to Q + 42, for hurts;
- threw it in the air, placed 15 px above the walker's floor and falling,
  so that B was one of the frames from Q to Q + 43, for bounces.

At each of these frames the player was placed at offsets one pixel apart
(−45 to 45) from the walker, facing right and left: 2 600 frames of play,
1 088 360 placements. The 35 217 placements where the thrown object had
already knocked the walker away were left out; the others had 175 539
hurts and 23 760 bounces on the frames of a pick-up, throw or put-down.
With the carrying ranges on every frame of a pick-up, throw or put-down,
the upright ranges from Q + 43 and Q + 13, the bounce ranges unchanged
and no bounce on Q + 1 to Q + 21: 0 mismatches. The upright ranges during the
pick-up, throw and put-down instead: 24 990 mismatches; bounces allowed
on Q + 1 to Q + 21: 24 654.

As a holdout, by inputs alone: 6 000 random runs near the four walkers,
starting while carrying, with Action held in only part of the stretches
of play (letting go is a throw, or a put-down with Down; pressing it
again next to the object is a pick-up) and Jump in a sixth of them.
Of the frames with a walker close (within 60 px across and 70 px up or
down), 2 322 were in a pick-up (every pick-up in these runs was on the
ground from start to end), 2 012 in a
put-down and 11 078 in a throw (2 923 of them in the air), with 139
hurts and 60 bounces on those frames. 0 mismatches.

## Evidence for walkers that aren't at full speed

On the opening floor of the first level, with the first character alone,
in modified copies of the game in which the walker that first appears
there is replaced: by a large walker that patrols and turns, by small
walkers with and without a patrol, and by a medium walker. The walkers
without a patrol are passed by a jump, walk to a barrier, stop and come
back. On every frame of the windows in which a walker speeds up from rest
after it appears, turns at the end of a patrol, or stops at a barrier and
speeds up the other way, the player was placed at offsets one pixel
apart (−45 to 45) from the walker, facing right and left: standing
(hurt), early in a jump (hurt) and falling (bounce). 2 200 frames of
play, 174 040 placements (60 416 hurts, 37 436 bounces). In 1 548 of
these frames the walker wasn't moving at full speed the way it faced: in
64 of them it was moving against its facing, and in 12 it wasn't moving.
The barrier is at the end of the floor, and placements past it were left
out (the player can't be there), so at the barrier only the ends of the
ranges on the side away from it were checked.

The rules of this file with the walker's facing at the end of H − 1 (hurt)
and B − 1 (bounce): 0 mismatches. The same tables with the walker's
direction of motion instead: 362 mismatches; with its facing one frame
later for bounces (end of B) or one frame earlier for hurts (end of
H − 2): 98.

As a holdout, by inputs alone: 6 000 random runs near the four walkers,
started in or just before those windows (128 144 frames with a walker
close; 15 148 of them with a walker not at full speed, including 193
hurts and 136 bounces). 0 mismatches, except one frame with a walker at
full speed: a landing frame where the player was in the case listed
under "Not covered" (which picture shows on the landing frame).

## In `title-2`

Everything in this section was measured on `title-2` only, with the lead
character alone (the other character hadn't joined yet), on the walkers of the
first level. They all have the same size, so `title-2` has one kind of
walker here. Every walker measured was heading left; walkers heading
right are not covered. `y` and `vy` are as in a trace (down is
positive). The jump frames G and T and the gravity are as in
`jump-input.md` and `jump-arc.md` ("In `title-2`"); crouches are as in
`ground-speed.md` ("Crouching (`title-2`)").

### Offsets (`title-2`)

Contact is decided from whole-pixel positions. Round each position down:
`x` toward the left and `y` toward the top (the whole-pixel part of
`y`, since `y` grows downward). Using the rounded positions, let

- **I** = the player's `x` minus the walker's `x`;
- **U** = the walker's `y` minus the player's `y`: how many pixels the
  player's position is above the walker's (0 on the same flat floor,
  positive when the player is higher, negative when the walker is
  higher).

For a frame **E**, both offsets use the player's position **at the end
of E**, after the player has moved on E as on any other frame, and the
walker's position **at the end of E − 1**, before its own move on E.

So, unlike `title-1`, nothing is decided a frame ahead: the first frame
on which the offsets are within the ranges below is the frame on which
the hurt starts or the bounce happens (frame B of `bounce.md`, "In
`title-2`"). What happens in a `title-2` hurt is not specified yet.

### Poses (`title-2`)

The ranges depend on the player's **pose on E**: the pose the player
has at the end of E as a result of moving on E (before a hurt or bounce
replaces it).

- **In the air**: from the frame G on which a jump starts (G and G + 1
  count, although the player is still on the ground) until landing; and
  then from the landing frame L to L + 17. If a direction and Action are
  held through the landing, so that the player runs on, the landing part
  is L to L + 8 instead.
- **Crouching**: from the frame a crouch starts to U + 10, where U is the
  frame the crouch ends (the frames on which no turn can start). From
  U + 11 the player is standing, walking or running again.
- **Running**: on the ground after a roll has carried on into running
  with Action held (`ground-speed.md`, `roll-speed.md`), including while
  slowing down after the inputs are released.
- **Walking**: on the ground and moving otherwise, including while
  slowing down after the direction is released.
- **Standing**: on the ground and not moving, and at the end of a
  slow-down (below).

When the player slows down, the switch from the walking or running
ranges to the standing ranges doesn't come on the frame `vx` reaches 0:
it was seen 18 to 34 frames after the direction was released, with `vx`
then between 7/256 and 34/256 (either sign). The switch from running to
walking when Action is released while a direction stays held was seen 8
to 21 frames after the release. The exact frame is not covered (see
"Not covered in `title-2`").

### Ranges (`title-2`)

A walker is in contact with the player on frame E when **I** is within
the horizontal range for the player's pose and facing, and **U** is
within the vertical range for the pose (all ends included):

| Pose | I, facing right | I, facing left | U |
|---|---|---|---|
| standing | −23 to 30 | −23 to 30 | −36 to 20 |
| crouching | −23 to 30 | −23 to 30 | −22 to 22 |
| walking | −25 to 28 | −21 to 32 | −36 to 20 |
| running | −28 to 29 | −22 to 35 | −32 to 17 |
| in the air | −26 to 29 | −22 to 33 | −28 to 21 |

The same ranges give hurts and bounces; which of the two happens is
decided below. On the ground, the vertical range matters when the walker
is on a different floor (a step above or a deck below the player).

### Hurt or bounce (`title-2`)

When a walker is in contact with the player on frame E:

1. If the player ends E on the ground (this includes the landing frame
   and the frames G and G + 1 of a jump), the player is **hurt** on E.
2. Otherwise the player **bounces** on E if both of these hold, and is
   **hurt** on E if not:
   - the player's `vy` on E after this frame's gravity, before the bounce
     replaces it, is at least **−1.25** (−320/256). With the gravity of
     `jump-arc.md` ("In `title-2`"): a player falling, or rising slowly,
     bounces; −1.25 bounces and −321/256 hurts, with either gravity;
   - **U** is at least **−10**.

### Examples (`title-2`)

Synthetic. Positions are at the end of each frame.

A player standing facing right, with a walker heading left at 1 px per
frame on the same floor:

| Frame | Player `x` | Walker `x` | I (walker as on the frame before) | Result |
|---|---|---|---|---|
| 1 | 76.5 | 100.875 | 76 − 101 = −25 | none |
| 2 | 76.5 | 99.875 | 76 − 100 = −24 | none |
| 3 | 76.5 | 98.875 | 76 − 99 = −23 | hurt starts on frame 3 |

A player facing right, falling with `vy` 2 at the end of frame 20 after
a jump released early (gravity 0.4375, so `vy` on frame 21 is 2.4375).
At the end of frame 20 the walker is at `x` 110.875, `y` 200; at the
end of frame 21 the player is at `x` 130.5, `y` 181.75. I = 130 − 110 =
20, U = 200 − 181 = 19: contact, the player is in the air, `vy` is at
least −1.25 and U at least −10, so the player bounces on frame 21.

A player rising with jump held (gravity 0.3515625) beside a walker,
within the ranges: with `vy` −1.6015625 (−410/256) at the end of E − 1,
`vy` on E is −1.25 and the player bounces; with −1.60546875 (−411/256)
it is −1.25390625 (−321/256) and the player is hurt.

A walker on a step 30 px above the player's floor, with I within range:
U = −30, so it hurts a standing or walking player (U at least −36) but
doesn't touch a crouching one (U at least −22).

### Evidence (`title-2`)

About 1 160 000 placements of the player or the walker at chosen
offsets, one pixel apart, in poses reached by inputs (standing,
crouching down, crouched and rising, walking and running both ways,
jumps held and released at every stage of the arc both ways, the
landing frames), plus fixed rising speeds 1/256 apart: 223 714 hurts,
68 568 bounces and 871 805 placements without contact, all as this
section says. By inputs alone, 39 062 frames near walkers up to each
run's first hurt or bounce (118 hurts, 271 bounces, from standing,
walking, running, jumping, falling and the landing frames, with several
walkers on screen): 0 mismatches.

### Not covered in `title-2`

- Walkers heading right, walkers that aren't walking at full speed
  (frozen, stopped or speeding up) and walkers already bounced on.
- The exact frame of the switch from the walking or running ranges to
  the standing ranges, and from running to walking (see "Poses").
- Contact while turning on the ground, carrying an object or bouncing,
  and with two walkers on the same frame. Rolling, and the stop after a
  roll, are in `rolling-into-walkers.md` ("In `title-2`").
- Falls that don't start with a jump: they used the "in the air" ranges
  in the 12 contacts seen (11 bounces, 1 hurt), but weren't measured by
  placement.
- The second character and other kinds of enemies. What happens in a
  hurt with one character is in `knock-out.md` ("In `title-2`").

## Not covered

This list is for `title-1`; `title-2` has its own list above.

- Walkers that are frozen, and the vertical reach of walkers that aren't at full speed (measured
  only with the player on the walker's floor or just above it). Walkers
  already bounced on don't touch the player (`walker.md`, "Being bounced
  on").
- Contact after a bounce, or being knocked back; a crouching player on
  a slope or with the walker on another floor. Carrying: a pick-up in
  the air (only pick-ups on the ground were measured), with the walker on
  another floor or on a slope, and bounces with the player pressed against
  a wall.
  Rolls into walkers are in `rolling-into-walkers.md`.
- Rolling off a ledge onto a walker, and runs off a ledge (a run starts
  with a roll). Walk-offs were measured from ledges without a face 48
  and 112 px high and from one with a face 48 px high.
- Which jumps still show a closing picture on the landing frame and
  which show the landing pictures from it ("Poses"): only the 24 jumps
  listed there were measured. How long each closing and landing picture
  shows in general (measured for those jumps and one 13-frame fall from
  a ledge with a face).
- The reach of the closing and landing pictures on the ground above
  U 0, and of the landing pictures in the air (they were only seen on
  the ground, with the player and the walker on the same floor).
- Walkers above the player (U below −6 on the ground, below −4 in the
  air), and the lower end of the vertical reach in the air.
- The other character, and hits while two characters are present.
- Other kinds of enemies. The medium walker on slopes (its contact was
  measured on flat floor only).
