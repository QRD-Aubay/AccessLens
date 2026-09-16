# JSON Format

## Format
```json
{
  "story": "…",
  "criteria": [
    {
      "id": "11.1",
      "applicability": "applicable",
      "tests": [
        {
          "id": "11.1.1",
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
