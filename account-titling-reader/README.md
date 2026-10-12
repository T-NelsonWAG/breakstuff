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
- Records are stored in the artifact's shared database (`extractions` collection). Uploaded files are not saved.

## Runtime

The page runs as a claude.ai artifact with the `sample` (Claude), `db` (team log) and `user` (names on records) capabilities. Opened as a plain local file, it can only show the manual-entry form.

## Updating the live page

Edit `index.html`, then republish it to the artifact URL above from Claude Code.
