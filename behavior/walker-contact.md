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
- Contact while turning on the ground, rolling, carrying an object or
  bouncing, and with two walkers on the same frame.
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
- Falls without a jump (walking or rolling off a ledge): the player's
  vertical reach was seen to differ from that of a jump.
- Walkers above the player (U below −6 on the ground, below −4 in the
  air), and the lower end of the vertical reach in the air.
- The other character, and hits while two characters are present.
- Other kinds of enemies, and how to tell the two kinds of walker apart
  other than by these sizes.
