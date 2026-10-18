# Sprite frame vectors

Synthetic vectors for `../../formats/sprites.md`. All data is made up;
none of it comes from a game. Written and re-checked against the prose
by the research side's generator.

## Input

- `bank.hex`: part of a made-up bank, 16 bytes per line, each line
  starting with the address of its first byte (hex). The frame table
  starts at address `8010` and has entries for frames 0 to 38; the
  frames are in bank `5a`. Filler bytes (`ee`) lie between and after
  the frames; a reader must not reach them.
- `object-tiles.hex`: a made-up tile set (32 tiles, one per line, 32
  bytes each, numbered from 00) standing for the tiles already in place
  for an object; frame 5 (a short frame) is drawn with it.
- `palette.hex`: a made-up sprite palette, 30 bytes.

What the data exercises:

- Frame 0 (no picture), short frames (1 to 34) and full frames (35 and
  up); several entries pointing at the same frame; an order adjustment
  of 4 (frame 36).
- Frame 35: two large pieces, both small groups, and a second tile run
  that starts at sheet tile 16.
- Frame 36: small pieces only, one tile run (`N2` = 0), and positions
  of `00` and `ff` (offsets −128 and 127).
- Frame 37: nine large pieces, so large piece 8 uses sheet rows 2 and 3,
  and a first run of 48 tiles.
- Frame 5: a short frame (5-byte header, no tile data).

## Expected output

- `table.tsv`: every table entry: bank, address (hex), order adjustment
  and form.
- `frames.tsv`: the header fields of frames 5, 35, 36 and 37 and each
  frame's length in bytes (`-`: not in a short frame).
- `pieces.tsv`: each piece in order: size, offset of its top-left pixel
  from the origin, and its sheet tiles (for a large piece: top left, top
  right, bottom left, bottom right).
- `sheets.tsv`: for each full frame, which tile of which run is at each
  sheet tile.
- `pictures.txt`: the pictures of frames 5, 35 and 37, as stored and
  mirrored. Each starts with the range of offsets shown; then one line
  per row, one character per pixel: the colour number in hex, `.` for
  transparent, and `+` for the origin when it is transparent.
- `palette.tsv`: the palette's colours 1 to 15 as red, green and blue
  (0 to 31).
- `hashes.tsv`: the picture hash of frames 35, 36 and 37 ("Picture
  hashes"): the box (left, top, width, height), the number of opaque
  pixels, the number of bytes hashed and the SHA-256 in hex.
- `hash-input-35.hex`: every byte hashed for frame 35, in order, 16 per
  line.
