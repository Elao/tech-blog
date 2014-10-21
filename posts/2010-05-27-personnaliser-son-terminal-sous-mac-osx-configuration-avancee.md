
Sommaire :

1. [Les fichiers bash utilisateur](/blog/les-fichiers-bash-utilisateur.html)
1. [Personnaliser son terminal (Configuration simple)](/blog/personnaliser-son-terminal-sous-mac-osx.html)
2. Personnaliser son terminal (Configuration avancée)
3. [Gérer ses terminaux avec Visor](/blog/gerer-ses-terminaux-avec-visor.html)

Nous avons de la couleur dans notre terminal et quelques alias, c'est top, mais nous pouvons aller un peu plus loin en améliorant notre quotidien de développeur !
Voici les différentes améliorations glanées au fil du temps, notamment de ce <a title="Infinitered" href="http://blog.infinitered.com/" target="_blank">site</a>, que j'utilise (à placer dans le .bashrc)

**Garder un oeil sur les logs apache et php:**

```
if [ -f ${PHP_ERROR_FILE} ]; then
        alias phperror="tail -f ${PHP_ERROR_FILE}"
fi

if [ -f ${APACHE_LOG_FILE} ]; then
        alias apachelogs="tail -f ${APACHE_LOG_FILE}"
fi
```

**Les indispensables ...**

```
export HISTCONTROL=ignoredups # Ignores dupes in the history
shopt -s checkwinsize # After each command, checks the windows size and changes lines and columns

# bash completion settings (actually, these are readline settings)
bind "set completion-ignore-case on" # note: bind is used instead of setting these in .inputrc.  This ignores case in bash completion
bind "set bell-style none" # No bell, because it's damn annoying
bind "set show-all-if-ambiguous On" # this allows you to automatically show completion without double tab-ing
```

**Pour éviter la boulette et de passer une trèèèès longue nuit ...**

```
alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'
```

**Commandes utiles**

```
alias g='grep -i'  #Faire un grep non sensible à la casse
alias f='find . -iname' # Faire une recherche non sensible à la casse dans le répertoire courant
alias ducks='du -hcks * | sort -rn|head -11' # Renvoie la liste des répertoires avec leur taille
alias top='top -o cpu'
alias systail='tail -f /var/log/system.log'
```

**Et moins utiles ...**

```
#Renvoie la liste des commandes les plus utilisées
alias profileme="history | awk '{print \$2}' | awk 'BEGIN{FS=\"|\"}{print \$1}' | sort | uniq -c | sort -n | tail -n 20 | sort -nr"
```

**Réinitialiser les logs apache**

```
local APACHE_LOG_DIR="/var/log/apache2"
local PHP_ERROR_FILE="/var/log/apache2/php-error.log"
local APACHE_LOG_FILE="/var/log/apache2/apache-error.log"

for file in `ls ${APACHE_LOG_DIR}`
do
        if [ -f "${APACHE_LOG_DIR}/${file}" ]; then
                ` > "${APACHE_LOG_DIR}/${file}"`
        fi
done
```

Il suffit ensuite de taper la commande **clean\_apache\_logs **pour vider tous les fichiers de log contenus dans** /var/log/apache2 **

**Celui-ci est particulièrement sympa et permet de bookmarker ses répertoires favoris**

```
if [ ! -f ~/.dirs ]; then  # if doesn't exist, create it
        touch ~/.dirs
fi

alias show='cat ~/.dirs'
save (){
        command sed "/!$/d" ~/.dirs > ~/.dirs1; \mv ~/.dirs1 ~/.dirs; echo "$@"=\"`pwd`\" >> ~/.dirs; source ~/.dirs ;
}
source ~/.dirs  # Initialization for the above 'save' facility: source the .sdirs file
shopt -s cdable_vars # set the bash option so that no '$' is required when using the above facility
```

Le fonctionnement est très simple, si je suis dans le répertoire** /Volumes/Data/elao/projects** par exemple, la commande suivante créera un bookmark **"projects"**

```
save projects
```

Il me suffira ensuite, pour rejoindre ce répertoire de taper la commande suivante:

```
cd projects
```

La liste des bookmarks est consultable via la commande show

```
show
projects="/Volumes/Data/elao/projects"
```

Les bookmarks sont stockés dans le fichier ~/dirs.