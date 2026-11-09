# wrapup

A Claude Code skill I run at the end of a session. It looks back at what
happened, picks out the few things the next session should know (a
correction I made, a trap that cost time, a decision and its reason), and
writes each one where the next agent will actually read it: a test, a
comment, a topic notes file, or memory.

It edits and deletes old notes as much as it adds new ones, and it keeps
`CLAUDE.md` a short map that points to topic files instead of a pile of
every lesson ever learned. Most sessions it writes nothing, which is fine.

## Install

```bash
git clone https://github.com/tilb/wrapup ~/.claude/skills/wrapup
```

Then type `/wrapup` (or say "wrap up") at the end of a session.
Update later with `git -C ~/.claude/skills/wrapup pull`.

## Notes

- Step 1b reads the learnings log of [gstack](https://github.com/garrytan/gstack)
  if you use it. Without gstack that step finds nothing and is skipped.
- It never commits or pushes. Changes to checked-in files are left for you
  to review.

## License

MIT
