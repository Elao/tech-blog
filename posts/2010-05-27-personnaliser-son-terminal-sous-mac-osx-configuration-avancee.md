
Sommaire:

<li style="text-align: justify;">
  <a title="Les fichiers bash utilisateur" href="http://www.elao.com/blog/mac-os/terminal/les-fichiers-bash-utilisateur.html ">Les fichiers bash utilisateur</a>
</li>
1.  [Personnaliser son terminal (Configuration simple)][1]
2.  Personnaliser son terminal (Configuration avancée)
3.  [Gérer ses terminaux avec Visor][2]

Nous avons de la couleur dans notre terminal et quelques alias, c'est top, mais nous pouvons aller un peu plus loin en améliorant notre quotidien de développeur !
Voici les différentes améliorations glanées au fil du temps, notamment de ce <a title="Infinitered" href="http://blog.infinitered.com/" target="_blank">site</a>, que j'utilise (à placer dans le .bashrc)

**Garder un oeil sur les logs apache et php:**

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw1">if</span> <span class="br0">&#91;</span> <span class="re5">-f</span> <span class="co1">${PHP_ERROR_FILE}</span> <span class="br0">&#93;</span>; <span class="kw1">then</span><br />         <span class="kw3">alias</span> <span class="re2">phperror</span>=<span class="st0">"tail -f <span class="es3">${PHP_ERROR_FILE}</span>"</span><br /> <span class="kw1">fi</span><br /> <br /> <span class="kw1">if</span> <span class="br0">&#91;</span> <span class="re5">-f</span> <span class="co1">${APACHE_LOG_FILE}</span> <span class="br0">&#93;</span>; <span class="kw1">then</span><br />         <span class="kw3">alias</span> <span class="re2">apachelogs</span>=<span class="st0">"tail -f <span class="es3">${APACHE_LOG_FILE}</span>"</span><br /> <span class="kw1">fi</span>
  </div>
</div>

**Les indispensables ...**

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw3">export</span> <span class="re2">HISTCONTROL</span>=ignoredups <span class="co0"># Ignores dupes in the history</span><br /> <span class="kw3">shopt</span> <span class="re5">-s</span> checkwinsize <span class="co0"># After each command, checks the windows size and changes lines and columns</span><br /> <br /> <span class="co0"># bash completion settings (actually, these are readline settings)</span><br /> <span class="kw3">bind</span> <span class="st0">"set completion-ignore-case on"</span> <span class="co0"># note: bind is used instead of setting these in .inputrc.  This ignores case in bash completion</span><br /> <span class="kw3">bind</span> <span class="st0">"set bell-style none"</span> <span class="co0"># No bell, because it's damn annoying</span><br /> <span class="kw3">bind</span> <span class="st0">"set show-all-if-ambiguous On"</span> <span class="co0"># this allows you to automatically show completion without double tab-ing</span>
  </div>
</div>

**Pour éviter la boulette et de passer une trèèèès longue nuit ...**

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw3">alias</span> <span class="re2">rm</span>=<span class="st_h">'rm -i'</span><br /> <span class="kw3">alias</span> <span class="re2">cp</span>=<span class="st_h">'cp -i'</span><br /> <span class="kw3">alias</span> <span class="re2">mv</span>=<span class="st_h">'mv -i'</span>
  </div>
</div>

**Commandes utiles**

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw3">alias</span> <span class="re2">g</span>=<span class="st_h">'grep -i'</span>  <span class="co0">#Faire un grep non sensible à la casse</span><br /> <span class="kw3">alias</span> <span class="re2">f</span>=<span class="st_h">'find . -iname'</span> <span class="co0"># Faire une recherche non sensible à la casse dans le répertoire courant</span><br /> <span class="kw3">alias</span> <span class="re2">ducks</span>=<span class="st_h">'du -hcks * | sort -rn|head -11'</span> <span class="co0"># Renvoie la liste des répertoires avec leur taille</span><br /> <span class="kw3">alias</span> <span class="re2">top</span>=<span class="st_h">'top -o cpu'</span><br /> <span class="kw3">alias</span> <span class="re2">systail</span>=<span class="st_h">'tail -f /var/log/system.log'</span>
  </div>
</div>

**Et moins utiles ... <img src="http://old-blog.elao.dev/wp-includes/images/smilies/icon_smile.gif" alt="icon smile Personnaliser son terminal sous mac OSX (Configuration avancée)" class="wp-smiley" title="Personnaliser son terminal sous mac OSX (Configuration avancée)" /> **

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="co0">#Renvoie la liste des commandes les plus utilisées</span><br /> <span class="kw3">alias</span> <span class="re2">profileme</span>=<span class="st0">"history | awk '{print <span class="es1">\$</span>2}' | awk 'BEGIN{FS=<span class="es1">\"</span>|<span class="es1">\"</span>}{print <span class="es1">\$</span>1}' | sort | uniq -c | sort -n | tail -n 20 | sort -nr"</span>
  </div>
</div>

**Réinitialiser les logs apache**

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
            <span class="kw3">local</span> <span class="re2">APACHE_LOG_DIR</span>=<span class="st0">"/var/log/apache2"</span><br />         <span class="kw3">local</span> <span class="re2">PHP_ERROR_FILE</span>=<span class="st0">"/var/log/apache2/php-error.log"</span><br />         <span class="kw3">local</span> <span class="re2">APACHE_LOG_FILE</span>=<span class="st0">"/var/log/apache2/apache-error.log"</span><br /> <br />         <span class="kw1">for</span> <span class="kw2">file</span> <span class="kw1">in</span> <span class="sy0">`</span><span class="kw2">ls</span> <span class="co1">${APACHE_LOG_DIR}</span><span class="sy0">`</span><br />         <span class="kw1">do</span><br />                 <span class="kw1">if</span> <span class="br0">&#91;</span> <span class="re5">-f</span> <span class="st0">"<span class="es3">${APACHE_LOG_DIR}</span>/<span class="es3">${file}</span>"</span> <span class="br0">&#93;</span>; <span class="kw1">then</span><br />                         <span class="sy0">`</span> <span class="sy0">></span> <span class="st0">"<span class="es3">${APACHE_LOG_DIR}</span>/<span class="es3">${file}</span>"</span><span class="sy0">`</span><br />                 <span class="kw1">fi</span><br />         <span class="kw1">done</span>
  </div>
</div>

Il suffit ensuite de taper la commande **clean\_apache\_logs **pour vider tous les fichiers de log contenus dans** /var/log/apache2 **

**Celui-ci est particulièrement sympa et permet de bookmarker ses répertoires favoris**

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw1">if</span> <span class="br0">&#91;</span> <span class="sy0">!</span> <span class="re5">-f</span> ~<span class="sy0">/</span>.dirs <span class="br0">&#93;</span>; <span class="kw1">then</span>  <span class="co0"># if doesn't exist, create it</span><br />         <span class="kw2">touch</span> ~<span class="sy0">/</span>.dirs<br /> <span class="kw1">fi</span><br /> <br /> <span class="kw3">alias</span> <span class="re2">show</span>=<span class="st_h">'cat ~/.dirs'</span><br /> save <span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#123;</span><br />         <span class="kw3">command</span> <span class="kw2">sed</span> <span class="st0">"/!$/d"</span> ~<span class="sy0">/</span>.dirs <span class="sy0">></span> ~<span class="sy0">/</span>.dirs1; \<span class="kw2">mv</span> ~<span class="sy0">/</span>.dirs1 ~<span class="sy0">/</span>.dirs; <span class="kw3">echo</span> <span class="st0">"$@"</span>=<span class="co3">\"</span><span class="sy0">`</span><span class="kw3">pwd</span><span class="sy0">`</span><span class="co3">\"</span> <span class="sy0">>></span> ~<span class="sy0">/</span>.dirs; <span class="kw3">source</span> ~<span class="sy0">/</span>.dirs ;<br /> <span class="br0">&#125;</span><br /> <span class="kw3">source</span> ~<span class="sy0">/</span>.dirs  <span class="co0"># Initialization for the above 'save' facility: source the .sdirs file</span><br /> <span class="kw3">shopt</span> <span class="re5">-s</span> cdable_vars <span class="co0"># set the bash option so that no '$' is required when using the above facility</span>
  </div>
</div>

Le fonctionnement est très simple, si je suis dans le répertoire** /Volumes/Data/elao/projects** par exemple, la commande suivante créera un bookmark **"projects"**

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    save projects
  </div>
</div>

Il me suffira ensuite, pour rejoindre ce répertoire de taper la commande suivante:

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw3">cd</span> projects
  </div>
</div>

La liste des bookmarks est consultable via la commande show

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    show<br /> <span class="re2">projects</span>=<span class="st0">"/Volumes/Data/elao/projects"</span>
  </div>
</div>

Les bookmarks sont stockés dans le fichier ~/dirs.

 [1]: http://www.elao.com/blog/mac-os/personnaliser-son-terminal-sous-mac-osx.html "Personnaliser son terminal sous mac OSX"
 [2]: http://www.elao.com/blog/mac-os/terminal/gerer-ses-terminaux-avec-visor.html "Gérer ses terminaux avec Visor"