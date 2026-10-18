# Default controls

Titles: `title-1`, `title-2`

Which pad button each title uses, by default, for each neutral input of
the scenario format (`../scenarios/FORMAT.md`), so that an engine preset
can offer each title's own layout. Pad buttons are named by position:
the **D-pad**; the **bottom**, **right**, **left** and **top** face
buttons; the left and right **shoulders**; **Start** and **Select**.

The neutral inputs are `L` `R` `U` `D` (directions), `J` (Jump), `A`
(Action), `S` (Swap), `P` (Pause) and, new with this page, `T`
(**Tandem**): the input that acts on the pair of characters, with a
meaning of its own in each title (below). A **press** of an input is a
frame on which it is held after a frame on which it was not.

## `title-1`

| Pad button | Input |
|---|---|
| D-pad | `L` `R` `U` `D` |
| Bottom | `J` |
| Left | `A` |
| Right | `T` |
| Top | none in levels; on the level map it does what Jump does (`level-map.md`, "Pressing at a stop") |
| Shoulders | none |
| Start | `P` |
| Select | `S` |

Every input is on exactly one button. Two buttons swap the characters:
Select (`S`) and the right button (`T`). They are different inputs, and
`T` does more:

**Tandem in `title-1`.**

- **On foot, in a level, and swimming:** a `T` press is a Swap press
  (`swap.md`, "Which presses start a swap") on the frame of the press,
  with three differences:
  1. It counts only on the frame it is pressed. Holding `T` after a
     press that was refused does nothing later; holding `S` can
     (`swap.md`, "Holding Swap").
  2. The swap it starts never uses the player's lighter gravity: the
     vertical speed increases by 0.4375 on every move frame even when
     Jump was pressed on Q, Q − 1 or Q − 2 and held (`swap.md`, "How
     each character moves").
  3. After a swap in which the second character was the player, a `T`
     press on E itself starts the next swap; `S` works from E + 1 as
     `swap.md` says. After a swap in which the first character was the
     player, both work from E + 1.
- **Riding** (`riding.md`): a `T` press makes the rider get off the
  animal, on the ground or in the air. Standing still, it hops up off the
  animal and comes down on its own feet; the animal stays where it was.
  `S` does nothing while riding. Getting off frame by frame is in
  `getting-off.md`.
- **On the level map:** `T` does what Jump does (`level-map.md`,
  "Pressing at a stop"); `S` does not.

## `title-2`

| Pad button | Input |
|---|---|
| D-pad | `L` `R` `U` `D` |
| Bottom | `J` |
| Left | `A` |
| Right | `T` |
| Top | none |
| Shoulders | none |
| Start | `P` |
| Select | `S` |

**Tandem in `title-2`:** with the other character following, a `T`
press makes the player **lift** the other character onto its shoulders.
While it is lifted, an Action press throws it ahead, another `T` press
sets it down, Jump jumps with it, and Swap does nothing. Holding `T`
does no more than one press. The lift, the throw and setting down, frame
by frame, are not covered yet; a later page will cover them. Swapping
in `title-2` (`S`) is not covered yet either.

## Measurements

Measured by inputs alone, from saved states in levels, on the level map
(`title-1`) and in water. The `title-2` state with both characters was
reached by freeing the second character in play.

- Each pad button pressed, pressed twice, held while walking and pressed
  while walking, from standing with both characters, riding (`title-1`)
  and alone: the tables above.
- **Buttons with no input** (top, shoulders): 312 random runs in
  `title-1` (13 states: standing with one or two characters, carrying,
  swimming, riding on land and in water, the level map, near enemies,
  with the animal's crate) and 216 in `title-2` (9 states: with both
  characters, alone, swimming, near walkers, in several levels), 600
  frames each, random directions, Jump, Action and Swap (and in
  `title-2` Tandem), compared with the same run with the button also
  held on about a third of the segments. Both characters' `x`, `y`,
  action and facing were the same on every frame, except the top button
  on the `title-1` level map (it entered the level, as Jump does). The
  pictures differed only in single frames (by one to a few rows, with
  nothing else different) and, in one `title-2` level, in the phase of
  a background ripple at the top of the picture.
- **`T` against `S` in `title-1`**: the 2 015 runs of `swap.md` played
  again with the right button in place of Select (2 031 presses, 977
  swaps): which presses start a swap and every frame of every swap
  match `swap.md` with differences 2 and 3 above. Without difference 2,
  8 of the swaps don't match; without difference 3, one press. Holding
  the button for 60 frames from each run's first press (2 015 runs per
  button): `T` swapped only on the press frame; `S` as in `swap.md`,
  "Holding Swap". One-frame presses of each in
  random runs in the 13 `title-1` states (104 runs): the characters
  differed only while riding (18 runs, `T` got off), on the level map (1
  run, `T` entered the level) and in swaps started with a Jump press (10
  runs, difference 2).
- **Tandem in `title-2`**: from standing with both characters, one
  press, a held press, and a press followed 50 frames later by Action,
  Tandem, Swap or Jump.

## Not covered

- `title-3`.
- Left and Right held together, or Up and Down held together (the
  measuring setup cannot hold them).
- Buttons in menus, on the `title-2` level map, and while riding in
  `title-2`.
- In `title-1`: other animals than the one in `riding.md`, and other
  situations the measured states don't reach (`T` may do something of
  its own there; nothing else was seen). Holding `S` through the
  Down + Action ground action.
