
Bonjour à tous !

Je vais vous parler aujourd'hui d'une utilisation de **Dropbox** un peu différente de l'utilisation desktop **"standard"**. En effet, en travaillant sur un projet nécessitant l'archivage de certains documents, nous avons été confrontés à la problématique du "tampon" entre l'upload du document et son indexation dans une GED dédiée. De fait nous avons décidé d'utiliser Dropbox comme tampon, l'idée étant d'avoir une machine cliente déposant des fichiers dans un partage Dropbox, lui-même synchronisé sur un serveur de production, lequel, de son côté, exploitera les-dits fichiers avant de les supprimer.

Pour ceux qui ne connaîtraient pas ce très bon outil qu'est Dropbox, je vous invite à aller jeter un petit coup d'oeil à <a href="http://fr.wikipedia.org/wiki/Dropbox" target="_blank">Wikipedia</a> et à leur <a href="http://www.dropbox.com" target="_blank">site officiel</a>.

**En tout premier lieu et avant de commencer voici les pré-requis à l'installation du démon :**
- GCC 2.4 mini
- wget
- Python 2.5 ou plus
- Un navigateur internet (On notera que je ne cite volontairement pas de nom;))

### Installation

Nous allons commencer par créer un utilisateur dédié au service ayant pour groupe dropbox :

```
groupadd dropbox
useradd -r -d /etc/dropbox -g dropbox -s /bin/false dropbox
```

Nous pouvons récupérer la dernière version de Dropbox.
Il existe deux versions disponibles : 32bits / 64 bits ; à vous de choisir la bonne !

**32 bits:**

```
wget -O /tmp/dropbox.tar.gz 'http://www.dropbox.com/download/?plat=lnx.x86'
```

**64 bits:**

```
wget -O /tmp/dropbox.tar.gz 'http://www.dropbox.com/download/?plat=lnx.x86_64'
```

**Nous créons les deux répertoires que nous allons utiliser, à savoir `/etc/dropbox` pour la config et `/usr/local/dropbox` pour l'applicatif.**

```
mkdir -p /usr/local/dropbox /etc/dropbox
chown dropbox.dropbox /etc/dropbox
chmod 700 /etc/dropbox
```

**Nous décompressons ensuite l'archive :**

```
tar xvzf /tmp/dropbox.tar.gz -C /usr/local/dropbox --strip 1
rm /tmp/dropbox.tar.gz
```

**Nous allons à présent mettre en place un script init, permettant d'administrer le service :**

```
### BEGIN INIT INFO
# Provides:          dropbox
# Required-Start:
# Required-Stop:
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: starts the dropbox service
# Description:       starts dropbox using start-stop-daemon
### END INIT INFO

DROPBOX_USERS="dropbox"
DAEMON=/usr/local/dropbox/dropbox
unset DISPLAY

start() {
    echo "Starting dropbox..."
    for dbuser in $DROPBOX_USERS; do
        HOMEDIR=$(getent passwd $dbuser | cut -d: -f6)
        if [ -x $DAEMON ]; then
            HOME="$HOMEDIR" start-stop-daemon -b -o -c $dbuser -S -u $dbuser -x $DAEMON
        fi
    done
}

stop() {
    echo "Stopping dropbox..."
    for dbuser in $DROPBOX_USERS; do
        HOMEDIR=$(getent passwd $dbuser | cut -d: -f6)
        if [ -x $DAEMON ]; then
            start-stop-daemon -o -c $dbuser -K -u $dbuser -x $DAEMON
        fi
    done
}

status() {
    for dbuser in $DROPBOX_USERS; do
        dbpid=$(pgrep -u $dbuser dropbox)
        if [ -z $dbpid ] ; then
            echo "dropboxd for USER $dbuser: not running."
        else
            echo "dropboxd for USER $dbuser: running (pid $dbpid)"
        fi
    done
}

case "$1" in
  start)
    start
    sleep 1
    status
    ;;

  stop)
    stop
    sleep 1
    status
    ;;

  restart|reload|force-reload)
    stop
    start
    sleep 1
    status
    ;;

  status)
    status
    ;;

  *)
    echo "Usage: /etc/init.d/dropbox {start|stop|reload|force-reload|restart|status}"
    exit 1

esac

exit 0
```

**Ajoutons ce script au démarrage de la machine :**

```
chmod a+x /etc/init.d/dropbox
update-rc.d dropbox defaults
```

### Changer le répertoire par défaut de Dropbox

Par défaut le répertoire utilisé pour les fichiers est créé dans le compte de l'utilisateur exécutant le démon, nous allons voir ci-dessous comment modifier ce répertoire. Certains me feront remarquer qu'un bon vieux symlink ferait parfaitement l'affaire, mais j'aurais tendance à privilégier une méthode un peu plus "académique".

Dropbox utilise une base sqlite pour stocker ses données et notamment sa configuration.

Pour ceux qui maîtrisent sqlite et qui souhaitent aller modifier cette configuration en base, il faut éditer le champ `dropbox_path` de la table config, mais attention, la valeur du `path` doit être encodé en base 64 ! Pour les autres, vous pouvez utiliser le script Python fourni ici, pour appliquer cette modification en exécutant la commande suivante :

```
$ cp /etc/dropbox/.dropbox/dropbox.db dropbox.db.backup
$ wget http://dl.dropbox.com/u/119154/permalink/dropboxdir.py
$ chmod +x dropboxdir.py
$ mv ~/Dropbox /path/to/my/new/dir
$ ./dropboxdir --setfolder=/path/to/my/new/dir
```

**Attention !** Si le démon Dropbox a été lié à un compte **AVANT** la relocalisation du répertoire, et si le nouveau répertoire est vide, Dropbox va considérer que les fichiers ont été supprimés localement et effectuer la modification sur le répertoire distant, ce qui entrainera la suppression de **TOUS** vos fichiers distants (et de facto la suppression des fichiers sur tous les postes clients associés au compte Dropbox).

Nous pouvons à présent démarrer le service Dropbox et "attacher" notre machine à notre compte.

```
$ /usr/local/dropbox/dropbox start

This client is not linked to any account...
Please visit https://www.dropbox.com/cli_link?host_id=7d44a557aa58f285f2da0x67334d02c1 to link this machine.
```

Il ne reste à présent qu'à copier / coller cette url dans notre navigateur, ce qui devrait ajouter notre machine aux machines autorisées à synchroniser le contenu de notre Dropbox.

**Vous pouvez à présent arrêter Dropbox coté serveur et le relancer "proprement" en tant que service avec un :**

```
/etc/init.d/dropbox start
```
