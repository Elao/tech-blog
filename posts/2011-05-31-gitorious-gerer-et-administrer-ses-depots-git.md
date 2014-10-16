
[<img class="alignleft size-full wp-image-1551" title="gitorious" src="http://www.elao.org/wp-content/uploads/2011/05/gitorious.png" alt="gitorious GITorious   Gérer et administrer ses dépôts GIT" width="180" height="51" />][1]Bonjour à toutes et à tous,

Aujourd'hui au programme, un petit memo sur l'installation de [GITORIOUS][2]. Pour ceux qui l'ignorent, Gitorious est un projet web destiné à la gestion des projets Open Source utilisant GIT.
C'est le deuxième outil utilisé derrière GitHub bien que plus ancien que celui-ci et étant <a class="fancybox" title="... même si on sait tous que Ruby est plus lent qu'une grand-mère asthmatique ;)" href="http://www.elao.org/wp-content/uploads/2011/05/troll.gif">codé en Ruby <img src="http://old-blog.elao.dev/wp-includes/images/smilies/icon_wink.gif" alt="icon wink GITorious   Gérer et administrer ses dépôts GIT" class="wp-smiley" title="GITorious   Gérer et administrer ses dépôts GIT" /> </a>

<h3 class="articleTitle">
  Introduction:
</h3>

La suite de cet article s'adresse à ceux qui préfèrent héberger et gérer eux-mêmes leurs dépôts plutôt que de passer par gitHub. En plus de fournir une interface d'administration de ces dépôts GIT, GITORIOUS fournit en vrac:

- Une gestion par projet des dépôts
- Une notion de wiki (rattaché à un projet particulier)
- Une interface web permettant de gérer les "merge request" et les revues de code
- Une visualisation de l'activité du projet et des développeurs (commit, diff de fichiers, possibilité d'ajouter un commentaire sur un commit ... )

<h3 class="articleTitle">
  Screenshots:
</h3>

<ul class="gallery-fancy">
  <li>
    <a class="fancybox" rel="screens-gito" href="http://www.elao.org/wp-content/gallery/gitorious/collaborators.png"><img src="http://www.elao.org/wp-content/gallery/gitorious/thumbs/thumbs_collaborators.png" alt="thumbs collaborators GITorious   Gérer et administrer ses dépôts GIT"  title="GITorious   Gérer et administrer ses dépôts GIT" /></a>
  </li>
  <li>
    <a class="fancybox" rel="screens-gito" href="http://www.elao.org/wp-content/gallery/gitorious/commit_log.jpg"><img class="ngg-singlepic ngg-none" src="http://www.elao.org/wp-content/gallery/gitorious/thumbs/thumbs_commit_log.jpg" alt="thumbs commit log GITorious   Gérer et administrer ses dépôts GIT"  title="GITorious   Gérer et administrer ses dépôts GIT" /></a>
  </li>
  <li>
    <a class="fancybox" rel="screens-gito" href="http://www.elao.org/wp-content/gallery/gitorious/dashboard.png"><img class="ngg-singlepic ngg-none" src="http://www.elao.org/wp-content/gallery/gitorious/thumbs/thumbs_dashboard.png" alt="thumbs dashboard GITorious   Gérer et administrer ses dépôts GIT"  title="GITorious   Gérer et administrer ses dépôts GIT" /></a>
  </li>
  <li>
  </li>
  <li>
    <a class="fancybox" rel="screens-gito" href="http://www.elao.org/wp-content/gallery/gitorious/logs.png"><img class="ngg-singlepic ngg-none" src="http://www.elao.org/wp-content/gallery/gitorious/thumbs/thumbs_logs.png" alt="thumbs logs GITorious   Gérer et administrer ses dépôts GIT"  title="GITorious   Gérer et administrer ses dépôts GIT" /></a>
  </li>
  <li>
    <a class="fancybox" rel="screens-gito" href="http://www.elao.org/wp-content/gallery/gitorious/dashboard_0.png"><img class="ngg-singlepic ngg-none" src="http://www.elao.org/wp-content/gallery/gitorious/thumbs/thumbs_dashboard_0.png" alt="thumbs dashboard 0 GITorious   Gérer et administrer ses dépôts GIT"  title="GITorious   Gérer et administrer ses dépôts GIT" /></a>
  </li>
  <li>
    <a class="fancybox" rel="screens-gito" href="http://www.elao.org/wp-content/gallery/gitorious/side-by-side-diff.png"><img class="ngg-singlepic ngg-none" src="http://www.elao.org/wp-content/gallery/gitorious/thumbs/thumbs_side-by-side-diff.png" alt="thumbs side by side diff GITorious   Gérer et administrer ses dépôts GIT"  title="GITorious   Gérer et administrer ses dépôts GIT" /></a>
  </li>
  <li>
    <a class="fancybox" rel="screens-gito" href="http://www.elao.org/wp-content/gallery/gitorious/team-view.png"><img class="ngg-singlepic ngg-none" src="http://www.elao.org/wp-content/gallery/gitorious/thumbs/thumbs_team-view.png" alt="thumbs team view GITorious   Gérer et administrer ses dépôts GIT"  title="GITorious   Gérer et administrer ses dépôts GIT" /></a>
  </li>
</ul>

<div class="filet clear">
</div>

<span style="font-size: 20px; font-weight: bold;">Prérequis :</span>

Dans un premier temps installons les paquets nécessaire à GITORIOUS ...

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">apt-get install</span> build-essential zlib1g-dev libcurl4-openssl-dev apache2 mysql-server mysql-client apg geoip-bin libgeoip1<br /> libgeoip-dev libpcre3 libpcre3-dev zlib1g zlib1g-dev libyaml-dev libmysqlclient-dev apache2-dev libonig-dev<br /> <span class="kw2">zip</span> <span class="kw2">unzip</span> memcached git-core <span class="kw2">git-svn</span> git-doc git-cvs libreadline-dev openjdk-<span class="nu0">6</span>-jre sqlite3 libsqlite3-dev<br /> libmagick++<span class="nu0">3</span> libmagick++-dev libapache2-mod-xsendfile
  </div>
</div>

<div class="filet">
</div>

<img class="alignleft size-full wp-image-1553" title="phusion" src="http://www.elao.org/wp-content/uploads/2011/05/phusion.png" alt="phusion GITorious   Gérer et administrer ses dépôts GIT" width="246" height="108" />

<h3 class="articleTitle">
  Installation de Ruby Enterprise:
</h3>

J'ai récemment découvert [Ruby Enterprise][3] ; si comme moi vous n'êtes pas un expert Ruby mais vous avez plusieurs soft RoR à faire tourner (je pense notamment à Redmine et Gitorious) vous allez plus que l'aimer et ce, pour plusieurs raisons:

- Un garbage collector maison, utilisé notamment par Passenger, qui utilise le preforking pour réduire la consommation mémoire de Ruby (33% de gains annoncés)
- Des applications plus "scalables"
- Une meilleure gestion de l'allocation dynamique de la mémoire
- Et pour nos amis développeurs RoR, le dump des stacks mémoire pour le debug.
- Enfin, et c'est selon moi son principal avantage, Ruby Enterprise gère lui-même l'installation des paquetages Ruby (sans recourir à gem) ; c'est notamment très pratique pour les néophytes et cela résout le casse-tête que représente la gestion des gems en fonction des différentes versions de Ruby ... .

On récupère donc Ruby Enterprise ...

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">wget</span> http:<span class="sy0">//</span>rubyenterpriseedition.googlecode.com<span class="sy0">/</span>files<span class="sy0">/</span>ruby-enterprise-1.8.7-<span class="nu0">2011.03</span>.tar.gz<br /> <span class="kw2">tar</span> zxf ruby-enterprise-1.8.7-<span class="nu0">2011.03</span>.tar.gz
  </div>
</div>

Sans oublier la création d'un lien symbolique vers la version courante de ruby-enterprise qui nous permettra de gérer nos versions proprement.

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw3">cd</span> <span class="sy0">/</span>opt <span class="sy0">&&</span> <span class="kw2">ln</span> <span class="re5">-s</span> ruby-enterprise-1.8.7-<span class="nu0">2011.03</span><span class="sy0">/</span> ruby-enterprise
  </div>
</div>

Nous allons à présent créer les liens vers les différents binaires Ruby, afin d'éviter de surcharger le PATH. Libre à vous de le faire, je n'ai pas vraiment d'opinion quant à la façon la plus "propre" de procéder.

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">ln</span> <span class="re5">-s</span> <span class="sy0">/</span>opt<span class="sy0">/</span>ruby-enterprise<span class="sy0">/</span>bin<span class="sy0">/</span>ruby <span class="sy0">/</span>opt<span class="sy0">/</span>ruby-enterprise<span class="sy0">/</span>bin<span class="sy0">/</span>rake <span class="sy0">/</span>opt<span class="sy0">/</span>ruby-enterprise<span class="sy0">/</span>bin<span class="sy0">/</span>gem <span class="sy0">/</span>usr<span class="sy0">/</span>local<span class="sy0">/</span>bin
  </div>
</div>

<div class="filet">
</div>

<img class="alignleft size-full wp-image-1556" title="sphinx" src="http://www.elao.org/wp-content/uploads/2011/05/sphinx.jpg" alt="sphinx GITorious   Gérer et administrer ses dépôts GIT" width="200" height="51" />

<h2 class="articleTitle">
  Installation de Sphinx:
</h2>

Sphinx est un moteur de recherche relativement performant qui supporte nativement de nombreuses base de données dont MySQL et PostGreSQL. Il est extrêmement répandu sur des sites à fort traffic, notamment Craiglist.org.

**On récupère les sources ...**

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">wget</span> http:<span class="sy0">//</span>sphinxsearch.com<span class="sy0">/</span>files<span class="sy0">/</span>sphinx-0.9.9.tar.gz
  </div>
</div>

** ... que l'on décompresse.**

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">tar</span> zxf sphinx-0.9.9.tar.gz <span class="sy0">&&</span> <span class="kw3">cd</span> sphinx-0.9.9
  </div>
</div>

**Il s'agit ensuite d'une compilation / installation classique:**

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    .<span class="sy0">/</span>configure <span class="re5">--prefix</span>=<span class="sy0">/</span>usr<span class="sy0">/</span>local<span class="sy0">/</span>sphinx-0.9.9<br /> <span class="kw2">make</span> <span class="sy0">&&</span> <span class="kw2">sudo</span> <span class="kw2">make</span> <span class="kw2">install</span>
  </div>
</div>

**On gère notre versioning de la même façon que pour Ruby Enterprise:**

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw3">cd</span> <span class="sy0">/</span>usr<span class="sy0">/</span><span class="kw3">local</span> <span class="sy0">&&</span> <span class="kw2">ln</span> <span class="re5">-s</span> sphinx-0.9.9 sphinx
  </div>
</div>

**Et pour finir on rend disponible les binaires:**

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">ln</span> <span class="re5">-s</span> <span class="sy0">/</span>usr<span class="sy0">/</span>local<span class="sy0">/</span>sphinx<span class="sy0">/</span>bin<span class="sy0">/*</span> <span class="sy0">/</span>usr<span class="sy0">/</span>local<span class="sy0">/</span>bin
  </div>
</div>

<div class="filet">
</div>

<img class="alignleft size-full wp-image-1559" title="apache" src="http://www.elao.org/wp-content/uploads/2011/05/apache.gif" alt="apache GITorious   Gérer et administrer ses dépôts GIT" width="203" height="61" />

<h2 class="articleTitle">
  Installation de Apache activeMQ:
</h2>

Je n'ai guère eu le temps de me documenter sur ce composant, pour ceux que cela intéresse, vous trouverez votre bonheur [ici][4]

**On récupère les sources:**

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">wget</span> http:<span class="sy0">//</span>mir2.ovh.net<span class="sy0">/</span>ftp.apache.org<span class="sy0">/</span>dist<span class="sy0">/</span>activemq<span class="sy0">/</span>apache-activemq<span class="sy0">/</span>5.4.2<span class="sy0">/</span>apache-activemq-5.4.2-bin.tar.gz
  </div>
</div>

**Même démarche que plus haut:**

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">tar</span> zxf apache-activemq-5.4.2-bin.tar.gz<br /> <span class="kw2">sudo</span> <span class="kw2">mv</span> apache-activemq-5.4.2 <span class="sy0">/</span>usr<span class="sy0">/</span><span class="kw3">local</span><br /> <span class="kw3">cd</span> <span class="sy0">/</span>usr<span class="sy0">/</span><span class="kw3">local</span> <span class="sy0">&&</span> <span class="kw2">sudo</span> <span class="kw2">ln</span> <span class="re5">-s</span> apache-activemq-5.4.2 apache-activemq <span class="sy0">&&</span> <span class="kw3">cd</span> apache-activemq<br /> <span class="kw2">sudo</span> bin<span class="sy0">/</span>activemq setup <span class="sy0">/</span>etc<span class="sy0">/</span>default<span class="sy0">/</span>activemq <span class="sy0">&&</span> <span class="kw2">sudo</span> <span class="kw2">chmod</span> <span class="nu0">600</span> <span class="sy0">/</span>etc<span class="sy0">/</span>default<span class="sy0">/</span>activemq<br /> <span class="kw2">sudo</span> adduser <span class="re5">--system</span> <span class="re5">--no-create-home</span> <span class="re5">--home</span> <span class="sy0">/</span>usr<span class="sy0">/</span>local<span class="sy0">/</span>apache-activemq <span class="re5">--shell</span> <span class="sy0">/</span>bin<span class="sy0">/</span><span class="kw2">bash</span> activemq<br /> <span class="kw2">sudo</span> <span class="kw2">chown</span> <span class="re5">-R</span> activemq:nogroup <span class="sy0">/</span>usr<span class="sy0">/</span>local<span class="sy0">/</span>apache-activemq-5.4.2
  </div>
</div>

Il y a quelques adaptations à faire à ce niveau, commençons par le fichier

<div class="codecolorer-container text vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="text codecolorer">
    /etc/default/activemq
  </div>
</div>

Ajoutez ce qui suit au début du fichier:

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="re2">ACTIVEMQ_HOME</span>=<span class="sy0">/</span>usr<span class="sy0">/</span>local<span class="sy0">/</span>apache-activemq
  </div>
</div>

On modifie ensuite la ligne "Active User" comme suit:

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="re2">ACTIVEMQ_USER</span>=<span class="st0">"activemq"</span>
  </div>
</div>

Pour finir éditez le fichier

<div class="codecolorer-container text vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="text codecolorer">
    /usr/local/apache-activemq/conf/activemq.xml
  </div>
</div>

. Commentez ou remplacez le "**transportConnector**" existant et ajoutez le protocole stomp à la place:

<div class="codecolorer-container xml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="xml codecolorer">
    <span class="sc3"><span class="re1"><transportConnector</span> <span class="re0">name</span>=<span class="st0">"stomp"</span> <span class="re0">uri</span>=<span class="st0">"stomp://0.0.0.0:61613"</span><span class="re2">></span></span>;
  </div>
</div>

<div class="filet">
</div>

<h3 class="articleTitle">
  Récupération de GITorious
</h3>

Passons enfin aux choses sérieuses ...

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">sudo</span> <span class="kw2">git clone</span> git:<span class="sy0">//</span>gitorious.org<span class="sy0">/</span>gitorious<span class="sy0">/</span>mainline.git <span class="sy0">/</span>var<span class="sy0">/</span>www
  </div>
</div>

Libre à vous de l'installer ailleurs <img src="http://old-blog.elao.dev/wp-includes/images/smilies/icon_smile.gif" alt="icon smile GITorious   Gérer et administrer ses dépôts GIT" class="wp-smiley" title="GITorious   Gérer et administrer ses dépôts GIT" />

## Installation des Gems:

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw3">cd</span> <span class="sy0">/</span>var<span class="sy0">/</span>www<span class="sy0">/</span>gitorious <span class="sy0">&&</span>  <span class="sy0">/</span>opt<span class="sy0">/</span>ruby-enterprise<span class="sy0">/</span>bin<span class="sy0">/</span>bundle <span class="kw2">install</span>
  </div>
</div>

<div class="filet">
</div>

<h3 class="articleTitle">
  Configuration des services:
</h3>

Nous devons à présent faire le nécessaire pour faire "tourner" tout ce joli monde sous forme de services.
Pour ce faire, nous allons copier les scripts Sphinx et git-daemon du répertoire

<div class="codecolorer-container text vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="text codecolorer">
    gitorious/doc/templates/ubuntu
  </div>
</div>

vers le répertoire

<div class="codecolorer-container text vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="text codecolorer">
    /etc/init.d
  </div>
</div>

. Attention pensez à changer le PATH vers votre interpréteur Ruby dans le script du git-daemon pour

<div class="codecolorer-container text vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="text codecolorer">
    /usr/bin/ruby
  </div>
</div>

**Script pour stomp (</p> <div class="codecolorer-container text vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="text codecolorer">
    /etc/init.d/stomp
  </div>
</div>

)</strong>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="co0">#!/bin/sh</span><br /> <span class="co0"># Start/stop the stompserver</span><br /> <span class="co0">#</span><br /> <span class="co0">### BEGIN INIT INFO</span><br /> <span class="co0"># Provides:          stomp</span><br /> <span class="co0"># Required-Start:    $local_fs $remote_fs $network $syslog</span><br /> <span class="co0"># Required-Stop:</span><br /> <span class="co0"># Default-Start:     2 3 4 5</span><br /> <span class="co0"># Default-Stop:      1</span><br /> <span class="co0"># Short-Description: Stomp</span><br /> <span class="co0"># Description:       Stomp</span><br /> <span class="co0">### END INIT INFO</span><br /> <br /> <span class="kw3">test</span> <span class="re5">-f</span> <span class="sy0">/</span>usr<span class="sy0">/</span>bin<span class="sy0">/</span>stompserver <span class="sy0">||</span> <span class="kw3">exit</span> <span class="nu0"></span><br /> <br /> . <span class="sy0">/</span>lib<span class="sy0">/</span>lsb<span class="sy0">/</span>init-functions<br /> <br /> <span class="kw1">case</span> <span class="st0">"$1"</span> <span class="kw1">in</span><br /> start<span class="br0">&#41;</span>  log_daemon_msg <span class="st0">"Starting stompserver"</span> <span class="st0">"stompserver"</span><br />     start-stop-daemon <span class="re5">--start</span> <span class="re5">--name</span> stompserver <span class="re5">--startas</span> <span class="sy0">/</span>usr<span class="sy0">/</span>bin<span class="sy0">/</span>stompserver <span class="re5">--background</span> <span class="re5">--user</span> <span class="kw2">git</span><br />     log_end_msg <span class="re4">$?</span><br />     <span class="sy0">;;</span><br /> stop<span class="br0">&#41;</span>  log_daemon_msg <span class="st0">"Stopping stompserver"</span> <span class="st0">"stompserver"</span><br />     start-stop-daemon <span class="re5">--stop</span> <span class="re5">--name</span> stompserver<br />     log_end_msg <span class="re4">$?</span><br />     <span class="sy0">;;</span><br /> restart<span class="br0">&#41;</span> log_daemon_msg <span class="st0">"Restarting stompserver"</span> <span class="st0">"stompserver"</span><br />     start-stop-daemon <span class="re5">--stop</span> <span class="re5">--retry</span> <span class="nu0">5</span> <span class="re5">--name</span> stompserver<br />     start-stop-daemon <span class="re5">--start</span> <span class="re5">--name</span> stompserver <span class="re5">--startas</span> <span class="sy0">/</span>usr<span class="sy0">/</span>bin<span class="sy0">/</span>stompserver <span class="re5">--background</span> <span class="re5">--user</span> <span class="kw2">git</span><br />     log_end_msg <span class="re4">$?</span><br />     <span class="sy0">;;</span><br /> status<span class="br0">&#41;</span><br />     status_of_proc <span class="sy0">/</span>usr<span class="sy0">/</span>bin<span class="sy0">/</span>stompserver stompserver <span class="sy0">&</span>amp;<span class="sy0">&</span>amp; <span class="kw3">exit</span> <span class="nu0"></span> <span class="sy0">||</span> <span class="kw3">exit</span> <span class="re4">$?</span><br />     <span class="sy0">;;</span><br /> <span class="sy0">*</span><span class="br0">&#41;</span>      log_action_msg <span class="st0">"Usage: /etc/init.d/stomp {start|stop|restart|status}"</span><br />     <span class="kw3">exit</span> <span class="nu0">2</span><br />     <span class="sy0">;;</span><br /> <span class="kw1">esac</span><br /> <span class="kw3">exit</span> <span class="nu0"></span>
  </div>
</div>

**Script pour poller (</p> <div class="codecolorer-container text vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="text codecolorer">
    /etc/init.d/git-poller
  </div>
</div>

)</strong>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="co0">#!/bin/sh</span><br /> <span class="co0"># Start/stop the git poller</span><br /> <span class="co0">#</span><br /> <span class="co0">### BEGIN INIT INFO</span><br /> <span class="co0"># Provides:          git-poller</span><br /> <span class="co0"># Required-Start:    stomp</span><br /> <span class="co0"># Required-Stop:</span><br /> <span class="co0"># Default-Start:     2 3 4 5</span><br /> <span class="co0"># Default-Stop:      1</span><br /> <span class="co0"># Short-Description: Gitorious poller</span><br /> <span class="co0"># Description:       Gitorious poller</span><br /> <span class="co0">### END INIT INFO</span><br /> <br /> <span class="sy0">/</span>bin<span class="sy0">/</span><span class="kw2">su</span> - <span class="kw2">git</span> <span class="re5">-c</span> <span class="st0">"cd /var/www/gitorious;RAILS_ENV=production script/poller $@"</span>
  </div>
</div>

On copie pour finir

<div class="codecolorer-container text vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="text codecolorer">
    gitorious-logrotate
  </div>
</div>

de

<div class="codecolorer-container text vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="text codecolorer">
    gitorious/doc/templates/ubuntu
  </div>
</div>

vers

<div class="codecolorer-container text vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="text codecolorer">
    /etc/logrotate.d/gitorious
  </div>
</div>

Il ne nous reste plus qu'à rendre tous ces scripts exécutables au démarrage

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">chmod</span> <span class="nu0">755</span> <span class="sy0">/</span>etc<span class="sy0">/</span>init.d<span class="sy0">/</span>git-ultrasphinx <span class="sy0">/</span>etc<span class="sy0">/</span>init.d<span class="sy0">/</span><span class="kw2">git-daemon</span> <span class="sy0">/</span>etc<span class="sy0">/</span>init.d<span class="sy0">/</span>stomp <span class="sy0">/</span>etc<span class="sy0">/</span>init.d<span class="sy0">/</span>git-poller<br /> <br /> update-rc.d stomp defaults<br /> update-rc.d <span class="kw2">git-daemon</span> defaults<br /> update-rc.d git-ultrasphinx defaults<br /> update-rc.d git-poller defaults
  </div>
</div>

<div class="filet">
</div>

<h3 class="articleTitle">
  Configuration d'Apache
</h3>

**Installer passenger:**

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
      <span class="sy0">/</span>var<span class="sy0">/</span>lib<span class="sy0">/</span>gems<span class="sy0">/</span><span class="nu0">1.8</span><span class="sy0">/</span>bin<span class="sy0">/</span>passenger-install-apache2-module
  </div>
</div>

Faire charger le module par Apache en créant le fichier

<div class="codecolorer-container text vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="text codecolorer">
    /etc/apache2/mods-available/passenger.load
  </div>
</div>

, ajoutez-lui:

<div class="codecolorer-container apache vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="apache codecolorer">
    <span class="kw1">LoadModule</span> passenger_module /var/lib/gems/<span class="nu0">1.8</span>/gems/passenger-2.2.10/ext/apache2/mod_passenger.so<br /> PassengerRoot /var/lib/gems/<span class="nu0">1.8</span>/gems/passenger-2.2.10<br /> PassengerRuby /usr/bin/ruby1.8
  </div>
</div>

On s'assure que tous les modules nécessaires sont chargés par Apache:

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
      a2enmod passenger<br />   a2enmod rewrite<br />   a2enmod ssl
  </div>
</div>

Et, pour finir, on redémarre Apache:

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="sy0">/</span>etc<span class="sy0">/</span>init.d<span class="sy0">/</span>apache2 restart
  </div>
</div>

Nous devrions à présent avoir un environnement apache correctement configuré. Ci-dessous deux exemples de vhost Apache ; grâce à Passenger nous n'avons plus à nous préoccuper d'assurer un système de proxy entre Apache et Mongrel par exemple:

<div class="codecolorer-container apache vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="apache codecolorer">
    <span class="kw1">ServerName</span> gitorious.avalon<br /> <br /> <span class="kw1">ServerAdmin</span> webmaster@localhost<br /> <span class="kw1">DocumentRoot</span> /var/www/gitorious/public<br /> <br /> <span class="kw1">ErrorLog</span> /var/log/apache2/gitorious-error.log<br /> <span class="co1"># Possible values include: debug, info, notice, warn, error, crit,</span><br /> <span class="co1"># alert, emerg.</span><br /> <span class="kw1">LogLevel</span> warn<br /> <br /> <span class="kw1">CustomLog</span> /var/log/apache2/gitorious-access.log combined<br /> <br /> XSendFile <span class="kw2">on</span><br /> XSendFileAllowAbove <span class="kw2">on</span>
  </div>
</div>

<div class="codecolorer-container apache vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="apache codecolorer">
    <span class="kw1">ServerAdmin</span> webmaster@localhost<br /> <span class="kw1">DocumentRoot</span> /var/www/gitorious/public<br /> <br /> <span class="kw1">ErrorLog</span> /var/log/apache2/gitorious-error.log<br /> <br /> <span class="co1"># Possible values include: debug, info, notice, warn, error, crit,</span><br /> <span class="co1"># alert, emerg.</span><br /> <span class="kw1">LogLevel</span> warn<br /> <br /> <span class="kw1">CustomLog</span> /var/log/apache2/gitorious-ssl_access.log combined<br /> <br /> <span class="kw1">SSLEngine</span> <span class="kw2">on</span><br /> <span class="kw1">SSLCertificateFile</span>    /etc/ssl/certs/ssl-cert-snakeoil.pem<br /> <span class="kw1">SSLCertificateKeyFile</span> /etc/ssl/private/ssl-cert-snakeoil.key<br /> <br /> XSendFile <span class="kw2">on</span><br /> XSendFileAllowAbove <span class="kw2">on</span>
  </div>
</div>

<div class="filet">
</div>

<h3 class="articleTitle">
  Configuration de Gitorious
</h3>

Avant de commencer la configuration de Gitorious il nous faut:

- Créer un utilisateur MySQL git avec des privilèges globaux sur la table

<div class="codecolorer-container text vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="text codecolorer">
    gitorious_production
  </div>
</div>

- Créer un utilisateur système git et préparer le terrain

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    adduser <span class="re5">--system</span> <span class="re5">--home</span> <span class="sy0">/</span>var<span class="sy0">/</span>www<span class="sy0">/</span>gitorious<span class="sy0">/</span> <span class="re5">--no-create-home</span> <span class="re5">--group</span> <span class="re5">--shell</span> <span class="sy0">/</span>bin<span class="sy0">/</span><span class="kw2">bash</span> <span class="kw2">git</span><br /> <span class="kw2">chown</span> <span class="re5">-R</span> git:git <span class="sy0">/</span>var<span class="sy0">/</span>www<span class="sy0">/</span>gitorious<br /> <br /> <span class="kw2">su</span> - <span class="kw2">git</span><br /> <span class="kw2">mkdir</span> .ssh<br /> <span class="kw2">touch</span> .ssh<span class="sy0">/</span>authorized_keys<br /> <span class="kw2">chmod</span> <span class="nu0">700</span> .ssh<br /> <span class="kw2">chmod</span> <span class="nu0">600</span> .ssh<span class="sy0">/</span>authorized_keys<br /> <span class="kw2">mkdir</span> tmp<span class="sy0">/</span>pids<br /> <span class="kw2">mkdir</span> repositories<br /> <span class="kw2">mkdir</span> tarballs<br /> <br /> <span class="kw2">cp</span> config<span class="sy0">/</span>database.sample.yml config<span class="sy0">/</span>database.yml<br /> <span class="kw2">cp</span> config<span class="sy0">/</span>gitorious.sample.yml config<span class="sy0">/</span>gitorious.yml<br /> <span class="kw2">cp</span> config<span class="sy0">/</span>broker.yml.example config<span class="sy0">/</span>broker.yml
  </div>
</div>

**databases.yml**

Supprimer toutes les sections sauf la partie

<div class="codecolorer-container text vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="text codecolorer">
    production
  </div>
</div>

, vous devriez avoir quelque chose dans ce style:

<div class="codecolorer-container yaml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="yaml codecolorer">
    <span class="co4">production</span>:<span class="co3"><br />   adapter</span><span class="sy2">: </span>mysql<span class="co3"><br />   database</span><span class="sy2">: </span>gitorious_production<span class="co3"><br />   username</span><span class="sy2">: </span>git<span class="co3"><br />   password</span><span class="sy2">: </span>password<span class="co3"><br />   host</span><span class="sy2">: </span>localhost<span class="co3"><br />   encoding</span><span class="sy2">: </span>utf8
  </div>
</div>

Il va de soi que les accès sont à personnaliser selon votre configuration.

**gitorious.yml**

Supprimez toutes les sections sauf la partie

<div class="codecolorer-container text vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="text codecolorer">
    production
  </div>
</div>

, selon ce que vous voulez faire votre configuration ressemblera à celle ci-dessous:

<div class="codecolorer-container yaml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="yaml codecolorer">
    <span class="co4">development</span><span class="sy2">:<br /> </span><span class="co4"><br /> production</span><span class="sy2">:<br /> </span><span class="co3"><br />   cookie_secret</span><span class="sy2">: </span>57d23d5a0b754ffcb8426e588b48fe26rffdq78<br /> <br />   <span class="co1"># The path where git repositories are stored. The actual (bare) repositories resides</span><br />   <span class="co1"># in repository_base_path/#{project.slug}/#{repository.name}.git/:</span><span class="co3"><br />   repository_base_path</span><span class="sy2">: </span><span class="co6">&quot;/var/www/gitorious/repositories&quot;</span><br /> <span class="co3"><br />   gitorious_client_host</span><span class="sy2">: </span>localhost<br /> <br />   <span class="co1"># Port the ./script/gitorious script should use:</span><span class="co3"><br />   gitorious_client_port</span><span class="sy2">: </span><span class="nu0">80</span><br /> <br />   <span class="co1"># Host which is serving the gitorious app, eg &quot;gitorious.org&quot;</span><span class="co3"><br />   gitorious_host</span><span class="sy2">: </span>gitorious.avalon<br /> <br />   <span class="co1"># User which is running git daemon</span><span class="co3"><br />   gitorious_user</span><span class="sy2">: </span>git<br /> <br />   <span class="co1"># Email address to the support for the Gitorious server</span><span class="co3"><br />   gitorious_support_email</span><span class="sy2">: </span>hosting@my-domain.com<br /> <br />   <span class="co1"># Enable or Disable Public Mode (true) or Private Mode (false)</span><span class="co3"><br />   public_mode</span><span class="sy2">: </span>true<br /> <br />   <span class="co1"># Where should we store generated tarballs?</span><br />   <span class="co1"># (should be readable by webserver, since we tell it to send the file using X-Sendfile)</span><span class="co3"><br />   archive_cache_dir</span><span class="sy2">: </span><span class="co6">&quot;/var/www/gitorious/tarballs-cache&quot;</span><br />   <span class="co1"># Which directory should we work in when we generate tarballs, before moving</span><br />   <span class="co1"># them to the above dir?</span><span class="co3"><br />   archive_work_dir</span><span class="sy2">: </span><span class="st0">"/var/www/gitorious/tarballs-work"</span><br /> <br />   <span class="co1"># is it only site admins who can create new projects?</span><span class="co3"><br />   only_site_admins_can_create_projects</span><span class="sy2">: </span>false<br /> <br />   <span class="co1"># Should we hide HTTP clone urls?</span><span class="co3"><br />   hide_http_clone_urls</span><span class="sy2">: </span>true<br /> <br />   <span class="co1"># Is this gitorious.org? Read: should we have a very flashy homepage?</span><span class="co3"><br />   is_gitorious_dot_org</span><span class="sy2">: </span>false<br /> <br />   <span class="co1"># Pick a default license</span><br />   <span class="co1">#default_license: GNU Affero General Public License (AGPLv3)</span>
  </div>
</div>

Attention

<div class="codecolorer-container text vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="text codecolorer">
    gitorious_host
  </div>
</div>

doit exactement correspondre au nom du host utilisé pour accéder à l'application sous peine de rencontrer quelques problèmes avec les cookies de session.

Gitorious est installé nous n'avons plus maintenant qu'à lancer le script d'initialisation:

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw3">export</span> <span class="re2">RAILS_ENV</span>=production<br /> rake db:create<br /> rake db:migrate<br /> rake ultrasphinx:bootstrap
  </div>
</div>

Ajouter ceci dans votre crontab pour l'indexation de Sphinx

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    crontab <span class="re5">-e</span>    <span class="sy0">*</span> <span class="sy0">*</span> <span class="sy0">*</span> <span class="sy0">*</span> <span class="sy0">*</span> <span class="kw3">cd</span> <span class="sy0">/</span>var<span class="sy0">/</span>www<span class="sy0">/</span>gitorious <span class="sy0">&&</span> <span class="sy0">/</span>usr<span class="sy0">/</span>bin<span class="sy0">/</span>rake ultrasphinx:index <span class="re2">RAILS_ENV</span>=production
  </div>
</div>

<div class="filet">
</div>

<h3 class="articleTitle">
  Finalisation
</h3>

Redémarrez apache:

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="sy0">/</span>etc<span class="sy0">/</span>init.d<span class="sy0">/</span>apache2 restart
  </div>
</div>

Créez un utilisateur admin pour votre application (en étant auth en tant que git)

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">env</span> <span class="re2">RAILS_ENV</span>=production ruby1.8 script<span class="sy0">/</span>create_admin
  </div>
</div>

> <div class="aparte">
>   <p>
>     Ci-dessous les commandes pour gérer vos utilisateurs à partir de la ligne de commande:
>   </p>
>
>   <ul>
>     <li>
>       <strong>Supprimer un utilisateur:</strong> <div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
>         <div class="bash codecolorer">
>           <span class="kw2">env</span> <span class="re2">RAILS_ENV</span>=production ruby1.8 script<span class="sy0">/</span>console<br /> <span class="sy0">></span> user = User.find_by_login <span class="st0">"Username"</span><br /> <span class="sy0">></span> user.destroy
>         </div>
>       </div>
>     </li>
>
>     <li>
>       <strong>Créer un nouveau rôle:</strong> <div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
>         <div class="bash codecolorer">
>           <span class="kw2">env</span> <span class="re2">RAILS_ENV</span>=production ruby1.8 script<span class="sy0">/</span>console<br /> <span class="sy0">></span> Role.create<span class="sy0">!</span><span class="br0">&#40;</span>:name =<span class="sy0">></span> <span class="st0">"Member"</span>, :kind =<span class="sy0">></span> Role::KIND_MEMBER<span class="br0">&#41;</span><br /> <span class="sy0">></span> Role.create<span class="sy0">!</span><span class="br0">&#40;</span>:name =<span class="sy0">></span> <span class="st0">"Administrator"</span>, :kind =<span class="sy0">></span> Role::KIND_ADMIN<span class="br0">&#41;</span>
>         </div>
>       </div>
>     </li>
>   </ul>
> </div>

<div class="filet">
</div>

<h3 class="articleTitle">
  Conclusion
</h3>

Voilà, nous disposons à présent d'un outil permettant d'héberger nos propres dépôts GIT et de bénéficier d'une interface "user friendly" pour les administrer. Ne connaissant pas bien les possibilités de GitHub, je me garderai bien de faire une comparaison entre les deux outils ; d'ailleurs, je ne suis même pas certain qu'une réelle concurrence les oppose. Cependant GITORIOUS me semble être un très bon outil pour ceux qui souhaitent garder "la main" sur leurs sources, et je l'apprécie d'autant plus qu'il se révèle assez intuitif à l'usage.

<h3 class="articleTitle">
  Sources
</h3>

Cet article est largement inspiré de [Setting up Gitorious on your own server][5] et de [Gitorious Ubuntu Installation][6]

Comme d'habitude corrections, remarques et critiques sont les bienvenues <img src="http://old-blog.elao.dev/wp-includes/images/smilies/icon_smile.gif" alt="icon smile GITorious   Gérer et administrer ses dépôts GIT" class="wp-smiley" title="GITorious   Gérer et administrer ses dépôts GIT" />

 [1]: http://www.elao.org/wp-content/uploads/2011/05/gitorious.png
 [2]: http://www.gitorious.org/
 [3]: http://www.rubyenterpriseedition.com/
 [4]: http://activemq.apache.org/
 [5]: http://cjohansen.no/en/ruby/setting_up_gitorious_on_your_own_server
 [6]: https://gitorious.org/gitorious/pages/UbuntuInstallation