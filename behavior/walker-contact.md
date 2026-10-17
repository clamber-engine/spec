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
airborne and fidget poses change them.

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

- Walkers that aren't walking at full speed (turning, speeding up,
  stopped at a barrier, frozen) and walkers already bounced on.
- Contact while crouching, carrying, after a bounce, or being knocked
  back. Rolls into walkers are in `rolling-into-walkers.md`.
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
