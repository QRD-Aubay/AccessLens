# Protocole d'audit RGAA — Thématique 1 : Images

Ce document indique à un agent comment **stress-tester une user story** contre les 9 critères de la thématique *Images* du RGAA 4.1.2.

Le référentiel machine-lisible est dans `rgaa-images-criteria.yaml` (même dossier).

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

L'agent produit un rapport structuré :

```markdown
# Audit RGAA Images — <titre de la user story>

## Synthèse
- Critères applicables : X / 9
- Conformes : …
- Non conformes : …
- Informations manquantes : …

## Détail par critère

### Critère 1.1 — <question>
- Applicabilité : applicable | non_applicable | indéterminé
- Justification : <phrase courte reliée à la story>

#### Test 1.1.1 — <target>
- Statut : conforme | non_conforme | information_manquante
- Constat : <observation issue de la story>
- Preuve / extrait : <citation de la story ou du code décrit>
- Questions à lever : <si information_manquante>
- Recommandation : <action concrète : attribut à ajouter, mécanisme à prévoir…>

<... répéter pour chaque test ...>

<... répéter pour chaque critère 1.2 → 1.9 ...>

## Cas particuliers déclenchés
- <ex. CAPTCHA → renvoie vers 1.4/1.5>
- <ex. logo → 1.8 non applicable>

## Actions prioritaires
1. …
2. …
```

## 4. Règles de décision

- **Ne jamais présumer** de la présence d'un `alt`, `aria-label`, `<figure>`, etc. Si la story ne le mentionne pas, statuer `information_manquante`.
- Une image **cliquable** relève à la fois de 1.1 (alternative) et, si applicable, de 6 (Liens) — signaler ce croisement hors périmètre.
- Une image **décorative avec légende** rend 1.2 non applicable ; vérifier 1.9.
- **CAPTCHA** : toujours vérifier 1.4 + 1.5 ensemble.
- **Image texte** : vérifier 1.8 puis, si mécanisme de remplacement, s'assurer qu'il satisfait 1.1/1.3.
- **SVG** décoratif via `<use>` : appliquer 1.2.4 au SVG référencé.

## 5. Format machine (optionnel)

En plus du Markdown, l'agent peut émettre un JSON miroir :

```json
{
  "story": "…",
  "criteria": [
    {
      "id": "1.1",
      "applicability": "applicable",
      "tests": [
        {
          "id": "1.1.1",
          "status": "information_manquante",
          "evidence": "La story mentionne « une illustration » sans préciser d'alternative textuelle.",
          "questions": ["Quelle alternative textuelle porte l'illustration ?"],
          "recommendation": "Prévoir un attribut alt pertinent ou aria-label."
        }
      ]
    }
  ]
}
```

## 6. Références

- Source : <https://accessibilite.numerique.gouv.fr/methode/criteres-et-tests/#1>
- WCAG 2.1 : 1.1.1 (A), 1.4.5 (AA), 4.1.2 (A)
- EN 301 549 V2.1.2 : 9.1.1.1, 9.1.4.5, 9.4.1.2
