# ACAT Statement Reader

A single-page tool for incoming ACAT transfers. Upload a client's statement from the delivering firm, and Claude lists every account on it with its number, registration, type and positions. The page then checks each value against the statement's own text, looks up the clearing firm's ACAT/DTC number, and flags anything that doesn't match or may not transfer. A person checks each account and copies the breakdown into ClientWorks.

Live page: https://claude.ai/artifact/9xnMsWPsfaK3anMkCpD1DU (Claude artifact, shared with the organization)

## How it works

- `index.html` is the whole app: markup, styles, script and the DTCC directory in one file.
- PDFs are read in the browser with pdf.js. Lines are rebuilt from item positions, including statements that set every letter as its own item. Pages with an invisible OCR layer, or with images and almost no text, count as scanned; "This page intentionally left blank" pages are skipped.
- Scanned pages are transcribed by Claude from page images (`PROMPT_TRANSCRIBE`), in batches of the per-call image limit, several batches at once.
- Claude reads the statement in two kinds of call:
  1. **Accounts** (`PROMPT_ACCOUNTS`): delivering firm, clearing firm (quoted from the statement, or marked self-clearing), any printed DTC number, and every account with its number and registration exactly as printed, type, program, value, page range and ACAT eligibility. Long statements are sent in parts and merged.
  2. **Positions** (`PROMPT_POSITIONS`): one call per account over that account's pages: every holding with symbol, CUSIP, quantity, price, value and any transfer issue (sweep, proprietary, alternative, annuity, not DTC eligible, foreign).
- Speed: the rules find account numbers and their pages before Claude answers, so position reads start at the same time as the account list (`guessAccounts`). An early read is used only if Claude's account and page range agree (`usable`); otherwise it is read again over Claude's range. Up to three calls run at once (`pool`), answers are cached for 24 hours, and accounts appear as soon as the list is back while positions fill in.
- Rules check (`ACAT.checkRecord`, no Claude): every account number, registration, value, symbol and quantity must appear in the statement's text; account numbers printed after an account label but missing from Claude's list are flagged; the registration's wording is compared with the account type; positions must add up to the account value; CUSIP check digits are validated; the clearing firm is compared with the statement's own clearing/carrying/custodian wording; masked account numbers, debit balances, fractional shares and non-ACAT accounts (annuities, insurance, planning services) are flagged.
- ACAT/DTC number, in order: printed on the statement, the team's clearing-firm table (`firms`), then the built-in `DTCC_DIRECTORY`: NSCC members marked as ACATS participants (as of Sept 11, 2026) and all DTC participants (as of Sept 30, 2026), from dtcc.com/support/nscc-directories and dtc-directories. Flags cover a table number that differs from DTCC, a firm with more than one ACATS number, and a DTC participant that isn't an ACATS participant.
- Self-check: each account needs its number, registration, type and positions ticked as checked, the firms ticked, and a resolution note on every flag before "Mark ready".
- Records are stored in the artifact's shared database (`transfers`), with the clearing-firm table in `firms` and its change history in `firmChanges`. Statement files and text are not saved.
- Account numbers show only their last 4 characters until someone clicks Show; Copy, "Copy for ClientWorks" and the CSV still give the full number.

## Access

- Access rules (published with the page): anyone who can open the page can read everything; writing needs Contributor access.
- Viewers can read statements with Claude, browse the team log and firm table, copy and download CSV. Save, Delete and changes to the firm table are disabled for them, each with a "Contributor access needed" flag, and a notice at the top explains it.

## Runtime

The page runs as a claude.ai artifact with the `sample` (Claude, with images), `db` (team log and firm table), `user` (names on records) and `downloads` (CSV) capabilities, declared in `artifact.json`. Opened as a plain local file it shows the page but can't read statements or load the log.

## Updating the live page

Edit `index.html`, then republish it to the artifact URL above from Claude Code with the capabilities in `artifact.json`. To refresh the DTCC lists, download the current NSCC member directory and DTC participant listing and rebuild the `DTCC_DIRECTORY` line.
