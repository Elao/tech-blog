
Sommaire:

1.  [Les fichiers bash utilisateur][1]
2.  Personnaliser son terminal (Configuration simple)
3.  [Personnaliser son terminal (Configuration avancée)][2]
4.  [Gérer ses terminaux avec Visor][3]

Ceux qui ont déjà jeté un petit coup d&#8217;oeil au terminal mac OS X ont pu se rendre compte qu&#8217;il était loin d&#8217;être transcendant &#8230; nous allons donc voir aujourd&#8217;hui comment le rendre un peu moins austère.  
Pour commencer nous allons créer dans notre répertoire utilisateur deux fichiers (s&#8217;ils n&#8217;existent pas déjà):

- Un fichier appelé **.profile**  
- Un fichier appelé **.bashrc**

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">touch</span> .bashrc .profile
  </div>
</div>

Copiez / collez ensuite dans le fichier **.profile** les lignes suivantes:

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw1">if</span> <span class="br0">&#91;</span> <span class="re5">-n</span> <span class="st0">"<span class="es2">$BASH_VERSION</span>"</span> <span class="br0">&#93;</span>; <span class="kw1">then</span><br /> &nbsp; <span class="co0"># include .bashrc if it exists</span><br /> &nbsp; <span class="kw1">if</span> <span class="br0">&#91;</span> <span class="re5">-f</span> ~<span class="sy0">/</span>.bashrc <span class="br0">&#93;</span>; <span class="kw1">then</span><br /> &nbsp; &nbsp; . ~<span class="sy0">/</span>.bashrc<br /> &nbsp; <span class="kw1">fi</span><br /> <span class="kw1">fi</span><br /> <span class="co0"># set PATH so it includes user's private bin if it exists</div></span><br /> <span class="kw1">if</span> <span class="br0">&#91;</span> <span class="re5">-d</span> ~<span class="sy0">/</span>bin <span class="br0">&#93;</span> ; <span class="kw1">then</span><br /> &nbsp; <span class="re2">PATH</span>=~<span class="sy0">/</span>bin:<span class="st0">"<span class="es3">${PATH}</span>"</span><br /> <span class="kw1">fi</span>
  </div>
</div>

Et dans le **.bashrc**:

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw3">export</span> <span class="re2">PS1</span>=<span class="st_h">'\h:\w\$ '</span><br /> <span class="kw3">umask</span> 022<br /> <br /> <span class="kw3">export</span> <span class="re2">CLICOLOR</span>=<span class="nu0">1</span><br /> <br /> <span class="kw3">alias</span> <span class="re2">ll</span>=<span class="st_h">'ls -l'</span><br /> <span class="kw3">alias</span> <span class="re2">la</span>=<span class="st_h">'ls -A'</span><br /> <span class="kw3">alias</span> <span class="re2">l</span>=<span class="st_h">'ls -CF'</span><br /> <span class="kw3">alias</span> <span class="re2">vi</span>=<span class="st_h">'vim'</span><br /> <br /> <span class="kw1">function</span> cyan_red_prompt<br /> <span class="br0">&#123;</span><br /> &nbsp; <span class="kw3">local</span> <span class="re2">CYAN</span>=<span class="st0">"\[&#92;&#48;33[0;36m\]"</span><br /> &nbsp; <span class="kw3">local</span> <span class="re2">GRAY</span>=<span class="st0">"\[&#92;&#48;33[0;37m\]"</span><br /> &nbsp; <span class="kw3">local</span> <span class="re2">RED</span>=<span class="st0">"\[&#92;&#48;33[0;31m\]"</span><br /> <br /> &nbsp; <span class="re2">PS1</span>=<span class="st0">"<span class="es3">${CYAN}</span>[\u@\h <span class="es3">${RED}</span>\w<span class="es3">${CYAN}</span>]<span class="es3">${GRAY}</span> "</span><br /> <span class="br0">&#125;</span><br /> <br /> cyan_red_prompt
  </div>
</div>

 [1]: http://www.elao.com/blog/mac-os/terminal/les-fichiers-bash-utilisateur.html "Les fichiers bash utilisateur"
 [2]: http://www.elao.com/blog/mac-os/terminal/personnaliser-son-terminal-sous-mac-osx-configuration-avancee.html "Personnaliser son terminal sous mac OSX"
 [3]: http://www.elao.com/blog/mac-os/terminal/gerer-ses-terminaux-avec-visor.html "Gérer ses terminaux avec Visor"