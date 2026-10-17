# Noise vectors

Synthetic vectors for "Noise" in `../../formats/voice-output.md`. The
samples and cases are made up; none of them come from a game. Written by
the research side's generator with the weights of `voice-output.md`
("Blending"), and every case also run on an emulation of the console's
sound chip, which uses its own weights: only `mixed`, the one case with
a voice playing its sample, differs from these, by at most 19, and with
the console's weights in place of the formula every output matched.
Values are only handed where it makes no difference on which of the
next three outputs they count (`voice-output.md`, "When handed values
count").

## Input

`cases.tsv`: one case per row, tab-separated. The columns are those of
`../voice-output/cases.tsv` with one more:

| Column | Meaning |
|---|---|
| `case` | name |
| `first_count` | the shared count `c` (`voices.md`) on the case's first output (output 0) |
| `first_noise` | the noise register `R` on output 0 |
| `outputs` | how many outputs the case runs |
| `samples` | as in `../voice-output/` |
| `events` | as in `../voice-output/`, with the noise events below |

Noise events (numbers decimal):

- `k noise rate q`: the noise rate becomes `q`.
- `k vN noise on` / `k vN noise off`: noise on or off for voice `N`.

Before output 0: everything as in `../voice-output/`, the noise rate 0
and noise off for every voice; the echo volume is 0 throughout. Events
at output 0 already count on output 0, so a step due on output 0 at the
rate handed there is taken. A `main` event may repeat the main volume
already handed; it changes nothing.

What the cases exercise:

- `rate-31`: the example in `voice-output.md`, "Noise": rate 31, a step
  on every output.
- `rate-31-fixed`: the same with a fixed level (gain) and volumes −128,
  so the noise values show in the outputs.
- `slow-rates`: rate 20 (period 32), then rate 0 from output 4 003 (the
  register keeps its value), then rate 11 (period 192, so `q` = 496)
  from output 6 003.
- `pitch-ignored`: two voices with noise on, at different pitches, one
  on the left and one on the right: left and right are the same.
- `one-shot-end`: noise on a one-shot sample of 7 blocks: the note
  still ends when its position reaches 84.
- `mixed`: a voice playing its sample and a voice whose noise is turned
  off between two notes and on again before a third.

## Expected output

`outputs.tsv`: tab-separated `case`, `output`, `left`, `right`, one row
for every output of every case.
