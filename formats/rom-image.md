# Image files

Titles: `title-1`

How the importer recognizes a supported image of a title, checks that it
is complete and unmodified, and turns a **location** (the form in which
the other format specs give positions of data) into a position in the
image. Vectors: `../vectors/rom-image/`.

## Supported releases

The importer supports one release per title: the release that the
title's behavior files and reference traces were measured on
(`../TITLES.md`). So far that is `title-1` only.

| Title | Image size | SHA-256 of the image |
|---|---|---|
| `title-1` | 4 194 304 bytes (4 MiB) | `628147468c3539283197f58f03b94df49758a332831857481ea9cc31645f0527` |

Other releases of the same title (other revisions or regions) are not
supported. They are told apart from the supported one by the hash alone;
the importer doesn't need to know which release an unsupported image is.

## Files and images

A **file** is what the player gives the importer. The **image** is the
data the importer reads, which is the file with any copier header
removed:

1. If the file's size divided by 1024 leaves 512, the file starts with a
   512-byte **copier header**. The image is the file without its first
   512 bytes. What the copier header holds is ignored; it can be
   anything.
2. Otherwise, if the file's size divided by 1024 leaves 0, the image is
   the whole file.
3. Otherwise the file is not an image (outcome `not-an-image`).

File names and extensions are ignored.

## Identifying an image

1. Find the image as in "Files and images".
2. If the image's size isn't the size in "Supported releases", the
   outcome is `wrong-size`.
3. Take the SHA-256 of the whole image (the copier header is not
   included). If it is in "Supported releases", the outcome is that
   title id. Otherwise the outcome is `unsupported`.

The importer only reads data from an image whose outcome is a title id.
A hash match is also the check that the image is complete and
unmodified: a missing, extra or changed byte anywhere in the image gives
another hash. A copier header doesn't change the outcome, so an image
with one and the same image without one are the same supported image.

Images stored in other arrangements (split into several files, or with
their 64 KiB blocks in another order) are not supported; they give
`unsupported` or `wrong-size`.

## Banks and locations

The image is a run of **banks** of 64 KiB (65 536 bytes) each: bank
block `n` is image bytes 65 536`n` to 65 536`n` + 65 535. A 4 MiB image
has 64 bank blocks, 0 to 63.

Other format specs give positions of data in the image as
**locations**. A location is a **bank number** `b` (0 to 255) and an
**address** `a` (0 to 65 535). Stored in data, a location takes 3 bytes:
the address, low byte first, then the bank number. When a format says a
2-byte address is in "the same bank" as something else, the location is
that bank number with the stored address.

A bank number names a bank block, but several bank numbers name the
same block, and some don't name the image at all. With `n` = `b` modulo
64 (the bank block):

| Bank numbers | Addresses | Image position |
|---|---|---|
| 192–255 | 0 to 65 535 | 65 536`n` + `a` |
| 64–125 | 0 to 65 535 | 65 536`n` + `a` |
| 0–63 and 128–191 | 32 768 to 65 535 | 65 536`n` + `a` |
| 0–63 and 128–191 | 0 to 32 767 | Not in the image |
| 126 and 127 | any | Not in the image |

So in the bank numbers 0–63 and 128–191, only the second half of each
bank block can be reached. A location that isn't in the image, or whose
position is past the end of the image, gives no data; a format that
reaches one has met data it doesn't describe.

`locations.tsv` in the vectors lists bank numbers and addresses in hex,
with the image position in hex or `none`.

## Vectors

All three vector files are synthetic: no file in them is or holds an
image of any game.

- `sizes.tsv`: file sizes, whether the file has a copier header, the
  image size (`-` for no image) and the outcome before hashing
  (`not-an-image`, `wrong-size`, or `image` if hashing would come next).
- `hashes.tsv`: SHA-256 of the image and outcome for 4 MiB files made
  as follows, where byte `i` of pattern `k` is (7`i` + `k`) modulo 256
  for `i` from 0 to 4 194 303:
  - `A`: pattern 3, no copier header.
  - `A-copier-zero`: 512 bytes of 0, then pattern 3.
  - `A-copier-ff`: 512 bytes of 255, then pattern 3. All three `A` cases
    have the same image and hash.
  - `B`: pattern 3 with byte 2 097 152 set to 0.
  - `C`: pattern 4.
  None of them is supported, so every outcome is `unsupported`.
- `locations.tsv`: see "Banks and locations".

## Not covered

- Where in the image each kind of data starts. Each format spec that the
  importer reads will say how its data is found, using locations.
- Other releases of each title, and other titles than `title-1`.
