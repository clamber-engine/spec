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
transparent. Which palette each object uses is covered for the characters
and for the objects listed after them (below).

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

### The other objects' palettes (`title-1`)

In the first level, up to the dimming (`../behavior/dimming.md`), each
object below is drawn with one palette, the same on every frame it is
drawn and for all of its pictures (the table says where that depends on
which character is the player). The pictures are those named in the
object's own file, by their labels. Where the palettes are
is in `locators.md`. Where the image holds the same colours in more than
one place, the row gives one of them; any gives the same colours for
every picture below.

| Objects | Pictures | Palette |
|---|---|---|
| Letters | `letter-pic-1` to `letter-pic-4` (`../behavior/letters.md`) | the shared palette |
| Letters' sparkles | their small pictures (`../behavior/letters.md`) | the shared palette |
| Single collectibles; the count display's image and digits | `spin-1` to `spin-5` (`../behavior/collectibles.md`); the digits and three of the collectibles' eight pictures are not named | the shared palette |
| Bunches | `bunch-1` to `bunch-6` (`../behavior/bunches.md`) | the shared palette |
| The flash of a breaking object | `flash-1` to `flash-8` (`../behavior/thrown-objects.md`) | the shared palette |
| The smoke of a breaking crate | `smoke-1` to `smoke-6` (`../behavior/animal-crate.md`) | the shared palette |
| Thrown objects, lying objects and the pieces of a breaking object | `thrown-1` to `thrown-8`, `lying`, `piece-a-1` to `piece-b-8` (`../behavior/thrown-objects.md`) | the thrown objects' palette |
| The crate | `crate-whole`, `crate-broken-2` (`../behavior/animal-crate.md`) | the crate's palette |
| The animal before it is ridden | `animal-1` to `animal-22` (`../behavior/animal-crate.md`) | the animal's palette |
| The first character mounting the animal | `mounting-1` to `mounting-5` (`../behavior/animal-crate.md`) | the first character's leading palette |
| The animal's body while the first character, leading and alone, rides it | every picture the animal's body shows from the end of the mounting on: standing, walking, turning, jumping, charging and landing (`../behavior/riding.md`) | the animal's palette |
| The first character, leading and alone, sitting on the animal | every picture it shows there, `mounting-1` to `mounting-5` included (`../behavior/riding.md`) | the first character's leading palette |
| The animal's body while the second character, leading, rides it | every picture the animal's body shows while it is ridden, on the frames covered by "Pictures with the second character" (`../behavior/riding.md`) | the animal's palette |
| The second character, leading, sitting on the animal | `rider2-1` to `rider2-14` (`../behavior/riding.md`) | the second character's leading palette |
| The one-life extra-life items | `extra-life-1`, `rising-extra-life-1` (`../behavior/extra-lives.md`) | the one-life item palette |
| Small, medium and large walkers | `small-*`, `medium-*`, `large-*` (`../behavior/walker.md`), except `large-push` | the small, medium and large walkers' palettes |
| The thrower and its balls | `throw-*`, `sway-*`, `thrower-out`, `ball-*` (`../behavior/thrower.md`) | the thrower's palette |
| Critters | `wing-1` to `wing-6` (`../behavior/critter.md`) | the critters' palette |
| The stinger | `wing-1` to `wing-4` (`../behavior/stinger.md`) | the stinger's palette |
| The lives display's image and digits | (`../behavior/count-display.md`) | the first character's palette: the leading one while the first character is the player, the following one while the second character is |
| The two characters on the level map | `map-lead-1` to `map-lead-10`, `map-other-1` to `map-other-8` (`../behavior/level-map.md`) | the level map's palette |

The **shared palette** is the one letters, sparkles, collectibles, the
count display, bunches, flashes and smoke use. No picture of these
objects that is named by a hash uses a colour number above 13. The
image holds colours 1 to 13 of the shared palette in three places, and
the thrown objects' palette in four; `locators.md` gives one place for each.

The **one-life item palette** is the palette the one-life extra-life
items use (`../behavior/extra-lives.md`, "Kinds"), all fifteen colour
numbers, in the first level before the dimming. `locators.md` gives a
place for it.

The **level map's palette** is the one both characters on the level map
use, whichever of them leads and whichever picture they show; it is not
the characters' own palettes. It is the one palette on the map, and
`locators.md` gives its place. In the runs (the first level's map, with
the first character leading, with the second leading, and after the
first finish; 1627 pictures read on screen, none of them during a fade)
every colour read for the 18 named map pictures was a colour of this
palette; the second character's pictures never use colour 4 and the first
character's use all fifteen. Fades of the map, and the maps of other
places, are under "Not covered".

While the animal is ridden by the first character, leading and alone, the body and the character on it keep these
two palettes on every picture: riding changes neither, and the body does
not take the character's colours. Other things drawn over the body, such
as the puffs of the shared palette, show their own colours over it; the
body's own pixels keep the animal's.

The same holds while the second character rides, leading: the body keeps
the animal's palette and the rider `rider2-1` to `rider2-14` is drawn
with the second character's leading palette, not its following palette
and not the first character's. The first character was off screen in the
runs, so what colours it has there is not covered. The runs were all
from one place on flat floor and used a setup method; no rides of ordinary
play were read. A few body pictures (6 in 7 916) showed another colour of
the animal's own palette at some pixels; which pictures these were was not
identified, so the body rule above is exact only for the others.

Some pictures here are not named by hash (the sparkles, three of the
collectibles' eight, the count display's digits and the lives display's
image: "Not covered"). Their palette is the one given because the
colours read on screen for them were all among that palette's, not
because a named picture matched (the lives display: first character's
palette, which the evidence below checks against the player).

Within the first level before the dimming, the colours read for an
object stayed those of its palette from picture to picture; only a change
of player (the lives display, the mounting pictures) and fades changed
them. What else changes them is under "Not covered".

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
- The other objects' palettes, measured by inputs alone. As for the
  characters, each object's picture was found on screen with only the
  sprite layer drawn (as for the picture hashes in the object's file),
  and the screen colour of each of its colour numbers was read on the
  video frame after the logic frame that drew it. 302 runs of up to
  1 500 video frames of random play with Swap presses, from 151 saved
  positions (the first level, caves, water areas, rooms, carrying,
  throwing, riding); the first 600 frames of the same 302 runs for the
  collectibles and the displays, and all of their frames for the
  sparkles;
  and 42 runs of 500 logic frames from 14 saved positions with both
  characters for the lives display. Only pictures of the first level before
  the dimming started were used (a run counts as past the dimming from the
  first frame the view's left edge is at 4 704 or more, or from its first
  frame when it starts after the dimming has begun). A picture "read
  exactly" the palette when every colour number read had that palette's
  colour. Pictures read, of which exact (the rest were darker versions
  of the palette's colours by one common factor, as in a fade, or each
  differed in at least one colour and were not examined further):
  - letters 13 649, 13 634 exact (15 differed); sparkles 53 163 (with
    some pixel left to read), of which 52 852 showed only colours among the shared palette's (their
    pictures are not named: the colours read in a 24 x 24 square around
    each, leaving out the area covered by any other object's picture);
  - bunches 6 004, all exact; flashes 2 179, 2 095 exact, 84 darker;
    smoke 2 558, all exact;
  - thrown objects, lying objects and pieces 43 311, 43 283 exact, 28
    darker;
  - the crate 6 561, all exact; the animal 6 010, 6 008 exact (2
    differed); the first character mounting 1 072, all exact (leading
    palette of the first character, as it was the player in all of them);
  - the animal ridden by the first character, alone and leading (random
    inputs, each ride ending when riding ended or after 700 frames). In 4
    rides of ordinary play, started from a saved point partway through a
    ride: the character on it 2 676 pictures, every pixel in the first
    character's leading palette; the body 2 596 pictures, of which 2 542
    showed only the animal's palette's colours and 54, in the landing, jump
    and charge pictures, showed at some pixels one of four other colours,
    in each case one of the shared palette's colours 6 to 9 (the greys of
    the puffs). In 24 further rides, for which a setup method was used (3
    seeded runs of 8 rides from four saved starting points): the character
    on it 7 801 pictures, every pixel in the leading palette; the body 7 537
    pictures, of which 7 325 showed only the animal's palette's colours
    (colour 13 was never read) and 212, in the same kinds of pictures,
    showed the same four greys at some pixels. Pictures read just before a
    mount or after a ride were the character's own, not the body's, and are
    left out;
  - the animal ridden by the second character, leading (a setup method was
    used: 3 seeded runs of 5 rides from one saved point of a ride, on
    flat floor, the first character off screen, random inputs, each ride
    ending when riding ended or after 700 frames): the rider 7 925
    pictures, every pixel in the second character's leading palette and
    none of them in its following palette; the body 7 916 pictures, of
    which 7 910 showed only the animal's palette's colours at the right
    colour numbers and 6 showed at some pixels (112 in all) another colour
    of the animal's palette instead (which pictures was not identified);
  - small walkers 8 897, 8 885 exact (12 differed); medium walkers
    8 502, 8 501 exact (1 differed); large walkers 6 118, 6 113 exact (5
    darker);
  - the thrower and its balls 32 749, 32 662 exact, 3 darker, 84 differed;
  - critters 32 880, 32 394 exact, 476 darker, 10 differed; the stinger
    1 520, all exact;
  - single collectibles (the five named pictures) 61 559, 60 343
    exact (1 216 differed); the count display's image 6 868, 6 851
    exact (17 differed); the count display's digits 52 026, of which
    50 682 showed only colours among the shared palette's;
  - the lives display: on 1 271 frames with both readable, its colours
    were the first character's leading palette's exactly when the first
    character's own pictures were, and the following palette's exactly
    when those were (0 frames disagreed; 319 more frames had no readable
    first-character picture). No change of player came while it was
    shown, so when its colours change is not covered.
  - the one-life extra-life items (`../behavior/extra-lives.md`), in
    ordinary play of the first level and of one other area where such
    items are placed (24 runs of up to 2 000 frames, the player moving
    right more often than not, and 8 runs of 500 frames from a saved
    position in the first level): 175 pictures showed only colours of
    that palette, 0 others. In further runs of up to 600 frames of
    random play, for which a setup method was used (2 seeds from each of
    9 saved positions), 1 311 more pictures of the same items were read,
    476 of them rising and 835 not; every one showed exactly one colour
    per colour number, the same on every picture (colour numbers
    1 to 15 all read);
  - the tyres, in two areas other than the first level (which has none),
    entered at their start and played with the player moving right more
    often than not (2 seeds of 2 000 video frames each): in the first,
    1 038 pictures, 969 of them in one set of twelve colours (colour
    numbers 1 to 12; 13 to 15 never read), 69 darker
    versions of it; in the second, 226 pictures, 183 in one set of eleven
    colours (colour numbers 2 to 12), 43 darker. All
    seven tyre pictures were read in each. The first area's colours were
    found in the image as a 24-byte run in one place; the second area's
    were not found, with ten colours as a run, anywhere in it;
  Then, for each group, the most common colour read per colour number
  was looked for in the image as a 30-byte run, starting anywhere. Small,
  large and medium walkers, the thrower, the crate, the animal, the
  critters and the stinger each had one place in the image (the medium
  and large walkers' and the animal's colours were also found in a
  second place with the same colours; the first character's leading
  colours as in the characters' palettes above). The shared palette's
  group (13 of 15 colour numbers read) was found in three places, equal
  in colours 1 to 13; the thrown objects' (14 of 15) in four places,
  all with the same 15 colours.

  The level map's characters were read in the same way on the first
  level's map from four saved starting points, with random direction
  presses: 1 336 pictures of the object shown first and 291 of the
  second, every colour among the map palette's (all 18 named pictures
  seen). Its colours, taken as a 30-byte run, were found in one place in
  the image.

## Not covered

- The map's characters' colours while the map fades in or out, and on
  the maps of places other than the first level's.
- Which palette the two-life and three-life extra-life items use: their
  pictures were read only in runs from a setup method (476 and 1 732
  pictures, one colour per colour number on every picture), with
  only 4 partly hidden two-life pictures and no three-life picture
  read in ordinary play.
- Which palette the objects not listed above use (the signs, the launch
  barrels, the flying copy of a taken collectible, the objects of other
  areas). The tyres' colours differ between areas (see "Evidence"); which
  areas use which, and where the second area's come from, is not
  covered. (The frame table is in `locators.md`; it locates the frames.)
- The other objects' colours in some situations (the objects in "The
  other objects' palettes"):
  - near the first level's end, where the shared palette's colours
    change with the dimming (`../behavior/dimming.md`, "Sprite colours";
    the bunches' 3 517 pictures read then were none of them in the
    palette's colours) and the other palettes' are not covered there;
  - in other areas: of the pictures read there, the medium walkers' 85
    were none in the palette's colours (seen in some caves), 10 433 of
    the letters' 12 251 and 5 607 of the critters' 6 618 were, and
    nearly all or all of the others' were. Which areas, and which
    colours instead, is not covered;
  - while the picture fades out or in;
  - the `crate-broken-1` and `large-push` pictures, never seen in the
    runs above;
  - a second character mounting; the second character riding while it is
    not leading; the first character riding while not leading, or with
    the second character present;
  - the second character riding in ordinary play, on a slope or on any
    floor other than the flat one used (only one starting place on flat
    floor was read), and which body pictures showed another colour of the
    animal's palette; the first character's colours while the second
    character rides;
  - the frames on which the lives display's colours change when the
    player changes.
- The characters' colours in some situations:
  - near the first level's end, where the colours change with the
    dimming (`../behavior/dimming.md`, "Sprite colours" gives them for
    the first character leading, alone);
  - in areas where the characters are drawn with changed colours
    throughout (seen in one area other than the first level);
  - while the picture fades out or in (seen on leaving an area and
    around other area changes);
  - in some water areas, where the characters are shown darker
    throughout: in the two seen, each colour's red, green and blue were
    half the palette's, rounded down. Which areas these are, and whether
    other objects are darker too, is not covered;
  - areas where the player is an animal (the first character riding,
    leading and alone, uses its leading palette, see the table above);
  - in games with two players;
  - the picture of both characters on the level map.
- Which frames each kind of object shows and when (animation sequences
  and their timing), and which objects are
  mirrored top–bottom. The player's frames in the stop after a roll,
  after a jump's landing and on a bounce's frozen frames are in
  `../behavior/player-frames.md`, the walkers' pictures in
  `../behavior/walker.md` ("Pictures"), the thrower's and its
  balls' in `../behavior/thrower.md` ("Pictures"), the single
  collectibles' in `../behavior/collectibles.md` ("Pictures"), the
  bunches' in `../behavior/bunches.md` ("Pictures"), those of thrown
  objects, the flash and the pieces of a breaking object in
  `../behavior/thrown-objects.md` ("Pictures"), and the letters' and
  their sparkles' in `../behavior/letters.md` ("Pictures"), and the
  crate's, its smoke's and the animal's before it is ridden in
  `../behavior/animal-crate.md` ("Pictures"), and the count display's
  image and the timing of the digits and the lives image in
  `../behavior/count-display.md` ("Pictures"), and the critters'
  in `../behavior/critter.md` ("Pictures"), the tyres' in
  `../behavior/tyre.md` ("Pictures"), the extra-life items' in
  `../behavior/extra-lives.md` ("Pictures"), the signs' in
  `../behavior/signs.md` ("Pictures"), and the launch barrels' in
  `../behavior/launch-barrel.md` ("Pictures").
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
