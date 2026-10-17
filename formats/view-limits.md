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

`A` is less than `B`. Values of 32768 or more in `A`, `B`, `top` or
`bottom` are not covered (none was seen in the first level's record).

## The first level

`locators.md` gives the first level's record ("View limits: the first
level's record"). The first level's restart point
(`level-start.md`) uses the same record: after resuming there, the
limits were the same as when the level began. The record has 14
vertical limit entries; 1 of them is quick.

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
- The vertical limit entries: the checks of
  `../behavior/view-vertical.md` used the entries read from the first
  level's record as above.

## Not covered

- Where the records of levels other than the first are, and which record
  each area of a level uses. Some kinds of area (for example the
  underwater ones) don't take their left and right limits from such a
  record.
- Bits 1 to 15 of `rate`.
- Whether the limits can change during a level. In every run of the
  first level they stayed the same.
