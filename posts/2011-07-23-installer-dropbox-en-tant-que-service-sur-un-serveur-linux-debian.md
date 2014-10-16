
Bonjour à tous !

Je vais vous parler aujourd&#8217;hui d&#8217;une utilisation de **Dropbox** un peu différente de l&#8217;utilisation desktop **&#8220;standard&#8221;**. En effet, en travaillant sur un projet nécessitant l&#8217;archivage de certains documents, nous avons été confrontés à la problématique du &#8220;tampon&#8221; entre l&#8217;upload du document et son indexation dans une GED dédiée. De fait nous avons décidé d&#8217;utiliser Dropbox comme tampon, l&#8217;idée étant d&#8217;avoir une machine cliente déposant des fichiers dans un partage Dropbox, lui-même synchronisé sur un serveur de production, lequel, de son côté, exploitera les-dits fichiers avant de les supprimer.

Pour ceux qui ne connaîtraient pas ce très bon outil qu&#8217;est Dropbox, je vous invite à aller jeter un petit coup d&#8217;oeil à <a href="http://fr.wikipedia.org/wiki/Dropbox" target="_blank">Wikipedia</a> et à leur <a href="http://www.dropbox.com" target="_blank">site officiel</a>.

> <div class="aparte">
>   En tout premier lieu et avant de commencer voici les pré-requis à l&#8217;installation du démon:</p> <ul>
>     <li>
>       GCC 2.4 mini
>     </li>
>     <li>
>       wget
>     </li>
>     <li>
>       Python 2.5 ou plus
>     </li>
>     <li>
>       Un navigateur internet (On notera que je ne cite volontairement pas de nom;))
>     </li>
>   </ul>
> </div>

## Installation

Nous allons commencer par créer un utilisateur dédié au service ayant pour groupe dropbox :

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    groupadd dropbox<br /> useradd <span class="re5">-r</span> <span class="re5">-d</span> <span class="sy0">/</span>etc<span class="sy0">/</span>dropbox <span class="re5">-g</span> dropbox <span class="re5">-s</span> <span class="sy0">/</span>bin<span class="sy0">/</span><span class="kw2">false</span> dropbox
  </div>
</div>

Nous pouvons récupérer la dernière version de Dropbox.  
Il existe deux versions disponibles : 32bits / 64 bits ; à vous de choisir la bonne !

**32 bits:**

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">wget</span> <span class="re5">-O</span> <span class="sy0">/</span>tmp<span class="sy0">/</span>dropbox.tar.gz <span class="sy0">&</span>quot;http:<span class="sy0">//</span>www.dropbox.com<span class="sy0">/</span>download<span class="sy0">/</span>?<span class="re2">plat</span>=lnx.x86<span class="sy0">&</span>quot;
  </div>
</div>

**64 bits:**

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">wget</span> <span class="re5">-O</span> <span class="sy0">/</span>tmp<span class="sy0">/</span>dropbox.tar.gz <span class="sy0">&</span>quot;http:<span class="sy0">//</span>www.dropbox.com<span class="sy0">/</span>download<span class="sy0">/</span>?<span class="re2">plat</span>=lnx.x86_64<span class="sy0">&</span>quot;
  </div>
</div>

Nous créons les deux répertoires que nous allons utiliser, à savoir /etc/dropbox pour la config et /usr/local/dropbox pour l&#8217;applicatif.

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">mkdir</span> <span class="re5">-p</span> <span class="sy0">/</span>usr<span class="sy0">/</span>local<span class="sy0">/</span>dropbox <span class="sy0">/</span>etc<span class="sy0">/</span>dropbox<br /> <span class="kw2">chown</span> dropbox.dropbox <span class="sy0">/</span>etc<span class="sy0">/</span>dropbox<br /> <span class="kw2">chmod</span> <span class="nu0">700</span> <span class="sy0">/</span>etc<span class="sy0">/</span>dropbox
  </div>
</div>

Nous décompressons ensuite l&#8217;archive :

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">tar</span> xvzf <span class="sy0">/</span>tmp<span class="sy0">/</span>dropbox.tar.gz <span class="re5">-C</span> <span class="sy0">/</span>usr<span class="sy0">/</span>local<span class="sy0">/</span>dropbox <span class="re5">--strip</span> <span class="nu0">1</span><br /> <span class="kw2">rm</span> <span class="sy0">/</span>tmp<span class="sy0">/</span>dropbox.tar.gz
  </div>
</div>

Nous allons à présent mettre en place un script init, permettant d&#8217;administrer le service :

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="co0">### BEGIN INIT INFO</span><br /> <span class="co0"># Provides: &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;dropbox</span><br /> <span class="co0"># Required-Start:</span><br /> <span class="co0"># Required-Stop:</span><br /> <span class="co0"># Default-Start: &nbsp; &nbsp; 2 3 4 5</span><br /> <span class="co0"># Default-Stop: &nbsp; &nbsp; &nbsp;0 1 6</span><br /> <span class="co0"># Short-Description: starts the dropbox service</span><br /> <span class="co0"># Description: &nbsp; &nbsp; &nbsp; starts dropbox using start-stop-daemon</span><br /> <span class="co0">### END INIT INFO</span><br /> <br /> <span class="re2">DROPBOX_USERS</span>=<span class="st0">"dropbox"</span><br /> <span class="re2">DAEMON</span>=<span class="sy0">/</span>usr<span class="sy0">/</span>local<span class="sy0">/</span>dropbox<span class="sy0">/</span>dropbox<br /> <span class="kw3">unset</span> DISPLAY<br /> <br /> start<span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="br0">&#123;</span><br /> &nbsp; &nbsp; <span class="kw3">echo</span> <span class="st0">"Starting dropbox..."</span><br /> &nbsp; &nbsp; <span class="kw1">for</span> dbuser <span class="kw1">in</span> <span class="re1">$DROPBOX_USERS</span>; <span class="kw1">do</span><br /> &nbsp; &nbsp; &nbsp; &nbsp; <span class="re2">HOMEDIR</span>=$<span class="br0">&#40;</span><span class="kw2">getent</span> <span class="kw2">passwd</span> <span class="re1">$dbuser</span> <span class="sy0">|</span> <span class="kw2">cut</span> -d: -f6<span class="br0">&#41;</span><br /> &nbsp; &nbsp; &nbsp; &nbsp; <span class="kw1">if</span> <span class="br0">&#91;</span> <span class="re5">-x</span> <span class="re1">$DAEMON</span> <span class="br0">&#93;</span>; <span class="kw1">then</span><br /> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <span class="re2">HOME</span>=<span class="st0">"<span class="es2">$HOMEDIR</span>"</span> start-stop-daemon <span class="re5">-b</span> <span class="re5">-o</span> <span class="re5">-c</span> <span class="re1">$dbuser</span> <span class="re5">-S</span> <span class="re5">-u</span> <span class="re1">$dbuser</span> <span class="re5">-x</span> <span class="re1">$DAEMON</span><br /> &nbsp; &nbsp; &nbsp; &nbsp; <span class="kw1">fi</span><br /> &nbsp; &nbsp; <span class="kw1">done</span><br /> <span class="br0">&#125;</span><br /> <br /> stop<span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="br0">&#123;</span><br /> &nbsp; &nbsp; <span class="kw3">echo</span> <span class="st0">"Stopping dropbox..."</span><br /> &nbsp; &nbsp; <span class="kw1">for</span> dbuser <span class="kw1">in</span> <span class="re1">$DROPBOX_USERS</span>; <span class="kw1">do</span><br /> &nbsp; &nbsp; &nbsp; &nbsp; <span class="re2">HOMEDIR</span>=$<span class="br0">&#40;</span><span class="kw2">getent</span> <span class="kw2">passwd</span> <span class="re1">$dbuser</span> <span class="sy0">|</span> <span class="kw2">cut</span> -d: -f6<span class="br0">&#41;</span><br /> &nbsp; &nbsp; &nbsp; &nbsp; <span class="kw1">if</span> <span class="br0">&#91;</span> <span class="re5">-x</span> <span class="re1">$DAEMON</span> <span class="br0">&#93;</span>; <span class="kw1">then</span><br /> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; start-stop-daemon <span class="re5">-o</span> <span class="re5">-c</span> <span class="re1">$dbuser</span> <span class="re5">-K</span> <span class="re5">-u</span> <span class="re1">$dbuser</span> <span class="re5">-x</span> <span class="re1">$DAEMON</span><br /> &nbsp; &nbsp; &nbsp; &nbsp; <span class="kw1">fi</span><br /> &nbsp; &nbsp; <span class="kw1">done</span><br /> <span class="br0">&#125;</span><br /> <br /> status<span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="br0">&#123;</span><br /> &nbsp; &nbsp; <span class="kw1">for</span> dbuser <span class="kw1">in</span> <span class="re1">$DROPBOX_USERS</span>; <span class="kw1">do</span><br /> &nbsp; &nbsp; &nbsp; &nbsp; <span class="re2">dbpid</span>=$<span class="br0">&#40;</span>pgrep <span class="re5">-u</span> <span class="re1">$dbuser</span> dropbox<span class="br0">&#41;</span><br /> &nbsp; &nbsp; &nbsp; &nbsp; <span class="kw1">if</span> <span class="br0">&#91;</span> <span class="re5">-z</span> <span class="re1">$dbpid</span> <span class="br0">&#93;</span> ; <span class="kw1">then</span><br /> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <span class="kw3">echo</span> <span class="st0">"dropboxd for USER <span class="es2">$dbuser</span>: not running."</span><br /> &nbsp; &nbsp; &nbsp; &nbsp; <span class="kw1">else</span><br /> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <span class="kw3">echo</span> <span class="st0">"dropboxd for USER <span class="es2">$dbuser</span>: running (pid <span class="es2">$dbpid</span>)"</span><br /> &nbsp; &nbsp; &nbsp; &nbsp; <span class="kw1">fi</span><br /> &nbsp; &nbsp; <span class="kw1">done</span><br /> <span class="br0">&#125;</span><br /> <br /> <span class="kw1">case</span> <span class="st0">"$1"</span> <span class="kw1">in</span><br /> &nbsp; start<span class="br0">&#41;</span><br /> &nbsp; &nbsp; start<br /> &nbsp; &nbsp; <span class="kw2">sleep</span> <span class="nu0">1</span><br /> &nbsp; &nbsp; status<br /> &nbsp; &nbsp; <span class="sy0">;;</span><br /> <br /> &nbsp; stop<span class="br0">&#41;</span><br /> &nbsp; &nbsp; stop<br /> &nbsp; &nbsp; <span class="kw2">sleep</span> <span class="nu0">1</span><br /> &nbsp; &nbsp; status<br /> &nbsp; &nbsp; <span class="sy0">;;</span><br /> <br /> &nbsp; restart<span class="sy0">|</span>reload<span class="sy0">|</span>force-reload<span class="br0">&#41;</span><br /> &nbsp; &nbsp; stop<br /> &nbsp; &nbsp; start<br /> &nbsp; &nbsp; <span class="kw2">sleep</span> <span class="nu0">1</span><br /> &nbsp; &nbsp; status<br /> &nbsp; &nbsp; <span class="sy0">;;</span><br /> <br /> &nbsp; status<span class="br0">&#41;</span><br /> &nbsp; &nbsp; status<br /> &nbsp; &nbsp; <span class="sy0">;;</span><br /> <br /> &nbsp; <span class="sy0">*</span><span class="br0">&#41;</span><br /> &nbsp; &nbsp; <span class="kw3">echo</span> <span class="st0">"Usage: /etc/init.d/dropbox {start|stop|reload|force-reload|restart|status}"</span><br /> &nbsp; &nbsp; <span class="kw3">exit</span> <span class="nu0">1</span><br /> <br /> <span class="kw1">esac</span><br /> <br /> <span class="kw3">exit</span> <span class="nu0"></span>
  </div>
</div>

Ajoutons ce script au démarrage de la machine :

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">chmod</span> a+x <span class="sy0">/</span>etc<span class="sy0">/</span>init.d<span class="sy0">/</span>dropbox<br /> update-rc.d dropbox defaults
  </div>
</div>

## Changer le répertoire par défaut de Dropbox

Par défaut le répertoire utilisé pour les fichiers est créé dans le compte de l&#8217;utilisateur exécutant le démon, nous allons voir ci-dessous comment modifier ce répertoire. Certains me feront remarquer qu&#8217;un bon vieux symlink ferait parfaitement l&#8217;affaire, mais j&#8217;aurais tendance à privilégier une méthode un peu plus &#8220;académique&#8221;.

Dropbox utilise une base sqlite pour stocker ses données et notamment sa configuration.

Pour ceux qui maîtrisent sqlite et qui souhaitent aller modifier cette configuration en base, il faut éditer le champ &#8220;**dropbox_path**&#8221; de la table config, mais attention, la valeur du &#8220;path&#8221; doit être encodé en base 64 ! Pour les autres, vous pouvez utiliser le script Python fourni ici, pour appliquer cette modification en exécutant la commande suivante :

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    $ <span class="kw2">cp</span> <span class="sy0">/</span>etc<span class="sy0">/</span>dropbox<span class="sy0">/</span>.dropbox<span class="sy0">/</span>dropbox.db dropbox.db.backup<br /> $ <span class="kw2">wget</span> http:<span class="sy0">//</span>dl.dropbox.com<span class="sy0">/</span>u<span class="sy0">/</span><span class="nu0">119154</span><span class="sy0">/</span>permalink<span class="sy0">/</span>dropboxdir.py<br /> $ <span class="kw2">chmod</span> +x dropboxdir.py<br /> $ <span class="kw2">mv</span> ~<span class="sy0">/</span>Dropbox <span class="sy0">/</span>path<span class="sy0">/</span>to<span class="sy0">/</span>my<span class="sy0">/</span>new<span class="sy0">/</span><span class="kw2">dir</span><br /> $ .<span class="sy0">/</span>dropboxdir <span class="re5">--setfolder</span>=<span class="sy0">/</span>path<span class="sy0">/</span>to<span class="sy0">/</span>my<span class="sy0">/</span>new<span class="sy0">/</span><span class="kw2">dir</span>
  </div>
</div>

**Attention !** Si le démon Dropbox a été lié à un compte **AVANT** la relocalisation du répertoire, et si le nouveau répertoire est vide, Dropbox va considérer que les fichiers ont été supprimés localement et effectuer la modification sur le répertoire distant, ce qui entrainera la suppression de **TOUS** vos fichiers distants (et de facto la suppression des fichiers sur tous les postes clients associés au compte Dropbox).

Nous pouvons à présent démarrer le service Dropbox et &#8220;attacher&#8221; notre machine à notre compte.

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    $ <span class="sy0">/</span>usr<span class="sy0">/</span>local<span class="sy0">/</span>dropbox<span class="sy0">/</span>dropbox start<br /> <br /> This client is not linked to any account...<br /> Please visit https:<span class="sy0">//</span>www.dropbox.com<span class="sy0">/</span>cli_link?<span class="re2">host_id</span>=7d44a557aa58f285f2da0x67334d02c1 to <span class="kw2">link</span> this machine.
  </div>
</div>

Il ne reste à présent qu&#8217;à copier / coller cette url dans notre navigateur, ce qui devrait ajouter notre machine aux machines autorisées à synchroniser le contenu de notre Dropbox.

Vous pouvez à présent arrêter Dropbox coté serveur et le relancer &#8220;proprement&#8221; en tant que service avec un:

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="sy0">/</span>etc<span class="sy0">/</span>init.d<span class="sy0">/</span>dropbox start
  </div>
</div>