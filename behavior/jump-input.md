# Jump input timing

Titles: `title-1`, `title-2`

When a jump press turns into a jump. Frames are logic frames at 60 Hz,
numbered as in a trace. The **press frame** is the first frame the jump
input is held after a frame where it was not held. The **landing frame**
is the frame carrying the `land` event. Measured on flat ground with the
lead character.

The sections below up to "In `title-2`" give the `title-1` values.
`title-2` is different throughout; "In `title-2`" gives its complete
rules.

## On the ground

A press on frame P while grounded starts a jump on frame P + 1
(`jump_start` on P + 1). This includes every frame of a roll, standing or
moving: rolling never delays or blocks a jump.

What counts is being grounded at the end of frame P − 1. So where the
ground simply ends, a press on the first airborne frame of a walk-off
still starts a jump; at a ledge with a steep face it doesn't if the
player was carried down the face just before leaving it
(`walk-off.md`, "Jumping near the edge" and "Ledges without a face").
On a steep face in general, a press on a frame that starts on the face,
or whose next frame does, doesn't take off on P + 1; held, it can take
off later, once the player is on other floor (`steep-faces.md`,
"Jumping on or next to a face").

### Crouching

A press on any frame of a crouch (`ground-speed.md`, "Crouching"),
including its first frame D, the frames after Down is released early,
and U to U + 9, starts a jump on P + 1 as on any other grounded frame,
whether Down is held on P, on P + 1, or later.

One combination is different: **Action pressed on P together with the
Jump press, and Down held on P + 1**. Then there is no `jump_start` on
P + 1. Instead P + 1 is the first frame D of a crouch, with the usual
minimum length and target speeds of a crouch:

- if Jump is held on P + 2, P + 2 counts as a press on a crouch frame,
  and `jump_start` is on P + 3;
- otherwise no jump starts, and a new press is needed.

This doesn't depend on whether Down is held on P, on a crouch before P,
on how long Action is held, or on the speed (standing, walking or
running). Without Down on P + 1, or with Action pressed on P + 1
instead of P, the jump is an ordinary one (`air-control.md`, "Action and
Down around take-off and landing").

| Inputs on P, P + 1, P + 2 (grounded on P) | Result |
|---|---|
| Jump + Action, then Jump + Down, then Jump | `jump_start` on P + 3 |
| Jump + Action, then Jump + Down, then nothing | No jump; crouch from P + 1, at least to P + 13 |
| Jump + Action, then Down, then nothing | No jump; crouch from P + 1, at least to P + 13 |
| Jump + Action + Down, then Jump | `jump_start` on P + 1 |
| Jump + Down, then Jump + Action + Down, then Jump | `jump_start` on P + 1 |

Example: walking right at +2 with Right held throughout, Jump, Action
and Down pressed on frame 30, Action and Down released after frame 31,
Jump held on. `vx` is 544/256 on 30 and 572/256 on 31 (D: the target
with Action held), 501/256 on 32 (a press on a crouch frame keeps the
crouch's target 0), and 502/256 on 33, the `jump_start` frame (the air
target). The player is airborne from frame 34.

Measured from standing and walking with the crouch started 0 to 26
frames before P or not at all, Jump held 1 to 60 frames, Action pressed
on P or P + 1 and held 1 to 60 frames, Down on P, P + 1 and after
in every combination (4 264 cases), and in the random runs of
`air-control.md`, 1 676 such presses (1 482 with Jump held on P + 2):
every frame as described.

## Landing lockout

After landing, there is a short time when a jump can't start. Call the
earliest frame a jump can start after landing frame L **E**. For a
landing after a jump, E = **M + 2**, where M is the landing's **switch
frame** below. M is L or later (up to L + 6 in the measurements below),
so E is L + 2 or later. This holds for the first character's jumps out
of a roll too, whether pressed on the ground or in the air. The second
character's landings have their own rule (see "The second character"
below).

### The switch frame M

Let T be the frame carrying the jump's `jump_start` (`jump-arc.md`).

Let **A**, the **approach frame**, be the first frame from **T + 20** on
at whose end there is floor within reach below the player. In level y
(`view-vertical.md`), with the feet line F (the player's level y rounded
up to a whole px) and the floor `Y` of the column containing `x`: the
floor is within reach when it is at or below the feet and at most
**71 px** below them (0 ≤ `Y` − F ≤ 71), and its 32 px band is at most 2
more than the band of F, as in `view-vertical.md` ("The ground used"). A
player on the floor always has floor within reach, so A is never later
than the first frame from T + 20 on which the player is on the floor. A
doesn't depend on whether the player is still rising.

From A on there are seven **stage frames**:

- if A = T + 20: A, A + 2, A + 4, A + 6, A + 8, A + 11 and A + 15;
- if A is later than T + 20: A, A + 1, A + 3, A + 5, A + 7, A + 10 and
  A + 14.

Then:

- If L < A (the player landed before the approach frame): M = A + 4.
- Otherwise let **n** be the number of stage frames that are L or
  earlier (1 to 7):

  | n | M |
  |---|---|
  | 7 | L |
  | 6 | L + 1 |
  | 5 or 4 | L + 2 |
  | 3 or 2 | L + 3 |
  | 1 | L + 4 |

So a high jump that comes down onto lower or level ground usually has
M = L or L + 1, and a jump that lands on higher ground soon after its
top has a later M.

Examples: standing jumps on flat ground (the take-off floor is the
landing floor), Jump pressed on T − 1:

| Jump held | A | L | n | M | E |
|---|---|---|---|---|---|
| 1 frame | T + 20 | T + 31 | 6 | L + 1 | L + 3 |
| 10 frames | T + 20 | T + 36 | 7 | L | L + 2 |
| 14 frames | T + 24 | T + 38 | 7 | L | L + 2 |
| 20 frames | T + 29 | T + 41 | 6 | L + 1 | L + 3 |

With Jump held 14 frames, the feet are 73 px above the floor at the end
of T + 23 and 71 px at the end of T + 24, so A = T + 24 and the second
list of stage frames applies: L = A + 14 is the seventh. With Jump held
1 frame, the player is within reach already at T + 20, and L = A + 11 is
the sixth stage frame of the first list.

The same switch frame decides when walking, running or standing starts
after a landing (`ground-speed.md`, "After a landing").

Measured on `title-1` with the lead character: 826 landings after
standing, walking and running jumps (Jump held 1 to 30 frames, Action on
or off, some after walking back first): 730 on flat floor and 96 on
slopes, on floors from 82 px below to 80 px above the take-off floor,
including sites with steep faces (landings on face columns, or with A
ending on one, left out). L ranged from 2 frames before A to 23 frames after it. M matched
on all 826, and E (Jump pressed on L − 1 and held) on all 819 where it
was measured. 238 of these landings were recorded after the rule was
fixed. The window of "Pressing before landing" was checked to count
back from E with E from L + 2 to L + 8.

Jumps out of a roll (`roll-speed.md`, "Jumping out of a roll") pressed
on the ground follow the same rule, with T = P + 1 and the stage frames
counted from that T. Measured on `title-1` with the first character:
557 landings after jumps out of slow, normal and fast rolls, 487 on flat
floor (Jump pressed on a roll frame from S to S + 28 and held 1 to 40
frames, random input in the air and after the landing) and 70 in random
play through the first level, with n from 1 to 7 (landings before A
were left out). M matched on all 557.
The next jump's take-off (presses before and after the landing, as in
"Pressing before landing") matched E = M + 2 on all 227 landings where
the player jumped again before doing anything else; E = L + 3 gives 23
of them wrong.

The first character's jumps out of a roll pressed in the air, during the
fall after rolling off a ledge (`rolling-off-ledges.md`, "Jumping during
the fall"), follow the same rule too, with T = P + 1 whether P is a hover
frame or not. Measured on `title-1`: 154 landings after such jumps from
rolls off both ends of a raised platform whose ground simply ends
(normal and slow rolls, Action tapped or held; Jump pressed on L to
L + 20, some of the presses on a hover frame, and held 1 to 40 frames;
random input in the air and after the landing), with n of 1, 5 or 7 and
M of L, L + 2 or L + 4. These jumps often rise above the top of the
screen; only landings whose pictures could be followed on screen from
the last air frames on were counted. M matched on all 154. The next
jump's take-off matched E = M + 2 on all 67 landings where the player
jumped again before doing anything else and every frame up to the
take-off was on screen; E = L + 3 gives 46 of them wrong.

### After a bounce

A landing that ends a bounce off an enemy (`bounce.md`) has no stage
frames: its switch frame is the landing frame itself, M = L (the mode
starts on L, `ground-speed.md`, "After a fall or a bounce"). So
**E = L + 2**, however long jump was held before or after the bounce and
however far the player fell after it.

"Pressing before landing" below applies with this E:

| Case | Result |
|---|---|
| Pressed L − 10 to L + 1, held through L + 1 | Jumps on L + 2 |
| Pressed L − 11 or earlier, held through landing | No jump |
| Pressed in the air, released on L or earlier | No jump |
| Held without a break since B + 3 (B is the bounce frame, `bounce.md`; after bouncing again, the latest bounce frame) | No jump |
| Pressed on L + 2 or later | Jumps on the frame after the press |

Measured on `title-1` with the lead character: 190 landings after
bounces off a small walker, by inputs (jump held 1 to 37 frames for the
jump onto it and 0 to 40 frames, or to the end, from B + 3; none, Left,
Right or Action, or a direction with Action, held in the air), some
with the fall after the bounce made longer or shorter by moving the
player up or down 5 frames after the bounce. Landings were on flat
floor, on a slope and on one higher floor, 33 to 100 frames after B. 1 909 presses from L − 14 to
L + 4, held 10 to 13 frames or to the end, matched with 0 mismatches.
Landings where the player picked up an object with Action held, or left
the ground again before jumping, were left out. Landings after a second bounce
(`bounce.md`, "Bouncing again before landing"): 420 in random runs on
flat floor (take-offs from L + 2 to L + 39, or none by then), matched
with 0 mismatches.

The landing at the end of the entrance throw that begins a level
follows the same rules, with E = L + 2 (`level-start.md`, "The
landing"). There, Jump held without a break since before L − 10 starts
no jump.

### After a jump over a face's top

If `x` at the end of the approach frame A is in a face column
(`steep-faces.md`, "Columns, faces and steps"), the landing is a slide
landing (`steep-faces.md`, "The switch frame M"). This happens when a
jump passes low over a rising face and comes down beyond its top. If
the player lands on floor that isn't a face column and stays on such
floor, the stage frames above don't apply. Instead:

**M = max(L, A + 3) + 11**, so **E = max(L, A + 3) + 13**.

"Pressing before landing" below applies with this E, window included:
a press counts only if P ≥ E − 12, and this holds for presses on or
after L too. E is at least L + 13 here, so a press on L never counts.

| Case (L = A + 3 or later, so E = L + 13) | Result |
|---|---|
| Pressed in the air or on L, held | No jump |
| Pressed L + 1 to L + 12, held through L + 12 | Jumps on L + 13 |
| Pressed L + 13 or later | Jumps on the frame after the press |

With L = A + 1, E = L + 15. Presses on L + 1 and L + 2, held, give no
jump; a press on L + 3, held, gives a jump on L + 15.

Example: a running jump toward a face rising to the right, Jump held
16 frames. The feet first have floor within reach on T + 23, over a
face column, so A = T + 23. The player lands on L = T + 28 = A + 5, on
the floor beyond the face's top, and E = L + 13. Jump pressed again on
T + 20 and held gives no jump. The plain rule would give n = 4, M = L + 2
and a jump on L + 4. Jump pressed on L + 1 and held gives a jump on
L + 13.

Measured on `title-1` with the lead character, at two faces of 16
columns in the first level, one rising to the right and one rising to
the left. Both have flat floor below them and flat floor 3 px above
their top column. The runs were random walking and running jumps over
the face (Jump held 1 to 30 frames, sometimes letting go of the
direction or holding the other one in the air), each with 1 to 3 more
presses at random frames around the landing, held 1 to 20 frames or to
the end. A grid of 180 jumps and a grid of 42 presses from L − 1 to
L + 5 after landings on A + 1 and A + 2 were added. That gave 2 790
slide landings on floor that isn't a face column; the take-off frame
(or no take-off) matched this rule on all of them, and on the 2 708
plain landings in the same runs. With M as for a plain landing, 1 276
of these landings are wrong. M = max(L, A + 2) + 11 and
M = max(L, A + 4) + 11 give 44 and 46 wrong. Leaving presses made
after L out of the window gives 9 wrong. Landings that end on a face
column, or reach one before the take-off, were left out (see
`steep-faces.md`, "Jumping on or next to a face").

### The second character

With the second character as the player (`second-character.md`), a
landing after a jump has no stage frames, whether the jump started from
standing, walking or running or out of a roll, pressed on the ground or
in the air (`rolling-off-ledges.md`, "The second character"): its
switch frame is the landing frame itself, **M = L**, so
**E = L + 2**. This holds when L comes on the third or a later frame
showing the last air picture `s-air-6` (`player-frames.md`, "Second
character"). Every jump measured that lands on the floor it took off
from is such a landing. When the mode starts after these landings is in
`ground-speed.md` ("After a landing").

For a landing that comes earlier (before `s-air-6` or on its first or
second frame), the spec uses E = L + 2 as well. In the original, E was
L + 2 to L + 4 on these landings; reference comparisons on them should
allow up to 2 frames later on the next `jump_start`.

Measured on `title-1` with the second character leading and the first
following:

- 120 jumps on flat floor from standing, walking and running, Jump held
  1 to 40 frames, then pressed again on L − 1 and held: the next
  `jump_start` came on L + 2 on all 120. The first character's rule
  above gives M = L + 1 (n = 6), so E = L + 3, on 114 of them.
- 800 runs on flat floor, about half plain jumps (standing, walking or
  running, Jump held 1 to 40 frames) and half jumps out of slow, normal
  and fast rolls (Jump pressed on a roll frame from the roll's first
  frame to 23 frames after it, held 1 to 40 frames), with random input
  in the air and random input changed every 1 to 8 frames for 60 frames
  after the landing; and random play through the first level from two
  places. The take-off was read from the pictures on screen (the next
  `s-jump-1`). On the landings on the third or a later `s-air-6` frame
  where the player jumped again before doing anything else and stayed
  on the floor until then, the next take-off matched E = L + 2 (with
  "Pressing before landing" counting back from it) on all 873: 625 on
  flat floor (253 of them after jumps out of a roll) and 248 in random
  play (52 out of a roll). E = L + 3 gives 356 of them wrong, E = L + 1
  gives 311.
- 63 earlier landings where the player jumped again (all but 6 of them
  in random play): E = L + 2 fits 57 of them; the other 6 need L + 3 or
  L + 4.
- Jumps out of a roll pressed in the air: 1 600 runs on the three
  ledges of `rolling-off-ledges.md` ("The second character"), normal
  and slow rolls, Jump pressed on L to L + 20 and held 1 to 40 frames,
  random input in the air and random input changed every 1 to 8 frames
  for 60 frames after the landing, read from the pictures on screen as
  above. On the landings on the third or a later `s-air-6` frame where
  the player jumped again before doing anything else and stayed on the
  floor until then, the next take-off matched E = L + 2 on all 97.
  E = L + 3 gives 41 of them wrong, E = L + 1 gives 32.

### Landings this doesn't cover

For a landing after a fall that didn't start with a jump (walking or
rolling off a ledge), the spec defines **E = L + 3** unless another
file says otherwise. The mode after these landings is in
`ground-speed.md` ("After a fall or a bounce"). In the original E varies
on these landings too. Reference comparisons on them should allow ±1
frame on the `jump_start` and on frame counts measured from it.

## Pressing before landing (buffered jump)

A press made in the air is remembered for a short time, but only while
the input stays held.

With press frame P before landing, a jump starts on frame E if both hold:

- the jump input is held on every frame from P to E − 1, and
- P ≥ E − 12, i.e. the press came at most 11 frames before frame E − 1.

Otherwise no jump starts on landing. A new press is needed.

The window counts back from E, whatever M is.

| Case | Result |
|---|---|
| Pressed 1 to 9 frames before L, held through E − 1 | Jumps on E |
| Pressed exactly at E − 12, held through E − 1 | Jumps on E |
| Pressed at E − 13 or earlier, held through landing | No jump |
| Pressed in the air, released before E − 1 | No jump |
| Held continuously since the jump that took off | No jump |

A press on or after the landing frame (P ≥ L) starts a jump on
max(P + 1, E). That needs Jump held up to the frame before, and
P ≥ E − 12. The window matters when E is more than 12 frames after L,
as after a jump over a face's top (above).

## Worked example

A jump lands on frame 40 with M = L + 1 (L = 40, so E = 43). Pressing
again on frame 31 and holding gives a jump on frame 43. Pressing on
frame 30 and holding gives no jump.

## In `title-2`

Everything in this section was measured on `title-2` only, with the lead
character at the start of a game, on flat ground. A jump in `title-2`
starts on a grounded frame **G** and takes off two frames later: the
`jump_start` frame T is **G + 2** (`jump-arc.md`, "In `title-2`").
Nothing about the jump is visible on G and G + 1; the player stays on the
ground and horizontal speed follows the ground rules.

### When a jump starts (`title-2`)

A jump starts on frame G if all of these hold:

- the player is grounded on G, and G is not the landing frame (frames of
  a roll and of a crouch count as grounded; see below for crouches);
- the jump input is held on G;
- the press it belongs to (the first frame of the current unbroken hold,
  P) was made on **G − 15 or later**;
- that press hasn't already started a jump.

For a press made on the ground, this means a jump starts on P itself,
T = P + 2, even if the input is held for only one frame. This includes
every frame of a roll, standing or moving.

### Landing (`title-2`)

There is no variable lockout as in `title-1`. With landing frame L, the
earliest frame a jump can start is **L + 1** (so the earliest take-off is
L + 3). This was the same for every one of 180 landings measured (heights
from every hold length 1 to 30, standing and walking).

A press made in the air starts a jump on L + 1 if it is still held on
L + 1 and was made on L − 14 or later:

| Case | Result |
|---|---|
| Pressed L − 14 to L, held through L + 1 | Jump starts on L + 1 (T = L + 3) |
| Pressed L − 15 or earlier, held through L + 1 | No jump |
| Pressed in the air (or on L), released on or before L | No jump |
| Held continuously since the jump that took off | No jump |
| Pressed on L + 1 or later | Jump starts on the press frame |

The gravity rule in `jump-arc.md` counts held frames from G, which here
is L + 1.

### Crouching (`title-2`)

Crouching is described in `ground-speed.md` ("In `title-2`"). A jump can
start on any crouched frame, including the frames of getting down and the
11 frames of getting up. But if a jump starts on a frame G where the
crouch continues (exactly one of Up and Down held), and the crouch ends
on G + 1 or G + 2 (neither or both of Up and Down held), the jump doesn't
take off. Instead the crouch ends on that frame as usual, and:

- if the jump input is held on that frame, a jump starts over with that
  frame as the new G (take-off two frames later);
- otherwise there is no jump, and a new press is needed.

A jump started on the frame a crouch ends (the jump pressed together with
releasing Down) takes off normally.

| Inputs from G (crouched, Down held before) | Result |
|---|---|
| Down + jump, then Down, Down | Take-off on G + 2 |
| Down + jump, then Up, Up | Take-off on G + 2 |
| Down + jump, then nothing | No jump; getting up starts on G + 1 |
| Down + jump, then Down + jump, then nothing | No jump; getting up starts on G + 2 |
| Down + jump, then jump held | Jump starts over on G + 1, take-off on G + 3 |
| Jump alone (Down released on G) | Take-off on G + 2 |

### Not covered (`title-2`)

Presses made just before landing on ground of a different height, and
the second character. Jumps on slopes start as on flat floor (`slopes.md`,
"In `title-2`"). Jumping after walking off a ledge,
and on landing after such a fall (one frame later than after a jump), is
in `walk-off.md` ("In `title-2`").
