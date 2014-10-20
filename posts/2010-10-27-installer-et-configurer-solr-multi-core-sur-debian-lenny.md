Bonjour à tous !

Confrontés depuis quelques temps à la problématique d'avoir plusieurs moteurs de recherche de type solR sur un même serveur nous avons décidé de mettre en application la gestion multi-core de solR (à partir de la version 1.4) permettant de gérer plusieurs "core" et donc des indexes différents en fonction des applications via une seule et même instance solR.

Il existe des paquets Debian (apache-solr, tomcat 5.5) qui permettent d'avoir une instance fonctionnelle en peu de temps, mais j'ai pu lire chez nos amis anglophones que cela ne semble pas fonctionner du feu de dieu.

Nous allons donc partir sur une installation "hors gestionnaire de paquets" de Tomcat / apache-solR ...

### Installation de Tomcat:

La première étape pour cette installation est d'avoir un environnement Java fonctionnel, nous disposons donc de plusieurs solutions, à savoir, utiliser les paquets openJDK de Debian ou utiliser les paquets de Sun (disponibles en activant les dépôts non-free) sun-java6-jre.

```
apt-get install openjdk-6-jdk openjdk-6-jre
```

Nous pouvons à présent récupérer **Tomcat 6.0** (pour l'instant la version 7 est en bêta et comme le java c'est pas mon dada, je préfère partir sur des versions stables ...)

```
wget http://mirror.mkhelif.fr/apache/tomcat/tomcat-6/v6.0.29/bin/apache-tomcat-6.0.29.zip
```

J'ai choisi d'installer Tomcat dans **/usr/share/tomcat** correspondant à l'organisation Debian, pour ceux qui utilisent de moins bonnes distrib' (troll inside), libre à vous de le déployer ailleurs (**/opt/local** par exemple qui sera peut-être plus familier pour des utilisateurs de Red Hat, Fedora ... )

Une fois l'archive décompressée dans **/usr/share/tomcat** nous pouvons ajouter un script de démarrage dans **/etc/init.d**.
Script simpliste qui ne demande qu'à être amélioré (à vos commentaires):

```
#!/bin/sh
# Tomcat Init-Script

export JAVA_OPTS="$JAVA_OPTS -Dsolr.solr.home=/usr/share/tomcat/solr"

case $1 in
 start)
 sh /usr/share/tomcat/bin/startup.sh
 ;;

 stop)
 sh /usr/share/tomcat/bin/shutdown.sh
 ;;

 restart)
 sh /usr/share/tomcat/bin/shutdown.sh
 sh /usr/share/tomcat/bin/startup.sh
 ;;

esac
exit 0
```

Nous pouvons à présent démarrer notre serveur Tomcat via la commande

```
/etc/init.d/tomcat start
```

Si vous avez droit à ce message d'erreur ...

```
Cannot find /usr/share/tomcat/bin/catalina.sh
<This file is needed to run this program>
```

... pas de panique, il suffit de rajouter les droits d'exécution au(x) script(s) correspondant(s)


Nous allons à présent devoir accéder à l'administration du serveur Tomcat via l'URL : http://my_server:8080/manager/html

Cette page est normalement protégée, il nous faut donc configurer un accès administrateur pour pouvoir y accéder.
Nous allons pour se faire modifier le fichier **/usr/share/tomcat/conf/tomcat-users.xml**

```
<?xml version='1.0' encoding='utf-8'?>
<tomcat-users>
    <role rolename="admin"/>
    <role rolename="manager"/>
    <user username="tomcat-admin" password="tomcat" roles="admin,manager"/>
</tomcat-users>
```

### Installation de SolR

Premièrement commençons par télécharger solR ...

```
wget http://mir2.ovh.net/ftp.apache.org/dist//lucene/solr/1.4.1/apache-solr-1.4.1.zip
unzip apache-solr-1.4.1.zip
```

Une fois que nous avons récupéré solR, nous devons faire la liaison avec Tomcat

```
cp ~/apache-solr-1.4.1/dist/apache-solr-1.4.1.war /usr/share/tomcat/webapps/solr.war
```

Nous copions ensuite le schema de la config solR donnée en exemple:

```
cp -r ~/apache-solr-1.4.1/example/solr/ /usr/share/tomcat/
```

### Configuration du multi-core

Nous allons pour commencer copier le fichier **solr.xml** de l'exemple "multi-core" fournit avec le paquet solR

```
cp ~/apache-solr-1.4.1/example/multicore/solr.xml /usr/share/tomcat/solr/solr.xml
```

A présent, il nous faut créer un répertoire à l'intérieur de **/usr/share/tomcat/solr** pour chacune des applications (sites) pour lesquelles nous souhaitons utiliser solR.
Par exemple dans le cas ou nous avons 2 sites distincts, **"mon-super-site.com"** et **"best-site-online.com"** et que solR doit être utilisé par les deux nous allons avoir deux répertoires:

```
/usr/share/tomcat/solr/mon-super-site
/usr/share/tomcat/solr/best-site-online
```

Nous copions pour finir **/usr/share/tomcat/solr/conf** dans chacun des répertoires ci-dessus créés. Une fois terminé chaque répertoire doit avoir une copie de **/conf**

```
cp -r /usr/share/tomcat/solr/conf /usr/share/tomcat/solr/mon-super-site/conf
cp -r /usr/share/tomcat/solr/conf /usr/share/tomcat/solr/best-site-online/conf
```

Attention à bien conserver le répertoire conf initial car même si chacun de nos sites aura sa propre configuration le core de solR s'en sert toujours.
Nous pouvons maintenant modifier le fichier **solr.xml** (**/usr/share/tomcat/solr/solr.xml**):

```
<?xml version="1.0" encoding="UTF-8" ?>
<!--
All (relative) paths are relative to the installation path

 persistent: Save changes made via the API to this file
 sharedLib: path to a lib directory that will be shared across all cores
-->
<solr persistent="false">
  <!--
 adminPath: RequestHandler path to manage cores.
   If 'null' (or absent), cores will not be manageable via request handler
 -->
  <cores adminPath="/admin/cores">
    <core name="mon-super-site" instanceDir="monsupersite">
       <property name="dataDir" value="/usr/share/tomcat/solr/monsupersite/data" />
    </core>
    <core name="best-site-online" instanceDir="bestsiteonline">
       <property name="dataDir" value="/usr/share/tomcat/solr/bestsiteonline/data" />
     </core>
  </cores>
</solr>
```

Nous devons également spécifier l'emplacement de nos indexes (stockés par solR dans **solr/data** relativement au répertoire courant).
Nous avons pris la décisions de stocker les indexes de solR dans le répertoire correspondant à l'application concernée, par exemple **"monsupersite"**.

Pour ce cas nous modifierons donc la proprité dataDir du fichier **solrconfig.xml** situé dans **/usr/share/tomcat/solr/monsupersite/conf** de cette façon:

```
<dataDir>${solr.data.dir:/usr/share/tomcat/solr/monsupersite/data}</dataDir>
```

Nous pouvons redémarrer notre serveur tomcat !

Si tout s'est bien déroulé vous devriez pouvoir accéder à la page **http://my-server:8080/solr/** qui devrait vous répondre par un poli et gratifiant "Welcome to Solr!"

Commentaires, critiques et remarques sont, comme d'habitude, les bienvenues !