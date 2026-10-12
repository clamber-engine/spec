# Swapping characters

Titles: `title-1`

What happens when the player presses `S` (the swap input) while the other
character is with them: the two characters trade places, and the other
character becomes the one under control. Which presses start a swap, how
long it takes, how each character moves, and when control returns.

Frames are logic frames at 60 Hz, numbered as in a trace. `x`, `y` and
`vy` are as in a trace: right and down are positive, speeds in px per
frame. Speeds and positions are whole multiples of 1/256 px. "Left",
"Right", "Down", "Jump", "Action", "Swap" are the neutral inputs `L`,
`R`, `D`, `J`, `A`, `S`.

The **first character** is the one under control when a game begins;
the **second character** is the one that joins later (see
`losing-a-character.md`). The **player** is the character under control
before the swap; the **other character** is the one following it.

Measured on flat floor and on a 1-in-4 slope, with each character under
control in turn, by inputs alone: presses from standing, walking,
running, turning, crouching and after releasing Down, around jumps and
landings, during and after rolls, during and after a swap, and with the
other character in the air (2015 runs). On every frame of the 977 swaps,
both characters' `x`, `y` and facing match the rules below, and for each
of the 2031 presses the rules below say correctly whether it starts a
swap.

## Which presses start a swap

Call **Q** the frame of a press: `S` held on Q and not on Q − 1. Holding
`S` does nothing more; another swap needs another press.

A press on Q starts a swap on Q unless one of these holds:

1. There is no other character.
2. A swap is still going on: a press is refused up to and including the
   frame **E** on which control returns after the previous swap (below),
   and works from E + 1.
3. On Q − 1 the player is in the air (`ground` 0 on that row), or Q − 1
   is the frame it lands on (the row with `land`).
4. On Q − 1 the player is in a roll, or in the stop after a roll
   (`roll-input.md`): a roll that carries on into running allows a press
   from the frame after its `roll_end`; a roll that ends in a stop
   allows one from D + 18 (the stop lasts until D + 16).
5. The player is crouching. Call **C** the first frame Down is held and
   **U** the first frame it is no longer held (the last such run before
   Q, with C ≤ Q − 1). The press is refused while Q ≤ C + 22 or Q ≤ U + 9
   for the first character, and while Q ≤ C + 21 or Q ≤ U + 12 for the
   second character. (Down held for 5 frames from C, released on
   U = C + 5: the first character can swap from C + 23, the second from
   C + 22. Held for 20 frames: from U + 10 and U + 13.)
6. The two characters' heights differ by 8 px or more: take each
   character's `y` rounded to a whole pixel toward the floor if it has a
   fraction, the player's on Q − 1 and the other character's on Q − 1
   (but on Q when the second character is the player; see "The timeline").
   This holds whether the other character is above or below, standing or
   in the air: a press while the other character is in the air within
   7 px works.

A refused press is forgotten: nothing happens later because of it.

Presses that do start a swap, and what they cancel:

- On the frame of a Jump press (P in `jump-input.md`), on P + 1 (the
  `jump_start` row) or on P + 2: the player never leaves the ground. On
  P + 1 the jump's take-off speed is discarded. From P + 3 the player is
  in the air (rule 3).
- With Down pressed on Q itself: no crouch.
- With an Action press on Q (with or without a direction): no roll.
- During a turn (`ground-speed.md`), on any of its frames: the turn is
  abandoned; facing stays as it was on Q − 1.
- While walking or running: the player stops dead on Q (below).

## The timeline

Once a swap starts on Q, input is ignored until **E**. Both characters
first stay still, then each moves for 32 frames to the other's place:

| Player | Still | Moves (32 frames) | E |
|---|---|---|---|
| First character | Q to Q + 46 | Q + 47 to Q + 78 | Q + 79 |
| First character, with a Jump press on Q − 1 or Q − 2 | Q to Q + 45 | Q + 46 to Q + 77 | Q + 78 |
| Second character | Q to Q + 38 | Q + 39 to Q + 70 | Q + 72 |

The other character's moves:

- When the first character is the player, the other character stays
  still and moves on exactly the same frames as the player.
- When the second character is the player, the other (first) character
  still makes its usual following move on Q itself, and its position at
  the end of Q is its starting position for the swap. It stays still
  from Q + 1 to Q + 39 and moves from **Q + 40 to Q + 71**, one frame
  after the player.

"Still" means `x` and `y` are those of Q − 1 (for the first character
following the second: those of Q), whatever the speed was before.
Nothing slows down first.

On every frame before E: Left and Right do nothing, a Jump press is lost
(a jump needs a new press on E or later; Jump held from before E doesn't
jump), Action and Down do nothing, and Swap presses are refused. On E the
other character is the player in every respect: it stands still with
speed 0 (input on E counts as usual, so Left or Right held on E starts
walking on E), and its next Swap press can come on E + 1.

## How each character moves

Each character moves toward the place where the other one started. Take
both characters' starting positions (on Q − 1, or on Q for the first
character following the second).

**Horizontally.** **d** = the other's starting `x` with the fraction
dropped, minus the character's own starting `x` with the fraction
dropped. On each of its 32 move frames, `x` changes by d ÷ 32 (exact; a
multiple of 1/32). It ends on the other's whole-pixel position with its
own fraction kept.

**Vertically.** The **target** is the other's starting `y` rounded to a
whole pixel toward the floor if it has a fraction. The character has a
vertical speed **w**, which is −255/256 before its first move frame. On
each move frame:

1. `w` increases by **0.4375** (7/16), but never above **8**. For the
   player only, if Jump was pressed on Q, Q − 1 or Q − 2 and held on every
   frame from the press to Q, the increase is the player's lighter
   gravity from `jump-arc.md` / `second-character.md` instead (0.28125
   for the first character, 0.3515625 for the second) on all move frames.
2. `y` changes by `w`.
3. If `w` is positive and `y` is now below the target (greater), `y`
   moves up by the smallest whole number of pixels that puts it at or
   above the target.

The floor plays no part: a character whose target is lower than the
floor under it on the way sinks to its target, one whose target is
higher is lifted to it. On flat floor, where both start on the floor,
both characters bob by less than a pixel and end slightly above it.

On **E** the new player's `y` becomes its target exactly (the fraction is
dropped). Its `x` doesn't change.

**Facing.** Neither character's facing changes during the swap: each
keeps the facing it had on Q − 1, and still has it on E.

### Example (made-up numbers)

The first character is the player, standing on flat floor at `x` =
100.25, `y` = 50, facing left. The second character stands on the same
floor at `x` = 128.5, `y` = 50. Swap is pressed on Q.

- Q to Q + 46: neither moves.
- Player: d = 128 − 100 = 28, so `x` changes by +0.875 per frame from
  Q + 47, reaching 128.25 on Q + 78. The other character: d = −28, `x`
  goes from 128.5 to 100.5.
- Both have target 50. Their `y` is 49.44140625 on Q + 47, 49.3203125,
  49.63671875, then 49.390625 on Q + 50 (`y` went to 50.390625 and was
  moved up 1 px), 49.58203125, 49.2109375, … `y` is 49.953125 on Q + 66,
  and from Q + 67 `w` is 8, so it stays 49.953125 until Q + 78.
- E = Q + 79: the second character is the player, at `x` = 100.5,
  `y` = 50, facing as it did on Q − 1.

If instead the second character stood 4 px lower (`y` = 54), the player
would follow the same first three values, then 50.390625 (not below 54,
so not moved), 51.58203125, 53.2109375, 53.27734375 (moved up 2 px),
53.78125, …, and 53.953125 from Q + 66. The second character, with
target 50, would go 53.44140625, 53.3203125, then 49.63671875 on Q + 49
(from 53.63671875, moved up 4 px), and from there the same values as
on flat floor.

## In a trace

Rows Q to E − 1 follow the player (the character that was under
control before the swap). Rows from E follow the other character, which
is now the player. `x` and `y` stay relative to the scenario's start
position, so they jump on E to wherever the new player is.

Rows Q to E − 1:

- State `swap`, `ground` 1, no events. Facing as on Q − 1.
- `vx` and `vy` are the changes in `x` and `y` since the previous row
  (0 on Q and on the still frames).

Row E: the new player's own state (`idle` unless input on E starts a
move), `ground` 1, `vx` and `vy` as usual for that state, facing its
own. No `turn` on E for the change of character; `turn` on later rows
whenever facing changes.

When the press is on P + 1 of a jump, row Q is a `swap` row and there is
no `jump_start`. When it is on P + 2, row P + 1 has `jump_start` as
usual and the swap starts on the next row with `y` unchanged.

## Not covered

- How far apart horizontally the characters may be (up to 55 px
  measured), and swaps with the other character out of view.
- Walls, gaps or ledges between the two characters, and height
  differences whose fractions put them between 7 and 8 px.
- Enemies and other objects during a swap: whether they keep moving and
  whether they can hurt either character.
- Swaps while carrying an object, while hurt, during a bounce, and the
  Down + Action ground action.
- When the second character can swap after its own rolls (its roll
  lengths aren't specified yet).
- A press on the exact frame the other character lands from a jump of
  its own, when it is still within 7 px above the floor (it is left in
  the air and the player's target is that height).
- How the other character follows the player before and after a swap.
