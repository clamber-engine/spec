# Touching the thrower: hurt or bounce

Titles: `title-1`

When contact between the player and a thrower (`thrower.md`) hurts the
player (`knock-out.md`, `losing-a-character.md`) and when it gives a
bounce (`bounce.md`), what a bounce does to the thrower, what
happens when a rolling player meets it, and what a knocked-out thrower
does. Balls touching the player are in
`ball-contact.md`.

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
standing, walking and running on the ledge (running measured facing right).

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
falls through its ledge and every floor below it, and is removed once
it leaves the picture ("The knocked-out thrower" below). It throws no
more balls (measured for 200 frames after the bounce, past three of the
frames its timetable would have thrown on).

## Rolling into the thrower

A rolling player defeats the thrower the way a roll defeats a walker
(`rolling-into-walkers.md`), with the thrower's own range. This section
uses that spec's names: **S** is a roll's `roll_start` frame, **D** =
S + 29, **K** the frame on which the thrower is defeated, and the
**attack frames** are the same: every frame of a roll up to the last
frame before `roll_end` (D, D + 1 and D + 2 included when the roll
carries on into running), and D to D + 7 when it ends in a stop. A roll
that has been reversed (`roll-speed.md`) counts like any other.

### When the thrower is defeated

On an attack frame K the thrower is defeated when I, from the player's
position **at the end of K**, is within (both ends included):

| Player facing (at the end of K) | I |
|---|---|
| right | −24 to 33 |
| left | −32 to 25 |

The range facing left is the range facing right turned around, as in
"Being hurt". It is the same on every attack frame, for slow, normal and
fast rolls, with Action held or released, after a reversal, and on the
frame before a throw. The player was on the thrower's ledge (U = −1); a
player rolling on the floor under the ledge, about 80 px below it,
neither defeats the thrower nor is hurt by it.

On S itself the thrower is also defeated when I from the player's
position at the end of S − 1 is within the range. This shows only when
the player moves a pixel or more on S, as in a fast roll pressed while
running: facing right and moving right 2.33 px on S, the thrower was
defeated for I at the end of S − 1 from −26 to 33 (I at the end of S
from −24 to 35).

### Being hurt around a roll

As for walkers (`rolling-into-walkers.md`, "Being hurt around a roll"),
with the thrower's hurt rule ("Being hurt") in place of the walker's:

- if the hurt conditions hold at the end of S − 1, the player is hurt on
  S even though Action is pressed on S, and no roll starts;
- the player is never hurt by the thrower on an attack frame;
- after a roll that carries on into running, the earliest hurt is on the
  frame after `roll_end`;
- after a roll that ends in a stop, on D + 8 to D + 11 the thrower
  neither is defeated nor hurts the player; the earliest hurt is on
  D + 12, with the upright range. The frame before a throw still
  doesn't hurt.

### After the defeat

The player's pause on K + 1 and K + 2, how the roll goes on, the boost
and the speeds are exactly as after defeating a walker
(`rolling-into-walkers.md`, "The pause", "How the roll goes on" and
"Speed on roll frames").

The thrower is knocked out as by a bounce ("What a bounce does to the
thrower", with K in place of B): it doesn't move on K, K + 1 and K + 2,
then from K + 3 its `vy` (−5 on K) increases by 7/16 per frame, up to 8,
and it moves by it, with `vx` 0. It throws no more balls (measured for
300 frames after K, past six of the frames its timetable would have
thrown on).
What it does after that is in "The knocked-out thrower".

### Example

Synthetic. The thrower is at `x` 1000 on its ledge. The player rolls
right facing right at `vx` 3 (a normal roll at full speed); positions
are at the end of each frame.

| Frame | `x` | I | Notes |
|---|---|---|---|
| K − 1 | 974.5 | −26 | outside −24 to 33 |
| K | 977.5 | −23 | the thrower is defeated |
| K + 1 | 977.5 | −23 | pause |
| K + 2 | 977.5 | −23 | pause |
| K + 3 | 980.5 | −20 | the roll goes on (Action not held: `vx` stays 3) |

A fast roll pressed with the player facing right at `x` 1033.5 at the
end of S − 1 (I = 33) and moving 2.33 px on S defeats the thrower on S,
though I at the end of S is 35.

## Crouching and the fidget pose

On the thrower's ledge (U = −1, so only I is checked) a crouching player
and, for the first character, a player in the fidget pose have ranges of
their own. The hit is decided as in "Being hurt": the pose and I at the
end of H − 1.

**Crouching** (Down held on the ground, standing or walking, facing
right and left; `ball-contact.md`, "Which pose the player has"). Hurt
ranges for I:

| Character | Facing right | Facing left |
|---|---|---|
| first | −29 to 24 | −23 to 30 |
| second | −30 to 26 | −25 to 31 |

Facing left, each range is the facing-right range turned around, as in
"Being hurt". With D the first frame on which Down is held on the
ground, the range applies from the end of D, unchanged through the
crouch whatever the player's speed (a player walking when Down is
pressed has it from the end of D too). It lasts after Down is released:
with U the first frame without Down, up to the end of U + 7 for the
first character and up to the end of U + 10 for the second; from the end
of U + 8 (first) or U + 11 (second) the standing range applies (the
upright range of the first character, "Being hurt"; the standing range
of the second, "The second character under control"). Against the upright range (−26 to 33 facing right) the first
character's crouching range reaches 3 px further back and 9 px less far
forward; the second character's is wider than standing (−30 to 26
against −23 to 23).

**Fidget pose.** The first character's range shifts as for walkers
(`walker-contact.md`, "Fidget pose"): facing right the low end is 10 px
higher and the high end 3 px higher (−16 to 36); facing left the low
end is 3 px lower and the high end 10 px lower (−35 to 17). The pose is
on the frames `ball-contact.md` gives for it (from the 240th frame of
standing for 180 frames, and again every 457 frames). The second character's range in
the fidget pose is its standing range (−23 to 23 facing right, −22 to
24 facing left), on both periods.

Neither pose was measured for bounces: a crouching or fidgeting player
is on the ledge, where nothing above the thrower can be bounced on.

## The knocked-out thrower

This holds however the thrower was knocked out on K: by a bounce (with
B as K), a roll, or the Down + Action ground action
(`ground-action-enemies.md`, "The thrower"). It moves as described
there, falling through its ledge and every floor below it. When an
object knocked it out (`thrown-objects.md`, "Hitting the thrower"), it
starts falling one frame sooner, and its removal below is the same; so
is the first point below, for K + 2 to K + 44 only. The third point
wasn't measured for that case.

- **It never hurts the player and never gives a bounce**, from K + 4
  on until it is removed, wherever the player is, in each of the poses
  measured (standing, upright in the air and airborne). After a hit by
  an object the same held on each frame from K + 2 to K + 44, in the
  standing pose only (later frames, the jump poses, K and K + 1 weren't
  measured). A setup method was used; it was seen in arranged
  situations only, not in ordinary play. The player was at every second
  px from 60 px left to 60 px right of the thrower and from 70 px below
  to 50 px above it (61 × 61 = 3 721 positions on each frame, 160 003
  over the 43 frames), once moving down at 2 px a frame and once moving
  up at 1 px a frame (the two motions are separate runs, each with its
  own 160 003 positions): no hurt and no bounce in either. A thrower
  that hadn't been knocked out, with the player at the same positions
  on the same frames, moving down, gave 31 060 bounces and 12 388 hurts
  among the 160 003 positions.
- **It is removed** on the first frame from K + 3 on on which, after its
  move, it is outside the picture by the same edges as a ball
  (`thrower.md`, "Leaving the picture"), with its own `x` and `y` in
  place of the ball's. With `V` and `W` as they were at the start of the
  frame, `X` its `x` rounded down and `Y` its new `y` rounded up, it is
  removed when `X` − `V` is less than −31 or more than 288, or `Y` − `W`
  is more than 256. The margins of a thrower that hasn't been knocked
  out (removed only at `xt` − `V` ≤ −128 or ≥ 385, `thrower.md`, "The
  thrower") no longer apply. Measured: kept at `X` − `V` = −31 and 288
  and removed at −32 and 289; kept at `Y` − `W` = 256 and removed at
  257.
- **It never comes back.** With the view moved away past either side,
  far enough for a thrower that hasn't been knocked out to be removed,
  and back until `xt` was in the window again, nothing appeared at its
  place. A thrower that hadn't been knocked out appeared again, by the
  rule in `thrower.md`. It is there again, though, after the player is
  knocked out (`restart-barrel.md`, "What is there again after a
  knock-out") or leaves the level and comes back (`exits.md`, "Coming
  back to the level").

For example, with the view still and `yt` − `W` = 100, `Y` − `W` is 250
at the end of K + 44 and 258 on K + 45, the frame on which it is
removed. With the view moving sideways, it can be removed sooner, when
`xt` leaves the window.

## The second character under control

Observed on the same thrower with the second character under control
after the swap of `second-character.md` and the first character
following, with no ball on screen:

- with the player at chosen offsets from the thrower as above (a setup
  method was used to arrange them): standing, walking and running on
  the ledge, jumping and falling beside, under and over the thrower,
  facing right and left, the thrower in open air and at its own place,
  on every frame of its flapping and around its throws. About 748 000
  such situations, each followed for one or two frames, and 688
  landings on the ledge followed frame by frame;
- in random play with inputs alone, walking, running, rolling,
  crouching and jumping near the thrower: 700 runs, 69 349 frames,
  37 892 of them in the poses below with the thrower there. 30 hurts
  and 11 bounces, every one on the frame the rules give and none on any
  other (one of the bounces on a frame on which the player's speed was
  too close to the limit for the log to decide). The frames in other
  poses (rolling, crouching, the fidget pose) had no hurt, and no
  bounce was checked on them.

Everything under "Offsets" and "Being hurt" holds except the ranges: a
hit is decided at the end of H − 1, never on the frame before a throw
(checked at two throws), not from the positions at the end of a landing
frame on the ledge (a landing is hurt on L + 2 if still within the
range), and on the ledge only I is checked. The player is hurt in the
way of `losing-a-character.md` when the first character is there to
take over. Everything under "Bouncing" holds too, with the same rule on
the speed (the `vy` after this frame's gravity greater than −1.25: a
rise of 1.5625 px per frame at the end of the frame before bounced, one
of 1.625 did not), the same landing rule on the ledge and the same
knock-out of the thrower. Different are the ranges, which are narrower
than the first character's, and the poses:

| Pose | Player facing right, I | Player facing left, I | U in the air |
|---|---|---|---|
| standing | −23 to 23 | −22 to 24 | −31 to 16 |
| moving | −29 to 24 | −23 to 30 | −31 to 12 |

- **Standing** is standing still on the ground.
- **Moving** is everything else that was measured: walking and running
  on the ground and on the ledge, the take-off, the rise and the fall
  of a jump, and the landing frames on the ledge. There is no separate upright
  range in the first frames of a jump: every frame from take-off on
  has the moving range, whatever the player's speed.

Facing left, each range is the facing-right range turned around, as in
"Being hurt". On the ledge (U = −1) only I is checked.

Bounce ranges for the second character:

| Player facing | I | U |
|---|---|---|
| right | −38 to 29 | −13 to 34 |
| left | −28 to 39 | −13 to 34 |

U has a lower end of −13 (the first character's is −20). On the ledge a
player who would end B less than 5 px above its floor lands instead, as
for the first character (U below 4).

### Rolling into the thrower

The second character's rolls defeat the thrower by the rules of "Rolling
into the thrower", with this character's own S and D
(`rolling-into-walkers.md`, "The second character": its rolls last 24
frames, D = S + 24) and these differences:

| | First character | Second character |
|---|---|---|
| Defeat range, facing right, I at the end of K | −24 to 33 | **−29 to 24** |
| Defeat range, facing left, I at the end of K | −32 to 25 | **−23 to 30** |
| Attack frames when the roll carries on into running | D to D + 2 | **D to D + 5** |
| Attack frames when the roll ends in a stop | D to D + 7 | D to D + 7 |
| Earliest hurt after a roll that carries on | frame after `roll_end` | frame after `roll_end` (D + 7) |
| Earliest hurt after a stop | D + 12 | D + 12 |

The defeat range is the range for a moving player above, and facing left
is its mirror image. It is the same on every attack frame, for slow,
normal and fast rolls, with Action held or released. On S the thrower is
also defeated when I from the position at the end of S − 1 is within the
range (visible when the player moves a pixel or more on S, as in a fast
roll). Everything else in "Rolling into the thrower" holds: nothing on
D + 8 to D + 11 of a stop, no hurt on an attack frame, none on the frame
before a throw, and a roll on the floor about 80 px under the ledge
neither defeats the thrower nor is hurt by it. If the player standing is
hurt at the end of S − 1 (I −23 to 23 facing right), it is hurt on S and
no roll starts; with I at the end of S − 1 from −29 to −24, or 24, the
roll starts and defeats the thrower on S.
The pause, the boost and the speeds after the defeat are those of
"The second character" in `rolling-into-walkers.md`, and the thrower's
motion after the defeat is as in "After the defeat".

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

Rolling into the thrower:

- placements on its ledge, one pixel apart, I from −47 to 47 at the
  start of the frame: facing right and left, slow, normal and fast
  rolls, Action held throughout and pressed only on S, every frame from
  S − 2 to S + 48 (the roll, the frames that end it, the stop or the
  running after it); rolls reversed on S + 1 and S + 5, including one
  started with the thrower behind the player; and rolls on the floor
  under the ledge: 73 941 placements, each followed for two frames. Every
  set filled the ranges above with no gaps; 24 600 defeats on the frame
  itself, 1 042 on the next frame (the player moved into the range),
  9 600 hurts, none on an attack frame or on D + 8 to D + 11;
- by inputs alone, from two states with the player standing on the
  ledge left of the thrower, reached by inputs, every ball removed: 400
  runs (53 597 frames) with random approaches (standing, walking,
  running), slow, normal and fast rolls, Action held, released and
  pressed again, reversals, Jump and Down presses; each run checked
  until the player left the ledge's floor. 321 defeats (21 facing left
  after a reversal), 235 boosts (175 on K + 3), 45 hurts in runs without
  a defeat: every defeat frame, pause, boost, speed and the thrower's
  motion as described, 0 mismatches.

The second character rolling into the thrower:

- placements (a setup method was used to arrange them) on the ledge,
  one pixel apart, I from −50 to 50 at the
  start of the frame, facing right and left, slow, normal and fast
  rolls, Action held throughout and pressed only on S, every frame from
  S − 1 to S + 48, and rolls on the floor under the ledge: about 64 000
  placements, each followed for two frames. Defeats filled the ranges
  above with no gaps on every attack frame; hurts only in the standing
  range on S − 1 and from the frame after `roll_end` or from D + 12;
- in random play with inputs alone, starting with the second character
  standing on the ledge left of the thrower, the first character away
  from the ledge and no ball on screen: 200 runs (30 000 frames) with
  random approaches, slow, normal and fast rolls, Action held, released
  and pressed again, reversals, Jump and Down presses: 167 defeats (all
  facing right), 116 boosts, 0 mismatches in defeat frames, pause,
  boost, speeds and the thrower's motion. No defeat in these runs was
  facing left, so the facing-left ranges were checked by the
  placements only.

Crouching and the fidget pose:

- placements (a setup method was used to arrange them) on the ledge, I
  from −60 to 60 at one-pixel steps, followed for two frames, both
  characters: Down held from a standing start
  (facing right and, after a turn, left) and from walking, at the first
  and every following frame of the crouch; Down released after 20 frames
  with every frame from the release to 22 frames later; the first
  character standing at every frame from 225 to 900 (step 2 px in I),
  facing right, and facing left at six frames; the second character
  standing at 175 to 300 and at 800 frames. Every range above was the
  same on every frame of its period, and the edges fell on the frames
  given. A crouch begun in the first frames of a run, before the first
  character had settled, had a range one pixel to the left for its first
  nine frames; that was not reproduced from a settled stand and is not
  covered. These ranges were measured in arranged situations only, not
  in random play with inputs alone, and the thrower's flapping and
  throws were not varied separately.

The knocked-out thrower, knocked out by a bounce:

- contact: every frame from K + 4 until it was removed, the player
  placed at every second offset from the thrower's position, I from −60
  to 60 and U from −70 to 50 at the placement, each followed for two
  frames: standing on the ledge away from it and
  placed in the air (falling 2 px per frame and rising 1 px per frame),
  and in a jump's airborne pose (Jump held from 8 and from 20 frames
  before): 502 335 placements, no hurt and no bounce. As a control, a
  thrower that hadn't been knocked out was put at the same positions on
  the same frames: 353 495 placements, 61 956 bounces and 39 231 hurts;
- removal: 154 runs with the player kept at different places on the
  ledge, under it and in the air on both sides, so the view moved
  sideways and up or down while the thrower fell: removed on the frame
  the rule above gives in every run (67 below the picture, 49 on its
  left, 38 on its right, 2 of those also below it);
- coming back: 3 runs, the view moved away right and left and back
  (removed below, on the left and on the right of the picture); the
  same 3 without the knock-out as the control;
- after the player's knock-out: 4 runs, knocked out by a bounce or a
  roll, then the player knocked out by a walker, resuming at the level's
  start and at the restart point; each with a control run in which the
  thrower wasn't knocked out: seen at its place after the resume in all
  8.

## Not covered

- The Down + Action ground action on the thrower's ledge (the ground
  action is in `ground-action-enemies.md`). Crouching and the fidget
  pose away from the ledge; a crouch that begins in the first frames of
  a run, before the first character has settled; and the crouching and
  fidget ranges in random play with inputs alone.
- Rolling: defeats in the stop and on the frames that end a roll were
  measured by placements only (on the ledge, a roll reaches the thrower
  before then); rolls in the air near the thrower (rolling off the
  ledge's ends happens outside the ranges); two defeats in one roll.
- The upright pose in the air other than in the first frames of a jump,
  and the closing and landing pictures of the airborne pose
  (`walker-contact.md`, "Poses") in the air near the thrower.
- The knocked-out thrower: contact on K to K + 3 (in the measurements
  the player was still in the bounce then), a thrower outside the
  picture on its frozen frames K to K + 2, and a thrower more than
  31 px above the picture (`Y` − `W` less than −31).
  Contact and removal were measured after a bounce; after a roll or the
  ground action they are taken to be the same, the thrower's motion
  being the same.
- The second character's rolling defeats facing left in random play
  (checked by placements only); the second character using the
  ground action near the thrower; its landing frames other than those on
  the ledge; its rolls in the air near the thrower and two defeats in
  one roll. Contact while carrying, being hit or
  during a bounce's frozen frames, for either character; the follower.
- Throwers facing right and throwers in other levels.
