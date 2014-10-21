Bonjour à toutes et à tous,

Aujourd'hui au programme, un petit memo sur l'installation de [GITORIOUS][2]. Pour ceux qui l'ignorent, Gitorious est un projet web destiné à la gestion des projets Open Source utilisant GIT.
C'est le deuxième outil utilisé derrière GitHub bien que plus ancien que celui-ci et étant <a class="fancybox" title="... même si on sait tous que Ruby est plus lent qu'une grand-mère asthmatique ;)" href="http://www.elao.org/wp-content/uploads/2011/05/troll.gif">codé en Ruby <img src="http://old-blog.elao.dev/wp-includes/images/smilies/icon_wink.gif" alt="icon wink GITorious   Gérer et administrer ses dépôts GIT" class="wp-smiley" title="GITorious   Gérer et administrer ses dépôts GIT" /> </a>

### Introduction:

La suite de cet article s'adresse à ceux qui préfèrent héberger et gérer eux-mêmes leurs dépôts plutôt que de passer par gitHub. En plus de fournir une interface d'administration de ces dépôts GIT, GITORIOUS fournit en vrac:

- Une gestion par projet des dépôts
- Une notion de wiki (rattaché à un projet particulier)
- Une interface web permettant de gérer les "merge request" et les revues de code
- Une visualisation de l'activité du projet et des développeurs (commit, diff de fichiers, possibilité d'ajouter un commentaire sur un commit ... )

### Screenshots:

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

### Prérequis

Dans un premier temps installons les paquets nécessaire à GITORIOUS ...

```
apt-get install build-essential zlib1g-dev libcurl4-openssl-dev apache2 mysql-server mysql-client apg geoip-bin libgeoip1
libgeoip-dev libpcre3 libpcre3-dev zlib1g zlib1g-dev libyaml-dev libmysqlclient-dev apache2-dev libonig-dev
zip unzip memcached git-core git-svn git-doc git-cvs libreadline-dev openjdk-6-jre sqlite3 libsqlite3-dev
libmagick++3 libmagick++-dev libapache2-mod-xsendfile
```

<img class="alignleft size-full wp-image-1553" title="phusion" src="http://www.elao.org/wp-content/uploads/2011/05/phusion.png" alt="phusion GITorious   Gérer et administrer ses dépôts GIT" width="246" height="108" />

### Installation de Ruby Enterprise:

J'ai récemment découvert [Ruby Enterprise][3] ; si comme moi vous n'êtes pas un expert Ruby mais vous avez plusieurs soft RoR à faire tourner (je pense notamment à Redmine et Gitorious) vous allez plus que l'aimer et ce, pour plusieurs raisons:

- Un garbage collector maison, utilisé notamment par Passenger, qui utilise le preforking pour réduire la consommation mémoire de Ruby (33% de gains annoncés)
- Des applications plus "scalables"
- Une meilleure gestion de l'allocation dynamique de la mémoire
- Et pour nos amis développeurs RoR, le dump des stacks mémoire pour le debug.
- Enfin, et c'est selon moi son principal avantage, Ruby Enterprise gère lui-même l'installation des paquetages Ruby (sans recourir à gem) ; c'est notamment très pratique pour les néophytes et cela résout le casse-tête que représente la gestion des gems en fonction des différentes versions de Ruby ... .

On récupère donc Ruby Enterprise ...

```
wget http://rubyenterpriseedition.googlecode.com/files/ruby-enterprise-1.8.7-2011.03.tar.gz
tar zxf ruby-enterprise-1.8.7-2011.03.tar.gz
```

Sans oublier la création d'un lien symbolique vers la version courante de ruby-enterprise qui nous permettra de gérer nos versions proprement.

```
cd /opt && ln -s ruby-enterprise-1.8.7-2011.03/ ruby-enterprise
```

Nous allons à présent créer les liens vers les différents binaires Ruby, afin d'éviter de surcharger le PATH. Libre à vous de le faire, je n'ai pas vraiment d'opinion quant à la façon la plus "propre" de procéder.

```
ln -s /opt/ruby-enterprise/bin/ruby /opt/ruby-enterprise/bin/rake /opt/ruby-enterprise/bin/gem /usr/local/bin
```

<img class="alignleft size-full wp-image-1556" title="sphinx" src="http://www.elao.org/wp-content/uploads/2011/05/sphinx.jpg" alt="sphinx GITorious   Gérer et administrer ses dépôts GIT" width="200" height="51" />

#### Installation de Sphinx

Sphinx est un moteur de recherche relativement performant qui supporte nativement de nombreuses base de données dont MySQL et PostGreSQL. Il est extrêmement répandu sur des sites à fort traffic, notamment Craiglist.org.

**On récupère les sources ...**

```
wget http://sphinxsearch.com/files/sphinx-0.9.9.tar.gz
```

**... que l'on décompresse.**

```
tar zxf sphinx-0.9.9.tar.gz && cd sphinx-0.9.9
```

**Il s'agit ensuite d'une compilation / installation classique:**

```
./configure --prefix=/usr/local/sphinx-0.9.9
make && sudo make install
```

**On gère notre versioning de la même façon que pour Ruby Enterprise:**

```
cd /usr/local && ln -s sphinx-0.9.9 sphinx
```

**Et pour finir on rend disponible les binaires:**

```
ln -s /usr/local/sphinx/bin/* /usr/local/bin
```

<img class="alignleft size-full wp-image-1559" title="apache" src="http://www.elao.org/wp-content/uploads/2011/05/apache.gif" alt="apache GITorious   Gérer et administrer ses dépôts GIT" width="203" height="61" />

#### Installation de Apache activeMQ

Je n'ai guère eu le temps de me documenter sur ce composant, pour ceux que cela intéresse, vous trouverez votre bonheur [ici][4]

**On récupère les sources:**

```
wget http://mir2.ovh.net/ftp.apache.org/dist/activemq/apache-activemq/5.4.2/apache-activemq-5.4.2-bin.tar.gz
```

**Même démarche que plus haut:**

```
tar zxf apache-activemq-5.4.2-bin.tar.gz
sudo mv apache-activemq-5.4.2 /usr/local
cd /usr/local && sudo ln -s apache-activemq-5.4.2 apache-activemq && cd apache-activemq
sudo bin/activemq setup /etc/default/activemq && sudo chmod 600 /etc/default/activemq
sudo adduser --system --no-create-home --home /usr/local/apache-activemq --shell /bin/bash activemq
sudo chown -R activemq:nogroup /usr/local/apache-activemq-5.4.2
```

Il y a quelques adaptations à faire à ce niveau, commençons par le fichier

```
/etc/default/activemq
```

Ajoutez ce qui suit au début du fichier:

```
ACTIVEMQ_HOME=/usr/local/apache-activemq
```

On modifie ensuite la ligne "Active User" comme suit:

```
ACTIVEMQ_USER="activemq"
```

Pour finir éditez le fichier

```
/usr/local/apache-activemq/conf/activemq.xml
```

. Commentez ou remplacez le "**transportConnector**" existant et ajoutez le protocole stomp à la place:

```
<transportConnector name="stomp" uri="stomp://0.0.0.0:61613">;
```

### Récupération de GITorious

Passons enfin aux choses sérieuses ...

```
sudo git clone git://gitorious.org/gitorious/mainline.git /var/www
```

Libre à vous de l'installer ailleurs <img src="http://old-blog.elao.dev/wp-includes/images/smilies/icon_smile.gif" alt="icon smile GITorious   Gérer et administrer ses dépôts GIT" class="wp-smiley" title="GITorious   Gérer et administrer ses dépôts GIT" />

### Installation des Gems:

```
cd /var/www/gitorious &&  /opt/ruby-enterprise/bin/bundle install
```

### Configuration des services:

Nous devons à présent faire le nécessaire pour faire "tourner" tout ce joli monde sous forme de services.
Pour ce faire, nous allons copier les scripts Sphinx et git-daemon du répertoire

```
gitorious/doc/templates/ubuntu
```

vers le répertoire

```
/etc/init.d
```

. Attention pensez à changer le PATH vers votre interpréteur Ruby dans le script du git-daemon pour

```
/usr/bin/ruby
```

Script pour stomp

```
/etc/init.d/stomp
```

```
  #!/bin/sh
  # Start/stop the stompserver
  #
  ### BEGIN INIT INFO
  # Provides:          stomp
  # Required-Start:    $local_fs $remote_fs $network $syslog
  # Required-Stop:
  # Default-Start:     2 3 4 5
  # Default-Stop:      1
  # Short-Description: Stomp
  # Description:       Stomp
  ### END INIT INFO

  test -f /usr/bin/stompserver || exit 0

  . /lib/lsb/init-functions

  case "$1" in
  start)  log_daemon_msg "Starting stompserver" "stompserver"
      start-stop-daemon --start --name stompserver --startas /usr/bin/stompserver --background --user git
      log_end_msg $?
      ;;
  stop)  log_daemon_msg "Stopping stompserver" "stompserver"
      start-stop-daemon --stop --name stompserver
      log_end_msg $?
      ;;
  restart) log_daemon_msg "Restarting stompserver" "stompserver"
      start-stop-daemon --stop --retry 5 --name stompserver
      start-stop-daemon --start --name stompserver --startas /usr/bin/stompserver --background --user git
      log_end_msg $?
      ;;
  status)
      status_of_proc /usr/bin/stompserver stompserver &amp;&amp; exit 0 || exit $?
      ;;
  *)      log_action_msg "Usage: /etc/init.d/stomp {start|stop|restart|status}"
      exit 2
      ;;
  esac
  exit 0
```

Script pour poller

```
/etc/init.d/git-poller
```

```
#!/bin/sh
# Start/stop the git poller
#
### BEGIN INIT INFO
# Provides:          git-poller
# Required-Start:    stomp
# Required-Stop:
# Default-Start:     2 3 4 5
# Default-Stop:      1
# Short-Description: Gitorious poller
# Description:       Gitorious poller
### END INIT INFO

/bin/su - git -c "cd /var/www/gitorious;RAILS_ENV=production script/poller $@"
```

On copie pour finir

```
gitorious-logrotate
```

de

```
gitorious/doc/templates/ubuntu
```

vers

```
/etc/logrotate.d/gitorious
```

Il ne nous reste plus qu'à rendre tous ces scripts exécutables au démarrage

```
chmod 755 /etc/init.d/git-ultrasphinx /etc/init.d/git-daemon /etc/init.d/stomp /etc/init.d/git-poller

update-rc.d stomp defaults
update-rc.d git-daemon defaults
update-rc.d git-ultrasphinx defaults
update-rc.d git-poller defaults
```

### Configuration d'Apache

**Installer passenger:**

```
/var/lib/gems/1.8/bin/passenger-install-apache2-module
```

Faire charger le module par Apache en créant le fichier

```
/etc/apache2/mods-available/passenger.load
```

, ajoutez-lui:

```
LoadModule passenger_module /var/lib/gems/1.8/gems/passenger-2.2.10/ext/apache2/mod_passenger.so
PassengerRoot /var/lib/gems/1.8/gems/passenger-2.2.10
PassengerRuby /usr/bin/ruby1.8
```

On s'assure que tous les modules nécessaires sont chargés par Apache:

```
a2enmod passenger
a2enmod rewrite
a2enmod ssl
```

Et, pour finir, on redémarre Apache:

```
/etc/init.d/apache2 restart
```

Nous devrions à présent avoir un environnement apache correctement configuré. Ci-dessous deux exemples de vhost Apache ; grâce à Passenger nous n'avons plus à nous préoccuper d'assurer un système de proxy entre Apache et Mongrel par exemple:

```
ServerName gitorious.avalon

ServerAdmin webmaster@localhost
DocumentRoot /var/www/gitorious/public

ErrorLog /var/log/apache2/gitorious-error.log
# Possible values include: debug, info, notice, warn, error, crit,
# alert, emerg.
LogLevel warn

CustomLog /var/log/apache2/gitorious-access.log combined

XSendFile on
XSendFileAllowAbove on
```

```
ServerAdmin webmaster@localhost
DocumentRoot /var/www/gitorious/public

ErrorLog /var/log/apache2/gitorious-error.log

# Possible values include: debug, info, notice, warn, error, crit,
# alert, emerg.
LogLevel warn

CustomLog /var/log/apache2/gitorious-ssl_access.log combined

SSLEngine on
SSLCertificateFile    /etc/ssl/certs/ssl-cert-snakeoil.pem
SSLCertificateKeyFile /etc/ssl/private/ssl-cert-snakeoil.key

XSendFile on
XSendFileAllowAbove on
```

### Configuration de Gitorious

Avant de commencer la configuration de Gitorious il nous faut:

- Créer un utilisateur MySQL git avec des privilèges globaux sur la table

```
gitorious_production
```

- Créer un utilisateur système git et préparer le terrain

```
adduser --system --home /var/www/gitorious/ --no-create-home --group --shell /bin/bash git
chown -R git:git /var/www/gitorious

su - git
mkdir .ssh
touch .ssh/authorized_keys
chmod 700 .ssh
chmod 600 .ssh/authorized_keys
mkdir tmp/pids
mkdir repositories
mkdir tarballs

cp config/database.sample.yml config/database.yml
cp config/gitorious.sample.yml config/gitorious.yml
cp config/broker.yml.example config/broker.yml
```

**databases.yml**

Supprimer toutes les sections sauf la partie

```
production
```

, vous devriez avoir quelque chose dans ce style:

```
production:
  adapter: mysql
  database: gitorious_production
  username: git
  password: password
  host: localhost
  encoding: utf8
```

Il va de soi que les accès sont à personnaliser selon votre configuration.

**gitorious.yml**

Supprimez toutes les sections sauf la partie

```
production
```

, selon ce que vous voulez faire votre configuration ressemblera à celle ci-dessous:

```
development:

production:

  cookie_secret: 57d23d5a0b754ffcb8426e588b48fe26rffdq78

  # The path where git repositories are stored. The actual (bare) repositories resides
  # in repository_base_path/#{project.slug}/#{repository.name}.git/:
  repository_base_path: &quot;/var/www/gitorious/repositories&quot;

  gitorious_client_host: localhost

  # Port the ./script/gitorious script should use:
  gitorious_client_port: 80

  # Host which is serving the gitorious app, eg &quot;gitorious.org&quot;
  gitorious_host: gitorious.avalon

  # User which is running git daemon
  gitorious_user: git

  # Email address to the support for the Gitorious server
  gitorious_support_email: hosting@my-domain.com

  # Enable or Disable Public Mode (true) or Private Mode (false)
  public_mode: true

  # Where should we store generated tarballs?
  # (should be readable by webserver, since we tell it to send the file using X-Sendfile)
  archive_cache_dir: &quot;/var/www/gitorious/tarballs-cache&quot;
  # Which directory should we work in when we generate tarballs, before moving
  # them to the above dir?
  archive_work_dir: "/var/www/gitorious/tarballs-work"

  # is it only site admins who can create new projects?
  only_site_admins_can_create_projects: false

  # Should we hide HTTP clone urls?
  hide_http_clone_urls: true

  # Is this gitorious.org? Read: should we have a very flashy homepage?
  is_gitorious_dot_org: false

  # Pick a default license
  #default_license: GNU Affero General Public License (AGPLv3)
```

Attention

```
gitorious_host
```

doit exactement correspondre au nom du host utilisé pour accéder à l'application sous peine de rencontrer quelques problèmes avec les cookies de session.

Gitorious est installé nous n'avons plus maintenant qu'à lancer le script d'initialisation:

```
export RAILS_ENV=production
rake db:create
rake db:migrate
rake ultrasphinx:bootstrap
```

Ajouter ceci dans votre crontab pour l'indexation de Sphinx

```
crontab -e    * * * * * cd /var/www/gitorious && /usr/bin/rake ultrasphinx:index RAILS_ENV=production
```

### Finalisation

Redémarrez apache:

```
/etc/init.d/apache2 restart
```

Créez un utilisateur admin pour votre application (en étant auth en tant que git)

```
env RAILS_ENV=production ruby1.8 script/create_admin
```

Ci-dessous les commandes pour gérer vos utilisateurs à partir de la ligne de commande:

Supprimer un utilisateur :
```
env RAILS_ENV=production ruby1.8 script/console
> user = User.find_by_login "Username"
> user.destroy
```

Créer un nouveau rôle :

```
env RAILS_ENV=production ruby1.8 script/console
> Role.create!(:name => "Member", :kind => Role::KIND_MEMBER)
> Role.create!(:name => "Administrator", :kind => Role::KIND_ADMIN)
```

### Conclusion

Voilà, nous disposons à présent d'un outil permettant d'héberger nos propres dépôts GIT et de bénéficier d'une interface "user friendly" pour les administrer. Ne connaissant pas bien les possibilités de GitHub, je me garderai bien de faire une comparaison entre les deux outils ; d'ailleurs, je ne suis même pas certain qu'une réelle concurrence les oppose. Cependant GITORIOUS me semble être un très bon outil pour ceux qui souhaitent garder "la main" sur leurs sources, et je l'apprécie d'autant plus qu'il se révèle assez intuitif à l'usage.

### Sources

Cet article est largement inspiré de [Setting up Gitorious on your own server][5] et de [Gitorious Ubuntu Installation][6]

Comme d'habitude corrections, remarques et critiques sont les bienvenues

 [1]: http://www.elao.org/wp-content/uploads/2011/05/gitorious.png
 [2]: http://www.gitorious.org/
 [3]: http://www.rubyenterpriseedition.com/
 [4]: http://activemq.apache.org/
 [5]: http://cjohansen.no/en/ruby/setting_up_gitorious_on_your_own_server
 [6]: https://gitorious.org/gitorious/pages/UbuntuInstallation