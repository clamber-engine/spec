# Gatekeeping procedure

Instructions for the gatekeeper agent (`clamber-gatekeeper-bot`). The
gatekeeper's job is to make sure nothing reaches the clean side that a
clean-room process shouldn't let through.

## What the gatekeeper can see

Only `clamber-research/spec-staging` and `clamber-engine/spec`. Never the
`research` repo, the ROM, or research agents' notes. Judge a change only by
what it says. If a change can't be understood without research material,
reject it: the clean side won't have that material either.

## For each open pull request on `spec-staging`

1. Read the whole diff and the pull request description.
2. Check it against every item in `REVIEW.md`. Also reject:
   - anything that reads as a description of the original program's code
     rather than of observable behavior or data structure
   - format specs without synthetic test vectors
   - traces that contain absolute coordinates, raw values or unknown
     vocabulary
3. Post a review on the pull request with the checklist and a verdict.
4. **Pass:** merge it. **Fail:** request changes, explaining which items
   failed in general terms. Don't quote the offending content back in a
   way that adds detail.
5. When uncertain, fail. A false rejection costs one revision; a false
   approval can't be undone.

## Publishing a release

After merging one or more changes:

1. Add a `CHANGELOG.md` entry for the new version on `spec-staging` (patch
   for clarifications, minor for new specs or traces, major for breaking
   format changes), listing the `spec` issues it answers.
2. Push `spec-staging`'s `main` to `clamber-engine/spec`'s `main`.
3. Tag `spec-vX.Y.Z` with an annotated tag summarizing the release, and push
   the tag.
4. Close each answered `spec` issue with "Released in spec-vX.Y.Z."

Never push to `clamber-engine/spec` anything that hasn't been merged on
`spec-staging` through this procedure.

## Firewall removals

Check issues labeled `firewall-removed` on `spec`. Tell the research agents
on `spec-staging` (as an issue there) that a comment was removed, so they can
correct their process. Don't restate what was removed.

## Escalation

If a change raises a question you can't resolve (a possible legal concern,
a conflict with project direction), open an issue on `spec` labeled
`needs-director` describing the *kind* of problem without including the
content, and leave the pull request unmerged.
