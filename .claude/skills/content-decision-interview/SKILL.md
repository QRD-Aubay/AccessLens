---
name: content-decision-interview
description: Interview the user about missing concrete content or business decisions, one question at a time in a friendly interactive format, with a suggested answer when one is reasonable. Use whenever an agent has identified gaps that need a human decision (missing copy, missing alt text, missing labels/messages, ambiguous choices) and must collect the user's decisions before producing a final deliverable. Not for open-ended plan/idea stress-testing (use `grilling` for that).
argument-hint: <liste-des-points-a-trancher>
user-invocable: false
---

# Content Decision Interview

Tu interviews l'utilisateur sur une liste de points concrets qui nécessitent une décision
humaine (contenu manquant, arbitrage métier, choix ambigu) avant qu'un autre agent ou toi-même
puisse produire un livrable final. Il n'y a **pas d'arbre de décision ni de rounds groupés** :
les points sont traités **un par un, séquentiellement**. Tu poses une question, tu attends la
réponse de l'utilisateur, tu l'appliques, puis seulement ensuite tu passes à la question
suivante.

## Entrée

Une liste de points à trancher, fournie par l'agent appelant. Pour chaque point tu dois avoir :
- un contexte court (où ça se trouve : fichier, élément, ticket, critère concerné…) ;
- si possible, une proposition de contenu/valeur déduite du contexte disponible ;
- si aucune proposition cohérente ne peut être déduite, ne force rien : indique-le.

## Déroulé — un point à la fois

1. Prends le premier point non encore traité de la liste.
2. Pose **une seule question** pour ce point, avec ce format :

   ```
   ❓ **Q<n>/<total>** - **<titre court : fichier / élément / critère concerné>**

   <question : ce qui manque et pourquoi une décision est nécessaire>

   💡 Proposition : <contenu concret proposé, ou "aucune proposition cohérente, à toi de choisir">

   Que veux-tu faire ?
   1. Valider cette proposition
   2. Fournir mon propre contenu
   3. Laisser l'agent choisir la meilleure option
   4. Ignorer ce point (non prioritaire)

   ➡️ Recommandation : <option recommandée, en général 1 si une proposition solide existe, sinon 3>
   ```

3. Utilise l'outil de questions interactif de l'environnement (ex: `vscode_askQuestions`) si
   disponible, pour poser **cette seule question** (avec les 4 options ci-dessus comme choix et
   la saisie libre autorisée pour l'option 2). Si un tel outil n'est pas disponible, pose-la en
   texte brut dans le chat en suivant le format ci-dessus.
4. **Attends la réponse de l'utilisateur avant de faire quoi que ce soit d'autre.** Ne
   présente jamais la question suivante avant d'avoir reçu et acté la réponse à la question en
   cours.
5. Une fois la réponse obtenue, passe au point suivant et répète depuis l'étape 1, jusqu'à ce
   que tous les points aient été traités.

## Règles

- **Un point à la fois, jamais en lot.** Même si tu as la liste complète des points en tête,
  n'en affiche et n'en pose qu'un seul par échange avec l'utilisateur.
- **Ne jamais forcer une proposition non cohérente.** S'il n'y a pas assez de contexte pour
  proposer un contenu crédible, dis-le explicitement et laisse le choix ouvert (options 2, 3, 4
  restent valables ; l'option 1 n'existe pas dans ce cas).
- **Restituer les décisions telles quelles** à l'agent appelant : pour chaque point, le contenu
  final retenu (valeur validée, valeur fournie par l'utilisateur, ou meilleur choix de l'agent si
  délégué), ou son statut "écarté" si l'utilisateur a choisi d'ignorer.
- Reste concis et lisible : pas de jargon technique inutile, une question = un point clair.

## Sortie attendue

Une fois l'utilisateur a répondu à toutes les questions, renvoie à l'agent appelant, pour
chaque point, un résumé structuré :

```
- <titre du point> : <DÉCISION> — <contenu retenu, ou "écarté par l'utilisateur">
```

où `DÉCISION` vaut `proposition validée`, `contenu utilisateur`, `choix de l'agent`, ou `écarté`.
