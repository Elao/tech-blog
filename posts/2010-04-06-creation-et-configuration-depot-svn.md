
[<img class="alignleft size-full wp-image-139" title="under-construction" src="/blog/wp-content/uploads/2010/04/under-construction.png" alt="under construction Création et configuration dun dépot subversion (SVN)" width="128" height="128" />][1]
<span style="text-decoration: underline;"><strong>Sommaire:</strong></span>

1.  Création et configuration d'un dépôt SVN
2.  [Scripts de hook SVN][2]
3.  S'authentifier et accéder à un dépôt SVN (à venir)
4.  SVN over SSH (à venir)
5.  Authentification via certificat (à venir)

<span style="text-decoration: underline;"><br /> </span>

<p style="text-align: justify;">
  Subversion (en abrégé svn) est un <a title="Système de gestion de versions" href="http://fr.wikipedia.org/wiki/Syst%C3%A8me_de_gestion_de_versions" target="_blank">système de gestion de versions</a>, il agit sur une arborescence de fichiers afin de conserver toutes les versions des fichiers, ainsi que les différences entre les fichiers. De ce fait, et pour résumer, il permet non seulement de pouvoir revenir à un état T d'une application dans le temps mais également de travailler à plusieurs sur un seul et même projet, les conflits pouvant être engendrés entre deux utilisateurs manipulant un même fichier étant gérés par le serveur subversion.
</p>

<p style="text-align: justify;">
  Pour ceux qui n'ont pas encore subversion d'installer sur leur serveur ... c'est le moment !
</p>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">apt-get install</span> subversion
  </div>
</div>

<p style="text-align: justify;">
  En soit la création d'un dépot est une chose relativement aisée, l'utilitaire <strong>svnadmin (</strong>fournit avec le paquet Subversion) permet de le faire en une ligne de commande très simple:
</p>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">svnadmin</span> create <span class="sy0">/</span>path<span class="sy0">/</span>to<span class="sy0">/</span>repos
  </div>
</div>

<p style="text-align: justify;">
  Nous venons donc de créer un dépôt dans /path/to/repos, ce dépôt commence à la révision 0, elle ne possède qu'une seul propriété svn:date qui permet de récupérer la date de création de notre dépôt.
</p>

<p style="text-align: justify;">
  Historiquement SVN utilise des bases Berkeley DB pour stocker ses informations, il est cependant possible de choisir une méthode de stockage différente via l'argument<span style="font-family: terminal, monaco;">&#8211;fs-type.</span>
</p>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="sy0">></span><span class="kw2">svnadmin</span> create <span class="re5">--fs-type</span> fsfs <span class="sy0">/</span>path<span class="sy0">/</span>to<span class="sy0">/</span>repos<br /> <span class="kw2">svnadmin</span> create <span class="re5">--fs-type</span> bdb <span class="sy0">/</span>path<span class="sy0">/</span>to<span class="sy0">/</span>other<span class="sy0">/</span>repos
  </div>
</div>

<p style="text-align: justify;">
  <a href="/blog/wp-content/uploads/2010/04/warning.png"><img class="alignleft size-full wp-image-146" title="warning" src="/blog/wp-content/uploads/2010/04/warning.png" alt="warning Création et configuration dun dépot subversion (SVN)" width="48" height="48" /></a>
</p>

Il ne faut surtour pas créer un base base Berkeley sur un partage réseau, une base Berkeley ne peut pas exister sur ce type de système de fichier (NFS, AFS ou Windows SMB). Les bases Berkeley nécessite d'avoir en sous-couche un système de fichier implémentant un système de verrou POSIX stricte et plus important encore la possibilité d'adresser directement les fichiers dans la mémoire. La plupart des systèmes de fichier réseau ne fournissent pas ces fonctionnalités. En conséquence si vous essayez d'utiliser Berkeley DB sur un partage réseau, les résultats sont **imprévisibles.
**Plus grave, vous pouvez voir de mystérieuses erreurs apparaître  tout de suite, ou il peut s'écouler des mois avant que vous ne découvriez que votre base de données du référentiel est subtilement, mais surement, **corrompue**.

<p style="text-align: justify;">
  A noter que la commande <span style="font-family: terminal, monaco;">svnadmin</span> est la seule commande avec <span style="font-family: terminal, monaco;">svnlook</span> à être considérée comme des utilitaires serveur, elles sont toujours utilisées sur la machine hébergeant le dépôt de sources. C'est pour cela qu'elles accèdent au dépôt en utilisant un chemin "standard" vers le système de fichiers, elles ne peuvent de toutes façons pas accéder à une ressource réseau.
</p>

<p style="text-align: justify;">
  Après avoir exécuter la commande ci-dessus, vous devez avoir une arborescence de ce type dans votre dépôt:
</p>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    conf  db  format  hooks  locks  README.txt
  </div>
</div>

Cette arborescence peut être détaillée de la façon suivante:

*   **conf**:
    Est le répertoire contenant les fichiers de configuration du dépôt
*   **db**
    Le répertoire ou est données "versionnées" sont stockées. Ce répertoire est soit un environnement de type Berkeley DB (Tables et autres ...), soit un environnement de type FSFS contenant les fichiers des différentes révisions.
*   **format**
    Simple fichier contenant un entier indiquant le numéro de version du repository
*   **hooks**
    Le répertoire contenant des templates de scripts de "hoocking", permettant notamment de déclencher des traitements sur certaines actions effectuées sur le dépôt.
*   **locks**
    Le répertoire permettant de gérer les verrous sur données.Il est notamment utilisé pour "tracker" les différents accès aux dépots.

De manière général, vous ne devriez pas avoir à modifier le dépôt "à la main", l'outil <span style="font-family: terminal, monaco;">svnadmin</span> est censé fournir toutes les fonctionnalités nécessaire à la maintenance et la configuration d'un dépôt.

 [1]: /blog/wp-content/uploads/2010/04/under-construction.png
 [2]: /blog/linux/scripts-de-hook-svn-subversion-repository-hooking.html "Scripts de hook SVN (Subversion repository hooking)"