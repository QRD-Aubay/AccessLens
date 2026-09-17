# Format de sortie — synthèse chat / commentaire Jira

Ce document définit le format que l'orchestrateur `accesslens` doit suivre pour son résumé
final (affiché à l'utilisateur et posté en commentaire Jira). Il ne remplace pas le fichier
`docs/accessibility/<TICKET-ID>-checklist.md` (qui reste une pure checklist actionnable pour le
développeur) : c'est un résumé synthétique et lisible du traitement effectué.

## Structure — cas normal

```markdown
## AccessLens — checklist accessibilité <TICKET-ID>

### Besoin métier
- <bullet point résumant le besoin, issu de accesslens-jira-analyst>
- <bullet point supplémentaire si utile>

### Analyse technique
- **<Catégorie> — **`<fichier>` : <changement concret identifié par accesslens-code-analyst>
- **<Catégorie> — **`<autre fichier>` : <changement concret>
- **<Catégorie> / hors périmètre** : <constat, ou "aucun élément détecté">

### Audit RGAA <thématique 1>
- Critères applicables : <liste des critères>.
- Non-conformités principales : <constats clés remontés par l'agent spécialisé>.
- Points indéterminés : <le cas échéant>.
- <point positif éventuel, ex: "Bouton « … » conforme (x.y)">.

<... répéter la section "Audit RGAA <thématique>" pour chaque agent spécialisé invoqué
(ex: "Audit RGAA formulaires", "Audit RGAA images") ...>

### Actions recommandées
- <action concrète 1, agrégée depuis les checklists des agents spécialisés>
- <action concrète 2>

Checklist détaillée générée dans `docs/accessibility/<TICKET-ID>-checklist.md`.
```

Règles de remplissage :

- **`### Besoin métier`** : synthèse courte, tirée telle quelle de la sortie de
  `accesslens-jira-analyst`. Pas de détail sur les tickets liés ou les docs externes consultés
  ici (ils restent dans le fichier Markdown détaillé si pertinent).
- **`### Analyse technique`** : une ligne par fichier/changement significatif identifié par
  `accesslens-code-analyst`, préfixée par sa catégorie (`Form`, `Image`). Si une catégorie n'a
  aucun changement, ne crée pas de ligne pour elle. Termine toujours par une ligne
  `**Image / hors périmètre**` ou `**Form / hors périmètre**` (adaptée aux catégories réellement
  hors périmètre rencontrées) si des éléments ont été explicitement écartés ; sinon indique
  `aucun élément détecté`.
- **`### Audit RGAA <thématique>`** : une section par agent spécialisé effectivement invoqué à
  l'étape 3 de l'orchestrateur (ex: `accesslens-rgaa-forms` → "Audit RGAA formulaires",
  `accesslens-rgaa-images` → "Audit RGAA images"). Si plusieurs agents ont été invoqués (ex:
  Form + Image sur le même ticket), répète cette section pour chacun, dans l'ordre où les
  catégories apparaissent dans l'analyse technique. N'invente jamais de critère non remonté par
  l'agent spécialisé correspondant.
- **`### Actions recommandées`** : liste courte et actionnable, agrégée depuis les
  recommandations de tous les agents spécialisés invoqués. Pas de doublons ; regroupe les
  actions similaires.
- La dernière ligne pointant vers le fichier détaillé est obligatoire et reste identique dans
  sa forme (`Checklist détaillée générée dans \`docs/accessibility/<TICKET-ID>-checklist.md\`.`).

## Cas particulier — aucun élément dans le périmètre du POC

Si `accesslens-code-analyst` ne remonte aucun changement `Form` ni `Image` (tout est classé
« hors périmètre » ou aucun changement identifié), n'invoque aucun agent spécialisé et remplace
les sections `### Audit RGAA …` et `### Actions recommandées` par :

```markdown
### Audit RGAA
- Aucun élément dans le périmètre du POC AccessLens (Form / Image) n'a été détecté sur ce
  ticket ; aucun agent spécialisé n'a été invoqué.

### Actions recommandées
- Aucune action d'accessibilité à mener dans le cadre de ce POC pour ce ticket.
```

Le fichier Markdown détaillé est quand même généré (avec la liste des éléments hors périmètre),
et la dernière ligne pointant vers lui reste présente.

## Cas particulier — arrêt anticipé

Si `accesslens-jira-analyst` ou `accesslens-code-analyst` retourne `STATUT: ARRÊT_ANTICIPÉ`,
n'utilise pas la structure ci-dessus. Utilise à la place :

```markdown
## AccessLens — <TICKET-ID> : analyse non applicable

Le traitement a été arrêté avant la fin de l'analyse : ce ticket ne semble pas concerner
l'accessibilité.

**Raison :** <RAISON renvoyée par l'agent qui a déclenché l'arrêt>

Aucun fichier de checklist n'a été généré et aucun commentaire détaillé n'a été posté sur le
ticket pour ce traitement.
```

Dans ce cas, ne génère pas de fichier `docs/accessibility/<TICKET-ID>-checklist.md` et ne mets
pas à jour le champ custom Jira. Le commentaire Jira (si un est posté, voir SKILL.md de
l'orchestrateur) doit reprendre ce même contenu court, sans les sections normales.

## Métriques d'exécution (chat uniquement)

Dans le résumé affiché à l'utilisateur dans le chat (jamais dans le commentaire Jira), ajoute
toujours, après le contenu ci-dessus, un bloc final :

```markdown
---
**Temps total de traitement :** <durée, ex: "1 min 42 s">
**Tokens consommés (estimation) :** <nombre approximatif, ex: "~18 000 tokens">
```

Si aucune mesure fiable n'est disponible, indique-le explicitement (ex: "non mesurable dans cet
environnement") plutôt que d'inventer une valeur précise.
