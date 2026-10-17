# Bouncing off an enemy

Titles: `title-1`, `title-2`

What happens to the player's motion when they bounce off an enemy, from
the bounce until they land. Which contacts with a walker cause a bounce
is in `walker-contact.md`; what happens to a walker is in `walker.md`.
Frames are logic frames at 60 Hz, numbered as in a trace. `y` and `vy`
are as in a trace: down is positive, `vy` in px per frame. Measured with
the lead character on several bounces off ground enemies at different
points in a level, with many patterns of jump and direction input.

The sections below up to "In `title-2`" give the `title-1` values.
"In `title-2`" gives the `title-2` values: the same frozen frames and the
same rule for holding jump, but a different bounce speed and gravity.

## The bounce frame

Call **B** the frame on which the bounce happens (the trace row carrying
the `bounce` event).

1. On B the player first moves exactly as on any other airborne frame:
   vertically by the rules that applied before the bounce (`jump-arc.md`
   or `walk-off.md`), horizontally as usual.
2. Then `vy` is set to **−7.125** (−7 1/8), whatever it was before.
   Measured incoming `vy` ranged from −1.3125 (still rising) to 7.25;
   the result was always −7.125.

The row for B shows the moved position and `vy` −7.125. `ground` is 0.

## Two frozen frames

On **B + 1** and **B + 2** the player doesn't move: `x`, `y`, `vx` and
`vy` stay exactly as they were at the end of B. Direction input on these
frames doesn't change `vx`.

## Each frame from B + 3

Until the player lands, on each frame:

1. `vy` increases by the frame's gravity, but never above **8**.
2. The player moves down by the new `vy` (up while `vy` is negative).
3. The landing rule in `landing.md` applies.

The gravity is:

- **0.28125** (9/32) if the jump input is held on **every** frame from
  B + 3 up to and including this frame;
- **0.4375** (7/16) otherwise.

Only the hold from B + 3 matters:

- Holding jump before or during the bounce neither helps nor is needed.
  A hold that started earlier counts only if it is still held on B + 3,
  and a press first made on B + 3 counts the same as a hold carried over.
- Releasing jump, even for one frame, gives 0.4375 for the rest of the
  bounce. Pressing jump on B + 4 or later never gives the lighter
  gravity, and doesn't start a jump.

Horizontally, the bounce doesn't change `vx`; from B + 3 on it changes
as on any other airborne frame.

While airborne after the bounce the player's trace state is `bounce`,
from B up to the landing frame.

## How long jump is held

Let **K** be the number of frames jump is held without a break, starting
with B + 3 (K = 0 if it isn't held on B + 3). The lighter gravity applies
on frames B + 3 to B + 2 + K.

`bounce_height` as defined in `traces/METRICS.md` (it doesn't depend on
how high above the ground the bounce happens, as long as the player
doesn't land or hit anything before the top of the arc):

| K | `bounce_height` (px) |
|---|---|
| 0 | 54.5 |
| 1 | 57 |
| 2 | 59.34375 |
| 3 | 61.6875 |
| 4 | 63.875 |
| 5 | 65.9375 |
| 8 | 71.625 |
| 10 | 74.90625 |
| 15 | 81.375 |
| 20 | 85.3125 |
| 25 or more | 86.71875 |

From K = 25 on, the top of the arc doesn't get higher, but holding longer
still makes the fall slower.

On frame B + 2 + n (n ≥ 1, before landing and before `vy` reaches 8),
with gravity g used on every frame since B + 3:

- `vy` = −7.125 + g × n
- `y` relative to B = −7.125 × n + g × n(n + 1) / 2

## Example

A synthetic bounce 20 px above flat ground, jump not held after the
bounce (K = 0). Heights are above the floor at the end of each frame.

| Frame | Height above floor | `vy` | Notes |
|---|---|---|---|
| B | 20 | −7.125 | `bounce` |
| B + 1 | 20 | −7.125 | frozen |
| B + 2 | 20 | −7.125 | frozen |
| B + 3 | 26.6875 | −6.6875 | |
| B + 4 | 32.9375 | −6.25 | |
| B + 18 | 74.5 | −0.125 | top of the arc (54.5 above B) |
| B + 19 | 74.1875 | 0.3125 | |
| B + 35 | 9.6875 | 7.3125 | |
| B + 36 | 1.9375 → 0 | 7.75 → 0 | under 5 px and moving down: lands, placed on the floor, `vy` 0, `land` |

## In `title-2`

Everything in this section was measured on `title-2` only, with the lead
character, on bounces off two ground enemies at two places in the first
level, with many patterns of jump, direction and Action input. Speeds
are whole multiples of 1/256 px per frame, written both ways. **B** is
the bounce frame (the row with the `bounce` event), as above. The jump
arc of `title-2` and its names (take-off frame T, hold length H) are in
`jump-arc.md` ("In `title-2`"); air targets are in `air-control.md`
("In `title-2`").

### The bounce frame (`title-2`)

1. On B the player first moves exactly as on any other airborne frame of
   the jump that brought them there: vertically by the `title-2` jump
   rules, horizontally by the `title-2` air rules.
2. Then `vy` is set to **−8.21875** (−2104/256), the same speed as a
   `title-2` take-off, whatever it was before.

`ground` is 0 on B. In every measured bounce the player was falling onto
the enemy: incoming `vy` (on the frame before B) ranged from 4.46875 to
7.65625.

### Two frozen frames (`title-2`)

On **B + 1** and **B + 2** the player doesn't move: `x`, `y`, `vx`, `vy`
and `facing` stay exactly as they were at the end of B.

All input on these two frames is ignored. In particular, for the first
frame after them, B + 3, "the previous frame" in the air rules (which
decides between the walking and running target) is **B**, not B + 2:

- Action held with a direction on B gives the running target on B + 3,
  even if Action is released on B + 1 and B + 2.
- Action held only on B + 1 or B + 2 gives the walking target on B + 3.
- A direction held only on B + 1 or B + 2 doesn't turn the player and
  doesn't change `vx`.

### Each frame from B + 3 (`title-2`)

From B + 3 on, the player moves exactly as in a `title-2` jump whose
take-off frame T is **B + 2**, except for which frames count for holding
jump. Until the player lands, on each frame:

1. `vy` increases by the frame's gravity, but never above **8**.
2. The player moves down by the new `vy` (up while `vy` is negative).

The gravity is:

- **0.3515625** (90/256) if the jump input is held on **every** frame
  from B + 3 up to and including this frame, **and** `vy` before this
  frame's increase is negative (the player is still rising);
- **0.4375** (112/256) otherwise.

Only the hold from B + 3 matters, as in `title-1`:

- A hold that started before the bounce counts only if it is still held
  on B + 3, and a press first made on B + 1, B + 2 or B + 3 counts the
  same as a hold carried over.
- Releasing jump, even for one frame, gives 0.4375 for the rest of the
  bounce. Pressing jump on B + 4 or later never gives the lighter
  gravity, and doesn't start a jump.
- As in a `title-2` jump, the lighter gravity stops at the top of the arc
  even if jump is still held.

Horizontally, from B + 3 on, `vx` and `facing` follow the `title-2` air
rules (with the "previous frame" exception above).

As in `title-1`, the player's trace state is `bounce` from B up to the
landing frame. The landing frame's row is a grounded row (`ground` 1, a
grounded state, `land`), as on any other landing; the `bounce-walker`
reference trace (see "Example (`title-2`)") has one.

### How long jump is held (`title-2`)

Let **K** be the number of frames jump is held without a break, starting
with B + 3 (K = 0 if it isn't held on B + 3). The lighter gravity applies
on frames B + 3 to B + 2 + K while the player is rising. The arc from
B + 2 on is the arc of a `title-2` jump from flat ground held for
H = K + 3 frames, taken from its take-off frame. So `bounce_height`
(`traces/METRICS.md`) is the `apex_height` of that jump:

| K | `bounce_height` (px) |
|---|---|
| 0 | 73.125 |
| 1 | 74.671875 |
| 2 | 76.2109375 |
| 3 | 77.671875 |
| 4 | 79.046875 |
| 5 | 80.3359375 |
| 7 | 82.7265625 |
| 9 | 84.875 |
| 12 | 87.515625 |
| 15 | 89.578125 |
| 17 | 90.578125 |
| 20 | 91.609375 |
| 22 | 91.9140625 |
| 23 or more | 92 |

From K = 23 on, holding longer changes nothing: the player is already
falling, and falling always uses 0.4375. The height doesn't depend on how
high above the ground the bounce happens, as long as the player doesn't
land or hit anything before the top of the arc.

On frame B + 2 + n (n ≥ 1, before landing and before `vy` reaches 8),
with gravity g used on every frame since B + 3:

- `vy` = −8.21875 + g × n
- `y` relative to B = −8.21875 × n + g × n(n + 1) / 2

### Example (`title-2`)

A synthetic bounce 20 px above flat ground, jump not held after the
bounce (K = 0). Heights are above the floor at the end of each frame.

| Frame | Height above floor | `vy` | Notes |
|---|---|---|---|
| B | 20 | −8.21875 | `bounce` |
| B + 1 | 20 | −8.21875 | frozen |
| B + 2 | 20 | −8.21875 | frozen |
| B + 3 | 27.78125 | −7.78125 | |
| B + 4 | 35.125 | −7.34375 | |
| B + 5 | 42.03125 | −6.90625 | |
| B + 20 | 93.125 | −0.34375 | top of the arc (73.125 above B) |
| B + 21 | 93.03125 | 0.09375 | |
| B + 22 | 92.5 | 0.53125 | |
| B + 40 | 8.53125 | 8 | `vy` reaches the cap |
| B + 41 | 0.53125 | 8 | |

On B + 42, moving by 8 would take the player below the floor, so B + 42
is the landing frame (see "Not covered in `title-2`" for the landing
itself).

Scenario `bounce-walker` holds jump from frame 20 to the end, so K is
23 or more: in its `title-2` reference trace B is frame 64, 13.125 px
above the floor, the top of the arc is 92 px above B, and the player
lands on frame 112.

### Evidence (`title-2`)

268 bounces (252 followed to the landing), from standing, walking and
running jumps and jumps made as the enemy walked up, with jump held for
0 to 30 frames from B + 3 or until landing and direction and Action input on every frame
from B − 1 to B + 3: B (vertical and horizontal), both frozen frames,
and 10,301 airborne frames after them match this section with 0
mismatches, including 368 frames at the cap of 8. Every `bounce_height`
in the table was measured.

### Not covered in `title-2`

- Which contacts with other kinds of enemies cause a bounce rather than
  a hurt (walkers: `walker-contact.md`, "In `title-2`").
- The landing frame and the frames after it, beyond the one landing on
  flat ground in the `bounce-walker` reference trace (the measured
  landings were not on flat ground). Jumping on landing after a bounce.
- Bounces from a roll, bouncing again before landing, bouncing while
  carrying an object, and the second character.

## Not covered

This list is for `title-1`; `title-2` has its own list above.

- Which contacts with other kinds of enemies cause a bounce (walkers:
  `walker-contact.md`).
- Facing during the frozen frames.
- Bouncing off other kinds of objects (the animal crate is in
  `animal-crate.md`), bounces from a roll, bouncing again before
  landing, and the other character.
- Jumping on the landing frame after a bounce.
- Falls long enough after a bounce for `vy` to reach 8 weren't measured
  (the largest seen was 7.75); the cap is taken to be the same as for
  other falls.
