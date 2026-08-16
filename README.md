# ATSIP — conference website

Static site built with [Hugo](https://gohugo.io) (extended, v0.128+), deployed to
GitHub Pages. No database, no server-side code, no build dependencies beyond a
single binary.

## Structure

```
data/editions.yaml     Single source of truth for every edition
content/               Page content, one folder per edition year
layouts/               Templates
assets/css/main.css    Styles
.github/workflows/     Automatic deployment on push to main
```

## The one file that matters

`data/editions.yaml` drives the whole site: the homepage, the timeline, the
editions table, and the archive banners. Changing `current:` from `2027` to
`2029` flips every page at once — past editions get their banner, the homepage
points at the new one. Nothing else needs editing.

Metadata follows DBLP (<https://dblp.org/db/conf/atsip/index.html>), which
mirrors the published IEEE proceedings. Where the old `lab-atms.com` pages
disagree — Sfax for 2020, Moncton for 2022 — DBLP is authoritative.

## Local preview

```bash
hugo server -D
# http://localhost:1313
```

## Deployment

1. Create a GitHub **organisation** (`atsip-conference`), not a personal account.
   Add at least three owners from the steering committee.
2. Create a public repository and push this project to `main`.
3. Settings → Pages → Source: **GitHub Actions**.
4. Push. The workflow builds and deploys automatically.
5. Once the domain is registered: Settings → Pages → Custom domain, and update
   `baseURL` in `hugo.toml`.

## Adding the next edition

```yaml
current: 2029

editions:
  - num: 9
    year: 2029
    city: ...
    status: upcoming
```

Then `mkdir content/2029` with an `_index.md` carrying `cascade: {edition: 2029}`.
The previous edition freezes on its own.

## Conventions

- **Never** republish `submission`, `registration` or `important-dates` pages for
  past editions. They advertise dead deadlines and mislead authors. Archived
  editions keep only: metadata, committees, programme, recovered documents.
- Heavy PDFs (plenary slides, proceedings extracts) go to Zenodo for a permanent
  DOI, not into this repository — Git history keeps binaries forever.
- The canonical name is *International Conference on Advanced Technologies for
  Signal and Image Processing*. Use it without variation.

## To do

- [ ] Register the domain (10 years, owned by the ATSI association)
- [ ] IEEE technical sponsorship application — critical path
- [ ] Fill `content/committee.md` and `content/2027/committees.md`
- [ ] Confirm submission platform, then link it from the call for papers
- [ ] Attach recovered documents to past-edition pages as they become available
