# Account Titling Reader

A single-page tool for account opening. Upload a custodian statement or a trust, estate, POA or entity document, and Claude extracts the registration and titling, the parties and the beneficiaries. A person checks every field, then saves the record to a shared team log.

Live page: https://claude.ai/artifact/EzKQp67CxT81Gy7CAuCVdo (private Claude artifact)

## How it works

- `index.html` is the whole app: markup, styles and script in one file.
- PDFs are read in the browser with pdf.js. Text pages go to Claude as text, and scanned pages go as page images. Word files are read with mammoth.
- Claude reads the document in up to three passes:
  1. **First read** (`PROMPT`): extracts the fields as JSON.
  2. **Double-check** (`VERIFY_INTRO`, optional): checks every value against the document and lists its corrections.
  3. **Handwriting close-up** (`ZOOM_INTRO`): reads high-resolution crops of every handwritten fill-in a field depends on, in batches of the per-call image limit, one batch after another, and fixes the fields that depend on them. Routine per-page footer initials are not included.
- Scans longer than one request's image limit are transcribed in batches first, word for word, then read as text. Each page is drawn just before its batch is sent, at the ~1.2 MP Claude actually sees.
- Uploads are fingerprinted (SHA-256, `source.fileHash`). Uploading a PDF that matches a saved record with kept text and no unread pages reuses that text instead of transcribing again; "Read this file from scratch instead" forces a fresh read.
- Handwriting close-ups are cut while the double-check runs, and are recut only if the double-check changes the handwritten spots.
- Records are stored in the artifact's shared database (`extractions` collection). Uploaded files are not saved.
- Review: every saved record starts as **Draft**. "Mark reviewed" records who reviewed it and when (`review: {status, by, at}`); the reviewer must be someone other than the person who saved or last edited it. Any later edit returns it to Draft, and history notes when a review was cleared.
- Marking reviewed also requires: account title, registration type, tax ID type and number, signing authority, at least one named party with a role, trust name/date/governing state for trust registrations, each beneficiary level adding to 100%, a SharePoint link to the document, and a resolution note on every flag.
- Saves are safe against two people editing at once: each save takes a short lease on the record, re-reads it, and refuses (naming who) if it changed since it was opened. Every save, creation, review and deletion is logged in `extractions/<id>/history` with field-by-field changes, including flags and their resolution notes; tax IDs and SSN/EIN-shaped numbers are masked there.
- Only the page owner or an Editor sees "Delete record" (page-level only: the database rules can't tell a delete from a save). Deleting removes the saved document text first (stopping if that fails), then the record. The change history is kept with a "Deleted" entry (`KEEP_HISTORY_ON_DELETE`), and the deletion is logged in `deletions`.
- Tax ID fields, and any SSN- or EIN-shaped number in other fields, notes, flags, "Good to know" items, double-check corrections and history, show only the last 4 digits. "Show" reveals one field for the open record. Display only: full numbers are still stored. Claude is told never to repeat full tax IDs outside the tax ID fields.
- Page references cite the PDF page and, when different, the printed one: "p.28 (printed 27)".
- All flags are kept, including through Re-read (earlier flags and their resolution notes carry forward). The editor shows the first 4 with "Show N more flags"; the log shows each record's flag count and unresolved count, and can be filtered to Draft, Reviewed, or records with unresolved flags.
- A code-level check (`nameSpellingFlags`) flags: the same surname with first names one or two letters apart; common nicknames under one surname (Bill / William); and the same first name with surnames one or two letters apart (Doe / Dow), across the extracted names and the document text. Pairs with different middle initials or known, different birth dates are skipped.
- Controls are enforced by the page only. Give Contributor access only to the people who process accounts.
- For text-only reads (text PDFs, Word, pasted text, batch-transcribed scans), the text Claude read is kept in `sources/<record id>` (split into `parts/<n>` under the 256 KiB document cap). The Re-read button on a saved record runs the extraction again from it, keeping team notes and the SharePoint link. Deleting a record deletes its saved text.

## Runtime

The page runs as a claude.ai artifact with the `sample` (Claude), `db` (team log) and `user` (names on records) capabilities. Opened as a plain local file, it can only show the manual-entry form.

## Updating the live page

Edit `index.html`, then republish it to the artifact URL above from Claude Code.
