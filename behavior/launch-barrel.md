# Launch barrels

Titles: `title-1`

A **launch barrel** is an object placed in a level that catches the
player, holds them for a moment and then shoots them out. This file
covers the two kinds the first level has: one that shoots the player to
the left (when it catches the player, the hold, the shot, the flight
until the player lands, and when it can catch again), and one that
shoots the player straight down and breaks ("The barrel that shoots
down").

Frames are logic frames at 60 Hz, numbered as in a trace. `x` grows to
the right. `x`, `vx` and `facing` are as in a trace (`vx` in px per
frame, right positive). Vertical positions are **level y** as in
`view-vertical.md` (growing downward); `vy` is as in a trace (down
positive). The player's **feet line** `F` is as in `view-vertical.md`.
"Left", "Right", "Jump", "Action" are the neutral inputs `L`, `R`, `J`,
`A` of the scenario format. Speeds are whole multiples of 1/256 px per
frame, and this file writes them both ways where it matters.

Everything here was measured with the first character leading and
alone: on a copy of the barrel placed near the first level's start in a
copy of the image (on the floor, and 40, 60 and 75 px above it), and on
the first level's own barrel. The catch ranges were measured by placing
the player at offsets from the barrel one pixel apart, as for
extra-life items (`extra-lives.md`), and the rules were then checked by
inputs alone (see "Checks").

## The kind

| Kind | Shoots the player | Hold |
|---|---|---|
| `left-launch-barrel` | to the left | 15 frames |
| `down-launch-barrel` | straight down, and breaks | 28 frames |

A level's placement list places a launch barrel with an entry of one of
these kinds (`../formats/placements.md`, "Kinds"). From here up to "The
barrel that shoots down", this file is about the `left-launch-barrel`.
The first level has one, high up
near the level's end, above the stiff tyre (`tyre.md`, "The stiff tyre"):
a tyre jump from that tyre takes the player into it, and the shot
carries them left through the two `extra-life-3` items there
(`extra-lives.md`). How a launch barrel is drawn is not covered.

A launch barrel's **position** (`xb`, `yb`) is its entry's level x and
level y. It stays there: it doesn't fall, even when placed in the air,
and it doesn't move when it catches or shoots.

## Being caught

A launch barrel is **ready** unless it caught the player on one of the
62 frames before (see "Ready again"). On a frame **C** on which the
barrel is ready, the player is caught if, **after the player's move** on
C (walking, running, rolling, jumping, falling, landing), the offsets
below are inside the ranges for the player's state on C. There is no
condition on `vy`: a rising player is caught as readily as a falling
one.

### Offsets

Using whole pixels, let

- `X` be the player's `x` rounded down;
- **`I`** be how far the player is past the barrel in the direction the
  player is facing: `X − xb` when facing right, and `xb − 3 − X` when
  facing left;
- **`D`** = `F − yb`, how many px the feet line is below the barrel's
  position (negative when it is above).

As in `extra-lives.md`, each range below holds for both facings; facing
left, the ranges are 3 px further left than the mirror image of the
facing-right ones. For example, a standing player facing right is
caught with `X − xb` from −24 to 27; facing left, from −30 to 21.

### Catch ranges

| State | `I` | `D` |
|---|---|---|
| Standing, walking, running, the Down + Action ground action | −24 to 27 | −34 to 32 |
| Crouching (Down held on the ground) | −27 to 18 | −38 to 23 |
| Rolling | −22 to 27 | −30 to 30 |
| Jumping, from the take-off frame `T` to `T` + 5 | −24 to 27 | −34 to 32 |
| Jumping, from `T` + 6 on | −24 to 24 | −13 to 49 |
| In the air after a tyre jump (`tyre.md`) | −24 to 24 | −13 to 49 |

Ends are included. `T` is the frame carrying `jump_start`
(`jump-arc.md`); the jump rows switch on `T` + 6 as in
`extra-lives.md`, whatever the vertical speed and whether Jump is still
held. These are not the extra-life item's ranges: each is 4 px wider on
both sides in `I`, and in `D` reaches 5 px further up (more negative)
and 3 px less far down.

So a player walking along a floor on which a barrel stands (`D` = 0)
is caught when `I` first reaches −24, from either side. A barrel whose
position is up to 32 px above the floor catches a player walking under
it.

## The catch frame

On C the player has moved as usual. Then `vx` and `vy` become 0 and the
player is no longer on the ground (`ground` 0). The position on C is
the moved position.

## The hold

- **C + 1:** the player is moved by whole pixels so that `X` = `xb` and
  `F` = `yb` (`I` and `D` are both 0 facing right). The fractions of a
  pixel of `x` and of the level y are kept. `vx` and `vy` are 0.
- **C + 2 to C + 15:** the player doesn't move. `vx` and `vy` stay 0.

Inputs make no difference from C + 1 to C + 15: Left, Right, Up, Down,
Jump and Action, held or pressed on any of these frames, change nothing
here or later. `facing` doesn't change during the hold: it is the facing
the player had on C.

## The shot

- **C + 16:** the player faces left. `vx` becomes **−8.46484375**
  (−2167/256) and `vy` 0. The player doesn't move on C + 16.
- **C + 17 to C + 39** (23 frames): `x` changes by exactly `vx` each
  frame and `vx` stays −8.46484375. The level y doesn't change: there
  is no gravity. Inputs make no difference.

So on C + 39 the player is 23 × 8.46484375 = 194.69140625 px left of
where they were held, at the same height.

## The flight

From **C + 40** until the player lands, on every frame:

1. **Horizontal speed.** The target is −3 (−768/256) with Left held,
   +3 (+768/256) with Right held, and 0 with neither. Action makes no
   difference to the target. Using speeds in 1/256 px per frame,
   **step** = (target − `vx`) ÷ **32**, rounded toward zero; `vx`
   changes by step. (In `air-control.md` the gap is divided by 8; here
   it is 32, and the target is ±3 with or without Action.)
2. **Vertical speed.** `vy` grows by **0.4375**, up to 8 (the cap of
   `jump-arc.md`). Jump held or not makes no difference.
3. The player moves by the new `vx` and `vy`.

`facing` stays left until the player lands, even with Right held. The
flight ends on the frame the player lands (`landing.md`); on that frame
`vx` still follows step 1. From the next frame the usual rules apply
(the player is on the ground again).

For example, with nothing held, `vx` goes −2167/256 → −2100/256
(−8.203125) on C + 40 → −2035/256 → −1972/256 → … (each step is the
speed ÷ 32, rounded toward zero). With Left held it goes −2167/256 →
−2124/256 → −2082/256 → …; with Right held, −2167/256 → −2076/256 →
−1988/256 → ….

A barrel on the floor shoots the player along the floor; they land on
C + 40, the first frame with gravity.

## Ready again

After a catch on C, the barrel catches no one on C + 1 to C + 62, and
is ready again from C + 63. In the measurements, a player put back at
the barrel's position at the end of any frame from C + 52 to C + 62,
while still in the flight, was caught on C + 63; no catch on C + 1 to
C + 62 was seen in any run.

## The barrel that shoots down

The first level has one `down-launch-barrel`, at the top edge of the
level above the second wall exit. Play comes back to the level inside
it from the area behind that wall exit (`wall-exits.md`, "Coming back
from the second wall exit's area"). Its position (`xb`, `yb`) is as for
the `left-launch-barrel`, and it doesn't move either. `I`, `D` and the
speeds are as above (`vy` down positive).

**Being caught.** In the cases measured it catches the player as the
`left-launch-barrel` does: a lead walking right along the floor into a
copy of it on the floor was caught on the first frame with `I` = −24
(`D` = 0); a copy 60 px above the floor didn't catch a lead walking
under it. A lead falling next to it in the air after coming back to the
level (`wall-exits.md`) was caught with `I` from −11 to 21 and `D` 1, 2
or 32, and not with `I` from 39 to 41. Its catch ranges were not
measured separately. The catch frame **C** and C + 1 are as in "The
catch frame" and "The hold".

**The hold.** C + 2 to C + **28**: the player doesn't move, `vx` and
`vy` stay 0, `facing` is the facing on C, and inputs make no difference,
here or later.

**The shot.**

- **C + 29:** the barrel **breaks**: it is gone from this frame, and a
  broken piece appears where it was (how the pieces move and are drawn
  is not covered). The player faces **right**, whatever the facing
  before. `vx` becomes 0 and `vy` **+8.46484375** (+2167/256, down). The
  player doesn't move on C + 29.
- **C + 30:** the level y grows by exactly 8.46484375 and `x` doesn't
  change. There is no gravity, and inputs make no difference.

**The flight.** From **C + 31** until the player lands, every frame
follows the three steps of "The flight" above (target −3, +3 or 0 with
Left, Right or neither held, Action making no difference; step = gap ÷
32 rounded toward zero; `vy` grows by 0.4375 up to 8; then the move). So
on C + 31 `vy` becomes 8, and it stays 8. `facing` stays right until the
player lands, even with Left held. The landing frame and the frames
after are as for the `left-launch-barrel`. For example, with Right held
from C + 31, `vx` goes 0 → 24/256 (0.09375) on C + 31 → 47/256 → 69/256
→ ….

A barrel on the floor shoots the player into the floor: they land on
C + 30.

**Ready again.** Never: once it has broken it is gone.

## Example

The barrel's entry has level x 400 on a flat floor at level y 337, so
`yb` = 337. A player walking right on that floor at 2 px per frame is at
`x` 374.2265625 at the end of frame 130 (`I` = −26). On frame 131 they
move to 376.2265625: `I` = −24, inside the walking range, and `D` = 0,
so C = 131. At the end of 131, `x` is 376.2265625 with `vx` 0 and `vy` 0.

| Frame | `x` | level y | `vx` | `vy` | `facing` |
|---|---|---|---|---|---|
| 131 (C) | 376.2265625 | 337 | 0 | 0 | right |
| 132 to 146 | 400.2265625 | 337 | 0 | 0 | right |
| 147 (C + 16) | 400.2265625 | 337 | −8.46484375 | 0 | left |
| 148 | 391.76171875 | 337 | −8.46484375 | 0 | left |
| 170 (C + 39) | 205.53515625 | 337 | −8.46484375 | 0 | left |
| 171 (C + 40) | 197.33203125 | 337 (lands) | −8.203125 | | left |

On 171 the player lands on the floor; from 172 `vx` follows the
ground rules (`ground-speed.md`): with nothing held it is −7.1796875
(−1838/256) on 172.

## Checks

All but the last item are for the `left-launch-barrel`.

- Catch ranges: 75 grids of placements (a copy of the barrel 60 px
  above the floor; standing, walking, running, crouching, rolling, the
  ground action, and jumps at 17 frames from take-off to well after the
  top, Jump held or released early; both facings), 899 100 placements,
  242 956 of them caught: every one caught or not as the ranges say.
- By inputs only: 600 runs of random input around a copy of the barrel
  on the floor, 40 px and 75 px above it (about 870 000 frames), and
  200 runs on the first level's own barrel, each with a tyre jump from
  the stiff tyre into it followed by random input (about 97 000
  frames). The lead was caught 1 636 times. All 1 470 catches in the
  states of the table were on the frame the rules give, and the rules
  gave no other catch; 166 more were in states not covered here (see
  "Not covered"). After the catches, every frame of the hold, the shot
  and the flight up to the landing (87 284 frames, 1 625 shots, 1 611
  landings) matched the rules in `x`, level y, `vx`, `vy` and `facing`,
  except in the five cases under "Not covered". With the extra-life
  item's ranges instead, 1 021 catches came out wrong; with the gap
  divided by 8 in the flight, 23 965 frames.
- The first level's own barrel: in all 200 runs the player was caught
  in the air after a tyre jump, shot left, took both `extra-life-3`
  items there during the shot (5 lives before, 11 on C + 40), and landed
  on the floor below. The catch was on the first frame the tyre-jump
  row puts the player in range (`D` from 45 to 47 then, more than 49 on
  the frame before). These runs agree with that row but don't pin down
  its ends: it is the jump's row, not measured separately.
- The hold and the shot with every input: Left, Right, Left or Right
  with Action, Jump, Action, Up and Down held from C + 1 to C + 14, and
  the same held from C + 20 on, compared with no input: the hold and the
  shot were the same in every case, and from C + 40 only Left and Right
  (with or without Action) changed the flight.
- **The `down-launch-barrel`.** The first level's own barrel, in 301
  runs coming back from the second wall exit's area with random inputs
  (`wall-exits.md`), and in 33 runs in copies of the image with the
  start point or the barrel moved: caught on frame 2 of the area in all
  of them, and every frame from C to the landing as above (26 605 frames
  compared), except that in 108 runs the player came down onto the
  level's `hidden-barrel` and bounced (`hidden-objects.md`, "Landed on
  after a long fall"), each on the frame that file gives, and no other
  run had such a frame; those runs were compared up to the frame before
  the bounce. 26 runs were facing left on C: right from C + 29 in all.
  With no input, the barrel was there up to C + 28 and gone from C + 29,
  with a broken piece there from C + 29. In 11 runs in a copy with the
  start point 40 px right of the barrel, the lead was never caught. A
  copy of the barrel given to a class 1 entry near the first level's
  start, on the floor: a lead walking right into it was caught at `I` =
  −24, held, shot down on C + 29 and landed on C + 30; 60 px above the
  floor it caught nothing.

## Not covered

- The second character, and two characters: what the other character
  does while the lead is held or shot.
- Catches in states not in the table (166 in the runs): landing frames,
  turning and the frame after a turn, the stop after a roll, falling
  without a jump, carrying an object, and others. In three catches
  while throwing a carried object (`carrying.md`) the shot came later
  than C + 16 (once on C + 49).
- Touching enemies during the shot or the flight. In two runs the
  player bounced off a walker during the flight (`bounce.md`); then the
  flight rules above no longer held.
- Walkers and other objects touching the barrel: in 12 runs a walker
  set it off while the player was far away.
- Hitting walls, ceilings or slopes during the shot and the flight, and
  landing on a slope.
- The catch ranges during the flight (a shot player caught again).
- When a launch barrel is removed and appears again
  (`../formats/placements.md`, "When placed objects appear").
- How the barrel and the shot player are drawn, the objects drawn
  around them, and the sounds.
- Trace states and events for the frames held and shot.
- The `down-launch-barrel`'s catch ranges, the broken pieces, and a
  shot player meeting anything but the floor or the `hidden-barrel` on
  the way down.
- Launch barrels that shoot in other directions or wait for a button;
  the other levels; the other titles.
