# View limits

Titles: `title-1`

A level's **view-limit record** gives the range the picture can show
from left to right, and the list of vertical limits the view follows.
What the values do is in `../behavior/level-ends.md` (left and right)
and `../behavior/view-vertical.md` ("The level's limits"). Vectors:
`../vectors/view-limits/`.

## The record

All values are 16-bit words, each stored low byte first. The record
is:

1. the **left limit** `A`, a level x in whole px;
2. the **right limit** `B`, a level x in whole px;
3. the **vertical limit entries**, 8 bytes each, back to back;
4. an **end word** `ffff` (hex) where the next entry's first word would
   be.

Each vertical limit entry is four words:

| Word | Name | Meaning |
|---|---|---|
| 0 | `start` | The entry's starting `x` (whole px) |
| 1 | `top` | The top, a level y in whole px |
| 2 | `bottom` | The bottom, a level y in whole px |
| 3 | `rate` | Bit 0: 1 = quick, 0 = usual. The other bits were 0 |

These are the entries of `../behavior/view-vertical.md` ("The level's
limits"), in the order they are stored: an entry is in use from its
`start` up to the next entry's `start`, and the first entry is also in
use to the left of its start. The record has at least one entry, and
the entries' `start` values grow from one entry to the next.

The end word only ends the list; no entry's `start` is `ffff`. A start
of `ffff` (65535) can't be a level x of an entry in use, so a reader can
stop at the first `ffff` in word 0 of an entry.

Every word is a whole number from 0 to 65 535, with no sign. In the
first level's record `A` is less than `B`; in its scene's record they
are equal ("The first level's scene"). The scene's record has `A`, `B`
and `start` of 32 768 or more: they are level x in the scene's grid
(`level-ground.md`) like any other. Values of 32 768 or more in `top`
or `bottom` are not covered (none was seen).

## The first level

`locators.md` gives the first level's record ("View limits: the first
level's record"). The first level's restart point
(`level-start.md`) uses the same record: after resuming there, the
limits were the same as when the level began. The record has 14
vertical limit entries; 1 of them is quick.

The first level's room (`../behavior/exits.md`) doesn't use a record:
its left and right limits are both 0, and its picture doesn't move
(`../behavior/level-ends.md`, "In the first level's room").

## The first level's scene

`locators.md` also gives the record of the scene the floor exit leads
to (`../behavior/exits.md`, "The scene"). It has `A` = `B`, and one
vertical limit entry, whose `top` and `bottom` are equal. So the view
can't move either way in the scene: its picture's left edge is always
`A` and its top row always `top` (`../behavior/level-ends.md`, "In the
first level's scene").

## The area behind the first wall exit

`locators.md` also gives the record of the area behind the first
level's first wall exit (`../behavior/wall-exits.md`, "The area behind
the first wall exit"). It has `A` less than `B` and one
vertical limit entry, usual, with `top` less than `bottom`. The picture's left edge and the
player's limits follow from `A` and `B` as in
`../behavior/level-ends.md`, and the view as in `../behavior/view.md`
and `../behavior/view-vertical.md`.

## The area behind the second wall exit

`locators.md` also gives the record of the area behind the first
level's second wall exit (`../behavior/wall-exits.md`, "The area behind
the second wall exit"). As the scene's, it has `A` = `B`, and one
vertical limit entry, whose `top` and `bottom` are equal. So the view
can't move either way in the area: its picture's left edge is always `A`
and its top row `top` (`../behavior/level-ends.md`, "In the area behind
the second wall exit").

## Example (synthetic)

Bytes from the record's start, in hex:

    40 00 c0 12
    10 00 20 00 a0 00 00 00
    00 03 30 00 b0 00 01 00
    c8 08 00 00 08 01 00 00
    ff ff

- `A` = 64, `B` = 4800.
- Entry 0: start 16, top 32, bottom 160, usual. It is also in use for
  `x` below 16.
- Entry 1: start 768, top 48, bottom 176, quick.
- Entry 2: start 2248, top 0, bottom 264, usual. It is in use from 2248
  to the right end of the level.
- The end word follows.

So for a player at `x` = 800, entry 1 is in use. The picture's left
edge is kept from 64 to 4800 (`../behavior/level-ends.md`).

More cases, as bytes, are in `../vectors/view-limits/`.

## Checks

- Loading the first level from the level map in copies of the image
  with the first level's `A` and `B` changed (to 40 and 300, and to 0
  and 300 or 301): the picture's left edge and the player's limits
  followed the changed values (`../behavior/level-ends.md`, "Checks").
- The scene's record: in each of the 397 logic frames of the scene, in
  runs with the lead alone and with two characters, the picture's left
  edge was the record's `A` and its top row the record's `top`. In a
  copy of the image with the scene's `A`, `B` and `start` all lowered by
  64 and its `top` and `bottom` both lowered by 24, they were the
  changed values on every frame of the scene.
- The vertical limit entries: the checks of
  `../behavior/view-vertical.md` used the entries read from the first
  level's record as above.
- The record of the area behind the first wall exit: 71 walk-ins into
  the area, the lead alone, with and without input, 12 651 frames from
  frame 0 up to the first state those rules leave out: the view on
  every frame was as
  `../behavior/view.md` gives ("When an area begins" and the usual
  rules), with this record's `A` and `B`. In 800 runs through the area
  the picture's left edge was `B` whenever the lead passed the end
  marker, and the fade came as `../behavior/level-end.md` gives with
  that edge ("In the area behind the first wall exit"). In a copy of
  the image with `A` raised by 40, `B` lowered by 48, the entry's `top`
  lowered by 16 and its `bottom` by 17, in 20 runs: the view's
  position on frame 0 was the changed `A` + 1/2; a lead walking right was stopped at the
  changed `B` + 238 as `../behavior/level-ends.md` gives, short of the
  end marker, so the area couldn't be left; and the top row of the
  picture stayed at or above the changed `bottom` (at or above the
  image's own `bottom` in the image) on every frame except during the
  Down + Action ground action (`../behavior/ground-action.md`) and up to
  20 frames after it, when it was up to 4 px lower. The entry's `top`
  was never reached.
- The record of the area behind the second wall exit: in 1 200 runs
  through the area (`../behavior/picture-game.md`, "Checks"), the
  picture's left edge was `A` and its top row `top` on every frame but
  the ones `../behavior/level-ends.md` lists ("Checks"). In a copy of the
  image with `A` and `B` both lowered by 16, the whole picture was drawn
  16 px further right.

## Not covered

- Where the records of levels other than the first are, and which record
  each area of a level uses. Some kinds of area (for example the
  underwater ones) don't take their left and right limits from such a
  record.
- Bits 1 to 15 of `rate`.
- Whether the limits can change during a level. In every run of the
  first level they stayed the same.
