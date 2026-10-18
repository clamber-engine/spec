# Rolling into walkers

Titles: `title-1`, `title-2`

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

### Everything else during the pause

On K + 1 and K + 2 no other object moves either, as on the frozen frames
after a bounce (`bounce.md`, "Everything else on the frozen frames").
Every other object keeps the position, speeds and facing it had at the
end of K: other walkers of every size, critters (`critter.md`), the
pieces of a broken object, extra-life items that are moving
(`extra-lives.md`) and the following character (`following.md`,
"Pauses"). Nothing is made up afterwards: an object moving at a steady
speed takes its next step on K + 3, so it ends up two frames behind.

No object's picture changes on K + 1 or K + 2 either, the player's
included: an object whose picture changes on K shows its next change on
K + 3 at the earliest. The view is in `view.md` ("Frames on which the
view stays still").

Measured on the first level with the first character leading and the
second following or absent: 280 runs of random play with frequent
Action presses (about 3 540 000 logic frames, the lead kept from being
hurt), with 147 roll defeats. On both pause frames, none of the 404
objects that moved on K or on K + 3 moved or changed facing (147
defeated walkers, 100 other small walkers, 25 other medium walkers, 18
other large walkers, 31 critters, 11 pieces, 29 extra-life items and 43
following characters), and the 136 of them other than the following
character and the defeated walkers that moved at a steady speed before K
took the same step on K + 3 as on K. None of the 386 objects whose
picture changed on K or on K + 3 changed it on K + 1 or K + 2. Scripted
rolls into a small walker (116 defeats) and into two large walkers (147
defeats) gave the same, with the player's picture also unchanged on
both frames. Walkers defeated by a thrown or rolling object rather than
by the roll are left out here: there is no pause then.

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
  number of boosts so far in this chain of rolls**, but never more than
  **8**. After one defeat: slow 1 → 2, normal 3 → 4, fast 4 → 5. After a
  second defeat and boost in the same chain, a roll that started normal
  is 5; after the fifth it is 8, and it stays 8 after the sixth and
  later ones. A roll that started fast is 8 from the fourth boost on.
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
defeat. Chains of up to six defeats were measured, and none of these
rules changed along a chain except that the size stops growing at 8
(see "Size" above). Each boost follows "The
boost" above, with its own P to P + 32; the chain ends once a roll and
its ending are over without another defeat.

Measured on the first level's opening floor with up to six small
walkers in a row heading left (walkers added to the level, 22 to 29
pixels apart), the first character leading: 20 000 runs of random input
(walking or running first, Action held, released and pressed again at
random, Right mostly held with short Left presses, occasional Down and
Jump presses), about 4 560 000 logic frames. In the first 15 000 runs,
4 070 rolls defeated at least one walker, with 18 863 defeats; 2 614
chains had six defeats, and in 1 839 chains the starting size plus the
boosts came to 9 or 10, so the limit applied. Every frame of every chain
(defeats, pauses, roll, ending and stop frames, speeds, the defeated
walkers' motion and hurts) matched the rules of this page with the limit
of 8: 0 runs different. The same rules with no limit, or a limit of 7,
differ in 1 839 and 2 058 runs. The last 5 000 runs were
recorded after the rules were fixed, as a holdout: 1 362 rolls with a
defeat, 6 299 defeats, 870 chains of six, the limit applying in 623
chains, 0 runs different.

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
when it leaves the window, from K + 3 on, as in `walker-view.md`
("Defeated walkers falling out"). In the runs measured it was there for
the last time between K + 26 and K + 42.

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

## In `title-2`

Everything in this section was measured on `title-2` only, with the lead
character alone, on flat floor, on the walkers of the first level
(`walker.md`, "In `title-2`"), each heading left at full speed when met.
When a roll starts, how long it lasts and how it ends are in
`roll-input.md` ("In `title-2`"), and its speed in `roll-speed.md` ("In
`title-2`"). This section uses their names: **S** is a roll's first frame,
its 31 **roll frames** are S to S + 30, and **D** is the frame after them.
**K** is the frame on which a walker is defeated. The offset **I** and
the poses are as in `walker-contact.md` ("In `title-2`"): I is the
player's `x` at the end of the frame minus the walker's `x` at the end of
the frame before, both rounded down to whole pixels.

### Attack frames (`title-2`)

A walker can be defeated only on a **roll frame**, S included. These are
not attack frames:

- D, whether the roll ends in a stop or carries on into running, and the
  whole stop;
- the two frozen frames after a defeat (below);
- the frame G on which a Jump press ends a roll in a jump, and the rest
  of that jump.

### When a walker is defeated (`title-2`)

On a roll frame K, a walker is defeated on K when I is within the defeat
range (both ends included):

| Player facing (at the end of K) | I |
|---|---|
| right | −25 to 27 |
| left | −20 to 32 |

The range facing left is the range facing right moved 5 px to the right.
It is the same on every roll frame, for slow, normal and fast rolls and
for the faster rolls after a defeat (below), at every speed.

Contact on a roll frame never hurts the player.

### Being hurt around a roll (`title-2`)

- **Before the roll.** The frame before S is not special: the hurt rule
  of `walker-contact.md` applies with the pose the player has then. S is
  a roll frame, so a walker in the defeat range on S is defeated. Unlike
  `title-1`, there is no hurt on S.
- **The stop.** On D to D + 13 a walker hurts the player when I is within
  the defeat range above (it isn't defeated). On D + 14 the standing range
  of `walker-contact.md` applies, and the usual rules from D + 15.
- **A roll that carries on into running**, with or without a boost
  (below). From D the hurt rule of `walker-contact.md` applies with the
  walking or running ranges.
- **A jump out of a roll.** From G the hurt and bounce rules of
  `walker-contact.md` apply with the "in the air" ranges.

### The pause (`title-2`)

On **K** the player moves as on any roll frame, with the roll's speed
before the defeat.

On **K + 1** and **K + 2** the player doesn't move: `x`, `vx` and facing
stay as they were at the end of K. Every walker, the defeated one and any
other, also doesn't move on these two frames. Inputs on K + 1 and K + 2
are lost: a direction held only on these frames steers nothing, and a
Jump or Action press made on them does nothing, even if the button is
still held on K + 3 (it isn't a new press then). A direction still held
on K + 3 counts from K + 3, as on any roll frame.

### How the roll goes on (`title-2`)

What happens from K + 3 depends only on whether Action is held on K
itself (held since before, or pressed on K).

**Action not held on K.** The roll goes on where it stopped: its 31 roll
frames are counted without K + 1 and K + 2, so D = S + 33 after one
defeat. Since Action
wasn't held on every frame of the roll, the roll ends in a stop, D to
D + 14, as in `roll-input.md` ("In `title-2`"), 2 frames later than
without the defeat. Size and steering are unchanged.

**Action held on K (the boost).**

- The roll's size grows by **1** (256/256): slow 1.125 becomes 2.125
  (544/256), normal 3.375 becomes 4.375 (1120/256), fast 4.5 becomes 5.5
  (1408/256). The speed moves toward the new target from K + 3 (on K it
  still follows the old one).
- A **new run of 31 roll frames** starts on K + 3: K + 3 to K + 33, and
  D = K + 34.
- The roll always **carries on into running** on D, whatever Action does
  after K and whatever the size: D still moves at the roll's speed and
  from D + 1 the player walks or runs normally (`roll-input.md`).
- Steering works on every roll frame from K + 3 on, K + 3 included,
  as in `roll-speed.md` ("In `title-2`").

A walker met during the new roll frames is defeated by the same rules,
with the same pause; with Action held on that K, the size grows by 1
again (normal 3.375 → 4.375 → 5.375, fast 4.5 → 5.5 → 6.5) and another
31 roll frames start. Chains of two defeats were measured.

A Jump press on a roll frame from K + 3 on ends the roll in a jump as in
`roll-speed.md` ("Jumping out of a roll"), with the roll's size at that
time.

### The walker (`title-2`)

From K the defeated walker moves as a walker bounced on
(`walker.md`, "Being bounced on (`title-2`)") with B = K: on K it
doesn't move, its `vx` becomes −1 if the player faces right at the end
of K and +1 if the player faces left, and its `vy` becomes −8; it
doesn't move on K + 1 and K + 2; from K + 3 it moves by `vx` and `vy`,
then `vy` increases by 0.4375, up to 12. It also doesn't move on the two
frozen frames of a later defeat in the same chain. When it is removed
isn't specified.

### Examples (`title-2`)

Synthetic, from the rules above. The player stands facing right at `x`
100 and presses Action and Right on frame 0 (S = 0, a normal roll);
Right stays held. A walker heads left at full speed and is at `x`
160.875 at the end of frame −1. I uses the walker's position at the end
of the frame before.

**Action on frame 0 only:**

| Frame | Input | `x` | `vx` | State | Events | Notes |
|---|---|---|---|---|---|---|
| 0 | RA | 100.421875 | 0.421875 | roll | `roll_start` | |
| 11 | R | 121.5234375 | 2.6796875 | roll | | I = 121 − 149 = −28: no defeat |
| 12 | R | 124.2890625 | 2.765625 | roll | | I = 124 − 148 = −24: K |
| 13 | R | 124.2890625 | 2.765625 | roll | | pause |
| 14 | R | 124.2890625 | 2.765625 | roll | | pause |
| 15 | R | 127.12890625 | 2.83984375 | roll | | size 3.375 |
| 32 | R | 181.02734375 | 3.30859375 | roll | | the 31st roll frame |
| 33 | R | 184.34375 | 0 | idle | `roll_end` | D = S + 33: the stop |

**Action held from frame 0:** frames 0 to 14 as above (input `RA`), then

| Frame | Input | `x` | `vx` | State | Events | Notes |
|---|---|---|---|---|---|---|
| 15 | RA | 127.25390625 | 2.96484375 | roll | | size 4.375 |
| 16 | RA | 130.39453125 | 3.140625 | roll | | |
| 45 | RA | 248.4921875 | 4.3359375 | roll | | K + 33 |
| 46 | RA | 252.83203125 | 4.33984375 | run | `roll_end` | D = K + 34: carries on |

### In a trace (`title-2`)

- Every frame from S to the frame before D has state `roll`, including
  K + 1 and K + 2 and the roll frames after a boost. On K + 1 and K + 2,
  `x` and `vx` repeat the values of K.
- `roll_start` is only on S; D carries `roll_end`. No event marks a
  defeat or a boost.

### Evidence (`title-2`)

23 478 placements of a walker at chosen offsets from the player, one
pixel apart, on every frame of slow, normal and fast rolls both ways, of
the stop, of a roll carrying on into running, before a roll, and on the
last frames and the D of a boosted roll: 8 586 defeats and 5 226 hurts,
all as this section says, with no gaps in any range. By inputs, 700 runs (94 277 frames) with random approach
distances, Action holds, releases and presses, steering, Jump and Down
presses, both facings, and a second walker for chains (made by copying
the first one; it walked and was defeated like a natural one): 389
defeats (84 facing left), 144 boosts (13 of them a second boost in the
same chain), 11 317 attack frames, 778 frozen frames, 1 696 stop frames
and 8 031 frames of defeated walkers, with 0 mismatches. 400 of the runs
were recorded after the rules were fixed, as a holdout.

### Not covered in `title-2`

- Walkers heading right, walkers not at full speed, and walkers on a
  different floor from the player; rolls on slopes, off ledges or in the
  air.
- Two walkers in range on the same frame, and chains of more than two
  defeats.
- Whether a defeated walker can hurt the player while it falls, and when
  it is removed.
- Other kinds of enemies, objects other than walkers during the pause,
  and the second character.

## Not covered

This list is for `title-1`; `title-2` has its own list above.

- The player facing left into the large walker.
- Walkers that are turning, speeding up, stopped or frozen: their defeat
  range and their motion after the defeat.
- Walkers on a different floor from the player, and rolls on or off
  ledges, in the air, or jumping out of a roll (a Jump press on K + 3
  starts a jump; what happens next isn't specified).
- A roll that started slow reaching the limit (seven boosts); chains into
  walkers heading right, and into medium or large walkers, longer than
  two.
- Being hurt in a stop that a defeat has lengthened.
- Whether timed behaviour that doesn't show in an object's position,
  speeds, facing or picture (a walker's turn, the thrower's timetable) is
  also held during the pause, and objects other than those listed in
  "Everything else during the pause". Whether a defeated walker can hurt
  the player while it falls.
- Other kinds of enemies and the second character. Rolling into the
  thrower is in `thrower-contact.md`; the Down + Action ground action
  meeting a walker is in `ground-action-enemies.md`.
