# Riding into walkers

Titles: `title-1`

What happens between a walker (`walker.md`) and the player riding the
animal (`riding.md`): the animal **attacks** a walker in front of it and
**throws** it, and a walker that reaches the rider **knocks the rider
off**. Riding itself (speed, turns, jumps, the charge) is in
`riding.md`; getting on is in `animal-crate.md`.

Frames are logic frames at 60 Hz, numbered as in a trace. `x` and `vx`
are as in a trace: right is positive, `vx` in px per frame. Vertical
positions are **level y** as in `view-vertical.md`: they grow downward,
and `vy` is positive when moving down. "Left", "Right", "Jump" and
"Action" are the neutral inputs `L`, `R`, `J`, `A` of the scenario
format. Speeds are whole multiples of 1/256 px per frame; this file
writes them both ways where it helps (1536/256 = 6 px per frame).

The parts of riding (riding, turn, a jump's crouch, take-off, air and
landing frames, charge, charge end, settling frame), the charge frame
**C**, "steered" and the jump's lighter gravity are as in `riding.md`.

Measured with the first character alone, on flat floor, in a modified
copy of the game (the first level's crate moved onto the long flat floor
near the level's start, where one walker of the chosen kind was placed;
the level has no long flat floor next to its own crate), by inputs and
by placing the walker at chosen offsets from the rider, one pixel apart:

- for each kind of walker below, all four combinations of rider facing
  and walker facing, rider standing, moving, jumping and falling;
- random inputs while a walker of that kind walked on the same floor: 480
  runs with small walkers (98 712 frames), 200 with medium walkers
  (36 084 frames) and 200 with large walkers (34 255 frames). In every
  frame of every run, the rider's `x`, `y`, `vx`, `vy`, `ground`,
  `facing` and part of riding, and whether the walker was thrown, were
  exactly as the rules below give, through 552 automatic charges, 590
  throws and 161 knock-offs (each followed through the frozen frames and
  the flight until the run ended or the player touched a walker). The
  thrown walkers' motion was checked on every frame until they
  disappeared.

Measured with the second character as the player (leading, the first
character following), in the same kind of modified copy (a setup method
was used), for the small and medium walkers only: with the walker
standing at offsets one pixel apart from the rider, for all four combinations of rider facing and
walker facing with the rider at rest; and in 120 runs of random inputs
with each kind (44 826 frames with small walkers, 44 670 with medium
walkers). The attack and knock-off ranges came out exactly as in the
tables below, and in every frame of every random run the rider's `x`,
`y`, `vx`, `vy`, `ground`, `facing` and part of riding, and whether the
walker was thrown, were as the rules below give, with the second
character's riding targets (`riding.md`) and its air control and
gravity after a knock-off (`second-character.md`), through 98 automatic
charges, 98 throws and 42 knock-offs. The large walker was not measured with the
second character, and the attack and knock-off heights only through those
random runs.

## Kinds of walker

Three kinds of walker were measured. They walk the same way
(`walker.md`) but differ in size:

- the **small walker** (as in `walker-contact.md`),
- the **medium walker** (as in `walker-contact.md`): 13 px taller than
  the small walker, with different horizontal ranges,
- the **large walker** (as in `walker-contact.md`): 26 px taller than the
  small walker, and 3 px wider on each side.

How an importer tells the kinds apart is in `../formats/placements.md`
("Kinds").

## Offsets

As in `walker-contact.md`, round positions down to whole pixels (`x`
toward the left, the vertical position toward the floor) and let

- **I** = the rider's `x` minus the walker's `x`;
- **U** = how many pixels the rider's position is above the walker's (0
  when both are on the same flat floor).

The walker's **facing** is its facing in `walker.md`: the way it walks,
changing on T + 4 when it turns. A walker that has just appeared faces
the way it walks.

All ranges below are written for a rider **facing left**. For a rider
facing right, use the mirror image: the range for the **opposite** walker
facing, with both ends negated and swapped. For example, a small walker
facing left attacks from I = −24 to 55 when the rider faces left, so a
small walker facing right attacks from −55 to 24 when the rider faces
right.

## The attack

On a frame on which, using the rider's position **after** its own
motion on that frame and the walker's position at the **end of the
previous frame**,

1. I is within the attack range (table), and
2. U is at most the attack height (table),

the walker is **in front of the animal**. (Walkers higher than the rider
weren't covered on flat floor; with a small walker placed in the air,
up to 45 px above the rider still counted.)

Attack ranges for I, rider facing left (both ends included), and attack
heights:

| Walker | Walker facing left | Walker facing right | Attack height |
|---|---|---|---|
| small | −24 to 55 | −23 to 56 | 25 |
| medium | −31 to 53 | −21 to 63 | 38 |
| large | −27 to 58 | −26 to 59 | 51 |

### The automatic charge

If a walker is in front of the animal on a frame **D** on which the rider
is not in a charge (riding, turning, in any frame of a jump, in a charge
end or on the settling frame), the rider starts a charge without any
input, ending whatever part it was in (a turn stops where it is; a
jump's crouch doesn't take off, as with a pressed charge). D is already
a charge frame (the trace shows the charge from D), but the charge
counts from the next frame, **C = D + 1**. From then on
it is a charge as in `riding.md` (the hop on C + 10, the charge end from
C + 28, the same speed and gravity rules). A charge started this way
during a jump keeps the jump's steering and lighter gravity, as a
pressed charge does.

### The throw

On every frame from **C + 2** of a charge, pressed or automatic, a walker
that is in front of the animal on that frame is **thrown**:

- The rider: `vy` becomes **−6** (upward), replacing whatever it was. The
  rider has already moved on this frame, so the new `vy` first moves the
  rider on the next frame (with the charge's gravity, `riding.md`). If
  the rider was on the floor, it still counts as on the floor at the end
  of this frame, and the next frame counts as on the floor for starting
  a jump (as the frame after the hop does in `riding.md`).
- A small or medium walker: its `vx` becomes **twice the rider's `vx`**
  on this frame, its `vy` becomes **−6**, and it moves horizontally by
  the new `vx` on this frame (its vertical position doesn't change on
  this frame). On each later frame its `vy` increases by **0.4375**
  (never above 8), it moves by `vx` and `vy`, and it falls through the
  floor and disappears; it no longer touches anything.
- A large walker: `vy` as for the small walker. Its horizontal motion
  after the throw isn't covered (its speed is often, but not always, 1.5
  times the rider's on the throw frame, and changes in the air).

A walker that is in front of the animal on C or C + 1 of a pressed charge
isn't thrown on those frames, and one that leaves the attack range
before C + 2 isn't thrown at all. Throws happen in the air as well
(the rider falling back into range, for example), with the same rules.

## Being knocked off

The rider is **knocked off** on frame **H** when, with the positions at
the **end of H − 1** (the rider and the walker both after their own
motion on H − 1):

1. the walker is on the floor the rider rides on;
2. I is within the knock-off range (table), for the walker's facing at
   the end of H − 1; and
3. either the rider was on the floor at the end of **both** H − 2 and
   H − 1 (the take-off frame of a jump still counts as on the floor; a
   rider who landed on H − 1 was not on the floor at the end of H − 2),
   or U is at most the knock-off height (table).

Knock-off ranges for I, rider facing left (both ends included), and
knock-off heights:

| Walker | Walker facing left | Walker facing right | Knock-off height |
|---|---|---|---|
| small | −26 to 18 | −25 to 19 | −3 |
| medium | −33 to 16 | −23 to 26 | 10 |
| large | −29 to 21 | −28 to 22 | 23 |

So on flat floor a small walker only knocks off a rider who is on the
floor, while medium and large walkers also reach a rider low in the air,
and on the landing frame.

A knock-off is decided before the attack: if both happen, the rider is
knocked off. Walkers right next to the rider at the end of a frame
therefore knock it off even though they are also in front of the animal;
a walker approaching from the front is thrown before it gets that close
(for a rider at rest, a small walker walking toward the animal enters the
attack range at I = 56 and is thrown at I = 53).

### The knock-off

On **H**:

- the rider doesn't move; `vx` keeps its value, `vy` becomes **−6**, and
  facing doesn't change;
- the animal leaves the rider (below).

Frames **H + 1 to H + 15** are frozen: nothing in the game moves (the
player, the animal, walkers and all other objects), and input has no
effect (except that Jump has to stay held for the lighter gravity
below). On H itself the player and the animal don't move either, but
walkers and other objects make their usual move.

From **H + 16** the player is on foot and in the air, until landing:

1. `vx` follows the air control rule of `air-control.md` (targets ±2,
   ±3 with Action, 0 with no direction; step 1/8 of the gap).
2. `vy` increases by **0.4375**, never above 8; or by **0.28125** if at
   the end of H − 1 the rider was in a jump with the lighter gravity of
   `riding.md` (Jump held on every frame since its press frame) and Jump
   has been held on every frame since.
3. The player moves by `vx` and `vy`, and lands as in `landing.md`.

Facing doesn't change from H until the player lands. From a knock-off on
flat floor with the usual gravity, the player rises 38.1875 px (highest
on H + 28) and lands on H + 41, on foot. A walker under the falling
player gives a bounce (`bounce.md`), as for a player on foot.

### The animal

On H the animal's `vx` becomes **6** in the direction the rider faced
(+6 facing right, −6 facing left). It is frozen with everything else
until H + 15, moves 6 px per frame on H + 16 to H + 20, and from H + 21
its speed moves toward **4** in the same direction by one eighth of the
gap each frame, rounded toward zero (becoming 4 when the step would be
0): 1472, 1416, 1367, 1325 … (in 1/256). What it does at walls and
whether the player can get back on are not covered.

## Walkers behind the rider

The ranges above are offsets from the rider's `x`, and they reach behind
the rider as well as in front of it. For a rider facing left, a negative
I is a walker to the right of the rider, that is, behind it. A walker
behind the rider is therefore "in front of the animal" (and is thrown,
below) when it is at most this many pixels behind the rider's `x`:

| Walker | Walker facing left | Walker facing right |
|---|---|---|
| small | 24 | 23 |
| medium | 31 | 21 |
| large | 27 | 26 |

(Rider facing left; for a rider facing right, use the opposite walker
facing, as for the other ranges.) These are the same ranges as above, not
extra ones: the random runs, with the rider moving, turning, jumping and
charging, and the walkers standing at offsets at rest agreed with them,
and for the small and medium walkers they are the same for the second
character (the large walker was not measured with it). Walkers on slopes were not measured.

The knock-off range reaches 2 px farther behind the rider than the
attack range for every kind and facing (26 and 25 px for the small
walker, 33 and 23 for the medium, 29 and 28 for the large). So a walker
walking toward the rider from behind, on the rider's floor, reaches the
knock-off range first, and what happens depends on the knock-off
conditions (above):

- With the rider on the floor at the end of both of the last two frames
  and the walker on the rider's floor, the rider is knocked off.
- With the rider in the air, the rider is knocked off only if U is at
  most the knock-off height (table); otherwise the walker is **thrown**
  once it is in the attack range and U is at most the attack height,
  like one coming from the front. So a small walker walking under a
  rider in the air from behind is thrown while U is more than −3 and at
  most 25 (the small walker's knock-off height is below the floor, so it
  never knocks off a rider in the air); a medium walker is thrown while
  U is more than 10 and at most 38; a large walker while U is more than
  23 and at most 51. A rider who lands with the walker behind and still in the knock-off
  range is knocked off as the rules above give.

In the 240 random runs with the second character (small and medium
walkers), 7 walkers that were behind the rider were thrown, all with the
rider in the air, and 26 walkers that reached the rider from behind
knocked it off.

## Examples

All values were measured on flat floor, with positions made relative.

A small walker facing right walks toward a rider at rest facing left,
at `x` 0. Frame 0 is the first frame with the walker in front of the
animal (D; so C = 1). I on each row uses the walker's position at the
end of the row before:

| Frame | Rider part | Rider `y` | Rider `vy` | Walker `x` (end of frame) | I |
|---|---|---|---|---|---|
| −1 | riding | 0 | 0 | −55.3671875 | 57 (out of range) |
| 0 (D) | charge | 0 | 0 | −54.3671875 | 56 |
| 1 (C) | charge | 0 | 0 | −53.3671875 | 55 |
| 2 | charge | 0 | 0 | −52.3671875 | 54 |
| 3 (C + 2) | charge, throw | 0 | −6 | −52.3671875 | 53 |
| 4 | charge | −5.5625 | −5.5625 | −52.3671875 | |
| 16 | charge | −38.1875 | −0.3125 | | |
| 29 (C + 28) | charge end (landed) | 0 | 0 | | |
| 41 | settling frame | 0 | 0 | | |

The rider was at rest, so the thrown walker's `vx` is 0 and it doesn't
move sideways. The rider is in the air from frame 4, highest on frame 16
(38.1875 px above the floor), and lands on frame 29, C + 28, which is
also the first frame of the charge end.

A small walker facing right walks into a rider at rest facing right,
at `x` 0, from behind. The walker's I at the end of frame −1 is 26
(within −18 to 26, the mirror of the small walker facing left in the
table). Positions of the animal are measured from where it stood:

| Frame | Player | `y` | `vy` | Walker `x` (end of frame) | Animal moved |
|---|---|---|---|---|---|
| −1 | riding | 0 | 0 | −25.95703125 | 0 |
| 0 (H) | knocked off | 0 | −6 | −24.95703125 | 0 |
| 1 to 15 | frozen | 0 | −6 | −24.95703125 | 0 |
| 16 | in the air | −5.5625 | −5.5625 | −23.95703125 | 6 |
| 20 | in the air | −23.4375 | −3.8125 | −19.95703125 | 30 |
| 21 | in the air | −26.8125 | −3.375 | −18.95703125 | 35.75 |
| 28 | in the air | −38.1875 | −0.3125 | −11.95703125 | |

Here the walker walks under the falling player, who bounces off it
(`bounce.md`). With nothing below, the player lands on H + 41.

## Not covered

- Slopes, ledges and walls; walkers that are turning, speeding up or
  stopped; walkers higher or lower than the rider's floor.
- What happens after the knock-off landing (protection, getting back on
  the animal, the animal at walls or leaving the view).
- The large walker's horizontal motion after a throw.
- Other enemies and objects; the large walker and the heights of the
  attack and knock-off with the second character (except through the
  random runs above).
