# ACAT Transfer Assistant (prototype)

The first ACAT prototype, kept for reference. It has been replaced by the [ACAT Statement Reader](../acat-statement-reader/), which reads statements per account with Claude and checks the results against the statement.

Live page: https://claude.ai/artifact/VG32NWvCNwVpZuN1We62Qb (Claude artifact, private)

## How it works

- `index.html` is the whole app. Text PDFs are read in the browser with pdf.js; scanned PDFs can be transcribed by Claude ("Read with Claude").
- Delivering firm, account numbers and account type are found by keyword and pattern matching over the whole statement text. This is why it was replaced: a fund name in the holdings (for example "Fidelity Magellan") can be taken as the delivering firm, account numbers printed with spaces are missed, and a multi-account statement gets one guessed account type.
- The clearing-firm reference table is kept in each person's own browser (`localStorage`).
- Records are not saved; export a CSV (downloads capability) or copy the summaries before closing.

## Runtime

Runs as a claude.ai artifact with the `sample` (Claude, with images) and `downloads` capabilities, declared in `artifact.json`.
