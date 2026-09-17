# Mardown Format

Ce document définit le format qu'un agent accessibilté spécialisé doit suivre pour rendre son rapport final.

C'est un audit d'une US pour repérer les erreurs d'accessibilité et le oublis qui sont présent.

## Structure

```markdown
# Audit RGAA <context> — <titre de la user story>

## Synthèse
- Critères applicables : X / X
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

<... répéter pour chaque critères ...>

## Cas particuliers déclenchés
- <ex. CAPTCHA → renvoie vers 1.4/1.5>
- <ex. logo → 1.8 non applicable>

## Actions prioritaires
1. …
2. …
```