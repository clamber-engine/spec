# Getting off the animal

Titles: `title-1`

While riding the animal (`riding.md`), a press of Tandem (`T`,
`controls.md`) makes the player get off. Usually the player **hops** up
off the animal and comes down on foot; with a Jump press that would start
a riding jump, the player **jumps off** instead, in an ordinary jump.
The animal stays where the rider left it, and the player can get on it
again. Riding is in `riding.md`, getting on from the crate in
`animal-crate.md`, and being knocked off by a walker in
`riding-walkers.md`.

Frames are logic frames at 60 Hz, numbered as in a trace. `x` and `vx`
are as in a trace: right is positive, `vx` in px per frame. Vertical
positions are **level y** as in `view-vertical.md`: they grow downward,
and `vy` is positive when moving down. "Left", "Right", "Up", "Down",
"Jump", "Action" and "Tandem" are the neutral inputs `L`, `R`, `U`, `D`,
`J`, `A`, `T` of the scenario format. A **press** of an input is a frame
on which it is held after a frame on which it was not. The parts of
riding (riding, turn, a jump's crouch, air and landing frames, charge,
charge end, settling frame), **steered** frames and the **remembered
press** are as in `riding.md`.

Speeds are always whole multiples of 1/256 px per frame. The rules below
are exact in those units, so this file writes speeds both ways
(512/256 = 2 px per frame).

Measured with the first character alone, by inputs only:

- On the long flat floor near the first level's start, with the first
  level's crate moved there in a modified copy of the game (as in
  `riding.md`):
  - 378 scripted runs from a rider at rest and from one moving right, each
    with one input placed around the landing after the hop: Jump or
    Action pressed on a frame from 12 frames before the landing to 2
    after it and held for different lengths, Down held for 1 or 6 frames
    from 3 frames before the landing to 3 after it, Action held with a
    direction, a direction added after the landing with Action held
    since before it, and presses on the 7th to 10th frames after the
    landing; and 80 jump-offs with Jump held 1 to 30 frames and nothing,
    Right, Left, or Left and Action held in the air;
  - 1 600 runs of random inputs from a rider at rest and from the first
    riding frame after a mount: random riding inputs (directions, Up,
    Down, Jump and Action, pressed and held for random lengths), Tandem
    pressed on a random frame and again later, and random inputs on foot
    after getting off, until 40 frames after the player was back in their
    own on-foot play or left the floor's flat part.

  In all, 1 978 runs with 1 844 hops (518 from riding, 64 from turns,
  128 from crouches, 369 from a jump's air and landing frames, 557 from
  charges, 208 from charge ends; 736 of them started in the air) and 114
  jump-offs. In every frame from the press until the frame the
  player's own on-foot play started (or, for a jump-off, until landing or
  mounting), `x`, `y`, `vx`, `vy`, `ground`, `facing`, and whether the
  player was getting off, were exactly as the rules below give: 58 193
  hop frames and 4 145 jump-off frames. The frame and kind of every start
  of on-foot play were too: 205 runs started on the landing frame; 402
  jumps, 221 rolls, 24 ground actions and 468 crouches on later landing
  frames; and on the 9th frame after landing, 226 times standing, 101
  walking, 124 turns, 18 runs, 15 jumps, 11 rolls and 16 crouches. The
  animal's position and facing matched on all 134 267 frames checked
  (707 presses in the air, 705 of whose animals were seen to land). 399 times the player got on the
  animal again (368 from jumps on foot, 31 from jump-offs), each on the
  frame the mounting rule gives; on the 13 493 other airborne frames of
  jumps moving down, the rule gave no mount, and on 10 029 frames the
  hop was within the mounting ranges and didn't mount.
- On the flat floor next to the first level's own crate, unmodified: 200
  more random runs (19 990 frames, 189 hops, 5 jump-offs, 57 mounts
  again), with the same result.

Each rule was also checked against its neighbours: moving any frame
window below by one frame, changing the hop's starting speed by 16/256
or either gravity by 8/256, letting facing follow the input on P
whether or not P is steered, or changing the animal's landing distance
by 1 px, makes runs differ.

## The press

A Tandem press on any frame of riding starts getting off on that frame:
call it **P**. "Any frame of riding" is every part listed in `riding.md`,
"Parts of riding": riding, a turn, a jump's crouch, air and landing
frames, a charge, a charge end and the settling frame, on the floor or
in the air. Tandem presses while mounting (`animal-crate.md`, M to
M + 22) did nothing. Holding Tandem doesn't repeat it: a new press is
needed.

What happens on P depends on Jump, Down and Action on P:

1. If a riding jump would start on P by the rule in `riding.md`
   ("Jumping": Jump pressed on P, or a remembered press; the rider on
   the floor at the end of P − 1; P not steered), the player **jumps
   off** ("The jump-off" below). The Jump press is taken first, as for
   a charge in `riding.md`.
2. Otherwise, with Down held and an Action press on P, and the rider on
   the floor at the end of P − 1 (the charge hop's take-off frame counts
   as on the floor), the player gets off into the ground action
   (`ground-action.md`); this case is not covered.
3. Otherwise the player **hops** off. An Action press on P doesn't start
   a charge.

So Tandem and Jump pressed together give a jump-off on the floor, and a
hop in the air or on a steered frame.

## The hop

**On P**:

- `vx` follows the riding speed rule for P (`riding.md`, "Horizontal
  speed": targets ±2, or ±3.5 with Action; a step of 1/8 of the gap if P
  is steered, 1/32 otherwise), and `x` changes by the new `vx`.
- If P is steered, facing becomes the direction held on P (Left or
  Right; none: unchanged). Otherwise facing doesn't change. No turn
  starts.
- `vy` becomes −6, then the frame's gravity (below) is added, and the
  player moves by the new `vy`: −5.5625, or −5.71875 with the lighter
  gravity. The player is in the air (`ground` 0) from P, even when the
  rider was on the floor.

**From P + 1 until landing**, the player is in the air on foot:

1. `vx` follows the air control rule of `air-control.md` (targets ±2,
   ±3 with Action, 0 with no direction or both; a step of 1/8 of the
   gap).
2. `vy` increases by the frame's gravity, never above 8.
3. The player moves by `vx` and `vy`, and lands as in `landing.md`.

The gravity is **0.28125** on P if at the end of P − 1 the rider was in a
jump with the lighter gravity of `riding.md` (Jump held on every frame
since its press frame; also during a charge started in that jump) and
Jump is held on P, and on each later frame as
long as Jump has been held on every frame since; otherwise it is
**0.4375**. Once Jump is released, it is 0.4375 until landing.

Facing doesn't change from P + 1 until on-foot play starts (below),
whatever is held. The player doesn't get on the animal again during the
hop, even when coming down onto it.

From the floor with the usual gravity, the player rises 38.1875 px,
highest on P + 12, and lands on **P + 25**. A hop started higher (from a
jump or a charge) lands later.

### Landing

Call the landing frame **L**. The frames L to L + 8 are **landing
frames**: the player is on the floor and in none of the modes of
`ground-speed.md` (standing, walking, running); facing doesn't change
whatever is held, and no 8-frame turn starts. The speed rule applies on
them as on the ground.

On-foot play starts on the first of these that applies:

- **On L**, with Action and exactly one of Left or Right held: running
  starts on L at step 1 (counter 256, `ground-speed.md`, "The stride
  cycle"), facing unchanged even when the direction held is the one
  behind the player. A turn then starts at the first turn check, as in
  `ground-speed.md`.
- **Jumps**: as after a bounce, with **E = L + 2** (`jump-input.md`,
  "After a bounce", and its table): a Jump press on L − 10 to L + 1, held
  on every frame from the press through L + 1, gives a jump with its
  take-off frame (`jump_start`) on L + 2; a press on L − 11 or earlier,
  or one released before L + 1, gives none; a press on L + 2 or later
  gives a jump whose take-off frame is the frame after the press.
- **Rolls**: as in `roll-input.md`, "In the air", with this L: Action
  pressed on L − 6 to L and held on every frame through L + 1 starts a
  roll on L + 1, even with Down held; an Action press on L + 1 or a later
  landing frame starts a roll on that frame, or, with Down held, the
  ground action (`ground-action.md`).
- **Crouching**: Down held on a frame from L + 2 on, with no Action press
  on that frame, starts a crouch on that frame (`ground-speed.md`,
  "Crouching": that frame is its D). Down held on L or L + 1 alone does
  nothing on those frames.
- **On L + 9**, if nothing has started: a mode starts, as on the landing
  frame after a bounce (`ground-speed.md`, "After a fall or a bounce"):
  - Action and either direction held: running at step 1, facing
    unchanged, even with the direction behind the player held, at any
    speed;
  - otherwise the direction behind the player held: a turn starts;
  - the direction the player faces held: walking at step 1;
  - no direction held: standing.

  Down held, or a Jump or Action press, on L + 9 starts a crouch, a jump
  or a roll as above instead.

From then on the player is on foot and the usual rules apply.

### Example

Riding right on flat floor at about +1.65, Right held, Tandem pressed on
P with Right still held, Right held to P + 20 and Left from P + 21. The
rider's last frame (P − 1) is at `x` 0, `y` 0.

| Frame | Held | `x` | `y` | `vx` (1/256) | `vy` | Facing | |
|---|---|---|---|---|---|---|---|
| P − 1 | Right | 0 | 0 | 422 | 0 | right | riding |
| P | Tandem, Right | 1.65625 | −5.5625 | 424 | −5.5625 | right | in the air; riding step (1/32) |
| P + 1 | Right | 3.35546875 | −10.6875 | 435 | −5.125 | right | air control (1/8) |
| P + 12 | Right | 23.640625 | −38.1875 | 491 | −0.3125 | right | highest |
| P + 21 | Left | 40.67578125 | −21.3125 | 376 | 3.625 | right | facing doesn't change |
| P + 24 | Left | 42.69140625 | −7.8125 | 83 | 4.9375 | right | |
| P + 25 (L) | Left | 42.7265625 | 0 | 9 | 0 | right | lands |
| P + 26 | Left | 42.5078125 | 0 | −56 | 0 | right | landing frame |
| P + 33 | Left | 36.125 | 0 | −330 | 0 | right | last landing frame |
| P + 34 (L + 9) | Left | 34.75 | 0 | −352 | 0 | right | a turn starts |
| P + 38 | Left | 28.5703125 | 0 | −417 | 0 | left | facing changes (turn's T + 4) |

The animal stays at `x` −0.3125 (the rider's `x` on P − 1, 0.3125 past a
whole pixel, rounded down), `y` 0.

## The jump-off

**On P**, the player moves as on a hop's P (above): the riding speed rule
for P, `vy` −6 plus the frame's gravity, in the air. P isn't steered, so
facing doesn't change. Jump is held on P, so the gravity is 0.28125 and
`vy` is −5.71875.

**P + 1 is the jump's take-off frame** (the frame carrying `jump_start`):
`vx` follows the air control rule, `vy` increases by the frame's gravity
and the player moves by both, as on any airborne frame; then `vy`
becomes **−7**. Facing follows the input from P + 1 as in a jump
(`air-control.md`, "Facing").

**From P + 2** the jump goes on as in `jump-arc.md` ("Each frame after
T"), with the Jump press (P, or the remembered press) as the jump's
press frame: the gravity is 0.28125 while Jump has been held on every
frame since that press, 0.4375 otherwise; landing is as in
`landing.md`. The player can get on the animal again during the jump
(below).

From a rider at rest, with Jump held for 1 frame and Right held from
P + 1, the player lands on P + 33; with Jump held for 30 frames, on
P + 47. With nothing held after P the player comes straight down onto
the animal and gets on it again (on P + 26 with Jump held 1 frame).

## The animal

On P the animal leaves the rider. It stays at `x` = the rider's `x` at
the end of P − 1, rounded down to a whole pixel, and faces the way the
rider faced at the end of P − 1. Its level y is the rider's `y` at the
end of P − 1, rounded up to a whole pixel (the rider's feet line, as in
`view-vertical.md`): the floor, if the rider was on it.

If that is above the floor, the animal falls: it moves down 112/256 px on
P + 1 and 113/256 px on P + 2, and on each later frame 112/256 px more
than on the frame before (225/256, 337/256, 449/256 …). It lands on the
first frame on which that move would leave it less than 5 px above the
floor, or at or below it: it is then placed on the floor. Falls of up to
20 frames were measured (moves of up to 2017/256 px per frame; no limit
on the speed was seen).

From then on the animal stands where it is, facing the same way. It didn't
move in any run (in one, for 934 frames after P), with nothing else on
the floor. Walking or rolling through it doesn't get the player on it.

## Getting on again

The player gets on the animal again by the rule of `animal-crate.md`,
"Mounting the animal", with the animal's position for (`xc`, `yc`): from
a jump on foot, or from a jump-off, moving down, with `I` and `D` in
range. From the mount on, everything there applies (the 22 frames
without movement, riding from M + 23).

The `I` ranges there are those of an animal facing right (the first
level's crate's animal faces right). For an animal facing left they are
turned around:

| Animal faces | Player faces | `I` |
|---|---|---|
| right | right | −27 to 19 |
| right | left | −26 to 20 |
| left | left | −19 to 27 |
| left | right | −20 to 26 |

`D` is 5 to 45 in every case. A hop never gets on the animal ("The hop").

## Not covered

- Tandem with Down held and an Action press on the floor: the player was
  seen to leave the animal in the ground action (`ground-action.md`),
  moving up as on a hop's P, 5 times; what happens then is not covered.
- A Tandem press on M + 23, the first riding frame after a mount, and
  Tandem held from the mount into riding.
- What follows the landing of a jump-off (its landing frames and when
  on-foot play starts, `jump-input.md` and `ground-speed.md`, "After a
  landing").
- Getting on again after a bounce off an enemy (`bounce.md`), or while
  the animal is still falling.
- Slopes, steep faces, ledges and walls: getting off near them, the
  animal falling past a ledge, and a hop landing on other ground.
- What the animal does when it leaves the view and comes back, enemies
  and other objects meeting it, and the second character.
- What happens after a knock-off (`riding-walkers.md`): whether the
  animal running off can be got on again.
- How the hop, the jump-off and the animal are drawn, and the sounds.
- Trace state names for getting off: `traces/VOCABULARY.md` has none for
  the hop yet.
