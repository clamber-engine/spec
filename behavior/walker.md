# Walkers

Titles: `title-1`, `title-2`

A **walker** is the simplest ground enemy: it walks along the floor at a
steady speed, turns around at the ends of its patrol, and is defeated by
a bounce (`bounce.md`). This file covers how a walker moves, how it
turns, how it moves while the player is knocked out, what happens to
it when the player bounces off it, and which picture it shows on each
frame. Which contacts hurt the player and
which give a bounce, and the sizes of walkers, are in
`walker-contact.md`. A thrower's balls pass through walkers without
affecting them (`thrower.md`, "Balls and walkers").

Frames are logic frames at 60 Hz, numbered as in a trace. `x` grows to
the right; `y` and `vy` are as in a trace (down is positive). Speeds are
in px per frame. Measured on two walkers at different points of a level
over several thousand frames each: walking on flat floor and on 1-in-4
slopes, turning at both ends of a patrol, turning at a barrier, while the
player stood still, walked, jumped over them, bounced off them or was
knocked out by them.

## Walking

A walker has a **facing** (left or right) and a horizontal speed `vx`.
Its **target speed** is **+1** when it is heading right and **−1** when
it is heading left.

On each frame:

1. `vx` moves toward the target speed by one eighth of the difference,
   rounded toward zero to a multiple of 1/256. If that step would be 0
   (the difference is less than 8/256), `vx` becomes the target speed
   exactly.
2. The walker moves horizontally by the new `vx`.

A walker at full speed therefore moves exactly 1 px per frame. A walker
that appears at rest speeds up by the same rule. Starting from 0, its
speed on the following frames is 32, 60, 84, 105, 123, 139, 153, 165,
176, 186, 194, 201, 207, 213, 218, 222, 226, 229, 232, 235, 237, 239,
241, 242, 243, 244, 245, 246, 247, 248, 249 and then 256 (in 1/256 px
per frame): full speed on the 32nd frame.

The walker stays on the floor (a walker whose start is above the floor
first drops onto it: `walker-view.md`, "A start above the floor"): its
height is the floor's height at its `x`, on flat floor and on 1-in-4 slopes alike, and slopes don't change
its speed. It also follows steep faces (`steep-faces.md`, "Columns, faces
and steps") down and up, 4 px per column, and walks off ledges ("Walking
off a ledge" below).

## Walking off a ledge

A walker that reaches a ledge walks off it. It doesn't stop or turn at the
edge: its `vx`, facing and patrol go on by the rules above, in the air as
on the floor, and only its height changes. "Column" is as in
`steep-faces.md` ("Columns, faces and steps"): the whole-pixel part of
`x`. A frame **ends in** the column containing the walker's `x` at the
end of that frame.

**Leaving the floor.** Call **F** the walker's first frame off the floor:

- the first frame that ends in a column with no floor, or with floor
  **59 px or more** lower than the floor the walker was on, whether the
  floor before the drop is flat or the low end of a face;
- or, on the **lowest column of a face** whose next column on, in the
  walker's heading, has no floor or floor 59 px or more lower: the second
  frame in a row that ends in that column. The walker is on the face's
  lowest column only on the frame that moves it into that column. A
  walker below full speed (still speeding up, "Walking" above) can end
  two frames in one column; at full speed it never does, and then F is
  the first frame beyond the face.

At the low end of a face that meets floor only 1 px lower (the usual
foot of a face), a walker stays on the floor, however many frames it ends
in the face's lowest column.

**Falling.** On F the walker moves down by **3 7/16 px** (880/256) from
the floor it was on. On F + 1 it moves down by **113/256 px**, and on each
later frame by 7/16 px (112/256) more than on the frame before, but never
more than **8 px** per frame. This is the same growth and the same limit
as for a start above the floor (`walker-view.md`, "A start above the
floor"); only the first two moves differ.

**Landing.** On each frame after F, after the move down: if the walker's
feet are less than 5 px above the floor of the column its frame ends in,
or at or below it, its `y` becomes that floor's `y`, as for a start above
the floor. Only floor that was not above the walker's feet before the
move counts, so a walker that left the floor inside a face's lowest
column doesn't land back on that column. From the landing on it walks on
the floor as before.

### Example: off a flat ledge (synthetic)

A walker at full speed heading right walks on flat floor at `y` = 200
whose last column is 99. From column 100 the floor is 40 px lower, at
`y` = 240. `x` and `y` at the end of each frame:

| Frame | `x` | `y` | Moved down | |
|---|---|---|---|---|
| 0 | 98.25 | 200 | | on the floor |
| 1 | 99.25 | 200 | | on the floor, last column |
| 2 | 100.25 | 203.4375 | 3.4375 | F: off the floor |
| 3 | 101.25 | 203.87890625 | 0.44140625 | |
| 4 | 102.25 | 204.7578125 | 0.87890625 | |
| 5 | 103.25 | 206.07421875 | 1.31640625 | |
| … | | | | 7/16 more each frame |
| 12 | 110.25 | 227.5390625 | 4.37890625 | |
| 13 | 111.25 | 232.35546875 | 4.81640625 | 7.64 px above the floor |
| 14 | 112.25 | 240 | | the move gave 237.609375: landed |

### Example: the end of a face (synthetic)

A walker heading right, still speeding up, walks down a face whose lowest
column is 99, at `y` = 200 (column 98 at `y` = 196). Column 100 has no
floor.

| Frame | `x` | `vx` | `y` | |
|---|---|---|---|---|
| 0 | 98.578125 | 0.48046875 | 196 | on column 98 |
| 1 | 99.12109375 | 0.54296875 | 200 | moved into column 99: on the floor |
| 2 | 99.71875 | 0.59765625 | 203.4375 | still column 99: F |
| 3 | 100.36328125 | 0.64453125 | 203.87890625 | |

At full speed the same walker would have moved from column 99 to column
100 on frame 2 and left the floor there, with the same `y`.

### How it was measured

In the first level and in copies of its image with one walker entry
given another place, another kind or other settings
(`../formats/placements.md`): 111 runs, each following one walker from
the frame it appeared with the player standing away from it, 8 016
frames in all, of which 1 320 off the floor. Small, medium and large
walkers, heading left and right, at their own places and moved:

- off ledges at the low end of a face onto floor 59 px lower,
  heading left and right (three ledges), and off two faces' low ends
  over a pit, into the pit;
- off a flat ledge onto floor 175 px lower;
- 34 runs down three faces that end on floor 1 px lower, 12 of them with
  two or more frames in the face's lowest column;
- starts moved 1 px at a time before each face's low end, so that the
  walker reached it at speeds from 1/8 to 1 px per frame.

On every frame the walker's height was as the rules above give, and no
walker turned or changed speed at a ledge (the only turns were at the
ends of patrols). A first move of 3 or 3 1/2 px instead of 3 7/16 gets
more than 70 frames wrong, and without the rule for a face's lowest
column 15 frames are wrong.

## Turning at the end of a patrol

A walker can have a **patrol range** from L to R, set by how it is placed
(`../formats/placements.md`, "Settings": the patrol setting gives L and R
relative to the walker's start, and need not be centred on it). At the
start of each frame:

- heading left, if `x` rounded down is less than L, or
- heading right, if `x` rounded down is greater than R,

its target speed changes to the opposite direction from this frame on.
Call that frame **T**. The walker doesn't stop: `vx` eases from −1 to +1
(or +1 to −1) by the rule above, so it carries on a little past the end
of its range before coming back. Its **facing** changes on **T + 4**.

It then walks to the other end and turns there the same way, and so on.
The pattern repeats exactly: in the measured walker, each lap took 544
frames, and its range was 256 px wide, centred on its start (the point
where it first appears, `walker-view.md`).

A walker without a patrol range (the other one measured) keeps walking
until something else turns it.

## Turning at a barrier

Some places stop a walker heading toward them. In the case measured
nothing is drawn at the barrier. A walker heading left
toward a barrier at position X_b (it can't have `x` below X_b):

1. On the frame **S** where its move would take `x` below X_b, it is
   placed at `x` = X_b instead, and `vx` becomes **0**.
2. From **S + 1** its target speed is +1, and it speeds up by the rule
   above. Its facing changes on **S + 5**.

A barrier on the other side works the same way with the directions
swapped (not measured).

The barrier measured is at a vertical step up (`steep-faces.md`) at the
first level's left end: `X_b` is the left edge of the step's lower side.
The step stops the player too: walking or running into it, the player is
blocked as in `steep-faces.md` ("Stopped by a rise"), and gets onto its
upper side only by jumping. A player seen to go on past it without a
jump was walking out through the exit there, which moves the lead
whatever is in its way (`exits.md`). Thrown objects break at the step
(`thrown-objects.md`, "Breaking at a vertical step"). Whether walkers
turn at other vertical steps, or at places that aren't steps, is not
covered.

## While the player is knocked out

When the player is knocked out (`knock-out.md`, hit frame H), a walker
moves normally on H, then doesn't move on **H + 1 to H + 10**, and moves
normally again from H + 11. During the 32-frame pause between H + 7 and
H + 8 nothing moves (no logic frames run). The walker that knocked the
player out isn't affected: it walks on through the player and away.

## Being bounced on

Call **B** the frame on which the player bounces off the walker (the
frame with the player's `bounce` event, `bounce.md`).

1. On **B** the walker moves horizontally as usual but not vertically,
   and its `vy` becomes **−6**.
2. On **B + 1** and **B + 2** it doesn't move at all (the same frozen
   frames as the player's). No other walker moves on these frames
   either (`bounce.md`, "Everything else on the frozen frames").
3. On each frame from **B + 3**: `vy` increases by **0.4375** (7/16),
   but never above **8**; the walker moves down by the new `vy` and
   horizontally by its `vx`.

From B on the walker no longer turns, its facing doesn't change, and its
`vx` stays what it was on B. It doesn't land: it falls through the floor
and out of the bottom of the view, and is then removed (`walker-view.md`,
"Defeated walkers falling out", from B + 3). In every case
measured (all from flat floor, falling past the same view) it moved for
the last time on B + 41, 95 px below the floor it had been walking on.

On frame B + 2 + n (n ≥ 1, before `vy` reaches 8), relative to its
position on B:

- `vy` = −6 + 0.4375 × n
- `y` = −6 × n + 0.4375 × n(n + 1) / 2

It reaches the top of its arc 38.1875 px above the floor on B + 15, and
is below the floor from B + 29.

From B on, the walker no longer touches the player, wherever it is on
its fall: it never hurts the player (`walker-contact.md`, "Being hurt"),
and the player never bounces off it again.

Measured on the first level with all three kinds of walker: 304 bounces
in random play (290 different). From 20 frames of each, from B + 1 to
B + 41, the walker was put on the player on every frame up to B + 41,
and each run was compared with the same play where it was left to fall.
On 92 278 frames it ended the frame within 4 px across and 10 px up or
down of the player (the player rising on 22 685 of them, falling on
47 803 and on the ground on 23 269). On every frame the player was the
same in both runs. In 714 of these runs the player was on the ground
when the walker was first put there (3 311 frames on the ground; on
2 639 of them the walker ended the frame at the player's height). In
192 of those runs a walking walker was put on the player the same way:
it hurt the player in 161, on the next frame.

## Ground passable from below

Whether ground is passable from below (`../formats/level-ground.md`,
"Ground passable from below") makes no difference to a walker in
`title-1`: it walks, turns, follows slopes, falls and lands the same
way on ground that is passable from below and on ground that isn't. One
case is not covered (see "Not covered").

Measured in `title-1` by running the same inputs on the first level and
on a copy of it with every surface made not passable from below:

- 16 runs, each following one walker while the player kept near it
  (small, medium and large walkers: 11 at their own places in the first
  level and 5 placed at another place of it), 34,233 frames in all, on
  flat floor, slopes, a steep face followed down, ledges walked off into
  a pit, and turns at patrol ends and barriers. The walker's `x`, speed,
  facing and height were the same in both copies on every frame. In 2
  of the runs the player differed (it rose into a platform from below,
  `platforms-from-below.md`); the walker still didn't.
- In copies with a 160 px wide platform low over a flat floor (the
  platform's band directly above the floor's band), a walker walked
  toward the platform's end from the floor, with the platform passable
  from below and not. With the platform's underside 17 px above the
  walker's feet the walker walked under it; with the floor's top line
  just below the platform's band and the platform's end rising from the
  floor as a steep face, the walker stopped at the face and turned
  around. Both gave the same frames for either platform (6 pairs of runs,
  4,846 frames).

## Pictures

What a walker looks like on each frame in `title-1`. Each picture has a
label used in this file, and is named by its picture hash
(`../formats/sprites.md`, "Picture hashes"); it is drawn at the walker's
position as `../formats/sprites.md` says ("Where the picture is drawn"),
mirrored left–right when the walker faces left. The picture given for a
frame is the one on screen with that frame's view. In the lists,
"`small-turn-1` × 2" means that picture on 2 counted frames in a row.
Where a rule holds for all three kinds, `walk-1`, `turn-1` and so on
stand for the kind's own picture (`small-walk-1`, `medium-walk-1`,
`large-walk-1`).

| Picture | Picture hash (SHA-256) |
|---|---|
| `small-walk-1` | `7c2f3d11b44ed8c75cb0b4a354755ea35e683d2971d9231caf9195c0a4d61e31` |
| `small-walk-2` | `c14309358fa20e3867b39135ccef040ea1da30f80473307d96a7ec3c60fde242` |
| `small-walk-3` | `4f8962af276eca2e58386fdc53c882488479537c38e00e1841abc1e0ecf126d6` |
| `small-walk-4` | `1bda657696ee3fd83a858b516581470642acae8a5230babc918605808aa3c9c5` |
| `small-walk-5` | `91d61b90583ec574b17159f55b46790d42ee0cc23a8d44db0a3e0476287771a3` |
| `small-walk-6` | `a8c7eebf5b3fc0bfda4868c1e747d7665684b5439c4b49d047697d19ed87b273` |
| `small-walk-7` | `078ddef65fc92d0119db0313fa0fedf09934456bfb27177fb0737fa1b666091d` |
| `small-walk-8` | `d25af7a2c33ff23d0eed328dd38b66389453c93679120164e044709114b3f3c9` |
| `small-turn-1` | `8ff7718048bc0c0b62b5ab9485e7cb9f69e2388be34a3a4be77cf6d17b270b35` |
| `small-turn-2` | `505bba53bcbbd164a0f6c44fd1a0b102e1d41443b008c4830c84e3d2a78fa6a8` |
| `small-defeat-1` | `2b4be420f0262628e7932ffdae810c837bcf3c6946b78e56e27a4743899cb0fd` |
| `small-defeat-2` | `86b8e3529c04fdd7da4b68a20eb4a1cae29295cdc879f41d489f4b91794b7ffd` |
| `small-defeat-3` | `7cb1eb4ef1c0dc39da09c1faead04331fea290dc20da78bc6a6f1ad81aba037a` |
| `small-defeat-4` | `af174706dcadc47b06a40eedff1b4ae037a0cd6f0ce0bc6afac1bf87b219125d` |
| `small-defeat-5` | `954685342eaa1c661d1db1b148b2c6214f7ce79f861c3682403c6531b7db03a2` |
| `medium-walk-1` | `b9f00146ec22f81aa88115694045b5a270727651bb6a436d5dbe2b89e9b03136` |
| `medium-walk-2` | `2153ed7d1227729dff8dafc9f5432eacb5e78c5e62eecdf6d4fc193aa1c66937` |
| `medium-walk-3` | `b385fb7110882bacf89887763016ea3bbe3fcc6f2d0626638d4b6262135e7fd4` |
| `medium-walk-4` | `44b796d8ad28ec0b6d97c79bbe1c302d92e1a5fbdd5267f933491d3ac5c7881a` |
| `medium-walk-5` | `a4f35b5640ec9dbe49a961554eb1d497ecb026179cddfc4705f11c4c285cf335` |
| `medium-walk-6` | `b510026df8a51867ac9f6c115b143a83929d0ddd3fc6cf0a73aee9660738753c` |
| `medium-walk-7` | `1b35354d10aef54f28f1be7005f28fc42e317f32516653b36499a36ba54e76d1` |
| `medium-walk-8` | `fd4173a00f0a3624903dccca5d73814d24db663c0f706ebd1060e93d41fa6a65` |
| `medium-turn-1` | `f5a1114e7a38c079ee222c2343e29a73e03253281395ade40cb03010970d13c9` |
| `medium-turn-2` | `783ec48e6ceafae4a497669ff8fb103c9f38bbdb76171344af088d86c76f42ef` |
| `medium-defeat-1` | `e6899d14584604fd418cee6f352334dee4eb0d93c60aca75a39253bb9231dee2` |
| `medium-defeat-2` | `b23383f3dac11f4f10358d70c82dcee4769427728056fb595cf04e1c9687065c` |
| `medium-defeat-3` | `aba7eb091a3a227172fda73a05d0c6e1592c396563c7d096dba77a789137946f` |
| `medium-defeat-4` | `ef9dc6237499d6d5011bd22da12347a58fdbd8c331ddaf6856695fa6803f6633` |
| `medium-defeat-5` | `8c6387555b75c3cfa97b8e715c79d783b413f91db640951eff4a58d6b79f06d0` |
| `medium-defeat-6` | `33f1e11c34a34e0107c49b100846a65db5d9cb4758e92708cf35c78cd0fc2d11` |
| `medium-defeat-7` | `05d12601fe648f2fecd53793f83274e58b857cc8b0ba17bac95ce5b490254b6b` |
| `medium-defeat-8` | `a97b4a56032e295e0e9b780c3de6bee1ca8580246aa3444fae3aa320ca981a6c` |
| `large-walk-1` | `14ed81ef1ea7bba1db1fce2cc379e64040887fcafc026fdbd7ce575c5b1d8faa` |
| `large-walk-2` | `0c2ef61606dab2643cea2c780fe5316d23b3fd7f20d046c345281b2e93aaff5b` |
| `large-walk-3` | `9d910028b622a7bfaeeadf8deb1fff3b73421cbc211cab585f95df880f38e3f8` |
| `large-walk-4` | `03cecd10edc0c7212f527146ad1e425119543d392200a00e7c3d8c5d79b36660` |
| `large-walk-5` | `d9d6f9e74ec96f060be834dadf29653ac94a5b627ae6c1acbaf4839f014e4bf1` |
| `large-walk-6` | `7fa5ad2771cc3e167a00ea3f6e86d2e1529f975406c90fa6363d579b9a02fb42` |
| `large-walk-7` | `af780031e38c260a85d9d31da36cd4fb4891944985079ee87c36e1dddfe0fd59` |
| `large-walk-8` | `5c0db38558cb14d4a43b3b05c8bd53a2fdbec824b9d0c209c048a1030dcf1984` |
| `large-turn` | `ecfcc4d628ff4760fc0f2a333c2ea9a913b7b1b1ea125294f8d1e6ffe8b2bfcb` |
| `large-push` | `627cd4edad13098d0231e4dae9b466ac436ed5517b1b5ed30b32277cc94e3c7c` |
| `large-defeat-1` | `6999c1bed2ea2a494c221f809f669e91d20eba821c1b969e5def1703dd7766d9` |
| `large-defeat-2` | `799ffb0617d1d884c0470c2c7592709fc4a0de3f65cad6765ad6648054a7795f` |
| `large-defeat-3` | `ff935e807f2d6cc676cbef2b240fd759df826c794345a17ae578e29dcf0afc2f` |
| `large-defeat-4` | `1fa57c43315b7ca2004f22f972651dcec31f68aaf2da6650401f9866f68f2294` |
| `large-defeat-5` | `a5547224867f65f1a6a6386b992b7857621dbb9dd537b2964aa8d7af5524e23a` |

### Counted frames

A walker's pictures step on **counted frames**: every frame it is there
except

- the frames on which nothing moves: a bounce's two frozen frames
  (B + 1 and B + 2, "Being bounced on"), and K + 1 and K + 2 after a
  walker is defeated by a roll (`rolling-into-walkers.md`, "The pause")
  or by the ground action (`ground-action-enemies.md`);
- H + 1 to H + 10 while the player is knocked out, for the walkers that
  don't move then ("While the player is knocked out"; the walker that
  knocked the player out walks on and its frames count).

On frames that don't count the walker keeps the picture it had.

### Walking

A walking walker shows its kind's 8 walking pictures in order,
`walk-1` to `walk-8` and then `walk-1` again, each on 4 counted frames.
The cycle doesn't depend on the walker's speed: it is the same while the
walker speeds up from rest. A walker that
appears (`walker-view.md`) shows `walk-1` on its first 3 frames, then
`walk-2` on the next 4, and so on.

### Turning

A turn's pictures start on **U**, the first frame on which `vx` steps
toward the new heading: T at the end of a patrol ("Turning at the end of
a patrol") and S + 1 at a barrier ("Turning at a barrier"; on S itself
the walking pictures go on). From U, on 8 counted frames:

- small and medium walkers: `turn-1` × 2, `turn-2` × 4, `turn-1` × 2;
- large walkers: `large-turn` × 8.

The facing changes on the 5th of these frames (T + 4 and S + 5 above), so
the last 4 are mirrored the other way. The walker then shows `walk-1` on
the next 4 counted frames and goes on through the cycle as above,
whichever walking picture it showed before the turn.

### Pushed

A large walker can be pushed: its `vx` jumps to more than 1 px per frame
without it being defeated (seen when the second character comes down
onto it, `protection.md`, "Not covered"; the push itself isn't
specified). From that frame it shows `large-push`, while its speed eases
back. On the first frame on which its speed is 12/256 px per frame or
less, it shows `large-walk-1`, and that frame is the first of the 4.
If the walker then heads away from its facing, its turn doesn't start on
U: the 8 turn frames start right after those 4 frames of `large-walk-1`,
and its facing changes on the 5th of them.

### Defeated

From the frame on which a walker is defeated (its `vy` becomes −6: B for
a bounce, K for a roll, the ground action or a thrown object), it shows
its kind's list on counted frames, and then keeps the list's last picture
until it is removed:

- small: `small-defeat-1` × 14, `small-defeat-2` × 3, `small-defeat-3`
  × 4, `small-defeat-4` × 4, then `small-defeat-5`;
- medium: `medium-defeat-1` × 14, `medium-defeat-2` × 2, then
  `medium-defeat-3` to `medium-defeat-7` × 3 each, then
  `medium-defeat-8`;
- large: `large-defeat-1` × 14, `large-defeat-2` × 3, `large-defeat-3`
  × 4, `large-defeat-4` × 4, then `large-defeat-5`.

Its facing doesn't change from that frame on ("Being bounced on").

### Example (synthetic)

A small walker heading left reaches the end of its patrol. It showed
`small-walk-6` on frames 1 to 4, and the patrol's T is frame 7:

| Frame | Picture | Facing |
|---|---|---|
| 5, 6 | `small-walk-7` | left |
| 7, 8 | `small-turn-1` | left |
| 9, 10 | `small-turn-2` | left |
| 11, 12 | `small-turn-2` | right |
| 13, 14 | `small-turn-1` | right |
| 15 to 18 | `small-walk-1` | right |
| 19 to 22 | `small-walk-2` | right |

A large walker bounced on on frame B shows `large-defeat-1` on B, B + 1
and B + 2 (frozen frames, not counted) and on B + 3 to B + 15 (13 more
counted frames, 14 in all), then `large-defeat-2` on B + 16 to B + 18.

### Evidence for the pictures

Measured on screen in the first level, by inputs alone: 60 runs of up to
12 000 frames of random play (walking, running, jumping, rolling,
bouncing; both characters where the start had two) from 15 places along
the level. Only the sprites were drawn, and on every frame each walker's
picture was found on screen at its position, as for the player
(`player-frames.md`). Frames on which a walker's picture couldn't be
seen (outside the view, or covered by the player or another object) are
left out, and so are walkers already there on a run's first frame (where
they were in the walking cycle can't be known). The frames compared:

| | Frames | As above | Previous picture one frame longer | Other |
|---|---|---|---|---|
| walking | 38 155 | 37 702 | 392 | 61 |
| turning | 530 | 521 | 9 | 0 |
| facing during a turn | 530 | 530 | | 0 |
| pushed (large) | 643 | 643 | 0 | 0 |
| defeated | 2 922 | 2 916 | 5 | 1 |

These came from 374 walkers appearing, 143 turns, 129 defeats and 20
pushes of large walkers (one followed by a turn). In the "one frame
longer" column the walker showed, on the first frame of a new picture,
the picture before it (see "Not covered"). Of the 62 others, 16 showed
another kind's picture, and the
rest another picture of the walker's own cycle (7 of them
`small-walk-5` instead of `small-walk-2`). With "Counted frames" leaving
out only the bounce and roll frozen frames (the knock-out frames and the
ground action's pause counted), 6 190 fewer walking frames would
match.

## Examples

Synthetic. A walker heading left at full speed with a patrol range
starting at L = 200 (soft turn):

| Frame | `x` at the end of the frame | `vx` | Facing | Notes |
|---|---|---|---|---|
| 1 | 200.5 | −1 | left | |
| 2 | 199.5 | −1 | left | `x` rounded down is now below L |
| 3 | 198.75 | −0.75 | left | T: target speed becomes +1 |
| 4 | 198.21875 | −0.53125 | left | |
| 5 | 197.87890625 | −0.33984375 | left | |
| 6 | 197.703125 | −0.17578125 | left | |
| 7 | 197.671875 | −0.03125 | right | T + 4: facing changes |
| 8 | 197.76953125 | 0.09765625 | right | |
| 9 | 197.9765625 | 0.20703125 | right | |
| 38 | 221.2734375 | 0.97265625 | right | |
| 39 | 222.2734375 | 1 | right | full speed |

A walker heading left at full speed toward a barrier at X_b = 200:

| Frame | `x` at the end of the frame | `vx` | Facing | Notes |
|---|---|---|---|---|
| 3 | 200.5 | −1 | left | |
| 4 | 200 | 0 | left | S: would pass the barrier, stopped at it |
| 5 | 200.125 | 0.125 | left | |
| 6 | 200.359375 | 0.234375 | left | |
| 7 | 200.6875 | 0.328125 | left | |
| 8 | 201.09765625 | 0.41015625 | left | |
| 9 | 201.578125 | 0.48046875 | right | S + 5: facing changes |
| 36 | 224.75390625 | 1 | right | full speed from here |

A walker bounced on while walking left on flat floor, positions relative
to the end of B − 1, `y` down positive:

| Frame | `x` | `y` | `vy` | Notes |
|---|---|---|---|---|
| B | −1 | 0 | −6 | |
| B + 1 | −1 | 0 | −6 | frozen |
| B + 2 | −1 | 0 | −6 | frozen |
| B + 3 | −2 | −5.5625 | −5.5625 | |
| B + 4 | −3 | −10.6875 | −5.125 | |
| B + 15 | −14 | −38.1875 | −0.3125 | top of the arc |
| B + 16 | −15 | −38.0625 | 0.125 | |
| B + 29 | −28 | 3.375 | 5.8125 | below the floor: it doesn't land |

## In `title-2`

Everything in this section was measured on `title-2` only. Walkers there
walk like the ones above, with different rules for speeding up and for
being bounced on. Frames, `x`, `y`, `vx` and `vy` are as above
(down positive); speeds are whole multiples of 1/256 px per frame. Bounce
frames (B) and the player's frozen frames are those of `bounce.md` ("In
`title-2`").

### Walking (`title-2`)

A walker's **target speed** is **+1** heading right and **−1** heading
left, as in `title-1`. On each frame:

1. The walker moves horizontally by its current `vx` (the speed it had
   at the end of the previous frame).
2. Then `vx` moves toward the target speed. With D the difference
   between the target and `vx`, measured in 1/256 px per frame and
   taken without its sign, the step is D/16 rounded down plus D/64
   rounded down, in the direction of the target. If that step would be
   0 (D below 16/256), `vx` becomes the target speed exactly.

Unlike `title-1`, the move on a frame uses the speed from before that
frame's change, and the step is about 5/64 of the difference rather
than one eighth.

A walker at full speed moves exactly 1 px per frame. Starting from 0,
its speed at the end of the following frames is 20, 37, 53, 68, 81, 93,
105, 116, 126, 136, 144, 152, 159, 166, 172, 178, 183, 188, 193, 196,
199, 202, 205, 208, 211, 213, 215, 217, 219, 221, 223, 225, 226, 227,
228, 229, 230, 231, 232, 233, 234, 235, 236, 237, 238, 239, 240, 241
and then 256 (in 1/256 px per frame): full speed at the end of the 49th
frame, first used for the move on the 50th.

The walker stays on the floor: at the end of each frame its `y` is the
floor's height at the whole-pixel column containing its new `x`. This is
the same floor height the player stands on (`slopes.md`, "In
`title-2`"), on flat floor, 1-in-2 slopes and 1-in-4 slopes alike, and
slopes don't change its speed.

Every measured walker headed left from where it appeared, sped up to
full speed and kept going; none turned (see "Not covered in
`title-2`").

### Being bounced on (`title-2`)

1. On **B** the walker doesn't move at all. Its `vx` becomes **−1** if
   the player faces right at the end of B and **+1** if the player faces
   left, and its facing becomes that direction.
   Its `vy` becomes **−8**.
2. On **B + 1** and **B + 2** it doesn't move.
3. On each frame from **B + 3**: the walker moves horizontally by `vx`
   and vertically by `vy`; then `vy` increases by **0.4375** (7/16), but
   never above **12**.

From B on the walker no longer turns, its facing doesn't change, and its
`vx` stays what it was set to on B. It doesn't land: it falls through the
floor and on down until it is removed. In the measured bounces it was
removed between B + 27 and B + 53: soonest when the player ran on away
from it, latest when the player stood still (see "Not covered in
`title-2`").

On frame B + 2 + n (n ≥ 1, while `vy` stays below 12), relative to its
position on B:

- `x` = n × `vx`
- `y` = −8 × n + 0.4375 × n(n − 1) / 2

It reaches the top of its arc 77.1875 px above the floor on B + 21, is
below the floor from B + 40, and moves 12 px per frame from B + 49.

### Other walkers during a bounce (`title-2`)

When the player bounces off one walker, every other walker moves as usual
on B and doesn't move at all on **B + 1** and **B + 2** (the player's two
frozen frames), then carries on from B + 3 as if those two frames hadn't
happened: its `vx` is unchanged by them.

### Example (`title-2`)

Synthetic. A walker heading left at full speed, bounced on by a player
facing right, positions relative to the end of B − 1:

| Frame | `x` | `y` | `vy` | Notes |
|---|---|---|---|---|
| B | 0 | 0 | −8 | doesn't move |
| B + 1 | 0 | 0 | −8 | frozen |
| B + 2 | 0 | 0 | −8 | frozen |
| B + 3 | −1 | −8 | −7.5625 | |
| B + 4 | −2 | −15.5625 | −7.125 | |
| B + 5 | −3 | −22.6875 | −6.6875 | |
| B + 20 | −18 | −77.0625 | −0.125 | |
| B + 21 | −19 | −77.1875 | 0.3125 | top of the arc |
| B + 22 | −20 | −76.875 | 0.75 | |
| B + 39 | −37 | −4.625 | 8.1875 | |
| B + 40 | −38 | 3.5625 | 8.625 | below the floor: it doesn't land |
| B + 48 | −46 | 84.8125 | 12 | `vy` reaches the cap |
| B + 49 | −47 | 96.8125 | 12 | |

The same bounce by a player facing left gives the same `y` and `vy`, and
`x` = +1, +2, … from B + 3.

A walker starting at rest with `x` = 100 and target +1:

| Frame | `x` at the end of the frame | `vx` at the end of the frame |
|---|---|---|
| 1 | 100 | 0.078125 |
| 2 | 100.078125 | 0.14453125 |
| 3 | 100.22265625 | 0.20703125 |
| 4 | 100.4296875 | 0.265625 |
| 5 | 100.6953125 | 0.31640625 |

### Evidence (`title-2`)

Nine walkers at different places in the first level, met along a route
through it: 1,079 frames of walking (including speeding up from rest),
and the frozen frames of the other walkers during a bounce, match this
section with 0 mismatches. 286 bounces off walkers at three places, from
the bounce measurements in `bounce.md` ("In `title-2`") and 16 more
with two other walkers walking nearby, with the player facing right or
left on B: 70,629 frames of walking, every bounce frame, 10,123 frames
of the bounced walkers' falls (including frames at the cap of 12) and
64 frozen frames of the other walkers match with 0 mismatches. The
walker's height matched the player's floor on 46,826 frames, on flat
floor, 1-in-4 slopes and a 1-in-2 slope.

### Not covered in `title-2`

- Turning: patrol ranges and barriers. No measured walker turned.
- What the walker does while the player is hit, and walkers defeated by
  thrown objects. Rolls, including the two frames other walkers stop
  for, are in `rolling-into-walkers.md` ("In `title-2`").
- Walkers bounced on before reaching full speed (every measured one was
  at full speed heading left on B): whether their `vx` is still set to
  exactly ±1.
- When walkers appear and are removed (`walker-view.md` is `title-1`
  only), including how a defeated walker leaving the view is decided,
  and walkers appearing above the floor and dropping onto it.
- Which contacts hurt the player and which give a bounce
  (`walker-contact.md` is `title-1` only), walkers at ledges, and how
  walkers look.

## Not covered

This list is for `title-1`; `title-2` has its own list above.


- Whether walkers defeated in other ways (`thrown-objects.md`, "Hitting
  walkers", and `rolling-into-walkers.md`) can hurt the player while they
  fall, and the second character touching a walker defeated by a bounce.
- Ledges with drops between 2 and 58 px ("Walking off a ledge" was
  measured with floor 1 px lower, which is followed, and 59 px or more
  lower, which isn't), and faces whose lowest column is reached heading
  up the face. What a walker does at the bottom of a pit, below the
  level. Walkers on other slopes.
  A walker meeting a steep face that rises from its floor was seen to
  stop at it and turn around; how it turns isn't specified.
- A walker reaching ground whose band is directly above the walker's
  floor band and whose end is square (no face), with the floor's top
  line the line just below that band, so that the ground's underside is
  1 px above the walker's feet. In copies of the image with such
  ground, the walker went down through its floor from the frame on which
  it reached the ground's end. If the ground was passable from below it
  kept walking at its speed while going down; if not, it stopped where
  it met the ground for 6 frames and then moved back the other way,
  speeding up, while it went down. This is the only difference being
  passable from below was seen to make for a walker.
- What other places act as barriers (the one measured is a vertical
  step, "Turning at a barrier"), barriers met heading right, and a
  walker placed past a barrier.
- When a walker appears and disappears as the view moves is in
  `walker-view.md`.
- Other kinds of enemies, and walkers' pictures in `title-2`.
- Pictures ("Pictures"): on screen a walker sometimes shows its previous
  picture on one more frame, on the first frame of a new picture
  (406 of the 42 250 frames compared; it was seen with all three kinds,
  most often with small walkers). When this happens isn't covered;
  `../formats/sprites.md` ("Evidence") saw the same with the tiles in
  place for an object. Small and medium walkers pushed without being
  defeated (one small walker was seen; it kept its walking picture while
  pushed). A walker that appears already defeated: one small walker
  was seen, and it showed the list as if defeated on the frame before
  it appeared.
