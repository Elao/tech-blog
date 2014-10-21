Sommaire:

1.  [Les fichiers bash utilisateur][1]
2.  [Personnaliser son terminal (Configuration simple)][2]
3.  [Personnaliser son terminal (Configuration avancée)][3]
4.  Gérer ses terminaux avec Visor

Si vous êtes comme moi et qu'à la fin de la journée vous avez 50 terminaux d'ouverts sur plusieurs bureaux virtuels, et qu'il n'est plus possible de remettre la main sur LE terminal dont vous avez besoin ... ceci pourrait bien vous intéresser ;)

Il est en effet possible "d'attacher" les terminaux à la barre de menu mac OS X via un petit plugin SIMBL et de les afficher / cacher avec un simple raccourci clavier.

Ceux-ci peuvent être attachés à la menubar d'un space en particulier ou alors être liés à tous les spaces.

Ceci est possible grâce au plugin Visor, celui-ci nécessite l'installation de SIMBL (Un outils permettant de développer des plugins Cocoa)

**Dans l'ordre il faut donc :**

*   Télécharger et installer <a title="SIMBL" href="http://www.culater.net/software/SIMBL/SIMBL.php" target="_blank">SIMBL</a>
*   Télécharger le plugin <a title="Visor" href="http://visor.binaryage.com/" target="_blank">Visor</a>

Décompresser et copier le plugin Visor dans

```
~/Library/Application Support/SIMBL/Plugins
```

*   Fermer tous vos terminaux ouverts

Lorsque vous relancez votre application terminal celle-ci ne s'ouvre pas mais vous devriez voir apparaitre dans le menubar en haut à droite une petite icone la représentant.

Nous pouvons à présent accéder à notre terminal par un raccourci de notre choix (par défaut ^<), configurable en allant dans les préférences de Visor (clic droit sur l'icône -> Préférences)

 [1]: /blog/mac-os/terminal/les-fichiers-bash-utilisateur.html "Les fichiers bash utilisateur"
 [2]: /blog/mac-os/personnaliser-son-terminal-sous-mac-osx.html "Personnaliser son terminal sous mac OSX"
 [3]: /blog/mac-os/terminal/personnaliser-son-terminal-sous-mac-osx-configuration-avancee.html "Personnaliser son terminal mac OSX"