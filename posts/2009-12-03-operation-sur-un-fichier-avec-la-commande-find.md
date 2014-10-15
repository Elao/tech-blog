La commande &#8216;find&#8221; permet non seulement de faire des recherches sur les fichiers de l&#8217;arborescence mais également d&#8217;exécuter des commandes sur ces mêmes fichiers. Option au combien pratique pour toutes les tâches de maintenance / nettoyage des systèmes !  
```
find . -name ’*.yml’ -exec rm {} ;
```

**<span style="text-decoration: underline;"><strong>Explications:</strong></span>**

La commande ci-dessus va rechercher dans le répertoire courant tous les fichiers correspondant au masque suivant l&#8217;option *-name* et exécuter la commande *rm* sur le résultat. Cette simple commande permet donc de supprimer **tous** les fichiers de l&#8217;arborescence et de la sous-arborescence du répertoire courant suffixés par ***.yml***. 

**<span style="text-decoration: underline;">Syntaxe:</span>**

**.** représente le répertoire courant il aurait pu être par exemple */etc/mon_rep/ *

***-name*** est une option de find permettant de faire une recherche à partir d&#8217;un **masque** ou **pattern** en anglais.

<span class="userinput"><em><strong>’*.yml’</strong></em> est le masque représentant tous les fichiers suffixés par .yml</span>

***-exec*** est une option de find qui entraine l&#8217;exécution de la commande qui la suit

***rm*** est la commande UNIX permettant la suppression d&#8217;un fichier

{} représente touts les occurences de fichiers correspondantes au masque

***; ***termine la chaine de commande. <span style="font-size: small;"><cite dir="ltr">(Ne pas oublier de le protéger avec un backslah afin qu&#8217;il soit interprété correctement)</cite></span>