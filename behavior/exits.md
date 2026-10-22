# Exits

Titles: `title-1`

An **exit** is a placed object that takes the lead out of the area: once
the lead comes within its reach, the player loses control, the lead
walks away through the exit on its own, the picture fades to black and
play goes on in another area. This file covers when an exit takes over,
how the lead moves afterwards, when the area is left, and where the
first level's two exits lead: how the characters walk into the next
area, the short scene one exit leads to, how play comes back to the
level, and what of the level is kept then.

Frames are logic frames at 60 Hz, numbered as in a trace. Horizontal
positions are level x as in `view.md`; vertical positions are **level y**
as in `view-vertical.md`, growing downward. `x`, `y` and `vx` are the
lead's values as in a trace; `X` is `x` rounded down. "Right", "Left",
"Down", "Jump" and "Action" are the neutral inputs `R`, `L`, `D`, `J`,
`A` of the scenario format.

Measured on the first level, which has two exits, both leading left: one
on the level's floor near its left end, and one on higher ground above
it, reached by climbing. Most runs used the one on the floor. The runs
started with the lead standing a little right of it after the level's
entrance (`level-start.md`), alone or with the other character
following, and used random inputs (see "Checks").

## The exit object

A level's placement list (`../formats/placements.md`) places each exit
with an entry of kind `exit`. The exit doesn't move and is not drawn.
Its **position** (`xe`, `ye`) is its entry's level x and level y
("Positions" there).

Every exit measured **leads left**. What follows is for an exit leading
left; an exit leading right is not covered. Where an exit leads (which
area comes next) is a setting of the object; the first level's two
exits lead to different areas ("Where the first level's exits lead").

## When an exit takes over

Let `I` = `X` − `xe` at the end of a frame (how far right of the exit
the lead is, in whole px). The exit **takes over** on the first frame
that ends with all of these true:

1. the lead is on the ground (the landing frame of a jump or fall
   counts);
2. `vx` is 0 or less (the lead is still or moving left);
3. the lead's `y` is from 39 px above the exit's `ye` to 35 px below it
   (`y` − `ye` from −39 to 35);
4. `I` is at most the **reach** for the lead's state and facing on that
   frame:

| Lead's state at the end of the frame | Facing | Reach (largest `I`) |
|---|---|---|
| Standing, walking or running; the landing frame of a jump or fall | left | 31 |
| Rolling | left | 29 |
| Crouching (Down held) while still sliding | left | 34 |
| Standing, or turning (`ground-speed.md`) | right | 37 |
| Standing still carrying an object (`carrying.md`) | left | 30 |
| Standing still carrying an object | right | 32 |

Call that frame **`T`**. Only the lead is taken over. In the runs with
two characters, the other character (`following.md`) was never taken
over. From `T` on, up to and including the area's last frame, it
follows the lead as in `following.md`: the lead's positions in the
walk-out are added to the trail like any others, and it rests, waits
and steps on by the same rules.

A player moving right is never taken over, however close: a lead that
walks right past an exit and stops in its reach is taken over on the
frame `vx` becomes 0. A lead in the air is not taken over until the
frame it lands. Frames on which the lead is on a steep face above the
exit's floor (`steep-faces.md`), being carried down it, were not taken
over even with `y` − `ye` within the range of item 3.

How far left of the exit the reach goes is not covered (see "Not
covered"). In the first level, a lead coming from the right is taken
over before it gets there.

## The walk-out

From `T` on, the lead walks left on its own:

- **On `T`** the lead moves as usual for that frame (its input counts).
  At the end of `T`, `y` becomes `ye` + 1, whatever it was, with no
  fraction.
- **On `T` + 1** `vx` becomes **−2**, and `x` doesn't change.
- **From `T` + 2** `x` goes down by exactly 2 every frame. `y` doesn't
  change. The lead shows the walking pose.

From `T` + 1 on, inputs have no effect: no direction, Jump, Action or
Down held or pressed changes anything above. The walk takes no account
of the ground or of walls: the lead keeps the same `y` and passes through
whatever is in its way, and it is not stopped at the level's left end
(`level-ends.md`): `x` can go below 0.

**Facing.** A lead facing left on `T` keeps facing left. A lead facing
right on `T` (it was standing or turning) shows the pictures of a turn
from `T` + 2 and faces left from `T` + 6.

**An object in the lead's hands.** A `barrel` or a `holder` still in
the lead's hands at the end of `T` is let go on `T` + 1 and falls away
on its own. This includes an object carried with Action held and one
whose throw (`carrying.md`, "Throwing") began on a frame `Q` with
`Q` + 21 later than `T`, so that it hadn't left the hands yet; that
throw never happens. Letting go of Action on `T` or later makes no
difference either.

- **On `T` + 1** the object doesn't move: it is where it was held at the
  end of `T` (where a held object is relative to the player is not
  covered, see `carrying.md`). Its `vx` becomes **1** (256/256) in the
  direction the lead faces on `T`, and its `vy` becomes **−4**
  (−1024/256, moving up).
- **From `T` + 2**, every frame, `vy` increases by **0.4375** (112/256),
  but never above **8** (2048/256); then `x` moves by `vx` (exactly 1 px)
  and `y` by the new `vy`. Nothing else changes `vx`.
- It **touches nothing**: it passes through floors, faces and walls
  without landing, bouncing, rolling or breaking (a holder keeps the
  missing character, `holder-breaking.md`), and nothing the lead or
  the input does changes its motion.
- It is **removed** on the first frame on which, after the move, its `x`
  is less than 0 (left of the level's left end, however near the
  picture: it was seen at `x` 0 with the picture's left edge 27 px to
  the right of it), or its `y` rounded down is 256 or more below `U`,
  the view's vertical position rounded down as in
  `../formats/placements.md` ("When placed objects are removed"). If
  neither happens first, it is gone when the area changes.

So an object let go by a lead facing left rises for 9 frames and falls
from `T` + 11, at 8 px per frame from `T` + 29.

## Leaving the area

Call **`F`** the first frame after `T` that ends with

    X ≤ xe − 4

that is, with the lead at least 4 px left of the exit. In the runs `F`
was from `T` + 9 to `T` + 22, mostly `T` + 17 to `T` + 19.

- **The view.** The view's position `V` (`view.md`, `view-vertical.md`)
  and the picture's left edge (`level-ends.md`) move by their usual
  rules up to `F` − 1, and don't change from `F` on.
- **The fade.** The **brightness** of the whole picture, on a scale from
  15 (full) to 0 (black), is 15 on `F`, and goes down by 1 every second
  frame: frames `F` + 2`j` − 1 and `F` + 2`j` have brightness 15 − `j`.
  It reaches 0 on `F` + 29.
- **The end.** `F` + 30 is the last frame in the area. The lead goes on
  walking left by 2 px every frame up to it. On the next logic frame,
  play is in another area. For the first level's exits, which area that
  is and what happens there is in "Where the first level's exits lead".

## In a trace

The trace follows the lead. Up to and including `T` it shows the lead
as anywhere else. From `T` + 1 up to and including the area's last
frame, `F` + 30, every row has:

- state `walk`, `ground` 1 and `vy` 0, whatever is held: inputs have no
  effect, and the walk takes no account of the ground under it;
- `vx` −2, and `x` and `y` as in "The walk-out" (on `T` + 1, `vx` is
  already −2 while `x` hasn't moved yet);
- no events other than `turn`. A lead facing right on `T` gets `turn`
  on `T` + 6, when it faces left. There is no `leave_ground` or `land`,
  also where the walk goes on past the end of the floor or beyond the
  level's left end.

A trace that reaches the area's last frame ends with it. What a trace
shows from the next area's first frame on is not covered.

The scenario `../scenarios/exit-walk-out.scn` and its reference trace
cover this. The lead walks left towards the exit after the level's
entrance, presses Right just inside the reach and lets go, so the exit
takes it over while it turns to face right. Jump, Action and Right are
pressed during the walk-out and change nothing.

In the first level the floor exit is not on the floor: the floor ends
28 px right of the exit, against a vertical step up
(`steep-faces.md`), and the exit is at the floor's height inside the
higher ground beyond the step. The scenario's `requires` line describes
this. The reference trace doesn't depend on it: the lead is taken over
before it reaches the step (33 px right of the exit on `T`), and from
`T` + 1 the walk passes through the ground. A lead that the exit
didn't take over would be blocked at the step ("Stopped by a rise" in
`steep-faces.md`) rather than walk off the end of the floor.

## Where the first level's exits lead

The next area's frames are numbered from **0**, its first logic frame:
the logic frame after `F` + 30.

- **The floor exit** leads to a short **scene**: the characters walk in
  from the right ("Walking in"), the lead stops, and after a fixed time
  the picture fades out ("The scene"). Play then goes on in the first
  level again: the characters walk in from the left, starting from the
  first level's **scene-return record** (`../formats/level-start.md`),
  and the player has control from frame 28 as below. The floor exit is
  still there, a little left of that point, and it leads to the scene
  again each time it is reached.
- **The upper exit** leads to a **room**, a small area of its own where
  the player has control. The characters walk in from the right
  ("Walking in"). The room is left by walking right past its end marker
  (`level-end.md`, "In the first level's room"), not through an exit.
  After it, play goes on in the first level, which begins exactly as it
  does from the level map
  (`level-start.md`), at the first level's **room-return record**
  (`../formats/level-start.md`) instead of its start record, and out of
  the level's `room-return-entrance` instead of its `entrance`
  (`level-start.md`, "Coming back from the room").

Coming back from the room was checked frame by frame against the level's
own start: the lead's position, speed, state and facing were the same
on every one of the first 90 frames (see "Checks").

## Walking in

When the scene, the room, the level after the scene or the area behind
either wall exit (`wall-exits.md`) begins, the characters walk in.
Let (`xs`, `ys`) be the point of the area's start record
(`../formats/level-start.md`), and `d` the direction of the walk: −1
(left) into the scene and the room, +1 (right) back into the level
after the scene and into the areas behind the wall exits.

- **Frames 0 to 26.** On frame `k` the lead is at `x` = `xs` + `d` ×
  (3`k` − 80), `y` = `ys`, with no fraction of a px: it walks 3 px per frame
  in the direction `d`, facing that way from frame 1 (frame 0 is black,
  see "Fade-in").
- **Frame 27.** The lead moves 6 px: `x` = `xs` + 4`d`. In the area
  behind the first wall exit the lead is on the floor on frame 27, 1 px
  lower than the record's point (`y` = `ys` + 1); in the area behind the
  second, it is on the floor at the record's point (`y` = `ys`).
- **Frame 28 on.** The player has control. On frame 28 the lead is on
  the ground with a speed of 3 px per frame in the direction `d` (`vx` =
  3`d`, that is ±768/256) and moves by the usual rules
  (`ground-speed.md`), with that frame's input: with nothing held, for
  example, `vx` becomes ±2.625 on frame 28.
- **Input.** Nothing held or pressed on frames 1 to 27 has any effect
  (frame 0 was not tried with input).
  A Jump press on frame 28 or later works as usual (`jump-input.md`);
  Jump or Action held down from before frame 28 is not a press on 28.
- **The other character**, if there is one (`following.md`), walks in
  with the lead: on frames 0 to 26 it is 28 px behind (`x` − 28`d`,
  same `y`) and moves the same 3 px per frame, and on frame 27 it
  doesn't move.
- **Its trail.** On frame 27, before it moves, the other character's
  trail (`following.md`, "The trail") is replaced by 16 points, oldest
  first: (`xo` + ⌊28`k``d` / 16⌋, `yo`) for `k` = 0 to 15, where
  (`xo`, `yo`) is its own whole-pixel position. They go 1/16 of the way
  per point toward the lead's frame-26 position (28 px ahead), which is
  not itself one of them. The first point is its current point, and the
  catching-up mark is clear. In the level and in the room it follows the
  trail from frame 27 on ("Following the trail" there): on frame 27 it
  stays where it is (step 1 puts it on its own point), and the lead's
  frame-27 position is added at the end of that frame as usual. In the
  scene it doesn't follow ("The scene").
- **Fade-in.** The brightness (as in "Leaving the area") is `j` on
  frames 3`j`, 3`j` + 1 and 3`j` + 2 for `j` from 0 to 14, and 15 from
  frame 45 on.
- **The view** doesn't change on frames 0 to 26. Where it is on frame 0
  and how it moves from frame 27 on are in `view.md` and
  `view-vertical.md` (both "When an area begins"), measured walking back
  into the first level. In the room and
  the scene the picture doesn't move (`level-ends.md`).

In the scene the lead stops after frame 28 ("The scene"); in the room
and in the level, play simply goes on.

## The scene

- **Frames 0 to 28** are as in "Walking in", walking left.
- **From frame 29** the lead stands still, facing left, at the `x` it
  had at the end of frame 28, to the end of the scene. Nothing held or
  pressed has any effect. The other character, if there is one, stays
  where it was on frame 27.
- **Fade-out.** With the first character leading, the brightness is 15
  on frame 366 and goes down by 1 every second frame: frames 365 + 2`j`
  and 366 + 2`j` have brightness 15 − `j`. It is 0 on frames 395 and 396.
  With the second character leading the scene is 30 frames shorter: the
  brightness is 15 on frame 336, frames 335 + 2`j` and 336 + 2`j` have
  brightness 15 − `j`, and it is 0 on frames 365 and 366.
- **The last frame** is frame 396 with the first character leading and
  frame 366 with the second leading, whatever was pressed. On the next
  logic frame, play is back in the first level (frame 0 of "Walking
  in", walking right from the scene-return record). Which character
  leads is the one in front when the exit takes over; Swap
  (`swap.md`) pressed during the walk-out is not tried.

The scene's data is in `../formats/graphics.md`,
`../formats/level-ground.md`, `../formats/placements.md` and
`../formats/view-limits.md` ("The first level's scene"), its start
record in `../formats/level-start.md` ("the scene's start record"), and
its view in `level-ends.md` ("In the first level's scene"). Of the
scene's placed objects, one is the room's end marker and one a sign
(`signs.md`, "The scene's sign"); the third's identity is not covered.

### Pictures in the scene

The picture given for a frame is the one on screen with that frame's
view (`player-frames.md`, "The pictures"), mirrored left–right because
the characters face left. The pictures are named by their hash
(`../formats/sprites.md`, "Picture hashes"). Names of the walking and
standing pictures are those of `player-frames.md`; the pictures the
leading character shows while it waits in the scene are new:

| First character | Picture hash (SHA-256) |
|---|---|
| `scene-1` | `09ef39b8c01b7fe26d276f168402f4167cc9d38db2d473e35004ecf729206e6c` |
| `scene-2` | `61324a3c3597f2c033b2f41be8ea4322e7334de361bf3156ed6c9d194d2a6714` |
| `scene-3` | `9de7aa4fc0df89ca063f4b8ae1744d1b70c6be7098c34cba0dfa30ce781d7bf5` |
| `scene-4` | `c3200e506b8cd41f10ef020a17cf1d9730046ef5f467c674f48234401311c875` |
| `scene-5` | `c8aff6b3a5ec7727eddf266e3685f5fc79720ed8cf2ec0aaf36e7af9564b7be5` |
| `scene-6` | `75b72d7f6857b7a546a5fa3168a1b676edc56713608520fc1f67bfe3c1eadc36` |
| `scene-7` | `3001aefe4304aec30aa3c3603518be0012b7a1593c8131afe7c19548d83b18ad` |
| `scene-8` | `251acf233ea18fc5c5be4e9c5b3bb0ce2693f40727b9cd037fe23a7411f17bc0` |
| `scene-9` | `d617e9e7a23f21e47565568e4c910058aae349a7b3ce26910c4dad8cd016fbd8` |
| `scene-10` | `77ea4b322124a10d212498db7bb0febe00147c417185aea1f08a84987d77cc72` |
| `scene-11` | `dc5beb6737e0662f8c885247509935d4a5423981cc3eb61c5c4868453d4d3446` |
| `scene-12` | `29ca7a808f7f46d3d4948256b1a1916cb0ddcd068faf2f42776425e0561edee5` |
| `scene-13` | `c1b35d450fcfa8afa40be8f43cbc7af596ad0a5ba690a878bc92f5c1caed1285` |

| Second character | Picture hash (SHA-256) |
|---|---|
| `s-scene-1` | `49bc050262c27962098bf9f20a8e202d9adb7feaec6a727376436fb61499ef5f` |
| `s-scene-2` | `d30f00b2a403413af0c3fb335d6b5c19681bd45ea6cd8f38dc0d485ba23860b8` |
| `s-scene-3` | `6aaaa3f1e08fb1d76affa2007adea12f62c65ec5e578cd6361995e0c7adc226b` |
| `s-scene-4` | `69023e0cb6ce49e511e5ef182e76773501b4a6ac7f372729f020c9c5e1e01669` |
| `s-scene-5` | `c617366fc1f4edc54c9f8d780d592c1fd0baa3ef2e1752da2e510b530385c166` |
| `s-scene-6` | `a85a2454996ff759e0cade447e51188e4bdfa1cedc1a6509731ccc5d1c0311ff` |
| `s-scene-7` | `80dde04726f742dbb9b0d2fe68af598590372318ad9723abe8c8236eaf100466` |
| `s-scene-8` | `412e31d60ca329b3697518bb820c1ecda7227379eff1381950f9acc13439fcd1` |
| `s-scene-9` | `039e43f1462c8f6827ecd3bead24f77108aca4392bfc0bae18029d8e597dde9c` |
| `s-scene-10` | `14e89b4001c978a96b639dab2e2563e3dddff5edeac844384d79d6a7a2e26088` |
| `s-scene-11` | `63d05cb4952fa76e26af9aad7b42aa113d3b7a1901d2571d209272a9cfb70d8c` |
| `s-scene-12` | `47b02752138552e3245077ce6eac48505eb9870a07185229f2cb50681611ce1a` |
| `s-scene-13` | `f109257406a02a93facd69a051b008850e7dec4d9570df16293ae14dc7f4121c` |
| `s-scene-14` | `2877ae035ca8163ee1164c7d9043006b92bcc8d1aaef9e1a06ab2c207155a562` |
| `s-scene-15` | `f2593b8d3a52ffb872bba3115c96825774aa6851fd6ab1964a46ef567ad793eb` |
| `s-scene-16` | `825a3174a15be4d6c0fef323e5ce6467b1d3bcc93eff410dce76dfb7b9673edf` |
| `s-scene-17` | `9a47bb5fc16f3417efcb07ff10560bf8929b48794dfc5b5554d47e8b04d9fe63` |

**When a hash isn't found.** The pictures `scene-1` to `scene-13` fall
back together, and so do `s-scene-1` to `s-scene-17`: if any hash of a
group isn't found, the engine shows its own art for the leading
character of that kind on frames 28 to the scene's last, keeping the
timing given here. The leading character's walking pictures (`run-1`
to `run-20`, `s-run-1` to `s-run-13`) and the standing pictures of the
other character follow the fallbacks of `player-frames.md`.

Frames 0 to 2 are black (brightness 0, "Walking in", "Fade-in") and no
picture is on screen. On the last two frames of each scene the
brightness is 0 as well and no picture is on screen. Nothing held or
pressed changes any picture.

**The first character leading** (frames 0 to 396):

- Frames 3 to 27: `run-2` on frame 3, `run-3` on frame 4, then `run-4`
  to `run-18` in turn, each for 2 frames and 1 frame alternately,
  starting with `run-4` on frames 5 and 6, `run-5` on frame 7,
  `run-6` on frames 8 and 9, and so on up to `run-18` on frames 26 and
  27.
- Frames 28 to 99: `scene-1` × 7, `scene-2` × 6, `scene-3` × 6,
  `scene-4` × 21, `scene-5` × 14, `scene-6` × 9, `scene-7` × 9.
- From frame 100 to frame 394 a list of 62 frames repeats, starting on
  frames 100, 162, 224, 286 and 348: `scene-8`, `scene-9`, `scene-10`,
  `scene-11`, `scene-10`, `scene-9`, `scene-8`, each for 6 frames, then
  `scene-12` × 6, `scene-13` × 8, `scene-12` × 6. The list is cut off
  where the scene ends: frame 394 is its 47th frame, and frames 395 and
  396 show nothing.

**The second character leading** (frames 0 to 366):

- Frames 3 to 27: `s-run-2` on frame 3, then in turn, for 2 frames
  except where noted: `s-run-3` (frame 4; frame 5 was not read),
  `s-run-4` (frame 6; frame 7 was not read), `s-run-5` (1 frame, 8), `s-run-6` (9, 10), `s-run-7`, `s-run-8`,
  `s-run-9` (1 frame, 15), `s-run-10`, `s-run-11`, `s-run-12`,
  `s-run-13` (22 and 23), `s-run-1` (1 frame, 24), `s-run-2` (25 and
  26), `s-run-3` (1 frame, 27).
- Frames 28 to 129: `s-scene-1` × 4, `s-scene-2` × 19, `s-scene-3` to
  `s-scene-10` × 3 each, `s-scene-11` × 26, `s-scene-12` × 3,
  `s-scene-13` × 4, `s-scene-14` × 22.
- From frame 130, nine rounds `k` = 0 to 8 of 27 − `k` frames each
  follow: `s-scene-15` × 3, `s-scene-16` × 3, `s-scene-17` × 5,
  `s-scene-16` × 2, `s-scene-15` × 5, `s-scene-14` × (9 − `k`). Round 0
  starts on frame 130 and round 8 on frame 318. Then, from frame 337,
  `s-scene-15` × 3, `s-scene-16` × 3 and `s-scene-17` on frames 343 to
  362. Frames 365 and 366 show nothing; frames 363 and 364 are dark
  and were not read.

**The other character** (`following.md`) was read on screen where the
leading character didn't cover it. The second character behind the
first: `s-run-1` on frame 27, `s-stand-1` on every frame from 28 to
394. The first character behind the second: `run-16` to `run-20`,
then `run-1` to `run-9` on frames 15 to 28, one picture per frame;
`stand-1` on every frame from 32 to 364. Neither shows the swaying or
waiting pictures of `player-frames.md`, "Standing still", in the
scene.

## Coming back to the level

When play comes back to the first level after the scene or the room
(frame 0 of the walk-in from the scene-return record, or of the level's
start from the room-return record), some of what happened in the level
before the exit took over is kept and some is not.

Kept as it was when the area was left:

- the collectible count (`collectibles.md`, `count-display.md`) and the
  lives;
- the set of letters taken (`letters.md`);
- the figure count (`figure.md`);
- whether the level's restart point has been reached
  (`restart-barrel.md`): after coming back, a knock-out resumes at the
  restart point if the barrel was touched before the exit took over, and
  play begins at the level's start if it wasn't.

Still gone: a **bunch** taken (`bunches.md`), a **letter** taken, the
**restart barrel** once touched, the **figure** taken (`figure.md`), an
**extra-life item** taken (kinds `rising-extra-life-1`,
`rising-extra-life-2` and `extra-life-3`, `extra-lives.md`), and a
**hidden bunch** (kind `hidden-bunch`, `hidden-objects.md`) once
struck, together with the
bunch it released, whether that bunch was taken or not. A hidden object
of a falling kind (kinds `hidden-barrel` and `hidden-metal-barrel`) once
set off is still gone in the same way, together with the barrel or metal
barrel it released, whatever was done with that item before the exit
took over: left where it came to rest, picked up and put down somewhere
else, thrown, or still carried. After coming back the item is neither
where it came to rest nor where it was put down, and a fall onto the
hidden object's place from 170 px above releases nothing. Still carried
when the exit takes over, the item is still there, in the level, as the
walk-out begins, as for a barrel below; it is not in the scene or the
room, and the lead carries nothing when the next area begins. Without
an exit, an item put down somewhere else was gone too once the lead had
gone far away and come back within the same visit. The **animal
crate** (kind `animal-crate`, `animal-crate.md`) once broken is also
still gone, and so is the animal that came out of it if it was left
there and not mounted: neither the crate, its broken pieces nor the
animal is at its place after coming back, so nothing is there to land on
or to mount. A **holder** (kind `holder`, `holder-breaking.md`) once
broken is still gone too, and the character it freed is still there:
after coming back that character follows the lead as it did before the
exit (`following.md`).
They stay gone
after coming back. They don't appear when the view comes back over their
places, and nothing is taken or struck there again: the count, the
lives, the figure count and the set of letters don't change. For these
objects, "the rest of the visit to the level" in those files goes on
through the scene and the room.
This held for both `extra-life-3` items of the first level, taken
together before leaving: after either exit neither was there when the
view came back to their places, and holding the lead on their places
gave no lives. Not taken before the exit, both were there after either
exit and gave 3 lives each.

There again, as when the level begins:

- **single collectibles** (`collectibles.md`) taken before: they are
  drawn again and can be taken again, each adding one to the count
  again. For them, coming back starts a new visit to the level;
- a **small walker** (kind `small-walker`) bounced on before
  (`walker.md`, "Being bounced on"): it is there again when the view
  comes to its place, walking as the level places it;
- a **medium walker** (kind `medium-walker`) bounced on before: it is
  there again in the same way;
- a **large walker** (kind `large-walker`) bounced on before: it is
  there again in the same way. This held for both large walkers of the
  first level, each bounced on before leaving. Without an exit, a large
  walker bounced on was not there again when the lead went far away and
  came back within the same visit (one run);
- the **thrower** (kind `thrower`, `thrower.md`) knocked out before,
  by the ground action (`ground-action-enemies.md`, "The thrower"), by
  a bounce (`thrower-contact.md`) or by a roll (`thrower-contact.md`,
  "Rolling into the thrower"): it is there again when the view comes to
  its place, as the level places it. Without an exit, the thrower
  knocked out in any of these ways was not there again when the lead
  went far away and came back within the same visit (one run each);
- the **ball** the level places (`thrower.md`, "Balls placed in a
  level") after it appeared and was removed before: it appears again
  when the view comes to its place, at the same position and one frame
  on, as when the level begins. Without an exit it doesn't come back
  (`thrower.md`);
- a **rising extra-life item** (kinds `rising-extra-life-1` and
  `rising-extra-life-2`, `extra-lives.md`) that appeared and went away
  untaken before (`extra-lives.md`, "Going away untaken"), whether it
  rose out of the level or went away past the window's left edge: it
  appears again when its entry comes into the window, as when the level
  begins, and taking it gives its lives. Without an exit it doesn't come
  back (`extra-lives.md`);
- a **barrel** (kind `barrel`, `carrying.md`) picked up, thrown and
  broken against a wall before (`thrown-objects.md`, "Breaking against a
  wall"): it is at its place again when the view comes there, standing
  as the level places it;
- a **barrel** picked up and put down unbroken somewhere else
  (`carrying.md`, "Putting down"): it is at its place again in the same
  way, and nothing is where it was put down;
- a **barrel** still carried when the exit takes over: the exit takes
  over as usual and the barrel is still there, in the level, as the
  walk-out begins, and moves as in "The walk-out" ("An object in the
  lead's hands"). It
  is not in the scene or the room, and the lead carries nothing when the
  next area begins. After coming back it is at its place again in the
  same way.
- a **holder** (kind `holder`, `holder-breaking.md`) picked up and put
  down unbroken somewhere else: it is at its place again in the same
  way, still holding the missing character, and nothing is where it was
  put down. The missing character is still missing after coming back;
- a **holder** still carried when the exit takes over: as for the
  barrel, the exit takes over as usual and the holder is still there, in
  the level, as the walk-out begins, and moves as the barrel does. It
  is not in the scene or the room, it doesn't break, and the lead
  carries nothing when the next area begins. After coming
  back it is at its place again, still holding the missing character.

**Riding into the exit.** A lead riding the animal (`riding.md`) when
the exit takes over keeps it through the exit and after coming back:

- **The walk-out** is ridden, and is the same frame for frame as on
  foot ("The walk-out"): at the end of `T`, `y` becomes `ye` + 1; on
  `T` + 1, `vx` becomes −2 and `x` doesn't change; from `T` + 2, `x` goes
  down by exactly 2 every frame. The area's last frame comes as many
  frames after `T` as for a lead on foot. The animal stays under the lead
  all the way, at the lead's `x` in whole px.
- **In the scene and the room** the lead is on foot and the animal isn't
  there. Every frame there is as for a lead that left on foot.
- **Back in the level** the lead is riding the animal from frame 0 of
  the next area, and the animal is at the lead's `x` in whole px from
  frame 1. After the scene, frames 0 to 27 of "Walking in" are exactly
  as given there. From frame 28 the player has control and the speed
  follows `riding.md` ("Horizontal speed"), not `ground-speed.md`: with
  nothing held, `vx` goes from 768/256 to 744/256 on frame 28 and to
  721/256 on frame 29. After the room, the level begins as in
  `level-start.md`, with the same position and speed on every frame up
  to and including the landing frame. From the next frame the speed
  follows `riding.md` in the same way: with nothing held, 350/256 on the
  landing frame became 340/256.
- After either exit, Right and Jump then worked as in `riding.md`
  (a ridden jump). There is no second animal: neither the crate, its
  broken pieces nor another animal is at the crate's place.

**Knocked off before the exit.** If the rider was knocked off the animal
(`riding-walkers.md`, "Being knocked off") before the exit took over,
the animal is gone after coming back through either exit: it is neither
at the crate's place nor where it was running, and no crate or broken
pieces are there either. This holds whether the animal was still in the
level, running away in the view, when the exit took over, or had already
run out of it. The animal is not in the walk-out, the scene or the room,
and the lead is on foot throughout.

Other kinds of object are not covered (see "Not covered").

## Example

Synthetic, computed from the rules above, with an exit at `xe` = 300,
`ye` = 200.

A lead walking left with `vx` = −1.75 ends a frame at `x` = 333.5 (`I` =
33, not in reach while walking), and the next frame at `x` = 331.75
(`I` = 31): that frame is `T`. At the end of `T`, `y` = 201. On `T` + 1,
`x` stays 331.75 and `vx` = −2; on `T` + 2 it is 329.75, and on `T` + `k`
it is 331.75 − 2(`k` − 1). The first frame with `X` ≤ 296 is `T` + 19
(`x` = 297.75 on `T` + 18, 295.75 on `T` + 19), so `F` = `T` + 19. The
brightness is 15 on `T` + 19, 14 on `T` + 20 and `T` + 21, 1 on
`T` + 46 and `T` + 47, and 0 on `T` + 48 and `T` + 49. `T` + 49, with
`x` = 235.75, is the last frame in the area.

Walking in, synthetic, with a start record point (`xs`, `ys`) = (400,
150), walking right (`d` = +1): the lead is at `x` = 320 on frame 0, 323
on frame 1, 398 on frame 26 and 404 on frame 27, always at `y` = 150. An
other character is at 292 on frame 0 and 370 on frames 26 and 27. The
brightness is 0 on frames 0 to 2, 1 on frames 3 to 5, 14 on frames 42 to
44 and 15 from frame 45. With nothing held, frame 28 has `vx` = 2.625
and `x` = 406.625; with Right held, `vx` = 2.875 and `x` = 406.875.
On frame 27 the other character's trail becomes 370, 371, 373, 375,
377, 378, 380, 382, 384, 385, 387, 389, 391, 392, 394, 396 (all at
`y` = 150), and the lead's 404 is added at the end of the frame. The
other character stands on the ground at 370 and the newest point is
then 396, 26 px away: on frame 27 the catching-up mark stays clear and
it doesn't step on. On frame 28 the newest point is 404 (34 px): the
mark is set, and it steps on from frame 29, reaching 371 on frame 30.
Walking left (`d` = −1) from an other character at 500, the points are
500, 498, 496, 494, 493, … , 473 (the last is 500 + ⌊−420 / 16⌋).

## Checks

- **Takeover.** 400 runs from a random point 40 to 92 px right of the
  floor exit, with random inputs biased to the left (walking, running,
  rolling, jumping, crouching, stopping, short presses of Right):
  37 680 frames judged by the rules above, 329 takeovers (288 by the
  floor exit, 41 by the upper one, reached by climbing), no frame
  different. A second, held-out set of 200 runs from 34 to 116 px right
  of it: 21 575 frames, 144 takeovers, none different. Frames in states
  not in the table, with `I` in reach, were not judged (see "Not
  covered").
- **Reach edges.** For each state in the table, the lead was put at
  every whole-px `I` around the edge, with fractions 0 to 0.99, after
  being brought into the state away from the exit: taken over up to the
  reach, never at reach + 1. A lead moving right (walking, or turning
  with `vx` still above 0) was never taken over. A lead put 30 px right
  of the exit moving right at 0.6 px per frame, with no input, slowed
  down and was taken over on the first frame its `vx` was 0, 34 px
  right of it.
- **Vertical range.** With the lead standing on the floor facing right
  at `I` = 36, and the exit moved up and down in steps of 1 px (a change
  made for the test while the game ran), the exit took over with
  `y` − `ye` from −39 to 35 and not at −40 or 36.
- **Walk-out and leaving.** 325 runs that went on until the area changed
  (273 with the lead alone, 52 with the other character following; 289
  through the floor exit, 36 through the upper one), with random inputs
  (Left, Right, Jump, Action, Up, Down, Right with Jump or Action) on
  12 193 of the 15 466 frames from `T` + 1 on: every frame was as above
  (35 726 frames). Three runs started a turn: the lead faced right on `T`.
- **Other places.** In copies of the image in which an entry far from
  the left end was given one of the exits' references, as a class 1 or a
  class 2 entry (three copies), an exit was there: a lead walking right
  past it went on, and walking back left it was taken over with `I` =
  30, walked out, and left the area on `F` + 31, `F` being the first frame
  with `X` ≤ `xe` − 4. Each reference led to the same area as in its own
  place.
- **Walking in and the scene.** 700 runs with random inputs (Left,
  Right, Jump, Action, Up, Down, and Left or Right with Jump or Action)
  from frame 1 of the next area: 300 through the floor exit with the lead
  alone and 100 with the other character following, each through the
  scene and back into the level, and 300 through the upper exit into the
  room. 1 100 walk-ins: every frame 0 to 27 of the lead (29 700 frames)
  and of the other character (5 400 frames) was as in "Walking in", and
  the brightness of frames 0 to 47 as well (52 800 frames). The view
  didn't change on frames 0 to 26 in any of them. Frame 28 was judged
  in the 621 walk-ins with only Left, Right or Action held on it (and
  no new Action press): `vx` and `x` were as given by `ground-speed.md`
  from `vx` = 3`d` in all of them. In all 400 scenes the lead stood
  still from frame 29, the fade-out was as above, frame 396 was the
  last, and play went on in the level after it (the first character
  leading in all of them). In some runs the lead
  walked back into the floor exit afterwards and went through the scene
  again.

- **The scene's pictures.** 12 runs of the scene, from the frame after
  the area changed to the scene's last, each read on screen, frame by
  frame, with the sprite layer alone (the picture of a frame on the next
  video frame): the first character leading alone (3 runs, one with
  nothing held and two with random pad inputs), the first leading with
  the second behind it (3 runs, the lead read in two of them and the
  follower in one), the second leading with the first behind it (6
  runs: the second read with nothing held and with three random pad
  inputs, the first read with nothing held and with one random pad
  input). 4 505 frames were compared with the lists in "Pictures in the
  scene" and none differed; no frame was left unmatched but the ones
  named below. Not compared: the first character behind the second on
  frames 0 to 14 and 29 to 31, the second behind the first on frames 0
  to 26 (the figure was beyond the screen's right edge or partly covered
  by the lead, and what the screen matched there was another picture),
  and the second character leading on frames 5, 7, 363 and 364. The
  scene's length by who leads was measured in 4 more runs from the idle
  state with both characters after the level's entrance: with the
  first leading (no Swap press, and two presses) the fade began on
  frame 366, with the second leading (one press, and three) on frame
  336, each press followed by 80 frames with nothing held before Left was held.
- **The area behind the first wall exit.** 800 runs through the
  first wall exit into its area, the lead alone, 500 of them with random
  inputs biased to the right from frame 1 and 300 from frame 28: every
  frame 0 to 27 (22 400 frames) was as in "Walking in" with `d` = +1,
  the same in every run, and the brightness of frames 0 to 45 as well.
  With nothing held on frame 28 (89 runs), `vx` was 2.625 there.
- **The area behind the second wall exit.** 3 201 runs through the
  second wall exit into its area, the lead alone, entering 0 to 60
  frames after the exit opened, with random inputs or none from frame 1
  (`picture-game.md`, "Checks"): every frame 0 to 27 (89 628 frames) was
  as in "Walking in" with `d` = +1, the same in every run, with the
  brightness of frames 0 to 27 as in "Fade-in". In a copy of the image
  with the area's start record moved 40 px left, the lead's `x` was 40
  px less on frames 0 to 27 in all 12 runs, and on frames 0 to 26 its
  `y` was the same (there is no floor under the lead there: it was
  already falling on frame 27).
- **The records decide where.** In a copy of the image with the room's
  start record moved 16 px left, the scene-return record moved 40 px
  right and the room-return record moved to another point, each arrival
  started from the moved record (frames 0 and 27 as in "Walking in", and
  the level's start as in `level-start.md` from the moved room-return
  point); the first level's own start record was left as it was.
- **Two characters.** 400 runs with the other character following,
  each started standing still after the level's entrance, the lead first
  moved by random inputs biased to the left (Left, Left with Jump or
  Action, Jump, Down, Down with Left, Right, Action, none) until an
  exit took it over, then random inputs of every kind (also Up) to the
  end: 200 from near the floor exit and 200 from the high ground near
  the upper exit. 399 left through an exit (151 into the scene, 248 into
  the room) and many went on: back into the level after the scene,
  through the room's way out (`level-end.md`) to the level's start, and
  into the scene again. In 100 of the runs the other character was
  nearer the exit than the lead on `T`. Checked frame by frame against
  `following.md` from the frame before `T`, with the trail as it was
  there: 399 walk-outs, 18 916 frames, no difference. 647 walk-ins (248
  into the scene, 248 into the room, 151 back into the level; 18 116
  frames) were as in "Walking in". From frame 27 on, starting from the
  trail given there: 34 167 frames back in the level after the scene
  and 39 855 in the room, no difference; the room's frames stopped at
  the first frame the lead was on or thrown by the tyre (127 visits).
  In the 248 scenes the other character stayed where it was on frame 27
  to the scene's last frame (66 054 frames). In 150 starts after the
  room it was as in `level-start.md` ("The second character") on every
  frame up to the lead's landing (16 200 frames), and followed the lead
  as in `following.md` afterwards (30 252 frames from its own landing,
  with the trail as it was there).
- **Coming back from the room.** 41 runs leaving the room with Right
  held, one with no input afterwards and 40 with random inputs from
  frame 1: the lead was the same as at the level's own start from the
  level map (`level-start.md`, no input) on every frame 0 to 89 (3 690
  frames).

- **Coming back.** 16 runs with the lead alone, eight through each
  exit, each run going through the scene or the room and back. Four sets
  of objects: one single collectible, a bunch and the first letter; a
  second bunch, the second letter and the restart barrel; the third and
  fourth letters and a third bunch; one small walker. For each exit and
  set, one run took the set (or bounced on the walker) before leaving
  and one, the control, didn't; after coming back, both runs went to
  each object again. In all 16 the count, the lives, the set of letters
  and the restart point were the same on coming back as on leaving. Runs
  that had taken a set took nothing again except the single
  collectible (the count went up by one); each control took the whole
  set. The restart barrel was there again only in the controls, and a
  knock-out afterwards resumed at the restart point only in the runs
  that had touched it. The small walker was there again after both
  exits. 88 checks in all, none different.
- **Coming back, more kinds.** 26 more runs with the lead alone, 13
  through each exit, in five sets: two hidden bunches on different
  floors, each struck by the ground action and its bunch either left
  where it landed or taken; the two rising extra-life items; the figure
  (with the thrower and its balls taken out of the level for these runs,
  so that the lead wasn't hurt); one medium walker bounced on. Each set
  had a control that did nothing before leaving. After coming back, each
  run struck, took or went to the same objects again. The count, the
  lives, the figure count, the set of letters and the restart point were
  the same on coming back as on leaving in all 26. The runs that had
  struck a hidden bunch got nothing from it again; each control got its
  bunch. The runs that had taken the extra-life items or the figure got
  no lives or figure again; the controls got 3 lives and one figure. The
  medium walker, gone after the bounce, was there again after both
  exits, as in its control. 146 checks in all, none different.
- **Coming back, extra-life items gone untaken.** 16 more runs with the
  lead alone, 8 through each exit, with the first level's three rising
  extra-life items going away untaken in two ways before leaving: each
  left to rise out of the level, or the view moved right until each had
  gone past the window's left edge. Each had a control in which the
  items hadn't appeared before leaving. In half of the runs each item's
  place was watched after coming back, in the other half each item was
  taken. The lives were the same on coming back as on leaving in all 16.
  After coming back, every run saw the same items appear as its control
  and got the same 4 lives from them. 64 checks in all, none different.
- **Coming back, barrel and crate.** 8 more runs with the lead alone, 4
  through each exit: the barrel picked up, carried left and thrown into
  the wall left of it, where it broke; the animal crate landed on, so
  that it broke and the animal came out and was left waiting; and a
  control for each that did nothing before leaving. After coming back,
  each run went to the barrel's place, or to the crate's place to land
  on the crate again if it was there. The count, the lives, the set of letters and the
  restart point were the same on coming back as on leaving in all 8. The
  barrel was at its place again in all 4 barrel runs. In the 2 runs that
  had broken the crate, neither the crate nor the animal was there, so
  there was nothing to land on and no animal came out; in the 2 controls
  the crate was there and broke, and its animal came out. The same four
  runs without an exit (going straight to the places instead): the
  broken barrel was not there again, and the animal was still waiting by
  the broken crate. 47 checks in all, none different.
- **Coming back, moved barrels.** 6 more runs with the lead alone, 3
  through each exit: the barrel picked up, carried left and put down on
  flat floor; the barrel picked up and still carried, with Action held,
  into the exit (the lead standing still within reach when the exit took
  over); and a control that did nothing before leaving. The count, the
  lives, the set of letters and the restart point were the same on
  coming back as on leaving in all 6. In both carried runs the barrel
  was there on every frame up to the walk-out's first frame and was not
  in the scene or the room, and the lead wasn't carrying when the next
  area began. After coming back, the barrel was at its place again in
  all 6, and nothing was at the put-down place. The put-down run without
  an exit (the lead going far enough away for the barrel to be removed,
  then back) also found the barrel at its place again, as in
  `../formats/placements.md` ("Objects put down elsewhere"). 45 checks in all, none different.
- **Coming back, large walkers.** 5 more runs with the lead alone, 2
  through each exit and one without an exit: the lead standing in the
  path of the first large walker until it came near and then bounced on
  it, then bounced on the second as soon as it came into view, so that
  both were gone without the lead being hurt; and a control for each
  exit that did nothing before leaving. After coming back, each run went
  to both walkers' places. The count, the lives, the set of letters and
  the restart point were the same on coming back as on leaving in all 4
  exit runs. Both large walkers were there again after both exits, as in
  the controls. In the run without an exit (the lead going far left for
  120 frames, then back) neither was there. 29 checks in all, none
  different.
- **Coming back, the thrower.** 5 more runs with the lead alone, 2
  through each exit and one without an exit: the lead put on the
  thrower's ledge, facing it, 45 px left of it, and making the ground
  action, so that the thrower was knocked out without the lead being
  hurt; and a control for each exit that did nothing before leaving. In
  all 5 the balls were taken out of the level, so that they couldn't
  hurt the lead. After coming back, each run went to the thrower's
  place. The count, the lives, the set of letters and the restart point
  were the same on coming back as on leaving in all 4 exit runs. The
  thrower was there again after both exits, as in the controls. In the
  run without an exit (the lead going far left for 120 frames, then
  back) it was not there. 29 checks in all, none different.
- **Coming back, the thrower bounced on or rolled into, and the placed
  ball.** 13 more runs with the lead alone. For the thrower, 2 runs
  through each exit and 2 without an exit: the lead held still 30 px
  above the thrower and let fall onto it, so that it bounced and the
  thrower was knocked out; or the lead on the thrower's ledge, 45 px
  left of it, rolling right into it; and a control for each exit that
  did nothing before leaving. The balls were taken out of the level in
  these, so that they couldn't hurt the lead, and after coming back
  each run went to the thrower's place. For the placed ball, with the
  thrower taken out of the level so that it threw nothing: one run
  through each exit and one without an exit in which the lead went
  near the ball's place, the ball appeared and was removed ("Leaving
  the picture") about a second later, before the lead left; and a
  control for each exit in which the view never came to the ball before
  leaving. After coming back, each went near the ball's place again
  and watched for 600 frames. The count, the lives, the set of letters
  and the restart point were the same on coming back as on leaving in
  all 10 exit runs, and the lead wasn't hurt before leaving in any of
  the 13. The thrower was knocked out in all 6 runs that bounced on it
  or rolled into it, was there again after both exits, as in the
  controls, and wasn't there in the 2 runs without an exit. The ball
  appeared again after both exits, at the same position as in the
  controls and as before leaving; in the run without an exit it didn't
  appear. 89 checks in all, none different.
- **Coming back, the holders.** 8 more runs, each starting with the
  lead alone: for each of the first level's two holders, one run through
  each exit and one without an exit in which the lead picked the holder
  up and threw it, so that it broke and the missing character joined;
  and a control for each exit that did nothing before leaving. In all 8
  the balls were taken out of the level, so that they couldn't hurt the
  lead. After coming back, each run went to both holders' places. The
  count, the lives, the set of letters and the restart point were the
  same on coming back as on leaving in all 6 exit runs, and the lead was
  never hurt. In the 4 exit runs with a holder broken, the character it
  freed was following the lead before leaving, was next to the lead as
  the next area began and was still following it after coming back; the
  broken holder was not there again, and the other holder was. In the
  controls both holders were there and the character stayed missing. In
  the 2 runs without an exit (the lead going far away for 120
  frames, then back) the broken holder was not there either. 100 checks in all,
  none different.
- **Coming back, holders moved.** 12 more runs, each starting with the
  lead alone and with the balls taken out of the level. For each of the
  first level's two holders: one run through each exit in which the
  lead picked the holder up, carried it some way off and put it down
  unbroken; one run through each exit in which the lead was still
  carrying it as the exit took over; and one run without an exit in
  which it was put down, the lead went far away for 120 frames and came
  back to the holder's place. Two controls did nothing before leaving.
  In the 10 exit runs the count, the lives, the set of letters and the
  restart point were the same on coming back as on leaving, the lead was
  never hurt before leaving, and the missing character stayed missing
  throughout. In the 4 carried runs the walk-out began with the holder
  still there, and no holder was in the scene or the room. After coming
  back, in every run, both holders were at their places and nothing was
  where a holder had been put down. In the 2 runs without an exit the
  holder put down was back at its place when the lead came back to it,
  as "Objects put down elsewhere" in `../formats/placements.md` gives.
  156 checks in all, none different.
- **Riding into the exit.** 5 more runs, each starting with the lead
  alone and with the balls taken out of the level. In each, the lead
  broke the animal crate and mounted the animal with a jump from the
  boards. It rode through each exit with nothing held, or stayed in the
  level without an exit. Two controls for the exits broke the crate and
  left the animal waiting. In both exit runs, the walk-out frames were
  as above, with the rider and the animal together every frame. The
  walk-out lasted as many frames as in the control, and the scene or room
  frames were the same as in the control. Every frame of the first 200
  after coming back was riding, with one animal. Positions and speeds
  matched the control up to frame 27 (scene) or the landing frame (room),
  and on every later frame the speed was as `riding.md` gives with
  nothing held. Then the rider rode right for 40 frames and jumped. The
  lives were kept. In the controls no animal was there after coming
  back. Without an exit, the lead was still riding and the boards were
  still there. 54 checks in all, none different.
- **Knocked off before the exit.** 5 more runs, each starting with the
  lead alone and with the balls taken out of the level. In each, the
  lead broke the animal crate, mounted the animal, rode for 30 frames
  and was then put, riding, right next to a small walker, so that the
  rider was knocked off. In 2 runs the
  exit took over 60 frames after the knock-off, with the animal still
  in the level about 150 px from the lead and running away; in 3 runs
  the lead was first held still out of the walkers' reach for 400
  frames, and by then the animal was gone. These 3 were one through
  each exit and one without an exit. In every run the lives were kept,
  no animal was there on any frame while leaving, in the scene or room
  or in the first 200 frames after coming back, and the lead was never
  riding. Neither an animal nor the crate's boards were at the crate's
  place or along the animal's run, after either exit or without one.
  59 checks in all, none different.
- **Coming back, hidden objects set off by a fall.** 10 runs with the
  lead alone and the small walkers taken out of the level, for each of
  the first level's two falling kinds: one run through each exit in
  which the lead was put 170 px above the hidden object, with no speed
  and no input, so that it fell, landed on it and set it off, and the
  item came to rest and was left there; a control for each exit that did
  nothing before leaving; and one run without an exit (the lead going
  far left for 120 frames, then back). After coming back, each run put
  the lead 170 px above the hidden object's place again in the same way.
  The count, the lives, the set of letters and the restart point were
  the same on coming back as on leaving in all 8 exit runs, and the lead
  was never hurt. In the runs that had set it off, neither the hidden
  object nor its item was there after coming back, and the second fall
  released nothing. In the controls the hidden object was there, and
  the fall released the item, which came to rest where it had in the
  other runs before leaving. The runs without an exit found neither the
  hidden object nor its item either. 74 checks in all, none different.
- **Coming back, the item of a falling kind moved.** 16 runs with the
  lead alone and the small walkers taken out of the level, 8 for each of
  the first level's two falling kinds. Each run set the hidden object off
  as in the runs above, then the lead walked into the item from its right
  with Action held and picked it up. Then, before leaving: put down with
  Down on the release, at rest on the floor beside the lead 30 frames
  later; still carried, with Action held while walking left
  into the exit; or thrown left at once, after which it was gone from the
  level within 200 frames. One run of each through each exit, and one
  run without an exit for put down and for thrown (the lead going far
  left for 120 frames, then back). The count, the lives, the set of
  letters and the restart point were the same on coming back as on
  leaving in all 12 exit runs, and the lead was never hurt. A carried
  item was still there on 19 to 37 frames of the walk-out and on no frame
  of the scene or the room, and the lead wasn't carrying on frame 0 of
  the next area. After coming back, and in the runs without an exit
  after the lead came back, neither the item nor the hidden object was at
  the hidden object's place, at the item's rest place or at the put-down
  place, and a fall onto the hidden object's place from 170 px above
  released nothing. 138 checks in all, none different.
- **An object in the lead's hands.** 38 runs with the lead alone,
  logging every frame, in which the lead picked up the first level's
  barrel or one of its two holders and then came to an exit carrying it.
  For the barrel: walking left into each exit with Action held (4 runs
  per exit at different phases of the walk; in one of each the lead had
  been stopped right of the exit before it took over), landing in from
  three jumps, standing still facing left (3 runs) or right (6 runs)
  near the floor exit, letting go of Action 1, 3, 5 or 10 frames before
  `T` or on `T`, `T` + 1 or `T` + 2, and holding Right, Jump, Down, Up,
  Right with Jump, Right with Action, or nothing from `T` + 2. For each
  holder: walking into each exit. In 16 more runs the barrel was moved
  during the walk-out (a change made for the test while the game ran):
  1 to 9 px down on `T` + 11, or 68 to 76 px left and 100 px up on
  `T` + 17, to find where it is removed. In 49 runs the object was still
  in the hands at the end of `T`; in the run that let go 10 frames
  before `T` it had already been thrown, as in `carrying.md`, and in 4
  standing runs no exit took over. Over the 1 781 frames from `T` + 1 to
  the removal in those 49 runs, `x`, `y`, `vx` and `vy` were as the rules
  above give on every frame, to 1/256 px, and it was removed on the
  frame they give. Without a change it was removed below the picture 37
  frames after `T` + 1 at the floor exit, and left of `x` 0 24 frames
  after `T` + 1 at the upper exit. Moved down, it was still there after
  a move that left its `y` 255.375 below `U`, and removed after one that
  left it 256.375 below. Moved left, it was there at `x` 0 with `x` − `V`
  0 (upper exit) or −27 (moved), and removed on the next frame. For the
  reach rows the lead stood still carrying the barrel: facing left with
  an `x` fraction of 0.68 it was taken over at `I` 30 but not at 31 or
  32; facing right with a fraction of 0.17, at `I` 29 to 32 but not at
  33 or 34.
- **In a trace.** Six runs from the level's first frame into the
  floor exit, with Left alone, Left with Action, short presses of Right
  just inside the reach (two of them taken over facing right), and Jump,
  Action, Down and Right pressed during the walk-out, plus a walk-out
  through each of the two wall exits (`wall-exits.md`): every row from
  `T` + 1 to the area's last frame was as in "In a trace", and the
  original's next frame was in the other area.

## Not covered

- Exits leading right (other than the wall exits of `wall-exits.md`),
  exits in other levels, and where an exit leads in general: which area comes next is a setting of
  the object (its definition, `../formats/placements.md`), and how an
  area is entered (walking in, and which way, or as a level begins) is
  given above only for the first level's areas.
- The kind of the scene's class 2 entry that isn't the end marker, and
  why the scene is 30 frames shorter with the second character leading
  (whether it is the character or the pictures it shows that decide
  it, and the scene with the second character alone). The pictures the
  characters show in the scene on the frames named as not compared in
  "Checks", the other character's pictures there while it is covered
  by the lead, and the pictures with something carried, with an animal
  or after a Swap pressed during the walk-out. The room's
  data is in `../formats/graphics.md`, `../formats/level-ground.md`,
  `../formats/placements.md` and `../formats/level-start.md` ("The
  first level's room" or "the room's start record"), and its view in
  `level-ends.md` ("In the first level's room"); the kinds of the
  room's placed objects other than the tyre and the end marker are not
  covered.
- What else is kept when play comes back to the level after the scene
  or the room: tyres moved,
  an animal left after being mounted other than by a knock-off by a
  walker, objects of kinds the spec doesn't
  name, and objects other than a barrel or a holder carried through the
  exit. Whether a broken barrel
  ever comes back during the same visit without an exit (it didn't in
  the one run above). Coming back with two characters, other than
  the character freed by a holder broken in the same visit (the other
  runs in "Coming back to the level" had the lead alone).
- The other character walking into the area behind the first wall
  exit (only the lead alone was measured there), and the lead's height
  on frame 27 of other walk-ins where the floor isn't at the record's
  point.
- Riding into the exit with anything held during the walk-out or the
  walk-in, or with the second character; whether the animal is drawn
  on frames 0 to T − 1 of the level's start after the room
  (`level-start.md`), and how the rider and the animal are drawn during
  the walk-out and the walk-in.
- The other character's facing from `T` on and after a walk-in
  (`following.md` gives its turns only away from take-offs and
  landings), and the other character meeting the room's tyre: in the
  room, the other character was checked only up to the first frame on
  which the lead stood on the tyre or was thrown by it (`tyre.md`).
- How far left of the exit the reach goes, and the reach of states not
  in the table: rolls stopping or getting up (taken over at `I` up to 28
  to 30, by picture), crouching after the slide has stopped (up to 28 in
  the cases measured), turning while facing left, carrying an object
  while moving, turning or landing (walking in at 3 px per frame was
  taken over at `I` 29 and 30, the frame before at 32 and 33), riding,
  and the frames of a landing after the landing frame. How far the
  vertical range changes with the lead's state.
- What a trace shows from the first frame of the next area on, and
  which picture shows each step of the fade.
- Walkers, thrown objects or other objects meeting the lead during the
  walk-out, and the lead being hurt or knocked out then; an object let
  go in the walk-out meeting a walker; where the let-go object is drawn;
  the metal barrel released by a `hidden-metal-barrel` carried into the
  exit (it was gone 19 to 37 frames into the walk-out, as the rules
  above would give, but wasn't logged frame by frame).
- Pausing during the walk-out.
