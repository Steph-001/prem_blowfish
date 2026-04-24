# Notes Claude — blowfish-v1

Mémo pour reprise de session. Projet : site Hugo + thème Blowfish pour les cours
d'anglais de Stéphane Jacquet (Terminales), URL prod `terminales.stephanejacquet.fr`.

## État actuel

- Repo Hugo fonctionnel, thème Blowfish installé (`themes/blowfish/`).
- Home en style **lowkey** : hero + 2 listes (Units + News) utilisant
  le partial natif `article-link/simple.html`, override dans
  `layouts/partials/article-link/simple.html`.
- 5 chapitres publiés (non-draft) : witches, amish, animals, dystopia, survivalists.
- Color scheme actif : **congo**.
- Dossier de travail synchronisé Mac ↔ PC via iCloud (`~/Sync/blowfish-v1`).
- Git : repo local réinitialisé aujourd'hui (`git init` fresh), remote
  `https://github.com/Steph-001/blowblow.git`. Push initial en cours côté user.

## Conventions établies

### Dates (`taught:` field)
Hugo lit les dates dans l'ordre : `taught` → `date` → `publishDate` → `lastmod`
(voir `[frontmatter]` dans `config/_default/hugo.toml`).

Pour afficher la **date de début d'étude avec les élèves** plutôt que la date
de création du fichier, ajouter dans le front-matter d'un chapitre :

```yaml
taught: 2026-05-04   # format ISO, PAS "May 2026"
```

`dateFormat = "January 2006"` dans `languages.en.toml` → affiche mois + année.

### Problématique
Une seule source de vérité : le champ `description:` du front-matter. Le layout
`layouts/chapters/list.html` la rend comme un H2 en italique coloré. Ne PAS
dupliquer dans le corps du fichier.

### Tags (pages transverses : grammaire, prononciation, etc.)
Une page taguée est une page à l'intérieur d'un chapitre qui traite d'un sujet
transverse (grammaire, phonétique, culture…) et qu'on veut pouvoir retrouver
depuis plusieurs chapitres ou depuis un index centralisé.

**Créer une page taguée** — créer un `.md` dans le dossier du chapitre, avec
un `tags:` en liste dans le front-matter :

```yaml
---
title: "Adjective order"
linkTitle: "Adjectives"
draft: false
weight: 10
tags: [Grammar]          # ⚠️ TOUJOURS en liste, même pour un seul tag
---
```

**Règles clés :**
- Toujours `tags: [Foo]` (liste) et jamais `tags: Foo` (scalaire) — sinon le
  build Blowfish plante (`range can't iterate over Foo`).
- Le nom du tag est libre (Grammar, Pronunciation, Culture, Phonetics…). Hugo
  normalise automatiquement en slug (Grammar → `/tags/grammar/`).
- Maximum ~2 pages taguées par chapitre en pratique (UX des boutons).

**Ce que le layout fait automatiquement :**
- Sur la page du chapitre : un bouton pill à côté de Lexicon/Flashcards avec
  `🏷️ <nom du tag>` qui pointe directement vers la page taguée elle-même
  (voir `layouts/chapters/list.html`, bloc `$tagButtons`).
- Sur `/tags/` (accessible par le menu « Tags » de la barre du haut) : la liste
  de tous les tags utilisés sur le site, avec leur nombre d'occurrences.
- Sur `/tags/<nom>/` : la liste des pages portant ce tag, en cadres titre-seul
  (override `layouts/_default/term.html`, pas de vignette ni résumé).

**Menu Tags :** dans `config/_default/menus.en.toml`, utiliser `url = "/tags/"`
plutôt que `pageRef = "tags"` — `pageRef` peut devenir capricieux quand le
nombre de tags bouge.

### Images home (vignettes lowkey)
- Format : **WebP** (ou JPG propre) en **3:2**, ~100 KB.
- L'override `article-link/simple.html` cherche dans cet ordre :
  1. `page.Resources.GetMatch "featureimage.*"`
  2. `resources.Get` (assets/)
  3. Glob `*feature*` / `*cover*` / `*thumbnail*` / `*poster*`
  4. Première image trouvée dans les page resources
- ⚠️ **Piège** : fichier nommé `.jpg` mais en réalité WebP → Hugo Resize plante.
  Vérifier avec `file image.jpg` ; magic bytes `RIFF...WEBP` = renommer en `.webp`.

### Espacement home
`space-y-10` sur la div qui contient les entrées. C'est la classe Tailwind standard
de Blowfish, safe. Ne PAS utiliser `md:w-56`, `before:absolute`,
`group-hover:scale-105` etc. — Tailwind JIT ne les compile pas car pas dans les
fichiers scannés par Blowfish.

### Cadre/filet sur les cartes
Dans `assets/css/custom.css` : `.article-link--simple` reçoit un border bleu
(#bfdbfe clair → #1e3a8a dark). Géré par `[list] showCards = true` dans params.toml.

## Ce qui reste à faire

- **#45** TOC à droite sur les pages chapitre (modèle :
  <https://nunocoracao.github.io/blowfish_lowkey/posts/post-lite-one/>).
- **#44** Restyler `/chapters/` et `/news/` list pages en cohérence avec la home.
- **#41** Supabase login + progression (Steph devait tester avec un compte neuf).
- Écrire le « mode d'emploi » pour Steph avec toutes les conventions ci-dessus +
  section « cloner pour d'autres classes (Premières, Secondes) » avec palette
  différente.
- Considérer désactiver `showReadingTime` / `showWordCount` dans `[article]`
  (pas pertinent pour un site pédagogique).

## Points de friction connus

- **Tailwind JIT & Blowfish** : les classes custom non présentes dans les
  fichiers scannés par Blowfish ne seront PAS compilées → tout pété. Toujours
  utiliser des classes qui existent déjà dans le thème.
- **Submodule themes/blowfish** : historiquement déclaré dans `.gitmodules`.
  Après le fresh `git init` d'aujourd'hui, plus de `.gitmodules` — le thème
  est tracké comme du code vendoré. Si besoin de le remettre en submodule
  plus tard : `rm -rf themes/blowfish && git submodule add https://github.com/nunocoracao/blowfish.git themes/blowfish`.
- **.gitignore** : les `*.mp4`/`*.mov`/`*.webm` sont exclus (vidéos trop
  lourdes pour GitHub, synchronisées via iCloud). `public/` et `resources/_gen/`
  exclus (build output).
- **GitHub & email** : Steph a désactivé la protection d'email côté GitHub pour
  pouvoir push avec `stephanejacquet@outlook.com`. Pas besoin de noreply.

## Fichiers clés

- `config/_default/hugo.toml` — `[frontmatter]` date mapping
- `config/_default/languages.en.toml` — `dateFormat = "January 2006"`
- `config/_default/params.toml` — `colorScheme = "congo"`, `showCards = true`
- `layouts/partials/home/custom.html` — home hero + 2 listes lowkey
- `layouts/partials/article-link/simple.html` — override avec fallbacks image
- `layouts/chapters/list.html` — rend problématique depuis `.Description` +
  boutons Lexicon/Flashcards/🏷️ tag sur chaque chapitre
- `layouts/_default/term.html` — page `/tags/<nom>/` en cadres titre-seul
- `config/_default/menus.en.toml` — menu Tags (`url = "/tags/"`)
- `config/_default/hugo.toml` — `[taxonomies]` tag/category/author/series
- `assets/css/custom.css` — cadres bleus sur `.article-link--simple`
- `content/chapters/*/` — 5 chapitres actifs

## Commandes utiles

```bash
# build local
hugo server

# vérifier format d'une image soi-disant JPG
file content/chapters/foo/bar.jpg

# lister fichiers trackés par Git (vérifier qu'il n'y a pas de vidéos)
git ls-files | grep -c mp4   # doit renvoyer 0
```
