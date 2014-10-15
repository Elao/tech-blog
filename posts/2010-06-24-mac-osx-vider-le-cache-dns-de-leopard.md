
Lors de la mise à jour d&#8217;un serveur de nom ou tout simplement après une modification de ses DNS, il est parfois nécessaire de &#8220;vider&#8221; le cache local de notre machine pour profiter immédiatement des dernières modifications.

OSX dispose d&#8217;une commande permettant de le faire:

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">sudo</span> dscacheutil <span class="re5">-flushcache</span>
  </div>
</div>

A ajouter dans votre <a title="Personnaliser son terminal" href="http://www.elao.org/mac-os/terminal/personnaliser-son-terminal-sous-mac-osx-configuration-avancee.html" target="_blank">.bashrc</a> car très utile !