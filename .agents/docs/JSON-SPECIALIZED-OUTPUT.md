# JSON SPECIALIZED OUTPUT Format

Ce document définit le format qu'un agent accessibilté spécialisé doit suivre pour rendre son rapport final.

C'est un audit d'une US pour repérer les erreurs d'accessibilité et le oublis qui sont présent.


## Format
```json
{
  "story": "…",
  "criteria": [
    {
      "id": "<criteria-id>",
      "applicability": "applicable/non applicable",
      "tests": [
        {
          "id": "<test-id>",
          "status": "information_manquante",
          "evidence": "La story mentionne « un champ email » sans préciser la présence d'un <label> associé.",
          "questions": ["Le champ email dispose-t-il d'un <label for> associé, ou d'un aria-label / aria-labelledby ?"],
          "recommendation": "Associer un <label for=\"email\"> visible et pertinent, ou fournir un aria-label équivalent."
        }
      ]
    }
  ]
}
```
