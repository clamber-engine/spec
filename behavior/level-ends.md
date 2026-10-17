# The level's left and right ends

Titles: `title-1`

How far left and right the picture can go in a level, and where the
player is stopped at the level's left and right ends. Both come from the
level's **left limit** `A` and **right limit** `B`, whole px values of
level x stored in the level's view-limit record
(`../formats/view-limits.md`). How the view moves is in `view.md`; this
file only adds what the limits change.

Frames are logic frames at 60 Hz, numbered as in a trace. `x` and `vx`
are the player's values as in a trace; level x is as in `view.md`.

Measured on the first level, with the first character leading, alone,
near both of the level's ends, and in copies of the image whose
first-level record had other limits (see "Checks").

## The picture's left edge

The view's position `V` (`view.md`) moves by the rules of `view.md`
whatever the limits are, and it can go past them. The picture is drawn
from its **left edge** `E`:

    E = min(max(⌊V⌋, A), B)

That is, ⌊`V`⌋ kept from `A` to `B`. The ground layer and the sprites
are drawn with `E`, so the picture, 256 px wide, shows level x `E` to
`E` + 255, and nothing left of `A` or right of `B` + 255 is ever shown.
While `V` is past a limit, the picture stays still and the view keeps
moving by its own rules; the picture follows again as soon as ⌊`V`⌋ is
back between `A` and `B`.

Where another file uses the picture's left edge, or "`V` rounded down to
a whole pixel" as the picture's left edge, it means `E`. This was
checked for:

- when placed objects appear (`../formats/placements.md`, "When an
  entry appears", and `walker-view.md`): an entry is in the window by
  its position relative to `E`, not to ⌊`V`⌋;
- the fade at the level's end (`level-end.md`): it was measured with the
  picture still at the right limit and `V` past it.

## The player at the ends

On each frame, take `x0`, the player's `x` at the start of the frame.
After the player's `vx` for the frame is found, and before the player
moves:

- if `vx` is above 0 and ⌊`x0`⌋ is `B` + 238 or more, or
- if `vx` is below 0 and ⌊`x0`⌋ is less than `A` + 18,

then `vx` becomes 0 and `x` doesn't change on this frame.

So the player is stopped 18 px inside the ends of the range the picture
can show (`A` to `B` + 255). Only moves toward the end are stopped: a
player held at the right end can still move left. A move that starts
before the limit can end past it, by up to one frame's move.

The rule held while the player stood, walked, ran, turned, jumped and
fell, rolled, stopped after a roll, held Down, used the ground action
(`ground-action.md`) and bounced off a walker (`bounce.md`).

## In the first level's room

The room the upper exit leads to (`exits.md`) doesn't take its limits
from a view-limit record. In the room `A` and `B` are both 0:

- `E` is 0 on every frame, whatever `V` does, so the picture always
  shows level x 0 to 255.
- The picture doesn't move up or down either: its top row is at level y
  0 on every frame. The room's picture is a screen picture
  (`../formats/graphics.md`, "Screen pictures").
- "The player at the ends" holds with `A` = 0: a player moving left is
  stopped on a frame that starts with ⌊`x0`⌋ less than 18. On the
  right, the room's exit is reached first (`exits.md`, "Not covered").

## Examples

Synthetic, computed from the rules above, with `A` = 64 and `B` = 4800.

**The picture.** `V` = 4850.25 gives `E` = 4800; `V` = 1000.75 gives
`E` = 1000; `V` = 20.5 gives `E` = 64.

**The right end.** `B` + 238 = 5038. A player moving right with `vx` =
2 on every frame:

| Frame | `x0` | ⌊`x0`⌋ ≥ 5038? | `vx` | `x` after the frame |
|---|---|---|---|---|
| 1 | 5034.5 | no | 2 | 5036.5 |
| 2 | 5036.5 | no | 2 | 5038.5 |
| 3 | 5038.5 | yes | 0 | 5038.5 |
| 4 | 5038.5 | yes | 0 | 5038.5 |

**The left end.** `A` + 18 = 82. A player moving left with `vx` = −2 on
every frame from `x` = 85.25: frame 1 moves to 83.25 (⌊85.25⌋ = 85),
frame 2 to 81.25 (⌊83.25⌋ = 83), and from frame 3 (⌊81.25⌋ = 81, less
than 82) `vx` is 0 and `x` stays 81.25.

## Checks

- **The picture.** In 10 runs near the first level's ends (walking,
  running, jumping and turning, 1 030 pairs of pictures), the ground
  layer's shift between pictures matched the change of `E` on every pair
  (with the picture one frame behind, as in `view.md`). On the 229 pairs
  where `E` and ⌊`V`⌋ changed differently, it matched `E` every time and
  ⌊`V`⌋ never. In a copy of the image with the first level's limits
  changed to `A` = 40 and `B` = 300, and in a copy with them unchanged,
  the same held on 604 pairs (98 where they differed). In a screenshot with the player stopped at the right
  end, `E` = 300 and ⌊`V`⌋ = 478, the player's sprite was drawn from `E`.
- **Placed objects.** In the copy with `B` = 300, `E` stayed at 300 while
  `V` went to 480. An entry 329 px right of `E` and 149 px right of
  ⌊`V`⌋ never appeared; in a copy with the first level's limits, it
  appeared when the view came near it, as `walker-view.md` gives. In a
  copy with `A` = 150, the first level's `entrance` entry, more than 31
  px left of `E`, never appeared, and the characters didn't enter the
  level in 150 frames (`level-start.md`).
- **The player.** Random inputs (walking and running toward the end,
  jumps, rolls, Down, the ground action, turns) in pairs of runs whose
  limit differed by 1 px: `B` = 300 and 301 in copies of the image, and
  `A` = 150 and 149 set after the level had begun. 160 runs, 96 892
  frames: on every frame that started at or past the limit, `vx` was not
  toward the end, and when it was 0, `x` didn't change. In 42 of the 80
  pairs the two runs parted, and each time on the frame the rule gives:
  the frame started with ⌊`x0`⌋ on the stricter run's limit, the
  stricter run stopped and the other moved on. In the other 38 pairs no
  frame started exactly there. Moves away from the end, from past the
  limit, were not stopped (1 248 frames).
- **The room.** In the runs of `../formats/graphics.md` ("Evidence"),
  6 runs with 72 pictures and 4 runs with 14 800 objects drawn, the
  picture showed level x 0 to 255 and level y 0 to 223 every time, and
  every object was drawn as `../formats/sprites.md` gives with the
  picture's left edge at level x 0 and its top row at level y 0. 16 runs
  of random input in the room, mostly toward the left (walking,
  running, jumps, rolls, Down): 19 184 frames, of which 14 815 started
  with ⌊`x0`⌋ less than 18. On every one of them `vx` was not below 0,
  and when it was 0, `x` didn't change.

## Not covered

- Carrying an object, riding, ropes, swimming (`swimming.md` has its
  own left end), knock-outs, the other character, and the walk-out at
  the level's end (`level-end.md`), in which the lead goes past the
  point where the right end would stop it.
- The walk-out through an exit (`exits.md`), which the left end doesn't
  stop. The first level's left end has one: a player walking toward the
  end there is taken over by it before reaching the stop.
- Whether the player's vertical motion changes on a stopped frame.
- Other levels, and kinds of area other than the first level's room
  that don't take their limits from a view-limit record
  (`../formats/view-limits.md`, "Not covered"). The right end of the
  room.
- How the player's speed changes on the frames after a stop, beyond
  what the usual rules give from `vx` = 0.
