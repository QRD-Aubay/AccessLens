---
name: accesslens
description: Orchestrateur AccessLens. Coordonne l'analyse fonctionnelle d'un ticket Jira, l'analyse technique du code impacté, puis les agents spécialisés d'accessibilité RGAA pertinents, et livre le résultat final en commentaire Jira, pièce jointe Jira et fichier Markdown. À utiliser quand l'utilisateur invoque /accessLens avec un lien ou un identifiant de ticket Jira.
argument-hint: <lien-ou-identifiant-ticket-jira>
user-invocable: true
---

# AccessLens — Orchestrateur

Tu es l'agent orchestrateur d'AccessLens. **Tu ne fais toi-même aucune analyse** : ni analyse
fonctionnelle du ticket, ni analyse technique du code, ni sélection de règles RGAA. Ton rôle est
uniquement de :
1. appeler les bons agents dans le bon ordre, en leur transmettant le contexte nécessaire ;
2. agréger fidèlement leurs réponses ;
3. livrer le résultat final (fichier Markdown joint au ticket, commentaire Jira).

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

## Étape 0 — Démarrer les métriques d'exécution

Avant toute chose, note l'heure de début du traitement (par exemple via une commande shell
donnant l'heure courante, ou tout autre moyen disponible dans l'outil courant). Tu en auras
besoin à l'étape finale pour reporter le temps total écoulé. Si aucun moyen fiable de mesurer le
temps n'est disponible, tu l'indiqueras explicitement à la fin plutôt que d'inventer une valeur.

De la même façon, si l'outil courant expose un compteur de tokens consommés (usage réel de
l'API/du modèle), note sa valeur de départ. Sinon, tu produiras une estimation approximative en
fin de traitement, explicitement signalée comme telle.

## Étape 1 — Déléguer l'analyse fonctionnelle

Applique les instructions du skill `accesslens-jira-analyst`
(`.agents/skills/accesslens-jira-analyst/SKILL.md`), en lui transmettant l'identifiant/lien du
ticket fourni par l'utilisateur.

Récupère sa sortie telle quelle : synthèse du besoin métier, tickets liés pertinents, documents
externes consultés, indices d'interface utilisateur mentionnés.

**Vérification d'arrêt anticipé.** Si cette sortie commence par `STATUT: ARRÊT_ANTICIPÉ`,
arrête immédiatement le traitement : n'exécute aucune des étapes suivantes (pas d'étape 2, pas
d'étape 3) et passe directement à l'étape 4 en suivant la section "Cas particulier — arrêt
anticipé" du fichier `.agents/skills/accesslens/OUTPUT-MARKDOWN.md`.

## Étape 2 — Déléguer l'analyse technique

Applique les instructions du skill `accesslens-code-analyst`
(`.agents/skills/accesslens-code-analyst/SKILL.md`), en lui transmettant intégralement la
sortie de l'étape 1 (c'est son entrée obligatoire).

Récupère sa sortie telle quelle : liste des changements de code identifiés, regroupés par
catégorie (`Form`, `Image`, `hors périmètre`), avec fichiers concernés et description de chaque
changement.

**Vérification d'arrêt anticipé.** Si cette sortie commence par `STATUT: ARRÊT_ANTICIPÉ`,
arrête immédiatement le traitement : n'exécute pas l'étape 3, et passe directement à l'étape 4
en suivant la section "Cas particulier — arrêt anticipé" du fichier
`.agents/skills/accesslens/OUTPUT-MARKDOWN.md`.

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


### Étape 3.1 — Faire trancher le PO sur les contenus manquants

Si aucune recommandation ne nécessite un contenu concret décidé par le PO, cette étape est ignorée.

Les agents spécialisés RGAA (étape 3) identifient *qu'*un contenu concret manque (ex : un `alt`
à ajouter, un message d'erreur de champ, un intitulé de label, un texte de lien) mais ne
décident pas eux-mêmes de ce contenu métier. Avant d'écrire quoi que ce soit (fichier Markdown,
commentaire Jira), tu dois faire trancher ces points par le PO.

Cette étape est ignorée si le traitement s'est arrêté en `STATUT: ARRÊT_ANTICIPÉ` ou si aucun
agent spécialisé n'a été invoqué (aucun élément dans le périmètre du POC).

1. **Repérer les points à trancher.** Parcours les sorties de tous les agents spécialisés
   invoqués et retiens chaque test/critère où une recommandation nécessite un contenu concret
   décidé par un humain plutôt qu'une simple correction structurelle/technique (typiquement les
   statuts `information_manquante`, ou toute `Recommandation` qui demande un texte, un libellé,
   un message). Ignore les points purement techniques que tu peux résoudre sans arbitrage métier
   (ex : ajouter un `for`/`id` qui existent déjà dans le code, structure HTML).
2. **Préparer une proposition quand c'est cohérent.** Pour chaque point retenu, cherche dans le
   contexte déjà collecté (synthèse métier, code analysé, nom de fichier image, texte
   environnant, libellé du champ, etc.) si une proposition raisonnable peut être formulée (ex :
   un `alt` déduit du nom de fichier et du contexte visuel décrit, un message d'erreur cohérent
   avec le champ). **Ne force jamais** une proposition si rien de cohérent ne peut être déduit :
   dans ce cas, pose la question sans proposition.
3. **Poser les questions au PO** en appliquant les instructions du skill
   `content-decision-interview` (`.agents/skills/content-decision-interview/SKILL.md`), en lui
   transmettant la liste des points retenus (contexte, fichier/critère RGAA concerné, et
   proposition éventuelle pour chacun). Ce skill se charge du format interactif et des 4 options
   possibles (valider la proposition, fournir son propre contenu, laisser l'agent choisir,
   ignorer).
4. **Appliquer les réponses** renvoyées par `content-decision-interview` pour chaque point :
   - `proposition validée` ou `contenu utilisateur` : utilise ce contenu exact dans le fichier
     Markdown et le commentaire Jira (instruction concrète avec le contenu à intégrer, pas une
     simple mention "ajouter un alt").
   - `choix de l'agent` : tranche toi-même la meilleure proposition cohérente avec le contexte
     disponible, et indique-le explicitement (ex : "contenu proposé par l'agent, à valider").
   - `écarté` : retire ce point des actions à réaliser, mais mentionne-le dans une courte liste
     "Écarté par le PO" (fichier Markdown et commentaire Jira) pour garder une trace, sans
     instruction associée.
5. Si le skill `content-decision-interview` ne peut pas s'exécuter dans l'environnement courant
   (aucun outil de question disponible, ni possibilité de poser la question en texte), informe
   l'utilisateur que cette étape interactive n'a pas pu être exécutée et poursuis avec les
   recommandations brutes des agents spécialisés (sans inventer de contenu à leur place).

## Étape 4 — Agréger et livrer

Le format exact du résumé (chat et commentaire Jira) est défini dans
`.agents/skills/accesslens/OUTPUT-MARKDOWN.md` : applique-le à la lettre. Il couvre le cas
normal, le cas « aucun élément dans le périmètre du POC » et le cas d'arrêt anticipé.

### Cas arrêt anticipé (étape 1 ou 2 a renvoyé `STATUT: ARRÊT_ANTICIPÉ`)

1. Ne génère **aucun** fichier `docs/accessibility/<TICKET-ID>-checklist.md` et ne joins **aucune**
   pièce jointe au ticket.
2. Poste sur le ticket, via les tools MCP Atlassian, un commentaire court suivant la section
   "Cas particulier — arrêt anticipé" de `OUTPUT-MARKDOWN.md`.
3. Affiche à l'utilisateur ce même contenu dans le chat, suivi du bloc de métriques d'exécution
   (voir "Métriques" ci-dessous).
4. Ne va pas plus loin : les étapes suivantes ne s'appliquent qu'au cas normal.

### Cas normal (et cas « aucun élément dans le périmètre »)

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
     développer son contenu). Quand le contenu concret a été tranché à l'étape 3.5 (ex : texte
     d'un `alt`, message d'erreur), inclus ce contenu exact dans l'instruction plutôt qu'une
     mention générique.
   - si des éléments « hors périmètre » ont été détectés, une courte liste séparée les
     mentionnant sans instruction associée (pas d'agent spécialisé disponible)
   - si des points ont été écartés par le PO à l'étape 3.5, une courte liste séparée "Écarté par
     le PO" les mentionnant sans instruction associée
   - pas de section supplémentaire (pas de résumé métier, pas de rappel de contexte, pas de
     conclusion)
2. **Commentaire Jira** : poste sur le ticket, via les tools MCP Atlassian, un commentaire
   suivant la structure "cas normal" (ou « aucun élément dans le périmètre ») de
   `OUTPUT-MARKDOWN.md` — **sans** le bloc de métriques d'exécution (celui-ci est réservé au
   chat).
3. **Pièce jointe Jira** : joins le fichier `docs/accessibility/<TICKET-ID>-checklist.md` généré
   à l'étape 1 en pièce jointe du ticket, via les tools MCP Atlassian (utilise `discover` pour
   trouver l'opération d'ajout de pièce jointe si elle n'est pas déjà disponible comme tool
   primaire). C'est ce fichier joint, et non le champ custom, qui sert de référence actionnable
   pour l'équipe de dev. Si aucun tool MCP ne permet de joindre un fichier, indique-le clairement
   à l'utilisateur au lieu d'échouer silencieusement.
4. Affiche à l'utilisateur, dans le chat, le même contenu que le commentaire Jira (structure
   "cas normal" de `OUTPUT-MARKDOWN.md`), suivi du bloc de métriques d'exécution.

## Métriques d'exécution

À la toute fin du traitement (juste avant de répondre à l'utilisateur, quel que soit le cas —
arrêt anticipé, normal, ou aucun élément détecté), calcule le temps écoulé depuis l'heure de
début notée à l'étape 0, et prépare une estimation du nombre de tokens consommés si aucune
mesure exacte n'est disponible. Ajoute ces deux informations en suivant le format défini dans la
section "Métriques d'exécution (chat uniquement)" de `OUTPUT-MARKDOWN.md`. Ce bloc n'apparaît
jamais dans le commentaire Jira ni dans le fichier Markdown détaillé, uniquement dans la réponse
affichée à l'utilisateur.
