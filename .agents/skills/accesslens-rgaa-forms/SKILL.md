---
name: accesslens-rgaa-forms
description: Skill for auditing RGAA compliance of forms in web content. Use this skill to generate analytical comments for Jira issues based on form accessibility criteria.
user-invocable: false
---

# Protocole d'audit RGAA — Thématique 11 : Formulaires

Ce document indique à un agent comment **stress-tester une user story** contre les 13 critères de la thématique *Formulaires* du RGAA 4.1.2.

Le référentiel machine-lisible est dans [rgaa-forms-criteria.yaml](./referentiel/rgaa-forms-criteria.yaml).

---

## 1. Entrée attendue

Une **user story** (titre + description + critères d'acceptation), pouvant décrire :

- une nouvelle page/écran contenant un ou plusieurs formulaires,
- une modification d'un composant de formulaire (champ, groupe, bouton, message d'erreur, contrôle de saisie…),
- ou tout contenu impliquant `<input>`, `<select>`, `<textarea>`, `<button>`, `<label>`, `<fieldset>`, `<legend>`, `<optgroup>`, `<option>`, ou les rôles ARIA associés (`role="group"`, `role="radiogroup"`, `role="checkbox"`, `role="radio"`, `role="switch"`, `role="listbox"`, `role="textbox"`, `role="combobox"`, `role="button"`…).

## 2. Boucle d'audit

Pour **chaque critère** du fichier YAML (11.1 → 11.13), l'agent doit :

1. **Décider de l'applicabilité** en confrontant `applies_when` et `scope` à la story.
   Statut possible : `applicable`, `non_applicable`, `indéterminé`.
2. Si `applicable` ou `indéterminé`, pour **chaque test** du critère :
   1. Identifier le(s) `target` HTML/ARIA effectivement présent(s) dans la story.
   2. Évaluer chaque `condition` selon `logic` (`any` ou `all`).
   3. Statut du test : `conforme`, `non_conforme`, `information_manquante`.
3. Pour tout `information_manquante`, **formuler une question précise** à poser à l'auteur de la story (PO, designer, dev) pour lever le doute.
4. Consigner les `special_cases` et `technical_notes` pertinents.

## 3. Sortie attendue (Markdown)

L'agent produit un rapport structuré [OUTPUT-MARKDOWN.md](./OUTPUT-MARKDOWN.md).

## 4. Règles de décision

- **Ne jamais présumer** de la présence d'un `<label for>`, `aria-label`, `<fieldset>`, `<legend>`, `required`, `aria-required`, `autocomplete`, etc. Si la story ne le mentionne pas, statuer `information_manquante`.
- Un **bouton image** (`<input type="image">`) relève à la fois de 11.9 (intitulé) et de la thématique 1 (Images) — signaler ce croisement hors périmètre.
- Un **champ obligatoire** déclenche toujours 11.10.1 et 11.10.2 conjointement.
- Un formulaire **modifiant/supprimant des données sensibles** (financier, juridique, personnel) déclenche 11.12.1 ET 11.12.2.
- Un **regroupement de champs de même nature** (radios, checkboxes, adresses multi-champs) déclenche 11.5, 11.6 et 11.7 en cascade.
- Une **liste de choix** (`<select>`) avec familles d'options déclenche 11.8.
- Tout champ correspondant à une **donnée utilisateur standard** (nom, prénom, email, adresse, téléphone, CB…) déclenche 11.13.
- Un **intitulé visible** différent du nom accessible déclenche 11.2.5 et 11.9.2 (label in name).
- Les **étiquettes répétées** dans la page ou l'ensemble de pages déclenchent 11.3.

## 5. Format machine (optionnel)

En plus du Markdown, l'agent peut émettre un JSON miroir [OUTPUT-JSON.md](./OUTPUT-JSON.md).

## 6. Références

- Source : <https://accessibilite.numerique.gouv.fr/methode/criteres-et-tests/#11>
- WCAG 2.1 : 1.3.1 (A), 1.3.5 (AA), 2.4.6 (AA), 2.5.3 (A), 3.2.4 (AA), 3.3.1 (A), 3.3.2 (A), 3.3.3 (AA), 3.3.4 (AA), 4.1.2 (A)
- EN 301 549 V2.1.2 : 9.1.3.1, 9.1.3.5, 9.2.4.6, 9.2.5.3, 9.3.2.4, 9.3.3.1, 9.3.3.2, 9.3.3.3, 9.3.3.4, 9.4.1.2
