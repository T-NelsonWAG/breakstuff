# Winthrop team tools

Source for the team's Claude artifact pages. Each folder holds one page: `index.html` (the whole page, as served), `README.md` (how it works) and `artifact.json` (its live URL and the capabilities to publish it with).

| Folder | Page | Live |
|---|---|---|
| `team-tools/` | Winthrop Team Tools (hub) | https://claude.ai/artifact/4uMuNaHSU17UCzxrNZUY6p |
| `account-titling-reader/` | Account Titling Reader | https://claude.ai/artifact/EzKQp67CxT81Gy7CAuCVdo |
| `rep-id-search/` | Rep ID Search | https://claude.ai/artifact/GYQpQLpye6LxArj59zd1dW |
| `acat-statement-reader/` | ACAT Statement Reader | https://claude.ai/artifact/9xnMsWPsfaK3anMkCpD1DU |
| `acat-transfer-assistant-prototype/` | ACAT Transfer Assistant (old prototype) | https://claude.ai/artifact/VG32NWvCNwVpZuN1We62Qb |

The pages' shared data (team logs, the Rep ID list, the clearing-firm table) lives in each artifact's database, not in this repo.

To change a page, edit its `index.html` and republish it to its URL from Claude Code with the capabilities in its `artifact.json`. If a page was changed on claude.ai first, pull the live version into the folder before editing.
