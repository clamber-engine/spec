# Sprite frames

Titles: `title-1`

How the pictures of the characters, walkers, collectibles and other
moving things are stored, and how to turn them into images: the frame
table, frames (pieces and their tiles), sprite palettes, mirroring, and
where a frame's picture is drawn for an object. **Colours**, **palette
rows**, **tiles** and colour number 0 being transparent are as in
`graphics.md`. Vectors: `../vectors/sprites/`.

A **frame** is one still picture of an object. An object shows one frame
at a time; which frame each kind of object shows, and when, is not
covered here (see "Not covered").

## The frame table

The **frame table** says where each frame is stored. Entry `k` is for
frame `k` and is 4 bytes, at bytes 4`k` to 4`k` + 3 of the table:

| Byte | Meaning |
|---|---|
| 0–1 | Address of the frame (low byte first) |
| 2 | Bank number of the frame |
| 3 | **Order adjustment**: see "Not covered" |

Where the frame table starts in the image is in `locators.md`. The bank
number and address in an entry are a location, turned into a position
in the image as in `rom-image.md`. Entry 0 is all zero: frame 0 means
no picture.

In `title-1` the table has frames 1 to 2 748; the entries after them
are zero. The order adjustment is 0 in 2 734 entries and 4 in 14.

## Frames

Frames come in two forms, told apart by their number:

- frames 1 to 34 are **short** frames,
- frames 35 and up are **full** frames.

A frame is a **header**, then a **position** for each of its pieces,
then (full frames only) its **tile data**, stored one after another with
nothing between them.

The header of a full frame is 8 bytes:

| Byte | Name | Meaning |
|---|---|---|
| 0 | `L` | Number of large pieces |
| 1 | `S1` | Number of small pieces in the first small group |
| 2 | `T1` | Sheet tile of the first small group's first piece |
| 3 | `S2` | Number of small pieces in the second small group |
| 4 | `T2` | Sheet tile of the second small group's first piece |
| 5 | `N1` | Number of tiles in the first tile run |
| 6 | `D2` | Sheet tile where the second tile run goes |
| 7 | `N2` | Bits 0–3: number of tiles in the second tile run (0: there is no second run). Bits 4–7 are 0 in every `title-1` frame. |

The header of a short frame is 5 bytes: bytes 0 to 4 of the table
above. A short frame has no tile runs and no tile data.

After the header come `L` + `S1` + `S2` positions, 2 bytes each: first
the large pieces' positions, then the first small group's, then the
second small group's. In a position, byte 0 is `bx` and byte 1 is `by`.
The piece's top-left pixel is at

    (bx − 128, by − 128)

from the frame's **origin** (pixel (0, 0) of the frame's picture; x to
the right, y down). Offsets run from −128 to 127.

In a full frame, the tile data follows the positions: `N1` tiles, then
`N2` tiles, each a four-plane tile of 32 bytes (`graphics.md`). The
frame's length is 8 + 2(`L` + `S1` + `S2`) + 32(`N1` + `N2`) bytes; a
short frame's is 5 + 2(`L` + `S1` + `S2`).

## The frame's sheet

The tiles a frame's pieces use are numbered as a **sheet**, 16 tiles
(128 px) wide: sheet tile `n` is in sheet column `n` mod 16 and sheet row
`n` div 16.

In a full frame, the first run's tiles are sheet tiles 0 to `N1` − 1, in
order, and the second run's are sheet tiles `D2` to `D2` + `N2` − 1. In
every `title-1` full frame the two runs don't overlap and hold exactly
the sheet tiles the frame's pieces use, no more.

A short frame uses tiles that are already in place for the object: its
sheet is the object's own tile set, numbered from 0 the same way. Which
tile set that is, and where it is stored, is not covered.

## Pieces

A **large piece** is 16 × 16 px, made of four sheet tiles: `t` (top
left), `t` + 1 (top right), `t` + 16 (bottom left) and `t` + 17
(bottom right). Large piece `i` (counting from 0) has

    t = (2i mod 16) + 32 × (2i div 16)

so large pieces 0 to 7 have their top halves in sheet row 0 and bottom
halves in row 1, pieces 8 to 15 use rows 2 and 3, and so on.

A **small piece** is 8 × 8 px, one sheet tile. Piece `j` (from 0) of the
first small group is sheet tile `T1` + `j`; piece `j` of the second
small group is sheet tile `T2` + `j`. Both groups are drawn the same
way; they differ only in where their tiles are.

## The frame's picture

A frame's picture is its pieces drawn at their positions, each with its
tiles' pixels; colour number 0 is transparent. Pieces can overlap. In
`title-1`, 2 478 of the 2 714 full frames have pieces whose opaque
pixels overlap, and every such pixel has the same colour number in both
pieces, so which piece is in front never changes the picture.

The picture's colours come from one palette row chosen by the object
(see "Sprite palettes").

## Mirroring

An object can be drawn mirrored:

- **Left–right**: the picture's pixel (`x`, `y`) is drawn at
  (−1 − `x`, `y`) from the origin.
- **Top–bottom**: pixel (`x`, `y`) is drawn at (`x`, −1 − `y`).
- **Both**: pixel (`x`, `y`) is drawn at (−1 − `x`, −1 − `y`).

So a piece of width `w` at offset (`ox`, `oy`) ends up at
(−`ox` − `w`, `oy`) when mirrored left–right, with its pixels mirrored.
The player character is drawn mirrored left–right exactly when it
faces left.

## Picture hashes

A frame's **picture hash** names its picture without numbering it
(`../REVIEW.md`, "The third exception: picture hashes"). It is the
SHA-256 of these bytes, worked out from the frame's picture as
"The frame's picture" gives it, not mirrored:

1. The **box**: the smallest rectangle holding every opaque pixel, as
   offsets from the origin. Its left and top offsets, each as a signed
   16-bit number, then its width and height, each as an unsigned 16-bit
   number; all four low byte first (8 bytes).
2. Then one byte per pixel of the box, row by row from the top and left
   to right within a row: the pixel's **colour number** (1 to 15), or 0
   for a transparent pixel. Colours and palettes are not part of the
   hash.

Only full frames have picture hashes, and only those with at least 32
opaque pixels. In `title-1`, 2 699 of the 2 714 full frames have one,
and they have 2 684 different hashes (some frames have the same
picture).

A behavior file that names a picture by its hash means that picture,
mirrored as the object's mirroring says ("Mirroring"). To find it, an
importer decodes every full frame of the table, works out each one's
hash and keeps the frames whose hash is wanted. The behavior file says
what to show when a hash isn't found.

Example (synthetic): a frame whose only opaque pixels are colour 3 at
(−1, −2) and colour 5 at (0, −1) has the box left −1, top −2, width 2,
height 2, so the bytes hashed are `ff ff fe ff 02 00 02 00 03 00 00 05`.
(Such a picture has too few opaque pixels to be named; the vectors have
full-size ones.)

## Where the picture is drawn

For an object at level x `x` and level y `y` (as in `placements.md`; for
the player, its position as in `../behavior/view-vertical.md`), the
frame's origin is drawn at

    level x = ⌊x⌋ − 1
    level y = ⌈y⌉

For the player, ⌈`y`⌉ is its feet line `F`. A picture on screen uses the
object's position, frame and mirroring after the same logic frame as
the view it is shown with (`../behavior/view.md`).

## Sprite palettes

A **sprite palette** is 15 colours, stored in order (30 bytes): colours
1 to 15 of the palette row it is used as. Colour 0 is not stored; it is
transparent. Which palette each object uses is not covered, except for
the characters (below).

### The characters' palettes (`title-1`)

Each character has two sprite palettes of its own: a **leading**
palette, which it is drawn with while it is the player (the character
under control), and a **following** palette, which it is drawn with
while the other character is the player. A character on its own is the
player, so it uses its leading palette. Every picture of the character
(`../behavior/player-frames.md`) is drawn with that one palette. The
**first** and **second character** are as in `../behavior/swap.md`.
Where the four palettes are is in `locators.md`:

| Character | While it is the player | While the other one is the player |
|---|---|---|
| First character | first character's leading palette | first character's following palette |
| Second character | second character's leading palette | second character's following palette |

Apart from the cases under "Not covered", the colours stay the same
while playing, including while flashing (`../behavior/protection.md`)
and in water (except in the darker areas under "Not covered"): only a
change of player changes them. When the player
changes, the colours change on the frames below. Each is the logic frame
whose picture first shows the new colours (a picture is shown with its
logic frame's view, as in "Where the picture is drawn"). Before it, the
character still shows its old palette:

| How the player changes | The new player's leading palette from | The other character's following palette from |
|---|---|---|
| A swap (`../behavior/swap.md`) | S | S + 1 |
| Losing a character (`../behavior/losing-a-character.md`) | T | T + 1 |

**S** is the last frame of the swap on which both characters stand
still: in the timeline of `swap.md`, Q + 46 when the first character is
the player, Q + 45 when it is the player with a Jump press on Q − 1 or
Q − 2, and Q + 38 when the second character is the player. **T** is as
in `losing-a-character.md`. Swaps started by Tandem
(`../behavior/controls.md`) without a Jump press before them change
colours on the same frames as swaps started by Swap.

Example: the first character is the player, with no Jump press before
the swap, and Swap is pressed on frame 200, so S is 246. The second
character keeps its following colours up to frame 245 and shows its
leading colours from 246. The first character keeps its leading colours
up to 246 and shows its following colours from 247.

## Example (synthetic)

A full frame with header `02 03 04 01 14 07 10 05` has `L` = 2, `S1` =
3, `T1` = 4, `S2` = 1, `T2` = 20, `N1` = 7, `D2` = 16, `N2` = 5. Its
6 positions take 12 bytes; then come 7 tiles (sheet tiles 0 to 6) and
5 tiles (sheet tiles 16 to 20): 8 + 12 + 384 = 404 bytes in all.

- Large piece 0 uses sheet tiles 0, 1, 16 and 17; large piece 1 uses 2,
  3, 18 and 19.
- The first small group is sheet tiles 4, 5 and 6; the second is sheet
  tile 20.
- If large piece 0's position is `80 70`, its top-left pixel is at
  (0, −16): the piece covers x 0 to 15, y −16 to −1. Mirrored left–right
  it covers x −16 to −1.

Drawn for a player at level x 300.5 with level y 336.25, the origin is at
level x 299, level y 337.

## Vectors

`../vectors/sprites/`: a made-up bank with a frame table and short and
full frames, the decoded table, headers, pieces and sheets, the pictures
as stored and mirrored, a sprite palette, and the picture hashes of the
full frames with every byte hashed for one of them.

## Evidence

- Every drawn object was checked against this description while playing
  from 19 saved positions (14 in the first level, 5 elsewhere), two runs
  of up to 600 frames each with made-up input: 15 365 frames. For each
  object drawn, its frame's pieces were placed from the object's
  position and mirroring as above. Of 424 192 pieces so placed,
  424 173 were in the game's own list of shown pieces with the same
  place, size, tile, palette row and mirroring. The other 19 were all
  the small pieces of two frames, in two of the 15 365 frames.
- After a full frame was first shown for an object, the tiles the game
  had in place for that object were the frame's tile runs, at the sheet
  positions above, in 14 811 of 14 829 cases; in the other 18 the
  previous frame's tiles were still in place for one frame.
- On screen: the player's pictures, drawn from the frame, palette row,
  mirroring and position as above, matched the game's picture pixel for
  pixel in 147 of 147 consecutive frames of walking, running and
  jumping, and in 36 of 36 frames standing still.
- Reading every entry of the `title-1` frame table as above gave 34
  short and 2 714 full frames, each full frame's tile runs holding
  exactly the tiles its pieces use.
- The sprite palette rows the game had in place were found as 30-byte
  runs in the image (colours 1 to 15, in order).
- The characters' palettes, measured by inputs alone:
  - **Where the four palettes are.** Each character's picture was
    found on screen with only the sprite layer drawn (as for the picture
    hashes in `../behavior/player-frames.md`), and the screen colour of
    each of its colour numbers was read. Frames within 60 frames of a
    change of player were left out. In the 24 later runs under "On
    screen", leaving out the three with fades and the two darker water
    areas, each character showed the same 15 colours on every frame
    while it was the player (5 831 frames for the first character, 2 036
    for the second) and another 15 on every frame while the other one
    was (1 852 and 796 frames; 15 readings of single colour numbers of
    the second character differed). Each set of 15 colours was then
    looked for in the image as a 30-byte run of colours 1 to 15 in
    order, starting anywhere. Each following set was found once. Each
    leading set was found in several places, all the same colours, one
    of them directly before the same character's following palette; the
    rows in `locators.md` give that one and the following one.
  - **On screen.** 23 runs of up to 1 500 frames from 12 saved positions
    with both characters, random play with Swap presses. Each
    character's picture was found on screen (with only the sprite layer
    drawn) on 23 559 frames, and the screen colour of each of its
    colour numbers was read. On 23 330 frames they were exactly the
    palette given above, including on the frames around each change of
    player. Of the others, 226 came during a fade on leaving an area
    ("Not covered"), and 3 were single frames, between frames that
    matched, on which the colours read matched none of the four
    palettes. The same check was run later on 24 new runs: 18 runs of
    up to 600 frames from 18 positions with one character (levels, 4
    caves, 5 water areas, rooms, carrying and throwing) and 6 runs of up
    to 1 500 frames from 6 positions with two. With two characters,
    5 363 of 5 363 picture frames showed the palette given above,
    including the frames around 7 changes of player. With one, 6 630
    picture frames of the player showed its leading palette; the other
    193 came during fades in and out of the whole picture (three runs,
    around area changes), and in two of the five water areas every
    frame (857) showed the colours darker (see "Not covered").
  - **When the colours change.** On every frame, the screen pixels
    showing colours found in only one of the four palettes were
    counted. From 13 saved positions with both characters, one run with
    a Swap press on its own and one with a Jump press two frames before
    it; and 17 runs of up to 1 500 frames of random play from 6
    positions, with Swap and Tandem presses and hits: 67 changes of
    player (49 swaps by Swap, 7 by Tandem, 11 characters lost). Every
    one of the 134 colour changes came on the frame given above.
  - **Holdout,** written after the rules above: 72 new runs of up to
    1 500 frames of random play from 12 saved positions, 168 changes of player (85 swaps by
    Swap, 62 by Tandem, 21 characters lost). 317 colour changes were
    seen on screen (in the others the character was out of view). 316
    came on the frame given above. In the other, another object showed
    a few pixels of the old colours for a while; the character itself
    changed on time. In one of the 316, a swap whose player had pressed
    Jump before landing on Q − 4, both characters stood still one frame
    less than `swap.md` says (S was Q + 45 without a Jump press on Q − 1
    or Q − 2), and the colours changed on that S and S + 1.

## Not covered

- Where the sprite palettes are in the image, and which one each object
  uses, other than the characters' palettes above. (The frame table is
  in `locators.md`; it locates the frames.)
- The characters' colours in some situations:
  - near the first level's end, where sprite colours change with the
    dimming (`../behavior/dimming.md`, "Not covered");
  - in areas where the characters are drawn with changed colours
    throughout (seen in one area other than the first level);
  - while the picture fades out or in (seen on leaving an area and
    around other area changes);
  - in some water areas, where the characters are shown darker
    throughout: in the two seen, each colour's red, green and blue were
    half the palette's, rounded down. Which areas these are, and whether
    other objects are darker too, is not covered;
  - while riding an animal, and in areas where the player is an animal;
  - in games with two players;
  - the picture of both characters on the level map.
- Which frames each kind of object shows and when (animation sequences
  and their timing), which palette each uses (other than the
  characters' palettes above), and which objects are
  mirrored top–bottom. The player's frames in the stop after a roll,
  after a jump's landing and on a bounce's frozen frames are in
  `../behavior/player-frames.md`, the walkers' pictures in
  `../behavior/walker.md` ("Pictures"), the thrower's and its
  balls' in `../behavior/thrower.md` ("Pictures"), the single
  collectibles' in `../behavior/collectibles.md` ("Pictures"), the
  bunches' in `../behavior/bunches.md` ("Pictures"), those of thrown
  objects, the flash and the pieces of a breaking in
  `../behavior/thrown-objects.md` ("Pictures"), and the letters' and
  their sparkles' in `../behavior/letters.md` ("Pictures"), and the
  crate's, its smoke's and the animal's before it is ridden in
  `../behavior/animal-crate.md` ("Pictures"), and the count display's
  image and the timing of the digits and the lives image in
  `../behavior/count-display.md` ("Pictures").
- The tile sets short frames use, and where they come from.
- What the order adjustment does, and the order objects are drawn in
  (which object is in front of which), and how pictures are ordered
  against the background layers (`graphics.md`).
- Some things on screen are drawn without frames from the frame table
  (about 9 % of the pieces shown in the runs above). Some of their
  pictures are those of no full frame, such as three of the single
  collectibles' (`../behavior/collectibles.md`, "Pictures"), and the
  count display's digits and the lives display's image
  (`../behavior/count-display.md`, "Pictures").
- Collision or hit areas: frames hold none.
- Why the 19 pieces in "Evidence" were not shown, and frames shown for
  one frame with the previous frame's tiles.
