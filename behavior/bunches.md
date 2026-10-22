# Bunches

Titles: `title-1`

A **bunch** is a collectible worth ten. It is placed in a level, and the
player takes it by touching it, as with single collectibles
(`collectibles.md`). After that, its ten **items** are released one at a
time, and each one adds one to the collectible count. This file covers
when a bunch is taken, when each item is released and when each one
reaches the count.

Frames are logic frames at 60 Hz, numbered as in a trace. `x` grows to
the right. Horizontal positions are level positions as in `view.md`.
Vertical positions are **level y** as in `view-vertical.md`: they grow
downward, measured from the top of the level. The player's **feet line**
`F` is as in `view-vertical.md`.

Measured on the first level with the first character leading and alone.
The bunch was moved to chosen offsets from the player, one pixel apart.
The player was in poses reached by inputs only: standing, walking,
running, crouching, rolling, every frame of jumps (Jump held, released
early, from a run, facing left), and landing. Separately, the bunch was
moved to chosen places in and out of the picture as its items were
released. About 16 000 frames of play by inputs alone were then checked
against the rules below. These came from one route past one bunch and 80
variations of its approach jump (start, length, extra jumps, rolls,
crouches). That play included 56 pickups, all mid-jump, and each came on
the frame the rules give. In all 56, every item reached the count on the
frame the rules give, including 35 where some items flew and some were
counted at once. Two characters (the second character leading, and the
character that follows) were measured separately; see "Two characters"
under "Pickup ranges".

## A bunch's position

A bunch does not move. Its **position** is a point (`xb`, `yb`) in the
level. Its image is drawn around that point and spins, so the image's
size changes from frame to frame. Every frame measured fell inside
columns `xb` − 13 to `xb` + 15 and rows `yb` − 15 to `yb` + 8.

A level's placement list (`../formats/placements.md`) places a bunch with
an entry whose reference's kind is `bunch`: `xb` is 1 px left of the
entry's level x and `yb` is the entry's level y (see "Positions" there),
as for letters (`letters.md`). This was checked against "When a bunch is
taken" below in 24 runs with entries at chosen places
(`level-end.md`, "Walkers and bunches"). A bunch can also come out of
a hidden object (`hidden-objects.md`), with its position given there.

During a level's walk-out a bunch is still taken; see `level-end.md`.

## When a bunch is taken

A bunch is taken on frame `n` when both of these hold **at the end of
frame `n`**, after the player has moved on that frame:

1. it hasn't been taken already;
2. the player's offsets from it (below) are inside the ranges for the
   player's state on frame `n` (see "Pickup ranges").

So a bunch is taken on the first frame that ends with the player in
range. Call this frame `P`.

A taken bunch stays taken for the rest of the visit to the level. In
one case the view was moved back over a taken bunch's place, and the bunch
did not come back. After a knock-out it is there again
(`restart-barrel.md`, "What is there again after a knock-out"); after an
exit it stays taken (`exits.md`, "Coming back to the level"). After
finishing the level, not covered.

### Offsets

Using whole pixels, let

- `X` be the player's `x` rounded down;
- **`I`** be how far the player is past the bunch in the direction the
  player is facing: `X − xb` when facing right, `xb − X` when facing
  left;
- **`D`** = `F − yb`, how many px the feet line is below the bunch's
  position (negative when it is above).

Because `I` is measured in the facing direction, each range below holds
for both facings. For example, a standing player facing right takes a
bunch with `X − xb` from −15 to 21. Facing left, it takes one with
`X − xb` from −21 to 15.

## Pickup ranges

A bunch is in range when `I` and `D` are both within the ranges for the
player's state (ends included).

| State | `I` | `D` |
|---|---|---|
| Standing, walking, running, turning, Down + Action ground action | −15 to 21 | −8 to 40 |
| Crouching (Down held on the ground) | −18 to 12 | −12 to 31 |
| Rolling | −13 to 21 | −4 to 38 |
| Jumping, from the take-off frame `T` to `T` + 5 | −15 to 21 | −8 to 40 |
| Jumping, from `T` + 6 on | −15 to 18 | 13 to 57 |
| Carrying an object, from the pick-up to the end of a throw or a put-down | −14 to 16 | −8 to 40 |

`T` is the frame carrying `jump_start` (`jump-arc.md`). For the first six
frames of a jump, starting with `T`, the ranges are the standing ones.
From `T` + 6 until the jump lands, the last row applies on every frame,
whatever the vertical speed and the horizontal speed, and whether Jump
is still held or not (after the landing, see "Landing" below). (Single
collectibles are different. Their jump ranges depend on the vertical
speed.)

**After a roll.** Let `R` be the first frame after the roll's 29
frames, the frame `roll-input.md` calls D ("How a roll ends"). When the
roll ends in a stop (`roll_end` on `R`), the rolling row still applies
from `R` to `R` + 8, and the standing row applies from `R` + 9 to the
end of the stop. When a roll carries on into running (the player still
in `roll` on `R` to `R` + 2, `roll_end` on `R` + 3), the rolling row
applies on `R`, `R` + 1 and `R` + 2, and the standing row from `R` + 3.
`I` is measured in the direction the player faces on that frame, also
when the player turns during the stop. This was measured on flat
ground, after slow and normal rolls to the left and to the right, with
the bunch moved to every offset one pixel apart around the player on
every frame from `R` − 1 to `R` + 17 of four stops (one of them turning
on `R`, another on `R` + 8) and from `R` − 1 to `R` + 4 of a roll that
carried on (about 430 000 placements). Separately, in 18 244 runs of
play by inputs with a roll, random presses during the roll and the
stop, and the bunch placed once beforehand near the edge of the
standing row, 4 096 pickups came on stop frames and 280 on the three
frames of a roll carrying on, and every run took the bunch on the frame
these rules give; 21 of those pickups came in stops after a fast roll
(222 such stops). Using the rolling row on every frame of the stop would
have given the wrong frame in 3 339 of them; switching to the standing
row on `R` + 8 or `R` + 10 instead of `R` + 9, in 3 334 and 3 311.
Stops on slopes were not measured.

**Landing.** A jump lands on frame `L` (the frame carrying `land`).
The last row applies on every frame from `T` + 6 that ends with the
player in the airborne pose (`ball-contact.md`, "Which pose the player
has"), and that pose goes on for some frames after `L`. From the first
frame that ends with the player upright again, the standing row
applies, whatever the player then does (stands, walks or runs).
Measured on flat ground:

| Landing | Jumping ranges up to | Standing ranges from |
|---|---|---|
| Jumps in place with Jump held 3, 10, 14 or 30 frames, also turning left in the air, or with Action and a direction pressed from `L` + 2; jumps while walking left, with Left held through the landing or released in the air; all onto the floor they started from | `L` + 3 | `L` + 4 |
| Jumps while walking right, off a ledge onto a floor 176 px lower, with Right held through the landing or released in the air | `L` + 4 | `L` + 5 |
| Action and a direction held through the landing, so that the player runs on, onto the floor the jump started from | `L` | `L` + 1 |
| The same, off the ledge onto the floor 176 px lower | `L` + 1 | `L` + 2 |

In every case the switch came on the frame the airborne pose ended,
whether the player went on standing, walking or running. When the
airborne pose ends after other landings is not covered
(`ball-contact.md`).

This was measured with the bunch moved to every offset one pixel apart
around the player (`I` −27 to 29 facing right, −29 to 27 facing left,
`D` −20 to 60) on every frame from `L` − 1 to `L` + 10 of twelve
landings (664 848 placements); every frame had exactly the jumping or
the standing ranges. Separately, in 12 842 runs of play by inputs with a
jump (random inputs in the air and after the landing), with the bunch
placed once beforehand near the edge of the ranges, 2 459 pickups came
on `L` to `L` + 10, 975 of them on the ground in the airborne pose, and
every run took the bunch on the frame these rules give. Using the
standing ranges from `L` would have given the wrong frame in 1 304 of
them; from `L` + 4 whatever the pose, in 429.

**Ground action.** During the Down + Action ground action
(`ground-action.md`), the standing row applies on every frame from the
press frame `G` to `G` + 58, whatever the action's direction and speed,
including when the player moves against its facing. It also applies
while the action carries the player down a slope (1 px down for every
4 px across), and in the air after the action carries the player off a
ledge, on every frame measured until the action ended (what the action
does at a ledge is not covered in `ground-action.md`). This was measured
with the bunch moved to every offset one pixel apart around the player,
on every frame of five ground actions (direction 0, −1 and +1, facing right and
left, about 1 400 000 placements). Separately, in 19 162 runs of play by
inputs with ground actions and the bunch placed once beforehand near the
edge of the standing row, 6 090 pickups came on ground-action frames,
and every run took the bunch on the frame these rules give. Using the
crouching row on ground-action frames instead would have given the wrong
frame in 6 800 of them.

**Turning.** During a turn (`ground-speed.md`, "Facing and turns"),
from its first frame `U` to `U` + 7, the standing row applies on every
frame. `I` is measured in the direction the player faces on that frame:
the old facing from `U` to `U` + 3 and the new facing from `U` + 4. This
holds for turns from standing, walking and running, facing either way,
and for a new turn that starts on `U` + 8, whose first four frames again
use the facing it starts in. This was measured with the bunch
moved to every offset one pixel apart around the player, on every frame
of seven turns and the frames just before and after them (about
340 000 placements). Separately, in 16 489 runs of play by inputs with
turns and the bunch placed once beforehand near the edge of the standing
row, 6 396 pickups came on turn frames, and every run took the bunch on
the frame these rules give. Measuring `I` in the new facing on every
frame of a turn would have given the wrong frame in 3 310 of them.

**Falling without a jump.** When the player walks or runs off a ledge
(`walk-off.md`), the standing row applies on every frame from the frame
`E` on which the player leaves the ledge to the landing, on the landing
frame and on the frames after it, whether the player then stands still,
walks on or runs on. Unlike
after a jump, there is no switch to the jump row. `I` is measured in the
direction the player faces on that frame, also when the player turns in
the air. This was measured at a ledge without a face, with a floor 176 px
below it, with the bunch moved to every offset one pixel apart around
the player on every frame from `E` − 1 to 6 frames after the landing of
five walk-offs: walking right with the direction held and with it
released in the air, running right, walking left and releasing the
direction, and walking right then turning left in the air (about 950 000
placements). Separately, in 20 916 runs of play by inputs with walk-offs
from both ends of the ledge, random changes of direction, Action, Down
and Jump, and the bunch placed once near the edge of the standing row,
11 669 pickups came in the air during such falls (907 of them while
turning) and 547 on the landing frame or the 6 frames after it, and
every run took the bunch on the frame these rules give. Using the jump
row from `E` + 6 to 3 frames after the landing, as for a jump, would
have given the wrong frame in 10 312 of them. Walking off a ledge with
a steep face was not measured.

**Bouncing off a walker.** Let `B` be the bounce frame (`bounce.md`,
"The bounce frame"). On `B`, and on every frame from `B` + 3 until the
player lands, the ranges are `I` −17 to 27 and `D` −7 to 39, whatever
the height of the bounce, whether Jump is held, and whether the player
moves or slows in the air. No bunch is taken on the two frozen frames
`B` + 1 and `B` + 2 (`bounce.md`, "Two frozen frames"). A bunch moved
into range during them was taken on `B` + 3 when the player was still in
range at the end of `B` + 3, and not at all otherwise. Up to the frame
before `B`, the jump ranges apply as usual. On the landing frame and
after it, the standing row applies, whether the player stands, walks on
or turns. This was measured on flat ground with the bunch moved to every
offset one pixel apart around the player on every frame from `B` − 2 to
5 frames after the landing of six bounces: Jump held, Jump released on
`B` + 3, a short jump, facing left, moving right through the bounce and
landing walking, and the same slowing with Left and landing in a turn (2
315 682 placements); every frame had exactly the ranges these rules
give, and none on the frozen frames. Separately, in 11 390 runs of play
by inputs with a bounce (random waits, walks, jump lengths and changes
of direction and Action in the air), with the bunch placed once
beforehand near the edge of the ranges, 4 267 pickups came on bounce
frames (540 of them on `B`) and every run took the bunch on the frame
these rules give. Using the jump row on bounce frames instead would have
given the wrong frame in 4 468 of them; the standing row, in 2 987.
Bounces from a roll, bouncing again before landing, and landing on a
slope after a bounce were not measured.

**Carrying an object.** While the player carries an object
(`carrying.md`), the carrying row applies on every frame, whatever the
player does: during the 15 frames of a pick-up, standing, walking and
turning with the object, and on every frame of a jump with it, from the
take-off to the landing and on the frames after the landing. Unlike a
jump without an object, there is no switch at `T` + 6. Let `Q` be the
frame on which Action is let go (`carrying.md`, "Throwing"). In a throw,
the carrying row applies on every frame from `Q` to `Q` + 42, on the
ground and in the air, also after the object has left the hands on `Q` +
21; the standing row applies from `Q` + 43. In a put-down, the carrying
row applies from `Q` to `Q` + 12 and the standing row from `Q` + 13, the
frame the object is set down (`carrying.md`, "Putting down"). During a
turn with the object, `I` is measured in the old facing from the turn's
first frame to its fourth and in the new facing after that, as in
"Turning". The carrying row holds for both facings, like the others.

This was measured on flat ground with the bunch moved to every offset
one pixel apart around the player (`I` −29 to 31 facing right, −31 to 29
facing left, `D` −30 to 80) on every frame of eleven sequences: standing,
walking, turning and walking on, jumps in place (Jump held and released
early), while walking and turning in the air, throws standing, walking
and in the air, and a put-down followed by a new pick-up (501 frames,
3 392 271 placements). Every frame had exactly the carrying or the
standing ranges as above. Separately, in 17 199 runs of play by inputs
while carrying (random directions, jumps, throws, put-downs and new
pick-ups), with the bunch placed once beforehand near the edge of the
carrying or the standing row, 12 530 pickups came on carrying frames
(6 085 in the air, 2 898 during throws, 913 during turns, 186 during
put-downs, 18 during pick-ups), and every run took the bunch on the
frame these rules give. Using the standing row on carrying frames
would have given the wrong frame in 4 787 of them; the jump row from
the sixth frame in the air, in 3 302. In all of these, a thrown object
was taken out of play once it had left the hands, so that its landing
could not affect the player. Picking up in the air, and carrying on
slopes, were not measured.

**Being hit.** When the player is hit with no other character there
(`knock-out.md`), no bunch is taken from the hit frame `H` to the
level's last frame, whatever the offsets: not on `H`, not on the frozen
frames around the pause, not during the knock-back flight and its
landings, and not while lying still. A bunch whose offsets are inside
the ranges at the end of `H` − 1 has already been taken on `H` − 1, by
the row for the player's state on that frame. A bunch that is not taken
stays where it is until the level ends.

This was measured on flat ground with the bunch moved to every offset
one pixel apart around the player (`I` −29 to 31 facing right, −31 to 29
facing left, `D` −30 to 80) on every frame from `H` − 2 to the level's
last frame of four knock-outs: standing while a walker walks into the
player from in front, the same facing away from it, walking into a
walker, and with Jump pressed two frames before the hit, on the ground
(912 frames, 6 175 152 placements). On `H` − 2 and `H` − 1 the standing
row applied; from `H` on, no placement took the bunch. Separately, in
7 243 runs of play by inputs (random directions, jumps and crouches until
a walker hit the player), with the bunch placed once from `H` on at a
random offset near the standing row from a place the player would reach
within the next 40 frames, no run took the bunch; using the standing row
on these frames would have taken it in 5 657 of them. Hits in the air
and on slopes were not measured.

### Two characters

With two characters there (`second-character.md`), one leads and the
other follows it. The ranges above are the first character's when it
leads, with or without the second character following.

**Only the lead takes.** Whether a bunch is taken depends on the
leading character's offsets alone. The character that follows never
takes one, even with the bunch inside its own ranges and outside the
lead's. This holds with either character leading.

**Second character leading.** With the second character leading, its
own ranges apply:

| State | `I` | `D` |
|---|---|---|
| Standing | −12 to 11 | −10 to 38 |
| Walking, rolling | −18 to 12 | −6 to 38 |
| Crouching (Down held on the ground) | −19 to 14 | −9 to 25 |
| Jumping, from the take-off frame `T` until it lands | −18 to 12 | −6 to 38 |

Its jumps have one row: unlike the first character's, there is no
change at `T` + 6, and none with speed. With the second character
leading, Down + Action gives a roll and uses the rolling row. Its
landings are not covered.

For example, the second character standing facing left takes a bunch
with `xb − X` from −12 to 11, so with `X` from `xb` − 11 to `xb` + 12.

Measured on the first level. The bunch was moved to chosen offsets from
one character, one pixel apart, with both characters in poses reached by
inputs only: standing, walking, crouching, rolling (also with Action
held on from the roll), and frames of jumps from take-off to landing
(Jump held, released early, from a run, facing left). A letter was
measured the same way. With the second character leading, every pose
gave exactly the ranges above, facing either way, for both (801 125
placements). With the bunch or the letter moved to offsets from the
character that follows, in six poses of the lead with either character
leading (261 630 placements), it was taken on exactly the placements
that put it inside the lead's ranges. 54 773 of those placements put it
inside the follower's own ranges and outside the lead's; none of them
took it.

Separately, play by random inputs alone was checked against the rules,
at a bunch and at a letter, with each character leading: 543 287
frames with 511 pickups by the lead (509 mid-jump), each on the frame
the rules give, and no pickup on a frame they don't give. Another 22 pickups came in states listed under
"Not covered" (landing, bouncing) and weren't checked.

## Releasing the items

A taken bunch releases its ten items one at a time, on frames `P`,
`P` + 6, `P` + 12, … `P` + 54. Each item adds one to the collectible
count, either on the frame it is released or 17 frames later:

- An item **flies** to the count and arrives on its release frame + 17
  when, on its release frame, the bunch is both
  - at least 9 px right of the picture's left edge: `xb − V` ≥ 9; and
  - at least 11 px below the picture's top: `yb − W` ≥ 11.
- Otherwise the item is **counted at once**, on its release frame.

Here `V` is the view's horizontal position (`view.md`) and `W` its
vertical position (`view-vertical.md`), both as they were at the
**start** of the release frame (before the view moves on that frame),
rounded down to a whole px. The view position from the end of the frame
does not fit: in one natural run the bunch had `xb − V` = 8 by the end
of the release frame's view and its item still flew.

### The flying copy of an item

An item that flies is drawn as a copy of a collectible's image that moves
to the picture's top left corner, as in `collectibles.md` ("The flying
copy"), on the 17 frames `R` to `R` + 16 of its release frame `R`. With `V`
and `W` as above, let

    sx = xb − V − 9        sy = yb − W − 11

`sx` ≥ 0 and `sy` ≥ 0 exactly when the item flies (the two conditions
above). On frame `R` + `j`, `j` = 0 to 16, the top-left corner of the
copy's 16 × 16 square is on the picture at

    ( ⌊ sx · (16 − j) / 16 ⌋ ,  ⌊ sy · (16 − j) / 16 ⌋ )

so on `R` it is at (`sx`, `sy`), it moves in a straight line at a steady
speed (the fraction carried from frame to frame, positions rounded down)
and is at (0, 0) on `R` + 16, the frame before the count goes up. It
shows `spin-1` not mirrored (`collectibles.md`, "Pictures") on every
frame. Copies of several items are on the picture at once, 6 frames apart
along the same path while the view stands still; each is drawn from the
place set by the view on its own release frame. The bunch's own image
is not part of this and is covered under "Pictures".

For example, a bunch with `xb` − `V` = 75 and `yb` − `W` = 46 on frame
`R`: `sx` = 66 and `sy` = 35. The copy is at (66, 35) on `R`, (61, 32) on
`R` + 1, (57, 30) on `R` + 2, (33, 17) on `R` + 8, (4, 2) on `R` + 15 and
(0, 0) on `R` + 16.

Measured with the runs of `collectibles.md` ("Evidence for the flying
copy"): 267 copies followed the rule on all 17 frames, 2 were cut short
by a knock-out and followed the rule up to then, none left it
(4 547 frames; 2 406 showed `spin-1` unmirrored, 2 140 had another sprite
over the square, and 1 had other pixels of the count display in it, where
the copy passed through the count display's own place). Which of the two is in
front there is not covered.

There is no matching edge on the right or at the bottom. Items flew
from bunches placed up to 999 px right of the picture's left edge and
up to 260 px below its top. So, as the view scrolls on, a bunch
whose place leaves the picture to the left while it is releasing items
flies its first items and counts the rest at once.

For example, a bunch taken on frame `P` while the view stays still
with the bunch well inside the picture adds one to the count on each of
`P` + 17, `P` + 23, `P` + 29, … `P` + 71. If the player then keeps
running right and the bunch's `xb − V` drops below 9 just before frame
`P` + 30, the first five items arrive on `P` + 17, `P` + 23, `P` + 29,
`P` + 35 and `P` + 41. The other five are counted on `P` + 30, `P` + 36,
`P` + 42, `P` + 48 and `P` + 54. So the count goes up twice in some
6-frame steps.

Each item adds one as in `collectibles.md`, including the change from
99 to 0 that gives one more life. The count going up past 99 from a
bunch was not measured.

The count display (`count-display.md`) is shown from the frame the bunch
is taken.

## Pictures

What a bunch looks like on each frame in `title-1`, from the frame it
appears until it is taken. It spins: it shows `bunch-1` to `bunch-6` and
then back down, so one cycle is

    bunch-1, bunch-2, bunch-3, bunch-4, bunch-5, bunch-6,
    bunch-5, bunch-4, bunch-3, bunch-2

and then `bunch-1` again. Each picture is shown on 6 counted frames
(below), so a cycle lasts 60 counted frames. Each bunch keeps its own
cycle, counted from the frame it appears, so bunches are not in step
with each other.

The pictures are named by their picture hashes
(`../formats/sprites.md`, "Picture hashes"):

| Picture | Picture hash (SHA-256) |
|---|---|
| `bunch-1` | `dd937f5032cea06c1ee2ad953e0663cbc33ed594117d07dcc2240f2f272aef22` |
| `bunch-2` | `38d6262e7e94129ac3f0bc04df4b036977c5594ebc7e925bd2fcf0e6f6f2d51c` |
| `bunch-3` | `5e5bdee15de22effc4928e82d070952603b2b85101cfb203576740c460178243` |
| `bunch-4` | `d4e3d2b554b198bffb1d92c31201fed216e154f2044daba5a1ccbdb41f81eb9a` |
| `bunch-5` | `b8202d2431494a964555dbed5a73658e4b4fa68f3a72be118235395edd54acb4` |
| `bunch-6` | `7391473503d443cd367f8b3dccb96fa284b39123483e84cbd1f1085a24470100` |

Each picture is drawn unmirrored, with its origin at (`xb`, `yb`), as
`../formats/sprites.md` places an origin ("Where the picture is
drawn"). The picture given for a frame is the one on screen with that
frame's view.

**When a hash isn't found.** The six hashes belong together: if any of
them isn't found, the engine shows its own art for a bunch, on the same
frames and with the same timing as given here.

### Appearing

A bunch is not in the level all the time. It appears as a walker does
(`walker-view.md`, "The window" and "Appearing"), with its start `S`
being the entry's level x, `xb` + 1: a bunch that is not in the level
and hasn't been taken appears on the first frame whose start-of-frame
view `V` gives

    −31 ≤ S − V ≤ 288

That is always outside the picture, so a bunch is never seen on the
frame it appears. It also appears on frame 0 after a restart
(`restart-barrel.md`) when its `S` is in the window then. When a bunch
is removed again is not covered (see "Not covered"); one that has been
removed appears again by the same rule, and its cycle starts afresh.

### Counted frames

Call `A` the frame the bunch appears. Its pictures step on **counted
frames**: every frame from `A` on except the frames on which nothing
moves, as for walkers (`walker.md`, "Counted frames"):

- B + 1 and B + 2 of a bounce (`bounce.md`, "Everything else on the
  frozen frames");
- K + 1 and K + 2 after a walker is defeated by a roll
  (`rolling-into-walkers.md`, "The pause") or by the ground action
  (`ground-action-enemies.md`);
- H + 1 to H + 10 while the first character is knocked out
  (`knock-out.md`), H + 1 to H + 13 for the second character
  (`knock-out.md`, "The second character").

On frames that don't count the bunch keeps the picture it had. Pauses
(`extra-lives.md`, "While the game is paused", and the pause in a
knock-out) have no frames, so they don't change anything either.

### Which picture

Let `c` be the number of counted frames since `A`, not counting `A`
itself (so `c` = 0 on `A`). On a frame with count `c` the bunch shows
entry

    k = ⌊(c + 1) / 6⌋ mod 10

of the cycle above, counting its entries from 0 (`bunch-1`) to 9 (the
second `bunch-2`). So `bunch-1` is shown on `A` and the 4 frames after it
(5 frames), and from then on each picture lasts 6 counted frames.

### Once taken

From the frame `P` on which it is taken ("When a bunch is taken"), a
bunch is not drawn. Only its items are drawn as they are released
("Releasing the items"; how they look is not covered).

### Example (synthetic)

A bunch appears on frame 100 and the player bounces on a walker on
frame 120 (`B` = 120), with nothing else frozen:

| Frames | `c` | Picture |
|---|---|---|
| 100 to 104 | 0 to 4 | `bunch-1` |
| 105 to 110 | 5 to 10 | `bunch-2` |
| 111 to 116 | 11 to 16 | `bunch-3` |
| 117 to 124 | 17 to 22 | `bunch-4` (121 and 122 don't count) |
| 125 to 130 | 23 to 28 | `bunch-5` |
| 131 to 136 | 29 to 34 | `bunch-6` |
| 137 to 142 | 35 to 40 | `bunch-5` |
| 143 to 148 | 41 to 46 | `bunch-4` |
| 149 to 154 | 47 to 52 | `bunch-3` |
| 155 to 160 | 53 to 58 | `bunch-2` |
| 161 to 166 | 59 to 64 | `bunch-1` |

If it is taken on frame 140, it shows `bunch-5` up to frame 139 and
nothing from frame 140 on.

### Evidence for the pictures

Measured on screen in the first level, by inputs alone, with only the
sprites drawn: 40 runs, 102 544 frames. 24 runs played the level's 8
recorded routes from its start, each as recorded and twice with Jump and
Action presses added at random. 10 runs played the first part of one
route and then walked and ran back and forth at random over about
1 000 px with two bunches, so that they left the view on both sides and
came back, with jumps, rolls, crouches, bounces on walkers, roll
defeats, knock-outs and restarts. 6 runs did the same next to the bunch
over the pit. The bunches appeared 134 times by the rule above (127 on
the right, 7 on the left, none otherwise), and 6 more times on a
restart's frame 0. On every frame each bunch's picture on screen was
compared with the rule.

| | Frames |
|---|---|
| Compared, as the rule gives | 5 797 |
| Compared, another picture or none | 0 |
| Taken: compared, no bunch picture drawn | 6 |
| Left out: partly off the edge of the picture | 2 270 |
| Left out: another sprite drawn over part of it | 1 454 |
| Left out: the lives display shown over it during a knock-out | 16 |
| Left out: last frame before a restart, nothing drawn | 5 |

Bunches were seen whole and as the rule gives after frames that don't
count in 2 stays with a bounce, 11 with a roll defeat, 11 with the first
character's knock-out and 1 with the second character's. Bunches that
were already in the level when a run started part-way through the level
were left out. On the last frame before a restart nothing was drawn
where the bunch was (as for single collectibles, `collectibles.md`,
"Evidence for the pictures").

## Not covered

- The second character's landings and running state, and its ranges
  in the states listed in the next item.
- The ranges while being carried down a steep slope or a rounded ledge
  (`steep-faces.md`), while passing through a platform
  (`platforms-from-below.md`), while carrying an object on a slope or
  after picking it up in the air (`carrying.md`), and while being hit
  with the other character there (`losing-a-character.md`);
  the stop after a roll on a slope; walking off a ledge with a steep
  face.
- Landing on slopes, and a landing followed at once by a crouch, a
  roll, a turn or another jump.
- Bounces from a roll, bouncing again before landing, landing on a
  slope after a bounce, and the second character bouncing.
- When a bunch that hasn't been taken is removed as the view moves.
  In the runs for "Pictures", some bunches were gone on the first frame
  their `S` was outside the window, others only once `S` − `V` was
  −128 or less or 385 or more, as for a walker (`walker-view.md`,
  "Being removed").
- Whether a bunch's pictures wait while a character joins
  (`holder-breaking.md`, "Everything else waits"): no bunch was seen
  whole on screen during or after one.
- Which is in front where a flying copy and the count display's image overlap, and the copies in areas other than the first level's.
- How bunches are placed in a level's data.
