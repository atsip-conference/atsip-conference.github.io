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

## Design et interactions

La page d'accueil se compose d'une page de garde pleine largeur (`layouts/index.html`),
d'un bandeau photo, du bandeau institutionnel, de la frise des editions puis du
contenu de `content/_index.md`.

- **Largeur** : `--page` dans `assets/css/main.css` fixe la largeur maximale de
  tout le site. `<main>` n'est contraint que hors accueil
  (`layouts/_default/baseof.html`), pour que les bandeaux de l'accueil aillent
  de bord a bord.
- **Bandes** : `layouts/partials/banded.html` decoupe un contenu rendu en une
  section par `<h2>`, titre en colonne laterale collante. Applique a l'accueil
  et aux pages internes. Aucun JavaScript n'intervient.
- **Trace decorative** : `layouts/partials/cover-trace.html` est un SVG genere
  une fois (trace d'oscilloscope). Vectoriel, donc net a toute taille.
- **Interactions** : `layouts/partials/scripts.html` - compte a rebours jusqu'a
  la cloture des soumissions, filtre libre sur les topics, apparition au
  defilement, en-tete compact. Chaque bloc est isole ; sans JavaScript le
  contenu reste complet et les topics restent depliables (`<details>` natif).
- **Compte a rebours** : la date cible est l'attribut `data-countdown` dans
  `layouts/index.html`. A changer en meme temps que les dates cles.

L'image `static/img/sousse.png` fait 819 px de large : `.banner` est plafonnee a
52 rem pour qu'elle ne soit pas agrandie au-dela de sa definition.

## Local preview

```bash
hugo server -D
# http://localhost:1313
```

## Deployment

Push to `main`. Settings → Pages → Source must be **GitHub Actions**.

## Still to do

- [ ] Confirm the ATSIP'2027 committee and track chairs (carried over from 2024),
      including whether affiliations should now read ENET'Com rather than ENIS
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

## Organiser

The series was founded by Professor Ahmed Ben Hamida and the **ATMS** laboratory
(ENIS). **ATSIP'2027 is organised by the ATISP laboratory (ENET'Com)** — the
host laboratory changes between editions, so check it when rolling the site over
to a new year. It appears in `content/2027/_index.md`, `content/contact.md`,
`content/_index.md`, `layouts/partials/schema.html` and the CFP flyer.

Committee affiliations carried over from 2024 still read "ATMS, ENIS"; those are
individual affiliations, not the organiser, and should be confirmed per person.

## Naming

The conference name is **International Conference on Advanced Technologies,
Signal and Image Processing** — with a comma, matching the 2024 edition and the
logo. Earlier editions used "Advanced Technologies *for* Signal and Image
Processing"; do not mix the two, it fragments indexing.

## Search visibility

Technical groundwork is in place: `sitemap.xml`, `robots.txt`, canonical links,
Open Graph tags, and schema.org `Event` structured data on the current edition
page (which is what lets Google show dates and venue rather than a plain link).

What actually moves the ranking is off-site and has to be done by hand:

1. **Google Search Console** — verify the site, submit the sitemap, request
   indexing of the edition page. Without this, indexing takes weeks.
2. **Inbound links.** These matter more than anything on the page. Ask for a link
   from: IEEE Tunisia Section, the ATMS laboratory page, the universities of the
   committee members, and the conference's Facebook page.
3. **Conference listings** — WikiCFP, Conference Alerts, Guide2Research, AllConfs,
   and the IEEE conference calendar. Free, and they rank well themselves.
4. **Consistent naming.** Always "ATSIP'2027" and the comma form of the full name,
   in every announcement and mailing. Variants split the signal.

Note that "ATSIP" alone is also the acronym of a US transportation safety
association with an established domain; realistic targets are "ATSIP 2027",
"ATSIP conference", "ATSIP Sousse".
