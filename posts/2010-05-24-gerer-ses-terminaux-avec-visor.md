
Sommaire:

1.  [Les fichiers bash utilisateur][1]
2.  [Personnaliser son terminal (Configuration simple)][2]
3.  [Personnaliser son terminal (Configuration avancée)][3]
4.  Gérer ses terminaux avec Visor

<div class="ngg-gallery-singlepic-image ngg-left" style="max-width: 320px">
  <a href="http://old-blog.elao.dev/wp-content/gallery/macosx/visor-terminal.png"
		     title=""
             data-src="http://old-blog.elao.dev/wp-content/gallery/macosx/visor-terminal.png"
             data-thumbnail="http://old-blog.elao.dev/wp-content/gallery/macosx/thumbs/thumbs_visor-terminal.png"
             data-image-id="2"
             data-title="visor-terminal"
             data-description=""
             target='_self'
             class="shutterset_c070d9f2fc8dd2c90c7700c86a29c890"> <img class="ngg-singlepic"
             src="http://www.elao.com/blog/nextgen-image/2/320x240x100/6c0c78b5d8c4f8c0bceab3083080c863"
             alt="visor-terminal"
             title="visor-terminal"
              width="320" /> </a>
</div>

<span></span>

<p style="text-align: justify;">
  Si vous êtes comme moi et qu'à la fin de la journée vous avez 50 terminaux d'ouverts sur plusieurs bureaux virtuels, et qu'il n'est plus possible de remettre la main sur LE terminal dont vous avez besoin ... ceci pourrait bien vous intéresser ;)
</p>

<p style="text-align: justify;">
  Il est en effet possible "d'attacher" les terminaux à la barre de menu mac OS X via un petit plugin SIMBL et de les afficher / cacher avec un simple raccourci clavier.
</p>

<p style="text-align: justify;">
  Ceux-ci peuvent être attachés à la menubar d'un space en particulier ou alors être liés à tous les spaces.
</p>

<p style="text-align: justify;">
  Ceci est possible grâce au plugin Visor, celui-ci nécessite l'installation de SIMBL (Un outils permettant de développer des plugins Cocoa)
</p>

<span style="text-decoration: underline;">Dans l'ordre il faut donc:</span>

*   Télécharger et installer <a title="SIMBL" href="http://www.culater.net/software/SIMBL/SIMBL.php" target="_blank">SIMBL</a>
*   Télécharger le plugin <a title="Visor" href="http://visor.binaryage.com/" target="_blank">Visor</a>

Décompresser et copier le plugin Visor dans

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    ~<span class="sy0">/</span>Library<span class="sy0">/</span>Application Support<span class="sy0">/</span>SIMBL<span class="sy0">/</span>Plugins
  </div>
</div>

*   Fermer tous vos terminaux ouverts

Lorsque vous relancez votre application terminal celle-ci ne s'ouvre pas mais vous devriez voir apparaitre dans le menubar en haut à droite une petite icone la représentant.

<div class="ngg-gallery-singlepic-image ngg-center" style="max-width: 600px">
  <a href="http://old-blog.elao.dev/wp-content/gallery/macosx/screen_menubar.png"
		     title=""
             data-src="http://old-blog.elao.dev/wp-content/gallery/macosx/screen_menubar.png"
             data-thumbnail="http://old-blog.elao.dev/wp-content/gallery/macosx/thumbs/thumbs_screen_menubar.png"
             data-image-id="1"
             data-title="screen_menubar"
             data-description=""
             target='_self'
             class="shutterset_7b5136f852f7f2d1fad48ffbd4300eda"> <img class="ngg-singlepic"
             src="http://www.elao.com/blog/nextgen-image/1/600x240x100/e5aaa7c86935cedbfedadc68cb15a246"
             alt="screen_menubar"
             title="screen_menubar"
              width="600" /> </a>
</div>

<span></span>

Nous pouvons à présent accéder à notre terminal par un raccourci de notre choix (par défaut ^<), configurable en allant dans les préférences de Visor (clic droit sur l'icône -> Préférences)

 [1]: /blog/mac-os/terminal/les-fichiers-bash-utilisateur.html "Les fichiers bash utilisateur"
 [2]: /blog/mac-os/personnaliser-son-terminal-sous-mac-osx.html "Personnaliser son terminal sous mac OSX"
 [3]: /blog/mac-os/terminal/personnaliser-son-terminal-sous-mac-osx-configuration-avancee.html "Personnaliser son terminal mac OSX"