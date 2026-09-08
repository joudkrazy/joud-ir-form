# Joud / Crave Incident Report Form

A single-page, fillable incident report. Static HTML, no backend, no database.

Fill it in the browser, then **Print / Save PDF**. Nothing is stored server-side; closing
the tab discards the entry. The employee lookup reads a CSV you pick from your own machine,
in your own browser. That file is never uploaded.

## Files

| File | Purpose |
|---|---|
| `index.html` | The whole form: markup, styles, and the CSV lookup script |
| `crave-logo.png` | Header logo, left |
| `joud-logo.png` | Header logo, right |
| `Dockerfile` | nginx serving the three files above |
| `nginx.conf` | Listens on 8080, exposes `/health` |

## Local use

Open `index.html` in any browser. No build step, no server needed.

## Deployment (Coolify)

Build pack **Dockerfile**. The container serves plain HTTP on **8080** behind the platform's
TLS proxy, so there is no HTTPS redirect in `nginx.conf` — adding one causes a redirect loop.

The build takes no environment variables and must stay that way.

Health check path: `/health` → `200 ok`.

Push to `main` rebuilds the app once the deploy webhook is wired.

## Employee CSV

The lookup matches these column headings, case- and punctuation-insensitive:

- Name: `Name`, `Employee Name`, `EmpName`
- ID: `EmpNo`, `Employee Number`, `Id`, `Code` (a bare number also matches a `CR`-prefixed ID)
- `Designation` / `Role`, `Department`, `Location` / `Branch` / `Outlet`
- `DOJ` / `Date of Joining` — used to compute Years of Service
