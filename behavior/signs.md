# Signs

Titles: `title-1`

A **sign** is a board on a post placed in a level. It is scenery: it
stands where it is placed and nothing in play touches it. Characters,
walkers and thrown objects pass it as if it weren't there, it is never
taken, it doesn't hurt, and it changes no count. This file covers where
a sign is, when it appears and is removed as the view moves, and what
it does to play (nothing).

Frames are logic frames at 60 Hz, numbered as in a trace. `x` grows to
the right. Horizontal positions are level positions as in `view.md`.
Vertical positions are **level y** as in `view-vertical.md`.

The first level holds two signs, near its end, with different pictures
(one shows an arrow, the other a word). The first level's scene
(`exits.md`, "The scene") holds a third, with a picture of its own (a
board with words on it). All three are of the one kind `sign`; the
picture belongs to the reference ("Pictures"). Measured first with the first character leading and alone (the
sections before "Two characters"), then with two characters ("Two
characters"), using the
level's own signs and, in a copy of the image, copies of each of the
three placed near the level's start (see "Checks").

## A sign's position

A level's placement list places each sign with an entry of kind `sign`
(`../formats/placements.md`, "Kinds"). A sign's position is its entry's
position: level x `x` and level y `Yb` − `h`
(`../formats/placements.md`, "Positions"). A sign never moves: in every
frame of every run in "Checks" it was at its entry's position.

## Appearing and being removed

`V` is the view's position, rounded down to a whole pixel, as it was at
the start of the frame, and the **window** is as in
`../formats/placements.md` ("When placed objects appear"):
−31 ≤ `x` − `V` ≤ 288.

- **Appearing.** A sign that isn't there appears on the first frame on
  which its entry is in the window, as for every class 1 or class 2
  entry (`../formats/placements.md`). On the level's first frame, the
  signs already in the window are there.
- **Removal.** A sign that is there is removed on the first frame on
  which `x` − `V` ≤ −128 or `x` − `V` ≥ 385. These are the distances at
  which a walker that is away from its start is removed
  (`walker-view.md`); a sign is always at its start. This holds while
  the sign is in the vertical window. A sign above or below it is
  removed as soon as its entry is out of the window
  (`../formats/placements.md`, "When placed objects are removed").
- **Coming back.** A removed sign appears again, as above, the next
  time its entry is in the window. This can happen any number of times.

So while `x` − `V` is from −127 to −32 or from 289 to 384, a sign is
there if it was there on the frame before and not there if it wasn't.

Measured edges: a sign was there at `x` − `V` = −127 and 384 and removed
at −128 and 385; it appeared at 288 coming in from the right (not at 289
or 290) and at −31 coming in from the left (not at −32 or −33).

## What a sign does

Nothing. With a sign in the picture, play went exactly as with the same
sign placed out of reach (see "Checks"): the characters' positions,
speeds, actions and facing, the lives, the collectible count and its
display, the figure count, the letters taken, and every other object
were the same on every frame. In particular:

- The player walks, runs, rolls, crouches and jumps through a sign,
  and lands where it stands as if it weren't there. It is neither a
  wall nor a floor, and it isn't taken.
- Walkers walk past a sign without turning.
- A thrown object flies past a sign without breaking, bouncing or
  slowing.

In the pictures checked (the lead standing at each of the two signs),
the sign was drawn over the lead where they overlapped.

This holds with two characters in the level, whichever leads: the
second character leading, or the first leading with the second
following, a sign changed nothing on any frame, and it appeared and was
removed at the same distances ("Two characters" below).

## The scene's sign

The scene's placement list (`../formats/placements.md`, "In the first
level's scene") places its sign with its class 1 entry. In the scene the
picture doesn't move and every entry is in the window
(`level-ends.md`, "In the first level's scene"), so the sign is there,
at its entry's position, from the scene's frame 1 to its last frame
(frames numbered as in `exits.md`, "Walking in"), and isn't there on
frame 0. It never moves and is never removed there. The lead walks in
from the right and stops right of the sign (`exits.md`, "The scene"),
so the lead never reaches it.

Placed in the first level, the same reference places a sign that does
everything above: it appears and is removed at the same distances, and
it changes nothing in play.

## Pictures

What a sign looks like in `title-1`. Each picture has a label used in
this file and is named by its picture hash (`../formats/sprites.md`,
"Picture hashes"); it is drawn at the sign's position as
`../formats/sprites.md` says ("Where the picture is drawn"). A sign's
picture is the same on every frame, is never mirrored, and doesn't
depend on where the sign is or what is near it. Which picture a sign
has depends on its entry's reference, not on its kind: the three signs
of the first level and its scene have three different references and
three different pictures.

| Sign | Kind fingerprint (`../formats/locators.md`) | Picture | Picture hash (SHA-256) |
|---|---|---|---|
| The first level's sign nearer the start (the arrow) | `fc06b850598f866e68da84373087bd70260e200cd74a79edd39e9ee7612b91e3` | `sign-arrow` | `110f23c18c9c8cf82cec48cdd2ebca015fe391a76f7ce71b964e62a809813980` |
| The first level's sign nearer the end (the word) | `e332f1969a9e6ec35546e8ef037006fd8d1d4bbc9933646879072cc1f3a10009` | `sign-word` | `2a039daf6c8545e9ade6a6db934c638715c910108dce6a50331b3b50e2e4be9b` |
| The scene's sign (the board with words) | `47a9fc70cf6be73792670830d89523401fe75b8ad5ade692f5fe8d534962429c` | `sign-scene` | `2e20c598fa6aa63334469cb9a49445140fb9799c69b6db55840c4a79b8c68d06` |

The importer gives a placed sign the picture in the row whose
fingerprint is the one that matched its entry's reference
(`../formats/placements.md`, "The fingerprint of a reference"). The
three fingerprints are the three `sign` rows of `title-1` in
`../formats/locators.md`; each row of this table is one of them and
each picture belongs to one fingerprint only.

**When a hash isn't found.** The importer looks for every hash in this
table. If any isn't found, the engine shows its own art for signs,
keeping the position and removal given above.

Measured by the pictures on screen, with the first character walking
right and a copy of the image with the entry of one reference on the
floor at level x 800 (each of the three references, two walks each, six
runs). The sign was wholly inside the screen and recognisable on 684
frames per reference in the walk that kept it in view: every one showed
the picture of the table, unmirrored, and no other. The frames on which
the sign was partly outside the screen (26 per reference) were not
compared. In the second walk (399 frames per reference with the sign in
the window) it was partly outside the screen on every frame, so it
added nothing.
The signs in the level's own list were not read on screen; the
fingerprints of their entries' references are the ones in the table
(checked by computing each entry's candidate fingerprints as
`../formats/placements.md` says).

## Checks

Each check compared two copies of the image, the same but for one
entry's height: in one the sign stood on the floor, in the other the
same entry put it 1 000 px higher, out of reach. Both were played with
the same inputs, one input per logic frame, and compared on every frame
until play stopped (a knock-out's fade ends a run).

- **The lead.** A copy of each sign placed on the floor near the level's
  start: 60 runs per sign of 900 frames of random inputs (walking and
  running both ways, crouching, rolling, jumping, the ground action,
  standing still) after walking towards the sign, 54 459 frames per
  sign, 10 923 of them with the lead within 48 px of the sign
  horizontally and 80 px vertically. Differences: 0.
- **Walkers.** A copy of each sign placed on the floor on which a small
  walker patrols: 31 runs per sign, 22 888 frames per sign, about
  3 800 of them with a walker within 24 px of the sign. Differences: 0.
- **Thrown objects.** A copy of each sign placed on the floor, with a
  barrel placed before it in both copies: the lead picks up the barrel
  and throws it right through the sign, then 900 frames of random
  inputs; 30 runs per sign, 27 253 frames per sign. The barrel passed the
  sign in every run. Differences: 0.
- **Appearing and removal.** The two copies near the level's start and
  the level's own signs, walking, running and jumping away from and
  back to them in both directions, 41 runs: 10 920 frames, with 21
  appearances (14 from the right, 7 from the left) and 20 removals (11
  on the right, 9 on the left). On every frame the sign was there
  exactly when the rules above say.
- **The scene's sign.** The same lead, walker and thrown-object checks
  as above, with a copy of the scene's sign placed near the level's
  start (the lead and the thrown object) and on the small walker's floor
  (walkers): 54 459, 22 888 and 27 253 frames, with 10 923 frames with
  the lead near the sign, 3 802 with a walker within 24 px, and the
  barrel passing the sign in all 30 runs. Differences: 0. Appearing and
  removal, walking, running and jumping away from and back to the copy
  in both directions: 14 runs, 7 352 frames, 18 appearances (15 from
  the right, 3 from the left) and 15 removals (4 on the right, 11 on
  the left), each exactly when the rules above say. In the scene
  itself, in a run with the lead alone and one with two characters (397
  frames each), the sign was at its entry's position on every frame
  from frame 1 on.

## Two characters

Both signs, copies placed on the floor ahead of a pair of characters
(nothing else in the checks was changed), same method as above, 30 runs
of 900 frames of random inputs per sign and per arrangement:

- **The second character leading, the first following.** 53 942
  frames in all, 4 424 of them with the lead within 48 px of the sign.
  Differences: 0.
- **The first character leading, the second following.** 54 504
  frames, 9 018 with the lead within 48 px. Differences: 0.

Appearing and removal, walking and running (and jumping) towards a copy
and away from it with a pair: with the first leading, 8 runs (the two
signs, four walks each) of 500 to 1 050 frames, 10 appearances (all from the
right except 2 from the left after turning back) and 6 removals; with
the second leading, 4 runs of 400 to 728 frames, 4 appearances and 4
removals. Each happened exactly when the rules in "Appearing and being
removed" say.

## Not covered

- Signs with the second character leading and the first following in
  the scene, and with the characters swapping while a sign is in the
  window.
- Signs far above or below the view. The copy placed 1 000 px above
  its floor was missing on some frames on which its entry was in the
  window.
- Enemies other than small walkers, the thrower's balls, the animal and
  riding, and objects other than a thrown barrel meeting a sign.
- Signs in other levels, and the draw order of a sign and other
  objects beyond what "What a sign does" says. The pictures were read
  with the first character only.
