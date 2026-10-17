# Level start vectors

Synthetic vectors for `../../formats/level-start.md`. All data is made
up; none of it comes from a game.

## Input

- `bank.hex`: part of a made-up bank, 8 bytes per line, each line
  starting with the address of its first byte (hex). Five start records
  are stored back to back from address `4000`. `Yb` = 512. The bytes
  after the last record (`ee`) are filler.

What the data exercises: a height of 0 (the point is on the bottom edge),
a height equal to `Yb` (level y 0), a height above `Yb` (a negative level
y), the largest `x`, and words whose high byte is not 0.

## Expected output

- `points.tsv`: for the record at each `address` (hex), the words `x` and
  `h` and the point's `level_x` and `level_y` (= 512 − `h`).
