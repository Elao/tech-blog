Sommaire:

1.  Les fichiers bash utilisateur
2.  [Personnaliser son terminal (Configuration simple)](http://www.elao.com/blog/mac-os/personnaliser-son-terminal-sous-mac-osx.html)
3.  [Personnaliser son terminal (Configuration avancée)](http://www.elao.com/blog/personnaliser-son-terminal-sous-mac-osx-configuration-avancee.html)
4.  [Gérer ses terminaux avec Visor](http://www.elao.com/blog/mac-os/terminal/gerer-ses-terminaux-avec-visor.html)

Petite parenthèse aujourd'hui sur l'utilité et le fonctionnement des fichiers bash utilisateurs.
Ces fichiers sont couramment utilisés pour définir des "préférences", des alias, des fonctions et autres, de façon globale ou propre à un utilisateur.

Ainsi lorsque l'on démarre un "shell" (en ouvrant un terminal par exemple), différents fichiers sont lus et utilisés pour définir l'environnement de l'utilisateur qui a "ouvert" ce terminal.

**Dans l'ordre :**

*   /etc/profile
*   /etc/bashrc
*   ~/.profile
*   ~/.bashrc ( A condition que celui-ci soit appelé dans le .profile)

On peut même être plus précis, bash exécute en effet comme nous l'avons vu en premier lieu le fichier **/etc/profile **(s'il existe),** **avant de vérifier l'existence des fichiers ~/.bash\_profile, ~/.bash\_login et ~/.profile **dans cet ordre**, sachant que le fichier exécuté est le **premier fichier lisible**.

>  A noter que lorsqu'un shell autre qu'un shell de login est lancé seul les fichiers /etc/bashrc et ~/.bashrc sont lus (par exemple en tapant la commande **bash**)

Pour résumer, les deux fichiers situés dans **/etc** sont les fichiers lus "par défaut" lorsqu'un utilisateur se connecte, ils permettent donc de définir des comportements globaux à tous les comptes utilisateurs d'une machine. Libre à eux ensuite de redéfinir leurs propres comportements à partir des fichiers de leur compte utilisateur.

On comprendra donc qu'il est possible d'aller assez loin dans la personnalisation d'un terminal.

Attention toutefois lorsque vous décidez de modifier la variable **PATH** définie dans le **.bashrc**, celle-ci indique en gros, le ou les emplacements dans lesquels le système doit rechercher les commandes disponibles pour l'utilisateur.