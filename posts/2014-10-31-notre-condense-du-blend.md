# Notre condensé du BlendWebMix 2014 :

## Communication entre les acteurs

- S'accorder sur un vocabulaire commun : faire établir un glossaire par les équipe design, technique, client, managers, ect...
- Travailler ensemble sur la problématique produit : Dev, inté, design !
- Sass: définissez des variables compréhensible par les devs et le design, qui serviront de vocabulaire pour la discussion.

## Partir du besoin utilisateur:

- UX: c'est la manière dont vos utilisateur utilise votre produit.
- Observez comment vos utilisateur utilise le produit, partez du l'usage et designez une solution qui répond à ce besoin.
- Developpeurs : utilisez votre propre application, mettez vous à la place de vos utilisateurs.

Quelques exemples :

- Croper une image: ne coupez pas l'image n'importe comment, centrez sur le sujet
- Ne proposez pas des filtres de recherche aboutissant à 0 résultat !
- Quand un utilisateur viens pour un contenu, ne lui servez pas à la place un message "abonnez vous", "telecharger l'application", "accepter les cookies" : faites ça plus tard.
- Proposez des dates qui ont un sens en fonction du context d'utilisation : "Hier matin", "Dans 3 heures", "Mars 2013", ...

## Méthodes de travail : automatisez!

- Intégration / Dev front : Synchronisez vos navigateurs, vos devices avec votre code ! brunch.io, Sourcemap Js et CSS, Chrome Workspaces, Emmet Livestyle / Browser Sync, FB-FLo

## Projet : l'approche API

"Une API est un pont entre deux système qui ne parlent pas la même langue."

- Plutot que de faire un projet pour chaque device, faite un projet API et un petit client par device.
- L'API n'est pas juste un solution technique : c'est une vraie valeur métier au coeur du produit. C'est quelque chose que vous pouvez vendre !
- Organiser le projet autour d'une API vous aide à mettre en place une architecture de qualité
- L'API se constuit en étroite collaboration avec les responsables produit et l'équipe tehcnique autour d'un objectif métier commun.
- Techniquement : l'API permet le Side-Loading, charger plusieurs données en une requête.

Votre API doit être __ouverte__ et __documenté__ :
- Elle sera sniffée de toute façon
- Les clients qui utiliserons votre API vous apporterons des stats, des retours, des idées, ect.
- N'importe quelle application pourra integrer votre service
- Vous ne dites jamais non : un besoin client spécifique que vous ne pouvez pas prendre en charge ? Vos client peuvent tout simplement le faire développer, sans vous, grâce à votre API !

Vos "devoirs":
- Faites du RESTful à moins qu'une contrainte technique vous en empèche.
- Bien documenter
- Guarantir un bonne disponibilité, pas de regression, ect.
- Limité l'utilisation: attention au SPAM / attaque de votre API
- Des exemple de client dans différents language : pour lancer vos premiers utilisateurs
- Soyez vous-même des utilisateurs de votre API!
- Basez votre API sur des standars ! (Rest, SOAP, ...)

Resource pour une API REST en Symfony2 : format HAL + SF2 + JMSSerializer + Nelmio

## Dette Technique :

"La différence entre le code produit et le code idéal qu’on aurait pu avoir"

Comment la réduire ?

- Lire/Docummenter le code : schematiser son fonctionnement sur papier est un bon exercice
- Faire de petites évolutions et les livrer au client rapidement pour valider qu'on a bien compris le metier
- Pair programming et revue de code : permet de propager l'experience, la connaissance du projet
- Utiliser des outils de métrique pour cibler les éléments clés à refactorer en priorité
- Rester humble : attention au "Je supprime tout ce mauvais code et je recode tout", vous perdez de la valeur et de la logique metier en faisant ça.

La solution est dans l'équipe et la collaboration !

- Prendre conscience du problème
- Le nommer : notter quelque-part que telle fonctionnalité à besoin d'être refactoré, y revenir plus tard.
- Chercher des solutions
- Saisir les opportunité pour les appliquer.

## Accessibilité :
- Ne véhiculez pas une information uniquement par la couleur, la forme, la position : quelqu'un qui ne vous pas l'écran ne peut pas utiliser "le bouton vers en bas à droite" !
- Pour les lecteurs vocaux : Utilisez la sémentique HTML + implementer la spécifications ARIA

La phrase du Blen que nous retenons en particulier :
"Utilisez votre propre produit !" - Le business, le design, les techn, ...

## Angular 2:

Plus facile d'utilisation, plus performant : à suivre.
PS: une app Angular peut-être l'un des clients qui utilisent votre API ;)
