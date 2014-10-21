Sommaire:

1.  [Les fichiers bash utilisateur](/blog/les-fichiers-bash-utilisateur.html)
2.  Personnaliser son terminal (Configuration simple)
3.  [Personnaliser son terminal (Configuration avancée)](/blog/personnaliser-son-terminal-sous-mac-osx-configuration-avancee.html)
4.  [Gérer ses terminaux avec Visor](/blog/gerer-ses-terminaux-avec-visor.html)

Ceux qui ont déjà jeté un petit coup d'oeil au terminal mac OS X ont pu se rendre compte qu'il était loin d'être transcendant ... nous allons donc voir aujourd'hui comment le rendre un peu moins austère.
Pour commencer nous allons créer dans notre répertoire utilisateur deux fichiers (s'ils n'existent pas déjà):

- Un fichier appelé **.profile**
- Un fichier appelé **.bashrc**

```
touch .bashrc .profile
```

Copiez / collez ensuite dans le fichier **.profile** les lignes suivantes:

```
if [ -n "$BASH_VERSION" ]; then
  # include .bashrc if it exists
  if [ -f ~/.bashrc ]; then
    . ~/.bashrc
  fi
fi
# set PATH so it includes user's private bin if it exists</div>
if [ -d ~/bin ] ; then
  PATH=~/bin:"${PATH}"
fi
```

Et dans le **.bashrc**:

```
export PS1='\h:\w\$ '
umask 022

export CLICOLOR=1

alias ll='ls -l'
alias la='ls -A'
alias l='ls -CF'
alias vi='vim'

function cyan_red_prompt
{
  local CYAN="\[&#92;&#48;33[0;36m\]"
  local GRAY="\[&#92;&#48;33[0;37m\]"
  local RED="\[&#92;&#48;33[0;31m\]"

  PS1="${CYAN}[\u@\h ${RED}\w${CYAN}]${GRAY} "
}

cyan_red_prompt
```