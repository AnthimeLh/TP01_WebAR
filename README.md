# TP01 - WebAR (ST2AWD)

Projet de réalité augmentée sur le web avec **A-Frame** et **AR.js**, réalisé en 6 exercices progressifs. Le site est publié avec GitHub Pages :
👉 https://anthimelh.github.io/TP01_WebAR/

## Stack technique

- [A-Frame](https://aframe.io/docs/) 1.6.0 : moteur de scène 3D/WebXR déclaratif (balises HTML type `<a-box>`, `<a-scene>`...).
- [AR.js](https://ar-js-org.github.io/AR.js-Docs/) : détection de marqueurs par la caméra et superposition d'objets 3D en réalité augmentée, directement dans le navigateur.
- Aucun build, aucune dépendance à installer : tout tourne dans `index.html` via des `<script>` chargés depuis un CDN.

## Structure du projet

```
index.html          Scène AR principale (exercices 2, 3, 4 et 6)
qr.html              Page d'affichage du QR code de l'exercice 5
markers/
  custom.patt        Marqueur personnalisé entraîné (exercice 4)
  qr_hiro.png         QR code + marqueur Hiro combinés (exercice 5)
```

## Exercice 1 — Scène 3D avec A-Frame

Point de départ du TP : une scène A-Frame classique (hors AR) avec trois boîtes de couleurs différentes lévitant à 2 unités au-dessus d'un plan, et une caméra placée à 10 unités de hauteur / 15 unités de distance regardant le centre du plan.

Cette scène a ensuite été **transformée** en scène AR dès l'exercice 2 (comme demandé dans le TP), donc son contenu ne figure plus tel quel dans `index.html` final — c'est la base qui a servi à apprendre la syntaxe A-Frame (entités, positions, couleurs, caméra).

## Exercice 2 — Intégration d'AR.js et marqueur Hiro

- Ajout de la bibliothèque AR.js et de l'attribut `arjs` sur `<a-scene>` (`sourceType: webcam`) pour utiliser le flux caméra en direct.
- Un `<a-marker preset="hiro">` détecte le marqueur imprimé [Hiro](https://jeromeetienne.github.io/AR.js/data/images/HIRO.jpg).
- Quand il est détecté, une boîte semi-transparente (`material="opacity: 0.6; transparent: true;"`) apparaît et tourne en continu sur elle-même (`animation__spin`).
- Le site est publié via GitHub Pages pour pouvoir être testé sur smartphone.

## Exercice 3 — Interaction par clic/tap

- Un curseur (`<a-cursor>`) est ajouté à la caméra, avec un raycaster ciblant la boîte (`#arBox`).
- Un clic (souris) ou tap (mobile) sur la boîte :
  1. change sa couleur (cycle dans un tableau de 4 couleurs),
  2. accélère temporairement sa rotation pendant 1 seconde,
  3. déclenche un effet de pulse (léger agrandissement puis retour à la taille normale).

Voir le script en bas de `index.html`, listener `box.addEventListener('click', ...)`.

## Exercice 4 — Marqueur personnalisé

- Une image a été choisie et transformée en fichier `.patt` avec le [générateur officiel AR.js](https://jeromeetienne.github.io/AR.js/three.js/examples/marker-training/examples/generator.html), puis placée dans `markers/custom.patt`.
- Un second marqueur est déclaré dans la scène : `<a-marker type="pattern" url="markers/custom.patt">`.
- Quand ce marqueur est détecté, une **sphère jaune** apparaît (objet différent de la boîte), avec une animation de lévitation (va-et-vient vertical).

> **Point d'attention appris pendant le TP** : la fiabilité de la détection dépend beaucoup de l'image choisie. Un motif trop pauvre en contraste (grandes zones noires uniformes, peu de détails) est mal reconnu par AR.js. Il faut une image avec des formes noires/blanches franches et asymétriques (un peu comme le marqueur Hiro lui-même), imprimée avec une bordure noire nette.

## Exercice 5 — QR code + marqueur Hiro

- `markers/qr_hiro.png` : un QR code (correction d'erreur de niveau **H**, la plus robuste) qui redirige vers l'URL GitHub Pages du projet, avec le marqueur Hiro incrusté au centre. Le niveau H tolère jusqu'à ~30 % de surface masquée, ce qui permet d'incruster une image au centre sans casser le scan.
- `qr.html` : une page dédiée qui affiche ce visuel en grand, pratique à ouvrir et imprimer.
- **Scénario de test** :
  1. Scanner le QR code avec l'appareil photo du téléphone → ouvre le site.
  2. Pointer ensuite la caméra du navigateur (dans la scène AR) vers ce même QR code → le marqueur Hiro au centre est détecté et la boîte AR s'affiche.

## Exercice 6 — Interface utilisateur (UI DOM overlay)

- Un panneau HTML (`<div id="uiOverlay">`) est superposé à la scène AR, contenant :
  - un slider pour changer la taille de la boîte (0.1 → 3.0),
  - une palette de 3 couleurs (rouge, vert, bleu).
- Ce panneau est **caché par défaut** et n'apparaît que lorsque le marqueur Hiro est détecté, grâce aux événements `markerFound` / `markerLost` émis par AR.js sur l'élément `<a-marker>`.
- Déplacer le slider modifie l'attribut `scale` de la boîte en temps réel ; cliquer une couleur modifie son attribut `color` immédiatement.

## Comment tester le projet

Prérequis : un smartphone avec appareil photo, connecté à internet, et les marqueurs imprimés (Hiro + marqueur personnalisé) ou affichés sur un second écran.

1. **Exercices 2/3 (marqueur Hiro)** : ouvrir `https://anthimelh.github.io/TP01_WebAR/`, autoriser la caméra, pointer vers le marqueur Hiro imprimé → la boîte apparaît, tourne, et change de couleur/anime au tap.
2. **Exercice 4 (marqueur personnalisé)** : pointer la caméra vers l'image utilisée pour générer `custom.patt` → une sphère jaune apparaît et lévite.
3. **Exercice 5 (QR + Hiro)** : ouvrir `https://anthimelh.github.io/TP01_WebAR/qr.html`, scanner le QR avec l'appareil photo (ouvre le site), puis repointer la caméra du navigateur sur ce même QR → la boîte AR s'affiche au centre.
4. **Exercice 6 (UI)** : en pointant vers le marqueur Hiro, un panneau apparaît en bas de l'écran avec un slider et 3 couleurs ; le manipuler met à jour la boîte en temps réel, et il disparaît dès que le marqueur sort du champ de la caméra.

**Astuces pour une bonne détection :**
- Bonne luminosité, éviter les reflets (papier mat plutôt que glacé/plastifié).
- Marqueurs imprimés à plat, non froissés, d'au moins 5-6 cm de côté.
- Téléphone tenu stable, à 20-40 cm du marqueur.
- La caméra du navigateur nécessite HTTPS (déjà le cas via GitHub Pages) et l'autorisation d'accès caméra pour le site.
