---
title: "{{ replace .Name `-` ` ` | title }}"
date: {{ .Date }}
draft: true
weight: 10
showTableOfContents: true   # TOC à droite sur desktop (caché sur mobile)
# taught: YYYY-MM-DD    # ISO date — affiche "May 2026" sur la home
featureimage: ""        # nom du fichier image dans ce dossier (ex: "cover.jpg")
description: ""         # problématique du chapitre (s'affiche en gros sous le titre)
copyright: "<em>Credits to </em>"   # ligne de crédits en bas de page (HTML autorisé)
lexicon:
  # - term: "word"
  #   definition: "sa définition"
  #   stressed: "w*ord"   # l'astérisque précède la voyelle accentuée
---

<!--
  Structure recommandée d'un chapitre (modèle aboriginals) :
    ## A - Titre de section          ← h2, JAMAIS h3
    ## B - Titre de section
    ...

  Shortcodes utiles (tous en defaults) :
    {{`{{< fullscreen-image src="cover.jpg" >}}`}}
    {{`{{< pdf src="doc.pdf" max_width="100%" max_pages="1" >}}`}}    <!-- défaut : 1re page (bouton Download donne le complet) -->
    {{`{{< pdf src="doc.pdf" max_width="100%" max_pages="3" >}}`}}    <!-- 3 premières pages -->
    {{`{{< pdf src="doc.pdf" max_width="100%" >}}`}}                  <!-- toutes les pages (omettre max_pages) -->
    {{`{{< audio src="X.mp3" width="100%" rounded="true" >}}`}}
    {{`{{< spacer height="1rem" >}}`}}                                <!-- entre 2 médias sans heading -->

  Vidéo (toujours dans un wrapper, à 100%) :
    <div class="video-plus-card-wrapper mb-4" style="max-width:100%;">
    {{`{{< local-video src="X.mp4" poster="X.png" max_width="100%" align="left">}}`}}
    <div class="card">
      <div class="card-body">
        <p class="card-text text-justify">Légende vidéo.</p>
      </div>
    </div>
    </div>

  Place tous les médias (images, pdf, mp3, mp4) dans CE dossier.
  Après ajout de PDFs : python3 ~/Sync/scripts/convert_pdfs.py ~/Sync/<site>
-->
