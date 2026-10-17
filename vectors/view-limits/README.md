# View limits vectors

Synthetic vectors for `../../formats/view-limits.md` and the picture's
left edge in `../../behavior/level-ends.md`. All data is made up; none
of it comes from a game.

## Input

- `bank.hex`: part of a made-up bank, 8 bytes per line, each line
  starting with the address of its first byte (hex). Four view-limit
  records start at `4000`, `4020`, `4030` and `4058`. The bytes `ee`
  between and after them are filler and belong to no record.

What the data exercises: the example of the format spec (`4000`); a
record with a single entry whose top equals its bottom (`4020`); a
first entry that starts to the right of 0 (`4000`, `4030`, `4058`); an
entry starting at 32767, the largest start before the end word
(`4030`); entries one px apart (`4058`); and limits one px apart
(`4058`).

## Expected output

- `records.tsv`: for the record at each `address` (hex), the left limit
  `A`, the right limit `B` and the number of vertical limit entries.
- `entries.tsv`: each entry of each record, numbered from 0, with its
  `start`, `top`, `bottom` and rate (`usual` or `quick`).
- `in-use.tsv`: for a player at `x` (whole px), the number of the entry
  in use: the last entry whose `start` is at most `x`, or entry 0 if
  there is none.
- `edge.tsv`: for a view position `V` (px, with its fraction), the
  picture's left edge: ⌊`V`⌋ kept from `A` to `B`.
