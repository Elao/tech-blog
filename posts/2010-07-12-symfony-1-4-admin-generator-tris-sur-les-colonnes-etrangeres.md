
<p style="font-family: georgia, palatino;">
  Bonjour,
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  Je vous propose une méthode permettant d&#8217;effectuer des tris sur les colonnes étrangères dans les modules générés grâce à l&#8217;Admin Generator de Symfony. Ce n&#8217;est sans doute pas la seule manière de procéder, aussi, toute suggestion ou critique constructive est la bienvenue !
</p>

<p style="text-align: justify;">
  <span style="font-family: Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif; line-height: 19px; white-space: normal; font-size: 13px;"> </span>
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  L&#8217;exemple qui suit a été réalisé avec Symfony dans sa version 1.4 et l&#8217;ORM Doctrine. Il met en oeuvre deux entités reliées par une association de type 1:n :
</p>

- l&#8217;objet du modèle <span style="color: #3366ff;"><span style="font-family: georgia, palatino;">Produit</span></span><span style="font-family: georgia, palatino;"> qui appartient à une catégorie,<br /> - l&#8217;objet du modèle </span><span style="color: #3366ff;"><span style="font-family: georgia, palatino;">Catégorie</span></span><span style="font-family: georgia, palatino;"> qui contient plusieurs produits.</span>

<p style="font-family: georgia, palatino;">
  Le schéma de la base de données, volontairement simplifié, est le suivant :
</p>

<div class="codecolorer-container yaml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="yaml codecolorer">
    <span class="co4">Product</span>:<span class="co4"><br /> &nbsp; columns</span>:<span class="co3"><br /> &nbsp; &nbsp; id</span><span class="sy2">: </span> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span class="br0">&#123;</span> type<span class="sy2">: </span>integer<span class="br0">&#40;</span><span class="nu0">4</span><span class="br0">&#41;</span>, primary<span class="sy2">: </span>true, autoincrement<span class="sy2">: </span>true <span class="br0">&#125;</span><span class="co3"><br /> &nbsp; &nbsp; name</span><span class="sy2">: </span> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span class="br0">&#123;</span> type<span class="sy2">: </span>string<span class="br0">&#40;</span><span class="nu0">255</span><span class="br0">&#41;</span>, notnull<span class="sy2">: </span>true <span class="br0">&#125;</span><span class="co3"><br /> &nbsp; &nbsp; category_id</span><span class="sy2">: </span> &nbsp; <span class="br0">&#123;</span> type<span class="sy2">: </span>integer<span class="br0">&#40;</span><span class="nu0">4</span><span class="br0">&#41;</span>, notnull<span class="sy2">: </span>true <span class="br0">&#125;</span><span class="co3"><br /> &nbsp; &nbsp; price</span><span class="sy2">: </span> &nbsp; &nbsp; &nbsp; &nbsp; <span class="br0">&#123;</span> type<span class="sy2">: </span>decimal, notnull<span class="sy2">: </span>true <span class="br0">&#125;</span><span class="co4"><br /> &nbsp; relations</span>:<span class="co3"><br /> &nbsp; &nbsp; Category</span><span class="sy2">: </span> &nbsp; &nbsp; &nbsp;<span class="br0">&#123;</span> local<span class="sy2">: </span>category_id, foreign<span class="sy2">: </span>id, foreignAlias<span class="sy2">: </span>Products, onDelete<span class="sy2">: </span>CASCADE <span class="br0">&#125;</span><br /> <span class="co4"><br /> Category</span>:<span class="co4"><br /> &nbsp; columns</span>:<span class="co3"><br /> &nbsp; &nbsp; id</span><span class="sy2">: </span> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span class="br0">&#123;</span> type<span class="sy2">: </span>integer<span class="br0">&#40;</span><span class="nu0">4</span><span class="br0">&#41;</span>, primary<span class="sy2">: </span>true, autoincrement<span class="sy2">: </span>true <span class="br0">&#125;</span><span class="co3"><br /> &nbsp; &nbsp; name</span><span class="sy2">: </span> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span class="br0">&#123;</span> type<span class="sy2">: </span>string<span class="br0">&#40;</span><span class="nu0">100</span><span class="br0">&#41;</span>, notnull<span class="sy2">: </span>true <span class="br0">&#125;</span>
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  Nous partons du principe que vous disposez d&#8217;un projet Symfony &#8220;blanc&#8221; déjà créé et que la connexion à la base de données a déjà été configurée. Si ce n&#8217;est pas le cas, je vous invite à <a title="installer Symfony" href="http://www.symfony-project.org/getting-started/1_4/en/03-Symfony-Installation" target="_blank">installer Symfony</a> et <a title="créer un projet Symfony" href="http://www.symfony-project.org/getting-started/1_4/en/04-Project-Setup" target="_blank">créer votre premier projet</a> selon la méthode préconisée par les créateurs de Symfony. Pour les plus pressés d&#8217;entre vous, vous avez la possibilité de télécharger une <a title="Sandbox Symfony" href="http://www.symfony-project.org/get/sf_sandbox_1_4.zip" target="_blank">sandbox Symfony</a> prête à l&#8217;emploi.
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  Commençons par construire les classes de notre modèle ainsi que notre base de données, créer une application Backend, puis à l&#8217;aide de l&#8217;Admin Generator, générer le module associé à notre OM &#8220;Product&#8221;. Pour cela, ouvrons une ligne de commande, plaçons-nous à la racine de notre projet et lançons successivement les commandes symfony suivantes :
</p>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    &nbsp; .<span class="sy0">/</span>symfony doctrine:build <span class="re5">--all</span><br /> &nbsp; .<span class="sy0">/</span>symfony generate:app backend<br /> &nbsp; .<span class="sy0">/</span>symfony doctrine:generate-admin backend Product<br /> &nbsp; .<span class="sy0">/</span>symfony <span class="kw2">cc</span>
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  A présent, à l&#8217;aide de notre navigateur préféré, rendons-nous à l&#8217;URL <span style="color: #3366ff;">http://mon_hote_virtuel/backend_dev.php/product</span> (remplacez <em>mon_hote_virtuel </em>par celui que vous avez défini !) et admirons le résultat :
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  <img class="aligncenter size-medium wp-image-688" title="sf14_tris_fk_1" src="/blog/wp-content/uploads/2010/07/sf14_tris_fk_1.png" alt="sf14 tris fk 1 Symfony 1.4   Admin Generator   Tris sur les colonnes étrangères" width="75%" />
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  Bon, je vous le concède, il n&#8217;y a pas de quoi s&#8217;enthousiasmer, nous pourrions améliorer l&#8217;apparence de notre page et surtout, tout cela manque un peu de matière &#8230; Nous allons donc injecter quelques données dans la base. Concernant le style de la page, je vous fais confiance pour améliorer tout cela à l&#8217;aide d&#8217;une CSS ; pour ma part, je ne m&#8217;y risquerai pas de peur de faire étalage de mon manque flagrant de talent graphique &#8230;
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  Pour alimenter la base de données, voici le jeu de fixtures que nous allons charger :
</p>

<div class="codecolorer-container yaml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="yaml codecolorer">
    <span class="co1"># data/fixtures/fixtures.yml</span><span class="co4"><br /> Category</span>:<span class="co4"><br /> &nbsp; Category_1</span>:<span class="co3"><br /> &nbsp; &nbsp; id</span><span class="sy2">: </span><span class="nu0">1</span><span class="co3"><br /> &nbsp; &nbsp; name</span><span class="sy2">: </span>Categorie <span class="nu0">1</span><span class="co4"><br /> &nbsp; Category_2</span>:<span class="co3"><br /> &nbsp; &nbsp; id</span><span class="sy2">: </span><span class="nu0">2</span><span class="co3"><br /> &nbsp; &nbsp; name</span><span class="sy2">: </span>Categorie <span class="nu0">2</span><br /> <span class="co4"><br /> Product</span>:<span class="co3"><br /> <?php for($i = 1 ; $i <= 10 ; $i++) </span><span class="sy2">: </span>?><span class="co4"><br /> &nbsp; Produit_<?php echo $i ?></span>:<span class="co3"><br /> &nbsp; &nbsp; id</span><span class="sy2">: </span><?php echo $i.<span class="st0">"\\n"</span> ?><span class="co3"><br /> &nbsp; &nbsp; name</span><span class="sy2">: </span>Produit_<?php echo $i.<span class="st0">"\\n"</span> ?><span class="co3"><br /> &nbsp; &nbsp; price</span><span class="sy2">: </span><?php echo <span class="br0">&#40;</span>$i * <span class="nu0">1.0</span><span class="br0">&#41;</span>.<span class="st0">"\\n"</span> ?><span class="co3"><br /> &nbsp; &nbsp; Category</span><span class="sy2">: </span>Category_<?php echo <span class="br0">&#40;</span>$i <span class="co2">% 2 == 0) ? 1 : 2 ?><?php echo "\\n" ?></span><br /> <?php endfor ?>
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  Pour injecter les données en base, lançons la commande adéquate &#8230;
</p>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    .<span class="sy0">/</span>symfony doctrine:data-load
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  &#8230; et émerveillons-nous devant le résultat ainsi obtenu :
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  <img class="aligncenter size-medium wp-image-696" title="sf14_tris_fk_2" src="/blog/wp-content/uploads/2010/07/sf14_tris_fk_2.png" alt="sf14 tris fk 2 Symfony 1.4   Admin Generator   Tris sur les colonnes étrangères" height="50%" />
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  Nous allons à présent modifier le fichier generator.yml (apps/backend/modules/product/config) pour afficher l&#8217;intitulé de la catégorie plutôt que son identifiant :
</p>

<div class="codecolorer-container yaml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="yaml codecolorer">
    <span class="co4"> &nbsp; &nbsp;config</span>:<span class="co3"><br /> &nbsp; &nbsp; &nbsp; actions</span><span class="sy2">: </span>~<span class="co3"><br /> &nbsp; &nbsp; &nbsp; fields</span><span class="sy2">: </span> ~<span class="co4"><br /> &nbsp; &nbsp; &nbsp; list</span>:<span class="co3"><br /> &nbsp; &nbsp; &nbsp; &nbsp; display</span><span class="sy2">: </span><span class="br0">&#91;</span> =name, Category, price <span class="br0">&#93;</span><span class="co3"><br /> &nbsp; &nbsp; &nbsp; filter</span><span class="sy2">: </span> ~<span class="co3"><br /> &nbsp; &nbsp; &nbsp; form</span><span class="sy2">: </span> &nbsp; ~<span class="co3"><br /> &nbsp; &nbsp; &nbsp; edit</span><span class="sy2">: </span> &nbsp; ~<span class="co3"><br /> &nbsp; &nbsp; &nbsp; new</span><span class="sy2">: </span> &nbsp; &nbsp;~
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  <img class="aligncenter size-medium wp-image-696" title="sf14_tris_fk_3" src="/blog/wp-content/uploads/2010/07/sf14_tris_fk_3.png" alt="sf14 tris fk 3 Symfony 1.4   Admin Generator   Tris sur les colonnes étrangères" height="50%" />
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  Vous remarquerez que les en-têtes de colonnes des champs natifs de la table &#8220;Product&#8221; permettent de trier le contenu de la colonne mais ça n&#8217;est pas le cas de la colonne &#8220;Category&#8221;. Cela étant, vu le titre de cet article, cela ne devrait pas réellement vous étonner &#8230; Car nous allons justement voir une manière d&#8217;activer les tris sur ces colonnes étrangères. Et pour cela, nous allons surcharger le thème de l&#8217;Admin Generator, de manière notamment à bénéficier de cette fonctionnalité sur tous les modules que nous créerons.
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  Comme nous utilisons l&#8217;ORM Doctrine, le thème sur lequel s&#8217;appuie l&#8217;Admin Generator se trouve dans la distribution de Symfony, dans le sous-répertoire lib/plugins/sfDoctrinePlugin/data/generator/sfDoctrineModule :
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  <img class="aligncenter size-medium wp-image-696" title="Themes de l'admin generator de Doctrine" src="/blog/wp-content/uploads/2010/07/sf14_tris_fk_4.png" alt="sf14 tris fk 4 Symfony 1.4   Admin Generator   Tris sur les colonnes étrangères" height="25%" />
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  Nous constatons qu&#8217;il existe deux thèmes dans ce sous-répertoire : &#8220;admin&#8221; et &#8220;default&#8221;. Lequel des deux devons-nous surcharger ? Celui que nous utilisons actuellement ! Et pour connaître ce thème, il suffit de consulter le fichier generator.yml de notre module &#8220;Product&#8221;, qui nous informe que le thème utilisé est &#8220;admin&#8221; :
</p>

<div class="codecolorer-container yaml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="yaml codecolorer">
    <span class="co4">generator</span>:<span class="co3"><br /> &nbsp; class</span><span class="sy2">: </span>sfDoctrineGenerator<span class="co4"><br /> &nbsp; param</span>:<span class="co3"><br /> &nbsp; &nbsp; model_class</span><span class="sy2">: </span> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;Product<span class="co3"><br /> &nbsp; &nbsp; theme</span><span class="sy2">: </span> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; admin &nbsp;<span class="co1"># Le thème utilisé par l'admin generator ; autre possibilité : "default"</span>
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  Pour pouvoir surcharger le thème, il va nous falloir copier le contenu du sous-répertoire &#8220;admin&#8221; du plugin Doctrine (<em>ie</em> les trois sous-dossiers <strong>parts</strong>, <strong>skeleton</strong> et <strong>template</strong>) dans le dossier <span style="color: #3366ff;"><strong>data/generator/sfDoctrineModule/admin</strong></span> de notre projet Symfony (ce dossier n&#8217;existe pas, nous devons le créer manuellement) afin d&#8217;obtenir le résultat suivant :
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  <img class="aligncenter size-medium wp-image-696" title="Theme admin generator surchargé" src="/blog/wp-content/uploads/2010/07/sf14_tris_fk_5.png" alt="sf14 tris fk 5 Symfony 1.4   Admin Generator   Tris sur les colonnes étrangères" height="20%" />
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  <strong>Attention</strong> : si vous avez installé Symfony depuis un gestionnaire de sources, prenez soin de copier ces trois répertoires en excluant les dossiers de versioning (.svn, .git &#8230;) :
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  On copie les répertoires dans un dossier temporaire (SYMFONY_DIR = le répertoire où vous avez placé votre distribution Symfony).
</p>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    &nbsp;<span class="kw2">cp</span> <span class="re5">-r</span> SYMFONY_DIR<span class="sy0">/</span>lib<span class="sy0">/</span>plugins<span class="sy0">/</span>sfDoctrinePlugin<span class="sy0">/</span>data<span class="sy0">/</span>generator<span class="sy0">/</span>sfDoctrineModule<span class="sy0">/</span>admin ~<span class="sy0">/</span>Temp<span class="sy0">/</span>admin<br /> &nbsp;<span class="kw3">cd</span> ~<span class="sy0">/</span>Temp<span class="sy0">/</span>admin
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  On supprime les répertoires .svn récursivement
</p>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    &nbsp;<span class="kw2">rm</span> <span class="re5">-r</span> $<span class="br0">&#40;</span><span class="kw2">grep</span> <span class="re5">-lnr</span> <span class="st0">".svn"</span> <span class="sy0">*</span><span class="br0">&#41;</span>
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  On crée le répertoire /data/generator/sfDoctrineModule dans notre projet. Adaptez le chemin et le nom du projet en fonction de votre propre configuration !
</p>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    &nbsp;<span class="kw3">cd</span> <span class="sy0">/</span>path_to_my_workspace<span class="sy0">/</span>my_project_name<span class="sy0">/</span>data<br /> &nbsp;<span class="kw2">mkdir</span> generator<br /> &nbsp;<span class="kw3">cd</span> generator<br /> &nbsp;<span class="kw2">mkdir</span> sfDoctrineModule
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  Copions le dossier admin que nous avions placé dans un répertoire temporaire dans le répertoire que nous venons de créer.
</p>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    &nbsp;<span class="kw3">cd</span> sfDoctrineModule<br /> &nbsp;<span class="kw2">cp</span> <span class="re5">-r</span> ~<span class="sy0">/</span>Temp<span class="sy0">/</span>admin<span class="sy0">/</span> .<span class="sy0">/</span>admin
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  A présent, nous allons nous assurer que les fichiers du thème que nous venons de placer dans notre projet sont bien ceux qu&#8217;utilise l&#8217;Admin Generator. Pour cela, on va modifier un des fichiers du répertoire &#8220;templates&#8221; :
</p>

<div class="codecolorer-container html4strict vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="html4strict codecolorer">
    &nbsp; &nbsp;# data/generator/sfDoctrineModule/admin/template/templates/_list_header.php<br /> &nbsp; &nbsp;<span class="sc2"><<a href="http://december.com/html/4/element/h1.html"><span class="kw2">h1</span></a>></span>Une template que j'ai surchargée !<span class="sc2"><<span class="sy0">/</span><a href="http://december.com/html/4/element/h1.html"><span class="kw2">h1</span></a>></span>
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  Rechargeons la page du Backend listant les produits et constatons le résultat : notre modification a bien été prise en compte. Si ce n&#8217;est pas le cas, l&#8217;incontournable <em>symfony cc</em> devrait résoudre le problème ! Maintenant que nous savons comment surcharger un thème, nous pouvons le redéfinir afin d&#8217;inclure le tri sur les clés étrangères. Par ailleurs, en modifiant le partial _list_header.php, vous aurez sans doute remarqué le découpage des différentes vues et les partiels utilisés par l&#8217;Admin Generator ; les noms des fichiers sont suffisamment explicites pour comprendre à quoi chacun de ces fichiers correspond. En l&#8217;occurrence, nous souhaitons modifier les en-têtes de colonnes afin d&#8217;ajouter notre fameux tri sur les colonnes étrangères et nous nous doutons bien qu&#8217;il va falloir pour cela modifier le partial &#8220;_list_th_tabular.php&#8221;. Voici le contenu de ce fichier :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    #data/generator/sfDoctrineModule/admin/template/templates/_list_th_tabular.php<br /> <span class="kw2"><?php</span> <span class="kw1">foreach</span> <span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">configuration</span><span class="sy0">-></span><span class="me1">getValue</span><span class="br0">&#40;</span><span class="st_h">'list.display'</span><span class="br0">&#41;</span> <span class="kw1">as</span> <span class="re0">$name</span> <span class="sy0">=></span> <span class="re0">$field</span><span class="br0">&#41;</span><span class="sy0">:</span> <span class="sy1">?></span><br /> [?php slot('sf_admin.current_header') ?]<br /> <th class="sf_admin_<span class="kw2"><?php</span> <span class="kw1">echo</span> <a href="http://www.php.net/strtolower"><span class="kw3">strtolower</span></a><span class="br0">&#40;</span><span class="re0">$field</span><span class="sy0">-></span><a href="http://www.php.net/gettype"><span class="kw3">getType</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span> <span class="sy1">?></span> sf_admin_list_th_<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$name</span> <span class="sy1">?></span>"><br /> <span class="kw2"><?php</span> <span class="kw1">if</span> <span class="br0">&#40;</span><span class="re0">$field</span><span class="sy0">-></span><span class="me1">isReal</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">:</span> <span class="sy1">?></span><br /> &nbsp; [?php if ('<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$name</span> <span class="sy1">?></span>' == $sort[0]): ?]<br /> &nbsp; &nbsp; [?php echo link_to(__('<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$field</span><span class="sy0">-></span><span class="me1">getConfig</span><span class="br0">&#40;</span><span class="st_h">'label'</span><span class="sy0">,</span> <span class="st_h">''</span><span class="sy0">,</span> <span class="kw4">true</span><span class="br0">&#41;</span> <span class="sy1">?></span>', array(), '<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">getI18nCatalogue</span><span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="sy1">?></span>'), '@<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">getUrlForAction</span><span class="br0">&#40;</span><span class="st_h">'list'</span><span class="br0">&#41;</span> <span class="sy1">?></span>', array('query_string' => 'sort=<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$name</span> <span class="sy1">?></span>&sort_type='.($sort[1] == 'asc' ? 'desc' : 'asc'))) ?]<br /> &nbsp; &nbsp; [?php echo image_tag(sfConfig::get('sf_admin_module_web_dir').'/images/'.$sort[1].'.png', array('alt' => __($sort[1], array(), 'sf_admin'), 'title' => __($sort[1], array(), 'sf_admin'))) ?]<br /> &nbsp; [?php else: ?]<br /> &nbsp; &nbsp; [?php echo link_to(__('<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$field</span><span class="sy0">-></span><span class="me1">getConfig</span><span class="br0">&#40;</span><span class="st_h">'label'</span><span class="sy0">,</span> <span class="st_h">''</span><span class="sy0">,</span> <span class="kw4">true</span><span class="br0">&#41;</span> <span class="sy1">?></span>', array(), '<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">getI18nCatalogue</span><span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="sy1">?></span>'), '@<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">getUrlForAction</span><span class="br0">&#40;</span><span class="st_h">'list'</span><span class="br0">&#41;</span> <span class="sy1">?></span>', array('query_string' => 'sort=<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$name</span> <span class="sy1">?></span>&sort_type=asc')) ?]<br /> &nbsp; [?php endif; ?]<br /> <span class="kw2"><?php</span> <span class="kw1">else</span><span class="sy0">:</span> <span class="sy1">?></span><br /> &nbsp; [?php echo __('<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$field</span><span class="sy0">-></span><span class="me1">getConfig</span><span class="br0">&#40;</span><span class="st_h">'label'</span><span class="sy0">,</span> <span class="st_h">''</span><span class="sy0">,</span> <span class="kw4">true</span><span class="br0">&#41;</span> <span class="sy1">?></span>', array(), '<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">getI18nCatalogue</span><span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="sy1">?></span>') ?]<br /> <span class="kw2"><?php</span> <span class="kw1">endif</span><span class="sy0">;</span> <span class="sy1">?></span><br /> </th><br /> [?php end_slot(); ?]<br /> <span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">addCredentialCondition</span><span class="br0">&#40;</span><span class="st0">"[?php include_slot('sf_admin.current_header') ?]"</span><span class="sy0">,</span> <span class="re0">$field</span><span class="sy0">-></span><span class="me1">getConfig</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span> <span class="sy1">?></span><br /> <span class="kw2"><?php</span> <span class="kw1">endforeach</span><span class="sy0">;</span> <span class="sy1">?></span>
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  Avouez que le code est un petit peu intimidant &#8230; On note la présence de balises <?php ?> comme nous avons l&#8217;habitude d&#8217;en rencontrer mais également des pseudo-balises du type [?php ... ?]. L&#8217;Admin Generator va parser ce fichier une première fois afin de générer un fichier PHP et le placer dans le cache. Les balises 
  
  <?php vont donc dans un premier temps être interprétées afin de générer notre template en se basant sur la configuration du module, puis les balises [?php ?] vont être remplacées par de véritables balises PHP afin d'obtenir un code exécutable par l'interprétateur, une fois placé dans le cache. Jolie gymnastique intellectuelle, tout de même ! Il va donc nous falloir modifier ce fichier pour obtenir le résultat escompté. Un conseil : on peut aisément se perdre et s'embrouiller lorsque l'on plonge les mains dans ce code, entre les instructions destinées à être parsées par l'Admin Generator et celles qui seront interprétées par PHP lorsque notre application s'exécutera. Si vous vous sentez perdus, n'hésitez pas à consulter le fichier généré dans le cache (résultat obtenu après traitement par le générateur de code de Doctrine) pour y voir un peu plus clair. D'ailleurs, voici le contenu du fichier PHP généré et placé dans le cache :</p> 
  
  <div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="php codecolorer">
      #cache/dev/backend/modules/autoProduct/templates/_list_th_tabular.php<br /> <span class="kw2"><?php</span> slot<span class="br0">&#40;</span><span class="st_h">'sf_admin.current_header'</span><span class="br0">&#41;</span> <span class="sy1">?></span><br /> <th class="sf_admin_text sf_admin_list_th_name"><br /> &nbsp; <span class="kw2"><?php</span> <span class="kw1">if</span> <span class="br0">&#40;</span><span class="st_h">'name'</span> <span class="sy0">==</span> <span class="re0">$sort</span><span class="br0">&#91;</span><span class="nu0"></span><span class="br0">&#93;</span><span class="br0">&#41;</span><span class="sy0">:</span> <span class="sy1">?></span><br /> &nbsp; &nbsp; <span class="kw2"><?php</span> <span class="kw1">echo</span> link_to<span class="br0">&#40;</span>__<span class="br0">&#40;</span><span class="st_h">'Name'</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">,</span> <span class="st_h">'messages'</span><span class="br0">&#41;</span><span class="sy0">,</span> <span class="st_h">'@product'</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="st_h">'query_string'</span> <span class="sy0">=></span> <span class="st_h">'sort=name&sort_type='</span><span class="sy0">.</span><span class="br0">&#40;</span><span class="re0">$sort</span><span class="br0">&#91;</span><span class="nu0">1</span><span class="br0">&#93;</span> <span class="sy0">==</span> <span class="st_h">'asc'</span> ? <span class="st_h">'desc'</span> <span class="sy0">:</span> <span class="st_h">'asc'</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="br0">&#41;</span> <span class="sy1">?></span><br /> &nbsp; &nbsp; <span class="kw2"><?php</span> <span class="kw1">echo</span> image_tag<span class="br0">&#40;</span>sfConfig<span class="sy0">::</span><span class="me2">get</span><span class="br0">&#40;</span><span class="st_h">'sf_admin_module_web_dir'</span><span class="br0">&#41;</span><span class="sy0">.</span><span class="st_h">'/images/'</span><span class="sy0">.</span><span class="re0">$sort</span><span class="br0">&#91;</span><span class="nu0">1</span><span class="br0">&#93;</span><span class="sy0">.</span><span class="st_h">'.png'</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="st_h">'alt'</span> <span class="sy0">=></span> __<span class="br0">&#40;</span><span class="re0">$sort</span><span class="br0">&#91;</span><span class="nu0">1</span><span class="br0">&#93;</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">,</span> <span class="st_h">'sf_admin'</span><span class="br0">&#41;</span><span class="sy0">,</span> <span class="st_h">'title'</span> <span class="sy0">=></span> __<span class="br0">&#40;</span><span class="re0">$sort</span><span class="br0">&#91;</span><span class="nu0">1</span><span class="br0">&#93;</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">,</span> <span class="st_h">'sf_admin'</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="br0">&#41;</span> <span class="sy1">?></span><br /> &nbsp; <span class="kw2"><?php</span> <span class="kw1">else</span><span class="sy0">:</span> <span class="sy1">?></span><br /> &nbsp; &nbsp; <span class="kw2"><?php</span> <span class="kw1">echo</span> link_to<span class="br0">&#40;</span>__<span class="br0">&#40;</span><span class="st_h">'Name'</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">,</span> <span class="st_h">'messages'</span><span class="br0">&#41;</span><span class="sy0">,</span> <span class="st_h">'@product'</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="st_h">'query_string'</span> <span class="sy0">=></span> <span class="st_h">'sort=name&sort_type=asc'</span><span class="br0">&#41;</span><span class="br0">&#41;</span> <span class="sy1">?></span><br /> &nbsp; <span class="kw2"><?php</span> <span class="kw1">endif</span><span class="sy0">;</span> <span class="sy1">?></span><br /> </th><br /> <span class="kw2"><?php</span> end_slot<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span> <span class="sy1">?></span><br /> <span class="kw2"><?php</span> include_slot<span class="br0">&#40;</span><span class="st_h">'sf_admin.current_header'</span><span class="br0">&#41;</span> <span class="sy1">?></span><span class="kw2"><?php</span> slot<span class="br0">&#40;</span><span class="st_h">'sf_admin.current_header'</span><span class="br0">&#41;</span> <span class="sy1">?></span><br /> <th class="sf_admin_text sf_admin_list_th_Category"><br /> &nbsp; <span class="kw2"><?php</span> <span class="kw1">echo</span> __<span class="br0">&#40;</span><span class="st_h">'Category'</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">,</span> <span class="st_h">'messages'</span><span class="br0">&#41;</span> <span class="sy1">?></span><br /> </th><br /> <span class="kw2"><?php</span> end_slot<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span> <span class="sy1">?></span><br /> <span class="kw2"><?php</span> include_slot<span class="br0">&#40;</span><span class="st_h">'sf_admin.current_header'</span><span class="br0">&#41;</span> <span class="sy1">?></span><span class="kw2"><?php</span> slot<span class="br0">&#40;</span><span class="st_h">'sf_admin.current_header'</span><span class="br0">&#41;</span> <span class="sy1">?></span><br /> <th class="sf_admin_text sf_admin_list_th_price"><br /> &nbsp; <span class="kw2"><?php</span> <span class="kw1">if</span> <span class="br0">&#40;</span><span class="st_h">'price'</span> <span class="sy0">==</span> <span class="re0">$sort</span><span class="br0">&#91;</span><span class="nu0"></span><span class="br0">&#93;</span><span class="br0">&#41;</span><span class="sy0">:</span> <span class="sy1">?></span><br /> &nbsp; &nbsp; <span class="kw2"><?php</span> <span class="kw1">echo</span> link_to<span class="br0">&#40;</span>__<span class="br0">&#40;</span><span class="st_h">'Price'</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">,</span> <span class="st_h">'messages'</span><span class="br0">&#41;</span><span class="sy0">,</span> <span class="st_h">'@product'</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="st_h">'query_string'</span> <span class="sy0">=></span> <span class="st_h">'sort=price&sort_type='</span><span class="sy0">.</span><span class="br0">&#40;</span><span class="re0">$sort</span><span class="br0">&#91;</span><span class="nu0">1</span><span class="br0">&#93;</span> <span class="sy0">==</span> <span class="st_h">'asc'</span> ? <span class="st_h">'desc'</span> <span class="sy0">:</span> <span class="st_h">'asc'</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="br0">&#41;</span> <span class="sy1">?></span><br /> &nbsp; &nbsp; <span class="kw2"><?php</span> <span class="kw1">echo</span> image_tag<span class="br0">&#40;</span>sfConfig<span class="sy0">::</span><span class="me2">get</span><span class="br0">&#40;</span><span class="st_h">'sf_admin_module_web_dir'</span><span class="br0">&#41;</span><span class="sy0">.</span><span class="st_h">'/images/'</span><span class="sy0">.</span><span class="re0">$sort</span><span class="br0">&#91;</span><span class="nu0">1</span><span class="br0">&#93;</span><span class="sy0">.</span><span class="st_h">'.png'</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="st_h">'alt'</span> <span class="sy0">=></span> __<span class="br0">&#40;</span><span class="re0">$sort</span><span class="br0">&#91;</span><span class="nu0">1</span><span class="br0">&#93;</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">,</span> <span class="st_h">'sf_admin'</span><span class="br0">&#41;</span><span class="sy0">,</span> <span class="st_h">'title'</span> <span class="sy0">=></span> __<span class="br0">&#40;</span><span class="re0">$sort</span><span class="br0">&#91;</span><span class="nu0">1</span><span class="br0">&#93;</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">,</span> <span class="st_h">'sf_admin'</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="br0">&#41;</span> <span class="sy1">?></span><br /> &nbsp; <span class="kw2"><?php</span> <span class="kw1">else</span><span class="sy0">:</span> <span class="sy1">?></span><br /> &nbsp; &nbsp; <span class="kw2"><?php</span> <span class="kw1">echo</span> link_to<span class="br0">&#40;</span>__<span class="br0">&#40;</span><span class="st_h">'Price'</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">,</span> <span class="st_h">'messages'</span><span class="br0">&#41;</span><span class="sy0">,</span> <span class="st_h">'@product'</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="st_h">'query_string'</span> <span class="sy0">=></span> <span class="st_h">'sort=price&sort_type=asc'</span><span class="br0">&#41;</span><span class="br0">&#41;</span> <span class="sy1">?></span><br /> &nbsp; <span class="kw2"><?php</span> <span class="kw1">endif</span><span class="sy0">;</span> <span class="sy1">?></span><br /> </th><br /> <span class="kw2"><?php</span> end_slot<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span> <span class="sy1">?></span><br /> <span class="kw2"><?php</span> include_slot<span class="br0">&#40;</span><span class="st_h">'sf_admin.current_header'</span><span class="br0">&#41;</span> <span class="sy1">?></span>
    </div>
  </div>
  
  <p style="font-family: georgia, palatino; text-align: justify;">
    Ce code nous est tout de même un peu plus familier. Mais revenons à nos moutons ! Vous aurez sans doute noté dans le premier fichier la condition if ($field->isReal()) ; comme vous vous en doutez, c&#8217;est dans la clause &#8220;else&#8221; que nous allons gérer le cas des colonnes &#8220;virtuelles&#8221; afin de créer un lien permettant d&#8217;effectuer un tri sur ces colonnes.
  </p>
  
  <div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="php codecolorer">
      <span class="kw2"><?php</span> <span class="kw1">if</span> <span class="br0">&#40;</span><span class="re0">$field</span><span class="sy0">-></span><span class="me1">isReal</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">:</span> <span class="sy1">?></span><br /> &nbsp; &nbsp;# Si le champ est "réel" (comprendre "natif", "correspondant réellement à un champ de la table en BD")<br /> &nbsp; &nbsp;# Le code original reste inchangé<br /> &nbsp; &nbsp;# ....<br /> <span class="kw2"><?php</span> <span class="kw1">else</span> <span class="sy0">:</span> <span class="sy1">?></span><br /> &nbsp; &nbsp;# Nous allons ici créer un lien hypertexte permettant d'effectuer un tri sur les colonnes "virtuelles"<br /> <span class="kw2"><?php</span> <span class="kw1">endif</span> <span class="sy1">?></span>
    </div>
  </div>
  
  <p style="font-family: georgia, palatino; text-align: left;">
    Voici le code à placer entre les balises &#8220;<em>else</em>&#8221; et &#8220;<em>endif</em>&#8221; dans le fichier <span style="color: #3366ff;">/data/generator/sfDoctrineModule/admin/template/templates/_list_th_tabular.php <span style="color: #000000;"> </span></span>:
  </p>
  
  <div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="php codecolorer">
      <span class="kw2"><?php</span> <span class="kw1">else</span><span class="sy0">:</span> <span class="sy1">?></span><br /> &nbsp;<span class="kw2"><?php</span> <span class="kw1">if</span><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">configuration</span><span class="sy0">-></span><span class="me1">getValue</span><span class="br0">&#40;</span><span class="st_h">'list.fields.'</span><span class="sy0">.</span><span class="re0">$name</span><span class="sy0">.</span><span class="st_h">'.sort_key'</span><span class="br0">&#41;</span><span class="br0">&#41;</span> <span class="sy0">:</span> <span class="sy1">?></span><br /> &nbsp; [?php echo link_to(__('<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$field</span><span class="sy0">-></span><span class="me1">getConfig</span><span class="br0">&#40;</span><span class="st_h">'label'</span><span class="sy0">,</span> <span class="st_h">''</span><span class="sy0">,</span> <span class="kw4">true</span><span class="br0">&#41;</span> <span class="sy1">?></span>', array(), '<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">getI18nCatalogue</span><span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="sy1">?></span>'), '@<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">getUrlForAction</span><span class="br0">&#40;</span><span class="st_h">'list'</span><span class="br0">&#41;</span> <span class="sy1">?></span>', array('query_string' => 'sort=<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">configuration</span><span class="sy0">-></span><span class="me1">getValue</span><span class="br0">&#40;</span><span class="st_h">'list.fields.'</span><span class="sy0">.</span><span class="re0">$name</span><span class="sy0">.</span><span class="st_h">'.sort_key'</span><span class="br0">&#41;</span> <span class="sy1">?></span>&sort_type='.($sort[1] == 'asc' ? 'desc' : 'asc'))) ?]<br /> &nbsp; [?php if('<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">configuration</span><span class="sy0">-></span><span class="me1">getValue</span><span class="br0">&#40;</span><span class="st_h">'list.fields.'</span><span class="sy0">.</span><span class="re0">$name</span><span class="sy0">.</span><span class="st_h">'.sort_key'</span><span class="br0">&#41;</span> <span class="sy1">?></span>' == $sort[0]): ?]<br /> &nbsp; &nbsp;[?php echo image_tag(sfConfig::get('sf_admin_module_web_dir').'/images/'.$sort[1].'.png', array('alt' => __($sort[1], array(), 'sf_admin'), 'title' => __($sort[1], array(), 'sf_admin'))) ?]<br /> &nbsp; [?php endif; ?]<br /> &nbsp;<span class="kw2"><?php</span> <span class="kw1">else</span><span class="sy0">:</span> <span class="sy1">?></span><br /> &nbsp; [?php echo __('<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$field</span><span class="sy0">-></span><span class="me1">getConfig</span><span class="br0">&#40;</span><span class="st_h">'label'</span><span class="sy0">,</span> <span class="st_h">''</span><span class="sy0">,</span> <span class="kw4">true</span><span class="br0">&#41;</span> <span class="sy1">?></span>', array(), '<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">getI18nCatalogue</span><span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="sy1">?></span>') ?]<br /> &nbsp;<span class="kw2"><?php</span> <span class="kw1">endif</span><span class="sy1">?></span><br /> <span class="kw2"><?php</span> <span class="kw1">endif</span><span class="sy0">;</span> <span class="sy1">?></span>
    </div>
  </div>
  
  <p>
    Que venons-nous de faire ? Nous venons simplement de modifier le code pour qu&#8217;un lien soit généré lorsqu&#8217;une clé &#8220;sort_key&#8221; est déclarée sur les champs virtuels dans le fichier de configuration generator.yml :
  </p>
  
  <div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="php codecolorer">
      <span class="kw2"><?php</span> <span class="kw1">if</span><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">configuration</span><span class="sy0">-></span><span class="me1">getValue</span><span class="br0">&#40;</span><span class="st_h">'list.fields.'</span><span class="sy0">.</span><span class="re0">$name</span><span class="sy0">.</span><span class="st_h">'.sort_key'</span><span class="br0">&#41;</span><span class="br0">&#41;</span> <span class="sy0">:</span> <span class="sy1">?></span>
    </div>
  </div>
  
  <p>
    A présent, modifions le fichier generator.yml pour indiquer à l&#8217;Admin Generator que le champ virtuel &#8220;Category&#8221; doit faire l&#8217;objet d&#8217;un tri :
  </p>
  
  <div class="codecolorer-container yaml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="yaml codecolorer">
      &nbsp;<span class="co1"># apps/backend/modules/product/config/generator.yml</span><br /> <span class="co1"># ...</span><span class="co4"><br /> &nbsp; &nbsp; config</span>:<span class="co3"><br /> &nbsp; &nbsp; &nbsp; actions</span><span class="sy2">: </span>~<span class="co3"><br /> &nbsp; &nbsp; &nbsp; fields</span><span class="sy2">: </span> ~<span class="co4"><br /> &nbsp; &nbsp; &nbsp; list</span>:<span class="co3"><br /> &nbsp; &nbsp; &nbsp; &nbsp; display</span><span class="sy2">: </span><span class="br0">&#91;</span> =name, Category, price <span class="br0">&#93;</span><span class="co4"><br /> &nbsp; &nbsp; &nbsp; &nbsp; fields</span>:<span class="co4"><br /> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; Category</span>:<span class="co3"><br /> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; sort_key</span><span class="sy2">: </span>cat.name<br /> <span class="co1"># Le reste sans changement</span>
    </div>
  </div>
  
  <p>
    Si nous rechargeons la page du Backend, nous pouvons constater que l&#8217;en-tête de colonne &#8220;Category&#8221; se présente désormais sous la forme d&#8217;un lien hypertexte qui ajoute à l&#8217;URL un paramètre &#8220;sort_key&#8221; ayant pour valeur &#8220;cat.name&#8221;. Il ne nous reste plus qu&#8217;à modifier le code des actions et du modèle pour rendre notre lien fonctionnel. Pour commencer, nous allons créer une méthode dans la classe ProductTable permettant de rapatrier les objets de type &#8220;Product&#8221; avec les jointures nécessaires sur la table &#8220;Category&#8221; et c&#8217;est celle-ci que nous utiliserons dans le Backoffice pour afficher la liste des produits :
  </p>
  
  <div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="php codecolorer">
      <span class="co2">#lib/model/doctrine/ProductTable.class.php<br /> </span><span class="kw2">class</span> ProductTable <span class="kw2">extends</span> Doctrine_Table<br /> <span class="br0">&#123;</span><br /> &nbsp; <span class="kw2">public</span> <span class="kw2">function</span> retrieveProductsForBackend<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> &nbsp; <span class="br0">&#123;</span><br /> &nbsp; &nbsp; <span class="kw1">return</span> Doctrine_Query<span class="sy0">::</span><span class="me2">create</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">-></span><span class="me1">from</span><span class="br0">&#40;</span><span class="st_h">'Product p'</span><span class="br0">&#41;</span><span class="sy0">-></span><span class="me1">innerJoin</span><span class="br0">&#40;</span><span class="st_h">'p.Category cat'</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> &nbsp; <span class="br0">&#125;</span><br /> <span class="br0">&#125;</span>
    </div>
  </div>
  
  <p>
    Noter que l&#8217;alias défini sur Category (&#8216;cat&#8217;) doit correspondre à celui mentionné dans le fichier generator.yml (cat.name). A présent, nous allons modifier le fichier generator.yml pour que notre méthode soit utilisée dans le Backend :
  </p>
  
  <div class="codecolorer-container yaml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="yaml codecolorer">
      <span class="co1">#apps/backend/modules/product/config/generator.yml</span><span class="co4"><br /> &nbsp; &nbsp; &nbsp; list</span>:<span class="co3"><br /> &nbsp; &nbsp; &nbsp; &nbsp; display</span><span class="sy2">: </span><span class="br0">&#91;</span> =name, Category, price <span class="br0">&#93;</span><span class="co4"><br /> &nbsp; &nbsp; &nbsp; &nbsp; fields</span>:<span class="co4"><br /> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; Category</span>:<span class="co3"><br /> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; sort_key</span><span class="sy2">: </span>cat.name<span class="co3"><br /> &nbsp; &nbsp; &nbsp; &nbsp; table_method</span><span class="sy2">: </span>retrieveProductsForBackend
    </div>
  </div>
  
  <p>
    Rechargeons la liste des produits dans notre navigateur. Vérifions grâce à la Web Debug Toolbar que c&#8217;est bien notre méthode qui est appelée :
  </p>
  
  <div class="codecolorer-container sql vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="sql codecolorer">
      <span class="kw1">SELECT</span> <span class="kw1">COUNT</span><span class="br0">&#40;</span><span class="sy0">*</span><span class="br0">&#41;</span> <span class="kw1">AS</span> num_results <span class="kw1">FROM</span> <span class="br0">&#40;</span><span class="kw1">SELECT</span> &nbsp;p<span class="sy0">.</span>id <span class="kw1">FROM</span> product p <span class="kw1">INNER</span> <span class="kw1">JOIN</span> category c <span class="kw1">ON</span> p<span class="sy0">.</span>category_id <span class="sy0">=</span> c<span class="sy0">.</span>id <span class="kw1">GROUP</span> <span class="kw1">BY</span> p<span class="sy0">.</span>id<span class="br0">&#41;</span>
    </div>
  </div>
  
  <p>
    OK, tout est correct, la jointure sur la table Category est bien présente. Maintenant, testons notre lien pour voir si le tri est opérationnel. Et là, c&#8217;est la déception ! Les produits demeurent désespérément triés par nom, notre tri ne fonctionne pas ! En effet, il nous reste une petite modification à apporter. Par défaut, l&#8217;Admin Generator vérifie que l&#8217;on a bien affaire à un champ natif pour appliquer un tri. Nous allons supprimer ce comportement par défaut. Pour cela, il faut modifier le fichier <span style="color: #3366ff;">data/generator/sfDoctrineModule/admin/parts/sortingAction.php</span> :
  </p>
  
  <div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="php codecolorer">
      &nbsp; protected function isValidSortColumn($column)<br /> &nbsp; {<br /> &nbsp; &nbsp; return Doctrine::getTable('<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">getModelClass</span><span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="sy1">?></span>')->hasColumn($column);<br /> &nbsp; }
    </div>
  </div>
  
  <p>
    Remplacer le code ci-dessus par celui-ci :
  </p>
  
  <div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="php codecolorer">
      &nbsp; protected function isValidSortColumn($column)<br /> &nbsp; {<br /> &nbsp; &nbsp; $configVal=false;<br /> &nbsp; &nbsp; foreach($this->configuration->getValue(‘list.fields’) as $field => $value)<br /> &nbsp; &nbsp; {<br /> &nbsp; &nbsp; &nbsp; &nbsp;if($this->configuration->getValue(‘list.fields.’.$field.’.sort_key’,null)==$column)<br /> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;$configVal=true;<br /> &nbsp; &nbsp; }<br /> &nbsp; &nbsp; return (Doctrine::getTable('<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">getModelClass</span><span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="sy1">?></span>')->hasColumn($column) || $configVal) ;<br /> &nbsp; }
    </div>
  </div>
  
  <p>
    Voilà ! Le tri sur notre colonne &#8220;Category&#8221; est fonctionnel.
  </p>
  
  <p>
    Nous avons obtenu le résultat que nous souhaitions, mais il existe peut-être d&#8217;autres manières de procéder. Si vous avez des suggestions ou des remarques, n&#8217;hésitez pas à poster un commentaire ! Ca nous fera plaisir et ça fera sans doute avancer le schmilblick &#8230;
  </p>