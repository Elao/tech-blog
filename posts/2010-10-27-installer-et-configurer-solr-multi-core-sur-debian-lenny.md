
</a>Bonjour à tous !

Confrontés depuis quelques temps à la problématique d&#8217;avoir plusieurs moteurs de recherche de type solR sur un même serveur nous avons décidé de mettre en application la gestion multi-core de solR (à partir de la version 1.4) permettant de gérer plusieurs &#8220;core&#8221; et donc des indexes différents en fonction des applications via une seule et même instance solR.

Il existe des paquets Debian (apache-solr, tomcat 5.5) qui permettent d&#8217;avoir une instance fonctionnelle en peu de temps, mais j&#8217;ai pu lire chez nos amis anglophones que cela ne semble pas fonctionner du feu de dieu.

Nous allons donc partir sur une installation &#8220;hors gestionnaire de paquets&#8221; de Tomcat / apache-solR &#8230;

## Installation de Tomcat:

La première étape pour cette installation est d&#8217;avoir un environnement Java fonctionnel, nous disposons donc de plusieurs solutions, à savoir, utiliser les paquets openJDK de Debian ou utiliser les paquets de Sun (disponibles en activant les dépôts non-free) sun-java6-jre.

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">apt-get install</span> openjdk-<span class="nu0">6</span>-jdk openjdk-<span class="nu0">6</span>-jre
  </div>
</div>

Nous pouvons à présent récupérer **Tomcat 6.0** (pour l&#8217;instant la version 7 est en bêta et comme le java c&#8217;est pas mon dada, je préfère partir sur des versions stables &#8230;)

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">wget</span> http:<span class="sy0">//</span>mirror.mkhelif.fr<span class="sy0">/</span>apache<span class="sy0">/</span>tomcat<span class="sy0">/</span>tomcat-<span class="nu0">6</span><span class="sy0">/</span>v6.0.29<span class="sy0">/</span>bin<span class="sy0">/</span>apache-tomcat-6.0.29.zip
  </div>
</div>

J&#8217;ai choisi d&#8217;installer Tomcat dans **/usr/share/tomcat** correspondant à l&#8217;organisation Debian, pour ceux qui utilisent de moins bonnes distrib&#8217; (troll inside), libre à vous de le déployer ailleurs (**/opt/local** par exemple qui sera peut-être plus familier pour des utilisateurs de Red Hat, Fedora &#8230; )

Une fois l&#8217;archive décompressée dans **/usr/share/tomcat** nous pouvons ajouter un script de démarrage dans **/etc/init.d**.  
Script simpliste qui ne demande qu&#8217;à être amélioré (à vos commentaires):

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="co0">#!/bin/sh</span><br /> <span class="co0"># Tomcat Init-Script</span><br /> <br /> <span class="kw3">export</span> <span class="re2">JAVA_OPTS</span>=<span class="st0">"<span class="es2">$JAVA_OPTS</span> -Dsolr.solr.home=/usr/share/tomcat/solr"</span><br /> <br /> <span class="kw1">case</span> <span class="re4">$1</span> <span class="kw1">in</span><br /> &nbsp;start<span class="br0">&#41;</span><br /> &nbsp;<span class="kw2">sh</span> <span class="sy0">/</span>usr<span class="sy0">/</span>share<span class="sy0">/</span>tomcat<span class="sy0">/</span>bin<span class="sy0">/</span>startup.sh<br /> &nbsp;<span class="sy0">;;</span><br /> <br /> &nbsp;stop<span class="br0">&#41;</span><br /> &nbsp;<span class="kw2">sh</span> <span class="sy0">/</span>usr<span class="sy0">/</span>share<span class="sy0">/</span>tomcat<span class="sy0">/</span>bin<span class="sy0">/</span>shutdown.sh<br /> &nbsp;<span class="sy0">;;</span><br /> <br /> &nbsp;restart<span class="br0">&#41;</span><br /> &nbsp;<span class="kw2">sh</span> <span class="sy0">/</span>usr<span class="sy0">/</span>share<span class="sy0">/</span>tomcat<span class="sy0">/</span>bin<span class="sy0">/</span>shutdown.sh<br /> &nbsp;<span class="kw2">sh</span> <span class="sy0">/</span>usr<span class="sy0">/</span>share<span class="sy0">/</span>tomcat<span class="sy0">/</span>bin<span class="sy0">/</span>startup.sh<br /> &nbsp;<span class="sy0">;;</span><br /> <br /> <span class="kw1">esac</span><br /> <span class="kw3">exit</span> <span class="nu0"></span>
  </div>
</div>

Nous pouvons à présent démarrer notre serveur Tomcat via la commande

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="sy0">/</span>etc<span class="sy0">/</span>init.d<span class="sy0">/</span>tomcat start
  </div>
</div>

<div class="aparte">
  Si vous avez droit à ce message d&#8217;erreur &#8230;</p> <div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="bash codecolorer">
      Cannot <span class="kw2">find</span> <span class="sy0">/</span>usr<span class="sy0">/</span>share<span class="sy0">/</span>tomcat<span class="sy0">/</span>bin<span class="sy0">/</span>catalina.sh<br /> <span class="sy0"><</span>This <span class="kw2">file</span> is needed to run this program<span class="sy0">></span>
    </div>
  </div>
  
  <p>
    &#8230; pas de panique, il suffit de rajouter les droits d&#8217;exécution au(x) script(s) correspondant(s)
  </p>
</div>

Si tout fonctionne correctement, en accédant à votre serveur sur le port 8080 vous devriez avoir cette page qui s&#8217;affiche, signe que tomcat fonctionne correctement:

<div class="ngg-gallery-singlepic-image ngg-center" style="max-width: 640px">
  <a href="http://old-blog.elao.dev/wp-content/gallery/tomcat-solr/screen-shot-2010-10-22-at-11-34-50.png"
		     title=""
             data-src="http://old-blog.elao.dev/wp-content/gallery/tomcat-solr/screen-shot-2010-10-22-at-11-34-50.png"
             data-thumbnail="http://old-blog.elao.dev/wp-content/gallery/tomcat-solr/thumbs/thumbs_screen-shot-2010-10-22-at-11-34-50.png"
             data-image-id="3"
             data-title="screen-shot-2010-10-22-at-11-34-50"
             data-description=""
             target='_self'
             class="shutterset_4b129faa71d06c3c2ea71123183c6ee3"> <img class="ngg-singlepic"
             src="http://www.elao.com/blog/nextgen-image/3/640x480x100/f83a767c5a0dd51645be6acfbd55276c"
             alt="screen-shot-2010-10-22-at-11-34-50"
             title="screen-shot-2010-10-22-at-11-34-50"
              width="640" /> </a>
</div>

<span></span> 

Nous allons à présent devoir accéder à l&#8217;administration du serveur Tomcat via l&#8217;URL: <span style="font-family: Arial, Helvetica, sans-serif; color: #333333;"><span style="line-height: 18px;"><strong>http://my_server:8080/</strong></span></span><span style="font-family: Arial, Helvetica, sans-serif; line-height: 18px; color: #333333;"><strong>manager/html</strong></span>

Cette page est normalement protégée, il nous faut donc configurer un accès administrateur pour pouvoir y accéder.  
Nous allons pour se faire modifier le fichier ** /usr/share/tomcat/conf/tomcat-users.xml**

<div class="codecolorer-container xml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="xml codecolorer">
    <span class="sc3"><span class="re1"><?xml</span> <span class="re0">version</span>=<span class="st0">'1.0'</span> <span class="re0">encoding</span>=<span class="st0">'utf-8'</span><span class="re2">?></span></span><br /> <span class="sc3"><span class="re1"><tomcat-users<span class="re2">></span></span></span><br /> <span class="sc3"><span class="re1"><role</span> <span class="re0">rolename</span>=<span class="st0">"admin"</span><span class="re2">/></span></span><br /> <span class="sc3"><span class="re1"><role</span> <span class="re0">rolename</span>=<span class="st0">"manager"</span><span class="re2">/></span></span><br /> <span class="sc3"><span class="re1"><user</span> <span class="re0">username</span>=<span class="st0">"tomcat-admin"</span> <span class="re0">password</span>=<span class="st0">"tomcat"</span> <span class="re0">roles</span>=<span class="st0">"admin,manager"</span><span class="re2">/></span></span><br /> <span class="sc3"><span class="re1"></tomcat-users<span class="re2">></span></span></span>
  </div>
</div>

On redémarre à présent tomcat &#8230; et on s&#8217;authentifie pour arriver sur cette page:

<div class="ngg-gallery-singlepic-image ngg-center" style="max-width: 640px">
  <a href="http://old-blog.elao.dev/wp-content/gallery/tomcat-solr/screen-shot-2010-10-22-at-12-22-39.png"
		     title=""
             data-src="http://old-blog.elao.dev/wp-content/gallery/tomcat-solr/screen-shot-2010-10-22-at-12-22-39.png"
             data-thumbnail="http://old-blog.elao.dev/wp-content/gallery/tomcat-solr/thumbs/thumbs_screen-shot-2010-10-22-at-12-22-39.png"
             data-image-id="4"
             data-title="screen-shot-2010-10-22-at-12-22-39"
             data-description=""
             target='_self'
             class="shutterset_4b02ddf701e9bc5ecc8842fec1f17ec7"> <img class="ngg-singlepic"
             src="http://www.elao.com/blog/nextgen-image/4/640x480x100/10cae43336a20e566b62163b049e18de"
             alt="screen-shot-2010-10-22-at-12-22-39"
             title="screen-shot-2010-10-22-at-12-22-39"
              width="640" /> </a>
</div>

<span></span> 

## Installation de SolR

Premièrement commençons par télécharger solR &#8230;

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">wget</span> http:<span class="sy0">//</span>mir2.ovh.net<span class="sy0">/</span>ftp.apache.org<span class="sy0">/</span>dist<span class="sy0">//</span>lucene<span class="sy0">/</span>solr<span class="sy0">/</span>1.4.1<span class="sy0">/</span>apache-solr-1.4.1.zip<br /> <span class="kw2">unzip</span> apache-solr-1.4.1.zip
  </div>
</div>

Une fois que nous avons récupéré solR, nous devons faire la liaison avec Tomcat

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">cp</span> ~<span class="sy0">/</span>apache-solr-1.4.1<span class="sy0">/</span>dist<span class="sy0">/</span>apache-solr-1.4.1.war <span class="sy0">/</span>usr<span class="sy0">/</span>share<span class="sy0">/</span>tomcat<span class="sy0">/</span>webapps<span class="sy0">/</span>solr.war
  </div>
</div>

Nous copions ensuite le schema de la config solR donnée en exemple:

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">cp</span> <span class="re5">-r</span> ~<span class="sy0">/</span>apache-solr-1.4.1<span class="sy0">/</span>example<span class="sy0">/</span>solr<span class="sy0">/</span> <span class="sy0">/</span>usr<span class="sy0">/</span>share<span class="sy0">/</span>tomcat<span class="sy0">/</span>
  </div>
</div>

### Configuration du multi-core

Nous allons pour commencer copier le fichier **solr.xml** de l&#8217;exemple &#8220;multi-core&#8221; fournit avec le paquet solR

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">cp</span> ~<span class="sy0">/</span>apache-solr-1.4.1<span class="sy0">/</span>example<span class="sy0">/</span>multicore<span class="sy0">/</span>solr.xml <span class="sy0">/</span>usr<span class="sy0">/</span>share<span class="sy0">/</span>tomcat<span class="sy0">/</span>solr<span class="sy0">/</span>solr.xml
  </div>
</div>

A présent, il nous faut créer un répertoire à l&#8217;intérieur de **/usr/share/tomcat/solr** pour chacune des applications (sites) pour lesquelles nous souhaitons utiliser solR.  
Par exemple dans le cas ou nous avons 2 sites distincts, **&#8220;mon-super-site.com&#8221;** et **&#8220;best-site-online.com&#8221;** et que solR doit être utilisé par les deux nous allons avoir deux répertoires:

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="sy0">/</span>usr<span class="sy0">/</span>share<span class="sy0">/</span>tomcat<span class="sy0">/</span>solr<span class="sy0">/</span>mon-super-site<br /> <span class="sy0">/</span>usr<span class="sy0">/</span>share<span class="sy0">/</span>tomcat<span class="sy0">/</span>solr<span class="sy0">/</span>best-site-online
  </div>
</div>

Nous copions pour finir **/usr/share/tomcat/solr/conf** dans chacun des répertoires ci-dessus créés. Une fois terminé chaque répertoire doit avoir une copie de **/conf**

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">cp</span> <span class="re5">-r</span> <span class="sy0">/</span>usr<span class="sy0">/</span>share<span class="sy0">/</span>tomcat<span class="sy0">/</span>solr<span class="sy0">/</span>conf <span class="sy0">/</span>usr<span class="sy0">/</span>share<span class="sy0">/</span>tomcat<span class="sy0">/</span>solr<span class="sy0">/</span>mon-super-site<span class="sy0">/</span>conf<br /> <span class="kw2">cp</span> <span class="re5">-r</span> <span class="sy0">/</span>usr<span class="sy0">/</span>share<span class="sy0">/</span>tomcat<span class="sy0">/</span>solr<span class="sy0">/</span>conf <span class="sy0">/</span>usr<span class="sy0">/</span>share<span class="sy0">/</span>tomcat<span class="sy0">/</span>solr<span class="sy0">/</span>best-site-online<span class="sy0">/</span>conf
  </div>
</div>

Attention à bien conserver le répertoire conf initial car même si chacun de nos sites aura sa propre configuration le core de solR s&#8217;en sert toujours.  
Nous pouvons maintenant modifier le fichier **solr.xml** (**/usr/share/tomcat/solr/solr.xml**):

<div class="codecolorer-container xml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="xml codecolorer">
    <span class="sc3"><span class="re1"><?xml</span> <span class="re0">version</span>=<span class="st0">"1.0"</span> <span class="re0">encoding</span>=<span class="st0">"UTF-8"</span> <span class="re2">?></span></span><br /> <span class="sc-1"><!--</span><br /> <span class="sc-1"> All (relative) paths are relative to the installation path</span><br /> <br /> <span class="sc-1"> &nbsp;persistent: Save changes made via the API to this file</span><br /> <span class="sc-1"> &nbsp;sharedLib: path to a lib directory that will be shared across all cores</span><br /> <span class="sc-1">--></span><br /> <span class="sc3"><span class="re1"><solr</span> <span class="re0">persistent</span>=<span class="st0">"false"</span><span class="re2">></span></span><br /> &nbsp; <span class="sc-1"><!--</span><br /> <span class="sc-1"> &nbsp;adminPath: RequestHandler path to manage cores.</span><br /> <span class="sc-1"> &nbsp; &nbsp;If 'null' (or absent), cores will not be manageable via request handler</span><br /> <span class="sc-1"> &nbsp;--></span><br /> &nbsp; <span class="sc3"><span class="re1"><cores</span> <span class="re0">adminPath</span>=<span class="st0">"/admin/cores"</span><span class="re2">></span></span><br /> &nbsp; &nbsp; <span class="sc3"><span class="re1"><core</span> <span class="re0">name</span>=<span class="st0">"mon-super-site"</span> <span class="re0">instanceDir</span>=<span class="st0">"monsupersite"</span><span class="re2">></span></span><br /> &nbsp; &nbsp; &nbsp; &nbsp;<span class="sc3"><span class="re1"><property</span> <span class="re0">name</span>=<span class="st0">"dataDir"</span> <span class="re0">value</span>=<span class="st0">"/usr/share/tomcat/solr/monsupersite/data"</span> <span class="re2">/></span></span><br /> &nbsp; &nbsp; <span class="sc3"><span class="re1"></core<span class="re2">></span></span></span><br /> &nbsp; &nbsp; <span class="sc3"><span class="re1"><core</span> <span class="re0">name</span>=<span class="st0">"best-site-online"</span> <span class="re0">instanceDir</span>=<span class="st0">"bestsiteonline"</span><span class="re2">></span></span><br /> &nbsp; &nbsp; &nbsp; &nbsp;<span class="sc3"><span class="re1"><property</span> <span class="re0">name</span>=<span class="st0">"dataDir"</span> <span class="re0">value</span>=<span class="st0">"/usr/share/tomcat/solr/bestsiteonline/data"</span> <span class="re2">/></span></span><br /> &nbsp; &nbsp; &nbsp;<span class="sc3"><span class="re1"></core<span class="re2">></span></span></span><br /> &nbsp; <span class="sc3"><span class="re1"></cores<span class="re2">></span></span></span><br /> <span class="sc3"><span class="re1"></solr<span class="re2">></span></span></span>
  </div>
</div>

Nous devons également spécifier l&#8217;emplacement de nos indexes (stockés par solR dans **solr/data** relativement au répertoire courant).  
Nous avons pris la décisions de stocker les indexes de solR dans le répertoire correspondant à l&#8217;application concernée, par exemple **&#8220;monsupersite&#8221;**.

Pour ce cas nous modifierons donc la proprité dataDir du fichier **solrconfig.xml** situé dans **/usr/share/tomcat/solr/monsupersite/conf** de cette façon:

<div class="codecolorer-container xml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="xml codecolorer">
    <span class="sc3"><span class="re1"><dataDir<span class="re2">></span></span></span>${solr.data.dir:/usr/share/tomcat/solr/monsupersite/data}<span class="sc3"><span class="re1"></dataDir<span class="re2">></span></span></span>
  </div>
</div>

Nous pouvons redémarrer notre serveur tomcat !

Si tout s&#8217;est bien déroulé vous devriez pouvoir accéder à la page **http://my-server:8080/solr/** qui devrait vous répondre par un poli et gratifiant &#8220;Welcome to Solr!&#8221;

Commentaires, critiques et remarques sont, comme d&#8217;habitude, les bienvenues !