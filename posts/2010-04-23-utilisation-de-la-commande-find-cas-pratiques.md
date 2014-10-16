
La commande find, outils ô combien indispensable des administrateurs systèmes, permet d'effectuer et d'automatiser tout un tas de tâches de maintenance courantes.

Ce petit memo se veut être un post de centralisation des lignes de commande pratiques, pouvant dépanner l'admin en détresse !

## Rechercher un fichier

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">find</span> . <span class="re5">-name</span> <span class="st0">"databases*"</span>
  </div>
</div>

Cette commande renverra tous les fichier du répertoire courant **ET** de ses sous répertoires dont le nom correspond au masque suivant l'option **-name**.

## Rechercher tous les fichiers de type répertoire

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">find</span> . <span class="re5">-name</span> <span class="st0">"config"</span> <span class="re5">-type</span> d
  </div>
</div>

## Lister le contenu de répertoires correspondant à un masque

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">find</span> . <span class="re5">-type</span> d <span class="re5">-name</span> <span class="st0">"config"</span> <span class="re5">-exec</span> <span class="kw2">ls</span> <span class="re5">-l</span> <span class="br0">&#123;</span><span class="br0">&#125;</span> \;
  </div>
</div>

## Modifier les droits des fichiers de type répertoire

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">find</span> . <span class="re5">-type</span> d <span class="re5">-exec</span> <span class="kw2">chmod</span> g+x <span class="br0">&#123;</span><span class="br0">&#125;</span> \;
  </div>
</div>

Commande très utile pour donner les droits d'exécution aux seuls répertoires par exemple.

## Recherche à l'aide d'opérateurs logiques

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">find</span> <span class="sy0">/</span>tmp \<span class="br0">&#40;</span> <span class="sy0">!</span> <span class="re5">-user</span> webmaster \<span class="br0">&#41;</span>
  </div>
</div>

Cette commande renverra tous les fichier de /tmp n'appartenant pas à l'utilisateur webmaster

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">find</span> <span class="sy0">/</span> \<span class="br0">&#40;</span> <span class="re5">-type</span> d <span class="re5">-a</span> <span class="re5">-user</span> webmaster \<span class="br0">&#41;</span>
  </div>
</div>

Cette commande renverra tous les fichiers de type répertoire **ET** (option -a) qui appartiennent à webmaster

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">find</span> <span class="sy0">/</span> \<span class="br0">&#40;</span> <span class="re5">-name</span> a.out <span class="re5">-o</span> <span class="re5">-name</span> ’<span class="sy0">*</span>.o’ \<span class="br0">&#41;</span> <span class="re5">-atime</span> +<span class="nu0">7</span> <span class="re5">-exec</span> <span class="kw2">rm</span> <span class="br0">&#123;</span><span class="br0">&#125;</span> \;
  </div>
</div>

Cette commande supprimera tous les fichiers dont le nom correspond est a.out **OU** se terminant par .o **ET** dont la date de dernier accès est antérieure à 7 jours.