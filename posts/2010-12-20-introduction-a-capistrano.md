
Bonjour à tous !

Cela fait quelque temps que nous utilisons un outil dérivé de Capistrano et dédié au déploiement de projets Symfony baptisé **Capifony**.  
Nous allons essayer, au travers de plusieurs articles de vous faire partager notre expérience d&#8217;un outil très pratique pour déployer de façon plus &#8220;rigoureuse&#8221; vos projets Symfony !

Cet article abordera le point de vue **&#8220;théorique&#8221;** de l&#8217;utilisation de Capistrano / Capifony, nous aurons l&#8217;occasion de revenir sur l&#8217;aspect technique et sur son utilisation dans un prochain article.

## Introduction à Capistrano (&#8230; et Capifony)

Capistrano est un outil open-source permettant de déployer des scripts sur plusieurs serveurs, il est principalement utilisé pour les applications web. Il permet notamment d&#8217;automatiser la création d&#8217;une nouvelle &#8220;release&#8221; d&#8217;un projet fonctionnant sur un ou plusieurs serveurs, et supporte également la création (ou la surcharge) de tâches spécifiques.

Capistrano est écrit en Ruby et est disponible via le gestionnaire de paquets &#8220;RubyGems&#8221;.  
À l&#8217;origine conçu pour le déploiement d&#8217;applications Ruby On Rails, il est également utilisé pour déployer des applications web écrites en d&#8217;autres langages comme par exemple le PHP.

Capistrano peut convenir à la fois pour un usage &#8220;simple&#8221; aux développeurs soucieux de l&#8217;aspect **qualitatif** de leurs déploiements mais également à une utilisation plus &#8220;avancée&#8221; permettant la gestion de plusieurs environnements (multi-stages), la surcharge de tâches, l&#8217;utilisation de l&#8217;agent forwarding d&#8217;SSH &#8230;

## La gestion des environnements

Outre la capacité d&#8217;effectuer des déploiements en toute sérénité, Capistrano, par l&#8217;intermédiaire du plugin **capistrano-ext **permet également la gestion de différents environnements (Production, Pre-production, Dev &#8230;) appelés &#8220;**stages**&#8221; nous permettant à la fois :

- D&#8217;éviter d&#8217;écrire 3 ou 4 fois le même script de déploiement pour autant d&#8217;environnements.  
- De lancer des tâches spécifiques en fonction de l&#8217;environnement de déploiement.  
- De surcharger certaines tâches pour un environnement particulier.

Pour plus de précisions à son sujet je vous invite à consulter le <a href="https://github.com/capistrano/capistrano/wiki" target="_blank">site officiel</a> du projet.

## La gestion des rôles

Nous avons vu que Capistrano permet de gérer différents &#8220;stages&#8221; ou environnements, il permet dans le cas d&#8217;une architecture &#8220;distribuée&#8221; d&#8217;associer des &#8220;rôles&#8221; à des machines.

Capistrano permet donc d&#8217;associer un serveur (ou une grappe) à un rôle spécifique. Les serveurs peuvent également être associés à plusieurs rôles. Cette fonctionnalité peut-être très utile, lorsque, pour un même projet plusieurs machines entrent en jeu :

- Frontends multiples  
- Back-office dédié  
- Serveur de base de données dédié  
- Moteur de recherche  
- &#8230;

**Exemples:**

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    role :database, <span class="st0">"db.my-project.com"</span><br /> role :backend, <span class="st0">"admin@backend.my-project.com"</span>, <span class="st0">"db.my-project.com:1234"</span>
  </div>
</div>

A noter que dans le cas où le nom d&#8217;utilisateur est spécifié, il écrase l&#8217;utilisateur ou les options SSH configurés dans les scripts de déploiement. De la même façon, si le port est spécifié, il prend le pas sur celui qui a pu être spécifié en amont. D&#8217;où le fonctionnement abordé plus haut dit de surcharge de tâches et / ou de configuration.

Il est également possible d&#8217;associer un ou plusieurs attributs aux rôles, utile dans le cas où, par exemple, plusieurs serveurs de base de données sont utilisés et nécessitent donc de définir un &#8220;master&#8221; et un ou plusieurs &#8220;slave&#8221;.

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    role :db, <span class="st0">"db.my-project.com"</span>, :master =<span class="sy0">></span> <span class="kw2">true</span><br /> role :db, <span class="st0">"slave1.my-project.com"</span>
  </div>
</div>

## Les espaces de noms et les tâches

Tout le fonctionnement de Capistrano repose sur les tâches qui sont des actions spécifiques appelées lors des déploiements. Une collection de tâches est appellée une &#8220;recette&#8221; (Recipes).

Les tâches sont définies dans le coeur de Capistrano et dans les plugins mais peuvent être également définies et/ou surchargées à l&#8217;intérieur de nos fichiers de déploiement (Capfile, deploy.rb ou encore n&#8217;importe qu&#8217;elle autre fichier, à condition qu&#8217;il soit &#8220;chargé&#8221; par nos scripts).

Capifony, par exemple, que nous aborderons au cours d&#8217;un prochain article, n&#8217;est ni plus ni moins &#8220;*qu&#8217;une surcharge*&#8221; du coeur de Capistrano, apportant de nouvelles tâches spécifiques au Framework Symfony ; son fonctionnement est extrêmement simple puisqu&#8217;il vient surcharger certaines tâches existantes et en apporter de nouvelles.

La notion d&#8217;espace de nom quant à elle va nous offrir la possibilité d&#8217;écrire des tâches portant le même nom mais n&#8217;effectuant pas le même traitement. Ce cas de figure, lorsque l&#8217;on travaille sur ses propres scripts n&#8217;arrive que rarement, et même dans ce cas, nous avons le contrôle du nommage de nos tâches.  
En revanche, lorsque vous travaillez sur des scripts ayant vocation à être distribués, sous la forme de plugins par exemple, cette notion prend tout son sens et permet d&#8217;éviter tout conflit en évitant d&#8217;écraser une tâche existante.

## Conclusion:

Ce premier article nous permet d&#8217;aborder &#8220;gentiment&#8221; le fonctionnement de Capistrano. Les applications web devenant de plus en plus complexes, l&#8217;utilisation de ce type d&#8217;outils est à recommander pour qui veut être certain de garder une parfaite maitrise de ses mises en recette et / ou production. Même si les erreurs ou les oublis restent possibles, ils sont fortement encadrés et limités par l&#8217;automatisation des tâches.

Les prochains articles nous permettront de rentrer dans le vif du sujet en abordant les déploiements avec Capistrano sous un angle un peu plus &#8220;technique&#8221;.  
Comme d&#8217;usage, remarques, corrections et critiques sont les bienvenues !

Merci à Xavier R. pour la relecture <img src="http://old-blog.elao.dev/wp-includes/images/smilies/icon_smile.gif" alt="icon smile Introduction à Capistrano" class="wp-smiley" title="Introduction à Capistrano" />