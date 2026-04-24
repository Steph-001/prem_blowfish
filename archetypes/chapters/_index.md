---
title: "{{ replace .Name `-` ` ` | title }}"
date: {{ .Date }}
draft: true
weight: 10
showTableOfContents: true   # TOC à droite sur desktop (caché sur mobile)
# taught: YYYY-MM-DD    # ISO date — affiche "May 2026" sur la home
featureimage: ""        # nom du fichier image dans ce dossier (ex: "cover.jpg")
description: ""         # problématique du chapitre (s'affiche en gros sous le titre)
lexicon:
  # - term: "word"
  #   definition: "sa définition"
  #   stressed: "w*ord"   # l'astérisque précède la voyelle accentuée
---

<!--
  Structure recommandée d'un chapitre :
    ### A - Titre de section
    ### B - Titre de section
    ...

  Shortcodes utiles :
    {{`{{< fullscreen-image src="cover.jpg" max_width="100%" align="left" >}}`}}
    {{`{{< pdf src="doc.pdf" max_width="50%" align="left" >}}`}}
    {{`{{< spacer height="2rem" >}}`}}

  Place tous les médias (images, pdf, mp3, mp4) dans CE dossier.
-->
