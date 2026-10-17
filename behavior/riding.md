# Riding the animal

Titles: `title-1`

After mounting the animal (`animal-crate.md`), the player **rides** it.
This file covers riding on flat floor: horizontal speed, turning,
jumping, and the **charge** that an Action press starts. Getting on is
in `animal-crate.md`; everything else about riding is listed under "Not
covered".

Frames are logic frames at 60 Hz, numbered as in a trace. `x` and `vx`
are as in a trace: right is positive, `vx` in px per frame. Vertical
positions are **level y** as in `view-vertical.md`: they grow downward,
and `vy` is positive when moving down. "Left", "Right", "Jump", "Action",
"Up" and "Down" are the neutral inputs `L`, `R`, `J`, `A`, `U`, `D` of
the scenario format. A **press** of an input is a frame on which it is
held after a frame on which it was not.

Speeds are always whole multiples of 1/256 px per frame. The rules below
are exact in those units, so this file writes speeds both ways
(512/256 = 2 px per frame).

Measured with the first character alone, by inputs only:

- On a long stretch of flat floor near the first level's start, with the
  first level's crate moved there in a modified copy of the game (the
  level has no long flat floor next to its own crate), and away from the
  ends of that floor: scripted runs for each rule, and 453 runs of random
  inputs (directions, Up, Down, Jump and Action, pressed and held for
  random lengths, with Left and Right never held together), 586 426
  frames. In every frame of every run, `x`, `y`, `vx`, `vy`, `ground`,
  `facing` and which of the parts below the rider was in were exactly as
  the rules give. The runs held 8 217 jumps (972 of them from a press
  made before the jump could start), 9 193 charges (4 847 started in the
  air during a jump), 4 094 hops and 3 379 turns.
- On the flat floor next to the first level's own crate, unmodified: 40
  more random runs, 24 493 frames, with the same result.
- 40 of the 453 runs start on the first riding frame after a mount
  (M + 23 in `animal-crate.md`): riding starts with the speed the player
  had at the mount, and a Jump press on M + 23 doesn't start a jump
  (M + 22 ended in the air).

## Parts of riding

At any frame the rider is in one of these parts:

- **riding**: on the floor, under control;
- a **turn** (8 frames);
- a **jump**: 5 **crouch** frames on the floor, then the air, then 8
  **landing frames** on the floor;
- a **charge**, then its **charge end** (12 frames), then one
  **settling frame**, which is riding with the exceptions given below.

## Horizontal speed

On every frame (in every part above) the rider has a **target speed**
set by what is held on that frame:

| Held | Target |
|---|---|
| Right | +2 (+512/256) |
| Left | −2 (−512/256) |
| Right and Action | +3.5 (+896/256) |
| Left and Action | −3.5 (−896/256) |
| No direction | 0, with or without Action |

Up, Down and Jump make no difference.

Each frame, using speeds in 1/256 px per frame:

1. **gap** = target − current speed.
2. **step** = gap ÷ **N**, rounded toward zero to a whole number, where
   N is **8** on a **steered** frame (next section) and **32** on every
   other frame.
3. If step is 0, the speed becomes the target. Otherwise the speed
   changes by step.
4. The rider moves by the new speed: `x` changes by exactly the new `vx`
   on that frame.

This is the speed rule of `ground-speed.md` with other targets and, on
frames that aren't steered, a step of a 32nd of the gap instead of an
8th. So with N = 32 the speed snaps to the target once the gap is less
than 32/256 either way. There is no separate braking or turning rate.

## Steered frames

A jump makes the frames after it **steered** for a while. A frame is
steered when steering was on at the end of the frame before it.

- Steering comes on at the end of a jump's press frame (P below), so
  P + 1 is the first steered frame.
- It goes off at the end of the jump's landing frame: the landing frame
  is still steered, the frame after it isn't.
- If a charge starts during the jump (from its crouch or its air
  frames), steering stays on through the charge and its end, and goes
  off at the end of the settling frame: the settling frame is still
  steered.

On a steered frame:

- the speed step is gap ÷ 8;
- facing is the direction held on that frame (Left or Right); with no
  direction held it stays as it was. No turn starts;
- Jump presses don't start a jump (next sections).

On frames that aren't steered, facing changes only through turns.

## Riding, and turns

While riding, `y` is the floor's level y and `vy` is 0.

A **turn** starts on any riding frame on which the direction opposite
to facing is held, at any speed: call it T. The turn lasts 8 frames, T
to T + 7. Facing changes on **T + 4**. On T + 8 the rider is riding again,
and a new turn starts on T + 8 if the opposite direction is held then.
Holding the opposite direction for one frame is enough: the turn
completes even if nothing is held after T. The turn has no effect on
speed.

Turns don't start during a jump (crouch, air or landing frames), a
charge or a charge end. A jump or a charge started during a turn ends the
turn; facing keeps whatever value it had (it has changed only if T + 4
has passed).

Up and Down do nothing while riding.

## Jumping

A jump **starts** on frame P when all of these hold:

1. Jump is held on P, and either it is pressed on P, or it was pressed at
   most 11 frames before P (on P − 11 or later) and held on every frame
   since (a **remembered press**);
2. at the end of frame P − 1 the rider was on the floor (the hop's
   take-off frame, under "The charge", counts as on the floor);
3. P is not a steered frame.

So a jump can start while riding, during a turn, on the landing frames
of an earlier jump from the frame after its landing frame, during a
charge while on the floor, on the frame right after the hop's take-off
frame, and during a charge end and on the settling frame (unless a jump
made them steered). A press made in the air can't start a jump there,
but if it is still held, it starts one on the first frame these rules
allow, provided that frame is at most 11 frames after the press.
Example: a jump lands on frame L. Jump pressed on L − 10 and held starts
a jump on L + 1; pressed on L − 11 and held, no jump starts. Holding
Jump since the jump that took off never starts another.

Frames of the jump:

- **P to P + 4 (crouch)**: the rider stays on the floor; only `x` moves.
  Facing doesn't change on P; from P + 1 the frames are steered.
- **T = P + 5 (take-off frame)**: the rider hasn't moved up yet and is
  still on the floor, but `vy` is already **−7**.
- **From T + 1 until landing**: `vy` increases by the frame's gravity, but
  never above 8, and the rider moves by the new `vy`. The gravity is
  **0.28125** if Jump has been held on every frame from P (the press frame
  of a remembered press) up to and including this frame, and **0.4375**
  otherwise, as in `jump-arc.md` with this file's P. Landing is as in
  `landing.md`: the rider lands on the first frame on which they are
  moving down and end less than 5 px above the floor, or at or below it,
  and is placed on the floor.

So the vertical part of a riding jump is the vertical part of an
ordinary jump (`jump-arc.md`) started 4 frames later. Holding Jump on
P + 1 to P + 4 doesn't change the jump: the lighter gravity first
matters on T + 1.

- **Landing frames L to L + 7**: the rider is on the floor. L is still
  steered; L + 1 to L + 7 are not, but no turn starts on them. A jump
  can start from L + 1 (rule above), and a charge from L. On L + 8 the
  rider is riding again.

A jump that starts on the frame right after the hop's take-off frame
starts in the air: its crouch frames fall under the jump's gravity like
air frames, and on T `vy` is set to −7 in the air.

## The charge

An **Action press** starts a charge on that frame, call it C, when the
rider is

- riding (but not on the settling frame), turning, or on a jump's
  landing frames, or
- in a jump's crouch or air frames, including its landing frame. The
  charge keeps the jump's steering (see "Steered frames").

Action presses during a charge, a charge end or on the settling frame
do nothing.

When Jump and Action are pressed on the same frame, the Jump press is
taken first: if it starts a jump, the Action press then starts a charge
from that jump's crouch, on the same frame. So pressing both together
while riding gives a steered charge.

Frames of the charge:

- From C on, the speed rule applies on every frame as above, steered or
  not. Facing doesn't change during a charge that isn't steered.
- While the rider is on the floor, `y` doesn't change, until:
- **C + 10 (hop take-off)**: if the rider is on the floor at the end of
  this frame's vertical motion (including landing on this frame), `vy`
  becomes **−4**; the rider hasn't moved up yet and is still on the
  floor.
- **After the hop**: `vy` increases by the frame's gravity, never above
  8, and the rider moves by the new `vy`, landing as in `landing.md`. The
  gravity is 0.4375, except that a charge started from a jump uses the
  jump's rule: 0.28125 while Jump has been held on every frame since the
  jump's press frame P.
- A charge that starts in the air keeps falling under the same gravity
  rule. If it is still in the air on C + 10, there is no hop. If it lands
  before C + 10, it hops on C + 10.
- A Jump press can start a jump during the charge (rules under
  "Jumping"); the charge then ends.

The **charge end** begins on the first frame from C + 28 on that ends
with the rider on the floor: on C + 28 itself if the rider is on the
floor then, otherwise on the frame the rider lands. It lasts 12 frames,
E to E + 11, on the floor. Frame **E + 12** is the **settling frame**:
the rider is riding again, a turn can start on it, and an Action press
on it does nothing (unless a Jump press starts a jump on it first, as
above).

For a charge started while riding on flat floor, with Jump not held:
the hop leaves the floor on C + 11 and lands on C + 26, the charge end
is C + 28 to C + 39, and C + 40 is the settling frame. The hop's
highest point is 16.3125 px above the floor, reached on C + 19.

## Examples

All values were measured on flat floor, with positions made relative.

From standing still, Right held from frame 0 (facing right):

| Frame | `vx` (1/256) | `x` |
|---|---|---|
| 0 | 16 | 0.0625 |
| 1 | 31 | 0.18359375 |
| 2 | 46 | 0.36328125 |
| 3 | 60 | 0.59765625 |
| 105 | 512 | 147.5546875 |

Speed 2 is first reached on frame 105, after 147.5546875 px. Releasing
everything at speed 2 (frame 0 the first frame with nothing held) gives
`vx` 496, 481, 466, 452 … (in 1/256), and `vx` is 0 from frame 105 on,
after 64.4453125 px. From standing still with
Right and Action held, `vx` is 28, 55, 81, 106 … and reaches 3.5 on
frame 123.

Turning at speed: riding right at `vx` 456/256 with Right held, then Left
held from frame 0. `vx` is 426, 397, 369, 342 on frames 0 to 3 (the
turn has no effect on speed); facing becomes left on frame 4; frame 8 is
riding again.

A riding jump while moving left at `vx` −422/256, Left held, Jump pressed
on frame 0 only:

| Frame | Part | `vx` (1/256) | `y` | `vy` |
|---|---|---|---|---|
| 0 | crouch (P) | −424 | 0 | 0 |
| 1 | crouch, steered | −435 | 0 | 0 |
| 2 | crouch | −444 | 0 | 0 |
| 4 | crouch | −459 | 0 | 0 |
| 5 | take-off (T) | −465 | 0 | −7 |
| 6 | air | −470 | −6.5625 | −6.5625 |
| 21 | air | −503 | −52.5 | 0 |
| 36 | landing frame (L) | −512 | 0 | 0 |
| 44 | riding | −512 | 0 | 0 |

On frame 0 the step is a 32nd of the gap (−90/32 → −2); from frame 1 an
8th (−88/8 = −11). On frame 24 the gap is −7, so the speed becomes −512.
Frame 35 ends 6.5625 px above the floor; frame 36 would end exactly on
it, so the rider lands.

A charge from standing still, Action pressed on frame 0, Right held from
frame 0 (facing left, so facing stays left through the charge):

| Frame | Part | `vx` (1/256) | `y` | `vy` |
|---|---|---|---|---|
| 0 | charge (C) | 28 | 0 | 0 |
| 1 | charge | 43 | 0 | 0 |
| 10 | hop take-off | 155 | 0 | −4 |
| 11 | charge, air | 166 | −3.5625 | −3.5625 |
| 26 | charge, landed | 292 | 0 | 0 |
| 28 | charge end (E) | 304 | 0 | 0 |
| 39 | charge end | 361 | 0 | 0 |
| 40 | settling frame; a turn starts | 365 | 0 | 0 |

Frame 0's target is +3.5 (Action held); from frame 1 it is +2. A turn
starts on frame 40, because Right is held and the rider faces left.

## Not covered

- Riding on slopes, at walls, and off ledges; near the left end of the
  measured floor (by a wall) the rider's floor was 1 px higher than the
  floor under the rider, which is not explained.
- What the animal does to enemies, what happens when the rider is hurt,
  and getting off the animal (a Tandem press, `controls.md`).
- Objects: carrying, the crate, bonuses and anything touched while
  riding.
- Other animals, and riding with the second character present.
- How the rider and the animal are drawn (poses, the animal's gait).
- Trace state names for riding: `traces/VOCABULARY.md` has no names for
  the crouch, the charge or the charge end yet.
