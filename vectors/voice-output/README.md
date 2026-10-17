# Voice output vectors

Synthetic vectors for `../../formats/voice-output.md`. The samples and
cases are made up; none of them come from a game. Written by the research
side's generator with the weights of `voice-output.md` ("Blending"), and
every case also run on an emulation of the console's sound chip, which
uses its own weights: its outputs differ from these by at most 240 (in
`mix-limits`; at most 130 in the other cases), and with the console's
weights in place of the formula every output matched. Values are only
handed where it makes no difference on which of the next three outputs
they count (`voice-output.md`, "When handed values count").

## Input

`cases.tsv`: one case per row, tab-separated:

| Column | Meaning |
|---|---|
| `case` | name |
| `first_count` | the shared count `c` (`voices.md`) on the case's first output (output 0) |
| `outputs` | how many outputs the case runs |
| `samples` | `;`-separated samples, numbered from 0, each `blocks/loop`: the sample's blocks as hexadecimal bytes (9 bytes a block, `sound.md`), and its loop block |
| `events` | `;`-separated, each handed before output `k` (so it counts from output `k` on) |

Events (numbers decimal):

- `k main l r`: main volume left `l`, right `r` (bytes).
- `k vN pitch p`: voice `N` (0 to 7) is handed pitch `p`.
- `k vN vol l r`: voice `N`'s left and right volume (bytes).
- `k vN sample s`: voice `N` is handed sample `s`.
- `k vN env e1 e2 g`: envelope settings and gain (`voices.md`).
- `k vN start`: a start (taken at the next check, `voices.md`); it takes
  back a release still handed.
- `k vN release`: a release; it stays handed until the next start.

Before output 0 every voice has never started and all its values are 0;
the main volume is 0. The echo volume is 0 throughout.

What the cases exercise:

- `unit-pitch`: the worked example in `voice-output.md`.
- `negated`: volume and main volume −128 (both negate exactly).
- `slow-pitch`: pitch 300, so the fraction takes every value (every
  weight); a fixed level.
- `high-pitch`: pitch 31 744 on the left voice and 15 360 on the right
  one: left and right are the same.
- `one-shot-end`: a one-shot sample of 6 blocks: the note ends when its
  position reaches 68.
- `loop-filters`: a loop at block 2 with filters; the loop passes carry
  the decoding on.
- `restart-sounding`: a start while a note sounds.
- `sample-change-loop`: another sample handed during a note; after the
  note's last block it goes on from that sample's loop block.
- `mix-limits`: eight loud voices: limited sums, and main volume −128 on
  a sum of −32 768.
- `end-in-release`: a one-shot sample ending while the voice is
  released.
- `envelope-delay`: an attack and decay: each output uses the level of
  two outputs before.

## Expected output

`outputs.tsv`: tab-separated `case`, `output`, `left`, `right`, one row
for every output of every case.
