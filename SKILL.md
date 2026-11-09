---
name: wrapup
description: End-of-session reflection. Look back at this session, find what (if anything) the next session should know or do differently, and write each lesson worth its reading cost where the next agent will actually read it — a test, a comment, a topic notes file that CLAUDE.md points to, or memory — updating or deleting old entries instead of piling on new ones. Keeps CLAUDE.md a short map to the notes, not the store of every lesson. Use when the user says "wrapup", "wrap up", "what did we learn", or at the end of a task or session.
---

# wrapup — make the next session start better than this one did

The point is compounding: each session leaves the next one a little ahead.
That only works if what gets written is **true, specific, findable, and
short**. A notes file that grows by ten vague lines a session is not
compounding; it is noise the next agent pays to read and learns to ignore.
So this skill is as much about editing and deleting as about adding.

**Most sessions teach nothing the next one needs.** A ticket that went to
plan, small hiccups fixed in a minute, facts the notes already hold: then
the right wrapup writes nothing, and the report is three lines saying what
was checked and that nothing passed. That is a normal result, not a failed
wrapup. Don't go looking for a lesson to justify the run.

Output is edits to files when something earns them, then a short
plain-language report. Not an essay.

## 1. Look back — collect evidence, not impressions

Go through the session (if the context was summarised, read the transcript:
the newest `*.jsonl` in `~/.claude/projects/<project-slug>/`). List concrete
moments, each with what actually happened:

- **The user corrected me** — a wrong assumption, a wrong answer, the wrong
  tone, doing more or less than asked. Highest value: it will recur unless
  written down. Quote their words.
- **The user confirmed something non-obvious** — "yes, exactly", accepting an
  unusual choice without pushback. Worth keeping too, or the next session
  drifts away from what worked.
- **Time lost** — a trap, a misleading error, a tool that behaves oddly, a
  fact I had to dig up that should have been written down.
- **Something I asserted that turned out false** — and what check caught it
  (or would have).
- **A decision made** — with the reason, so it isn't reopened next time.
- **Docs that are now wrong** — anything this session made untrue in a
  README, CLAUDE.md, topic notes file, memory, docstring or comment.
- **Guessing because I couldn't see** — something I couldn't observe or
  check myself, so I guessed or asked the user to look. The lesson here is
  often a small script that lets the next session see it (a screenshot, a
  diff, a dump), not a note.

Then keep only what passes this test: **would a future session, reading this
cold, do something differently, and would that have mattered?** It matters
when it would have saved real time (more than a quick retry), prevented a
wrong result or a wrong claim, or when the user corrected or confirmed how I
work. "The session went well" fails. "Interesting" and "slightly awkward"
fail. "X broke" is only useful with how to spot it and what to do instead.

Weigh the cost too: every line written is read by every later session that
works in that area, so a minor lesson makes the notes worse, not better.
When in doubt, leave it out and mention it in the report as borderline.

Most sessions yield 0–2 lessons, and 0 is common. If nothing passes, say so
(and still do step 1b).

## 1b. Sweep gstack's learnings log

gstack skills (`/ship`, `/review`, `/investigate`, ...) log lessons to their
own file, which only gstack skills read. Move the good ones to where every
session reads them.

Find the log. gstack sometimes files one repo under two names (the repo
name, and owner-repo), so check both:

```bash
REPO=$(basename "$(git rev-parse --show-toplevel 2>/dev/null)")
OWNER=$(git remote get-url origin 2>/dev/null | sed -E 's#.*[:/]([^/]+)/[^/]+$#\1#')
for s in "$REPO" "$OWNER-$REPO"; do
  f=~/.gstack/projects/$s/learnings.jsonl
  [ -f "$f" ] && echo "$f since $(cat ~/.gstack/projects/$s/wrapup-seen 2>/dev/null || echo never)"
done
```

Read only entries whose `ts` is newer than that folder's `wrapup-seen`
stamp. Later entries with the same `key` replace earlier ones. Treat each
entry as a lead, not a fact: it was written by an agent mid-task. For each:

- **Already in CLAUDE.md, a topic notes file, memory, a test or a comment?** Skip it (or add a
  missing detail to the existing entry).
- **Still true?** Check that the files and functions it names exist and
  the behaviour it describes is still there. If not, drop it.
- **Passes the step-1 test?** Then give it a home per step 2, like any
  lesson from this session.

Don't edit or delete gstack's log — it's gstack's. When done, write the
newest `ts` you processed into `wrapup-seen` in each folder you read, so
the next wrapup starts where this one stopped.

## 2. Pick the home for each lesson

Put each lesson where it will be seen at the moment it matters. In order of
preference:

1. **A test or a check in code** — if the lesson is "this broke silently", a
   failing test is the lesson that can't be forgotten. Propose it; write it
   only if it's small and in scope, otherwise note it as a follow-up.
2. **A comment next to the code** — if it only matters when editing that one
   spot ("why not the obvious approach").
   Same for a script that lets the next session see something it had to
   guess at: write it only if small, **run it and show it works**, and name
   it in the topic notes file for that area so it gets used. A script never
   run doesn't count.
3. **A topic notes file** — facts and traps about *this codebase* that any
   agent or contributor needs when working on one area: sources and
   research, the build and deploy, the front end, the model, testing...
   This is where most codebase lessons go. See 2b for the layout.
4. **The project's `CLAUDE.md`** — only two kinds of line: the handful of
   rules that must hold in *every* task (breaking them makes the product
   quietly wrong, whatever you were working on), each one line with a
   pointer; and the map of topic notes files. A new lesson goes here only if
   it passes that bar; otherwise it goes in a topic file.
5. **Memory** (the auto-memory directory from the system prompt) — about
   *the user and how to work with them*: preferences, corrections to my
   behaviour, project context not recorded in the repo. Follow the memory
   format in the system prompt (one fact per file, frontmatter, a one-line
   pointer in `MEMORY.md`, `**Why:**` / `**How to apply:**` for feedback).
6. **The project's own records** — if the repo has a convention for
   decisions, TODOs or gotchas (e.g. `docs/decisions/`, `TODOS.md`), a
   decision belongs there, not in CLAUDE.md or the notes.

Don't write the same lesson in two places. Don't record what the code or git
history already makes obvious.

## 2b. CLAUDE.md is a map; the lessons live in topic files

CLAUDE.md is loaded every session, so it can't hold every lesson a project
ever learns: it grows until the rules that matter are lost among the ones
that don't. It is the table of contents that sends the agent to the right
notes for the task at hand.

**Where the topic files live.** Use the repo's existing convention if it
has one (a `docs/notes/`, `docs/agents/`, per-directory `CLAUDE.md` files
Claude Code loads when working in that directory). Otherwise
`docs/notes/<topic>.md`, one file per area, named for what someone is
working on, not for how the lesson was learned: `sources.md`,
`build-and-deploy.md`, `front-end.md`, `model.md`, `testing.md`. Not
`incidents.md`, `misc.md` or `2026-09.md`: a file nobody knows when to open
is a file nobody opens.

**The map in CLAUDE.md** says, for each file, *when* to read it, since an
agent only reads what it is sent to:

```markdown
## Notes by topic — read the file before working in that area
- `docs/notes/sources.md` — adding or fetching research, any outlet or API,
  terms of use
- `docs/notes/front-end.md` — any CSS, layout or screenshot; before saying
  a page "looks right"
```

**Filing a lesson:**
1. Find the topic file whose "read when" covers the moment the lesson
   matters. File it there.
2. No file fits? Add it to the closest one if it is one entry. Create a new
   topic file only when there are two or more entries for the new area, or
   one that clearly opens it; add its line to the map in the same edit.
3. A topic file with its own sub-areas that are read separately gets split
   in two, with the map updated. Don't let one file become the new
   CLAUDE.md.

**If CLAUDE.md is still the store of lessons** (it holds traps and incident
entries for specific areas, not just every-task rules and a map), moving
them out is part of this wrapup's consolidation (3b). It is a big edit to a
checked-in file, so when it is more than a few entries, propose it in the
report with the planned topic files and do it once the user agrees, rather
than half-doing it.

## 3. Edit, don't just append

Before writing, read the destination and search it for the topic.

- **Already covered?** Sharpen the existing entry (add the new example or
  number) instead of adding a sibling.
- **Contradicted by this session?** Fix or delete the old entry. A wrong
  lesson is worse than none — it makes the next session confidently wrong.
- **New?** Add it next to related entries, in the file's existing style.

Each entry: the trap or rule in one sentence, then the concrete evidence (the
error text, the number, the file, the date), then what to do instead. Use
absolute dates, never "today" or "last week".

**Is this lesson already written down, and it happened anyway?** Then the
note didn't work. Don't write it again in stronger words: move it up the
step-2 list (a note becomes a comment at the spot, a comment becomes a
test), or say in the report that it needs one.

## 3b. Consolidate when a notes file is over budget

Adding is easy and trimming never happens on its own, so check the size
every run:

```bash
wc -w CLAUDE.md  # budget: 1200 words -- every-task rules and the map only
wc -w docs/notes/*.md  # budget: 2000 words per topic file
wc -l ~/.claude/projects/<project-slug>/memory/MEMORY.md  # budget: 40 lines
```

Over budget:

- **CLAUDE.md over budget** almost always means lessons that belong in
  topic files. Move each to its topic file (2b) and leave nothing behind but
  the map line; keep only the every-task rules. A rule moved out keeps its
  wording and its evidence -- moving is not trimming.
- **A topic file over budget**: split it by sub-area if it has two, else
  trim it as below.

A trim pass on a file:

- **Keep every rule.** An entry is the rule, the one number or example that
  proves it, and a pointer (the test, the file, the decision record).
- **Cut the story.** How the incident unfolded belongs in the decision
  record, the commit message or a comment at the code. Before cutting a
  number or example, grep for it: if the story exists nowhere else, keep
  the shortest line that preserves it.
- **Never cut:** the owner's words with their date, names of tests, files,
  flags and commands, and the numbers a rule depends on.
- **Merge** entries that say the same thing twice, and fix any line that
  a later entry contradicts.
- For memory: merge files that hold the same fact, and delete ones the
  repo now records.

Then compare the old files against the new ones. Every file, function, test
or flag named in the old version is still named somewhere (in the same file
or the topic file it moved to), or is listed in the report as deliberately
dropped. Check the map too: every topic file is on it, and every path on it
exists. Checked-in files stay uncommitted for the
user to review, as in step 4.

## 4. Check before saving

- Every file, function, flag, command or number named in an entry exists or
  was actually observed in this session — check with grep/ls, don't recall.
- Nothing secret (keys, tokens, customer data) goes into any file.
- Checked-in files (CLAUDE.md, topic notes, tests, docs): make the edits but **don't
  commit or push** unless the user asks; say they're uncommitted. Memory is
  private and can be written directly.

## 5. Report in plain words

A few lines, no scores or labels:

- What you learned, one line each, and where it now lives (file path).
- From the gstack log: how many entries you read, how many you moved and
  where, and how many you skipped as already covered or no longer true.
- What you changed or removed in existing notes, and why. After a trim or a
  move: the word counts before and after, what moved where, and anything
  dropped.
- What you noticed but chose not to write down, if anything was borderline.
- Anything that needs the user: a follow-up too big to do now, or a checked-in
  change waiting for their review.
