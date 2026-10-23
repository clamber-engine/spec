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

Everything here, except "The second character", was measured with the
first character leading and alone: on a copy of the barrel placed near the first level's start in a
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
(`extra-lives.md`). How a launch barrel is drawn: "Pictures".

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

**A long stand.** The standing row doesn't hold on every standing
frame. With S the first standing frame after walking (`ground-speed.md`),
the first character standing on flat floor is caught with `I` from −14
to 30 and `D` from −33 to 36 on the frames S + 239 to S + 418, and by
the standing row on S to S + 238 and on S + 419 to S + 453, the end of
the stand measured (both facings; facing left as in "Offsets"). Later
frames of a longer stand were not measured. The second character has no such change ("The second
character").

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

## The second character

With the second character leading, a `left-launch-barrel` catches, holds
and shoots on the same frames as above (C, C + 1, the hold to C + 15,
the shot on C + 16, no gravity to C + 39, ready again from C + 63) and
the player lands on the same frame; `vy`, the level y and `facing` follow the
rules above on every frame. Two things differ: the catch ranges, and the
horizontal target in the flight.

**Catch ranges.** `I`, `D`, the facings and the 3 px rule are as in
"Being caught". Ends are included.

| State | `I` | `D` |
|---|---|---|
| Standing, from S (the first standing frame after walking, `ground-speed.md`) on | −21 to 17 | −36 to 30 |
| Walking and running, including the frames of a stop before S; rolling (the second character has no Down + Action ground action, `roll-input.md`); jumping, on every frame from the take-off frame `T` on | −27 to 18 | −32 to 30 |
| Crouching | −28 to 20 | −35 to 17 |

So a standing second character is caught over a range 6 px narrower in `I`
at the left end than a walking one, and reaches 4 px further up. Unlike the first character's,
the jump has one row: it doesn't change at `T` + 6, and a barrel
stays in reach for the whole jump. The standing row held on every
frame from S for as long as it was measured (331 frames), whatever the
pictures shown (`player-frames.md`, "Standing still").

**The flight.** Step 1 of "The flight" has the target **−3.375**
(−864/256) with Left held and **+3.375** (+864/256) with Right held;
with neither it is 0 as above. Everything else in the flight is as for the first
character, the gap still being divided by 32 and rounded toward zero.
For example, with nothing held `vx` goes −2167/256 → −2100/256 on C + 40
as above; with Left held, −2167/256 → −2127/256 (C + 40) → −2088/256 →
−2050/256 → …; with Right held, −2167/256 → −2073/256 → −1982/256 →
−1894/256 → ….

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
  flash appears where it was (its picture and motion: "Pictures"). The player faces **right**, whatever the facing
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

## Pictures

What a launch barrel, the smoke at a catch and the flash at a shot look
like on each frame in `title-1`. Each picture has a label used in this
file and is named by its picture hash (`../formats/sprites.md`, "Picture
hashes"); it is drawn at the object's position as
`../formats/sprites.md` says ("Where the picture is drawn"). The picture
given for a frame is the one on screen with that frame's view. None of
these is ever mirrored. "C + 4 to C + 6" means those frames and the ones
between; `n` below is the frame number minus C.

| Picture | Picture hash (SHA-256) |
|---|---|
| `rest` | `2dc9b09ee6e8ccecf28eabe2013fe1d77132160fd8f311a561fa13e44b933e7f` |
| `open-1` | `f0e8b6b1331420b2dac2941c46a37d66fc3412a5ca0561ce75b6820ac09420f9` |
| `open-2` | `042ef4c5ec23175b6c94593985db721a96cca06f6d5603a4e710bc7b29177e3b` |
| `open-3` | `35f403dce7a2841db70792f60a595a334f7b1b7d31d68f1e63405ac89961d2e3` |
| `down-rest` | `f806872ae2b445cb8eb0cdc5453a49b79f336140d775233a55d7224c772428b3` |

The `open` pictures show the barrel more and more open, from `open-1`
on. The barrel's further opening pictures are under "Not covered".

**When a hash isn't found.** The importer looks for every hash in this
table. If any isn't found, the engine shows its own art for that kind of
barrel on every frame, keeping the timing given here. The smoke and the
flash are those of `animal-crate.md` and `thrown-objects.md` ("Pictures"),
with the same rule there.

**The `left-launch-barrel`.** Before a catch, and from C + 61 on:
`rest`. After a catch on C:

| Frames | Picture |
|---|---|
| `n` 0 to 3 | `rest` |
| `n` 4 to 60 | not covered |

A barrel that doesn't catch is `rest` on every frame.

**The `down-launch-barrel`.** Before a catch: `down-rest`. After a catch
on C:

| Frames | Picture |
|---|---|
| `n` 0 to 4 | `down-rest` |
| `n` 5 to 16 | not covered |
| `n` 17 to 20 | `open-3` |
| `n` 21 to 23 | `open-2` |
| `n` 24 to 26 | `open-1` |
| `n` 27, 28 | `rest` |

It is gone from `n` 29 ("The barrel that shoots down").

**The smoke.** On C a smoke appears 15 px left of the barrel's position
and 18.5 px above it, and doesn't move. It shows `smoke-1` on `n` 0 and
1, then each of `smoke-2` to `smoke-6` for 3 frames (`n` 2 to 4, 5 to 7,
8 to 10, 11 to 13, 14 to 16), and is gone from `n` 17. The same on both
kinds.

**The flash.** It appears on the frame of the shot (C + 16 for the
`left-launch-barrel`, C + 29 for the `down-launch-barrel`, where the
barrel breaks), at the barrel's `x` and 16 px above its position, and
moves on that frame and on each frame after by 271/256 px (about
1.0586 px): to the left for the `left-launch-barrel`, down for the
`down-launch-barrel`. It shows `flash-1` for 4 frames, then each of
`flash-2` to `flash-8` for 5 frames, 39 frames in all, and is then gone
(from C + 55, and from C + 68).

Measured by the pictures on screen, in 4 runs with the first character
that used only inputs: three walks into a copy of a barrel on the floor
(the `left-launch-barrel` twice, once with Left held during the hold; the
`down-launch-barrel` once; 3 catches and 3 shots in all) and a jump past
the `down-launch-barrel` that didn't catch. On 831 frames the barrel was
drawn whole and recognisable, 77 flash frames and 33 smoke frames
likewise, and every one showed the picture given above for that frame. On
2 frames in which the flash was drawn over the barrel were not
recognisable as any one picture, and they were not counted. The
player's picture covers the barrel during the hold, so the
`left-launch-barrel`'s opening pictures were not seen in these runs, and
those of the `down-launch-barrel` only from `n` 17 to 26. The timings were
checked on every frame of all 4 runs.

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
- **The second character leading** (the `left-launch-barrel`). Catch
  ranges: a setup method was used to put the barrel, for each pose, at
  offsets from the lead one pixel apart, once along `I` at `D` = 0 and
  once along `D` at `I` = 0, and whether the lead was caught was read
  from the screen: standing, walking after 1 to 30 frames, running, rolling,
  crouching, jumps of 1 to 45 frames with Jump held, in both facings
  (about 40 states), and the standing row on each of 331 frames of one
  stretch of standing. The same scans with the first character gave
  every row of the table in "Catch ranges" (standing, walking, running,
  the ground action, crouching, rolling and the jump rows) and so
  checked the method. The flight: with nothing held, Left held and
  Right held, with the barrel on the floor and 60 px above it: `vx` followed
  the rule with ±3.375 on all 42 flight frames of the three
  raised runs, and `vy`, the level y, `facing` and the landing frame were the
  same as the first character's in all six runs.
- The first character's long stand: the same scans on each frame of a
  stand of 454 frames, once facing right and once facing left.
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

- The second character's catch ranges and flight target, and the first
  character's long stand, were measured with the setup method above and
  not checked in runs of ordinary play (the first character's other rules
  were).
- Two characters: what the other character does while the lead is
  held or shot. The second character's catches in states not in its
  table (landing frames, turning, the stop after a roll, falling without
  a jump, carrying an object, the air after a tyre jump, and others),
  and the `down-launch-barrel` with the second character.
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
- The pictures of the barrel's opening that no run with inputs showed:
  all of the `left-launch-barrel`'s (`n` 4 to 60) and the
  `down-launch-barrel`'s from `n` 5 to 16, including the most open
  ones. For these frames the engine shows its own art.
- Which of the barrel and the player is drawn in front, how the shot
  player is drawn, the small objects left along the shot player's path
  and at the break (too small to be named by a hash), and the sounds.
- Trace states and events for the frames held and shot.
- The `down-launch-barrel`'s catch ranges, and a
  shot player meeting anything but the floor or the `hidden-barrel` on
  the way down.
- Launch barrels that shoot in other directions or wait for a button;
  the other levels; the other titles.
