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

## Liens vers les actes : le DOI, jamais l'adresse Xplore

Chaque edition porte un champ `doi` et pas de champ `xplore`. Les deux menent a
la meme page de sommaire sur IEEE Xplore, mais l'adresse directe
(`ieeexplore.ieee.org/xpl/conhome/<numero>/proceeding`) repose sur un numero
interne qui change quand IEEE reorganise son site : les sept liens du site ont
casse de cette facon. Le DOI, lui, est permanent.

Ne pas remettre d'URL Xplore en dur. Pour une nouvelle edition, prendre le DOI
des actes, verifiable sur `https://api.crossref.org/works/<doi>` : la reponse
donne le titre, l'annee et le lieu, ce qui confirme du meme coup la
numerotation de l'edition.

Les sept DOI ont ete verifies un par un aupres de CrossRef le 18 octobre 2026 ;
les lieux retournes concordent avec `editions.yaml` (Monastir 2016, Fes 2017,
Sousse 2014/2018/2020).

Astuce : le DOI des actes est le prefixe de celui de chaque article de
l'edition. `10.1109/ATSIP49331.2020.9231837` (un article) donne
`10.1109/ATSIP49331.2020` (les actes).

## Topics: two levels

The eight general tracks belong to the **conference**. They live in
`content/_index.md` under `## Topics` and are shown on the homepage. They follow the
ATSIP'2024 call for papers, with subtopics written out in full.

Each edition declares its **own** emphases and special sessions in its call for
papers (`content/YYYY/call-for-papers.md`).

## Microsoft CMT

L'accuse de reception CMT vit a UN SEUL endroit : la fin de
`content/2027/submission.md`, rendu sur
<https://atsip-conference.github.io/2027/submission/>. C'est l'URL declaree
dans la demande de site CMT.

Microsoft releve cette page et compare le texte mot pour mot. Ne pas le
reformuler, ne pas le mettre en italique ou en gras, ne pas le deplacer dans du
JavaScript, ne pas le commenter. Sans lui, la demande CMT est refusee.

Il n'est PAS dans le pied de page. Il y a ete, ce qui le faisait apparaitre sur
les pages des editions 2014-2024 : or ces editions n'ont pas utilise CMT, et la
phrase y etait donc fausse.

La regle de style compte autant que le texte : `.cmt-ack` le rend a taille et
couleur normales. Ne pas le reduire ni le griser, un texte minuscule ou pale
pouvant passer pour une tentative de dissimulation.

Si l'edition courante change (2029, etc.), deplacer le paragraphe dans la page
de soumission de la nouvelle edition et mettre a jour l'URL aupres de CMT.

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
- **Emblemes des tracks** : `layouts/partials/track-emblem.html` dessine les huit
  figures en SVG, appelees depuis le markdown par le shortcode
  `{{< emblem N >}}` (`layouts/shortcodes/emblem.html`). L'ordre des numeros
  suit celui des tracks dans `content/_index.md` : si l'ordre change, il faut
  changer les numeros aussi. La classe `em-accent` porte l'ambre.
- **Compte a rebours** : la date cible est l'attribut `data-countdown` dans
  `layouts/index.html`. A changer en meme temps que les dates cles.

L'image `static/img/sousse.png` fait 819 px de large : `.banner` est plafonnee a
52 rem pour qu'elle ne soit pas agrandie au-dela de sa definition.

## Pages de garde

Deux gabarits portent la meme page de garde bleue :

- `layouts/index.html` pour la conference (nom complet, 8 editions, prochaine
  edition) ;
- `layouts/_default/section.html` pour chaque edition. Le contenu depend de
  `status` dans `data/editions.yaml` : `upcoming` affiche la date limite avec
  son compte a rebours et les boutons de soumission, sinon l'ISBN et les liens
  vers les actes.

`<main>` n'est plus contraint : chaque gabarit pose ses propres `.wrap`, pour
que les bandeaux aillent de bord a bord. Un nouveau gabarit doit donc penser a
encapsuler son contenu.

## Rubans d'images

`layouts/partials/strip.html` sert les deux rubans : les thematiques sous la
page de garde de l'accueil (`data/cover_slides.yaml`) et les photos du lieu
sous celle de l'edition en cours (`data/venue_photos.yaml`).

Deux regles a ne pas casser :

1. La liste est repetee pour couvrir l'ecran, puis rendue DEUX FOIS, et
   l'animation translate de -50%. C'est ce qui donne une boucle sans saut.
2. Le nom de balise ne doit jamais etre construit dynamiquement
   (`<{{ $tag }}>`) : Go l'echappe et les cartes sortent en texte brut.

Images : JPG, 1200 px de large au maximum, moins de 150 Ko. Le rapport n'a pas
d'importance, rien n'est recadre. Eviter les fonds blancs (ils creusent un trou
dans le ruban sombre) et le texte incruste (doublon avec le libelle).

Les trois photos de Sousse sont decoupees du bandeau du CFP 2024 et ne font que
250 px de large : avec si peu d'images, la repetition se voit. De vraies photos,
plus nombreuses et plus grandes, ameliorent beaucoup le resultat.

## Bandeau institutionnel

Les logos (ATSIP, IEEE, ATSI, IEEE Tunisia Section) sont en HAUT de chaque
page, pas dans le pied. Chaque gabarit appelle
`{{ partial "sponsors.html" (dict "big" true) }}` juste apres son en-tete : un
nouveau gabarit doit penser a l'ajouter.

Ne pas le mettre dans la liste des elements animes au defilement
(`layouts/partials/scripts.html`) : etant au-dessus de la ligne de flottaison,
il clignoterait a l'ouverture de la page.

## En-tete bleu des pages d'edition

`layouts/partials/edition-head.html` : version condensee de la page de garde,
posee sur chaque page portant un parametre `edition` (cascade depuis
`content/<annee>/_index.md`). Le titre de la page remplace le nom de
l'edition, et les faits tiennent sur une ligne. Sans ce parametre, on retombe
sur un titre de page ordinaire.

## Chapeau pleine largeur

La classe `.lead`, posee autour des paragraphes d'introduction dans le
markdown, les etale sur toute la largeur EN COLONNES. Une seule colonne sur
1400 px donnerait des lignes de 170 caracteres, illisibles. Utilisee sur
`content/2027/_index.md` et `content/2027/call-for-papers.md`.

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

Ce qui est en place, et qu'il ne faut pas casser :

- un titre et une description UNIQUES par page, generes dans
  `layouts/partials/head.html`. Google penalise les descriptions dupliquees ;
  verifier apres tout ajout de page que les titres restent distincts ;
- `layouts/partials/schema.html` : un `EventSeries` sur l'accueil qui liste les
  huit editions (c'est ce qui rattache « ATSIP » a une entite unique et le
  distingue de l'association americaine de meme sigle), un `Event` sur la page
  de l'edition courante, un fil d'Ariane sur les pages internes ;
- `sitemap.xml`, `robots.txt`, liens canoniques, Open Graph.

Ce qui reste a faire a la main, et qui pese bien plus lourd :

1. **Google Search Console** — propriete en « prefixe d'URL » (le mode
   « domaine » est impossible sur github.io, qui n'est pas a nous).
   La verification passe par `static/google4823855c7d7be657.html` : NE PAS
   SUPPRIMER ce fichier, Google le relit periodiquement et la propriete serait
   perdue. Son contenu est impose par son nom. Un second canal existe si
   besoin : `params.googleVerification` dans `hugo.toml` emet la balise meta.
   Apres verification, soumettre
   `https://atsip-conference.github.io/sitemap.xml` et demander l'indexation
   de `/` et de `/2027/`. Sans cela, l'indexation prend des semaines.
2. **Liens entrants.** Facteur dominant. Demander un lien depuis : IEEE Tunisia
   Section, la page du laboratoire ATISP, ENET'Com, les universites des membres
   du comite, la page Facebook de la conference.
3. **Annuaires de conferences** : WikiCFP, Conference Alerts, Guide2Research,
   AllConfs, le calendrier IEEE. Gratuits, et bien classes eux-memes.
4. **Nommage constant** : toujours « ATSIP'2027 » et la forme complete avec
   virgule, dans chaque annonce et chaque mail. Les variantes dispersent le
   signal.

« ATSIP » seul est aussi le sigle d'une association americaine de securite
routiere, au domaine bien etabli. Les requetes realistes sont « ATSIP 2027 »,
« ATSIP conference », « ATSIP Sousse », « IEEE ATSIP ».
