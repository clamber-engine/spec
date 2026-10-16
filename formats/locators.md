# Locators

Where each kind of data the importer reads starts in a supported image
(`../REVIEW.md`, "The one exception: locator tables"). One section per
supported image, named by its title id and identified by the SHA-256 of
the image (`rom-image.md`). Each row gives a location as in
`rom-image.md`: a bank number and an address, both in hex. How the data
is laid out from there, including counts and entry sizes, is in the
format spec named in the row.

A section can also list **kind fingerprints** (`../REVIEW.md`, "The
second exception: kind fingerprints"): fingerprints and the
engine-neutral kinds they stand for. How a fingerprint is computed is in
the format spec named above the table.

## `title-1`

SHA-256 of the image:
`628147468c3539283197f58f03b94df49758a332831857481ea9cc31645f0527`

| Data | Format spec | Bank | Address |
|---|---|---|---|
| Sound: sample table | `sound.md` | `c9` | `ff00` |
| Sound: shared sample list | `sound.md` | `ca` | `b24a` |
| Sound: sample-set table | `sound.md` | `ca` | `b212` |
| Sound: song table | `sound.md` | `ca` | `b15e` |

### Kind fingerprints: placement references

Format spec: `placements.md` ("Kinds"). Kind names as in its "Kind
names" table.

| Fingerprint | Kind |
|---|---|
| `15ac2312a3f8d89089ee4c658cf1bee3df8b12254e7ebe4d7646b35028b932d6` | `letter-1` |
| `180898553bcc5b68269a895a68e20ed50a90d35c3a70a22caadff48695fbdd43` | `holder` |
| `3e1f6c2f24fa461bc637adec8e62fe520d4f2a69e7cf8e0d950610235d2e3486` | `letter-4` |
| `46a03d0df615a0aff54b9f2a823f9ead4e500b9ecdeb48c2fe2dfe37aa02431f` | `letter-2` |
| `4cb625b24ccb2edc0d7487e5ff9aa9de2cd5409110383d64bfa5ff1a97a22170` | `large-walker` |
| `68ec177ef637f9d79142cd826f4de8d5b14836aa0067cee144d2c606eeaf9c52` | `letter-3` |
| `705fd77a10893864d58f32885b437d1e741c3b935279dd4648a7c2282548d067` | `barrel` |
| `7a0a5f16cbb9153611411aa5c6ed1aa024f8f2af6b87a221073c56f814c33e3d` | `thrower` |
| `814362811b6cd4c8fc220ee7fbd04eb626f6837ef7d7e5dc0027700380948338` | `bunch` |
| `a18b370140438f25f467370655a5c802ccd40f07be7e5296c81570fd169a9add` | `small-walker` |
| `b0b7540b3c061784c8c319b69b0c7db7ed9c67a84d094cefd7b05fa52dcbe7a4` | `small-walker` |
| `bc061647de9b607ad9eb002282563c85bb821236cf557781087f987a0ba862aa` | `small-walker` |
| `c8337d0771ac9e0feb76d90747123b32fba93ad3fe8fc5db12cd8a1a647b5910` | `small-walker` |
| `e2e5bad069370a387793f29708000e57f265a85fa38b598be9d019dec4259cc9` | `end-marker` |
| `f85e5c914a63c73583c210056c33f883bcd8fecee32b42323bb458fb6f174819` | `ball` |
