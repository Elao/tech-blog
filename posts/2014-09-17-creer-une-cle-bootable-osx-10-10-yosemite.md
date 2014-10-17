Suite à la sortie récente de <strong>OSX Yosemite</strong> vous trouverez ci-dessous la marche à suivre pour créer une clé "bootable" qui permettra entre autre:

* D'éviter que toute l'équipe télécharge 5Go de données pour l'installer
* De conserver une copie locale "au cas ou"
* Ou encore de faire une installation "from scratch" sur un poste, parce que des fois c'est quand même bien de faire du propre ;)

1.  En premier lieu procurez-vous Yosemite via le mac app store. (pour rappel il est installé dans /Applications)
Avant de lancer la mise à jour, copier le fichier <strong>InstallESD.dmg</strong> vers un emplacement de sauvegarde, en effet une fois la mise à jour faites le package sera automatiquement supprimé.

2.  Connectez une clé USB formatée à votre machine, si vous le faites avec l'utilitaire de disque la partition par défaut devrait s'appeler "Untitled" modifiez les instruction en conséquence si ce n'est pas le cas chez vous.

3.  Exécuter la liste d'instructions suivantes:

```
sudo hdiutil attach /Applications/Install\ OS\ X\ Yosemite.app/Contents/SharedSupport/InstallESD.dmg
```

```
sudo asr restore -source /Volumes/OS\ X\ Install\ ESD/BaseSystem.dmg -target /Volumes/Untitled -erase -format HFS+
```