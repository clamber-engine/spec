# Rolling into walkers

Titles: `title-1`

What happens when a rolling player meets a walker (`walker.md`): on which
frames and at which offsets the walker is defeated, the pause that
follows, how the roll carries on and speeds up, what happens to the
walker, and when the player can be hurt around a roll. When a roll
starts, its speed and how it ends without a walker are in
`roll-input.md` and `roll-speed.md`; which contacts hurt a player who
isn't rolling is in `walker-contact.md`.

Frames are logic frames at 60 Hz, numbered as in a trace. `x`, `vx` and
`facing` are as in a trace: right is positive, `vx` in px per frame.
"Left", "Right", "Down", "Jump", "Action" are the neutral inputs `L`,
`R`, `D`, `J`, `A` of the scenario format. This spec uses the names of
`roll-input.md`: **S** is a roll's `roll_start` frame (its press frame)
and **D** = S + 29 is the frame after its 29 frames. **K** is the frame
on which a walker is defeated.

Speeds are whole multiples of 1/256 px per frame. A roll's **size** is as
in `roll-speed.md` (slow 1, normal 3, fast 4); this spec adds sizes 2
and 5 (512/256 and 1280/256 px per frame).

Measured with the first character alone, on flat floor and 1-in-4
slopes, on the two kinds of walker of `walker-contact.md`, by inputs
alone: 1 089 runs (about 253 000 frames, 631 defeats, 17 of them a
second defeat in the same roll) with slow, normal and fast rolls,
Action held, released and pressed again at every frame around a
defeat, direction, Jump and Down presses on the frames after it, and
walkers met during the roll, during the frames that end it and during
the stop after it. In every run, on every frame from the
press of the roll that meets a walker to the end of that roll's ending,
the defeat frames, the frames of the roll, its ending and the stop,
`vx` on roll frames, the frames on which the player was hurt (507 runs
ended in a hurt) and the walker's motion match the rules below; in runs
without a defeat, no defeat was due. The exact offsets were found
by placing the player at chosen offsets from a walker, one pixel apart,
in poses reached by inputs, at every phase of slow, normal and fast
rolls and of the stop after a roll.

## Attack frames

A walker can be defeated only on an **attack frame**:

- every frame of a roll, from `roll_start` up to the last frame before
  `roll_end` (when the roll carries on into running, this includes the
  frames D, D + 1 and D + 2 of `roll-input.md`);
- when the roll ends in a stop: the first 8 frames of the stop, D to
  D + 7.

The two frozen frames after a defeat (below) are not attack frames.
Running after a roll, the later frames of the stop, and every other
state are not attack frames.

## When a walker is defeated

On an attack frame K, a walker walking at full speed (1 px per frame,
`walker.md`) on the same floor or slope as the player is defeated on K
when, with both positions **at the end of K** (after both have moved on
K), the offset **I** is within the defeat range below. I is as in
`walker-contact.md`: the player's `x` minus the walker's `x`, both
rounded down to whole pixels.

Defeat ranges for I (both ends included):

| Walker | Player facing | Walker heading left | Walker heading right |
|---|---|---|---|
| small | right | −16 to 25 | −17 to 24 |
| small | left | −24 to 17 | −25 to 16 |
| large | right | −19 to 28 | −20 to 27 |
| large | left | not measured | not measured |

The large walker's ranges are the small walker's widened by 3 px on each
side, as for its hurt and bounce ranges.

The ranges are the same on every attack frame: for slow, normal, fast
and sped-up rolls (below), on the frames that end a roll, and in the
first 8 frames of the stop. The defeat is decided on the frame itself,
not one frame ahead as a hurt is.

Contact on an attack frame never hurts the player. A walker that is
within the hurt range of `walker-contact.md` but outside the defeat
range at the end of an attack frame does nothing on that frame; in the
cases measured, it reached the defeat range on a later frame.

## Being hurt around a roll

- **Before the roll.** If the hurt conditions of `walker-contact.md`
  hold at the end of S − 1, the player is hurt on S even though Action
  is pressed on S, and no roll starts.
- **During the roll** (every attack frame), the player is never hurt by
  a walker.
- **A roll that carries on into running:** from its `roll_end` frame the
  hurt rule of `walker-contact.md` applies again. The earliest hurt is on
  the frame after `roll_end`.
- **A roll that ends in a stop:** on D + 8 to D + 11 a walker neither is
  defeated nor hurts the player. From D + 11 the hurt rule applies again:
  the earliest hurt is on D + 12.

## The pause

On **K + 1** and **K + 2** neither the player nor the defeated walker
moves: the player's `x` and `vx` stay as they were at the end of K.
Inputs on these two frames have no effect: a direction, Jump, Down or an
Action press made only on K + 1 or K + 2 changes nothing. (Action held on
these frames counts only if it is still held on K + 3, below.)

All of the roll's timing then resumes on K + 3 where it stopped: every
later frame of the roll, of the frames that end it and of the stop comes
2 frames later than it would have without the defeat.

## How the roll goes on

### Without Action

A roll that defeats a walker during the roll always **carries on into
running**, whatever Action does and whatever its size, fast included.
With no boost (next section), and S, D the roll's own frames:

- S to D + 1: roll frames (the 29 frames plus the pause);
- D + 2, D + 3 and D + 4: still `roll`, as the frames D to D + 2 of a
  roll that carries on (`roll-input.md`);
- D + 5: `roll_end`. The speed on D + 5 still follows the roll; from
  D + 6 the ground rules of `ground-speed.md` apply.

The roll's size and direction don't change because of the defeat, and
steering works as in `roll-speed.md`.

A defeat on one of the frames that end a roll carrying on (D, D + 1 or
D + 2) works the same way: those frames last until D + 4 and `roll_end`
is on D + 5.

A defeat in the stop after a roll (D to D + 7) doesn't change the stop:
from K + 3 it goes on as in `roll-input.md`, 2 frames later than it
would have. Its speeds are the same, and with a direction held the
player first moves again on D + 20 instead of D + 18.

### The boost

Call **P** the first frame with Action held, from **K + 3** up to and
including the first frame after the roll or the stop:

- after a defeat during a roll: up to and including the frame that would
  be `roll_end`;
- after a defeat in the stop: up to and including D + 19.

Action may have been held since before the roll; a new press counts too.
On P a **new 29-frame roll** starts:

- P to P + 28 are roll frames, P + 29 to P + 31 are still `roll`, and
  `roll_end` is on P + 32. It always carries on into running, whatever
  Action does and whatever its size.
- **Size:** on P the size becomes the size the roll had on its own press
  frame S (slow 1, normal 3, fast 4, before any steering) **plus the
  number of boosts so far in this chain of rolls**. After one defeat:
  slow 1 → 2, normal 3 → 4, fast 4 → 5. After a second defeat and boost
  in the same chain, a roll that started normal is 5.
- **Direction** stays the roll's direction. Steering works as in
  `roll-speed.md` from P on, with P as the new roll's first frame: a
  direction opposite to the roll's direction, held on P or later,
  reverses it on that frame; the roll's own direction held from P + 1 on
  makes a size 1 or 2 roll a normal roll (size 3). The direction held on
  P itself doesn't do this. So a slow roll steered into a normal roll
  before the defeat drops to size 2 on P, and becomes size 3 again on
  P + 1 if the direction is still held.
- The speed rule of `roll-speed.md` applies toward the new target from P
  itself.

If P is in the stop, `roll_start` is on P. Otherwise the player is
already in `roll`, and there is no event on P; when P is the frame that
would have been `roll_end`, that `roll_end` doesn't happen.

There is one boost per defeat. Releasing Action after P and pressing it
again does nothing until another walker is defeated; once the roll and
its ending are over, the next roll is an ordinary one (`roll-input.md`).

If there is no frame P, the roll goes on as in "Without Action".

### More defeats

A walker defeated during a boosted roll is defeated by the same rules
and gives the same pause, timing and boost, with P counted from the new
defeat. Chains of two defeats were measured.

## Speed on roll frames

On every frame of a roll, of the frames that end it, and on its
`roll_end` frame, `vx` moves toward direction × size by the speed rule of
`roll-speed.md`, with the size as above. On K + 1 and K + 2 `vx` doesn't
change. In the stop, `vx` follows `roll-input.md`, 2 frames later.

## The walker

From K the walker moves as a walker bounced on (`walker.md`, "Being
bounced on") with B = K: on K it moves horizontally as usual but not
vertically and its `vy` becomes −6; on K + 1 and K + 2 it doesn't move;
from K + 3 its `vy` increases by 7/16 per frame, up to 8, and it moves
by its `vy` and by the `vx` it had on K. It doesn't land and is removed
once out of view. In the runs measured it last moved between K + 26 and
K + 42; when it is removed isn't specified.

A walker that is turning or speeding up can be defeated too, and the
player's pause, timing and boost are then the same. Its defeat range and
its motion after K aren't specified.

## Examples

Synthetic, from the rules above. The player walks right at +2 (`vx` 2)
and presses Action and Right on frame 0 (S = 0, a normal roll, D = 29);
Right stays held. A small walker heads left at full speed. `x` is
relative to the start of frame 0; I uses whole-pixel positions at the end
of the frame.

**Action held from frame 0:**

| Frame | Input | `x` | `vx` | State | Events | Notes |
|---|---|---|---|---|---|---|
| 10 | RA | 27.53515625 | 2.7578125 | roll | | I = −22 |
| 11 | RA | 30.3203125 | 2.78515625 | roll | | I = −18: outside −16 to 25 |
| 12 | RA | 33.12890625 | 2.80859375 | roll | | I = −14: K, the walker is defeated |
| 13 | RA | 33.12890625 | 2.80859375 | roll | | pause |
| 14 | RA | 33.12890625 | 2.80859375 | roll | | pause |
| 15 | RA | 36.0859375 | 2.95703125 | roll | | P = K + 3: size 4 |
| 16 | RA | 39.171875 | 3.0859375 | roll | | |
| 43 | RA | 140.6953125 | 3.9609375 | roll | | P + 28 |
| 46 | RA | 152.6015625 | 3.97265625 | roll | | P + 31 |
| 47 | RA | 156.6015625 | 4 | run | `roll_end` | P + 32: still the roll's speed rule |
| 48 | RA | 160.4765625 | 3.875 | run | | running (`ground-speed.md`) |

**Action on frame 0 only:** frames 0 to 14 as above (input `R` from
frame 1), then

| Frame | Input | `x` | `vx` | State | Events | Notes |
|---|---|---|---|---|---|---|
| 15 | R | 35.9609375 | 2.83203125 | roll | | no boost: size 3 |
| 30 | R | 79.8125 | 2.96484375 | roll | | D + 1: last of the roll frames |
| 31 | R | 82.78125 | 2.96875 | roll | | D + 2 |
| 33 | R | 88.75390625 | 3 | roll | | D + 4 |
| 34 | R | 91.75390625 | 3 | walk | `roll_end` | D + 5 |
| 35 | R | 94.62890625 | 2.875 | walk | | walking (`ground-speed.md`) |

Without the walker, the second roll would have ended in a stop with
`roll_end` on frame 29.

## In a trace

- The frames of a roll and of its ending, including K + 1 and K + 2,
  have state `roll`. On K + 1 and K + 2, `x` and `vx` repeat the values
  of K.
- A pause in the stop shows as rows with the stop's own state (`idle`
  when `vx` is 0) and no movement.
- No event in the trace vocabulary marks a defeat.
- `roll_start` and `roll_end` are as in "The boost".

## Not covered

- The player facing left into the large walker.
- Walkers that are turning, speeding up, stopped or frozen: their defeat
  range and their motion after the defeat.
- Walkers on a different floor from the player, and rolls on or off
  ledges, in the air, or jumping out of a roll (a Jump press on K + 3
  starts a jump; what happens next isn't specified).
- More than two defeats in one chain, and whether the size keeps growing.
- Being hurt in a stop that a defeat has lengthened.
- Whether other enemies and objects move during the pause, and whether a
  defeated walker can hurt the player while it falls.
- Other kinds of enemies, the Down + Action ground action meeting a
  walker (see `ground-action.md`), and the second character.
