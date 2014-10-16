
<span style="text-decoration: underline;">Sommaire:</span>

1.  [Création et configuration d&#8217;un dépôt SVN][1]
2.  Scripts de hook SVN
3.  S&#8217;authentifier et accéder à un dépôt SVN (à venir)
4.  SVN over SSH (à venir)
5.  Authentification via certificat (à venir)

<p style="text-align: justify;">
  Dans ce chapitre nous allons parler de &#8220;Hook&#8221; ou &#8220;Crochet&#8221; en français, mais il faut avouer que ça sommes moins bien <img src="http://old-blog.elao.dev/wp-includes/images/smilies/icon_wink.gif" alt="icon wink Scripts de hook SVN (Subversion repository hooking)" class="wp-smiley" title="Scripts de hook SVN (Subversion repository hooking)" />
</p>

<p style="text-align: justify;">
  Pour faire cours un &#8220;hook&#8221; est un programme qui permet de &#8220;détourner&#8221; un traitement avant de le rendre au workflow &#8220;normal&#8221;. Dans notre cas de dépôt SVN un hook va être déclenché par un évenement du référentiel (notre dépôt) comme la création d&#8217;une nouvelle révision ou la modification d&#8217;une propriété non versionnée. Un hook va être appelé avec suffisamment d&#8217;information pour déterminer de quel type est l&#8217;évenement, qu&#8217;elle est sa cible et qui la déclenché.<br /> Selon la valeur de retour de notre hook, le traitement initié sur le dépôt peut continuer, s&#8217;arrêter ou suspendre son exécution.
</p>

<p style="text-align: justify;">
  Comme vu dans le <a title="Création et configuration d’un dépot subversion (SVN)" href="http://www.elao.org/linux/creation-et-configuration-depot-svn.html">chapitre précédent</a> le répertoire de Hooks dispose de quelques exemples de scripts.
</p>

<ul style="text-align: justify;">
  <li>
    post-commit.tmpl
  </li>
  <li>
    post-lock.tmpl
  </li>
  <li>
    post-revprop-change.tmpl
  </li>
  <li>
    post-unlock.tmpl
  </li>
  <li>
    pre-commit.tmpl
  </li>
  <li>
    pre-lock.tmpl
  </li>
  <li>
    pre-revprop-change.tmpl
  </li>
  <li>
    pre-unlock.tmpl
  </li>
  <li>
    start-commit.tmpl
  </li>
</ul>

<p style="text-align: justify;">
  Cette liste présente un modèle de script pour chacun des &#8220;hooks&#8221; disponible pour notre dépôt subversion. Si l&#8217;on regarde le contenu de ces scripts, et plus particulièrement celui de &#8220;start-commit.tmpl&#8221;, nous pouvons voir comment les différentes informations sont récupérées:
</p>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="re2">REPOS</span>=<span class="st0">"$1"</span><br /> <span class="re2">USER</span>=<span class="st0">"$2"</span><br /> <br /> <span class="co0"># Exit on all errors.</span><br /> <span class="kw1">set</span> <span class="re5">-e</span><br /> <br /> <span class="st0">"<span class="es2">$REPOS</span>"</span><span class="sy0">/</span>hooks<span class="sy0">/</span>commit-allower.pl <span class="re5">--repository</span> <span class="st0">"<span class="es2">$REPOS</span>"</span> <span class="re5">--user</span> <span class="st0">"<span class="es2">$USER</span>"</span><br /> <span class="st0">"<span class="es2">$REPOS</span>"</span><span class="sy0">/</span>hooks<span class="sy0">/</span>special-auth-check.py <span class="re5">--user</span> <span class="st0">"<span class="es2">$USER</span>"</span> <span class="re5">--auth-level</span> <span class="nu0">3</span><br /> <br /> <span class="co0"># All checks passed, so allow the commit.</span><br /> <span class="kw3">exit</span> <span class="nu0"></span>
  </div>
</div>

<p style="text-align: justify;">
  Le fonctionnement des différents script est généralement expliqué à l&#8217;intérieur des templates, si nous souhaitons, par exemple, mettre en place un traitement se déclenchant au moment ou un commit est fait, il nous suffit de créer un script appelé &#8220;start-commit&#8221;. Le langage utilisé importe peu, il faut seulement que votre serveur soit capable de l&#8217;interpréter correctement, mais cela peut être un Shell Script, du C, du Python, du PHP, du Perl que sais-je, la seule contrainte à respecter étant de le nommer exactement de la même façon que le hook prévu et de bien rendre ce script exécutable.
</p>

 [1]: http://www.elao.org/linux/creation-et-configuration-depot-svn.html "Création et configuration d’un dépot subversion (SVN)"