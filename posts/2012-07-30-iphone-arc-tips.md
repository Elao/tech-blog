
Loin de moi l'idée de vous expliquer en détail comment marche l'Automatic Reference Counting (ARC) apparut avec iOS 5, mais si vous ne connaissez pas voici un petit descriptif, suivi d'un petit tip fort pratique.

**Quand on apprend l'objective C, il y a plusieurs choses qui peuvent perturber un développeur venant du web, du monde java, de la bulle Flash etc. Pour ma part ça a été principalement ces 2 points :**

*   La syntaxe à base de crochet `[[MaClasse maMethode] jerebondisurleReturn];` (>_<)
*   La gestion mémoire (o_O)

Sur ce deuxième point, quand on n'a pas l'habitude de penser à déallouer la mémoire sur un objet dont on ne sert plus, ou à dire "ne te détruit pas, je me servirais de toi après", on se prend rapidement les pieds avec les `release`, `autorelease`, `dealloc` de l'objective C.

Grâce à ARC, tout ça est derrière nous. En démarrant un nouveau projet, xCode va nous coché par défaut l'utilisation de ARC. Ce système permet, avant de compiler, de faire les `dealloc/release/autorelease` pour nous de façon complètement transparente.

<img title="ARC" src="/blog/medias/2012-07-30-iphone-arc-tips/preferences.png" alt="iPhone   ARC tips" width="300" height="201" />

Du coup, avec ce système activé, xCode nous dit "je m'occupe de tout".
Et si on commence à poser du code, des classes qui contiennent des releases, autorelease etc, là xCode ne sera pas notre pote et dira "Je m'occupe de tout, enlève-moi ça de là, tu me perturbes".

Dans ce cas-là, comment récupérer du code d'anciens projets, ou des librairies qui ne sont pas passées sous ARC et qui ne passeront jamais dessus ?

En fait c'est très simple, il suffit de dire à notre compilateur "sur cette classe, y a pas ARC, ne fais rien". Une capture d'écran vaut mieux que de longues phrases, c'est ici que ça se passe:

<img src="/blog/medias/2012-07-30-iphone-arc-tips/xcode.png" alt="iPhone   ARC tips" width="584" height="215" />

Le flag de compilation à rajouter est `-fno-objc-arc`

Et voilà le tour est joué.

Vous pouvez maintenant avoir la souplesse d'ARC et des librairies non-ARC dans le même  projet !