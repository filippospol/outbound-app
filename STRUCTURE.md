# Structure

```
Google Sheets (call logs)  →  GitHub Actions (clean + publish)  →  Shiny app (shinyapps.io)
```

The source worksheets are maintained by the Outbound team. Once a day (10:15, plus
on-demand via `workflow_dispatch`), the GitHub Action reads every campaign tab, cleans and
validates the rows, and commits the result back to this repo. The deployed Shiny app loads
the published bundle — it never reads Google Sheets live.

## What's in this repo

| Path | Role |
| --- | --- |
| `data/outbound-comms-analysis-clean-data.R` | Scraper/cleaner — runs inside the GitHub Action |
| `.github/workflows/outbound-comms-analysis-data.yml` | The daily pipeline (schedule + manual dispatch) |
| `data/outbound-clean-data.rds` | Published typed bundle the app loads (data + CQS exclusion list + build timestamp) |
| `data/outbound-clean-data.csv` | Published plain CSV, kept for other consumers |

## Endpoints

- **Data bundle (the app reads this):**
  `https://raw.githubusercontent.com/filippospol/outbound-app/refs/heads/main/data/outbound-clean-data.rds`
- **Trigger a pipeline run** (the app's Rebuild button, `POST`, needs a token with Actions write):
  `https://api.github.com/repos/filippospol/outbound-app/actions/workflows/outbound-comms-analysis-data.yml/dispatches`
- **Check run status:**
  `https://api.github.com/repos/filippospol/outbound-app/actions/workflows/outbound-comms-analysis-data.yml/runs`

## Pipeline details

- Every worksheet tab is read in chunks; tabs that aren't campaign data are skipped
  automatically, so new campaign tabs are picked up with no code change.
- A data-quality guard scans tight-domain columns (`Email Ban`, `Agent's Gender`,
  `Response`, …) per source tab and reports out-of-domain values on the run's summary
  page — a pasted-in block with shifted columns is named the next morning instead of
  being discovered months later. It reports only; it never alters data or fails the build.
- Each run writes headline numbers (rows, campaigns, months, latest call) to the
  GitHub Actions run summary.
- `raw.githubusercontent.com` serves through a CDN with `max-age=300`, so the app can
  lag a fresh publish by up to ~5 minutes.

## The Shiny app

The dashboard itself is deployed on shinyapps.io. It loads the `.rds` bundle above at
startup, shows a "Data last updated" stamp from the bundle's build timestamp, and has a
Rebuild button that fires the workflow via the dispatch endpoint (token read server-side
from an environment variable or a local `gh_token.txt`, which is gitignored and must
never be committed).
