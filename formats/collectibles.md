# Single collectibles

Titles: `title-1`

Where a level's single collectibles (`../behavior/collectibles.md`) are
in its data. A level's **group list** places them in **groups**: each
group is a **shape**, a small grid of cells some of which hold an item,
put at a position in the level. The shapes are in a **shape table**
that all levels share. Vectors: `../vectors/collectibles/`.

Bunches, letters, figures and other objects that are taken by touching
them are in the placement list (`placements.md`), not here.

## The group list

The group list is a run of 4-byte **records**, back to back. Read each
record as one 32-bit number, low byte first. Its bits are:

| Bits | Name | Meaning |
|---|---|---|
| 0 | `skip` | 0 in every group of the first level (see "Not covered") |
| 1 to 9 | `shape` | The shape's number in the shape table, 0 to 511 |
| 10 to 20 | `top` | The group's top `Y` = 8 × `top`, a level y in whole px |
| 21 to 31 | `left` | The group's left `X` = 8 × `left`, a level x in whole px |

So `X` and `Y` are multiples of 8, from 0 to 16 376.

The list ends at the first record whose first two bytes are both 0.
That record is not a group, and nothing after it is part of the list.
A list can be empty: its first record is then the end.

## The shape table

The table starts with **offsets**: the offset of shape `s` is the
16-bit word, low byte first, at bytes 2`s` and 2`s` + 1 of the table.
Shape `s`'s data starts that many bytes after the table's first byte.
The number of shapes is not stored; a reader only needs the shapes its
groups name.

A shape's data is:

1. byte 0: the number of **columns** `n`;
2. byte 1: the number of **rows** `h`, from 1 to 16 (the top two bits
   of this byte were 0 in every shape);
3. from byte 2 on, the **cell bits**: `n` × `h` bits, taken from 16-bit
   words stored low byte first, bit 0 of a word first and bit 15 last,
   one word after the other.

The cell bits go column by column, from column 0 (the left one) to
column `n` − 1: the first `h` bits are column 0, the next `h` column 1,
and so on. A column doesn't start a new word; it starts with the bit
after the previous column's last bit. Within a column the first bit is
the **bottom** row (row `h` − 1) and the last bit the **top** row (row
0). The bits left over in the last word are not part of the shape.

A cell whose bit is 1 holds an item; a cell whose bit is 0 is empty.

## Items

A group at (`X`, `Y`) whose shape has a 1 in column `c`, row `r` places
one single collectible there. Its 16 × 16 px image
(`../behavior/collectibles.md`, "A collectible's position") has

- its leftmost column at level x `X` + 8`c`;
- its top row at level y `Y` + 8`r` + 1.

So the collectible's position (`xc`, `yc`) is (`X` + 8`c` + 8,
`Y` + 8`r` + 9).

When an item can be taken is in `../behavior/collectibles.md`.

## The first level

`locators.md` gives the first level's group list ("Single collectibles:
the first level's group list") and the shape table ("Single
collectibles: shape table"). The first level's list has 10 groups, which
use 5 different shapes and place 34 items. Its shapes have 1 to 13 rows.

The first level's room and its scene (`../behavior/exits.md`) have no
single collectibles.

## Example (synthetic)

A shape table whose shape 2 has these bytes, in hex, from its start:

    03 05 d9 4c

`n` = 3, `h` = 5, and the cell bits come from one word, `4cd9`. From
bit 0 up its bits are `1 0 0 1 1 0 1 1 0 0 1 1 0 0 1 0`, so the 15 cell
bits are:

- column 0: `1 0 0 1 1`, so rows 4, 3, 2, 1, 0 are 1, 0, 0, 1, 1;
- column 1: `0 1 1 0 0`, rows 4 to 0;
- column 2: `1 1 0 0 1`, rows 4 to 0.

Bit 15 is left over. The cells holding items, as (column, row), are
(0, 0), (0, 1), (0, 4), (1, 2), (1, 3), (2, 0), (2, 3) and (2, 4).

A group record with bytes `04 64 40 0c` is the number `0c406404`:
`skip` 0, `shape` 2, `top` 25, `left` 98. So `X` = 784 and `Y` = 200.
Its item in cell (1, 2) has its image's leftmost column at level x 792
and its top row at level y 217; its position is (800, 225).

## Checks

- The game's own table of groups, made when an area begins, was
  compared with what these rules give from the image, for every area
  with a list that isn't empty and that began when it was started
  straight from the level map: 153 areas, 2 539 groups and 10 619
  items (lists that several areas share were counted once per area). The groups' positions,
  sizes and cells all matched.
- In copies of the image, the first group of the first level's list was
  replaced by each of the shapes 1 to 129 in turn, near the start of the
  level. The items drawn were exactly those these rules place: 936
  items, none missing and none extra. Along a route through the first
  level, the 22 items it passes were drawn where these rules place them.

## Not covered

- Groups whose `skip` bit is 1. None is in the first level; in a copy
  of the image with the bit set, the group's items weren't drawn.
- A row count of 0 or above 16, a shape with no columns, and groups
  whose cells overlap other groups' cells.
- Where other levels' group lists are, and the lists of the areas
  behind the first level's wall exits (`../behavior/wall-exits.md`).
- How an item is drawn and animated.
