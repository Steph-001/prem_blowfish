# Mode d'emploi — Site Hugo + thème Blowfish

Guide de maintenance et de référence pour faire évoluer les sites
`secondes.stephanejacquet.fr`, `premieres.stephanejacquet.fr` et
`terminales.stephanejacquet.fr` sans se perdre dans la technique.

- **Auteur** : Stéphane Jacquet
- **Stack** : Hugo extended + thème Blowfish + Tailwind
- **Sources** : `~/Sync/sec`, `~/Sync/prem`, `~/Sync/term` (iCloud, synchronisé Mac / PC)
- **Backup Git** : `github.com/Steph-001/sec_blowfish` (et équivalents pour prem / term)
- **Dernière mise à jour** : avril 2026 (modèle "aboriginals")

---

## Sommaire

1. Démarrage rapide
2. Structure du projet
3. Les fichiers de configuration
4. Créer un nouveau chapitre
5. Le front-matter en détail
6. Images pour les vignettes
7. Shortcodes disponibles
8. Convention markdown des chapitres (modèle aboriginals)
9. Conversion des PDFs en images empilées
10. Lexicon (vocabulaire)
11. Dates d'étude (champ `taught`)
12. Home, TOC et mise en page
13. Cards / légendes Bootstrap-like
14. Couleurs et cloner pour une autre classe
15. Synchronisation Mac/PC et Git
16. Pièges connus
17. Dépannage
18. Crédits / copyright des médias

---

## 1. Démarrage rapide

### Prérequis

Hugo extended (v0.160 ou supérieur), Git, un éditeur (VS Code recommandé), et
**poppler** pour la rasterisation des PDFs. Sur macOS via Homebrew :

```bash
brew install hugo git poppler
hugo version          # doit contenir "extended"
pdftoppm -v           # doit afficher la version Poppler
```

### Lancer le serveur local

Depuis la racine d'un site :

```bash
cd ~/Sync/sec        # ou prem, ou term
hugo server
```

Hugo affiche une URL (typiquement `http://localhost:1313/`). Chaque modif d'un
fichier markdown est rechargée à chaud. Pour un changement de **layout** ou de
**config**, il faut souvent stopper (`Ctrl+C`) et relancer.

> **Astuce** — pour forcer un rebuild complet : `rm -rf resources/_gen public && hugo server`

### Ouvrir un chapitre

Les chapitres sont dans `content/chapters/<nom>/_index.md`.

---

## 2. Structure du projet

Seuls les dossiers en gras sont à connaître pour le travail quotidien.

| Dossier / fichier | Rôle |
|---|---|
| **`content/chapters/`** | Les unités pédagogiques (1 dossier = 1 chapitre) |
| `content/news/` | Articles d'actualité ponctuels (optionnel) |
| **`assets/css/custom.css`** | CSS maison qui surcharge le thème |
| `layouts/` | Layouts custom qui overrident le thème Blowfish |
| `config/_default/` | Config du site (`hugo.toml`, `params.toml`, etc.) |
| `static/` | Fichiers copiés tels quels à la racine du site |
| `themes/blowfish/` | Le thème — à NE PAS modifier |
| **`medias non référencés/`** | Fichiers médias mis de côté (orphelins, brouillons) |
| `public/` | Sortie de build — généré, à ignorer |
| `resources/` | Cache Hugo — généré, à ignorer |

### Anatomie d'un chapitre

```
content/chapters/witches/
  _index.md          ← page principale du chapitre
  lexicon.md         ← appelle {{< lexicon */>}}
  flashcards.md      ← appelle {{< flashcards */>}}
  Which.jpg          ← featureimage
  intro.mp4          ← vidéos
  circe.pdf          ← PDFs
  circe-page-1.jpg   ← rasterisation PDF (générée par script)
  debunking.mp3      ← audios
```

---

## 3. Les fichiers de configuration

Six fichiers TOML dans `config/_default/`, chacun avec une responsabilité précise.

| Fichier | Responsabilité |
|---|---|
| `hugo.toml` | Config Hugo globale (URL, thème, pagination, dates) |
| `languages.en.toml` | Titre, description, auteur, format de date |
| `params.toml` | Paramètres du thème (couleurs, TOC, affichage…) |
| `menus.en.toml` | Menus (barre du haut, dropdowns, pied de page) |
| `markup.toml` | Options de rendu markdown (HTML inline, TOC) |
| `module.toml` | Version minimale Hugo requise |

### `hugo.toml` — l'essentiel

```toml
theme = "blowfish"                          # ne pas toucher
baseURL = "https://secondes.stephanejacquet.fr/"

[pagination]
  pagerSize = 20

[outputs]
  home = ["HTML", "RSS", "JSON"]

[frontmatter]
  date = ["taught", "date", "publishDate", "lastmod"]
```

### `languages.en.toml` — la localisation

```toml
title = "Secondes"
dateFormat = "January 2006"

[params]
  description = "English lessons for French Seconde students."
```

### `params.toml` — points clés

```toml
colorScheme = "congo"             # voir section 14 pour les options
defaultAppearance = "light"
autoSwitchAppearance = true
enableSearch = true

[article]
  showTableOfContents = true       # TOC à droite sur les chapitres
  showReadingTime = false
  showWordCount = false

[list]
  showCards = true                  # cadre bleu autour des entrées home
```

### `menus.en.toml` — la navigation

```toml
[[main]]
  name = "Units"
  url = "/#units"
  weight = 10

[[main]]
  name = "News"
  pageRef = "news"                  # ou url = "/news/"
  weight = 20

[[main]]
  name = "Tags"
  url = "/tags/"                    # toujours url, pas pageRef
  weight = 50
```

> **Astuce** — Après toute modif d'un fichier dans `config/_default/`, relancer `hugo server`. La config n'est pas rechargée à chaud.

---

## 4. Créer un nouveau chapitre

Trois archetypes sont installés dans `archetypes/chapters/`.

### Méthode 1 — `hugo new` (depuis archetype)

```bash
cd ~/Sync/sec
hugo new chapters/brexit/_index.md
hugo new chapters/brexit/lexicon.md
hugo new chapters/brexit/flashcards.md
```

Hugo crée le dossier avec le front-matter pré-rempli (`draft: true`).

### Méthode 2 — copier un chapitre existant

```bash
cp -r content/chapters/aboriginals content/chapters/brexit
# puis éditer le front-matter (title, date, weight, featureimage, description)
# et remplacer les médias dans le dossier
```

Plus rapide si on part d'une structure proche.

### Étape suivante : remplir le front-matter

```yaml
---
title: "Brexit"
date: 2026-04-21T09:00:00+02:00
draft: false                          # passer à false pour publier
weight: 10
taught: 2026-05-15                    # date ISO obligatoire (jour inclus)
featureimage: "brexit.jpg"
description: "How has leaving the EU reshaped British identity?"
showTableOfContents: true
lexicon:
  - term: "Brexit"
    definition: "the withdrawal of the UK from the EU"
    stressed: "Br*exit"
---
```

---

## 5. Le front-matter en détail

| Champ | Type | Rôle |
|---|---|---|
| `title` | string | Titre affiché (H1 de la page) |
| `date` | date ISO | Date de création (fallback si `taught` absent) |
| `taught` | date ISO | Date de début d'étude avec les élèves (voir section 11) |
| `draft` | bool | `true` = masqué en production |
| `weight` | int | Ordre d'affichage (petit = haut) |
| `featureimage` | string | Nom du fichier image pour la vignette home |
| `description` | string | **Problématique** (rendue en H2 italique sur la page) |
| `showTableOfContents` | bool | TOC à droite sur le chapitre |
| `lexicon` | array | Liste de termes (voir section 10) |
| `copyright` | string | Mention copyright unique du chapitre (voir section 18) |

### La règle d'or sur la problématique

Le champ `description` est la **source unique de vérité** pour la problématique
du chapitre. Elle apparaît :

- sur la vignette home (à droite de l'image)
- en haut de la page chapitre (rendue en H2 italique coloré)
- dans les balises meta (SEO)

**Ne jamais réécrire la problématique dans le corps du `_index.md`.**

> **Attention** — Les dates doivent être au format ISO (`2026-05-15` ou `2026-05-15T09:00:00+02:00`). `"May 2026"` en chaîne est ignoré silencieusement par Hugo.

---

## 6. Images pour les vignettes

### Format recommandé

- Ratio : **3:2 horizontal** (par ex. 900×600 px)
- Format : WebP (plus léger) ou JPG de qualité
- Poids : ~100 Ko, idéalement < 200 Ko
- Nom : pas d'espace ni d'accent — `brexit.jpg` oui, `le brexit !.jpg` non

### Où la placer

Dans le dossier du chapitre, à côté de `_index.md`. Le nom est référencé dans
`featureimage`.

### Le piège classique : WebP renommé `.jpg`

Si une image téléchargée est en réalité un WebP déguisé en `.jpg` (cas fréquent
sur certains sites), Hugo plante au build : `image: unknown format`.

```bash
# vérifier le vrai format
file content/chapters/brexit/brexit.jpg

# si "WebP image data" : renommer
mv content/chapters/brexit/brexit.jpg content/chapters/brexit/brexit.webp
# puis dans _index.md : featureimage: "brexit.webp"
```

---

## 7. Shortcodes disponibles

Les shortcodes sont des mini-composants réutilisables qu'on appelle dans le
markdown avec la syntaxe `{{< … >}}`.

### `fullscreen-image` — image cliquable en plein écran

```markdown
{{< fullscreen-image src="rumspringa.jpg" >}}
```

Paramètres optionnels : `max_width` (défaut `100%`), `align` (`center` / `left`).
**En pratique sur sec/prem/term, on utilise les défauts.**

### `responsive-image` — image simple, pas de fullscreen

```markdown
{{< responsive-image src="diagram.png" >}}
```

Mêmes paramètres que `fullscreen-image`. À utiliser quand l'image n'a pas
besoin d'être agrandie au clic.

### `local-video` — vidéo MP4 dans la page

```markdown
{{< local-video src="intro.mp4" poster="cover.jpg" max_width="100%" align="left">}}
```

Paramètres : `src` (obligatoire), `poster` (image d'aperçu), `max_width`,
`align`. **Toujours encapsuler dans un `video-plus-card-wrapper`** (voir
section 8).

### `pdf` — PDF rasterisé en images empilées

```markdown
{{< pdf src="circe.pdf" max_width="100%" max_pages="1" >}}
```

Le shortcode cherche `<base>-page-*.jpg` à côté du PDF (générés par le
script `convert_pdfs.py`, voir section 9). S'il les trouve, il empile les
images. Sinon, il retombe sur l'embed `<object>` natif. Un bouton
**"Download PDF"** est toujours affiché sous le bloc.

**Convention sur les 3 sites** : tous les PDFs utilisent `max_pages="1"`
pour n'afficher que la 1re page (rendu plus lisible, moins de scroll). Le
bouton Download donne accès au PDF complet de toute façon.

**Variantes** :

```markdown
{{< pdf src="doc.pdf" max_width="100%" max_pages="1" >}}    <!-- 1re page seulement -->
{{< pdf src="doc.pdf" max_width="100%" max_pages="3" >}}    <!-- 3 premières pages -->
{{< pdf src="doc.pdf" max_width="100%" >}}                  <!-- toutes les pages (omettre max_pages) -->
```

### `audio` — lecteur audio

```markdown
{{< audio src="listening.mp3" width="100%" rounded="true" >}}
```

### `spacer` — espace vertical entre 2 éléments

```markdown
{{< spacer height="1rem" >}}
```

À utiliser **uniquement** entre 2 médias qui se suivent **sans heading entre
eux**. Sinon, le margin du heading suffit.

### `lexicon` / `flashcards`

À placer dans `lexicon.md` / `flashcards.md` (sous-pages du chapitre) :

```markdown
{{< lexicon >}}
{{< flashcards >}}
```

### `gallery` — galerie d'images (sous-dossier)

```markdown
{{< gallery max_width="200px" >}}
```

Affiche toutes les images du sous-dossier `gallery/` du chapitre. Cliquables
en plein écran, layout flexbox responsive.

---

## 8. Convention markdown des chapitres (modèle aboriginals)

La page **`content/chapters/aboriginals/_index.md`** dans `sec/` est le
**modèle canonique**. Tout nouveau chapitre suit strictement ce pattern.

### Hiérarchie de titres

```markdown
## A - Titre de section principale     ← h2, JAMAIS h3
### 1. Sous-section numérotée          ← h3, optionnel
#### Type: description                  ← h4, label par média (optionnel)
<média>
```

- `## A/B/C` (h2) pour les sections principales — **jamais h3**.
- Les `### 1./2./3.` (h3) numérotés et les `#### Video: …` (h4) sont
  **optionnels**. Ne pas en ajouter automatiquement, c'est à toi de décider du
  grain pédagogique.
- **Ne jamais écrire un `#`** dans le markdown : la H1 est gérée par le
  layout via le frontmatter `description`.

### Vidéo — toujours dans un wrapper

```markdown
<div class="video-plus-card-wrapper mb-4" style="max-width:100%;">

{{< local-video src="X.mp4" poster="X.png" max_width="100%" align="left">}}

<div class="card">
  <div class="card-body">
    <p class="card-text text-justify">Légende.</p>
  </div>
</div>
</div>
```

Le wrapper contient **uniquement la vidéo et sa card-caption**.

### Image fullscreen + caption

```markdown
{{< fullscreen-image src="X.png" >}}
<div class="card">
  <div class="card-body">
    <p class="card-text text-justify">Légende.</p>
  </div>
</div>
```

Pas de wrapper autour. La card vient juste après l'image.

### PDF (Docsy-style sur les 3 sites)

```markdown
{{< pdf src="document.pdf" max_width="100%" >}}
```

### Audio

```markdown
{{< audio src="X.mp3" width="100%" rounded="true" >}}
```

### Spacers

- Pas de spacer entre un heading (`##`/`###`/`####`) et le média qui suit.
- `{{< spacer height="1rem" >}}` UNIQUEMENT entre 2 médias qui se suivent
  **sans heading entre eux**. Cas rare.

### Cards-captions

- Toutes les `<div class="card">` reçoivent automatiquement le styling
  Bootstrap-like via `assets/css/custom.css` (cadre subtil, padding, fond
  clair / sombre).
- Largeur fixée à **92%** (légèrement plus étroite que les médias 100%).
- Si une image / vidéo n'a pas de légende, **ne pas mettre une card vide** :
  laisser le média seul.

### Classes legacy supportées

Le markdown peut utiliser ces classes Bootstrap (CSS dédié dans `custom.css`) :

- `text-primary` — bleu accent (lien-color du thème)
- `fw-bold` — gras
- `text-justify` — texte justifié

---

## 9. Conversion des PDFs en images empilées

Le shortcode `pdf` rend les PDFs en empilant des JPGs (rendu propre, sans la
toolbar de viewer du navigateur). Il faut convertir les PDFs **une fois**
après les avoir ajoutés.

### Script automatique

```bash
# convertir tous les PDFs d'un site en images empilées
python3 ~/Sync/scripts/convert_pdfs.py ~/Sync/sec    # ou prem/, term/

# options
python3 ~/Sync/scripts/convert_pdfs.py -f ~/Sync/sec      # forcer la reconversion
python3 ~/Sync/scripts/convert_pdfs.py -r 200 ~/Sync/sec  # DPI plus élevé (défaut 150)
```

Le script crée `<base>-page-1.jpg`, `<base>-page-2.jpg`, etc. à côté de
chaque PDF. Le shortcode les détecte automatiquement.

### Manuellement (un seul PDF)

```bash
cd content/chapters/<chapitre>/
pdftoppm -jpeg -r 150 mondoc.pdf mondoc-page
```

---

## 10. Lexicon (vocabulaire)

Le lexique de chaque chapitre est défini dans le front-matter de son
`_index.md`, puis rendu automatiquement sur `lexicon.md` via le shortcode.

### Structure d'une entrée

```yaml
lexicon:
  - term: "resilient"
    definition: "résilient"
    stressed: "res*ilient"
  - term: "(to) assess"
    definition: "évaluer"
    stressed: "(to) ass*ess"
```

### Accents toniques colorés

Préfixer la voyelle accentuée par `*`. Elle est rendue en **rouge gras**.

| Écriture | Rendu |
|---|---|
| `res*ilient` | res**i**lient |
| `(to) *injure` | (to) **i**njure |
| `p*a*ct` | p**a**ct |

Le shortcode trie par ordre alphabétique et affiche `term : definition`.

---

## 11. Dates d'étude (champ `taught`)

Par défaut, Hugo affiche la date de création (`date`) du fichier. Pour
afficher plutôt la date à laquelle le chapitre a commencé à être étudié,
utiliser `taught` :

```yaml
taught: 2026-05-04        # format ISO obligatoire
```

L'ordre de priorité dans `hugo.toml` est :

```
taught → date → publishDate → lastmod
```

Format d'affichage défini dans `languages.en.toml` :

```toml
dateFormat = "January 2006"        # → "May 2026"
```

> **Attention** — Le jour est obligatoire dans la date ISO même s'il n'apparaît pas. Hugo a besoin d'une date complète pour parser. `"May 2026"` en chaîne est ignoré silencieusement.

---

## 12. Home, TOC et mise en page

### La home en style lowkey

La home utilise un layout custom (`layouts/partials/home/custom.html`) qui
affiche un hero, puis deux listes : Units (chapitres) et News. Chaque entrée
appelle le partial natif `article-link/simple.html`.

L'écart entre les cartes est contrôlé par `space-y-10` (Tailwind, déjà
compilé). Pour élargir : `space-y-12` ou `space-y-16`.

### Cadres autour des cartes home

Le bord bleu vient de `assets/css/custom.css` :

```css
.article-link--simple {
  border-color: #bfdbfe !important;       /* bleu clair */
}
.article-link--simple:hover {
  border-color: #60a5fa !important;       /* bleu plus vif */
}
```

### Table des matières (TOC)

Activée par `showTableOfContents = true` dans `[article]` de `params.toml`.

- Sur écran large (≥ 1024 px) : sidebar à droite, sticky.
- Sur mobile : repliée dans un `<details>`.
- Pour désactiver sur un chapitre : `showTableOfContents: false` dans le
  front-matter.

### Layout `chapter-{slug}` sur les chapitres

Le layout `layouts/chapters/list.html` ajoute automatiquement la classe
`chapter-<dossier>` sur le `<div class="article-content">`. Ça permet aux
règles CSS scopées aux chapitres (cards, PDFs, vidéos) de s'appliquer **uniquement**
sur les pages chapitres et pas sur les autres pages (news, lexicon).

---

## 13. Cards / légendes Bootstrap-like

Les chapitres utilisent `<div class="card">` héritées de l'ancien site Docsy.
Blowfish n'inclut pas Bootstrap — donc on a reproduit le rendu attendu via du
CSS custom dans `assets/css/custom.css`, scopé aux pages chapitre :

```css
.article-content[class*="chapter-"] .card {
  background: #ffffff;
  border: 1px solid #e5e7eb;
  border-radius: 0.5rem;
  max-width: 92%;
  margin: 0.75rem auto !important;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.05);
}
.article-content[class*="chapter-"] .card-body { padding: 0.875rem 1.125rem; }
.article-content[class*="chapter-"] .card-text { … }
```

Le CSS gère également le mode sombre. Largeur fixée à **92%** pour rester
visiblement plus étroit que les médias (100%), sans être trop intrusif.

### Fix CSS pour les médias

`assets/css/custom.css` contient aussi plusieurs corrections critiques :

- **Vidéo Safari/Firefox** : annule le `margin: 2em` que Tailwind Typography
  ajoute aux `<video>` (sans ça, les contrôles natifs sont coupés).
- **fullscreen-image** : annule le margin prose sur l'`<img>` enfant du
  wrapper (sans ça, ~32px de gap en plus sous l'image).
- **responsive-image** : annule le margin-top prose pour aligner avec
  fullscreen-image.
- **PDF** : ajoute 2em haut/bas autour du wrapper, plus tout le styling de
  `.pdf-pages`, `.pdf-page` et `.pdf-download-link`.

**À ne pas modifier** sauf si tu sais ce que tu fais.

---

## 14. Couleurs et cloner pour une autre classe

### Le schéma actuel

Défini dans `config/_default/params.toml` :

```toml
colorScheme = "congo"        # Terminales = congo (bleu profond)
```

Blowfish fournit plusieurs schémas prêts à l'emploi : `blowfish`, `congo`,
`avocado`, `cherry`, `fire`, `forest`, `neon`, `noir`, `ocean`, `princess`,
`sapphire`, `slate`.

### Cloner pour une autre classe

```bash
cd ~/Sync
cp -r sec premieres-v2
cd premieres-v2

# 1. config/_default/hugo.toml
#    baseURL = "https://premieres.stephanejacquet.fr/"

# 2. config/_default/languages.en.toml
#    title = "Premières"

# 3. config/_default/params.toml
#    colorScheme = "avocado"     # ou autre

# 4. Vider content/chapters/ et remettre les chapitres propres
```

> **Astuce** — Pour un rendu vraiment distinct, changer aussi la couleur du bord des cartes dans `assets/css/custom.css` pour harmoniser avec le nouveau colorScheme.

---

## 15. Synchronisation Mac/PC et Git

### Le setup actuel

- **iCloud** synchronise en permanence `~/Sync/{sec,prem,term}` entre Mac et
  PC. C'est la sauvegarde principale.
- **Git** ajoute une sauvegarde versionnée sur GitHub (1 repo par site).

### Routine de push

```bash
cd ~/Sync/sec        # ou prem, term
git add .
git commit -m "ajout du chapitre Brexit"
git push
```

### Ce qui est et n'est pas versionné

Le `.gitignore` exclut :

- `public/` (build output)
- `resources/_gen/` (cache Hugo)
- `node_modules/`
- `.DS_Store`
- **Toutes les vidéos** : `*.mp4`, `*.mov`, `*.webm` (trop lourdes pour GitHub)

Versionné : tout le reste — `content/`, `layouts/`, `assets/`, `config/`,
images, PDFs, audios.

> **Attention** — Les vidéos sont volontairement hors-Git car trop lourdes pour GitHub. Elles restent sur le disque et circulent via iCloud. Si un jour tu veux les versionner, regarder Git LFS.

---

## 16. Pièges connus

### Tailwind JIT et Blowfish

Blowfish utilise Tailwind en mode JIT : seules les classes qu'il trouve dans
ses propres fichiers sont compilées. Écrire du HTML custom avec des classes
comme `md:w-56`, `before:absolute` ou `group-hover:scale-105` ne fonctionnera
pas — elles seront silencieusement ignorées.

**Règle** : réutiliser des partials existants dont les classes sont déjà
compilées. Pour ajouter du style custom, passer par `assets/css/custom.css`.

### WebP déguisé en `.jpg`

Voir section 6.

### Duplication de la problématique

Voir section 5. Ne jamais réécrire la problématique dans le corps du
`_index.md`.

### Date en chaîne au lieu d'ISO

`taught: "May 2026"` est silencieusement ignoré. Toujours `taught: 2026-05-04`.

### Casse des noms de fichiers en production

Hugo sur **macOS** est case-insensitive (filesystem APFS). Sur **Linux**
(serveur OVH, GitHub Actions), c'est case-sensitive. Donc :

- `Image.jpg` et `image.jpg` sont identiques en local Mac, **différents** en
  prod.
- Convention recommandée : tout en minuscules sur disque (sauf noms propres
  type `James.pdf`, `Which.jpg`).

Pour renommer en gardant la casse différente sur un filesystem case-insensitive :

```bash
mv Image.jpg image_tmp.jpg
mv image_tmp.jpg image.jpg
```

### Médias non référencés

Le dossier `medias non référencés/` à la racine de chaque site contient les
fichiers déplacés là parce que pas (encore) utilisés dans le markdown :
brouillons, alternatives, sources `.docx`, etc. Pas inclus dans le build.

### Vidéos MP4 en pix_fmt yuvj420p (espace JPEG)

Symptôme côté navigateur : "No video with supported format and MIME type
found". La vidéo a un codec H.264 valide mais utilise l'espace colorimétrique
`yuvj420p` (JPEG / full range 0-255), techniquement déprécié et rejeté par
de nombreux navigateurs. Souvent invisible sous Docsy à l'époque, mais
casse aujourd'hui.

**Diagnostic** — scanner tous les sites pour repérer les vidéos
problématiques :

```bash
for site in sec prem term; do
  find ~/Sync/$site/content/chapters -name "*.mp4" -exec sh -c \
    'pix=$(ffprobe -v error -select_streams v -show_entries stream=pix_fmt -of csv=p=0 "$1" 2>/dev/null); [ "$pix" = "yuvj420p" ] && echo "  $1"' _ {} \;
done
```

Si la commande ne retourne rien : tout va bien. Sinon, chaque ligne est une
vidéo à re-encoder.

**Fix** — re-encoder une vidéo problématique :

```bash
cd ~/Sync/sec/content/chapters/<chapitre>/
ffmpeg -i video.mp4 -c:v libx264 -profile:v high -pix_fmt yuv420p \
       -crf 18 -preset medium -c:a aac -b:a 128k video_new.mp4
mv video_new.mp4 video.mp4    # remplace l'original
```

CRF 18 préserve la qualité visuelle ; abaisser à 23-28 pour une vidéo plus
légère.

### Ne pas toucher `themes/blowfish/`

Toute modification directement dans le dossier du thème sera écrasée si tu
mets le thème à jour. Pour surcharger un layout du thème, le copier vers
`layouts/` (au même sous-chemin) puis l'éditer là. Hugo charge en priorité
les layouts du projet.

---

## 17. Dépannage

### Le site ne se charge pas

```bash
# stopper Hugo (Ctrl+C) puis relancer
hugo server

# si toujours cassé : vider le cache et rebuilder
rm -rf resources/_gen public
hugo server
```

### Une image n'apparaît pas sur la home

- Vérifier que le fichier est bien dans le dossier du chapitre.
- Vérifier le nom dans `featureimage:` (sensible à la casse, attention espaces).
- Vérifier le vrai format : `file image.jpg`.

### Les contrôles vidéo sont coupés en bas

Bug Safari/Firefox connu et corrigé dans `assets/css/custom.css`. Si ça
revient, vérifier que la règle `.video-aspect-ratio-container > video {
margin: 0 !important }` est toujours présente.

### Un PDF affiche `<object>` au lieu des images empilées

Le PDF n'a pas encore été rasterisé. Lancer :

```bash
python3 ~/Sync/scripts/convert_pdfs.py ~/Sync/sec
```

### Une modif de layout ne prend pas effet

Hugo recharge à chaud le contenu et `assets/css/`, mais pas toujours
`layouts/`. Stopper (`Ctrl+C`) et relancer `hugo server`.

### Vérifications utiles

```bash
# version Hugo
hugo version

# combien de vidéos dans Git ? (doit être 0)
git ls-files | grep -c mp4

# taille du projet
du -sh .

# les classes chapter-* sont-elles bien générées ?
grep -c "chapter-" public/chapters/*/index.html
```

### En cas de panique Git

Si Git est dans un état incompréhensible après une série d'essais, l'option
nucléaire :

```bash
cd ~/Sync/sec
rm -rf .git
git init
git add .
git commit -m "repart à zéro"
git remote add origin https://github.com/Steph-001/sec_blowfish.git
git branch -M main
git push -u origin main --force
```

> **Attention** — `--force` écrase l'historique distant. À utiliser seulement si tu es sûr que la version locale est la bonne.

---

## 18. Copyright des médias

Le frontmatter accepte un champ `copyright` (chaîne unique) qui rend
automatiquement une ligne discrète en bas de la page chapitre, type footer
de magazine. Une seule mention par chapitre — pour synthétiser les sources
ou la mention légale globale du chapitre.

### Front matter

```yaml
copyright: "© 2026 Stéphane Jacquet — Sources : <em>Seek Discomfort</em>, BBC, Fourth Estate. Usage pédagogique."
```

- HTML autorisé dans la chaîne (`<em>`, `<i>`, `<strong>`, `<a>`, etc.).
- Si `copyright` est absent ou vide, **aucun bloc n'est rendu** — comportement
  silencieux, pas d'erreur.

### Rendu

Le layout `chapters/list.html` rend automatiquement à la fin de la page :

```
─────────────────────────────────
© 2026 Stéphane Jacquet — Sources : Seek Discomfort, BBC, Fourth Estate. Usage pédagogique.
```

Style : ligne en italique très discret (gris clair, petit), séparée du
contenu par une fine ligne grise. Adapté au mode sombre. Aucun heading,
juste une mention de bas de page comme un copyright traditionnel.

### Bonnes pratiques

- Une seule mention concise par chapitre.
- Préfixer avec `©` ou `Copyright` si pertinent.
- Citer les sources principales en fin de mention.
- Pour le domaine public ou licences libres, le préciser.

---

*Fin du mode d'emploi. Dernière mise à jour : avril 2026.*
