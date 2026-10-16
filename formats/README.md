# Format specs

Describe the structure of data the importer reads: fields, encodings,
and compression algorithms in prose. No copied bytes. Every decoder spec
needs synthetic vectors in `../vectors/`.

Where each kind of data starts in a supported image goes in
`locators.md` and nowhere else, under the rules in `../REVIEW.md`
("The one exception: locator tables"). Other format specs refer to the
locator table by the data kind's name. Fingerprint tables that tell
object kinds apart also go in `locators.md` ("The second exception: kind
fingerprints" in `../REVIEW.md`).

## Files

- `level-ground.md`: a level's ground: the grid (column grids and row
  grids), cell types, shapes, and the ground below a point. Vectors:
  `../vectors/level-ground/`.
- `rom-image.md`: recognizing a supported image file (copier headers,
  size, hash) and turning locations (bank number and address) into
  positions in the image. Vectors: `../vectors/rom-image/`.
- `graphics.md`: the first level's background pictures: colours and
  palette rows, tiles, packed tile data, tile entries, cell pictures,
  and the two layers made from the grid. Vectors: `../vectors/graphics/`.
- `placements.md`: a level's placement list: entries, classes, groups,
  kinds of objects (by fingerprint), positions, and when the objects
  placed appear. Vectors:
  `../vectors/placements/`.
- `sprites.md`: pictures of characters and other moving things: the
  frame table, short and full frames, pieces, sheets, mirroring, where
  a frame is drawn, and sprite palettes. Vectors: `../vectors/sprites/`.
- `locators.md`: where each kind of data starts in each supported
  image.
- `sound.md`: samples (sample table, records, blocks, decoding, loops),
  which samples each song has (shared list, sample sets), and where each
  song's data is. Vectors: `../vectors/sound/`.
- `songs.md`: what song data says: the header, steps and beats (period
  and tempo), tracks of notes, rests and commands, lengths, the pitch of
  a note, the three effects, song-wide settings, and what each voice is
  handed and when. Vectors: `../vectors/songs/`.
- `sound-effects.md`: sound effects: the effect block and table, the
  effect tempo, starting an effect, how an effect takes a voice from the
  song and hands it back. Vectors: `../vectors/sound-effects/`.
