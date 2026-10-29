# Riding the animal

Titles: `title-1`

After mounting the animal (`animal-crate.md`), the player **rides** it.
This file covers riding on flat floor: horizontal speed, turning,
jumping, the **charge** that an Action press starts, standing and
walking, and how the animal and the rider are drawn. Getting on is
in `animal-crate.md`; platforms passable from below are in
`platforms-from-below.md` ("Riding"); everything else about riding is
listed under "Not covered".

Frames are logic frames at 60 Hz, numbered as in a trace. `x` and `vx`
are as in a trace: right is positive, `vx` in px per frame. Vertical
positions are **level y** as in `view-vertical.md`: they grow downward,
and `vy` is positive when moving down. "Left", "Right", "Jump", "Action",
"Up" and "Down" are the neutral inputs `L`, `R`, `J`, `A`, `U`, `D` of
the scenario format. A **press** of an input is a frame on which it is
held after a frame on which it was not.

Speeds are always whole multiples of 1/256 px per frame. The rules below
are exact in those units, so this file writes speeds both ways
(512/256 = 2 px per frame).

Measured with the first character alone, by inputs only:

- On a long stretch of flat floor near the first level's start, with the
  first level's crate moved there in a modified copy of the game (the
  level has no long flat floor next to its own crate), and away from the
  ends of that floor: scripted runs for each rule, and 453 runs of random
  inputs (directions, Up, Down, Jump and Action, pressed and held for
  random lengths, with Left and Right never held together), 586 426
  frames. In every frame of every run, `x`, `y`, `vx`, `vy`, `ground`,
  `facing` and which of the parts below the rider was in were exactly as
  the rules give. The runs held 8 217 jumps (972 of them from a press
  made before the jump could start), 9 193 charges (4 847 started in the
  air during a jump), 4 094 hops and 3 379 turns.
- On the flat floor next to the first level's own crate, unmodified: 40
  more random runs, 24 493 frames, with the same result.
- 40 of the 453 runs start on the first riding frame after a mount
  (M + 23 in `animal-crate.md`): riding starts with the speed the player
  had at the mount, and a Jump press on M + 23 doesn't start a jump
  (M + 22 ended in the air).

Measured with the second character as the player: see "The second
character" below, for the same floor and the same kinds of run.

## Parts of riding

At any frame the rider is in one of these parts:

- **riding**: on the floor, under control;
- a **turn** (8 frames);
- a **jump**: 5 **crouch** frames on the floor, then the air, then 8
  **landing frames** on the floor;
- a **charge**, then its **charge end** (12 frames), then one
  **settling frame**, which is riding with the exceptions given below.

## Horizontal speed

On every frame (in every part above) the rider has a **target speed**
set by what is held on that frame:

| Held | Target |
|---|---|
| Right | +2 (+512/256) |
| Left | −2 (−512/256) |
| Right and Action | +3.5 (+896/256) |
| Left and Action | −3.5 (−896/256) |
| No direction | 0, with or without Action |

Up, Down and Jump make no difference.

Each frame, using speeds in 1/256 px per frame:

1. **gap** = target − current speed.
2. **step** = gap ÷ **N**, rounded toward zero to a whole number, where
   N is **8** on a **steered** frame (next section) and **32** on every
   other frame.
3. If step is 0, the speed becomes the target. Otherwise the speed
   changes by step.
4. The rider moves by the new speed: `x` changes by exactly the new `vx`
   on that frame.

This is the speed rule of `ground-speed.md` with other targets and, on
frames that aren't steered, a step of a 32nd of the gap instead of an
8th. So with N = 32 the speed snaps to the target once the gap is less
than 32/256 either way. There is no separate braking or turning rate.

## The second character

With the second character as the player (leading, the first character
following), riding follows every rule of this file except the
four rows of the table above (the two speed targets for walking and
the two for charging, each to the right and to the left), which are
larger:

| Held | Target (second character) |
|---|---|
| Right | +2.25 (+576/256) |
| Left | −2.25 (−576/256) |
| Right and Action | +3.9375 (+1008/256) |
| Left and Action | −3.9375 (−1008/256) |
| No direction | 0 |

The speed rule, the divisors 8 and 32, turns, jumps, the charge and
the steered frames are as for the first character, with these targets.
So from rest with Right held the speed goes 18, 35, 51, 67, 82 in
1/256 px per frame on the first frames (16, 31, 46, 60, 74 for the
first character), and the vertical speeds of the jump, the hop and the charge are those
of the first character. With the first
character as the player and the second character following, the
rules and targets above hold unchanged.

Observed on the same kind of flat floor as above (a setup method was
used): 16 runs of random inputs with the second
character leading (7 577 frames checked, containing 50 jumps from
riding, 52 turns and 120 charges), and 16 runs with the first character
leading and the second following (8 000 frames). In every frame of every
run, `x`, `y`, `vx`, `vy`, `ground`, `facing` and the part of riding were
exactly as the rules give, with the targets above for the second
character. Not checked: these rules in ordinary play, at the level's own
animal without a setup method, with the second character as the player;
getting off, and riding at walls with the second character. The pictures
with the second character are in "Pictures with the second character"
below, and riding on slopes with the second character is in
`riding-slopes.md`.

## Steered frames

A jump makes the frames after it **steered** for a while. A frame is
steered when steering was on at the end of the frame before it.

- Steering comes on at the end of a jump's press frame (P below), so
  P + 1 is the first steered frame.
- It goes off at the end of the jump's landing frame: the landing frame
  is still steered, the frame after it isn't.
- If a charge starts during the jump (from its crouch or its air
  frames), steering stays on through the charge and its end, and goes
  off at the end of the settling frame: the settling frame is still
  steered.

On a steered frame:

- the speed step is gap ÷ 8;
- facing is the direction held on that frame (Left or Right); with no
  direction held it stays as it was. No turn starts;
- Jump presses don't start a jump (next sections).

On frames that aren't steered, facing changes only through turns.

## Riding, and turns

While riding, `y` is the floor's level y and `vy` is 0.

A **turn** starts on any riding frame on which the direction opposite
to facing is held, at any speed: call it T. The turn lasts 8 frames, T
to T + 7. Facing changes on **T + 4**. On T + 8 the rider is riding again,
and a new turn starts on T + 8 if the opposite direction is held then.
Holding the opposite direction for one frame is enough: the turn
completes even if nothing is held after T. The turn has no effect on
speed.

Turns don't start during a jump (crouch, air or landing frames), a
charge or a charge end. A jump or a charge started during a turn ends the
turn; facing keeps whatever value it had (it has changed only if T + 4
has passed).

Up and Down do nothing while riding.

## Jumping

A jump **starts** on frame P when all of these hold:

1. Jump is held on P, and either it is pressed on P, or it was pressed at
   most 11 frames before P (on P − 11 or later) and held on every frame
   since (a **remembered press**);
2. at the end of frame P − 1 the rider was on the floor (the hop's
   take-off frame, under "The charge", counts as on the floor);
3. P is not a steered frame.

So a jump can start while riding, during a turn, on the landing frames
of an earlier jump from the frame after its landing frame, during a
charge while on the floor, on the frame right after the hop's take-off
frame, and during a charge end and on the settling frame (unless a jump
made them steered). A press made in the air can't start a jump there,
but if it is still held, it starts one on the first frame these rules
allow, provided that frame is at most 11 frames after the press.
Example: a jump lands on frame L. Jump pressed on L − 10 and held starts
a jump on L + 1; pressed on L − 11 and held, no jump starts. Holding
Jump since the jump that took off never starts another.

Frames of the jump:

- **P to P + 4 (crouch)**: the rider stays on the floor; only `x` moves.
  Facing doesn't change on P; from P + 1 the frames are steered.
- **T = P + 5 (take-off frame)**: the rider hasn't moved up yet and is
  still on the floor, but `vy` is already **−7**.
- **From T + 1 until landing**: `vy` increases by the frame's gravity, but
  never above 8, and the rider moves by the new `vy`. The gravity is
  **0.28125** if Jump has been held on every frame from P (the press frame
  of a remembered press) up to and including this frame, and **0.4375**
  otherwise, as in `jump-arc.md` with this file's P. Landing is as in
  `landing.md`: the rider lands on the first frame on which they are
  moving down and end less than 5 px above the floor, or at or below it,
  and is placed on the floor.

So the vertical part of a riding jump is the vertical part of an
ordinary jump (`jump-arc.md`) started 4 frames later. Holding Jump on
P + 1 to P + 4 doesn't change the jump: the lighter gravity first
matters on T + 1.

- **Landing frames L to L + 7**: the rider is on the floor. L is still
  steered; L + 1 to L + 7 are not, but no turn starts on them. A jump
  can start from L + 1 (rule above), and a charge from L. On L + 8 the
  rider is riding again.

A jump that starts on the frame right after the hop's take-off frame
starts in the air: its crouch frames fall under the jump's gravity like
air frames, and on T `vy` is set to −7 in the air.

## The charge

An **Action press** starts a charge on that frame, call it C, when the
rider is

- riding (but not on the settling frame), turning, or on a jump's
  landing frames, or
- in a jump's crouch or air frames, including its landing frame. The
  charge keeps the jump's steering (see "Steered frames").

Action presses during a charge, a charge end or on the settling frame
do nothing.

When Jump and Action are pressed on the same frame, the Jump press is
taken first: if it starts a jump, the Action press then starts a charge
from that jump's crouch, on the same frame. So pressing both together
while riding gives a steered charge.

Frames of the charge:

- From C on, the speed rule applies on every frame as above, steered or
  not. Facing doesn't change during a charge that isn't steered.
- While the rider is on the floor, `y` doesn't change, until:
- **C + 10 (hop take-off)**: if the rider is on the floor at the end of
  this frame's vertical motion (including landing on this frame), `vy`
  becomes **−4**; the rider hasn't moved up yet and is still on the
  floor.
- **After the hop**: `vy` increases by the frame's gravity, never above
  8, and the rider moves by the new `vy`, landing as in `landing.md`. The
  gravity is 0.4375, except that a charge started from a jump uses the
  jump's rule: 0.28125 while Jump has been held on every frame since the
  jump's press frame P. So does a charge started after a mount while
  Jump has been held on every frame since the press of the jump on foot
  the player mounted from (`getting-off.md`, "Right after a mount").
- A charge that starts in the air keeps falling under the same gravity
  rule. If it is still in the air on C + 10, there is no hop. If it lands
  before C + 10, it hops on C + 10.
- A Jump press can start a jump during the charge (rules under
  "Jumping"); the charge then ends.

The **charge end** begins on the first frame from C + 28 on that ends
with the rider on the floor: on C + 28 itself if the rider is on the
floor then, otherwise on the frame the rider lands. It lasts 12 frames,
E to E + 11, on the floor. Frame **E + 12** is the **settling frame**:
the rider is riding again, a turn can start on it, and an Action press
on it does nothing (unless a Jump press starts a jump on it first, as
above).

For a charge started while riding on flat floor, with Jump not held:
the hop leaves the floor on C + 11 and lands on C + 26, the charge end
is C + 28 to C + 39, and C + 40 is the settling frame. The hop's
highest point is 16.3125 px above the floor, reached on C + 19.

## Standing and walking

On the floor, outside a turn, a jump, a charge and a charge end, the
rider is **standing** or **walking**. Nothing above depends on which:
speed, turns, jumps and the charge follow the sections above either way.
The difference is in the pictures ("Pictures"), and in when a turn, a
jump or a charge ends in one or the other.

Below, **forward** is the direction the rider faces at the start of the
frame, **opposite** the other one, "held" means held alone (not with the
other direction), and `v` is |`vx`| in 1/256 px per frame after the
frame's speed rule.

**Which one comes next.** The **first riding frame** is the frame after
a turn's 8 frames (T + 8), after a jump's landing frames (L + 8) and
the settling frame (E + 12) after a charge end. A Jump or Action press
that starts a jump or a charge, and the opposite direction held (a turn,
"Riding, and turns"), come first, as the rules above say. Otherwise:

- on a **first riding frame**: walking starts on it if forward is held,
  or if `v` is 224 or more (7/8 px per frame) even with nothing held, and
  standing starts if not. A rider still moving fast after a charge or a
  jump therefore goes on walking, and one slower than that, with nothing
  held, stands. (Measured: 223 and less stood, 224 and more walked);
- **while standing**: forward held starts walking on that frame, at any
  speed;
- **while walking**: see "The stride" and "Stand check".

On a steered frame (the settling frame after a steered charge, the
landing frames of a jump) facing is the direction held and no turn
starts ("Steered frames"): "forward" is then the direction held.

The first frame of a stretch of walking, and the first frame of a stretch
of standing, are called W and S in what follows.

**The stride.** A walking rider goes through a cycle of 15 **steps**,
numbered 1 to 15; step 1 follows step 15. Each step begins with a change
of the animal's picture or of the rider's height ("Pictures"), so every
step boundary can be seen on screen. A **stride counter** is a compact way
to give how many frames each step lasts at any speed (the durations were
read from the pictures at many constant speeds):

- On W the stride starts at step 1 with the counter at 768. Nothing else
  happens on that frame.
- On each later frame of walking, the counter goes down by the **stride
  rate**, ⌊`v`/2⌋ + ⌊`v`/4⌋ but at least 192. If the result is 0 or less,
  the step **ends** on that frame: the length of the next step is added
  to the counter and the next step begins, on that frame; this is
  repeated while the counter is still 0 or less (at the top speeds, a
  rate above 256 can let steps 14 and 15 end on the same frame).
  Steps 1 to 13 are 768 long, step 14 is 512 long and step 15 is 256 long.

At speeds below 256 a step of 768 lasts 4 frames; at 2 px per frame
(`v` = 512, rate 384) it lasts 2 frames. The stride goes on, and only its
step is used (the rider's height, "Pictures"), on the first frame of a
turn that starts while walking; the pictures of a turn don't use it.

**Stand check.** On a frame on which step 4 or step 15 ends, if `v` is
less than 48: with neither Left nor Right held the rider is standing from
that frame (that frame is S); with forward held, walking starts again on
that frame (step 1, counter 768, as on W; the rider's moving cycle and
height start again too, "Pictures"). Otherwise, and on the ends of the
other steps, nothing happens: the walk goes on.
Examples (flat floor, facing left, from standing, Left held on frames 0
to n − 1, nothing else): n = 1 to 4 — step 4 ends on frame 16 with `v`
below 48 (0 for n = 1 and 2, 32 for 3, 47 for 4) and the rider stands from
frame 16; n = 5 — `v` is 56 on frame 16, the walk goes on, and step 15
ends on frame 56 with `v` 0: standing from frame 56 on.

## Pictures

What the animal and the rider look like in `title-1` while the first
character rides on flat floor, on every frame of the parts above and of
"Standing and walking", including the first frames after the mount
(`animal-crate.md`, "While mounting"; what that leaves out is under "Not
covered"). Each picture has a label used in this file and is named
by its picture hash (`../formats/sprites.md`, "Picture
hashes"). The picture given for a frame is the one on screen with that
frame's view.

**Two pictures a frame.** Every frame of riding draws two pictures at
the player's position (`../formats/sprites.md`, "Where the picture is
drawn"): the **animal**, and the **rider** sitting on it, drawn in front
of it. Both are mirrored left to right when the player faces left on the
frame (the facing of the trace row), and not mirrored when it faces
right; so they change side on T + 4 of a turn. The rider's picture is
drawn at the same x as the animal's and **d** px lower (higher when d is
negative), whatever the picture. d is not always the same: "The rider's
height" below.

| Picture | Picture hash (SHA-256) |
|---|---|
| `ride-turn-1` | `a0595a49c3c1183235f903b8e8b9d9b34e8aec212a0881d743b27830270a9be6` |
| `ride-turn-2` | `b440b9aaf02b8650060e59932ed9e139523fc3adc87013ae66a9d3546c4f69a5` |
| `ride-walk-1` | `2dc178093a660f193147d1be9f89288da6721dfac942a9bbc3c675650ee513e3` |
| `ride-walk-2` | `76ccb53a372574e0e28ed0c8eb9d2729e53862750d64cd5053930bbe99d869f2` |
| `ride-walk-3` | `d4f16256b0d7cd952acca54fdcb7a4ef51e785d7329ea803802eb00b868e3fb0` |
| `ride-walk-4` | `e0a9af4c20472158ad65e9ac8a894111e5398d40fae614f71afa34312f6504da` |
| `ride-walk-5` | `02da78bbd0e6feca3f29e7823c5053a48280f19204b9fadaa8d051cbcc3927b5` |
| `ride-walk-6` | `fbebe9b78353e757eafdd7ef44188e19c44999b2aa9329aa8e561f3d2b74147c` |
| `ride-walk-7` | `b291ca739da734c67efe8700f1e89bd629ef8e0e8f841757b5658c8938e9961f` |
| `ride-walk-8` | `d60cd3363b16540bf6ad807712698383b7bde0181d986b06d2953562822a341d` |
| `ride-walk-9` | `78bfb9511837b9a6d34c743307143d36215060f4e31adf15d26ff21f8f316eab` |
| `ride-walk-10` | `242e56b5f9cd582e8088369c9ffdcf70c0f95be2147492a557a7bc3a32bf0047` |
| `ride-walk-11` | `7dba37ac220a0a7881b8d1208a45f8302c4bd536071df4d0d416ddf6fa0de76a` |
| `ride-walk-12` | `3ffde9faa94179aca5092006b8cd5ea1a2147d749a1509ec46a61dd28e6e8a8a` |
| `ride-walk-13` | `cb8fcccc5cbb32e99223f68e98e321457c591d4e837e25c26a85741099fb57aa` |
| `ride-walk-14` | `2097ef108d081845e73bf9fa81436713fc9ebba013e1970e96f9505bedea78d0` |
| `ride-jump-1` | `52d9a7388ca7e6ee5cc82bf4ee80e58e95640c58c8194a88787d76d4c46254c7` |
| `ride-jump-2` | `4a50f25290a0b598a5770e7375f8cd3355d11f97ce2ba6a77cb45aa44c57e5f8` |
| `ride-jump-3` | `05bebab3d565d8fdec2de21ad9788f2eabfdde27bb98a942310715a19b4a033c` |
| `ride-jump-4` | `cbc131072843658cb65c086863cd313d817c67712064628415cee07ca687c7b3` |
| `ride-jump-5` | `1e37672c9744686d2b83d61b9d27424024516fcc3852f377120c5ef40750f612` |
| `ride-jump-6` | `7a63d107cdfd03b89ee2d62cc688c5224733408c1e7650984dc97e8554378b7e` |
| `ride-charge-1` | `d5df73140d1cc8da94265d2f7884c9790c4e50b6c6f918942fa0862c1a582bc8` |
| `ride-charge-2` | `fab9a5b428406375577de12800fa720f396f365e25e10dc16cdb2b7b69fac4fd` |
| `rider-1` | `f3af9ff72667449efbba318d5e0e7a2d8ba8eaf34d4f648a3c8da95988c59c27` |
| `rider-2` | `45dd0e320db5edb2137e9868afa6e1ce00c059aa35c259ec8944c4af5e46772c` |
| `rider-3` | `ab6af9981f24ac29d213380609b7ee7a35570ca239f1728db291acae79bdc5cd` |
| `rider-4` | `785e0c9185ba5a202e86f37e950554bd9e9f07f47204801cdde2b96cdc0d5241` |
| `rider-5` | `bbf7403935646d00b2c77fb0b0cc081b40e14bc6ef17989ceaf7553835345007` |
| `rider-6` | `ff335196c29d3bc5c78b462e7d9df4468724a8d79527054e7993b6a398397d6b` |
| `rider-7` | `2543bfa471dc15c59af069d3d032d419c3ab9868b2408f72d1489650c6a4915d` |
| `rider-8` | `b3a5694010d6e0af7edd1bca90ef47ed3dc0ce9a7fce07b484976db7a9e614a2` |
| `rider-9` | `4014b48ab6c89d8abe78979caea6c770cc6467e1700496c8f7838dd8e309e2d8` |
| `rider-10` | `c8601d211a37c5529927f7fe2442d9dd938d6ea1a6c8e24d3213772c81897c1b` |
| `rider-11` | `bc8b3b1a6872f5d9139dc2d017d96007d4fc95f72788e5a3f4c023abbb198d29` |
| `rider-12` | `751303c886d46a4785d65aad3a0326bb6a6dc3b7eac97d886e07adb7eb9c4312` |
| `rider-13` | `31b0e7b55c5c2b64ad504a1c83c9f4d6ec6c11d9bfe8b131568483ba5af874eb` |
| `rider-14` | `46a1bf38d323a3d1ca18f469c93e0dfd0f62de96d7041ed4e5929bdfce7e6c87` |
| `rider-15` | `1ac54e1052bec52495c098dd8f6bed3dc782508eb1393af0ccaabdebebd2d05d` |
| `rider-16` | `dabcb8239729663951a60e3fe5e44f26d33b9bd42397e5dcbb587d76c36eb28e` |

The animal's pictures while standing are `animal-1` to `animal-6` of
`animal-crate.md` ("Pictures", the animal waiting). `rider-1`, `rider-9`,
`rider-10` and `rider-11` are the pictures `mounting-5`, `mounting-4`,
`mounting-3` and `mounting-2` of that file.

**When a hash isn't found.** The importer looks for every hash in the
table above and for `animal-1` to `animal-6`. The animal's pictures
(`ride-turn-1` to `ride-charge-2`, with `animal-1` to `animal-6`) belong
together, and so do the rider's (`rider-1` to `rider-16`): if any hash of
a group isn't found, the engine shows its own art for the group, on the
frames, at the places and with the mirroring given here.

### The rider's height

d changes only like this:

- On every frame of walking, d is the value of the current step in the
  table below. On the first frame of a turn that starts while walking,
  it is the value of the step the stride is in after that frame's counter
  update (the stride goes on for that one frame, "The stride").
- d becomes 1 on the first frame of a charge (also a charge that a
  Jump and Action press starts in a jump's crouch), on the first frame of
  a turn that doesn't start from walking (from standing, a jump or a
  charge end), and on the frame after a turn's 8th frame (T + 8), also
  when a turn starts again on it. A turn that a jump or a charge
  interrupts doesn't set d on the next frame (a charge sets 1 as said).
- Everywhere else d stays as it was. So after a walk the rider keeps
  the d of its last step through a jump, a landing and standing, until a
  charge or a turn sets 1 or the next walk sets its step's value.

| Step | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| d | 1 | 1 | 0 | 0 | −1 | −2 | −2 | −3 | −3 | −3 | −2 | −2 | −1 | 0 | 1 |

### Standing

From S, the animal's pictures are `animal-1`, `animal-2`, `animal-3`,
`animal-4`, `animal-5`, `animal-6`, `animal-5`, `animal-4`, `animal-3`,
`animal-2`, then `animal-1` again and so on, **6 frames each**: a cycle
of 60 frames.

The rider's pictures play a fixed **loop of 284 frames** that advances
on every standing frame, and only on those. Its 14 entries, in order,
with the number of frames each shows:

| Entry | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Picture | `rider-6` | `rider-7` | `rider-8` | `rider-1` | `rider-8` | `rider-7` | `rider-6` | `rider-7` | `rider-8` | `rider-1` | `rider-2` | `rider-3` | `rider-4` | `rider-5` |
| Frames | 58 | 10 | 10 | 10 | 10 | 10 | 58 | 10 | 10 | 10 | 10 | 58 | 10 | 10 |

(Entry 14 is followed by entry 1.) Where the loop starts depends on how
the standing starts:

- **On S, in general** (after walking, a turn, a landing or a charge
  end): at entry 10, with `rider-1` on S to S + 8 (9 frames, not 10),
  `rider-2` on S + 9 to S + 18, `rider-3` on S + 19 to S + 76, and so
  on.
- **After a jump taken from standing** (the frame before P was a
  standing frame): the loop doesn't advance during the jump (P to
  L + 7) and goes on from the position it had on the last standing frame
  before P. Until the entry that frame was in ends, the rider shows
  `rider-9` (the picture of the jump's end) instead of that entry's
  picture; from the next entry on, the entries' pictures.
  Example: a jump from standing taken on the 30th frame of entry 1 (so 28
  frames of it are left) shows `rider-9` on L + 8 to L + 35 and then
  `rider-7`.

The first standing after the mount starts on M + 22 and follows the
rules above, with S = M + 22 (`animal-crate.md`, "While mounting").

### Walking

The animal's picture on a frame of walking is that of the current step
("Standing and walking"): step 1 `ride-walk-1`,
2 `ride-walk-2`, 3 `ride-walk-3`, 4 `ride-walk-4`, 5 `ride-walk-5`,
6 `ride-walk-6`, 7 `ride-walk-7`, 8 `ride-walk-8`, 9 `ride-walk-9`,
10 `ride-walk-10`, 11 `ride-walk-11`, 12 `ride-walk-12`, 13
`ride-walk-13`, and steps 14 and 15 both `ride-walk-14` (told apart by
the rider's height, 0 and 1).

The rider's pictures on frames of walking and of a turn follow the
**rider's moving cycle**: `rider-14`, `rider-15`, `rider-16`,
`rider-15`, then `rider-14` again and so on. Let m be the number of
frames since the cycle **started**, m = 0 on the start frame: the picture
is the (⌊(m + 1)/8⌋ mod 4)-th of the cycle (the first is number 0). So
`rider-14` is shown on the first 7 frames, and every later picture on 8
frames. The cycle starts on W (also when walking starts again at a stand
check), on a turn's first frame when the frame before wasn't walking or
turning (from standing, a jump, a charge or a charge end), and on T + 8
of a turn (also when a turn starts on it). It doesn't start again when a
turn starts while walking: the cycle goes on through the turn.

### A turn

On the frames T to T + 7 of a turn the animal's pictures are
`ride-turn-1` on T and T + 1, `ride-turn-2` on T + 2 to T + 5 and
`ride-turn-1` on T + 6 and T + 7. The rider's pictures are those of its
moving cycle above.

### A jump

With P, T, L as in "Jumping", the animal's pictures are `ride-jump-1` on
P to P + 4, `ride-jump-2` on T to T + 2, `ride-jump-3` on T + 3 to
T + 5 and `ride-jump-4` from T + 6 until the frame before the **fall
frame** S1, then `ride-jump-5` from S1 until L − 1, `ride-jump-6` on L
to L + 2 and `ride-jump-1` on L + 3 to L + 7. The rider's are
`rider-9` on P to P + 4, `rider-10` on T to T + 2, `rider-11` from T + 3
until S1 − 1, `rider-12` from S1 until L − 1, `rider-13` on L to L + 2
and `rider-9` on L + 3 to L + 7.

S1 is the later of two frames: F + 2, where F is the first frame that
ends with the rider moving down (`vy` > 0); and W1 + 1, where W1 is the
first frame from F on that ends with the rider at most 72 px above the
floor of the take-off (the limit lies between 71.5 and 73.1 px). In a
jump whose highest point is no higher than 72 px (Jump held for 15
frames or fewer, measured), W1 = F and S1 = F + 2. A higher jump has its
highest point more than 72 px up, and S1 comes when the fall has brought
the rider down to 72 px: 5 to 10 frames after F in the jumps measured.

### A charge

From the frame C of the charge's press, the animal's pictures are
`ride-charge-1` on C to C + 9 and `ride-charge-2` from C + 10 until the
frame before the charge end. A charge end's 12 frames, E to E + 11, show
`ride-walk-10` on E to E + 2, `ride-walk-11` on E + 3 to E + 5,
`ride-walk-13` on E + 6 to E + 8 and `ride-walk-14` on E + 9 to E + 11.

The rider's pictures, for a charge pressed on a frame of standing,
walking or turning (the rest is not covered): `rider-9` on C to C + 5,
`rider-10` on C + 6 to C + 8, `rider-11` on C + 9 to C + 32, `rider-10`
on C + 33 to C + 37, and `rider-9` from C + 38 on, until the standing or
walking that follows starts.

For a charge that starts in a jump's crouch, in the air or on a landing
frame, the animal's pictures are as above, and the rider's pictures are
not covered.

### Example of a jump

Jump pressed on frame 0 only, from standing (P = 0), the jump landing on
frame 36 (T = 5, F = 22, W1 = F, S1 = 24, L = 36): animal `ride-jump-1`
on 0 to 4, `ride-jump-2` on 5 to 7, `ride-jump-3` on 8 to 10,
`ride-jump-4` on 11 to 23, `ride-jump-5` on 24 to 35, `ride-jump-6` on 36
to 38 and `ride-jump-1` on 39 to 43; rider `rider-9` on 0 to 4,
`rider-10` on 5 to 7, `rider-11` on 8 to 23, `rider-12` on 24 to 35,
`rider-13` on 36 to 38 and `rider-9` on 39 to 43. Standing starts on 44.

### Evidence for the pictures

Measured in the first level on the flat floor of a modified copy of the
game (as above), with the sprites alone drawn, by inputs alone:

- 27 runs of random play (directions, Up, Down, Jump and Action, pressed
  and held for random lengths) from three riding states, 14 544 logic
  frames, with 64 turns, 184 jumps, 231 charges, 164 starts of walking
  and 42 of standing;
- 16 scripted runs of 6 840 frames in all: a jump taken from standing at
  each of 12 places of the rider's standing loop followed by 400 standing
  frames, and a stop of the walk, a turn and a charge each followed by
  standing.

Everything below is what was seen on screen and in the positions, on
the 21 384 frames of these 43 runs.

- **The animal's picture and its mirroring.** The rules above give the
  animal a picture on 18 689 frames. It was identified on screen on
  17 742 of them and was the one given on all of them; on the other 947
  another sprite hid too much of it (296 in charges, 470 in jumps, 101
  standing, 58 walking, 22 in turns). The mirroring was the one given on
  the 17 742.
- **The rider's picture.** The rule gives it on 12 621 of the 18 689
  frames (standing, walking, turns whose moving cycle was known, jumps and
  charges pressed on a frame of standing, walking or turning), and it was
  the one given on all of them. The other 6 068 are not covered: 6 032
  frames of charges pressed in a jump (the rule for the other charges
  gave the picture on 5 011 of them and another on 1 021, "Not covered"),
  and 36 frames of turns at the start of a run, where the phase of the
  moving cycle wasn't known. The rider's mirroring was the one given on
  18 653 frames (the 12 621 and the 6 032).
- **The rider's height d.** The rule gives d on 15 372 frames (the rest
  are frames before the run's first walk, turn or charge, when d is not
  known); the rider was drawn at that height below the animal on all
  of them.
- **Which comes next.** After 6 405 standing frames, 226 first riding
  frames and 1 497 frames on which a step of the walk ended, standing,
  walking or a turn followed as the rules in "Standing and walking" say.
  Standing and walking were told apart by their pictures. The limit of 224 was
  measured on further runs (random play, taps of Left, jumps held for 1
  to 40 frames, charges followed by different speeds) with `v` from the
  positions on the first riding frame from 183 to 331: 223 and below
  stood, 224 and above walked. Over all runs, 173 stand checks were
  seen: 19 ended in standing, 2 started walking again with forward held
  and 152 went on, with `v` of 48 or more.
- **The fall limit.** The limit of 72 px lies between 71.5 and 73.1 px;
  9 jumps went higher than 72 px, with S1 − F from 5 to 10 frames.

## Pictures with the second character

What the animal and the rider look like while the **second character** is
the player and rides on flat floor, on every frame of the parts above and
of "Standing and walking", except the first frames after the mount (see
"Not covered"). The first character, when it is on the level, follows as
usual.

**The animal** is drawn exactly as in "Pictures" for the first
character: the same pictures on the same frames in standing, walking,
turns, jumps, charges and charge ends, and the same mirroring. Nothing in
this section changes it, with one limitation: on one charge frame of the
measurements another animal picture than the given one was on screen,
and that frame is not explained (see "Not covered").

**The rider** is drawn at the same place as for the first character
("Two pictures a frame": in front of the animal, at the animal's x, d px
lower, mirrored like the animal), but with the pictures of the second
character, with timings of their own. They are named by their picture
hashes like the others:

| Picture | Picture hash (SHA-256) |
|---|---|
| `rider2-1` | `0dbb9575884f04a5a16daaebee28e1bd7f2fe3738f79e678f9ad6bf0f073bf77` |
| `rider2-2` | `14674da2273016d31b8d911320d5fe013f4545d190c70618336b53713d1629cf` |
| `rider2-3` | `9bf1b3b1576e20c30fd1a3138aa7b8be04bffe7b5fe252502653c56b8abbc5b8` |
| `rider2-4` | `735b97b200c7dc45c542f1c2cf04130737992f85dcb7a223bad407c9a26a2bfb` |
| `rider2-5` | `629feec9eed9c918f7eb7b690d7f053deaad0fb73cbdba719b6140f4a438665e` |
| `rider2-6` | `761aca2755ceee7dc6338d9dfd53749bdad5ce7d0da85a932b69f0f9795e9d51` |
| `rider2-7` | `15b89a799c22307242e775748f459df25c244094164f3894059eaa1463a7503a` |
| `rider2-8` | `f3dc1463de87a6402c91c05d937bcfcebd23b524353e352572d556b464397e1e` |
| `rider2-9` | `a2dc0c2f5943f8f716c99c1881589958e6d160493cb636b31bcb662ee82676a8` |
| `rider2-10` | `6ec04a5173d827901b9d758ccceff24e02a4e9ce1ee51ce4d3a8a919aab4016d` |
| `rider2-11` | `9121d214a7b6e02b5020d584d5931ffc34ff738723608a3edb2baa88883c3868` |
| `rider2-12` | `95faf279cb0034de55c2f7cff49f5d39d40a8f7f8946e9aac14331e43ef0ed24` |
| `rider2-13` | `542b9c01387d5c19070c939c1ec2d4686a21b7a3c18085933932e7d864350010` |
| `rider2-14` | `26b6663ace6d7998c0d1a5cc85fc0a027d767a1f378b39d5fb462eb3370cf1fc` |

`rider2-1` to `rider2-5` are the second character's standing pictures,
`rider2-6` to `rider2-9` its pictures while moving and `rider2-10` to
`rider2-14` those of a jump and a charge.

**When a hash isn't found.** The importer looks for every hash in the
table above. The pictures `rider2-1` to `rider2-14` belong together: if
any hash isn't found, the engine shows its own art for the second
character's rider, on the frames, at the places and with the mirroring
given here (the animal's pictures are those of "Pictures" and are not
affected).

### The second character's rider: height

d is given by the rules of "The rider's height" with these differences:

- A charge sets d to 4 on its first frame C (also for a charge that a
  Jump and Action press starts in a jump's crouch, in the air or on a
  landing frame), and d is 4 on C to C + 9 and 1 from C + 10 on.
- A jump gives d its own values, by the rider's picture ("A jump"
  below): 2 on P to P + 4 (`rider2-10`), 0 on T to T + 2 and −2 on
  T + 3 to T + 5 (`rider2-11`), −4 while `rider2-12` is shown, −3 while
  `rider2-13` is shown in the air, −2 while `rider2-14` is shown, 2 on
  L to L + 2 (`rider2-13`) and 3 from L + 3 on. After a jump d stays 3
  until a charge, a turn or a step of walking sets it as above.

Walking and turns set d exactly as for the first character (the same
steps, the same frames).

### The second character's rider: standing

The animal's pictures are those of "Standing". The rider's pictures play a
fixed **loop of 166 frames** that advances on every standing frame, and
after a charge also on the last four frames of the charge (C + 36 to
C + 39, below), and on no other frame. Its 12 entries, in order, with the number of frames each
shows:

| Entry | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Picture | `rider2-1` | `rider2-2` | `rider2-3` | `rider2-4` | `rider2-3` | `rider2-2` | `rider2-3` | `rider2-4` | `rider2-5` | `rider2-4` | `rider2-3` | `rider2-2` |
| Frames | 40 | 8 | 8 | 40 | 8 | 6 | 6 | 6 | 20 | 8 | 8 | 8 |

(Entry 12 is followed by entry 1.) Where the loop starts depends on how
the standing starts:

- **After walking, a turn or a landing** (S is the first standing
  frame): at entry 1 with `rider2-1` on S to S + 38 (39 frames, not 40),
  then `rider2-2` on S + 39 to S + 46, and so on.
- **After a charge pressed on a frame of standing, walking or turning**
  (C is its press, as in "A charge" below): `rider2-1` is on C + 36 to
  C + 75, so on S, which is C + 40, the loop is on the 5th frame of
  entry 1, and `rider2-1` shows on S to S + 35. After a charge pressed in
  a jump see "Not covered".
- **After a jump taken from standing** (the frame before P was a standing
  frame): the loop doesn't advance during the jump (P to L + 7) and goes
  on from the position it had on the last standing frame before P. Until
  the entry that frame was in ends, the rider shows `rider2-11` (the
  picture of the jump's end) instead of that entry's picture; from the
  next entry on, the entries' pictures.

The first standing after the mount (M + 22 on) is not covered.

### The second character's rider: walking and turns

The animal's pictures are those of "Walking" and "A turn". The rider's
**moving cycle** is `rider2-6`, `rider2-7`, `rider2-8`, `rider2-9`,
`rider2-8`, `rider2-7`, then `rider2-6` again and so on, **6 frames
each**: with m as in "Walking" (the number of frames since the cycle
started, from the same start frames as for the first character), the
picture is the (⌊(m + 1)/6⌋ mod 6)-th of the cycle (the first is number 0),
so `rider2-6` is shown on the first 5 frames and every later picture on
6. A turn's frames use the same cycle, which goes on through a turn that
starts while walking.

### The second character's rider: a jump

With P, T, L, F and S1 as in "A jump" (F is the first frame that ends
with the rider moving down), the rider's pictures are `rider2-10` on P to
P + 4, `rider2-11` on T to T + 5, `rider2-12` from T + 6 until F − 1,
`rider2-13` from F until S1 − 2, `rider2-14` from S1 − 1 until L − 1,
`rider2-13` on L to L + 2 and `rider2-11` on L + 3 to L + 7. When S1 =
F + 2 (a low jump), `rider2-13` is shown on F alone.

For the jump of "Example of a jump" (P = 0, T = 5, F = 22, S1 = 24, L =
36): `rider2-10` on 0 to 4, `rider2-11` on 5 to 10, `rider2-12` on 11 to 21,
`rider2-13` on 22, `rider2-14` on 23 to 35, `rider2-13` on 36 to 38 and
`rider2-11` on 39 to 43; d is 2 on 0 to 4, 0 on 5 to 7, −2 on 8 to 10,
−4 on 11 to 21, −3 on 22, −2 on 23 to 35, 2 on 36 to 38 and 3 on 39 to
43.

### The second character's rider: a charge

For a charge pressed on a frame of standing, walking or turning, with C
as in "A charge": `rider2-10` on C and C + 1, `rider2-11` on C + 2 to
C + 4, `rider2-12` on C + 5 to C + 7, `rider2-13` on C + 8 to C + 10,
`rider2-14` on C + 11 to C + 26, `rider2-13` on C + 27 to C + 29,
`rider2-12` on C + 30 to C + 32, `rider2-11` on C + 33 to C + 35 and
`rider2-1` from C + 36 on, the first entry of the standing loop, until
the standing or walking that follows starts. In every charge that
nothing interrupted the charge end began on C + 28, the earliest frame
"The charge" allows, so the standing or walking that follows started on
C + 40 (the settling frame, E + 12).

For a charge that starts in a jump's crouch, in the air or on a landing
frame, the rider's pictures are not covered.

### Evidence for the second character's pictures

Measured on the flat floor of a modified copy of the game (as above),
with the sprites alone drawn, by inputs alone, with the second character
leading and the first character off screen (a setup method was used):

- 18 runs of random play (directions, Up, Down, Jump and Action, pressed
  and held for random lengths) from a riding state, 12 505 logic frames,
  with 63 turns, 170 jumps, 203 charges and 156 starts of walking;
- 16 scripted runs of 6 856 frames in all, as for the first character: a
  jump taken from standing at each of 12 places of the rider's standing
  loop followed by standing, and a stop of the walk, a turn and a charge
  each followed by standing.

What was seen on screen on the 19 361 frames of these 34 runs:

- **The animal's picture and its mirroring.** The rules above give the
  animal a picture on 17 221 frames. It was identified on screen on
  17 173 of them: it was the one given on 17 172, and on one frame the
  picture found was another one (a charge frame). On the other 48 frames
  (43 in charges, 5 in jumps) another sprite hid too much of it. The
  mirroring was the one given on all 17 173.
- **The rider's picture.** The rule gives it on 9 914 frames (standing,
  walking, turns, jumps, and charges pressed on a frame of standing,
  walking or turning; the first standing after the mount excluded), and it
  was the one given on all of them. The rider's mirroring was the one
  given on all 17 139 frames on which the rider was identified,
  including 5 418 frames of charges pressed in a jump, for which the
  picture is not covered (the rule for the other charges gave the picture
  on 4 421 of them and another on 997, "Not covered").
- **The rider's height d.** The rule gives d on 13 291 frames; the rider
  was drawn at that height below the animal on all of them.
- **Which comes next.** After 6 384 standing frames, 202 first riding
  frames and 1 132 frames on which a step of the walk ended, standing,
  walking or a turn followed as the rules in "Standing and walking" say,
  as for the first character.

24 further runs (19 349 frames) with the first character following next
to the animal and in view: the rider's picture was the one given on all 7
324 frames of the rules above identified on screen, and d the one given.
The animal's pictures were identified there on fewer frames, the first
character often hiding part of it.

## Examples

All values were measured on flat floor, with positions made relative.

From standing still, Right held from frame 0 (facing right):

| Frame | `vx` (1/256) | `x` |
|---|---|---|
| 0 | 16 | 0.0625 |
| 1 | 31 | 0.18359375 |
| 2 | 46 | 0.36328125 |
| 3 | 60 | 0.59765625 |
| 105 | 512 | 147.5546875 |

Speed 2 is first reached on frame 105, after 147.5546875 px. Releasing
everything at speed 2 (frame 0 the first frame with nothing held) gives
`vx` 496, 481, 466, 452 … (in 1/256), and `vx` is 0 from frame 105 on,
after 64.4453125 px. From standing still with
Right and Action held, `vx` is 28, 55, 81, 106 … and reaches 3.5 on
frame 123.

Turning at speed: riding right at `vx` 456/256 with Right held, then Left
held from frame 0. `vx` is 426, 397, 369, 342 on frames 0 to 3 (the
turn has no effect on speed); facing becomes left on frame 4; frame 8 is
riding again.

A riding jump while moving left at `vx` −422/256, Left held, Jump pressed
on frame 0 only:

| Frame | Part | `vx` (1/256) | `y` | `vy` |
|---|---|---|---|---|
| 0 | crouch (P) | −424 | 0 | 0 |
| 1 | crouch, steered | −435 | 0 | 0 |
| 2 | crouch | −444 | 0 | 0 |
| 4 | crouch | −459 | 0 | 0 |
| 5 | take-off (T) | −465 | 0 | −7 |
| 6 | air | −470 | −6.5625 | −6.5625 |
| 21 | air | −503 | −52.5 | 0 |
| 36 | landing frame (L) | −512 | 0 | 0 |
| 44 | riding | −512 | 0 | 0 |

On frame 0 the step is a 32nd of the gap (−90/32 → −2); from frame 1 an
8th (−88/8 = −11). On frame 24 the gap is −7, so the speed becomes −512.
Frame 35 ends 6.5625 px above the floor; frame 36 would end exactly on
it, so the rider lands.

A charge from standing still, Action pressed on frame 0, Right held from
frame 0 (facing left, so facing stays left through the charge):

| Frame | Part | `vx` (1/256) | `y` | `vy` |
|---|---|---|---|---|
| 0 | charge (C) | 28 | 0 | 0 |
| 1 | charge | 43 | 0 | 0 |
| 10 | hop take-off | 155 | 0 | −4 |
| 11 | charge, air | 166 | −3.5625 | −3.5625 |
| 26 | charge, landed | 292 | 0 | 0 |
| 28 | charge end (E) | 304 | 0 | 0 |
| 39 | charge end | 361 | 0 | 0 |
| 40 | settling frame; a turn starts | 365 | 0 | 0 |

Frame 0's target is +3.5 (Action held); from frame 1 it is +2. A turn
starts on frame 40, because Right is held and the rider faces left.

## Not covered

- Riding on slopes, at walls, and off ledges; near the left end of the
  measured floor (by a wall) the rider's floor was 1 px higher than the
  floor under the rider, which is not explained.
- What the animal does to enemies and what happens when the rider is
  hurt. Getting off the animal (a Tandem press) is in `getting-off.md`.
- Objects: carrying, the crate, bonuses and anything touched while
  riding, other than single collectibles (`collectibles.md`, "Riding the
  animal").
- Other animals, getting off and walls with the second character as the
  player ("The second character"), and those rules in ordinary play.
- With the second character as the player ("Pictures with the second
  character"): the rider's pictures, and the standing after them, for a
  charge pressed in a jump's crouch, in its air frames or on its landing
  frames (the rule for the other charges gave the picture on 4 421 of 5
  418 such frames); the first frames after the mount; slopes, walls and
  ledges, hurt or knocked off, and the animal hitting an enemy; and
  the animal's pictures with the first character in view, which hid
  part of it on many frames.
- The animal's picture on one charge frame (another picture than the given
  one was seen there; no cause found), after a
  charge pressed in a jump, on a jump that starts in the air after a
  hop, on slopes, at walls and ledges, while hurt or knocked off, and
  when the animal hits an enemy. In a charge pressed in a jump's crouch,
  in its air frames or on its landing frames, the animal's pictures are
  those of "Pictures", but the rider's depend on something not found:
  the rule for a charge from standing, walking or turning gave them on
  5 011 of 6 032 such frames. Whether the animal's standing loop is the same near the
  walls of the measured floor, with the second character present and
  with another animal.
- Trace state names for riding: `traces/VOCABULARY.md` has no names for
  the crouch, the charge or the charge end yet.
