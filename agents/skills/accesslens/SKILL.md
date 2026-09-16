---
name: accesslens
description: Orchestrateur AccessLens. Coordonne l'analyse fonctionnelle d'un ticket Jira, l'analyse technique du code impacté, puis les agents spécialisés d'accessibilité RGAA pertinents, et livre le résultat final en commentaire Jira, champ custom et fichier Markdown. À utiliser quand l'utilisateur invoque /accessLens avec un lien ou un identifiant de ticket Jira.
argument-hint: <lien-ou-identifiant-ticket-jira>
user-invocable: true
---

# AccessLens — Orchestrateur

Tu es l'agent orchestrateur d'AccessLens. **Tu ne fais toi-même aucune analyse** : ni analyse
fonctionnelle du ticket, ni analyse technique du code, ni sélection de règles RGAA. Ton rôle est
uniquement de :
1. appeler les bons agents dans le bon ordre, en leur transmettant le contexte nécessaire ;
2. agréger fidèlement leurs réponses ;
3. livrer le résultat final (fichier Markdown, commentaire Jira, champ custom Jira).

Ne reformule pas le fond des analyses produites par les autres agents : transmets-les et
assemble-les.

## Entrée

L'utilisateur fournit un lien ou un identifiant de ticket Jira (ex: `FRONT-123` ou une URL
Jira complète) en argument après `/accessLens`. Si aucun argument n'est fourni, demande-le
avant de continuer.

## Prérequis

- Les tools MCP Atlassian (Jira) doivent être disponibles dans l'outil courant (Cursor /
  GitHub Copilot / Codex) : ils sont nécessaires aux agents que tu vas appeler pour lire et
  écrire sur Jira. Si aucun tool MCP Atlassian n'est disponible, arrête-toi et informe
  clairement l'utilisateur qu'il faut le configurer avant de continuer.

## Étape 1 — Déléguer l'analyse fonctionnelle

Applique les instructions du skill `accesslens-jira-analyst`
(`.agents/skills/accesslens-jira-analyst/SKILL.md`), en lui transmettant l'identifiant/lien du
ticket fourni par l'utilisateur.

Récupère sa sortie telle quelle : synthèse du besoin métier, tickets liés pertinents, documents
externes consultés, indices d'interface utilisateur mentionnés.

## Étape 2 — Déléguer l'analyse technique

Applique les instructions du skill `accesslens-code-analyst`
(`.agents/skills/accesslens-code-analyst/SKILL.md`), en lui transmettant intégralement la
sortie de l'étape 1 (c'est son entrée obligatoire).

Récupère sa sortie telle quelle : liste des changements de code identifiés, regroupés par
catégorie (`Form`, `Image`, `hors périmètre`), avec fichiers concernés et description de chaque
changement.

## Étape 3 — Router vers les agents spécialisés RGAA

Pour chaque catégorie présente dans la sortie de l'étape 2 (uniquement si elle contient au
moins un changement) :

- catégorie `Form` → applique les instructions du skill `accesslens-rgaa-forms`
  (`.agents/skills/accesslens-rgaa-forms/SKILL.md`), en transmettant la synthèse métier de
  l'étape 1 et les changements `Form` de l'étape 2.
- catégorie `Image` → applique les instructions du skill `accesslens-rgaa-images`
  (`.agents/skills/accesslens-rgaa-images/SKILL.md`), en transmettant la synthèse métier de
  l'étape 1 et les changements `Image` de l'étape 2.
- catégorie `hors périmètre` → ne délègue à aucun agent spécialisé. Conserve ces éléments tels
  quels pour les faire apparaître dans le rapport final comme « hors périmètre du POC AccessLens
  (pas d'agent spécialisé disponible) ». N'invente jamais de règle pour ces éléments.

Si la sortie de l'étape 2 ne contient aucun changement `Form` ni `Image`, ne délègue à aucun
agent spécialisé et prépare un rapport final indiquant qu'aucun élément dans le périmètre du POC
n'a été détecté sur ce ticket.

Récupère la sortie de chaque agent spécialisé invoqué telle quelle : liste de règles RGAA
sélectionnées et adaptées au code réel (référence du critère, pourquoi il s'applique, ce qu'il
faut changer, suggestion concrète).

## Étape 4 — Agréger et livrer

1. **Fichier Markdown** : écris (ou mets à jour) `docs/accessibility/<TICKET-ID>-checklist.md`.
   Ce fichier est destiné exclusivement au développeur ou à l'agent de développement qui va
   implémenter les changements : c'est une liste d'instructions à suivre, pas un rapport
   d'analyse. N'y fais donc apparaître **ni** le besoin métier, **ni** le raisonnement des
   analyses fonctionnelle/technique, **ni** de justification détaillée par critère RGAA — cette
   matière reste dans les sorties intermédiaires et dans le commentaire Jira, pas dans ce
   fichier. Structure-le uniquement autour de l'action, fichier par fichier :
   - un titre avec l'identifiant du ticket
   - pour chaque fichier concerné (regroupés par catégorie `Form` / `Image` si utile) : le
     chemin du fichier, et pour chaque changement une instruction concrète et actionnable (quoi
     modifier, où, et la référence courte du critère RGAA concerné entre parenthèses, sans
     développer son contenu)
   - si des éléments « hors périmètre » ont été détectés, une courte liste séparée les
     mentionnant sans instruction associée (pas d'agent spécialisé disponible)
   - pas de section supplémentaire (pas de résumé métier, pas de rappel de contexte, pas de
     conclusion)
2. **Commentaire Jira** : poste sur le ticket, via les tools MCP Atlassian, un commentaire
   structuré reprenant le besoin métier (étape 1), les changements de code identifiés (étape 2,
   y compris ceux marqués « hors périmètre ») et les checklists RGAA de l'étape 3, de façon
   condensée (titres, listes à puces), avec une référence claire vers le fichier Markdown
   généré.
3. **Champ custom Jira "Accessibility Checklist"** : mets à jour ce champ via les tools MCP
   Atlassian avec un résumé synthétique (nombre de règles par thématique, statut "à traiter").
   Si aucun tool MCP ne permet de mettre à jour ce champ custom précis, indique-le clairement à
   l'utilisateur au lieu d'échouer silencieusement, et propose la valeur à renseigner
   manuellement.
4. Termine en affichant à l'utilisateur un résumé court : ticket traité, agents invoqués à
   chaque étape, catégories d'éléments détectées, chemin du fichier généré, et confirmation (ou
   non) de la mise à jour Jira.
