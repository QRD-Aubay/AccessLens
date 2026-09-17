# SCRUM-22 — Checklist accessibilité

## Form

### Webapp/index.html
- Ajouter une nouvelle section après `contact-section` (après la ligne 141, avant `</main>`) contenant la zone d'inscription newsletter.
- Texte de présentation à inclure : « Recevez chaque mois nos inspirations voyage et offres exclusives directement dans votre boîte mail. »
- Ajouter un `<label for="newsletter-email">Adresse email</label>` visible, positionné immédiatement au-dessus ou à gauche du champ (11.1, 11.2, 11.3, 11.4).
- Ajouter le champ `<input type="email" id="newsletter-email" name="newsletter-email" autocomplete="email">` (11.13).
- Ajouter un bouton avec l'intitulé exact « S'inscrire à la newsletter » (11.9).
- Ajouter une instruction de format visible avant validation : « Format attendu : nom@domaine.fr » (11.10.5, 11.11).
- Implémenter un message d'erreur visible en cas d'adresse invalide : « Veuillez saisir une adresse email valide (exemple : nom@domaine.fr). », associé au champ via `aria-describedby` et avec `aria-invalid="true"` posé sur le champ (11.10.6, 11.10.7, 11.11).
- Implémenter un message en cas de soumission avec champ vide : « Veuillez renseigner votre adresse email. » (11.10.3).
- Implémenter le message de confirmation après inscription réussie : « Merci ! Votre inscription à la newsletter a bien été prise en compte. » (11.10).

## Hors périmètre
- `Webapp/styles.css` : ajout de styles pour la mise en cohérence visuelle de la nouvelle section newsletter (pas d'agent spécialisé disponible dans le POC AccessLens).
