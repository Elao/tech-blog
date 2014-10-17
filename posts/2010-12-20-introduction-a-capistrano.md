
Bonjour à tous !

Cela fait quelque temps que nous utilisons un outil dérivé de Capistrano et dédié au déploiement de projets Symfony baptisé **Capifony**.
Nous allons essayer, au travers de plusieurs articles de vous faire partager notre expérience d'un outil très pratique pour déployer de façon plus "rigoureuse" vos projets Symfony !

Cet article abordera le point de vue **"théorique"** de l'utilisation de Capistrano / Capifony, nous aurons l'occasion de revenir sur l'aspect technique et sur son utilisation dans un prochain article.

### Introduction à Capistrano (... et Capifony)

Capistrano est un outil open-source permettant de déployer des scripts sur plusieurs serveurs, il est principalement utilisé pour les applications web. Il permet notamment d'automatiser la création d'une nouvelle "release" d'un projet fonctionnant sur un ou plusieurs serveurs, et supporte également la création (ou la surcharge) de tâches spécifiques.

Capistrano est écrit en Ruby et est disponible via le gestionnaire de paquets "RubyGems".
À l'origine conçu pour le déploiement d'applications Ruby On Rails, il est également utilisé pour déployer des applications web écrites en d'autres langages comme par exemple le PHP.

Capistrano peut convenir à la fois pour un usage "simple" aux développeurs soucieux de l'aspect **qualitatif** de leurs déploiements mais également à une utilisation plus "avancée" permettant la gestion de plusieurs environnements (multi-stages), la surcharge de tâches, l'utilisation de l'agent forwarding d'SSH ...

### La gestion des environnements

Outre la capacité d'effectuer des déploiements en toute sérénité, Capistrano, par l'intermédiaire du plugin `capistrano-ext` permet également la gestion de différents environnements (Production, Pre-production, Dev ...) appelés `stages` nous permettant à la fois :

- D'éviter d'écrire 3 ou 4 fois le même script de déploiement pour autant d'environnements.
- De lancer des tâches spécifiques en fonction de l'environnement de déploiement.
- De surcharger certaines tâches pour un environnement particulier.

Pour plus de précisions à son sujet je vous invite à consulter le <a href="https://github.com/capistrano/capistrano/wiki" target="_blank">site officiel</a> du projet.

### La gestion des rôles

Nous avons vu que Capistrano permet de gérer différents "stages" ou environnements, il permet dans le cas d'une architecture "distribuée" d'associer des "rôles" à des machines.

Capistrano permet donc d'associer un serveur (ou une grappe) à un rôle spécifique. Les serveurs peuvent également être associés à plusieurs rôles. Cette fonctionnalité peut-être très utile, lorsque, pour un même projet plusieurs machines entrent en jeu :

- Frontends multiples
- Back-office dédié
- Serveur de base de données dédié
- Moteur de recherche
- ...

**Exemples:**

```
role :database, "db.my-project.com"
role :backend, "admin@backend.my-project.com", "db.my-project.com:1234"
```

A noter que dans le cas où le nom d'utilisateur est spécifié, il écrase l'utilisateur ou les options SSH configurés dans les scripts de déploiement. De la même façon, si le port est spécifié, il prend le pas sur celui qui a pu être spécifié en amont. D'où le fonctionnement abordé plus haut dit de surcharge de tâches et / ou de configuration.

Il est également possible d'associer un ou plusieurs attributs aux rôles, utile dans le cas où, par exemple, plusieurs serveurs de base de données sont utilisés et nécessitent donc de définir un "master" et un ou plusieurs "slave".

```
role :db, "db.my-project.com", :master => true
role :db, "slave1.my-project.com"
```

### Les espaces de noms et les tâches

Tout le fonctionnement de Capistrano repose sur les tâches qui sont des actions spécifiques appelées lors des déploiements. Une collection de tâches est appellée une "recette" (Recipes).

Les tâches sont définies dans le coeur de Capistrano et dans les plugins mais peuvent être également définies et/ou surchargées à l'intérieur de nos fichiers de déploiement (Capfile, deploy.rb ou encore n'importe qu'elle autre fichier, à condition qu'il soit "chargé" par nos scripts).

Capifony, par exemple, que nous aborderons au cours d'un prochain article, n'est ni plus ni moins "*qu'une surcharge*" du coeur de Capistrano, apportant de nouvelles tâches spécifiques au Framework Symfony ; son fonctionnement est extrêmement simple puisqu'il vient surcharger certaines tâches existantes et en apporter de nouvelles.

La notion d'espace de nom quant à elle va nous offrir la possibilité d'écrire des tâches portant le même nom mais n'effectuant pas le même traitement. Ce cas de figure, lorsque l'on travaille sur ses propres scripts n'arrive que rarement, et même dans ce cas, nous avons le contrôle du nommage de nos tâches.
En revanche, lorsque vous travaillez sur des scripts ayant vocation à être distribués, sous la forme de plugins par exemple, cette notion prend tout son sens et permet d'éviter tout conflit en évitant d'écraser une tâche existante.

### Conclusion

Ce premier article nous permet d'aborder "gentiment" le fonctionnement de Capistrano. Les applications web devenant de plus en plus complexes, l'utilisation de ce type d'outils est à recommander pour qui veut être certain de garder une parfaite maitrise de ses mises en recette et / ou production. Même si les erreurs ou les oublis restent possibles, ils sont fortement encadrés et limités par l'automatisation des tâches.

Les prochains articles nous permettront de rentrer dans le vif du sujet en abordant les déploiements avec Capistrano sous un angle un peu plus "technique".
Comme d'usage, remarques, corrections et critiques sont les bienvenues !

Merci à Xavier R. pour la relecture