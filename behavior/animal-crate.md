# The animal crate

Titles: `title-1`

An **animal crate** stands on the floor. When the player lands on it
from above, it breaks and the player bounces off it, as off an enemy.
Shortly after, an **animal** comes out where the crate stood and waits
there. When the player lands on the animal from a jump or a bounce, the
player mounts it, and after a short pause rides it. This file covers
breaking the crate, the animal coming out, and mounting. Riding is not
covered yet.

Frames are logic frames at 60 Hz, numbered as in a trace. `x` grows to
the right. Horizontal positions are level positions as in `view.md`.
Vertical positions are **level y** as in `view-vertical.md`: they grow
downward. The player's **feet line** `F` is as in `view-vertical.md`
(the player's `y` rounded up). "Moving down" on a frame means that the
player's `vy` used for that frame's move is greater than 0. "Right",
"Left" and "Jump" are the neutral inputs `R`, `L`, `J` of the scenario
format.

Measured on the first level's crate with the first character alone, by
inputs only except where said:

- Ranges: the player was put at every whole-pixel offset around the
  crate, and around the animal, one pixel apart, in the air, facing
  either way, and run for one frame (about 113 000 placements). Around
  the crate: falling in a jump (also with the downward speed forced to
  values from 0.125 to 8 px per frame), rising in a jump, and in the
  walking, standing and rolling poses with a downward speed. Around the
  animal: the same, and falling after a bounce, and with the player at
  half-pixel positions.
- The ranges were then checked on 400 runs of random inputs (walking,
  running, jumping, rolling, crouching) around the crate, starting on
  either side of it, 134 205 frames: in every run the crate broke on the
  frame the rules give, the animal came out 18 frames later, and the
  player mounted on the frame the rules give, or not at all when they
  give no frame (400 breaks, all from jumps, 205 facing right; 399
  mounts, 261 from jumps and 138 from the bounce off the crate, 194
  facing right). For each frame of these runs the rules were applied to
  where the player would have been without the crate or the animal (the
  same frame run again with the object moved away).
- Mounting while the animal hops: the player was put at every
  whole-pixel offset around the animal on each frame from B + 17 to
  B + 23, facing either way, falling after a bounce at 3 px per frame
  (also at half-pixel positions, with the downward speed forced to 1
  and 8 px per frame, and rising), about 100 000 placements. Then at
  12 000 random positions, speeds and inputs on the frames B + 17 to
  B + 24, each run also with the animal moved away for the would-be
  position, the player mounted on the frame the rules give in every
  case (2 357 mounts, 87 of them with `D` above 45), and never
  otherwise. With the height range kept at 5 to 45 and mounting only
  from B + 22, 1 243 of the 12 000 cases differ. A player who has just
  bounced off the crate is still far above the animal on these frames;
  the cases were reached by placing the player.

## The crate

A level's placement list (`../formats/placements.md`) places the crate
with an entry of kind `animal-crate` ("Kinds" there). The crate does
not move. Its **position** (`xc`, `yc`) is the entry's level x, and the
entry's level y plus 1 (see "Positions" there). In the first level the
crate stands on the floor: `yc` is the level y of the floor under it.

Walking, running or rolling through the crate does nothing: the player
keeps their position, speed and action.

## Breaking the crate

Using whole pixels, let

- **`I`** = the player's `x` rounded down, minus `xc`;
- **`D`** = `yc` − `F`: how many px the player's feet line is above
  the crate's position.

The player breaks the crate on frame `n` when, **at the end of frame
`n`**, after the player has moved on that frame:

1. the player is in the air and was moving down on `n`;
2. the player is not rolling;
3. `I` is within the range for the player's facing on `n`, and `D` is
   within 5 to 36 (both ends included):

| Facing | `I` |
|---|---|
| right | −28 to 40 |
| left | −42 to 26 |

Facing left, the range is the facing-right range turned around and
moved 2 px to the left: from −(high end) − 2 to −(low end) − 2.

The ranges were the same in the jump pose at every downward speed
measured, and in the walking and standing poses with a downward speed.
A player rising in a jump never broke the crate, and neither did a
player rolling in the air.

On the frame `n` the crate breaks, the player **bounces** off it:
`n` is the bounce frame **B** of `bounce.md`, and everything there
applies (the move on B, `vy` set to −7.125, the two frozen frames, the
gravity depending on Jump held from B + 3). The trace row for B carries
the `bounce` event. The crate breaks only once.

On B the crate is drawn broken, and a flash and a puff of smoke appear
near it. The broken crate stays where it was and does nothing.

## The animal comes out

On frame **B + 18** the animal appears at `x` = `xc`. For a few frames
it hops: its level y is `yc` minus the height below.

| Frame | Height above `yc` (px) |
|---|---|
| B + 18 | 1.5625 |
| B + 19 | 2.6875 |
| B + 20 | 3.375 |
| B + 21 | 3.625 |
| B + 22 on | 0 |

From B + 22 on, the animal stands at (`xc`, `yc`). It does not move
until it is mounted. It stayed there in a run of 900 frames with no
input. Walking through it does nothing.

## Mounting the animal

For a frame `n`, take the position the player would have at the end of
`n` if the animal weren't there (the player's usual move for that
frame), and with it

- **`I`** = that `x` rounded down, minus `xc`;
- **`D`** = `yc` − the feet line of that position.

The player mounts the animal on frame `n` (from B + 19 on) when:

1. the player was moving down on `n`;
2. the player is in the air after a jump or after a bounce (`bounce.md`,
   including the bounce off the crate), and not rolling;
3. `I` is within the range for the player's facing on `n`, and `D` is
   within 5 to 45 + `k` (both ends included):

| Facing | `I` |
|---|---|
| right | −27 to 19 |
| left | −26 to 20 |

The left range is the right one moved 1 px to the right; it is not the
right one turned around.

`k` is how far the animal was above `yc` at the end of frame `n` − 1,
in whole pixels (rounded down). It is 0 once the animal stands, and
differs from 0 only on the frames around its hop:

| `n` | `k` |
|---|---|
| B + 19 | 1 |
| B + 20 | 2 |
| B + 21 | 3 |
| B + 22 | 3 |
| B + 23 on | 0 |

On B + 18, the frame the animal appears, the player never mounts. Only
the high end of the `D` range moves with `k`; the low end stays 5, and
the `I` ranges are the same on every frame.

A player rising in a jump never mounted. A player put in the air in the
walking pose (as after walking off a ledge) or rolling never mounted,
whatever the position.

Call the frame the player mounts **M**.

- **On M**, the player first moves as usual, horizontally too (the
  frame's air control, `air-control.md`, changes `vx` as on any airborne
  frame). Then the player is moved by whole pixels so that `x` rounded
  down is `xc` and the feet line `F` is `yc` − `k`: the fractions of `x`
  and `y` are kept. `vy` becomes 0. `vx` keeps its value from the move.
  If `k` isn't 0 (a mount while the animal hops), the animal stops
  hopping: from M to M + 21 it stays `k` px above `yc`, at level y
  `yc` − `k`.
- **M + 1 to M + 21**: the player doesn't move; `x`, `y` and `vx` stay as
  they were at the end of M. Inputs have no effect: Jump pressed on
  M + 5 did not start a jump, and Left held from M + 10 changed nothing
  before M + 23.
- **M + 22**: the player still doesn't move.
- **From M + 23** the player rides the animal: `y` is `yc` (the
  fraction is dropped) and the player is on the ground, also after a
  mount while the animal hops (the player then moves down to `yc` on
  M + 23). Riding is in `riding.md`. After a mount while the animal hops,
  the frames M + 23 to M + 39 were the same as after a mount on the
  standing animal with the same inputs.

## Pictures

What the crate, the flash and smoke of its breaking, and the animal
look like in `title-1`, from the frame they appear until the
animal is mounted. Each picture has a label used in this file and is
named by its picture hash (`../formats/sprites.md`, "Picture hashes").
Pictures are drawn at the object's position as `../formats/sprites.md`
says ("Where the picture is drawn"). The picture given for a frame is
the one on screen with that frame's view. "× 6" means that picture on
6 frames in a row.

| Picture | Picture hash (SHA-256) |
|---|---|
| `crate-whole` | `824d72398d393ab5b629658c4d0c3fe839d860771e787ae0007c38d4204655ae` |
| `crate-broken-1` | `b64bec841c9c782a505bba947198810039ef5d1918244829fb4e674c60aa4982` |
| `crate-broken-2` | `0f224add78a919130c3a9896b17619f284499f2e8334f4fe490b35c3eeba9505` |
| `smoke-1` | `3aed926cbb66ce90a9dea5a908f1a117e8dd6cbbdfc048dd3fc912e44385925d` |
| `smoke-2` | `5b5a4f4b41fb429923b0bb42fe3757ec7f84164bfccd2726d7e626b5de123677` |
| `smoke-3` | `038cc8a80e7684919c299de6624778800082117e1eb4cb984121d5179a650522` |
| `smoke-4` | `da464122c679ae0ff4760b6126abc21354b451a0358b6cf82ad37e69cca5ad94` |
| `smoke-5` | `b4cbc221f148d25a47ad023a229fe89f0e33e34068d5ab2d9b556476fd3d98d7` |
| `smoke-6` | `4c634ade6d96a0352c9248a06b2b7777ae8bdadb5d93a5716d7fd6efe910b804` |
| `animal-1` | `01e3b99502ecc5bfecfa63ce024ca6be06e03d23cb0ea13980e4f378d1876300` |
| `animal-2` | `ba60a8cb352aaaf3b6e3402978fa714bcf77a34bb8e6fbcce4dd06b01a7e979b` |
| `animal-3` | `919e587adefce67a0df424881ff3161e43c24626423abafdf0893a4a29029f00` |
| `animal-4` | `34ce9aad93c0bf96e8b2d63e244948de543e54ccd98a8e44c954472c6f78f938` |
| `animal-5` | `5bdcb55d5ef926301252ac7a24c0f912dd551c2df91a4ade34e43c5a5f308501` |
| `animal-6` | `4e6a0f89a251a7d29e1ab7dee6031de49477abca22c05b50dfabf6a46fb67922` |
| `animal-7` | `c7401f115aefc81f1d0b15390d4f55d1c39953bb26c28a3c02709bd72265bde1` |
| `animal-8` | `31458bf28c55b5772d5081f0c9c86fa7c567d0a4b5a673a8a7eb932963d96448` |
| `animal-9` | `6fcc753c417420819cc2a2c7aee93d6dd991e2cfd0a15464b7c4aac4d15798fc` |
| `animal-10` | `4794fc5573311ce28eeb77a7847763d26cb4629c185787ffd95dab6ab2febd5b` |
| `animal-11` | `a59e50ad34fb392eeb75d9936b45d4dc4338adf684e73a1d8639bc3b4920327a` |
| `animal-12` | `a9b91bddccd049b80f2228d61e6e02ce9703d64cb1c07188930b54652815cb22` |
| `animal-13` | `1df20771933ae23f11755975c98745c27195d76e0bc55bdeeab1e42571ff02e3` |
| `animal-14` | `fc78d9a0e636a45a47957f49ed18585c36df1fd95a84d337c999b525d5034840` |
| `animal-15` | `e31a01143ea39cbfcb8adc73e58f2b423f106d70e9d07edf5931b62698e8d74b` |
| `animal-16` | `31e8eac40cc55b741767ca2c04539a87032d1b565241e405b7f01bb3ffe3fb61` |
| `animal-17` | `827ae94d2f017fb130a4bb315e1d4ffb04014141335ea6a26d348770b2eea7eb` |
| `animal-18` | `caab473632e3a1055db0eabf329f5948ed258ebcf9812edc9162343d01dbe5b0` |
| `animal-19` | `1cb810d797aa44b49710de2a2658e78c0d7d87892171a7b4ccc6c417fabe364e` |
| `animal-20` | `2dd1ba0384f742231709e510da15b9dee0399ca885b56dd6bb1ebe2bbde02beb` |
| `animal-21` | `46e2c128d6248d363a52be434956b809dc2258ea3fda490a8abaa307cca7f530` |
| `animal-22` | `c75f6a2e130945bf8d153677257b788dcd21f0f24fcf99e8c0e1bb8c671a5e16` |
| `mounting-1` | `7a18af7e9caafaa0de78c6feda870567d7dd7df7c5f8efc26f9a86f606ef2e27` |
| `mounting-2` | `bc8b3b1a6872f5d9139dc2d017d96007d4fc95f72788e5a3f4c023abbb198d29` |
| `mounting-3` | `c8601d211a37c5529927f7fe2442d9dd938d6ea1a6c8e24d3213772c81897c1b` |
| `mounting-4` | `4014b48ab6c89d8abe78979caea6c770cc6467e1700496c8f7838dd8e309e2d8` |
| `mounting-5` | `f3af9ff72667449efbba318d5e0e7a2d8ba8eaf34d4f648a3c8da95988c59c27` |

The flash uses the eight pictures `flash-1` to `flash-8` of
`thrown-objects.md` ("Pictures"), listed there with their hashes; the
timing, motion and mirroring given there don't apply to it, those below
do.

**When a hash isn't found.** The importer looks for every hash in this
section and in the flash list of `thrown-objects.md`. The pictures of
each of these groups belong together: the three crate pictures, the six
smoke pictures, the 22 animal pictures, the five mounting pictures, the
eight flash pictures. If any hash of a group isn't found, the engine
shows its own art for the group, on the frames, at the places and with
the mirroring given here.

### Counting the frames

Some timings below count **counted frames**: all frames except those on
which nothing moves, which are listed in `letters.md` ("Sparkles",
"When a sparkle comes"; among them B + 1 and B + 2). A count starts
at 0 on the frame named, and that frame is counted. A frame on which
nothing moves keeps the count it had on the frame before.

### The crate

The crate is drawn at (`xc`, `yc`):

- `crate-whole` before it breaks;
- `crate-broken-2` from B + 18 on, for as long as the crate is there.

`crate-broken-1`, `smoke-1` and `smoke-2` were never seen whole on
screen (the flash or the smoke is over them on every frame they could be
shown), and neither was `flash-1`. They stay in the lists only so that
the groups of hashes below belong together; where and when they are
shown is not covered, and so is what the crate looks like from B to
B + 17.

None is mirrored. A crate that is removed and comes back
(`../formats/placements.md`, "When placed objects are removed") is whole
again, also if it had been broken, and shows `crate-whole`; it can break
again as above.

### The flash and the smoke

On B a flash and a puff of smoke appear. Let `c` be the number of
counted frames since B, with `c` = 0 on B (and on B + 1 and B + 2,
which aren't counted).

**The flash** is at `x` = `xc` and level y `yc` − 0.75 − 0.75 × `c`:
it starts 0.75 px above the crate's position and rises 0.75 px on
every counted frame. It shows each of `flash-2` to `flash-8` for 5
counted frames (`flash-2` for `c` 4 to 8, `flash-3` for 9 to 13, and so
on, `flash-8` for 34 to 38). It is gone from the frame with `c` = 39.
It isn't mirrored. Which picture it shows while `c` is 0 to 3 is not
covered (`flash-1` was never seen whole).

**The smoke** is at level x `xc` + `dx` and level y `yc` − `dy`, where
`dx` and `dy` are whole numbers that differ from break to break. It
doesn't move. It shows each of `smoke-3` to `smoke-6` for 3 counted
frames (`smoke-3` for `c` 5 to 7, `smoke-4` for 8 to 10, `smoke-5` for
11 to 13, `smoke-6` for 14 to 16). It is gone from the frame with
`c` = 17. It isn't mirrored. Which pictures it shows while `c` is 0 to
4 is not covered (`smoke-1` and `smoke-2` were never seen whole).
Over the 35 breaks measured, the smallest `dx` seen was −17 and the
largest 14, the smallest `dy` seen was 25 and the largest 34; these are
the values seen, not known bounds, and how they are chosen is not
covered.

### The animal waiting

The animal is drawn at its position (`xc`, `yc` − the height in "The
animal comes out"). From B + 18, until it is mounted, it plays a fixed
sequence of pictures, whatever the player does. Let `a` be the number
of counted frames since B + 18, with `a` = 0 on B + 18. The sequence is
717 counted frames long, numbered 0 to 716, and starts again at 0 after
frame 716. On B + 18 the animal is already on frame 1 of it: on counted
frame `a` it is on frame (`a` + 1) mod 717. The parts of the sequence
are, in order:

| Part | Pictures, each × the number of frames | Frames |
|---|---|---|
| 1 | `animal-22`, `animal-21`, `animal-20`, `animal-19`, each × 6 | 24 |
| 2 | `animal-18` × 26 | 26 |
| 3 | `animal-19`, `animal-20`, `animal-21`, `animal-22`, each × 6 | 24 |
| 4 | `animal-1` × 21 | 21 |
| 5 | `animal-2` to `animal-13` in turn, each × 6 | 72 |
| 6 | `animal-14`, `animal-15`, `animal-16`, `animal-15` in turn, that run five times, each × 4 | 80 |
| 7 | `animal-16`, `animal-17`, `animal-18`, then `animal-17` down to `animal-11` one by one, then `animal-12` up to `animal-22` one by one, each × 6 | 126 |
| 8 | `animal-1`, `animal-2`, `animal-3`, each × 4 | 12 |
| 9 | `animal-4`, `animal-5`, `animal-6`, `animal-5` in turn, that run ten times, each × 8 | 320 |
| 10 | `animal-4`, `animal-3`, `animal-2`, each × 4 | 12 |

So `animal-22` is shown on frames 0 to 5, that is on `a` = 0 to 4 the
first time round. The pictures of the animal are not mirrored while it
waits.

How much of the sequence was seen is in "Evidence for the pictures":
the frames listed there as not seen are not covered, and so are the
lengths of the end of part 1 and the start of part 7.

### While mounting

The animal's pictures from M + 2 to M + 21:

| Frames | Picture |
|---|---|
| M + 2 | `mounting-1` |
| M + 3 to M + 5 | `mounting-2` |
| M + 6 to M + 8 | `mounting-3` |
| M + 9 to M + 13 | `mounting-4` |
| M + 14 to M + 21 | `mounting-5` |

They are mirrored left to right when the player faces left on M, and
not mirrored when the player faces right. The animal stays where it
was (`xc`, and `yc` − `k`, "Mounting the animal"). Which pictures are
shown on M and M + 1, and from M + 22 until the first part of riding
ends, is not covered; the pictures of riding itself are in `riding.md`.

### Evidence for the pictures

Measured on screen in the first level, with only the sprites drawn,
by inputs alone: 42 runs, 94 506 frames. The runs started on either side
of the crate or at the animal and played seeded random inputs (walking,
running, jumping, rolling, crouching), some of them walking so far that
the crate and the animal left the view and came back. 69 pauses and 38
restarts after knock-outs and falls were among them. The crate was
broken 35 times (32 by the first character, once by the second
character, twice by an object falling on it, which isn't covered here)
and the animal was mounted 25 times (17 facing left, 8 facing right).

Frames with the picture partly or wholly off the edge of the screen, or
with a character or another object near it, or with the flash or smoke
over it, were left out, as were frames on which no picture was found at
the place.

- **Crate.** Seen whole on 3 082 frames: always `crate-whole`; on 1 132
  frames after the break (with the animal gone): always `crate-broken-2`
  (0 different). `crate-broken-1` was never seen whole: the flash or the
  smoke is over it on every frame it could be shown, and the same goes
  for `flash-1`, `smoke-1` and `smoke-2`, which is why their frames
  are not covered. All 9 crates that left the view broken came back
  whole.
- **Flash.** On 373 frames the flash showed the picture the rules give,
  each of `flash-2` to `flash-8` at least 23 times; on 1 other frame the
  picture found at its place was another one, with the smoke across it.
- **Smoke.** On 326 frames the smoke showed the picture the rules give,
  each of `smoke-3` to `smoke-6` at least 73 times. Its place was
  found on screen in each of the 35 breaks, at the values given, and it
  did not move.
- **The animal waiting.** On 1 472 frames the animal showed the picture
  the rules give, with every one of `animal-1` to `animal-22` seen at
  least 18 times (0 different). Counting the sequence's positions
  0 to 716 (the number `a` + 1, taken mod 717), 567 of the 717 were seen
  on screen; the 150 that were not are 9 to 23, 195 to 196, 242 to 261,
  266 to 292, 306 to 310, 328 to 329, 387 to 389, 462 to 464, 502,
  506 to 551, 554 to 558, 572 to 574, 590 to 600, 639 to 642 and 691
  to 693 (what the animal shows on those positions is not covered).
  The start of each part was seen, except the start of part 7. The step
  from the last position of the part before into the start of a part was
  seen for parts 1, 3, 4, 5, 6, 8, 9 and 10, but not for part 2 or part 7.
  The longest wait followed was 1 269 counted frames from B + 18, so the
  sequence was seen to start again after position 716 (positions 0 to 8,
  56 to 95, 131 to 194, 353 to 386 and 552 to 553 were seen a second
  time round, with the pictures the rules give).
- **Mounting.** From M + 2 to M + 21 the picture the rules give
  (mirrored as given) was seen on 448 frames, another picture on 3 (the
  player's own picture across the animal's). On M and M + 1 the picture
  found at the animal's place was never the animal's (36 of 36 frames,
  where one was found); it is not covered.

## Example

A crate at `xc` = 1000, `yc` = 400, on a floor at level y 400. The
player jumped while walking left and is falling, facing left, with
`vx` −2, Left held, Jump not held. The positions are those of a
measured break, moved to this crate.

| Frame | `x` | `y` | `vy` | `I` | `D` | What happens |
|---|---|---|---|---|---|---|
| 0 | 1018.5 | 353.4375 | 5.34375 | 18 | 46 | |
| 1 | 1016.5 | 359.21875 | 5.78125 | 16 | 40 | |
| 2 | 1014.5 | 365.4375 | −7.125 | 14 | 34 | The crate breaks; `bounce` (the move used `vy` 6.21875) |
| 3 | 1014.5 | 365.4375 | −7.125 | | | Frozen (`bounce.md`) |
| 4 | 1014.5 | 365.4375 | −7.125 | | | Frozen |
| 5 | 1012.5 | 358.75 | −6.6875 | | | |
| 20 | | | | | | The animal appears at `x` 1000, `y` 398.4375 |
| 24 | | | | | | The animal stands at (1000, 400) |

Mounting: later, the player falls toward the animal facing right, with
`vx` 2 and Right held. A frame ends with the player at `x` = 988.25,
`y` = 353.5, `vy` 5.8125 (`F` = 354, `D` = 46: too high). On the next
frame `vy` becomes 6.25 and the player would end at `x` = 990.25,
`y` = 359.75 (`I` = −10, `F` = 360, `D` = 40): the player mounts on
that frame and is put at `x` = 1000.25, `y` = 399.75 (`F` = 400), with
`vy` 0 and `vx` 2. The player stays there for 22 more frames; riding
begins on the 23rd frame after the mount, with `y` = 400.

## Not covered

- Riding: everything from M + 23 on except `riding.md` (moving,
  turning, jumping, the charge, standing, and their pictures on flat
  floor), that is slopes, walls, what the animal does to enemies, being
  knocked off. Getting off and getting on again are in `getting-off.md`.
- Other levels, other crates, and the kind of animal: the first level's
  crate holds one animal; how a crate's animal is chosen, and other
  animals, are not covered.
- A crate not standing on the floor, and an animal coming out above the
  floor. Mounting while the animal hops was measured falling after a
  bounce only, not in a jump.
- The second character: whether a following character breaks the crate
  or mounts the animal.
- Breaking the crate while bouncing off an enemy, carrying an object,
  being hit, swapping, or during the Down + Action ground action; and
  objects thrown at the crate or the animal.
- What happens to the animal when it leaves the view and comes back. The
  crate is removed and comes back as in `../formats/placements.md`
  ("When placed objects are removed").
- The crate's picture from B to B + 17, the flash's while `c` is 0 to 3
  and the smoke's while `c` is 0 to 4; how `dx` and `dy` of the smoke
  are chosen; the animal's picture on the positions of the waiting
  sequence not seen ("Evidence for the pictures"), the end of part 1 and
  the start of part 7; the pictures when
  the crate is broken by a falling object (the broken crate stood 4 px
  to the left in the two cases seen) or by the second character; the
  sounds.
- The animal's pictures on M and M + 1 and from M + 22 until the first
  part of riding ends (the later ones are in `riding.md`, "Pictures"),
  and the animal's pictures when it comes back after leaving the view.
- The trace state of the player while mounting.
