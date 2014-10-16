
Sommaire:

1.  Les fichiers bash utilisateur
2.  [Personnaliser son terminal (Configuration simple)][1]
3.  [Personnaliser son terminal (Configuration avancée)][2]
4.  [Gérer ses terminaux avec Visor][3]

Petite parenthèse aujourd&#8217;hui sur l&#8217;utilité et le fonctionnement des fichiers bash utilisateurs.  
Ces fichiers sont couramment utilisés pour définir des &#8220;préférences&#8221;, des alias, des fonctions et autres, de façon globale ou propre à un utilisateur.

Ainsi lorsque l&#8217;on démarre un &#8220;shell&#8221; (en ouvrant un terminal par exemple), différents fichiers sont lus et utilisés pour définir l&#8217;environnement de l&#8217;utilisateur qui a &#8220;ouvert&#8221; ce terminal.

<span style="text-decoration: underline;">Dans l&#8217;ordre:</span>

*   /etc/profile
*   /etc/bashrc
*   ~/.profile
*   ~/.bashrc ( A condition que celui-ci soit appelé dans le .profile)

On peut même être plus précis, bash exécute en effet comme nous l&#8217;avons vu en premier lieu le fichier **/etc/profile **(s&#8217;il existe),** **avant de vérifier l&#8217;existence des fichiers ~/.bash\_profile, ~/.bash\_login et ~/.profile **dans cet ordre**, sachant que le fichier exécuté est le **premier fichier lisible**.

> <p style="text-align: justify;">
>   A noter que lorsqu&#8217;un shell autre qu&#8217;un shell de login est lancé seul les fichiers /etc/bashrc et ~/.bashrc sont lus (par exemple en tapant la commande <strong>bash</strong>)
> </p>

<p style="text-align: justify;">
  Pour résumer, les deux fichiers situés dans <strong>/etc</strong> sont les fichiers lus &#8220;par défaut&#8221; lorsqu&#8217;un utilisateur se connecte, ils permettent donc de définir des comportements globaux à tous les comptes utilisateurs d&#8217;une machine. Libre à eux ensuite de redéfinir leurs propres comportements à partir des fichiers de leur compte utilisateur.
</p>

<p style="text-align: justify;">
  On comprendra donc qu&#8217;il est possible d&#8217;aller assez loin dans la personnalisation d&#8217;un terminal <img src="http://old-blog.elao.dev/wp-includes/images/smilies/icon_smile.gif" alt="icon smile Les fichiers bash utilisateur" class="wp-smiley" title="Les fichiers bash utilisateur" /><br /> Attention toutefois lorsque vous décidez de modifier la variable <strong>PATH</strong> définie dans le <strong>.bashrc</strong>, celle-ci indique en gros, le ou les emplacements dans lesquels le système doit rechercher les commandes disponibles pour l&#8217;utilisateur.
</p>

 [1]: http://www.elao.com/blog/mac-os/personnaliser-son-terminal-sous-mac-osx.html "Personnaliser son terminal sous mac OSX"
 [2]: http://www.elao.com/blog/personnaliser-son-terminal-sous-mac-osx-configuration-avancee.html "Personnaliser son terminal"
 [3]: http://www.elao.com/blog/mac-os/terminal/gerer-ses-terminaux-avec-visor.html "Gérer ses terminaux avec Visor"