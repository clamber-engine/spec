# Touching the thrower: hurt or bounce

Titles: `title-1`

When contact between the player and a thrower (`thrower.md`) hurts the
player (`knock-out.md`, `losing-a-character.md`) and when it gives a
bounce (`bounce.md`), and what a bounce does to the thrower. Balls
touching the player are in `ball-contact.md`.

Frames are logic frames at 60 Hz, numbered as in a trace. `x` grows to
the right. Measured on the first level's thrower with the first
character alone, every ball removed as soon as it appeared:

- by placing the player at chosen offsets from the thrower, one pixel
  apart, in poses reached by inputs (standing, walking and running on
  the thrower's ledge, landing on it, and jumping, rising and falling
  beside, under and over the thrower), facing right and left: about
  370 000 placements, each followed for one or two frames, and 688
  landings on the ledge followed frame by frame. Most were made with the
  thrower moved 100 px up into open air, away from its ledge; the ranges
  were the same as at its own place wherever the ledge didn't get in the
  way;
- by inputs alone, walking, running, rolling, crouching and jumping at
  random near the thrower, the 400 runs of `ball-contact.md` with every
  ball removed: 188 942 frames, 95 000 of them with the thrower there
  and not knocked out, 247 hurts and 38 bounces by the thrower. Every
  hurt came on the frame the rules below give and none on any other
  frame; every bounce did too, leaving out 29 frames on which the
  player's speed was too close to the limit for the log to decide (22
  bounced).

## Offsets

As in `walker-contact.md`: round each position down, `x` toward the left
and the vertical position toward the floor (down), and let

- **I** = the player's `x` minus the thrower's `x`;
- **U** = how many pixels the player's position is above the thrower's
  (positive when the player is higher).

The thrower's position is 1 px above the floor of its ledge, so a player
standing on the ledge has U = −1. The player's **facing** is as in a
trace. The thrower never moves until it is bounced on (below), and
these ranges don't change while it flaps or throws.

## Being hurt

The player is hurt on frame **H** when, with the positions **at the end
of H − 1**, all of these hold:

1. **I** is within the hurt range for the player's **pose** at the end
   of H − 1 (the poses of `ball-contact.md`, "Which pose the player
   has") and facing (table below);
2. the player is within the vertical reach for that pose:
   - **in the air, upright** (a jump up to five frames after its
     take-off frame T, `ball-contact.md`): U from **−33** to **14**;
   - **in the air, airborne**: U from **−50** to **−7**;
   - **on the thrower's ledge** (U = −1, standing, walking or running,
     or in the airborne pose after landing there): only I is checked,
     with the range of the pose; but not from the positions at the end
     of the frame on which the player lands (L): a player who lands
     within the I range isn't hurt on L + 1, and is hurt on L + 2 if
     still within it at the end of L + 1;
3. H isn't the frame before one of the thrower's throws: for a throw on
   frame T (`thrower.md`, "When it throws"), the player is never hurt by
   the thrower on T − 1. A player still within reach at the end of T − 1
   is hurt on T.

As with walkers, a hit is decided one frame ahead, and if these
conditions hold at the end of a frame the player is hurt on the next
frame even if that frame would otherwise give a bounce.

Hurt ranges for I (both ends included):

| Pose | Player facing right | Player facing left |
|---|---|---|
| upright | −26 to 33 | −32 to 27 |
| airborne | −26 to 30 | −29 to 27 |

As with walkers, in the airborne pose the end of the range on the side
behind the player is 3 px further in. Facing left, each range is the
facing-right range turned around: from −(high end) + 1 to −(low end) + 1.

The ranges were the same on every frame of each pose measured, whatever
the player's speed (up speeds from 1.6 to 7 px per frame, horizontal
speeds from −2 to 2), and on every frame of the thrower's flapping. The
upright ranges were the same on every take-off frame (T to T + 5), for
the standing pictures (placed in the air) and standing, walking and
running on the ledge (running measured facing right).

In play the upright reach in the air applies only to the first frames
of a jump: walking off a ledge in the upright pose (`walk-off.md`,
"Ledges without a face") doesn't happen near the thrower in the first
level.

## Bouncing

On frame **B** the player bounces (`bounce.md`) when, after the player
has moved on B, all of these hold:

1. the player is in the air: B isn't a landing frame (`landing.md`);
2. the player isn't rising fast: the `vy` the player has on B after this
   frame's gravity is **greater than −1.25**, as for walkers;
3. **I**, from the player's position at the end of B, is from **−28 to
   43** facing right, or from **−42 to 29** facing left;
4. **U**, from the same position, is from **−20 to 27**.

The bounce doesn't depend on the pose: the same ranges were measured
in the upright and the airborne pose, and on the frames before a throw.
Unlike a walker's, the thrower's bounce range has a lower end: a player
up to 20 px below it bounces. Its ledge can be passed from below
(`platforms-from-below.md`), and a player moving slowly up or down
inside the ledge, under the thrower, bounces off it.

On the ledge the landing rule (`landing.md`) comes first: a player who
would end B less than 5 px above the ledge's floor (U below 4) lands
instead of bouncing. So a player coming down onto the ledge within the
bounce range from 4 px or more above it bounces off the thrower before
landing.

A player who bounces on B isn't hurt by the thrower on B + 1, even if
the hurt conditions hold at the end of B.

## What a bounce does to the thrower

A bounce knocks the thrower out, as the Down + Action ground action does
(`ground-action-enemies.md`, "The thrower"), with B in place of K: it
doesn't move on B, B + 1 and B + 2, then from B + 3 its `vy` (−5 on B)
increases by 7/16 per frame, up to 8, and it moves by it; `vx` is 0. It
falls through its ledge and is later removed (when isn't specified). It
throws no more balls (measured for 200 frames after the bounce, past
three of the frames its timetable would have thrown on).
In the random runs it never hurt the player or gave a bounce after it
was knocked out.

## Examples

Synthetic. The thrower is at `x` 1000, its position 300 px above a
reference floor; positions are at the end of each frame.

A player facing right jumps up beside the thrower, I = 10 on every
frame, take-off on frame T:

| End of frame | Pose | U | Next frame |
|---|---|---|---|
| T + 5 | upright | −36 | not hurt (below −33) |
| T + 6 | airborne | −31 | hurt (airborne reach −50 to −7) |

The same jump with I = 32: on T + 5 (upright, U −36) not within reach;
on T + 6 I = 32 is above the airborne high end (30): not hurt. If the
player keeps rising past the thrower and starts falling with I = 32 and
U = 20, the player bounces on that frame (I within −28 to 43, U within
−20 to 27).

A player facing right lands on the thrower's ledge at I = −29 on frame
L, outside the bounce range, then walks right in the airborne pose:

| End of frame | I | Next frame |
|---|---|---|
| L | −29 | |
| L + 1 | −27 | outside −26 to 30: not hurt |
| L + 2 | −26 | inside: hurt on L + 3 |

A player who lands within the I range (measured by placing the player
there on the landing frame) isn't hurt on L + 1, and is hurt on L + 2 if
still within the range at the end of L + 1.

The thrower throws on frame 500. A player facing right rises past it in
the airborne pose with I = 0 and U = −45 at the end of 498 and U = −40
at the end of 499: no hurt on 499 (the frame before the throw); hurt on
500.

## Evidence

Placements, one pixel apart, with the hurt or bounce frame compared with
the offsets at the end of the frame before it (hurt) or of the same
frame (bounce):

- in open air, facing right and left: the upright pose on the take-off
  frames and the airborne pose 19 frames into a jump, about 7 000 to
  7 800 offsets each; every frame from T + 4 to T + 44 (upright on the
  first two, airborne on the 39 others, covering every picture of the
  rise and the fall and the thrower's flapping), and the airborne pose
  at other up and horizontal speeds; every set filled the ranges above
  exactly, with no gaps;
- in open air, around three throws (left, straight up, right; a ball at
  its start on the throw frame for two of them, one frame on for the
  third): five frames each, hurts and bounces. On the frame before each
  throw no offset hurt; on the other frames, and for bounces on all of
  them, the ranges were those above;
- the up-speed limit: up speeds from 1.4375 to 1.5625 px per frame
  before gravity (1.15625 to 1.28125 after it) at I 0, U 0;
- both ranges at once: 1 617 offsets rising 1.6 px per frame, slow
  enough to bounce on the next frame: all 969 within the hurt range
  were hurt, the others within the bounce range bounced;
- at the thrower's own place: standing, walking and running on its
  ledge, and the airborne pose under and over it: the same ranges as in
  open air, except that bounces over the ledge start at U 4, as
  described;
- 688 landings on the ledge after jumps with Jump held 1, 8, 20 and 40
  frames, facing right and left, the player then put at every I from
  −45 to 45: none was hurt on L + 1; on L + 2 every one within the
  airborne range was hurt (57 of 86 for each jump and facing).

Random runs: the counts are above. Three frames in the hurt range were
frames before a throw; none was hurt.

## Not covered

- Rolling, crouching, the fidget pose and the Down + Action ground
  action on the thrower's ledge (the ground action is in
  `ground-action-enemies.md`). Rolling along the ledge through the
  thrower's place was seen not to hurt.
- The upright pose in the air other than in the first frames of a jump,
  and the closing and landing pictures of the airborne pose
  (`walker-contact.md`, "Poses") in the air near the thrower.
- Whether a knocked-out thrower can hurt the player or give a bounce
  while it falls, and when it is removed.
- The other character, and contact while carrying, being hit or during
  a bounce's frozen frames.
- Throwers facing right and throwers in other levels.
