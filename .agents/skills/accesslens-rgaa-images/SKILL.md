---
name: accesslens-rgaa-images
description: Skill for auditing RGAA compliance of images in web content. Use this skill to generate analytical comments for Jira issues based on image accessibility criteria.
---

# Protocole d'audit RGAA — Thématique 1 : Images

Ce document indique à un agent comment **stress-tester une user story** contre les 9 critères de la thématique *Images* du RGAA 4.1.2.

Le référentiel machine-lisible est dans [rgaa-images-criteria.yaml](./referentiel/rgaa-images-criteria.yaml) 

---

## 1. Entrée attendue

Une **user story** (titre + description + critères d'acceptation), pouvant décrire :

- une nouvelle page/écran contenant des images,
- une modification d'un composant contenant des images (icônes, illustrations, graphiques, CAPTCHA, bouton image, image texte, image légendée…),
- ou tout contenu avec balise `<img>`, `<svg>`, `<canvas>`, `<object>`, `<embed>`, `<area>`, `<input type="image">`, ou `role="img"`.

## 2. Boucle d'audit

Pour **chaque critère** du fichier YAML (1.1 → 1.9), l'agent doit :

1. **Décider de l'applicabilité** en confrontant `applies_when` et `scope` à la story.
   Statut possible : `applicable`, `non_applicable`, `indéterminé`.
2. Si `applicable` ou `indéterminé`, pour **chaque test** du critère :
   1. Identifier le(s) `target` HTML/ARIA effectivement présent(s) dans la story.
   2. Évaluer chaque `condition` selon `logic` (`any` ou `all`).
   3. Statut du test : `conforme`, `non_conforme`, `information_manquante`.
3. Pour tout `information_manquante`, **formuler une question précise** à poser à l'auteur de la story (PO, designer, dev) pour lever le doute.
4. Consigner les `special_cases` et `technical_notes` pertinents.

## 3. Sortie attendue (Markdown)

L'agent produit un rapport structuré [OUTPUT-MARDOWN.md](./OUTPUT-MARDOWN.md).

## 4. Règles de décision

- **Ne jamais présumer** de la présence d'un `alt`, `aria-label`, `<figure>`, etc. Si la story ne le mentionne pas, statuer `information_manquante`.
- Une image **cliquable** relève à la fois de 1.1 (alternative) et, si applicable, de 6 (Liens) — signaler ce croisement hors périmètre.
- Une image **décorative avec légende** rend 1.2 non applicable ; vérifier 1.9.
- **CAPTCHA** : toujours vérifier 1.4 + 1.5 ensemble.
- **Image texte** : vérifier 1.8 puis, si mécanisme de remplacement, s'assurer qu'il satisfait 1.1/1.3.
- **SVG** décoratif via `<use>` : appliquer 1.2.4 au SVG référencé.

## 5. Format machine (optionnel)

En plus du Markdown, l'agent peut émettre un JSON miroir [OUTPUT-JSON.json](./OUTPUT-JSON.json)

## 6. Références

- Source : <https://accessibilite.numerique.gouv.fr/methode/criteres-et-tests/#1>
- WCAG 2.1 : 1.1.1 (A), 1.4.5 (AA), 4.1.2 (A)
- EN 301 549 V2.1.2 : 9.1.1.1, 9.1.4.5, 9.4.1.2
