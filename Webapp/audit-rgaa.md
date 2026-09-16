# Audit RGAA - Non-conformites identifiees

Ce document recense les problemes d'accessibilite volontairement presents dans la page [index.html](C:/Users/Hackathon_user/Desktop/Projet_Hackathon/AccessLens/Webapp/index.html), en reference au RGAA 4.1.2.

## Perimetre

- Page analysee : [index.html](C:/Users/Hackathon_user/Desktop/Projet_Hackathon/AccessLens/Webapp/index.html)
- Feuille de style analysee : [styles.css](C:/Users/Hackathon_user/Desktop/Projet_Hackathon/AccessLens/Webapp/styles.css)
- Audit cible sur les non-conformites observables dans la version actuelle de la page

## Synthese

Les non-conformites se concentrent principalement sur :

- les alternatives textuelles des images du carrousel ;
- l'usage de controles de formulaire pour piloter le carrousel ;
- l'absence d'etiquettes de champs dans le formulaire de contact ;
- des contrastes insuffisants sur plusieurs textes et composants ;
- l'absence d'indices semantiques utiles pour l'autocompletion.

## Detail des non-conformites

| Critere RGAA | Statut | Constat |
| --- | --- | --- |
| 1.1 - Chaque image porteuse d'information a-t-elle une alternative textuelle ? | Non conforme | La premiere image du carrousel est informative mais son attribut `alt` est vide. |
| 1.3 - Pour chaque image porteuse d'information ayant une alternative textuelle, cette alternative est-elle pertinente ? | Non conforme | Les deux autres images du carrousel ont des alternatives trop generiques (`photo de voyage`, `image`) qui ne restituent pas l'information utile. |
| 3.2 - Dans chaque page web, le contraste entre la couleur du texte et la couleur de son arriere-plan est-il suffisamment eleve ? | Non conforme | Les placeholders du formulaire et le texte des boutons du carrousel / formulaire n'atteignent pas un contraste suffisant. |
| 3.3 - Dans chaque page web, les couleurs utilisees dans les composants d'interface ou les elements graphiques porteurs d'informations sont-elles suffisamment contrastees ? | Non conforme | Les points de navigation du carrousel ont un contraste trop faible par rapport a leur fond. |
| 10.8 - Pour chaque page web, les contenus caches ont-ils vocation a etre ignores par les technologies d'assistance ? | Non conforme | Les boutons radio du carrousel sont caches avec `display: none` alors qu'ils pilotent une fonctionnalite interactive. |
| 11.1 - Chaque champ de formulaire a-t-il une etiquette ? | Non conforme | Les champs du formulaire de contact n'ont aucune etiquette associee. |
| 11.2 - Chaque etiquette associee a un champ de formulaire est-elle pertinente ? | Non conforme | Le carrousel utilise des `label` vides ou ambigus pour piloter des champs radio ; les placeholders du formulaire servent aussi de pseudo-etiquettes. |
| 11.10 - Dans chaque formulaire, le controle de saisie est-il utilise de maniere pertinente ? | Non conforme | Des champs radio sont detournes pour gerer la navigation du carrousel, au lieu d'etre utilises pour une saisie utilisateur. |
| 11.13 - La finalite d'un champ de saisie peut-elle etre deduite pour faciliter le remplissage automatique des champs avec les donnees de l'utilisateur ? | Non conforme | Les champs `name` et `email` ne disposent pas d'attributs `autocomplete` adaptes. |

## Constats detailles

### 1.1 - Alternative textuelle absente sur une image informative

- Emplacement : [index.html:58](C:/Users/Hackathon_user/Desktop/Projet_Hackathon/AccessLens/Webapp/index.html:58)
- Observation : l'image du slide "Capitales culturelles" utilise `alt=""`.
- Probleme : l'image participe au contenu de la section et accompagne une destination de voyage ; elle n'est donc pas strictement decorative.
- Impact : un utilisateur de lecteur d'ecran ne recoit aucune information equivalente.

### 1.3 - Alternatives textuelles non pertinentes

- Emplacements :
  - [index.html:71](C:/Users/Hackathon_user/Desktop/Projet_Hackathon/AccessLens/Webapp/index.html:71)
  - [index.html:84](C:/Users/Hackathon_user/Desktop/Projet_Hackathon/AccessLens/Webapp/index.html:84)
- Observation :
  - `alt="photo de voyage"`
  - `alt="image"`
- Probleme : ces textes sont trop generiques et ne decrivent ni la destination ni le sens apporte par l'image.
- Impact : l'information transmise visuellement n'est pas restituee aux technologies d'assistance.

### 3.2 - Contraste insuffisant sur des textes utiles

- Emplacements :
  - placeholders du formulaire : [styles.css:290](C:/Users/Hackathon_user/Desktop/Projet_Hackathon/AccessLens/Webapp/styles.css:290)
  - texte des boutons : [styles.css:195](C:/Users/Hackathon_user/Desktop/Projet_Hackathon/AccessLens/Webapp/styles.css:195)
- Valeurs observees :
  - placeholder `#a6acb5` sur fond `#fdfbf7` : contraste approx. 2.21:1
  - texte `#f8f5ef` sur fond `#9c7c38` : contraste approx. 3.6:1
- Probleme : ces valeurs sont insuffisantes pour du texte non decoratif.
- Impact : lecture degradee pour les personnes malvoyantes ou en conditions de contraste reduit.

### 3.3 - Contraste insuffisant sur un composant d'interface

- Emplacement : [styles.css:220](C:/Users/Hackathon_user/Desktop/Projet_Hackathon/AccessLens/Webapp/styles.css:220)
- Observation : les points de navigation du carrousel utilisent `#d8c39b` sur `#fffdf9`.
- Valeur observee : contraste approx. 1.69:1
- Probleme : le composant est difficilement perceptible.
- Impact : la navigation visuelle dans le carrousel est degradee.

### 10.8 - Controles interactifs caches aux technologies d'assistance

- Emplacements :
  - radios du carrousel : [index.html:52](C:/Users/Hackathon_user/Desktop/Projet_Hackathon/AccessLens/Webapp/index.html:52)
  - masquage CSS : [styles.css:141](C:/Users/Hackathon_user/Desktop/Projet_Hackathon/AccessLens/Webapp/styles.css:141)
- Observation : les trois boutons radio qui pilotent les slides sont masques avec `display: none`.
- Probleme : ces elements servent a l'interaction mais sont retires de l'arbre d'accessibilite et du parcours clavier.
- Impact : la fonctionnalite du carrousel n'est pas correctement exploitable avec certaines technologies d'assistance.

### 11.1 - Absence d'etiquettes sur les champs du formulaire de contact

- Emplacements :
  - [index.html:128](C:/Users/Hackathon_user/Desktop/Projet_Hackathon/AccessLens/Webapp/index.html:128)
  - [index.html:129](C:/Users/Hackathon_user/Desktop/Projet_Hackathon/AccessLens/Webapp/index.html:129)
  - [index.html:131](C:/Users/Hackathon_user/Desktop/Projet_Hackathon/AccessLens/Webapp/index.html:131)
  - [index.html:132](C:/Users/Hackathon_user/Desktop/Projet_Hackathon/AccessLens/Webapp/index.html:132)
- Observation : aucun champ n'est associe a un element `<label>`.
- Probleme : le placeholder ne remplace pas une etiquette de formulaire.
- Impact : comprehension et saisie degradees pour les utilisateurs de lecteur d'ecran, de commande vocale ou de memoire de travail reduite.

### 11.2 - Etiquettes absentes, vides ou peu pertinentes

- Emplacements :
  - labels "Precedent" / "Suivant" : [index.html:65](C:/Users/Hackathon_user/Desktop/Projet_Hackathon/AccessLens/Webapp/index.html:65), [index.html:66](C:/Users/Hackathon_user/Desktop/Projet_Hackathon/AccessLens/Webapp/index.html:66), [index.html:78](C:/Users/Hackathon_user/Desktop/Projet_Hackathon/AccessLens/Webapp/index.html:78), [index.html:79](C:/Users/Hackathon_user/Desktop/Projet_Hackathon/AccessLens/Webapp/index.html:79), [index.html:91](C:/Users/Hackathon_user/Desktop/Projet_Hackathon/AccessLens/Webapp/index.html:91), [index.html:92](C:/Users/Hackathon_user/Desktop/Projet_Hackathon/AccessLens/Webapp/index.html:92)
  - labels vides des points : [index.html:98](C:/Users/Hackathon_user/Desktop/Projet_Hackathon/AccessLens/Webapp/index.html:98), [index.html:99](C:/Users/Hackathon_user/Desktop/Projet_Hackathon/AccessLens/Webapp/index.html:99), [index.html:100](C:/Users/Hackathon_user/Desktop/Projet_Hackathon/AccessLens/Webapp/index.html:100)
  - placeholders utilises a la place d'etiquettes : [index.html:128](C:/Users/Hackathon_user/Desktop/Projet_Hackathon/AccessLens/Webapp/index.html:128) a [index.html:132](C:/Users/Hackathon_user/Desktop/Projet_Hackathon/AccessLens/Webapp/index.html:132)
- Probleme :
  - les labels du carrousel ne decrivent pas clairement la valeur du bouton radio associe ;
  - certains labels sont totalement vides ;
  - les champs du formulaire reposent sur des placeholders au lieu d'une etiquette stable.
- Impact : nomination confuse ou absente des controles.

### 11.10 - Usage non pertinent des controles de saisie

- Emplacements :
  - radios du carrousel : [index.html:52](C:/Users/Hackathon_user/Desktop/Projet_Hackathon/AccessLens/Webapp/index.html:52) a [index.html:54](C:/Users/Hackathon_user/Desktop/Projet_Hackathon/AccessLens/Webapp/index.html:54)
  - declenchement via labels : [index.html:65](C:/Users/Hackathon_user/Desktop/Projet_Hackathon/AccessLens/Webapp/index.html:65) a [index.html:100](C:/Users/Hackathon_user/Desktop/Projet_Hackathon/AccessLens/Webapp/index.html:100)
- Observation : des champs radio, qui sont des controles de formulaire, servent ici de mecanisme de navigation du carrousel.
- Probleme : le composant imite un carrousel interactif sans semantique adaptee.
- Impact : comprehension et interaction moins robustes pour les utilisateurs d'aides techniques.

### 11.13 - Absence d'autocompletion semantique

- Emplacements :
  - champ nom : [index.html:128](C:/Users/Hackathon_user/Desktop/Projet_Hackathon/AccessLens/Webapp/index.html:128)
  - champ email : [index.html:129](C:/Users/Hackathon_user/Desktop/Projet_Hackathon/AccessLens/Webapp/index.html:129)
- Observation : aucun attribut `autocomplete` de type `name` ou `email` n'est defini.
- Probleme : la finalite de ces champs n'est pas exposee de facon exploitable aux navigateurs et aides techniques.
- Impact : perte de confort pour les utilisateurs qui s'appuient sur le remplissage automatique.

## Remarques

- Ce document decrit les defauts actuellement presents dans la page, dans une logique de demonstration.
- Il peut servir de base a un futur correctif ou a un jeu de tests pour un outil de remediation automatique.

