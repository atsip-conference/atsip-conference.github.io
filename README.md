# ATSIP — conference website

Static site built with [Hugo](https://gohugo.io) (extended, v0.128+), deployed to
GitHub Pages. No database, no server-side code, no build dependencies beyond a
single binary.

## The one file that matters

`data/editions.yaml` drives the whole site: the homepage, the timeline, the
editions table, the archive banners. Changing `current:` from `2027` to `2029`
flips every page at once.

Metadata follows DBLP (<https://dblp.org/db/conf/atsip/index.html>), which
mirrors the published IEEE proceedings.

## Topics: two levels

The eight general tracks belong to the **conference**. They live in
`content/_index.md` under `## Topics` and are shown on the homepage. They follow the
ATSIP'2024 call for papers, with subtopics written out in full.

Each edition declares its **own** emphases and special sessions in its call for
papers (`content/YYYY/call-for-papers.md`).

## Microsoft CMT

The CMT acknowledgment is rendered on **every page**, in
`layouts/partials/foot.html`, as static plain text. CMT fetches the page and
compares the text exactly.

Do not style it, do not reword it, do not move it into JavaScript, do not comment
it out. Without it, a CMT site request is refused.

## Local preview

```bash
hugo server -D
# http://localhost:1313
```

## Deployment

Push to `main`. Settings → Pages → Source must be **GitHub Actions**.

## Still to do

- [ ] Confirm the ATSIP'2027 committee and track chairs (carried over from 2024)
- [ ] Name the partner journals for the special issues
- [ ] CMT submission link, once the site request is accepted
- [ ] Venue and accommodation details
- [ ] IEEE technical sponsorship application — critical path

## Call-for-papers flyer

The one-page A4 flyer lives in `static/files/CFP_ATSIP2027.pdf` and is linked from
the call-for-papers page. Its source is kept outside this repository; rebuild it
with WeasyPrint from the HTML (wkhtmltopdf lays out at 1024px and shrinks, which
leaves a white band on the right of the page — do not use it).

The photo band (`static/img/sousse.png`) is taken from the ATSIP'2024 flyer:
medina souk, golf course, marina.

## Naming

The conference name is **International Conference on Advanced Technologies,
Signal and Image Processing** — with a comma, matching the 2024 edition and the
logo. Earlier editions used "Advanced Technologies *for* Signal and Image
Processing"; do not mix the two, it fragments indexing.
