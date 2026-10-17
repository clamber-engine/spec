# Single collectibles vectors

Synthetic vectors for `../../formats/collectibles.md`. All data is made
up; none of it comes from a game.

## Input

- `bank.hex`: part of a made-up bank, 8 bytes per line, each line
  starting with the address of its first byte (hex). Two group lists
  start at `6000` and `6020`, and a shape table at `6030` with shapes 0
  to 4. The bytes `ee` are filler and belong to no list or shape.

What the data exercises: the example of the format spec (the group at
`6000` and shape 2); an empty list (`6020`); end records whose last two
bytes aren't 0; a group whose first byte is 0 but whose second isn't,
which is a group and not the end (the last group at `6000`); groups at
level x and y 0 and at the largest `X` and `Y`, 16 376; a shape of one
cell (shape 0); a shape with 16 rows whose cell bits fill two words
exactly (shape 1); columns that start in one word and end in the next,
and bits left over in the last word (shapes 2 and 3); a shape with one
row (shape 4); and shapes whose data don't follow each other directly.

## Expected output

- `groups.tsv`: each group of the list at `list` (hex), numbered from 0
  in stored order, with its `skip` bit, `shape`, `X` and `Y`. The empty
  list has no rows.
- `items.tsv`: each item, by its group, with the cell's `column` and
  `row`, the level x of its image's leftmost column (`left`), the level
  y of its image's top row (`top`), and its position `xc`, `yc`. Items
  are listed by group, then by column, then by row.
