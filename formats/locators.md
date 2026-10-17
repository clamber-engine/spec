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
| Sound: sound-effect block | `sound-effects.md` | `c9` | `2d95` |
| Level ground: the first level's grid | `level-ground.md` | `d9` | `0000` |
| Level ground: the first level's type table | `level-ground.md` | `d9` | `ed00` |
| Level graphics: the first level's packed tile data | `graphics.md` | `d5` | `8fc0` |
| Level graphics: the first level's palette set | `graphics.md` | `f9` | `a1dc` |
| Level graphics: the first level's picture table | `graphics.md` | `d9` | `a3c0` |
| Sprite frames: the frame table | `sprites.md` | `fb` | `cc9c` |
| Placements: the first level's placement list | `placements.md` | `fd` | `95dc` |
| Level start: the first level's start record | `level-start.md` | `fc` | `bf78` |
| Level start: the first level's restart record | `level-start.md` | `fc` | `bfb4` |
| Level start: the first level's scene-return record | `level-start.md` | `fc` | `c09c` |
| Level start: the first level's room-return record | `level-start.md` | `fc` | `bf58` |
| View limits: the first level's record | `view-limits.md` | `fc` | `957a` |
| Level ground: the first level's room's grid | `level-ground.md` | `e3` | `0000` |
| Level ground: the first level's room's type table | `level-ground.md` | `d6` | `0000` |
| Level graphics: the first level's room's tile set, tiles 0 to 986 | `graphics.md` | `cf` | `84a0` |
| Level graphics: the first level's room's tile set, tiles 987 to 1 023 | `graphics.md` | `cf` | `0000` |
| Level graphics: the first level's room's screen map | `graphics.md` | `cf` | `7da0` |
| Level graphics: the first level's room's palette set | `graphics.md` | `f9` | `d683` |
| Placements: the first level's room's placement list | `placements.md` | `fd` | `d3c0` |
| Level start: the first level's room's start record | `level-start.md` | `fc` | `c090` |
| Level ground: the first level's scene's grid | `level-ground.md` | `da` | `0000` |
| Level ground: the first level's scene's type table | `level-ground.md` | `ec` | `15f4` |
| Level graphics: the first level's scene's packed tile data | `graphics.md` | `dc` | `0000` |
| Level graphics: the first level's scene's palette set | `graphics.md` | `f9` | `af65` |
| Level graphics: the first level's scene's picture table | `graphics.md` | `da` | `bf00` |
| Level graphics: the first level's scene's front tile set | `graphics.md` | `e7` | `fce9` |
| Level graphics: the first level's scene's front map | `graphics.md` | `e7` | `dce9` |
| Placements: the first level's scene's placement list | `placements.md` | `fd` | `d068` |
| Level start: the first level's scene's start record | `level-start.md` | `fc` | `c03c` |
| View limits: the first level's scene's record | `view-limits.md` | `fc` | `a5f2` |
| Level map: point tables | `level-map.md` | `fc` | `f47b` |
| Level map: corner records | `level-map.md` | `c0` | `d13c` |

### Kind fingerprints: placement references

Format spec: `placements.md` ("Kinds"). Kind names as in its "Kind
names" table.

| Fingerprint | Kind |
|---|---|
| `064bbe75bd3abfab4b38a24ad7b839fc30bdfc417023fd381ada77694e617ded` | `stinger` |
| `0d36c9a5953a166b2bbfafe60fc65cbe1abd9b66392a33c650daaeb3db1b9fe8` | `exit` |
| `0e1f45ccf4bc6e7a48005c73ce0003f0a0d0205fbb063ade26b4f1fe83877da0` | `stinger` |
| `12ddb2c3b0a218c0ccb5c1798e7094b5a2b61d21d2361da03b6f6286fa191e0a` | `restart-barrel` |
| `15ac2312a3f8d89089ee4c658cf1bee3df8b12254e7ebe4d7646b35028b932d6` | `letter-1` |
| `15e5d66f8306cbb5fc446248adeed6c91374c587a91b124a466964d3c2afd580` | `stinger` |
| `180898553bcc5b68269a895a68e20ed50a90d35c3a70a22caadff48695fbdd43` | `holder` |
| `1dd6e0d8ec2f8e99c24863107071b385b740a4d0fe8f4c5c43f37d52952db345` | `stinger` |
| `21186f287296d7b9c4bd4c75f0fbb40c006ecc509ea40745ba84abfa49e97f3b` | `stinger` |
| `253004ec8ef2f867d9bacc7dcc6abeb4fa4ae6dc86d98336db33c32e11196e3a` | `stinger` |
| `2c3d3e307d2fe5c18345e8308e49c7922732a9ec006ad6cd67900634117da740` | `animal-crate` |
| `334d93d686429e72e3c6a60e23570cd379765c0db2cae288375dc82355f418f7` | `tyre` |
| `3b1534a2da04c2655ee410d9570846bf8da60ecabf7afa758b2e5cab13c547a0` | `stinger` |
| `3e1f6c2f24fa461bc637adec8e62fe520d4f2a69e7cf8e0d950610235d2e3486` | `letter-4` |
| `3f9e1b957059a405568bfd11ba7757786263483c98b2a01bb8e22cc9ae833735` | `stinger` |
| `46a03d0df615a0aff54b9f2a823f9ead4e500b9ecdeb48c2fe2dfe37aa02431f` | `letter-2` |
| `4c90ae328510dac24fa35424acdaa6b793fcbebfa288220cce41179f518d9978` | `tyre` |
| `4cb625b24ccb2edc0d7487e5ff9aa9de2cd5409110383d64bfa5ff1a97a22170` | `large-walker` |
| `5fae0c706628b184dc3d2db99a589d70ce53d54750e685369dd03f73f71e7f06` | `stinger` |
| `68ec177ef637f9d79142cd826f4de8d5b14836aa0067cee144d2c606eeaf9c52` | `letter-3` |
| `705fd77a10893864d58f32885b437d1e741c3b935279dd4648a7c2282548d067` | `barrel` |
| `765a7a9517d401764d141421814606b3c711a924352de8ea10d9878433fe40de` | `stinger` |
| `7a0a5f16cbb9153611411aa5c6ed1aa024f8f2af6b87a221073c56f814c33e3d` | `thrower` |
| `7d89c7e6b0041ab373a3e96810740ef610b1e112d06596aef72ff8fa0fe46b41` | `hidden-bunch` |
| `814362811b6cd4c8fc220ee7fbd04eb626f6837ef7d7e5dc0027700380948338` | `bunch` |
| `82dee59bc51d643e34964b5adebf3c9ae39a4fcb4c321a22026922108e32a052` | `figure` |
| `8aad3408aa49bd9ceda4b82bd21591b32c7417dd4d4971eba27e30fd47eaf42c` | `entrance` |
| `9338801286daea12e1f30fd2478b61a44ef3325da9a893f865e2dff0b43c9cc7` | `hidden-barrel` |
| `96815bf167d1e7ae0187cf583b5e9cd05af19646c808ad1b8abb2fe09ad30e52` | `stinger` |
| `a18b370140438f25f467370655a5c802ccd40f07be7e5296c81570fd169a9add` | `medium-walker` |
| `a3917b33a073e05c714b57e8b4ca93dc514015f068d5d3ae7c8e89e64d7bcf08` | `stinger` |
| `a589d75d56211618a7b72120ff23715bf7dbfce7afe68a106e75170cc54be675` | `end-marker` |
| `b0b7540b3c061784c8c319b69b0c7db7ed9c67a84d094cefd7b05fa52dcbe7a4` | `small-walker` |
| `bc061647de9b607ad9eb002282563c85bb821236cf557781087f987a0ba862aa` | `small-walker` |
| `c8337d0771ac9e0feb76d90747123b32fba93ad3fe8fc5db12cd8a1a647b5910` | `medium-walker` |
| `ca241289eec0b66d060f41c4a59b57f87638faf55b0b2b7aa6082d5dafed37af` | `exit` |
| `cc9382a3b49fbd2580834652575cf6c0fb7129761622b5cf7261081c549f25e6` | `hidden-metal-barrel` |
| `d95f1da6f034e0484756a23152ad6c3ecdc3bf418860bc083e55a33ef6cf6abc` | `stinger` |
| `e2e5bad069370a387793f29708000e57f265a85fa38b598be9d019dec4259cc9` | `end-marker` |
| `f44d17c1cc692790ebee841de128717e08b078d09eb11b1931a12c05fbe5c81c` | `stinger` |
| `f4b1fad5059ecd6ac05c3a59a129e6b47adf0feabddab5c437e06aec2b6214e2` | `tyre` |
| `f85e5c914a63c73583c210056c33f883bcd8fecee32b42323bb458fb6f174819` | `ball` |

## `title-2`

SHA-256 of the image:
`b79c2bb86f6fc76e1fc61c62fc16d51c664c381e58bc2933be643bbc4d8b610c`

| Data | Format spec | Bank | Address |
|---|---|---|---|
| Level ground: the first level's packed map | `level-ground.md` ("In `title-2`"), `nibble-packing.md` | `e3` | `e006` |
| Level ground: the first level's type table | `level-ground.md` ("In `title-2`") | `fc` | `6154` |
