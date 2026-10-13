# Titles

The original game is a series of titles. Behavior files and reference
traces say which title they were measured on, with an engine-neutral
**title id**, so the engine can compare a scenario against the reference
that matches the preset it ran.

## Ids

A title id is `title-N`, where `N` is the title's position in the series
in order of first release, counting from 1: `title-1` is the first title,
`title-2` the second, and so on. Ids are never renamed or reused.

## Releases

Each measured title was measured on one release, which runs at 60 Hz.
Differences between releases of the same title (revisions, regions) are
not covered. If a difference is found, the spec will add a way to name
the release; until then, a title id stands for that one release.

## What has been measured

| Id | Behavior files | Reference traces |
|---|---|---|
| `title-1` | All files in `behavior/` | All traces in `traces/reference/` |
| `title-2` | `ground-speed.md`, `jump-arc.md`, `jump-input.md`, `air-control.md`, `roll-input.md`, `roll-speed.md`, `bounce.md`, `walk-off.md`, `rolling-off-ledges.md`, `slopes.md` (sections "In `title-2`") | None |

`title-3` hasn't been measured yet.

## Where ids appear

- Reference traces: the required `# title:` header line
  (`traces/FORMAT.md`).
- Behavior files: a `Titles:` line directly under the file's heading
  (`behavior/README.md`).
