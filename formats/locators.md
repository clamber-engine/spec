# Locators

Where each kind of data the importer reads starts in a supported image
(`../REVIEW.md`, "The one exception: locator tables"). One section per
supported image, named by its title id and identified by the SHA-256 of
the image (`rom-image.md`). Each row gives a location as in
`rom-image.md`: a bank number and an address, both in hex. How the data
is laid out from there, including counts and entry sizes, is in the
format spec named in the row.

## `title-1`

SHA-256 of the image:
`628147468c3539283197f58f03b94df49758a332831857481ea9cc31645f0527`

| Data | Format spec | Bank | Address |
|---|---|---|---|
| Sound: sample table | `sound.md` | `c9` | `ff00` |
| Sound: shared sample list | `sound.md` | `ca` | `b24a` |
| Sound: sample-set table | `sound.md` | `ca` | `b212` |
| Sound: song table | `sound.md` | `ca` | `b15e` |
