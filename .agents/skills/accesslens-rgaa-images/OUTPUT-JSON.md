# JSON Format

## Format
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