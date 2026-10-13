# Format specs

Describe the structure of data the importer reads: fields, encodings,
and compression algorithms in prose. No ROM offsets, no copied bytes.
Every decoder spec needs synthetic vectors in `../vectors/`.

## Files

- `level-ground.md`: a level's ground: the grid (column grids and row
  grids), cell types, shapes, and the ground below a point. Vectors:
  `../vectors/level-ground/`.
