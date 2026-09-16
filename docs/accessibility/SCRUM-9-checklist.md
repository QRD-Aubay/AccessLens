# Checklist accessibilité — SCRUM-9

## Besoin métier

Le formulaire de contact permet actuellement de renseigner le nom complet, l’adresse email, le sujet de la demande et un message décrivant le projet de voyage. Afin de mieux qualifier les demandes reçues par l’agence, il doit permettre la saisie facultative d’un numéro de téléphone. Le numéro doit pouvoir être saisi dans un format libre, national ou international, sans empêcher la soumission du formulaire lorsqu’il est absent. Le nouveau champ doit rester visuellement cohérent avec les champs existants.

## Ticket(s) lié(s) pertinents

Aucun ticket lié pertinent identifié.

## Documents externes consultés

Aucun.

## Indices d’interface utilisateur mentionnés

- Formulaire de contact
- Champ « Nom complet »
- Champ « Adresse email »
- Nouveau champ « Telephone »
- Champ « Sujet de la demande »
- Champ de message libre décrivant le projet de voyage
- Positionnement du champ « Telephone » après « Adresse email »
- Cohérence visuelle avec les champs existants

## Changements de code identifiés

### Form

- **Fichier :** `Webapp/index.html`
  - Ajouter, immédiatement après le champ `email`, un champ de téléphone facultatif au sein de `.form-grid`.
  - Utiliser `type="tel"`, un nom de soumission explicite tel que `phone`, et le libellé visuel attendu « Telephone ».
  - Ne pas ajouter l’attribut `required` ni de contrainte de format afin d’accepter une saisie nationale ou internationale libre et de permettre une soumission sans numéro.
  - Extrait existant :
    ```html
    <div class="form-grid">
      <input type="text" name="name" placeholder="Nom complet">
      <input type="email" name="email" placeholder="Adresse email">
    </div>
    ```

- **Fichier :** `Webapp/styles.css`
  - Aucun ajout de style nécessaire : les règles existantes `.contact-form input` appliquent déjà largeur, bordure, rayon, espacement, police, couleur et fond à tout nouveau `<input>`.
  - Le champ téléphone héritera donc de la cohérence visuelle demandée, y compris de la disposition responsive de `.form-grid`.

### Image

Aucun changement identifié.

### Hors périmètre du POC AccessLens

Aucun changement identifié.

## Audit RGAA — Thématique Formulaires

### Synthèse

- Critères applicables : 4 / 13 (11.1, 11.9, 11.10, 11.13)
- Critères indéterminés : 3 / 13 (11.2, 11.4, 11.11)
- Critères non applicables : 6 / 13 (11.3, 11.5, 11.6, 11.7, 11.8, 11.12)
- Tests conformes : 2 (11.9.1, 11.9.2)
- Tests non conformes : 2 (11.1.1, 11.13.1)
- Tests avec informations manquantes : 7 (11.2.1–11.2.5, 11.4.1–11.4.3, 11.10.5/6/7 partiel, 11.11.1, 11.11.2)

> Constat transverse : le formulaire (`Webapp/index.html`, lignes 126-134) n’utilise aucun `<label>`, `aria-label`, `aria-labelledby` ni `title` sur ses champs, uniquement des attributs `placeholder`. Le nouveau champ `phone` reproduit exactement ce même pattern (`placeholder="Telephone"`), donc il hérite du même défaut de conformité que les champs existants plutôt que de le corriger.

### Critère 11.1 — Chaque champ de formulaire a-t-il une étiquette ?

- Applicabilité : **applicable**
- Test 11.1.1 : **non conforme**. Aucun mécanisme accepté (`aria-labelledby`, `aria-label`, `<label for>`, `title`, bouton adjacent) n’est présent. Le `placeholder` n’est pas un mécanisme d’étiquetage valide.
- Recommandation : ajouter un `<label for="phone">Téléphone</label>` (ou `aria-label="Téléphone"`) lié par `id="phone"`/`for="phone"`, et corriger de la même façon les quatre champs existants pour la conformité globale du formulaire.

### Critère 11.2 — Chaque étiquette associée à un champ est-elle pertinente ?

- Applicabilité : **indéterminé**.
- Les tests 11.2.1 à 11.2.4 sont en **information manquante** : la story prévoit un libellé visuel « Telephone », mais celui-ci est implémenté comme `placeholder`, pas comme étiquette accessible.
- Le test 11.2.5 est en **information manquante** : il dépend de l’implémentation du futur label.
- Question à lever : le texte sera-t-il « Téléphone » ou « Telephone » ? Recommandation : utiliser « Téléphone » comme texte du label et préciser le caractère facultatif selon la convention du formulaire.

### Critère 11.3 — Étiquettes répétées cohérentes

**Non applicable.** Aucun formulaire ou champ de même nature répété n’est identifié.

### Critère 11.4 — Étiquette et champ accolés

- Applicabilité : **indéterminé**.
- Les tests 11.4.1 et 11.4.2 sont en **information manquante** : aucun `<label>` n’existe actuellement et la disposition CSS du futur label dans `.form-grid` n’est pas décrite.
- Le test 11.4.3 est **non applicable** : le champ est de type `tel`, pas une case à cocher, un bouton radio ou un switch.
- Recommandation : vérifier ou prévoir explicitement le positionnement visuel du futur label au-dessus ou à gauche du champ.

### Critère 11.5 — Regroupement des champs de même nature

**Non applicable.** Le champ téléphone est un champ unique, sans groupe de radios, cases à cocher ou sous-champs multiples.

### Critère 11.6 — Regroupement pourvu d’un moyen de compréhension

**Non applicable.** Aucun regroupement déclenché par le critère 11.5.

### Critère 11.7 — Pertinence de la légende de regroupement

**Non applicable.** Aucun `<fieldset>` ou regroupement concerné.

### Critère 11.8 — Regroupement des items d’une liste de choix

**Non applicable.** Aucun `<select>` n’est présent ou modifié.

### Critère 11.9 — Intitulé de bouton pertinent

- Applicabilité : **applicable**.
- Test 11.9.1 : **conforme**. « Envoyer la demande » décrit clairement l’action.
- Test 11.9.2 : **conforme**. Le nom accessible correspond au contenu visible.

### Critère 11.10 — Contrôle de saisie pertinent

- Applicabilité : **applicable** (sous-parties mixtes).
- Les tests 11.10.1 à 11.10.4 sont **non applicables** selon les informations fournies : aucun champ n’est marqué `required` ou `aria-required="true"`.
- Les tests 11.10.5 à 11.10.7 sont en **information manquante** : `type="email"` impose une contrainte native sans instruction visible dédiée ; pour `phone`, l’absence de contrainte de format est intentionnelle.
- Question à lever : les champs existants sont-ils réellement facultatifs côté serveur, ou l’absence de `required` HTML est-elle un oubli préexistant ?

### Critère 11.11 — Suggestions facilitant la correction d’erreurs

- Applicabilité : **indéterminé**.
- Les tests 11.11.1 et 11.11.2 sont en **information manquante** : les messages d’erreur ou la validation des autres champs, notamment `email`, ne sont pas décrits.
- Question à lever : le formulaire affiche-t-il des messages d’erreur personnalisés ou repose-t-il uniquement sur la validation native ? Aucun message d’erreur n’est prévu pour `phone`, ce qui est cohérent avec l’absence de contrainte de format.

### Critère 11.12 — Récupération/modification des données sensibles

**Non applicable.** Le formulaire crée une demande de contact et ne modifie ni ne supprime de données existantes.

### Critère 11.13 — Déduction de la finalité du champ (`autocomplete`)

- Applicabilité : **applicable**.
- Test 11.13.1 : **non conforme**. Aucun attribut `autocomplete` n’est présent sur `name`, `email` ni prévu sur `phone`.
- Recommandation : ajouter `autocomplete="tel"` sur le champ téléphone et, pour la conformité globale du formulaire, `autocomplete="name"` et `autocomplete="email"` sur les champs existants.

## Instructions pour le développeur / agent de dev

1. Dans `Webapp/index.html`, ajouter le champ `phone` juste après `email`, avec `type="tel"`, `name="phone"`, sans `required`, sans `pattern` et sans contrainte de format.
2. Ajouter un véritable `<label>` associé par `for`/`id` au champ téléphone ; idéalement corriger aussi les quatre champs existants qui utilisent actuellement uniquement `placeholder`.
3. Utiliser « Téléphone » comme intitulé accessible et visible, et expliciter le caractère facultatif selon la convention retenue pour les autres champs.
4. Ajouter `autocomplete="tel"` à `phone`, ainsi que `autocomplete="name"` et `autocomplete="email"` aux champs correspondants existants.
5. Vérifier la disposition du label dans `.form-grid`. Les styles existants des inputs devraient conserver la cohérence visuelle, mais le positionnement des labels doit être contrôlé.
6. Vérifier le comportement des messages d’erreur et des contraintes existantes, notamment pour `email`, sans ajouter de contrainte de format au téléphone.
