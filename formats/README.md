# Format specs

Describe the structure of data the importer reads: fields, encodings,
and compression algorithms in prose. No ROM offsets, no copied bytes.
Every decoder spec needs synthetic vectors in `../vectors/`.

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
  positions, and when the objects placed appear. Vectors:
  `../vectors/placements/`.
