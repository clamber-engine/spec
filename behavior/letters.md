# Letters

Titles: `title-1`

The first level holds four **letters**, numbered 1 to 4 by their place
in the word they spell. The player takes a letter by touching it. Each
letter taken is shown in a **letter row** at the top of the picture,
and taking all four gives one more life. This file covers when a letter is
taken, the letters taken so far, the reward, when the letter row is
shown, and what it shows after the fourth letter.

Frames are logic frames at 60 Hz, numbered as in a trace. `x` grows to
the right. Horizontal positions are level positions as in `view.md`.
Vertical positions are **level y** as in `view-vertical.md`: they grow
downward, measured from the top of the level. The player's **feet line**
`F` is as in `view-vertical.md`. Positions in the picture are in px from
its top-left corner (the picture is 256 × 224 px, `view.md`,
`view-vertical.md`).

Measured on the first level with the first character leading and
alone (two characters: see "Two characters" under "Pickup ranges"). The player was placed at chosen offsets from two of the level's
letters, one pixel apart, in poses reached by inputs only: standing,
walking, running, crouching, rolling, the Down + Action ground action,
and frames of jumps from take-off until well after the top (Jump held
or released early, facing right and left). The letters taken, the
reward and the letter row were measured on all four letters.
Separately, play by inputs alone was checked against the rules below:
about 108 000 frames from one route through the level and 160
variations of it with jumps, rolls and crouches added at random, and
about 112 000 frames from 320 short runs of random inputs starting near
one letter. That play included 223 pickups of three different letters
in the states covered here (standing, walking, running, rolling, and
jumps both before and after `T` + 6, facing both ways). Each came on the
frame the rules give, and no letter was taken on a frame the rules
don't give. Another 56 pickups happened in states listed under "Not
covered" and weren't checked.

## A letter's position

A letter does not move. Its **position** is a point (`xl`, `yl`) in the
level. A level's placement list (`../formats/placements.md`) places each
letter with an entry; `xl` is 1 px left of the entry's level x and `yl`
is the entry's level y (see "Positions" there). How to tell that an
entry places a letter, and which of the four it is, is not covered. How
a letter is drawn is in "Pictures".

## Pictures

What a letter looks like in `title-1`, from the frame it appears until
it is taken. Letters appear and are removed by the window, as in
`../formats/placements.md` ("When placed objects appear" and the
removal table there).

### The letter

A letter shows one picture on every frame it is there: letter 1 (the
`letter-1` reference in `../formats/placements.md`) shows
`letter-pic-1`, letter 2 shows `letter-pic-2`, and so on. The picture
doesn't change while the letter is there.

| Picture | Picture hash (SHA-256) |
|---|---|
| `letter-pic-1` | `97f1cd7b0d69aeb8afe3674d1ab1ec2e2e1455a2bf576166a0134fbbb77271dd` |
| `letter-pic-2` | `e0c16aa33da949afd12713fcb32f3c4a754ec187d7ab7cbd2aa77332f7344835` |
| `letter-pic-3` | `2b72a3136aa47070e7cf268dd333209e491921482c5f17836d70b1fead420fa4` |
| `letter-pic-4` | `fb23fa5c51c85954880b6477f55eb6a3c192b3c0d32bd1c8c69281e4f9b6a089` |

The hashes are as in `../formats/sprites.md` ("Picture hashes"). Each
picture is drawn unmirrored, with its origin at the letter's position
(`xl`, `yl`), as `../formats/sprites.md` places an origin ("Where the
picture is drawn"). The picture given for a frame is the one on screen
with that frame's view.

From the frame `P` on which it is taken, the letter isn't drawn; the
copy that flies to the letter row is drawn from the picture made on `P`
("The letter row", "The copy").

**When a hash isn't found.** The four hashes belong together: if any of
them isn't found, the engine shows its own art for all four letters, on
the same frames and at the same places.

### Sparkles

While a letter is there, small **sparkles** come and go around it. A
sparkle is drawn by itself, not as part of the letter's picture.

**Counting the frames.** Frames are numbered from the latest frame 0 as
in `collectibles.md` ("Counting the frames"): from frame 0 of the level,
and from 0 again when play starts again after a knock-out or a fall
out; pauses add no frames.

**When a sparkle comes.** A **sparkle frame** is a frame whose number
`m` has `m` mod 16 = 14 (frames 14, 30, 46, …), unless it is a frame on
which nothing moves:

- B + 1 and B + 2 of a bounce (`bounce.md`, "Everything else on the
  frozen frames");
- K + 1 and K + 2 after a walker is defeated by a roll
  (`rolling-into-walkers.md`, "The pause") or by the ground action
  (`ground-action-enemies.md`);
- H + 1 to H + 10 while the first character is knocked out
  (`knock-out.md`), H + 1 to H + 13 for the second character
  (`knock-out.md`, "The second character");
- the 33 frames while a freed character joins (`holder-breaking.md`,
  "Everything else waits").

On a frame on which nothing moves no sparkle comes; the next one comes
on the next sparkle frame, 16 frames on.

On each sparkle frame, every letter that is there at the end of the
frame gets one new sparkle. Its **point** is

    (xl + a, yl + b)

where `a` is a whole number from −16 to 15 and `b` a whole number from
−21 to 10, both chosen at random for each sparkle. So sparkles come
anywhere in a 32 × 32 px square around the letter's position, reaching
further above it than below. The numbers don't come equally often: in
the runs below every value of `a` but 8 and every value of `b` but −14
came, some more than 50 times as often as others. Which numbers come is not
covered.

**A sparkle's pictures.** Call `N` the frame a sparkle comes, and let
`c` be the number of counted frames since `N`, not counting `N` itself
(so `c` = 0 on `N`). Counted frames are every frame except those on
which nothing moves (listed above). The sparkle shows step

    s = ⌊c / 5⌋ + 1

of 8 steps, so each step lasts 5 counted frames, and it is gone on the
frame with `c` = 40: it is drawn on `N` and the 39 counted frames after
it. It doesn't move.

A sparkle doesn't depend on its letter once it has come: it stays its
40 counted frames also when the letter is taken or removed in the
meantime, and the window doesn't remove it.

Since sparkle frames are 16 frames apart and a sparkle lasts 40 counted
frames, a letter that has been there for a while has two or three
sparkles at a time.

The 8 steps' pictures are not full frames of the frame table, so they
have no picture hash and are not named here. Each is centred on the
sparkle's point and fits in a square from 6 px left of and above the
point to 6 px right of and below it (13 × 13 px). The sparkle grows from
step 1 to its largest at step 4 and then shrinks. The engine shows its
own art for them, on the frames and at the points given here.

### Example (synthetic)

A letter appears on frame 100 and nothing freezes. Sparkle frames are
110, 126, 142, … (110 mod 16 = 14). The letter gets sparkles on frames
110, 126 and 142. The sparkle that comes on frame 110 shows step 1 on
frames 110 to 114, step 2 on 115 to 119, …, step 8 on 145 to 149, and
is gone from frame 150. If the player bounces with `B` = 120, frames 121
and 122 don't count: that sparkle shows step 3 on frames 120 to 126 and
is gone from frame 152. Frame 126 is still a sparkle frame, so the next
sparkle comes on 126 as before.

If the letter is taken on frame 130, it isn't drawn from 130 on, and
gets no sparkle on frame 142; the sparkles that came on 110 and 126 go
on until they end.

### Evidence for the pictures

Measured on screen in the first level, by inputs alone, with only the
sprites drawn: 44 runs, 101 573 frames. 32 runs played the level's 8
recorded routes from its start, as recorded and with Jump and Action
presses added at random. 12 runs played the first part of a route and
then walked, ran, jumped, rolled and crouched back and forth at random
around the first or the fourth letter, so that it left the view on both
sides and came back. Half the runs had pauses (72 in all); there were
65 restarts after knock-outs and falls, and bounces, roll defeats,
knock-outs of both characters and joins while letters were there.

- **Letters.** Letters appeared 115 times and were removed 56 times as
  in `../formats/placements.md`, and were taken 34 times. On 3 220
  frames a letter was seen whole with only sparkles near it, and each
  time it showed its picture as above (0 different). Left out: frames
  with the letter partly or wholly off the edge of the picture, with a
  character or another object near it, or with a sparkle over it; the
  frame before a restart, when nothing is drawn. On `P` the letter's
  picture was never seen at its place.
- **Sparkles.** 1 218 sparkle frames with a letter there: on 1 203 each
  letter there got one sparkle, at the place the rule gives (with `a`
  and `b` in the ranges above); on the other 15, see "Not covered". No
  sparkle came at a letter on any other frame, including 130 frames
  with `m` mod 16 = 14 on which nothing moved. 1 135 sparkles were seen
  from `N` to the end, and on each of 46 752 frames a sparkle showed
  the step the rule gives and was gone at `c` = 40 (0 different); 42
  more ended at a restart. Their size on screen was measured on frames
  with nothing else near them.


## When a letter is taken

A letter is taken on frame `n` when both of these hold **at the end of
frame `n`**, after the player has moved on that frame:

1. it hasn't been taken already;
2. the player's offsets from it (below) are inside the ranges for the
   player's state on frame `n` (see "Pickup ranges").

So a letter is taken on the first frame that ends with the player in
range. Call this frame `P`. The offsets are those of bunches
(`bunches.md`), with the letter's position in place of the bunch's; the
ranges are below.

From `P` on, the letter is no longer in the level. It never comes back
during the visit to the level: in one case the player was moved about
500 px past a taken letter's place and back, and it did not come back
(an untaken letter did, in the same case). After a knock-out it is there
again only when play resumes at the level's start (`restart-barrel.md`,
"What is there again after a knock-out"); after an exit it stays taken
(`exits.md`, "Coming back to the level"). After finishing the level, not
covered.

### Offsets

Using whole pixels, let

- `X` be the player's `x` rounded down;
- **`I`** be how far the player is past the letter in the direction the
  player is facing: `X − xl` when facing right, `xl − X` when facing
  left;
- **`D`** = `F − yl`, how many px the feet line is below the letter's
  position (negative when it is above).

Because `I` is measured in the facing direction, each range below holds
for both facings. For example, a standing player facing right takes a
letter with `X − xl` from −15 to 21. Facing left, it takes one with
`X − xl` from −21 to 15.

## Pickup ranges

A letter is in range when `I` and `D` are both within the ranges for
the player's state (ends included). For the states that `bunches.md`
covers, they are the ranges of bunches, including the Down + Action
ground action (`ground-action.md`), which has the standing ranges.

| State | `I` | `D` |
|---|---|---|
| Standing, walking, running | −15 to 21 | −8 to 40 |
| Turning on the ground (see below) | −15 to 21 | −8 to 40 |
| Down + Action ground action | −15 to 21 | −8 to 40 |
| Crouching (Down held on the ground) | −18 to 12 | −12 to 31 |
| Rolling | −13 to 21 | −4 to 38 |
| Jumping, from the take-off frame `T` to `T` + 5 | −15 to 21 | −8 to 40 |
| Jumping, from `T` + 6 on | −15 to 18 | 13 to 57 |

`T` is the frame carrying `jump_start` (`jump-arc.md`). For the first six
frames of a jump, starting with `T`, the ranges are the standing ones.
From `T` + 6 until the jump lands, the last row applies on every frame,
whatever the vertical speed, and whether Jump is still held or not.

**Falling without a jump.** When the player walks or runs off a ledge
(`walk-off.md`), the standing row applies on every frame in the air,
from the first one to the landing, on the landing frame and on the
frames after it, whether the player then stands still, walks on or runs
on. Unlike a jump, the fall never switches to the last row. `I` is
measured in the direction the player faces on that frame, also when the
player turns in the air. This was measured at a ledge without a face,
with a floor 176 px below it, with the letter moved to every offset one
pixel apart around the player on every frame from the last frame on the
ledge to 6 frames after the landing of five walk-offs: walking right
with the direction held and with it released in the air, running right,
walking left and releasing the direction, and walking right then
turning left in the air (about 960 000 placements). Separately, in
21 464 runs of play by inputs with walk-offs from both ends of the
ledge, random changes of direction, Action, Down and Jump, and the
letter placed once near the edge of the standing row, 11 187 pickups
came in the air during such falls (634 of them while turning) and 704 on
the landing frame or the 6 frames after it, and every run took the
letter on the frame these rules give. Using the last row from the sixth
frame in the air to 3 frames after the landing, as for a jump, would
have given the wrong frame in 9 740 of them. Walking off a ledge with a
steep face was not measured.

For example, a player facing right walking on a floor 33 px below a
letter's position (`D` = 33) takes it on the first frame that ends with
`X − xl` from −15 to 21. Walking right from far left, that is the first
frame that ends with `X` = `xl` − 15 or more.

**Turning.** During a turn on the ground (`ground-speed.md`, "Facing and
turns"), from its first frame `U` to `U` + 7, the standing ranges apply
on every frame, as for bunches (`bunches.md`, "Turning"). `I` is
measured in the direction the player faces on that frame: the old
facing from `U` to `U` + 3 and the new facing from `U` + 4. This holds
for turns from standing, walking and running, facing either way, and
for a new turn that starts on `U` + 8, whose first four frames again use
the facing it starts in. On the two frames after a turn that no new
turn follows, the standing ranges apply in the new facing too (the
player was then standing, walking or running). This was measured with
the letter moved to every offset one pixel apart around the player, on
every frame of seven turns and the frames just before and after them
(about 373 000 placements). Separately, in 17 342 runs of play by inputs
with turns, the letter placed once near the edge of the standing ranges,
5 931 pickups came on turn frames, and every run took the letter on the
frame these rules give. Measuring `I` in the new facing on every frame
of a turn would have given the wrong frame in 3 118 of them.

**Landing.** The last row applies on every frame from `T` + 6 that ends
with the player in the airborne pose (`ball-contact.md`, "Which pose the
player has"), and that pose goes on for some frames after the jump lands
on frame `L` (the frame carrying `land`). From the first frame that ends
with the player upright again, the standing ranges apply. Measured on
flat ground:

| Landing | Jumping ranges up to | Standing ranges from |
|---|---|---|
| Jumps in place with Jump held 3, 10, 14 or 30 frames, also turning left in the air, or with Action and a direction pressed from `L` + 2; a jump while walking left; all onto the floor they started from | `L` + 3 | `L` + 4 |
| A jump while walking right, off a ledge onto a floor 176 px lower | `L` + 4 | `L` + 5 |
| Action and a direction held through the landing, so that the player runs on, onto the floor the jump started from | `L` | `L` + 1 |
| The same, off the ledge onto the floor 176 px lower | `L` + 1 | `L` + 2 |

In every case the switch came on the frame the airborne pose ended.
This was measured with the letter moved to every offset one pixel apart
around the player on every frame from `L` − 1 to `L` + 10 of ten
landings (about 540 000 placements); every frame had exactly the
jumping or the standing ranges. Separately, in 12 842 runs of play by
inputs with a jump (random inputs in the air and after the landing),
with the letter placed once beforehand near the edge of the ranges,
2 459 pickups came on `L` to `L` + 10, 975 of them on the ground in the
airborne pose, and every run took the letter on the frame these rules
give. Using the standing ranges from `L` would have given the wrong
frame in 1 304 of them; from `L` + 4 whatever the pose, in 429; from
`M` + 3, with `M` the switch frame of `jump-input.md` (from `M` when
running starts on `M`), in 308. When
the airborne pose ends after other landings is not covered
(`ball-contact.md`).

**After a roll.** Let `R` be the first frame after the roll's 29
frames, the frame `roll-input.md` calls D ("How a roll ends"). When the
roll ends in a stop (`roll_end` on `R`), the rolling ranges still apply
from `R` to `R` + 8, and the standing ranges apply from `R` + 9 to the
end of the stop. When a roll carries on into running (the player still
in `roll` on `R` to `R` + 2, `roll_end` on `R` + 3), the rolling ranges
apply on `R`, `R` + 1 and `R` + 2, and the standing ranges from `R` + 3.
`I` is measured in the direction the player faces on that frame, also
when the player turns during the stop. This was measured on
flat ground, after slow rolls to the left and to the right and a roll to
the left started from walking, with the letter moved to every offset one
pixel apart around the player on every frame from `R` − 1 to `R` + 17 of
four stops (one of them turning on `R`, another on `R` + 8) and from
`R` − 1 to `R` + 4 of a roll that carried on (about 430 000
placements). Separately, in 10 815 runs of play by inputs with a roll,
random presses during the roll and the stop, and the letter placed once
beforehand near the edge of the standing ranges, 1 766 pickups came on
stop frames and 111 on the three frames of a roll carrying on, and every
run took the letter on the frame these rules give. Using the rolling
ranges on every frame of the stop would have given the wrong frame in
1 437 of them; switching to the standing ranges on `R` + 8 or `R` + 10
instead of `R` + 9, in 1 425 and 1 418. Stops after a fast roll
(`roll-speed.md`) and stops on slopes were not measured.

### Two characters

With two characters there (`second-character.md`), only the character
that leads takes a letter, by its own offsets. The character that
follows never takes one, even with the letter inside its own ranges and
outside the lead's. The ranges above are the first character's when it
leads, with or without the second character following. With the second
character leading, its ranges are those of bunches for the second
character (`bunches.md`, "Two characters"), with one row for its jumps
from take-off until they land.
This was measured with letters and bunches alike, with each character
leading (`bunches.md`, "Two characters", has the measurements).

**Landing, second character leading.** After a jump that lands on frame
`L` on the floor it started from, a letter is taken by the jumping row
(the walking row) on `L` and on every frame to `L` + 16, and by the
standing row from `L` + 17, as bunches are (`bunches.md`, "Landing,
second character leading"). A letter at `I` = −18, for example, is
taken on `L` + 16 and not on `L` + 17. This was observed with the
letter at every offset from `I` −24 to 24 and `D` −12 to 44 around the
second character (a setup method was used), on each frame from 3 before
`L` to 21 after it, after a jump with Jump let go after 10 frames,
facing right: 25 grids, each exactly the rectangle of its row. It was
not checked in ordinary play, so the rule is stated only for that jump.
Other jump lengths, facing left, moving landings, lower floors and
slopes were not measured for letters.

## The letters taken

The player has a set of **letters taken**, which starts empty. Letters
can be taken in any order.

A letter taken on frame `P` joins the set a little later, after its
copy has flown to the letter row (see "The letter row"). In the two
cases where both were measured, it joined 2 frames after the copy
reached its place. Over all cases measured it joined on `P` + 30 (two
cases), `P` + 31 or `P` + 33, and in the runs of "Another letter while
the row is shown" (letters drawn in other places in the picture) from
`P` + 6 to `P` + 35. The copy's exact path is not covered, so neither is
the exact frame.

## All four letters

On the frame the fourth letter joins the set, the player gets **one
more life**. The order the letters were taken in doesn't matter (each of
the four letters was measured as the last one taken).

After that, the letter row shows the **ending**: the four letters blink,
then move one at a time to the right, to where the number of lives is
shown, and the number of lives shown goes up by one. See "The ending"
below; the life itself counts from the frame the fourth letter joins the
set. The set of letters taken is empty again from then on.

After a knock-out the set is emptied when play resumes at the level's
start and kept when it resumes at the restart point (`restart-barrel.md`,
"What is there again after a knock-out"); an exit keeps it (`exits.md`,
"Coming back to the level"). Whether it survives finishing the level or
entering another level is not covered.

## The letter row

The letter row has four **slots**, one per letter. Slot `k` (for letter
`k`) shows its letter as a 16 × 16 px image whose top-left pixel is at
(84 + 24 × (`k` − 1), 12) in the picture: x 84, 108, 132 and 156. A slot
whose letter isn't in the set is empty. The row is not shown during
ordinary play.

Positions in this section are in the picture **made on frame `n`**: the
one drawn from the state at the end of frame `n`, shown once frame `n`
is over. A row "at y `t`" has the top row of its images at y `t`; parts
above the picture's top edge aren't drawn.

When a letter is taken on frame `P` (for the fourth letter, only up to
the frame it joins the set; see "The ending"):

1. **The copy.** From the picture made on `P` on, a copy of the letter
   is drawn starting about where the letter was and moving toward its
   slot, slowing as it gets near: each frame it covers roughly an eighth
   of the distance left. It reached its slot on `P` + 28 or `P` + 29 in
   the cases measured above (sooner when the letter was drawn nearer
   its slot), and stays there, also while the rest of the row is still
   sliding in.
2. **Sliding in.** The rest of the row (the letters already in the set)
   slides in from the top, 4 px per frame: at y −12 on `P` + 2, −8 on
   `P` + 3, and so on to 16 on `P` + 9, then at y 12 from `P` + 10 on.
3. **Shown.** The row, with the new letter in its slot, stays at y 12
   up to `P` + 121.
4. **Sliding out.** It moves up 4 px per frame: y 8 on `P` + 122, 4 on
   `P` + 123, and so on to −12 on `P` + 127. It isn't drawn from
   `P` + 128 on.

| Frame | Row at y |
|---|---|
| `P`, `P` + 1 | not drawn |
| `P` + 2 to `P` + 9 | −12, −8, −4, 0, 4, 8, 12, 16 |
| `P` + 10 to `P` + 121 | 12 |
| `P` + 122 to `P` + 127 | 8, 4, 0, −4, −8, −12 |
| `P` + 128 on | not drawn |

For example, with letters 1 and 2 already taken, take letter 3 on frame
100. Letters 1 and 2 are drawn with their top-left pixels at (84, 12)
and (108, 12) from frame 110. Letter 3's copy reaches (132, 12) around
frame 128. All three stay there until frame 221, and nothing of the row
is drawn from frame 228.

### Another letter while the row is shown

Take a letter on frame `P1` and another one on `P2` = `P1` + `d`, with
`d` from 1 to 127, while the row for the first is still drawn. It may
come before or after the first one in the word: the order makes no
difference to anything in this section, and each letter in the set is
drawn in its own place in the row. When the second letter is the fourth
(`d` from 2), what follows holds up to the frame it joins the set; from
then on see "The ending" ("After another letter"). The row's
**end** is its last frame at y 12: `P1` + 121 after the first letter.
What happens depends on whether `P2` comes before the end:

- **`P2` before the end** (`d` from 1 to 120). The row goes on as it
  was, without sliding in again: still sliding in when `d` is 9 or
  less, then at y 12. Its end becomes `P2` + 121: it stays at y 12 up to
  `P2` + 121, slides out on `P2` + 122 to `P2` + 127 (y 8, 4, 0, −4,
  −8, −12) and isn't drawn from `P2` + 128 on.
- **`P2` on the end or later** (`d` from 121 to 127). The first row
  goes on as if the second letter hadn't been taken: it slides out on
  `P1` + 122 to `P1` + 127 and isn't drawn from `P1` + 128. The second
  letter brings in a **second row**, which follows the table above
  from `P2` (not drawn on `P2` and `P2` + 1, y −12 on `P2` + 2, and so
  on). Until the first is gone, both are drawn, each showing the
  letters in the set; where they overlap, the picture shows both. From
  `d` = 128 on only the second row is left, as for a single letter.

Each letter's copy (step 1) flies to its slot and joins the set as for a
single letter, whatever the row does.

For example, with letter 4 already taken, take letter 1 on frame 100.
The row (letter 4) slides in from frame 102 and is at y 12 from 110, and
letter 1 joins the set around frame 130. Taking letter 2 on frame 150,
letters 1, 4 and then 2 stay at y 12 up to frame 271, slide out on 272 to
277, and nothing of the row is drawn from 278. Taking letter 2 on frame
224 instead, the first row (letters 1 and 4) is at y 8, 4, 0, −4, −8 and
−12 on frames 222 to 227, and the second row (letters 1 and 4, joined by
letter 2 when its copy arrives) is at −12, −8, −4, 0 on 226 to 229, at
12 from 234 to 345, and is not drawn from 352.

A third letter (again not the fourth) taken while a row is still drawn
follows the same rules, with the last row brought in as the first row
and the frame its end was last moved to as its end. Taken before that
end, it moves the end to its own frame + 121 and the row goes on as it
was; taken on the end or later (up to the end + 6), it brings in a row
of its own beside the one sliding out. While two rows are drawn, it is
the second one, the one brought in last, that it acts on: the first
slides out as it would have.

For example, with no letter taken before, take letter 1 on frame 100,
letter 2 on 160 and letter 3 on 260: the row stays at y 12 up to frame
381, slides out on 382 to 387, and isn't drawn from 388. Taking letter
2 on frame 221 instead (the first row's end), and letter 3 on 226: the first row slides out on 222
to 227; the second row, brought in by letter 2, is at y 12 from 231, now
up to 347, slides out on 348 to 353, and isn't drawn from 354.

The first level's letters are 866 px or more apart, so there this needs
the player to get from one letter to the next in under 128 frames. In
the checks, the player and the view were moved next to the second letter
on `P1` + 1, and the player was then put on it on `P2`. Pairs of letters
next to each other in the word were used (1 then 2, 2 then 3, 3 then 4),
each with none or one of the other two letters already in the set, with
`d` from 2 to 140 and without the second pickup: 1 260 runs. In another
13 runs only the player was moved and the view followed by itself
(`d` from 140 to 200). On every frame of each run, to 199 frames after
the second pickup, where each letter in the set was drawn matched the
rules above: 345 631 frames, 0 different. A copy that reached its slot before the
row was at y 12 stayed at its slot (twice). The picture itself was
checked in one run with `d` = 123.

For `d` = 1 and for a second letter earlier in the word, the checks used
a copy of the image in which letter 2, 3 or 4 was placed 61 px right of
letter 1, both letters' places in the view at once. The player was put
on one of the two letters and then on the other: letter 1 first and the
other letter first, each with none or one of the other two letters
already in the set, with `d` from 1 to 140 and without the second
pickup: 2 538 runs. On all 685 260 frames, checked as above, where each
letter in the set was drawn matched the rules, 0 different. Under a rule
in which a letter taken before the end brings in its own row instead,
2 160 of the 2 538 runs came out different.

For a third letter, the copy of the image also had another letter
placed 81 px below letter 1, on the floor there, so that three letters
(1 and two of 2, 3 and 4) were in the view at once. The player was put
on the three letters in each of the six orders, with none of the
letters in the set before, `d` from the first pickup to the second in
13 steps from 1 to 140 and from the second to the third in 22 steps
from 1 to 140, around the ends above: 5 148 runs, in 4 446 of them the
third letter taken while a row was drawn. On all 1 674 342 frames,
checked as above, where each letter in the set was drawn matched the
rules, 0 different. Under a rule in which every later letter brings in
its own row, 4 770 runs came out different, and under one in which only
the second letter can move the end, 3 510.

## The ending

`J` is the frame the fourth letter joins the set (the copy of the letter
taken last has reached its slot; "The letters taken"). Up to `J` the
row is as in "The letter row" for the fourth letter too: on `J` all four
letters are in their slots at y 12. From `J` + 1 on, the row doesn't
slide out; this section gives what it shows instead.

### The four letters

The letter taken last is **one frame late**: everything below happens
to it one frame later than to the other three. (When the fourth letter
is taken while the row is shown for another one, which letters are late
can differ: see "After another letter".) For letter `k`, let
`n` = `f` − `J` on frame `f` (`f` − `J` − 1 for a late letter), and let
its **slot place** be (84 + 24 × (`k` − 1), 12), its slot in the row.

| `n` | Letter `k` is |
|---|---|
| 0 | at its slot place |
| 1 to 8, 17 to 24, 33 to 40, 49 to 56 | not drawn |
| 9 to 16, 25 to 32, 41 to 48 | at its slot place |
| 57 to `M` − 1 | at its slot place |
| from `M` = 67 + 24 × (`k` − 1) | moving right, then gone |

So the not-late letters blink: off for 8 frames, on for 8, four times,
and then stay on.

**Moving.** The letter's horizontal position `x` starts at its slot
place's x (84, 108, 132 or 156). On each frame from `M` on, `x` moves
right by an eighth of the distance left to 192, but by at least ½ px;
if less than ½ px would then be left, `x` is 192 instead. The letter
is drawn with its top-left pixel at (`x` rounded down, 12). On the
frame after the one on which `x` is 192, it isn't drawn any more.

A not-late letter starts moving on `J` + 67, `J` + 91, `J` + 115 or
`J` + 139 (letters 1 to 4), is at 192 on `J` + 98, `J` + 120, `J` + 142
or `J` + 162, and is not drawn from the next frame. Letter 1, for
example, is drawn at x 97, 109, 119, 128, 136, 143, 149, 154, 159, 163,
167, 170, 172, 175, 177, 179, 180, 182, 183, 184, 185, 186, 186, 187,
188, 188, 189, 189, 190, 190, 191 and 192 on `J` + 67 to `J` + 98.

### The lives display

`G` is the first frame on which letter 4 is gone: `J` + 163, or `J` +
164 when letter 4 was taken last.

Extra-life items taken around the ending, while this lives display or
an item's is shown, are in `extra-lives.md`, "The four-letter ending".

The **lives display** shows the number of lives near the top right,
with the same digits as the count display (`count-display.md`, "What the
count display shows"): one digit (1 to 9) at x 220, two digits (10 to
99) at x 215 (tens) and x 225 (units). An image is drawn to its left;
that image and how it moves are not covered. It shows the number of
lives before the new life up to `G` − 1, and the new number from `G` on.

| Frame | Lives number at y |
|---|---|
| `J` to `J` + 6 | not drawn |
| `J` + 7 to `J` + 12 | −12, −8, −4, 0, 4, 8 |
| `J` + 13 to `G` + 121 | 10 |
| `G` + 122 to `G` + 127 | 6, 2, −2, −6, −10, −14 |
| `G` + 128 on | not drawn |

With 99 or more lives before the new life, the number shown is 99 also
from `G` on (`extra-lives.md`, "More than 99 lives").

### After another letter

When the fourth letter is taken on `P2` while the row is still shown for
another letter taken on `P1` (as in "Another letter while the row is
shown", `d` = `P2` − `P1` from 2 to 127), the row follows that section up
to `J`, and the ending is as above from `J` on, with one difference:
which letters are **late**. Each letter is either on time or late (one
frame late in everything above, as the letter taken last is after a
single letter), but the late ones are not always just the letter taken
last. With `d` from 121 to 140 (the largest measured) they were. With
`d` up to 120, the late
ones were sometimes all four letters, sometimes only the letter taken on
`P1`, and sometimes only the letter taken last:

| Letters (`P1`, then `P2`) | Only the letter taken on `P1` | All four | Only the letter taken last |
|---|---|---|---|
| 1, then 2 | `d` 2 to 4 | `d` 5 and 6 | `d` 7 to 140 |
| 2, then 3 | — | `d` 2 to 120 | `d` 121 to 140 |
| 3, then 4 | `d` 2 | `d` 3 to 120 | `d` 121 to 140 |

What decides this for `d` up to 120 is not covered. `G` is still the
first frame on which letter 4 is gone: `J` + 163, or `J` + 164 when
letter 4 is late. The lives display follows its table from `J` whatever
is late, and the lives go up by one on `J`.

For example, with letters 1 and 2 already taken and 5 lives, take
letter 3 on frame 1 and letter 4 on frame 61 (`d` = 60). Letter 4 joins
the set on frame 89 (`J`), and all four letters are late: all four are
drawn on frame 90, none of them on 91 to 98, all four again from 99, and
letter 1 starts moving on frame 157. The lives display comes in with 5
at y −12 on frame 96, as for a single letter. Letter 4 is gone from
frame 253 (`G` = `J` + 164), and the display shows 6 from then on.

### Example

With letters 1, 2 and 4 already taken and 5 lives, the player takes
letter 3, which joins the set on frame 531 (`J`), and the player has 6 lives
from frame 531. Letter 3 is the late one. Letters 1, 2 and 4 are not
drawn on frames 532 to 539, 548 to 555, 564 to 571 and 580 to 587;
letter 3 on 533 to 540, 549 to 556, 565 to 572 and 581 to 588. The
lives display comes in from the top with 5 at y −12 on frame 538 and is
at y 10 from 544. Letter 1 moves from frame 598 and is gone from 630,
letter 2 moves from 622 and is gone from 652, letter 3 moves from 647
and is gone from 675, and letter 4 moves from 670 and is gone from 694
(`G`). The lives display shows 6 from frame 694, leaves at y 6 on frame
816 and is not drawn from 822.

### While the player is knocked out

This covers a knock-out (`knock-out.md`: hit with no other character)
whose hit frame `H` is from `J` − 11 on, before or during the ending.
`E` is the level's last frame (`knock-out.md`, "Lying still"), and `B`
is `E` − 122. In the runs `E` was always `H` + 225, so `B` was `H` + 103.

**The four letters** are drawn as in "The four letters" up to `E`, on
the knock-out's frozen frames too: the knock-out changes nothing there.
The new life is given on `J` also when `H` comes before `J`.

**Where the lives number is.** Give the number a position `p` on every
frame, also when it isn't drawn: it is drawn at y `p` when `p` is
greater than −16, and not drawn otherwise. Without a knock-out, `p` is:

| Frame | `p` |
|---|---|
| before `J` | −44 |
| `J` to `J` + 12 | −40 + 4 × (frame − `J`): −40, −36, …, 8 |
| `J` + 13 to `G` + 121 | 10 |
| `G` + 122 to `G` + 130 | 6, 2, −2, −6, −10, −14, −18, −22, −26 |
| from `G` + 131 | −44 |

which gives the table in "The lives display". With a knock-out, up to
`H` it is the same, and then:

| Frame | `p` |
|---|---|
| `H` + 1 to `H` + 10 | as on `H` |
| `H` + 11 to `B` + 2 | 4 more than on the frame before, but at most 10 |
| `B` + 3 | −44 |
| `B` + 4 to `E` | 4 more than on the frame before, but at most 10 |

So after `H` the number never leaves: from `H` + 11 it comes in from
wherever it was, turning round if it was leaving, and stays at y 10. It
isn't drawn on `B` + 3 to `B` + 10, comes in again at y −12 on `B` + 11
and is at y 10 from `B` + 17 to `E`.

**The number shown** starts as the number of lives before the new life
and changes twice: it goes down by one on `B` + 3, and up by one on
`G`′ for the new life, where `G`′ is:

- `H` + 11 when `G` is `H` + 1 to `H` + 10;
- `B` + 28 when `G` is `B` + 4 to `B` + 17;
- `G` otherwise.

When `G`′ is `B` + 3 the two changes cancel. So the number shown is the
number of lives before the new life before the earlier of `B` + 3 and
`G`′ and again from the later of them on, for every `H`; between them
it is one more (`G`′ to `B` + 2, when `G`′ comes first) or one less
(`B` + 3 to `G`′ − 1, when `G`′ comes later). The digits are placed as
in "The lives display".

**Example.** As in "Example" above (5 lives before the new life, `J` =
531, `G` = 694), with the player hit on frame 560 (`H`): `E` = 785 and
`B` = 663. The lives number is at y 10 from 544 with 5, as without the
hit, and stays there up to 665. It isn't drawn on 666 to 673, comes in
with 4 at y −12 on 674 and is at y 10 from 680. On 694 (`G`) it becomes
5, which it shows up to 785.

Hit on frame 819 instead, while the number is leaving (y −6 on 819): it
stays at y −6 up to 829, is at −2, 2 and 6 on 830 to 832 and at y 10
from 833, showing 6. `E` = 1044 and `B` = 922: it isn't drawn on 925 to
932, comes in with 5 at y −12 on 933 and is at y 10 from 939 to 1044.

### Checks

Each of the four letters was taken last, at its place in the level,
with the other three already in the set; for each, with the view in
three places before it was taken (the letter joined the set 18 to 36
frames after it was taken), with no input or with Right, Left, Jump,
Right + Action or Down held from `J` on, and with 1, 5 or 8 lives
before: 216 runs. On every frame from `J` to `J` + 300, where each
letter was drawn, whether and where the lives number was drawn, the
frame its number changed, and the frame the lives went up were as
above: 50 583 frames, 0 different. In 78 of the runs the player was
hit and knocked out, or fell out of the level, 16 to 274 frames after
`J`; those runs were compared up to the frame before. The frames with
the lives number partly above the picture's top edge (y −12, −10 and
−14), the blinking, the late letter and the first frame of a move were
also checked in the picture itself in one run. 10 and 99 lives after
the new life (9 and 98 before) gave the two-digit places, and 99 before
gave 99 shown throughout.

For "After another letter", the fourth letter was taken after the one
before it in the word (1 then 2, 2 then 3, 3 then 4, the other two
letters already in the set), with `d` from 2 to 140, as in the checks of
"Another letter while the row is shown": 417 runs. On every frame from
`P1` to `J` + 300 (to `J` + 299 in 139 runs where the player was knocked
out on `J` + 300), where each letter and the lives number were drawn,
the frame the number changed and the frame the lives went up were as
above, with each letter's lateness taken from the run: 165 936 frames,
0 different, and in every run each letter was either on time or late
throughout.

**Knocked out.** Letter 1 was taken last, at its place, with the other
three already in the set and a walker put on the player on one frame,
so that the player was hit on the next one: every `H` from `J` − 11 to
`J` + 300, with no input or with Right held from the frame before `H`,
and with 1, 5 or 9 lives before (6 runs for each `H`, 1 878 knock-outs).
On every frame from `H` − 1 to `E` (426 306 frames), where each letter
was drawn was the same as in a run from the same place without the
hit, and whether and where the lives number was drawn, its digits and
the number of lives were as above: 0 different. With one life before,
the number shown went down to 0 and back to 1; with 9, it went up to 10
(two digits) before or after going down. Putting the walker on the
player while the player was still in the air after being placed at the
letter (`H` up to `J` − 12) gave no hit.

## Not covered

- With two characters: the second character's landings other than
  a rightward jump of 10 frames onto its own floor (other jump lengths,
  facing left, moving landings), its running state, and the letter row,
  the letters taken and the reward (only which character takes a
  letter, and on which frame, was measured).
- Being hit, bouncing, carrying, and the stop after a fast roll or on
  a slope; walking off a ledge with a steep face. Landing on slopes, and
  a landing followed at once by a crouch, roll, turn or another jump.
- Which letters are late in the ending when the fourth letter is taken
  120 frames or less after another (see "After another letter").
- Taking the fourth letter on the frame after another.
- The ending while the player leaves the level or finishes it, or while
  another lives display (`count-display.md`) is shown. A knock-out with
  `H` before `J` − 11 (between the pickup and the copy reaching its
  slot), with 99 lives, with a letter other than letter 1 taken last
  (only the rules in terms of `G` were checked, with `G` = `J` + 163), or
  with the fourth letter taken after another ("After another letter");
  losing a character (`losing-a-character.md`) during the ending; and
  how the image beside the lives number is drawn during a knock-out.
- In one setup, with the player rolling from the pickup on, the lives
  display came in 2 frames later than above (y −12 on `J` + 9); why is
  not covered.
- How the row's images are drawn; the copy's exact path.
- Sparkles ("Pictures"): on 15 of the 1 218 sparkle frames in the runs,
  a letter got no sparkle. On 8 of them many other short-lived objects
  were there (each time the items just released from a bunch, near the
  second letter); on the other 7 another object came on that same frame
  (such as on the first frame of a bounce, `B`). When this happens is
  not covered. Which sparkle is drawn in front when they
  overlap the letter or each other, and the sparkles' own pictures.
- Other levels, and whether every level has four letters.
