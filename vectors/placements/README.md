# Placement vectors

Synthetic vectors for `../../formats/placements.md`. All data is made
up; none of it comes from a game.

## Input

- `bank.hex`: part of a made-up bank, 8 bytes per line, each line
  starting with the address of its first byte (hex). The placement list
  starts at address `9008`. `Yb` = 512.

What the data exercises:

- Entries of classes 1, 2, 5 and 8, two entries with the same `x`, and
  two with the same reference.
- A group (entry 3) whose reference is `9050`: its members start at
  `9058`. The 8 bytes at `9050` are not zero here, and are not part of
  the group. One member's `x` is smaller than the group entry's.
- Entry 6 comes after an entry with a larger `x`.
- Heights of 0 and 512, an `x` near the top of the 16-bit range, and a
  reference of `ffff`.
- Filler bytes (`ee`) between and after the lists, which a reader must
  not reach.

## Expected output

- `entries.tsv`: every entry of the list in stored order (`index` from
  0), each group's members right after the group entry (`index` `i.j`
  for member `j` of entry `i`, `group` = `i`). `level_y` = 512 − `h`;
  `r` in hex.
- `appear.tsv`: with the view moving right 3 px a frame, `V` = −40 + 3`n`
  at the start of frame `n` (frame 0 is the level's first frame), the
  frame on which each object appears and its `V`, or `never` within
  frames 0–299. Class 8 places nothing. Entry 6 is in the window from
  frame 134 on, but appears on frame 151 with entry 5, which is stored
  before it and is right of the window until then ("Stored order").

## Kinds

Vectors for "Kinds" in `../../formats/placements.md`.

### Input

- The **image**: 3 bank blocks (196 608 bytes), not stored. The byte at
  position `p` (0 to 196 607) is (37`p` + ⌊`p` / 2048⌋) mod 256. Its
  SHA-256 is
  `a6ba56c90978aa11c57f493b12e84636df5e08f726485164e53537cc69aaa433`.
- `kinds-rows.tsv`: made-up kind fingerprint rows for this image, as a
  `locators.md` section would list them.
- References to look up: `8100`, `9000`, `9010`, `ffe0`, `4000`, `9020`
  and `9030`.

What the data exercises:

- Rows made from bank blocks 0, 1 and 2, and two rows with the same
  kind.
- Reference `ffe0`: in block 1 its 64 bytes run on into block 2; in
  block 2 they would run past the end of the image, so block 2 gives no
  fingerprint.
- Reference `4000` matches no row.

### Expected output

- `kinds-candidates.tsv`: every candidate fingerprint, one line per
  reference and bank block, or `none`.
- `kinds.tsv`: for each reference, the block whose candidate matched a
  row and the row's kind, or `-` and `none`.

### Settings

Vectors for "Settings" in `../../formats/placements.md`, with the same
image and references.

- Input: `kinds-settings-rows.tsv`, made-up settings rows for two of the
  fingerprints in `kinds-rows.tsv`, as a `locators.md` section would list
  them; `-` marks a setting the row doesn't give.
- Expected output: `kinds-settings.tsv`, the settings each reference's
  objects start with: given settings, the default for the one not given
  (heading of reference `9010`), and `-` for kinds without settings and
  for the reference that matches no row.
- Input: `kinds-ball-settings-rows.tsv`, a made-up `ball` settings row
  for one of the two `ball` fingerprints, giving its start `vx` only.
  Speeds are in 1/256 px per frame; `-` marks a setting not given.
- Expected output: `kinds-ball-settings.tsv`, the start speeds each
  reference's objects start with: reference `9020` the given start `vx`
  and the default start `vy`, reference `9030` (no settings row) both
  defaults, and `-` for the other kinds.

## Lists with sections

Vectors for "Lists with sections" and "In a list with sections" in
`../../formats/placements.md`.

### Input

- `sections-bank.hex`: part of a made-up bank, in the same form as
  `bank.hex`. The list starts at address `9008`, its section table at
  `9100`, and the members of its one group at `9188` (the group entry's
  reference is `9180`). `Yb` = 1024.
- The view: `V` and `U` at the start of each frame are the first two
  columns after `frame` in `sections-frames.tsv`. Frame 0 is the area's
  first frame.
- No object is ever removed.

What the data exercises:

- Three sections (entries 1–6, 7–10 and 11–14) joined by two records,
  and the record of zeros that ends the table, with other bytes that
  are not zero.
- Class 15 entries that wait for the vertical window, class 1 entries
  that don't, and a group.
- A crossing that starts, the far section's entries appearing, and the
  home section changing as the view goes up past a point; later, coming
  back down past a point, and the side edge deciding when the view jumps
  past a side edge and the bottom edge in one frame.
- A frame on which a far section entry appears, so that a home section
  entry waits one frame (frames 228 and 229).
- Entry 14 is stored after entry 13, which has a larger `x`: it can't
  appear before entry 13 is in the window (frame 813).
- `V` less than 32 (frames 0–9 and others).
- No record's point is in the window on frame 0.
- Not exercised: class 2 entries, and removals (no object is removed,
  so the group stays clear).

### Expected output

- `sections-records.tsv`: the section table's records (`index` from 0)
  with `level_y` = 1024 − `h`.
- `sections-frames.tsv`: for each frame, the entries whose objects
  appear on that frame (a group is listed by its entry's number, and all
  its members appear with it), or `-`.
- `sections-appear.tsv`: for each entry of class 1, 5 or 15, the frame
  on which its object (or the group's objects) appears and the `V` and
  `U` of that frame, or `never`.
