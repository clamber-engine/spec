# Exits

Titles: `title-1`

An **exit** is a placed object that takes the lead out of the area: once
the lead comes within its reach, the player loses control, the lead
walks away through the exit on its own, the picture fades to black and
play goes on in another area. This file covers when an exit takes over,
how the lead moves afterwards, and when the area is left. Which area
comes next, and what happens there, is not covered yet.

Frames are logic frames at 60 Hz, numbered as in a trace. Horizontal
positions are level x as in `view.md`; vertical positions are **level y**
as in `view-vertical.md`, growing downward. `x`, `y` and `vx` are the
lead's values as in a trace; `X` is `x` rounded down. "Right", "Left",
"Down", "Jump" and "Action" are the neutral inputs `R`, `L`, `D`, `J`,
`A` of the scenario format.

Measured on the first level, which has two exits, both leading left: one
on the level's floor near its left end, and one on higher ground above
it, reached by climbing. Most runs used the one on the floor. The runs
started with the lead standing a little right of it after the level's
entrance (`level-start.md`), alone or with the other character
following, and used random inputs (see "Checks").

## The exit object

A level's placement list (`../formats/placements.md`) places each exit
with an entry of kind `exit`. The exit doesn't move and is not drawn.
Its **position** (`xe`, `ye`) is its entry's level x and level y
("Positions" there).

Every exit measured **leads left**. What follows is for an exit leading
left; an exit leading right is not covered. Where an exit leads (which
area comes next) is a setting of the object; the first level's two
exits lead to different areas.

## When an exit takes over

Let `I` = `X` − `xe` at the end of a frame (how far right of the exit
the lead is, in whole px). The exit **takes over** on the first frame
that ends with all of these true:

1. the lead is on the ground (the landing frame of a jump or fall
   counts);
2. `vx` is 0 or less (the lead is still or moving left);
3. the lead's `y` is from 39 px above the exit's `ye` to 35 px below it
   (`y` − `ye` from −39 to 35);
4. `I` is at most the **reach** for the lead's state and facing on that
   frame:

| Lead's state at the end of the frame | Facing | Reach (largest `I`) |
|---|---|---|
| Standing, walking or running; the landing frame of a jump or fall | left | 31 |
| Rolling | left | 29 |
| Crouching (Down held) while still sliding | left | 34 |
| Standing, or turning (`ground-speed.md`) | right | 37 |

Call that frame **`T`**. Only the lead is taken over. In the runs with
two characters, the other character (`following.md`) was never taken
over; it went on following the lead.

A player moving right is never taken over, however close: a lead that
walks right past an exit and stops in its reach is taken over on the
frame `vx` becomes 0. A lead in the air is not taken over until the
frame it lands. Frames on which the lead is on a steep face above the
exit's floor (`steep-faces.md`), being carried down it, were not taken
over even with `y` − `ye` within the range of item 3.

How far left of the exit the reach goes is not covered (see "Not
covered"). In the first level, a lead coming from the right is taken
over before it gets there.

## The walk-out

From `T` on, the lead walks left on its own:

- **On `T`** the lead moves as usual for that frame (its input counts).
  At the end of `T`, `y` becomes `ye` + 1, whatever it was, with no
  fraction.
- **On `T` + 1** `vx` becomes **−2**, and `x` doesn't change.
- **From `T` + 2** `x` goes down by exactly 2 every frame. `y` doesn't
  change. The lead shows the walking pose.

From `T` + 1 on, inputs have no effect: no direction, Jump, Action or
Down held or pressed changes anything above. The walk takes no account
of the ground or of walls: the lead keeps the same `y` and passes through
whatever is in its way, and it is not stopped at the level's left end
(`level-ends.md`): `x` can go below 0.

**Facing.** A lead facing left on `T` keeps facing left. A lead facing
right on `T` (it was standing or turning) shows the pictures of a turn
from `T` + 2 and faces left from `T` + 6.

## Leaving the area

Call **`F`** the first frame after `T` that ends with

    X ≤ xe − 4

that is, with the lead at least 4 px left of the exit. In the runs `F`
was from `T` + 9 to `T` + 22, mostly `T` + 17 to `T` + 19.

- **The view.** The view's position `V` (`view.md`, `view-vertical.md`)
  and the picture's left edge (`level-ends.md`) move by their usual
  rules up to `F` − 1, and don't change from `F` on.
- **The fade.** The **brightness** of the whole picture, on a scale from
  15 (full) to 0 (black), is 15 on `F`, and goes down by 1 every second
  frame: frames `F` + 2`j` − 1 and `F` + 2`j` have brightness 15 − `j`.
  It reaches 0 on `F` + 29.
- **The end.** `F` + 30 is the last frame in the area. The lead goes on
  walking left by 2 px every frame up to it. On the next logic frame,
  play is in another area. Which area that is, where the characters are
  in it, and what happens there are not covered.

## Example

Synthetic, computed from the rules above, with an exit at `xe` = 300,
`ye` = 200.

A lead walking left with `vx` = −1.75 ends a frame at `x` = 333.5 (`I` =
33, not in reach while walking), and the next frame at `x` = 331.75
(`I` = 31): that frame is `T`. At the end of `T`, `y` = 201. On `T` + 1,
`x` stays 331.75 and `vx` = −2; on `T` + 2 it is 329.75, and on `T` + `k`
it is 331.75 − 2(`k` − 1). The first frame with `X` ≤ 296 is `T` + 19
(`x` = 297.75 on `T` + 18, 295.75 on `T` + 19), so `F` = `T` + 19. The
brightness is 15 on `T` + 19, 14 on `T` + 20 and `T` + 21, 1 on
`T` + 46 and `T` + 47, and 0 on `T` + 48 and `T` + 49. `T` + 49, with
`x` = 235.75, is the last frame in the area.

## Checks

- **Takeover.** 400 runs from a random point 40 to 92 px right of the
  floor exit, with random inputs biased to the left (walking, running,
  rolling, jumping, crouching, stopping, short presses of Right):
  37 680 frames judged by the rules above, 329 takeovers (288 by the
  floor exit, 41 by the upper one, reached by climbing), no frame
  different. A second, held-out set of 200 runs from 34 to 116 px right
  of it: 21 575 frames, 144 takeovers, none different. Frames in states
  not in the table, with `I` in reach, were not judged (see "Not
  covered").
- **Reach edges.** For each state in the table, the lead was put at
  every whole-px `I` around the edge, with fractions 0 to 0.99, after
  being brought into the state away from the exit: taken over up to the
  reach, never at reach + 1. A lead moving right (walking, or turning
  with `vx` still above 0) was never taken over. A lead put 30 px right
  of the exit moving right at 0.6 px per frame, with no input, slowed
  down and was taken over on the first frame its `vx` was 0, 34 px
  right of it.
- **Vertical range.** With the lead standing on the floor facing right
  at `I` = 36, and the exit moved up and down in steps of 1 px (a change
  made for the test while the game ran), the exit took over with
  `y` − `ye` from −39 to 35 and not at −40 or 36.
- **Walk-out and leaving.** 325 runs that went on until the area changed
  (273 with the lead alone, 52 with the other character following; 289
  through the floor exit, 36 through the upper one), with random inputs
  (Left, Right, Jump, Action, Up, Down, Right with Jump or Action) on
  12 193 of the 15 466 frames from `T` + 1 on: every frame was as above
  (35 726 frames). Three runs started a turn: the lead faced right on `T`.
- **Other places.** In copies of the image in which an entry far from
  the left end was given one of the exits' references, as a class 1 or a
  class 2 entry (three copies), an exit was there: a lead walking right
  past it went on, and walking back left it was taken over with `I` =
  30, walked out, and left the area on `F` + 31, `F` being the first frame
  with `X` ≤ `xe` − 4. Each reference led to the same area as in its own
  place.

## Not covered

- Exits leading right, exits in other levels, and where an exit leads:
  which area comes next, where the characters are when it begins, and
  what happens there (in the first level: a short scene in one case, a
  room in the other).
- How far left of the exit the reach goes, and the reach of states not
  in the table: rolls stopping or getting up (taken over at `I` up to 28
  to 30, by picture), crouching after the slide has stopped (up to 28 in
  the cases measured), turning while facing left, carrying an object,
  riding, and the frames of a landing after the landing frame. How far
  the vertical range changes with the lead's state.
- What the other character does from `T` on. In the runs it went on
  following the lead (`following.md`) and was in the next area with it;
  this was not checked frame by frame.
- What a trace shows from `T` on (`../traces/VOCABULARY.md` has no state
  for the walk-out), and which picture shows each step of the fade.
- Walkers, thrown objects or other objects meeting the lead during the
  walk-out, and the lead being hurt or knocked out then.
- Pausing during the walk-out.
