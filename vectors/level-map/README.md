# Level map vectors

Synthetic vectors for `../../formats/level-map.md`. All data is made up;
none of it comes from a game.

## Input

- `points.hex`: the seven point tables of a made-up map, 16 bytes per
  line, each line starting with the address of its first byte (hex).
  The tables start at address `a000` (so table 1 is `a000`–`a0ff`,
  table 2 `a100`–`a1ff`, and so on to table 7 at `a600`–`a6ff`).
- `corners.hex`: the corner records, starting at address `c000`. Here
  `K` = 3 (corner ids 112, 113 and 114).

What the data exercises:

- Positions on pages 0, 1 and 2, `x` on its page 255 (id 200, appearance
  point on the next page), and `h` from 7 to 255.
- A route through two corners in a row, in both directions (stops 20 and
  30), and two directions with the same neighbour (stop 30, Left and
  Down).
- A corner whose record names `S` → `S` but not the walk's destination:
  from stop 40, Up goes straight to 200, while Down goes through corner
  114 to 250.
- A stop with one neighbour (10), and directions with no neighbour (0).

## Expected output

- `points.tsv`: every id with a non-zero position or neighbour: its kind
  (stop or corner), position `x` and `h`, the appearance point's `x`
  (stops only; its `h` is `h`), and the neighbours for Right, Left, Up
  and Down (0 = none).
- `routes.tsv`: for every stop and direction with a non-zero neighbour,
  the route, as `id@x,h` for each point in order. The last point is the
  neighbour.
