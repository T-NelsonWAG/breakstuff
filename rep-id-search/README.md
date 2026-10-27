# Rep ID Search

A single-page lookup for Winthrop's LPL / IFA Rep IDs. Type a Rep ID to see its advisors, primary point of contact, branch, OSJ, manager ID and entity, or type an advisor's name or a branch to see every ID they're on. Answers come only from the team's list, so a typo never returns a near match; IDs one character away are offered as "Did you mean".

Live page: https://claude.ai/artifact/GYQpQLpye6LxArj59zd1dW (Claude artifact, shared with the organization)

## How it works

- `index.html` is the whole app: markup, styles and script in one file.
- The list lives in the artifact's shared database: `reps/<REPID>` (one document per Rep ID, retired IDs kept with `status: "retired"`), `meta/list` (the list date and nicknames) and `changes` (history of every add, edit, retire and import).
- Offices and their manager IDs are fixed in `GROUPS` (WW and FPS 36HK, UWM K43N, Keel 826X); the OSJ is 50Y8.
- Search matches Rep IDs exactly, then advisor names (including nicknames from `meta/list`) by word prefix, then branch and manager IDs. Lookalike IDs use `CONFUSABLE` character swaps (O/0, I/1, S/5…) and one-character edits.
- "Check a new IFA Rep ID list" reads the "IFA REP IDs ACTIVE" PDF (pdf.js) or pasted text, and shows what's new, changed and missing before anything is saved; "Apply ticked changes" writes only the ticked rows.
- "Ask a question in plain English" sends the list to Claude, which answers only from it.
- CSV export (downloads capability) writes one row per active Rep ID with a plain-sentence column.

## Access

- Access rules: default (anyone who can open the page reads the list; writing needs Contributor access).
- Viewers can search, browse by office, see recent changes, ask Claude, compare an IFA list and export CSV. Adding, editing, retiring, applying list changes and saving the list date or nicknames are disabled for them, each with a "Contributor access needed" flag, and a notice at the top explains it.

## Runtime

The page runs as a claude.ai artifact with the `db`, `sample`, `user` and `downloads` capabilities, declared in `artifact.json`.

## Updating the live page

Edit `index.html`, then republish it to the artifact URL above from Claude Code with the capabilities in `artifact.json`.
