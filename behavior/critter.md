# Critters

Titles: `title-1`

A **critter** is a small winged creature that flies about in a level.
It is scenery: nothing in play touches it. Characters, the animal,
walkers and thrown objects pass it as if it weren't there, it is never
taken, it doesn't hurt, and it changes no count. This file covers when
a critter appears and is removed as the view moves, where it appears,
how it flies, and what it does to play (nothing).

Frames are logic frames at 60 Hz, numbered as in a trace. `x` grows to
the right. Horizontal positions are level positions as in `view.md`.
Vertical positions are **level y** as in `view-vertical.md`; `y` grows
downwards.

The first level holds two critters, both of the one kind `critter`: one
near the level's start and one just right of the animal crate.
Measured with the first character leading and alone, using the level's
own critters and copies of them (see "Checks").

## Positions used

- `S` is the critter's entry's level x and `E` its level y
  (`../formats/placements.md`, "Positions").
- `x` is the critter's own level x and `y` the level y at which it is
  drawn, both kept to 1/256 px. `X` is `x` rounded down to a whole
  pixel.
- `V` is the view's position, rounded down to a whole pixel, as it was
  at the start of the frame, and `U` the view's vertical position (the
  level y of the picture's top row, `view-vertical.md`), rounded down
  the same way.
- The **window** is as in `../formats/placements.md` ("When placed
  objects appear"): a horizontal position `p` is in it when
  −31 ≤ `p` − `V` ≤ 288.

⌊ ⌋ rounds down and ⌈ ⌉ rounds up to a whole number.

## Appearing

A critter that isn't there appears on the first frame on which its
entry is in the window, as for every class 1 or class 2 entry
(`../formats/placements.md`, including the rule for `V` less than 32).

Unlike other placed objects, a critter doesn't appear at its entry's
position. On the frame `F` on which it appears, two whole numbers are
chosen at random: `r` from 0 to 255 and `q` from 1 to 512. The critter
starts with (the names are those of "Flying"):

- `x` = `V` + `r`: somewhere across the picture's width;
- `ux` = 4`r` if `r` is less than 128, and 4(`r` − 256) otherwise: a
  horizontal speed of up to 2 px per frame, to the right for `r` from 1
  to 127 and to the left for `r` from 128 to 255;
- a flight height `z` = `U`: the picture's top row (with a fraction of
  less than 1 px that this file doesn't fix; see "Not covered");
- `uy` = `q`: from 1/256 to 2 px per frame, downwards;
- its target (`A`, `B`) = (`S`, `E`), its entry's position;
- and, for its first move, the drawn `y` of "the frame before" taken as
  `E`, so that step 2 of that move sees no vertical distance.

It makes its first move on `F` itself, so on `F` it is already drawn
after one frame of flight: just below or above the picture's top row
(in the 277 appearances measured during play, `r` was from 1 to 246
and `q` from 6 to 511, and `y` − `U` was from −7 to 2).

**At the level's start.** The critter near the first level's start
has its entry in the window on the level's frame 0. It appears on
frame 0 as above, with 0 in place of both `V` and `U`, but it doesn't
fly on frames 0 to 40: on those frames it is drawn at `x` = `r` and at
its entry's level y `E`. It flies from frame 41, the first move again
seeing no vertical distance (it was drawn at `E` on frame 40). In the
40 runs measured, every one entered from the map in the same way, `r`
was the same each time (so it stood 90 px left of its entry).

## Flying

A critter flies towards a **target** (`A`, `B`), a point given in whole
px. It keeps a horizontal speed `ux` and a vertical speed `uy`, in
1/256 px per frame (`ux` positive to the right, `uy` positive
downwards), and a **flight height** `z`, a level y kept to 1/256 px. It
is drawn at (`x`, `y`) with `y` = `z` − `f`, where `f` is the frame's
flutter (below).

On each frame on which it flies, in this order:

1. `ux` ← `ux` + ⌊(16 × (`A` − `X`) − `ux`) / 32⌋.
2. `uy` ← `uy` + ⌈(4 × (`B` − ⌈`y`⌉) − `uy`) / 64⌉, with `y` where it
   was drawn on the frame before.
3. `x` ← `x` + `ux` / 256 and `z` ← `z` + `uy` / 256.
4. `y` ← `z` − `f`.
5. If −4 ≤ `X` − `A` ≤ 3 and −3 ≤ ⌈`y`⌉ − `B` ≤ 4 (with the new `x`
   and `y`), it **perches** (below).

So each speed is pulled a part of the way towards a speed that grows
with the distance to the target: horizontally towards 1/16 of the
distance per frame, closing 1/32 of the difference each frame;
vertically towards 1/64 of the distance per frame, closing 1/64 of the
difference. The critter overshoots the target and swings back about it
until it comes close enough to perch.

**The flutter** `f` takes the values 0, 2, 4, 6, 7, 5, 3, 1 px in turn,
one per frame, and then starts again. On frame `n` of the level,
counting frame 0 as when the level is entered from the map, `f` is the
value at place (`n` + 2) mod 8 of that list, counting the first as
place 0. So `f` is 4 on frame 0, 6 on frame 1 and 0 on frame 6. The
list goes on with the frames, also on frames on which the critter
doesn't fly. It raises the picture without changing `z`.

**Perching.** On the frame `P` on which it perches the critter is drawn
where step 4 put it. From `P` + 1 to `P` + 359 it stays exactly there:
it doesn't move and its drawn `y` doesn't change (the flutter doesn't
show). It flies again from `P` + 360, starting with step 1. On `P`
itself its target becomes (`A` − 64, −16), 64 px left of the old target
and at level y −16, and its speeds become `ux` = 512 (2 px per frame to
the right) and `uy` = 256 (1 px per frame down), which it has when it
starts again on `P` + 360.

So a critter flies down to its entry's position, perches there, rises
to a point 64 px further left at level y −16 and perches there, and
then moves 64 px further left at that height each time, perching for
359 frames between moves, until it is removed ("Removal"). Nothing else
changes its path: the characters, the animal and other objects don't
(see "What a critter does").

**Held still.** On the frames on which the game holds the other objects
still, a critter doesn't fly either: its position, speeds, flight
height and drawn `y` stay as they were (the flutter list still goes on,
so its next flying frame uses that frame's `f`). In the runs these were
the two frozen frames after a bounce (`bounce.md`) and those after a
roll defeats a walker (`rolling-into-walkers.md`), frames H + 1 to
H + 10 of a knock-out (`knock-out.md`), and the 33 frames while a freed
character joins (`holder-breaking.md`, "Everything else waits").

### Example (synthetic)

A critter's entry is at (400, 120). It appears on frame 1000 with `V` =
300 and `U` = 40, and the random numbers are `r` = 40 and `q` = 256. So
it starts with `x` = 340, `ux` = 160, `z` = 40 (taking the fraction as
0), `uy` = 256, target (400, 120), and `y` counting as 120 before the
first move.

| Frame | `ux` | `uy` | `x` | `z` | `f` | `y` |
|---|---|---|---|---|---|---|
| 1000 | 185 | 252 | 340.72265625 | 40.984375 | 4 | 36.984375 |
| 1001 | 209 | 254 | 341.5390625 | 41.9765625 | 6 | 35.9765625 |
| 1002 | 231 | 256 | 342.44140625 | 42.9765625 | 7 | 35.9765625 |
| 1003 | 252 | 258 | 343.42578125 | 43.984375 | 5 | 38.984375 |

On frame 1000, step 1 gives 160 + ⌊(16 × 60 − 160) / 32⌋ = 160 + 25 =
185, and step 2 gives 256 + ⌈(4 × 0 − 256) / 64⌉ = 256 − 4 = 252. On
frame 1001, step 2 uses ⌈36.984375⌉ = 37: 252 + ⌈(4 × 83 − 252) / 64⌉ =
252 + 2 = 254.

Flying on with the same rules, it first meets the perching condition on
frame 1338, at `x` = 399.01171875, `y` = 123.23046875 (`X` − `A` = −1,
⌈`y`⌉ − `B` = 4). It stays there until frame 1697. Its target is now
(336, −16), and on frame 1698 `ux` = 512 + ⌊(16 × (336 − 399) − 512) /
32⌋ = 464 and `uy` = 256 + ⌈(4 × (−16 − 124) − 256) / 64⌉ = 244.

## Removal

A critter that is there is removed on the first frame on which **both**
of these hold:

1. its entry is outside the window (`S` − `V` < −31 or
   `S` − `V` > 288), and
2. the critter itself, after that frame's move, is outside the window:
   `X` − `V` < −31 or `X` − `V` > 288.

So a critter isn't removed while it is in the window, even if its entry
has left it, and isn't removed while its entry is in the window, even
if it has flown out of it. In the runs, critters whose entry was outside
the window were there with `X` − `V` from −31 to 288 (−31 on 33 frames,
288 on 82 frames) and were removed at −32 or less and 289 or more.

A removed critter appears again, as above, the next time its entry is
in the window. This can happen any number of times.

Once, a critter whose entry was outside the window was removed while
flying up past the top of the picture (`y` − `U` went from about −30
to below −31) with `X` inside the window. Removal above or below the
picture is not covered otherwise.

## What a critter does

Nothing. With a critter in the picture, play went exactly as with the
same critter's entry placed 1 000 px higher, out of reach (see
"Checks"): the characters' positions, speeds, actions and facing, the
lives, the collectible count and its display, the figure count, the
letters taken, and every object that isn't the critter's own were the
same on every frame, with one exception below. In particular:

- The player walks, runs, rolls, crouches and jumps through a critter,
  also when it is lowered to the player's height; it is neither a wall
  nor a floor, and it isn't taken.
- Riding the animal through it changes nothing.
- Walkers walk past it, and a thrown barrel flies past it.

The exception: the place of the sparkles shown at a letter
can differ when a critter has flown differently. With one set of
inputs a sparkle at a letter was 2 px left and 13 px lower than in the
other copy. Nothing else differed in those runs.

At times a second object belonging to the second critter appeared
about 4 px left of and 2 px above its entry's position. It was there in
one copy and not, or elsewhere, in the other in 11 of the 270 runs at
the second critter, and changed nothing else either. What it is and
when it appears are not covered.

## Pictures

What a critter looks like on each frame in `title-1`. Each picture has a
label used in this file and is named by its picture hash
(`../formats/sprites.md`, "Picture hashes"); it is drawn at the critter's
position (`x`, `y`) as `../formats/sprites.md` says ("Where the picture
is drawn"). The picture given for a frame is the one on screen with that
frame's view.

| Picture | Picture hash (SHA-256) |
|---|---|
| `wing-1` | `f5dc4566a5cd0aefdf573d5b3fde4e9df3f20b436d7a67ce22e84a483e7f4dbe` |
| `wing-2` | `4fb1c6f34cd5b0466291d63064abccb9bb9f0e4058e6615e18b8b2d0654fc488` |
| `wing-3` | `986fac57723bd4b5141f4529276970ff849a28cbb39c03288ed29d1b32b727a9` |
| `wing-4` | `e7acc228c0b58aba59dd34a0e00d07c03d21f14db67d8a42994b8b711baf6b9d` |
| `wing-5` | `8112530ba12148673fcddad550fab64428a60ff128cd0c17a2c015bdab3dd81a` |
| `wing-6` | `ded3e0d8d279162659dc17197b1ff23a10ca22e22c87d96d3243a08623edbfea` |

**When a hash isn't found.** The importer looks for every hash in this
section. If any of the six isn't found, the engine shows its own art for
the critter on every frame, keeping the timing and mirroring given here.

**Mirroring.** On a frame on which it flies, the critter is drawn
mirrored left–right if its horizontal speed `ux` (after step 1) is
negative, and not mirrored if `ux` is 0 or positive. On a frame held
still, and while it is perched, it is drawn as on the frame before.

**Flying.** Its pictures step once on each frame on which it flies.
Counting those frames from `k` = 0 on the frame `F` on which it
appeared during play, and again from `k` = 0 on the frame `P` + 360 on
which it flies again after a perch, the pictures repeat every 10
frames: `wing-2`, `wing-3`, `wing-4`, `wing-5`, `wing-6`, `wing-5`,
`wing-4`, `wing-3`, `wing-2`, `wing-1` (`k` mod 10 = 0 to 9). Frames
held still keep the picture they had and are not counted.

**Perched.** From `P` to `P` + 359, counting `a` = 0 on `P`:

| `a` | Picture |
|---|---|
| 0 to 79 | `wing-1` |
| 80 to 103 | `wing-2` × 6, `wing-3` × 6, `wing-4` × 6, `wing-5` × 6 |
| 104 to 154 | `wing-6` × 51 |
| 155 to 178 | `wing-5` × 6, `wing-4` × 6, `wing-3` × 6, `wing-2` × 6 |
| 179 to 259 | `wing-1` × 81 |
| 260 to 283 | `wing-2` × 6, `wing-3` × 6, `wing-4` × 6, `wing-5` × 6 |
| 284 to 334 | `wing-6` × 51 |
| 335 to 358 | `wing-5` × 6, `wing-4` × 6, `wing-3` × 6, `wing-2` × 6 |
| 359 | `wing-1` |

On `P` + 360 the flying list starts again at `k` = 0 with `wing-2`. The
pictures don't depend on the critter's entry, where it is or what the
player does. A critter that is removed and appears again starts its list
again from its new `F`.

Measured by the pictures on screen: 18 runs of 2 000 video frames
(standing still, walking, running and jumping both ways near both
critters), with 30 appearances during play, 21 perches, and critters
removed and coming back. On 2 181 frames on which the critter flew and
was drawn whole on screen and recognisable, 2 114 showed the picture
above with the mirroring above. The other 67 showed the picture the
list gives 1 frame earlier (50 frames) or 2 frames earlier (17), and no
other picture. On 793 perched frames every one showed the picture
above. Frames on which the critter was not wholly inside the picture
(2 370), or was covered in part (19), or was not recognised (22), or
nothing was drawn (15) were not counted. With the list moved on by one
frame the same check matched 8 of the 2 181 flying frames.

## Checks

Each "does nothing" check compared two copies of the image, the same but
for the critter's entry's height: in one the critter was at its own
height or lowered to the lead's height, in the other the same entry put
it 1 000 px higher. Both were played with the same inputs, one input per
logic frame, and compared on every frame until play stopped (a
knock-out's fade ends a run). The critter itself was left out of the
comparison.

- **The lead.** The level's second critter at its own height and
  lowered to two heights at which it flies around the lead: 60 runs
  per height of 900 frames of random inputs (walking and running both
  ways, crouching, rolling, jumping, the ground action, standing still,
  breaking the animal crate and riding the animal) after walking
  towards it, 47 513 frames per height, with the lead within 32 px of
  the critter horizontally and from 16 px below to 48 px above it on
  1 916, 3 499 and 5 044 of them, and the animal within 24 px of it on
  about 1 100 frames at each lowered height (within 24 px
  horizontally and 40 px vertically).
- **The level's start.** The first critter from the level's first
  frame, at its own height and lowered: 30 runs per height, 27 146
  frames per height, about 1 200 with the lead near it.
- **Walkers.** The lowered second critter with the small walkers to its
  right: 30 runs, 12 690 frames, 2 700 of them with a walker within
  24 px of the critter.
- **Thrown objects.** The animal crate's entry made a barrel in both
  copies: the lead picks it up, carries it and throws it right through
  the critter, then 900 frames of random inputs; 30 runs at each of two
  heights, 23 487 frames per height.

In all these runs (330 runs, 256 495 frames), the characters, lives and
counts never differed. Other objects differed only as said in "What a
critter does": the sparkle in 3 runs (one set of inputs, at the three
heights of the second critter) and the critter's second object in 11
runs.

- **Appearing and removal.** Random walks, runs and jumps both ways
  around both critters, from four places in the level and from the
  level's first frame, 340 runs: 208 120 frames, 304 appearances and
  443 removals. On every frame the critter was there exactly when the
  rules above say (the critter is gone on the frame of its removal,
  so there `X` was taken as its x on the frame before plus its speed
  then, give or take 1 px: 440 of the 443 removals needed no margin), but for the one removal above the picture.
- **The second character leading.** The second critter, from a start
  near it with the second character leading and the first following,
  with the critter at its usual height and, using a setup method, flying
  at two lower heights around the lead: 30 runs per height of 900 frames of random inputs as for the
  first character, 26 971 frames per height, with the lead within 32 px
  of the critter horizontally and from 16 px below to 48 px above it on
  1 100, 2 069 and 2 297 of them. The characters, lives and counts never
  differed, and other objects differed only as said in "What a
  critter does". The 30 plain random walks (900 frames each) gave 39 appearances and 37 removals, and on every frame
  the critter was there exactly when "Appearing" and the removal rule
  say. So those rules, and that a critter changes nothing, hold with
  the second character leading.
- **The kind.** The critter's reference given, in a copy of the image,
  to a class 1 entry near the level's start in place of its own placed
  a critter there, which appeared at the top of the picture and flew as
  above.

- **Flying.** 162 runs: 40 random walks of up to 2 400 frames (walking,
  running, jumping and standing still both ways) from each of four
  starts (near the first critter, two near the second, and the level's
  first frame entered from the map), and one run of 6 000 frames
  standing still near each critter; 231 551 frames, with a critter
  there on 76 677. Every frame was compared with the rules above,
  starting from what the critter was on the frame before: on all 76 278
  frames after the first one of each stay, its `x`, flight height,
  drawn `y`, speeds and target were as the rules give, counting
  1 815 frames held still (40 at the level's start in each of the 40
  runs from the map, and 215 frames in 23 holds during play: 5 after
  bounces, 3 after rolls into walkers, 12 knock-outs, 2 characters
  joining, and the one under "Not covered"). On all 277 appearances
  during play and the 40 at the level's start there were an `r` and a
  `q` that gave exactly what was seen. All 23 perches that started and
  ended in a run lasted to `P` + 359, flying again on `P` + 360.
## Not covered

- How `r` and `q` are chosen beyond being whole numbers in those ranges
  (any choice in them is a possible game), and the fraction of the
  flight height on appearing (the runs fitted with fractions from 0 to
  255/256 px).
- The flutter's place in its list when play doesn't begin by entering
  the level from the map (after a knock-out, after coming back through
  an exit).
- A perch, or the 41 frames at the level's start, that overlaps frames
  held still. One more hold was seen: the second character knocked out
  with the first already lost (`losing-a-character.md`); the critter
  stood still on 13 frames in a row from the frame after the hit, with
  the game's 32-frame pause after the tenth of them.
- Distances between a critter and its target of 2 048 px or more (not
  reached in the runs).
- Removal above or below the picture, and critters whose entry is far
  above or below the view.
- The second object that appeared near the second critter (see "What
  a critter does").
- The second character leading at the level's start, the critter's
  flight rules with the second character leading (only its appearing,
  removal and effect on play were checked), and the first character
  leading with the second following.
- Enemies other than small walkers, the thrower's balls, and objects
  other than a thrown barrel meeting a critter.
- Critters in other levels.
- The pictures on the 41 frames at the level's start (the critter is
  below the picture then, in the first level), and on frames on which a
  critter appears but is held still; a perch that overlaps frames held
  still.
