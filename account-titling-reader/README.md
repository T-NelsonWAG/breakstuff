# Account Titling Reader

A single-page tool for account opening. Upload a custodian statement or a trust, estate, POA or entity document, and Claude extracts the registration and titling, the parties and the beneficiaries. A person checks every field, then saves the record to a shared team log.

Live page: https://claude.ai/artifact/EzKQp67CxT81Gy7CAuCVdo (private Claude artifact)

## How it works

- `index.html` is the whole app: markup, styles and script in one file.
- PDFs are read in the browser with pdf.js. Text pages go to Claude as text, and scanned pages go as page images. Word files are read with mammoth.
- Claude reads the document in up to three passes:
  1. **First read** (`PROMPT`): extracts the fields as JSON.
  2. **Double-check** (`VERIFY_INTRO`, optional): checks every value against the document and lists its corrections.
  3. **Handwriting close-up** (`ZOOM_INTRO`): reads high-resolution crops of handwritten fill-ins and fixes the fields that depend on them.
- Scans longer than one request's image limit are transcribed in batches first, word for word, then read as text. Each page is drawn just before its batch is sent, at the ~1.2 MP Claude actually sees.
- Uploads are fingerprinted (SHA-256, `source.fileHash`). Uploading a PDF that matches a saved record with kept text and no unread pages reuses that text instead of transcribing again; "Read this file from scratch instead" forces a fresh read.
- Handwriting close-ups are cut while the double-check runs, and are recut only if the double-check changes the handwritten spots.
- Records are stored in the artifact's shared database (`extractions` collection). Uploaded files are not saved.
- Review: every saved record starts as **Draft**. "Mark reviewed" records who reviewed it and when (`review: {status, by, at}`); the reviewer must be someone other than the person who saved or last edited it, and a record with flags needs Team notes first. Any later edit returns it to Draft.
- Flags can't be removed from a record. A code-level check (`nameSpellingFlags`) adds a flag whenever the same surname appears with first names one or two letters apart, across the extracted names and the document text.
- For text-only reads (text PDFs, Word, pasted text, batch-transcribed scans), the text Claude read is kept in `sources/<record id>` (split into `parts/<n>` under the 256 KiB document cap). The Re-read button on a saved record runs the extraction again from it, keeping team notes and the SharePoint link. Deleting a record deletes its saved text.

## Runtime

The page runs as a claude.ai artifact with the `sample` (Claude), `db` (team log) and `user` (names on records) capabilities. Opened as a plain local file, it can only show the manual-entry form.

## Updating the live page

Edit `index.html`, then republish it to the artifact URL above from Claude Code.
