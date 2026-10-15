# Image file vectors

Synthetic vectors for `../../formats/rom-image.md`. No file described
here is or holds an image of any game; the hashed files are made from a
simple pattern, described in the format's "Vectors" section.

- `sizes.tsv`: file size, copier header (`yes`/`no`), image size (`-` if
  the file is not an image), outcome before hashing.
- `hashes.tsv`: case, SHA-256 of the image, outcome.
- `locations.tsv`: bank number and address (hex), image position (hex)
  or `none`, for a 4 MiB image.
