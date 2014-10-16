
<p style="font-family: georgia, palatino;">
  Bonjour,
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  Je vous propose une méthode permettant d'effectuer des tris sur les colonnes étrangères dans les modules générés grâce à l'Admin Generator de Symfony. Ce n'est sans doute pas la seule manière de procéder, aussi, toute suggestion ou critique constructive est la bienvenue !
</p>

<p style="text-align: justify;">
  <span style="font-family: Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif; line-height: 19px; white-space: normal; font-size: 13px;"> </span>
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  L'exemple qui suit a été réalisé avec Symfony dans sa version 1.4 et l'ORM Doctrine. Il met en oeuvre deux entités reliées par une association de type 1:n :
</p>

- l'objet du modèle <span style="color: #3366ff;"><span style="font-family: georgia, palatino;">Produit</span></span><span style="font-family: georgia, palatino;"> qui appartient à une catégorie,<br /> - l'objet du modèle </span><span style="color: #3366ff;"><span style="font-family: georgia, palatino;">Catégorie</span></span><span style="font-family: georgia, palatino;"> qui contient plusieurs produits.</span>

<p style="font-family: georgia, palatino;">
  Le schéma de la base de données, volontairement simplifié, est le suivant :
</p>

<div class="codecolorer-container yaml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="yaml codecolorer">
    <span class="co4">Product</span>:<span class="co4"><br />   columns</span>:<span class="co3"><br />     id</span><span class="sy2">: </span>            <span class="br0">&#123;</span> type<span class="sy2">: </span>integer<span class="br0">&#40;</span><span class="nu0">4</span><span class="br0">&#41;</span>, primary<span class="sy2">: </span>true, autoincrement<span class="sy2">: </span>true <span class="br0">&#125;</span><span class="co3"><br />     name</span><span class="sy2">: </span>          <span class="br0">&#123;</span> type<span class="sy2">: </span>string<span class="br0">&#40;</span><span class="nu0">255</span><span class="br0">&#41;</span>, notnull<span class="sy2">: </span>true <span class="br0">&#125;</span><span class="co3"><br />     category_id</span><span class="sy2">: </span>   <span class="br0">&#123;</span> type<span class="sy2">: </span>integer<span class="br0">&#40;</span><span class="nu0">4</span><span class="br0">&#41;</span>, notnull<span class="sy2">: </span>true <span class="br0">&#125;</span><span class="co3"><br />     price</span><span class="sy2">: </span>         <span class="br0">&#123;</span> type<span class="sy2">: </span>decimal, notnull<span class="sy2">: </span>true <span class="br0">&#125;</span><span class="co4"><br />   relations</span>:<span class="co3"><br />     Category</span><span class="sy2">: </span>      <span class="br0">&#123;</span> local<span class="sy2">: </span>category_id, foreign<span class="sy2">: </span>id, foreignAlias<span class="sy2">: </span>Products, onDelete<span class="sy2">: </span>CASCADE <span class="br0">&#125;</span><br /> <span class="co4"><br /> Category</span>:<span class="co4"><br />   columns</span>:<span class="co3"><br />     id</span><span class="sy2">: </span>            <span class="br0">&#123;</span> type<span class="sy2">: </span>integer<span class="br0">&#40;</span><span class="nu0">4</span><span class="br0">&#41;</span>, primary<span class="sy2">: </span>true, autoincrement<span class="sy2">: </span>true <span class="br0">&#125;</span><span class="co3"><br />     name</span><span class="sy2">: </span>          <span class="br0">&#123;</span> type<span class="sy2">: </span>string<span class="br0">&#40;</span><span class="nu0">100</span><span class="br0">&#41;</span>, notnull<span class="sy2">: </span>true <span class="br0">&#125;</span>
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  Nous partons du principe que vous disposez d'un projet Symfony "blanc" déjà créé et que la connexion à la base de données a déjà été configurée. Si ce n'est pas le cas, je vous invite à <a title="installer Symfony" href="http://www.symfony-project.org/getting-started/1_4/en/03-Symfony-Installation" target="_blank">installer Symfony</a> et <a title="créer un projet Symfony" href="http://www.symfony-project.org/getting-started/1_4/en/04-Project-Setup" target="_blank">créer votre premier projet</a> selon la méthode préconisée par les créateurs de Symfony. Pour les plus pressés d'entre vous, vous avez la possibilité de télécharger une <a title="Sandbox Symfony" href="http://www.symfony-project.org/get/sf_sandbox_1_4.zip" target="_blank">sandbox Symfony</a> prête à l'emploi.
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  Commençons par construire les classes de notre modèle ainsi que notre base de données, créer une application Backend, puis à l'aide de l'Admin Generator, générer le module associé à notre OM "Product". Pour cela, ouvrons une ligne de commande, plaçons-nous à la racine de notre projet et lançons successivement les commandes symfony suivantes :
</p>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
      .<span class="sy0">/</span>symfony doctrine:build <span class="re5">--all</span><br />   .<span class="sy0">/</span>symfony generate:app backend<br />   .<span class="sy0">/</span>symfony doctrine:generate-admin backend Product<br />   .<span class="sy0">/</span>symfony <span class="kw2">cc</span>
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  A présent, à l'aide de notre navigateur préféré, rendons-nous à l'URL <span style="color: #3366ff;">http://mon_hote_virtuel/backend_dev.php/product</span> (remplacez <em>mon_hote_virtuel </em>par celui que vous avez défini !) et admirons le résultat :
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  <img class="aligncenter size-medium wp-image-688" title="sf14_tris_fk_1" src="/blog/wp-content/uploads/2010/07/sf14_tris_fk_1.png" alt="sf14 tris fk 1 Symfony 1.4   Admin Generator   Tris sur les colonnes étrangères" width="75%" />
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  Bon, je vous le concède, il n'y a pas de quoi s'enthousiasmer, nous pourrions améliorer l'apparence de notre page et surtout, tout cela manque un peu de matière ... Nous allons donc injecter quelques données dans la base. Concernant le style de la page, je vous fais confiance pour améliorer tout cela à l'aide d'une CSS ; pour ma part, je ne m'y risquerai pas de peur de faire étalage de mon manque flagrant de talent graphique ...
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  Pour alimenter la base de données, voici le jeu de fixtures que nous allons charger :
</p>

<div class="codecolorer-container yaml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="yaml codecolorer">
    <span class="co1"># data/fixtures/fixtures.yml</span><span class="co4"><br /> Category</span>:<span class="co4"><br />   Category_1</span>:<span class="co3"><br />     id</span><span class="sy2">: </span><span class="nu0">1</span><span class="co3"><br />     name</span><span class="sy2">: </span>Categorie <span class="nu0">1</span><span class="co4"><br />   Category_2</span>:<span class="co3"><br />     id</span><span class="sy2">: </span><span class="nu0">2</span><span class="co3"><br />     name</span><span class="sy2">: </span>Categorie <span class="nu0">2</span><br /> <span class="co4"><br /> Product</span>:<span class="co3"><br /> <?php for($i = 1 ; $i <= 10 ; $i++) </span><span class="sy2">: </span>?><span class="co4"><br />   Produit_<?php echo $i ?></span>:<span class="co3"><br />     id</span><span class="sy2">: </span><?php echo $i.<span class="st0">"\\n"</span> ?><span class="co3"><br />     name</span><span class="sy2">: </span>Produit_<?php echo $i.<span class="st0">"\\n"</span> ?><span class="co3"><br />     price</span><span class="sy2">: </span><?php echo <span class="br0">&#40;</span>$i * <span class="nu0">1.0</span><span class="br0">&#41;</span>.<span class="st0">"\\n"</span> ?><span class="co3"><br />     Category</span><span class="sy2">: </span>Category_<?php echo <span class="br0">&#40;</span>$i <span class="co2">% 2 == 0) ? 1 : 2 ?><?php echo "\\n" ?></span><br /> <?php endfor ?>
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  Pour injecter les données en base, lançons la commande adéquate ...
</p>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    .<span class="sy0">/</span>symfony doctrine:data-load
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  ... et émerveillons-nous devant le résultat ainsi obtenu :
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  <img class="aligncenter size-medium wp-image-696" title="sf14_tris_fk_2" src="/blog/wp-content/uploads/2010/07/sf14_tris_fk_2.png" alt="sf14 tris fk 2 Symfony 1.4   Admin Generator   Tris sur les colonnes étrangères" height="50%" />
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  Nous allons à présent modifier le fichier generator.yml (apps/backend/modules/product/config) pour afficher l'intitulé de la catégorie plutôt que son identifiant :
</p>

<div class="codecolorer-container yaml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="yaml codecolorer">
    <span class="co4">    config</span>:<span class="co3"><br />       actions</span><span class="sy2">: </span>~<span class="co3"><br />       fields</span><span class="sy2">: </span> ~<span class="co4"><br />       list</span>:<span class="co3"><br />         display</span><span class="sy2">: </span><span class="br0">&#91;</span> =name, Category, price <span class="br0">&#93;</span><span class="co3"><br />       filter</span><span class="sy2">: </span> ~<span class="co3"><br />       form</span><span class="sy2">: </span>   ~<span class="co3"><br />       edit</span><span class="sy2">: </span>   ~<span class="co3"><br />       new</span><span class="sy2">: </span>    ~
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  <img class="aligncenter size-medium wp-image-696" title="sf14_tris_fk_3" src="/blog/wp-content/uploads/2010/07/sf14_tris_fk_3.png" alt="sf14 tris fk 3 Symfony 1.4   Admin Generator   Tris sur les colonnes étrangères" height="50%" />
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  Vous remarquerez que les en-têtes de colonnes des champs natifs de la table "Product" permettent de trier le contenu de la colonne mais ça n'est pas le cas de la colonne "Category". Cela étant, vu le titre de cet article, cela ne devrait pas réellement vous étonner ... Car nous allons justement voir une manière d'activer les tris sur ces colonnes étrangères. Et pour cela, nous allons surcharger le thème de l'Admin Generator, de manière notamment à bénéficier de cette fonctionnalité sur tous les modules que nous créerons.
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  Comme nous utilisons l'ORM Doctrine, le thème sur lequel s'appuie l'Admin Generator se trouve dans la distribution de Symfony, dans le sous-répertoire lib/plugins/sfDoctrinePlugin/data/generator/sfDoctrineModule :
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  <img class="aligncenter size-medium wp-image-696" title="Themes de l'admin generator de Doctrine" src="/blog/wp-content/uploads/2010/07/sf14_tris_fk_4.png" alt="sf14 tris fk 4 Symfony 1.4   Admin Generator   Tris sur les colonnes étrangères" height="25%" />
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  Nous constatons qu'il existe deux thèmes dans ce sous-répertoire : "admin" et "default". Lequel des deux devons-nous surcharger ? Celui que nous utilisons actuellement ! Et pour connaître ce thème, il suffit de consulter le fichier generator.yml de notre module "Product", qui nous informe que le thème utilisé est "admin" :
</p>

<div class="codecolorer-container yaml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="yaml codecolorer">
    <span class="co4">generator</span>:<span class="co3"><br />   class</span><span class="sy2">: </span>sfDoctrineGenerator<span class="co4"><br />   param</span>:<span class="co3"><br />     model_class</span><span class="sy2">: </span>          Product<span class="co3"><br />     theme</span><span class="sy2">: </span>                 admin  <span class="co1"># Le thème utilisé par l'admin generator ; autre possibilité : "default"</span>
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  Pour pouvoir surcharger le thème, il va nous falloir copier le contenu du sous-répertoire "admin" du plugin Doctrine (<em>ie</em> les trois sous-dossiers <strong>parts</strong>, <strong>skeleton</strong> et <strong>template</strong>) dans le dossier <span style="color: #3366ff;"><strong>data/generator/sfDoctrineModule/admin</strong></span> de notre projet Symfony (ce dossier n'existe pas, nous devons le créer manuellement) afin d'obtenir le résultat suivant :
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  <img class="aligncenter size-medium wp-image-696" title="Theme admin generator surchargé" src="/blog/wp-content/uploads/2010/07/sf14_tris_fk_5.png" alt="sf14 tris fk 5 Symfony 1.4   Admin Generator   Tris sur les colonnes étrangères" height="20%" />
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  <strong>Attention</strong> : si vous avez installé Symfony depuis un gestionnaire de sources, prenez soin de copier ces trois répertoires en excluant les dossiers de versioning (.svn, .git ...) :
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  On copie les répertoires dans un dossier temporaire (SYMFONY_DIR = le répertoire où vous avez placé votre distribution Symfony).
</p>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
     <span class="kw2">cp</span> <span class="re5">-r</span> SYMFONY_DIR<span class="sy0">/</span>lib<span class="sy0">/</span>plugins<span class="sy0">/</span>sfDoctrinePlugin<span class="sy0">/</span>data<span class="sy0">/</span>generator<span class="sy0">/</span>sfDoctrineModule<span class="sy0">/</span>admin ~<span class="sy0">/</span>Temp<span class="sy0">/</span>admin<br />  <span class="kw3">cd</span> ~<span class="sy0">/</span>Temp<span class="sy0">/</span>admin
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  On supprime les répertoires .svn récursivement
</p>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
     <span class="kw2">rm</span> <span class="re5">-r</span> $<span class="br0">&#40;</span><span class="kw2">grep</span> <span class="re5">-lnr</span> <span class="st0">".svn"</span> <span class="sy0">*</span><span class="br0">&#41;</span>
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  On crée le répertoire /data/generator/sfDoctrineModule dans notre projet. Adaptez le chemin et le nom du projet en fonction de votre propre configuration !
</p>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
     <span class="kw3">cd</span> <span class="sy0">/</span>path_to_my_workspace<span class="sy0">/</span>my_project_name<span class="sy0">/</span>data<br />  <span class="kw2">mkdir</span> generator<br />  <span class="kw3">cd</span> generator<br />  <span class="kw2">mkdir</span> sfDoctrineModule
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  Copions le dossier admin que nous avions placé dans un répertoire temporaire dans le répertoire que nous venons de créer.
</p>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
     <span class="kw3">cd</span> sfDoctrineModule<br />  <span class="kw2">cp</span> <span class="re5">-r</span> ~<span class="sy0">/</span>Temp<span class="sy0">/</span>admin<span class="sy0">/</span> .<span class="sy0">/</span>admin
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  A présent, nous allons nous assurer que les fichiers du thème que nous venons de placer dans notre projet sont bien ceux qu'utilise l'Admin Generator. Pour cela, on va modifier un des fichiers du répertoire "templates" :
</p>

<div class="codecolorer-container html4strict vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="html4strict codecolorer">
       # data/generator/sfDoctrineModule/admin/template/templates/_list_header.php<br />    <span class="sc2"><<a href="http://december.com/html/4/element/h1.html"><span class="kw2">h1</span></a>></span>Une template que j'ai surchargée !<span class="sc2"><<span class="sy0">/</span><a href="http://december.com/html/4/element/h1.html"><span class="kw2">h1</span></a>></span>
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  Rechargeons la page du Backend listant les produits et constatons le résultat : notre modification a bien été prise en compte. Si ce n'est pas le cas, l'incontournable <em>symfony cc</em> devrait résoudre le problème ! Maintenant que nous savons comment surcharger un thème, nous pouvons le redéfinir afin d'inclure le tri sur les clés étrangères. Par ailleurs, en modifiant le partial _list_header.php, vous aurez sans doute remarqué le découpage des différentes vues et les partiels utilisés par l'Admin Generator ; les noms des fichiers sont suffisamment explicites pour comprendre à quoi chacun de ces fichiers correspond. En l'occurrence, nous souhaitons modifier les en-têtes de colonnes afin d'ajouter notre fameux tri sur les colonnes étrangères et nous nous doutons bien qu'il va falloir pour cela modifier le partial "_list_th_tabular.php". Voici le contenu de ce fichier :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    #data/generator/sfDoctrineModule/admin/template/templates/_list_th_tabular.php<br /> <span class="kw2"><?php</span> <span class="kw1">foreach</span> <span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">configuration</span><span class="sy0">-></span><span class="me1">getValue</span><span class="br0">&#40;</span><span class="st_h">'list.display'</span><span class="br0">&#41;</span> <span class="kw1">as</span> <span class="re0">$name</span> <span class="sy0">=></span> <span class="re0">$field</span><span class="br0">&#41;</span><span class="sy0">:</span> <span class="sy1">?></span><br /> [?php slot('sf_admin.current_header') ?]<br /> <th class="sf_admin_<span class="kw2"><?php</span> <span class="kw1">echo</span> <a href="http://www.php.net/strtolower"><span class="kw3">strtolower</span></a><span class="br0">&#40;</span><span class="re0">$field</span><span class="sy0">-></span><a href="http://www.php.net/gettype"><span class="kw3">getType</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span> <span class="sy1">?></span> sf_admin_list_th_<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$name</span> <span class="sy1">?></span>"><br /> <span class="kw2"><?php</span> <span class="kw1">if</span> <span class="br0">&#40;</span><span class="re0">$field</span><span class="sy0">-></span><span class="me1">isReal</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">:</span> <span class="sy1">?></span><br />   [?php if ('<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$name</span> <span class="sy1">?></span>' == $sort[0]): ?]<br />     [?php echo link_to(__('<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$field</span><span class="sy0">-></span><span class="me1">getConfig</span><span class="br0">&#40;</span><span class="st_h">'label'</span><span class="sy0">,</span> <span class="st_h">''</span><span class="sy0">,</span> <span class="kw4">true</span><span class="br0">&#41;</span> <span class="sy1">?></span>', array(), '<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">getI18nCatalogue</span><span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="sy1">?></span>'), '@<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">getUrlForAction</span><span class="br0">&#40;</span><span class="st_h">'list'</span><span class="br0">&#41;</span> <span class="sy1">?></span>', array('query_string' => 'sort=<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$name</span> <span class="sy1">?></span>&sort_type='.($sort[1] == 'asc' ? 'desc' : 'asc'))) ?]<br />     [?php echo image_tag(sfConfig::get('sf_admin_module_web_dir').'/images/'.$sort[1].'.png', array('alt' => __($sort[1], array(), 'sf_admin'), 'title' => __($sort[1], array(), 'sf_admin'))) ?]<br />   [?php else: ?]<br />     [?php echo link_to(__('<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$field</span><span class="sy0">-></span><span class="me1">getConfig</span><span class="br0">&#40;</span><span class="st_h">'label'</span><span class="sy0">,</span> <span class="st_h">''</span><span class="sy0">,</span> <span class="kw4">true</span><span class="br0">&#41;</span> <span class="sy1">?></span>', array(), '<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">getI18nCatalogue</span><span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="sy1">?></span>'), '@<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">getUrlForAction</span><span class="br0">&#40;</span><span class="st_h">'list'</span><span class="br0">&#41;</span> <span class="sy1">?></span>', array('query_string' => 'sort=<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$name</span> <span class="sy1">?></span>&sort_type=asc')) ?]<br />   [?php endif; ?]<br /> <span class="kw2"><?php</span> <span class="kw1">else</span><span class="sy0">:</span> <span class="sy1">?></span><br />   [?php echo __('<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$field</span><span class="sy0">-></span><span class="me1">getConfig</span><span class="br0">&#40;</span><span class="st_h">'label'</span><span class="sy0">,</span> <span class="st_h">''</span><span class="sy0">,</span> <span class="kw4">true</span><span class="br0">&#41;</span> <span class="sy1">?></span>', array(), '<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">getI18nCatalogue</span><span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="sy1">?></span>') ?]<br /> <span class="kw2"><?php</span> <span class="kw1">endif</span><span class="sy0">;</span> <span class="sy1">?></span><br /> </th><br /> [?php end_slot(); ?]<br /> <span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">addCredentialCondition</span><span class="br0">&#40;</span><span class="st0">"[?php include_slot('sf_admin.current_header') ?]"</span><span class="sy0">,</span> <span class="re0">$field</span><span class="sy0">-></span><span class="me1">getConfig</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span> <span class="sy1">?></span><br /> <span class="kw2"><?php</span> <span class="kw1">endforeach</span><span class="sy0">;</span> <span class="sy1">?></span>
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  Avouez que le code est un petit peu intimidant ... On note la présence de balises <?php ?> comme nous avons l'habitude d'en rencontrer mais également des pseudo-balises du type [?php ... ?]. L'Admin Generator va parser ce fichier une première fois afin de générer un fichier PHP et le placer dans le cache. Les balises

  <?php vont donc dans un premier temps être interprétées afin de générer notre template en se basant sur la configuration du module, puis les balises [?php ?] vont être remplacées par de véritables balises PHP afin d'obtenir un code exécutable par l'interprétateur, une fois placé dans le cache. Jolie gymnastique intellectuelle, tout de même ! Il va donc nous falloir modifier ce fichier pour obtenir le résultat escompté. Un conseil : on peut aisément se perdre et s'embrouiller lorsque l'on plonge les mains dans ce code, entre les instructions destinées à être parsées par l'Admin Generator et celles qui seront interprétées par PHP lorsque notre application s'exécutera. Si vous vous sentez perdus, n'hésitez pas à consulter le fichier généré dans le cache (résultat obtenu après traitement par le générateur de code de Doctrine) pour y voir un peu plus clair. D'ailleurs, voici le contenu du fichier PHP généré et placé dans le cache :</p>

  <div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="php codecolorer">
      #cache/dev/backend/modules/autoProduct/templates/_list_th_tabular.php<br /> <span class="kw2"><?php</span> slot<span class="br0">&#40;</span><span class="st_h">'sf_admin.current_header'</span><span class="br0">&#41;</span> <span class="sy1">?></span><br /> <th class="sf_admin_text sf_admin_list_th_name"><br />   <span class="kw2"><?php</span> <span class="kw1">if</span> <span class="br0">&#40;</span><span class="st_h">'name'</span> <span class="sy0">==</span> <span class="re0">$sort</span><span class="br0">&#91;</span><span class="nu0"></span><span class="br0">&#93;</span><span class="br0">&#41;</span><span class="sy0">:</span> <span class="sy1">?></span><br />     <span class="kw2"><?php</span> <span class="kw1">echo</span> link_to<span class="br0">&#40;</span>__<span class="br0">&#40;</span><span class="st_h">'Name'</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">,</span> <span class="st_h">'messages'</span><span class="br0">&#41;</span><span class="sy0">,</span> <span class="st_h">'@product'</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="st_h">'query_string'</span> <span class="sy0">=></span> <span class="st_h">'sort=name&sort_type='</span><span class="sy0">.</span><span class="br0">&#40;</span><span class="re0">$sort</span><span class="br0">&#91;</span><span class="nu0">1</span><span class="br0">&#93;</span> <span class="sy0">==</span> <span class="st_h">'asc'</span> ? <span class="st_h">'desc'</span> <span class="sy0">:</span> <span class="st_h">'asc'</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="br0">&#41;</span> <span class="sy1">?></span><br />     <span class="kw2"><?php</span> <span class="kw1">echo</span> image_tag<span class="br0">&#40;</span>sfConfig<span class="sy0">::</span><span class="me2">get</span><span class="br0">&#40;</span><span class="st_h">'sf_admin_module_web_dir'</span><span class="br0">&#41;</span><span class="sy0">.</span><span class="st_h">'/images/'</span><span class="sy0">.</span><span class="re0">$sort</span><span class="br0">&#91;</span><span class="nu0">1</span><span class="br0">&#93;</span><span class="sy0">.</span><span class="st_h">'.png'</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="st_h">'alt'</span> <span class="sy0">=></span> __<span class="br0">&#40;</span><span class="re0">$sort</span><span class="br0">&#91;</span><span class="nu0">1</span><span class="br0">&#93;</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">,</span> <span class="st_h">'sf_admin'</span><span class="br0">&#41;</span><span class="sy0">,</span> <span class="st_h">'title'</span> <span class="sy0">=></span> __<span class="br0">&#40;</span><span class="re0">$sort</span><span class="br0">&#91;</span><span class="nu0">1</span><span class="br0">&#93;</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">,</span> <span class="st_h">'sf_admin'</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="br0">&#41;</span> <span class="sy1">?></span><br />   <span class="kw2"><?php</span> <span class="kw1">else</span><span class="sy0">:</span> <span class="sy1">?></span><br />     <span class="kw2"><?php</span> <span class="kw1">echo</span> link_to<span class="br0">&#40;</span>__<span class="br0">&#40;</span><span class="st_h">'Name'</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">,</span> <span class="st_h">'messages'</span><span class="br0">&#41;</span><span class="sy0">,</span> <span class="st_h">'@product'</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="st_h">'query_string'</span> <span class="sy0">=></span> <span class="st_h">'sort=name&sort_type=asc'</span><span class="br0">&#41;</span><span class="br0">&#41;</span> <span class="sy1">?></span><br />   <span class="kw2"><?php</span> <span class="kw1">endif</span><span class="sy0">;</span> <span class="sy1">?></span><br /> </th><br /> <span class="kw2"><?php</span> end_slot<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span> <span class="sy1">?></span><br /> <span class="kw2"><?php</span> include_slot<span class="br0">&#40;</span><span class="st_h">'sf_admin.current_header'</span><span class="br0">&#41;</span> <span class="sy1">?></span><span class="kw2"><?php</span> slot<span class="br0">&#40;</span><span class="st_h">'sf_admin.current_header'</span><span class="br0">&#41;</span> <span class="sy1">?></span><br /> <th class="sf_admin_text sf_admin_list_th_Category"><br />   <span class="kw2"><?php</span> <span class="kw1">echo</span> __<span class="br0">&#40;</span><span class="st_h">'Category'</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">,</span> <span class="st_h">'messages'</span><span class="br0">&#41;</span> <span class="sy1">?></span><br /> </th><br /> <span class="kw2"><?php</span> end_slot<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span> <span class="sy1">?></span><br /> <span class="kw2"><?php</span> include_slot<span class="br0">&#40;</span><span class="st_h">'sf_admin.current_header'</span><span class="br0">&#41;</span> <span class="sy1">?></span><span class="kw2"><?php</span> slot<span class="br0">&#40;</span><span class="st_h">'sf_admin.current_header'</span><span class="br0">&#41;</span> <span class="sy1">?></span><br /> <th class="sf_admin_text sf_admin_list_th_price"><br />   <span class="kw2"><?php</span> <span class="kw1">if</span> <span class="br0">&#40;</span><span class="st_h">'price'</span> <span class="sy0">==</span> <span class="re0">$sort</span><span class="br0">&#91;</span><span class="nu0"></span><span class="br0">&#93;</span><span class="br0">&#41;</span><span class="sy0">:</span> <span class="sy1">?></span><br />     <span class="kw2"><?php</span> <span class="kw1">echo</span> link_to<span class="br0">&#40;</span>__<span class="br0">&#40;</span><span class="st_h">'Price'</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">,</span> <span class="st_h">'messages'</span><span class="br0">&#41;</span><span class="sy0">,</span> <span class="st_h">'@product'</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="st_h">'query_string'</span> <span class="sy0">=></span> <span class="st_h">'sort=price&sort_type='</span><span class="sy0">.</span><span class="br0">&#40;</span><span class="re0">$sort</span><span class="br0">&#91;</span><span class="nu0">1</span><span class="br0">&#93;</span> <span class="sy0">==</span> <span class="st_h">'asc'</span> ? <span class="st_h">'desc'</span> <span class="sy0">:</span> <span class="st_h">'asc'</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="br0">&#41;</span> <span class="sy1">?></span><br />     <span class="kw2"><?php</span> <span class="kw1">echo</span> image_tag<span class="br0">&#40;</span>sfConfig<span class="sy0">::</span><span class="me2">get</span><span class="br0">&#40;</span><span class="st_h">'sf_admin_module_web_dir'</span><span class="br0">&#41;</span><span class="sy0">.</span><span class="st_h">'/images/'</span><span class="sy0">.</span><span class="re0">$sort</span><span class="br0">&#91;</span><span class="nu0">1</span><span class="br0">&#93;</span><span class="sy0">.</span><span class="st_h">'.png'</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="st_h">'alt'</span> <span class="sy0">=></span> __<span class="br0">&#40;</span><span class="re0">$sort</span><span class="br0">&#91;</span><span class="nu0">1</span><span class="br0">&#93;</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">,</span> <span class="st_h">'sf_admin'</span><span class="br0">&#41;</span><span class="sy0">,</span> <span class="st_h">'title'</span> <span class="sy0">=></span> __<span class="br0">&#40;</span><span class="re0">$sort</span><span class="br0">&#91;</span><span class="nu0">1</span><span class="br0">&#93;</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">,</span> <span class="st_h">'sf_admin'</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="br0">&#41;</span> <span class="sy1">?></span><br />   <span class="kw2"><?php</span> <span class="kw1">else</span><span class="sy0">:</span> <span class="sy1">?></span><br />     <span class="kw2"><?php</span> <span class="kw1">echo</span> link_to<span class="br0">&#40;</span>__<span class="br0">&#40;</span><span class="st_h">'Price'</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">,</span> <span class="st_h">'messages'</span><span class="br0">&#41;</span><span class="sy0">,</span> <span class="st_h">'@product'</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="st_h">'query_string'</span> <span class="sy0">=></span> <span class="st_h">'sort=price&sort_type=asc'</span><span class="br0">&#41;</span><span class="br0">&#41;</span> <span class="sy1">?></span><br />   <span class="kw2"><?php</span> <span class="kw1">endif</span><span class="sy0">;</span> <span class="sy1">?></span><br /> </th><br /> <span class="kw2"><?php</span> end_slot<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span> <span class="sy1">?></span><br /> <span class="kw2"><?php</span> include_slot<span class="br0">&#40;</span><span class="st_h">'sf_admin.current_header'</span><span class="br0">&#41;</span> <span class="sy1">?></span>
    </div>
  </div>

  <p style="font-family: georgia, palatino; text-align: justify;">
    Ce code nous est tout de même un peu plus familier. Mais revenons à nos moutons ! Vous aurez sans doute noté dans le premier fichier la condition if ($field->isReal()) ; comme vous vous en doutez, c'est dans la clause "else" que nous allons gérer le cas des colonnes "virtuelles" afin de créer un lien permettant d'effectuer un tri sur ces colonnes.
  </p>

  <div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="php codecolorer">
      <span class="kw2"><?php</span> <span class="kw1">if</span> <span class="br0">&#40;</span><span class="re0">$field</span><span class="sy0">-></span><span class="me1">isReal</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">:</span> <span class="sy1">?></span><br />    # Si le champ est "réel" (comprendre "natif", "correspondant réellement à un champ de la table en BD")<br />    # Le code original reste inchangé<br />    # ....<br /> <span class="kw2"><?php</span> <span class="kw1">else</span> <span class="sy0">:</span> <span class="sy1">?></span><br />    # Nous allons ici créer un lien hypertexte permettant d'effectuer un tri sur les colonnes "virtuelles"<br /> <span class="kw2"><?php</span> <span class="kw1">endif</span> <span class="sy1">?></span>
    </div>
  </div>

  <p style="font-family: georgia, palatino; text-align: left;">
    Voici le code à placer entre les balises "<em>else</em>" et "<em>endif</em>" dans le fichier <span style="color: #3366ff;">/data/generator/sfDoctrineModule/admin/template/templates/_list_th_tabular.php <span style="color: #000000;"> </span></span>:
  </p>

  <div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="php codecolorer">
      <span class="kw2"><?php</span> <span class="kw1">else</span><span class="sy0">:</span> <span class="sy1">?></span><br />  <span class="kw2"><?php</span> <span class="kw1">if</span><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">configuration</span><span class="sy0">-></span><span class="me1">getValue</span><span class="br0">&#40;</span><span class="st_h">'list.fields.'</span><span class="sy0">.</span><span class="re0">$name</span><span class="sy0">.</span><span class="st_h">'.sort_key'</span><span class="br0">&#41;</span><span class="br0">&#41;</span> <span class="sy0">:</span> <span class="sy1">?></span><br />   [?php echo link_to(__('<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$field</span><span class="sy0">-></span><span class="me1">getConfig</span><span class="br0">&#40;</span><span class="st_h">'label'</span><span class="sy0">,</span> <span class="st_h">''</span><span class="sy0">,</span> <span class="kw4">true</span><span class="br0">&#41;</span> <span class="sy1">?></span>', array(), '<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">getI18nCatalogue</span><span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="sy1">?></span>'), '@<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">getUrlForAction</span><span class="br0">&#40;</span><span class="st_h">'list'</span><span class="br0">&#41;</span> <span class="sy1">?></span>', array('query_string' => 'sort=<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">configuration</span><span class="sy0">-></span><span class="me1">getValue</span><span class="br0">&#40;</span><span class="st_h">'list.fields.'</span><span class="sy0">.</span><span class="re0">$name</span><span class="sy0">.</span><span class="st_h">'.sort_key'</span><span class="br0">&#41;</span> <span class="sy1">?></span>&sort_type='.($sort[1] == 'asc' ? 'desc' : 'asc'))) ?]<br />   [?php if('<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">configuration</span><span class="sy0">-></span><span class="me1">getValue</span><span class="br0">&#40;</span><span class="st_h">'list.fields.'</span><span class="sy0">.</span><span class="re0">$name</span><span class="sy0">.</span><span class="st_h">'.sort_key'</span><span class="br0">&#41;</span> <span class="sy1">?></span>' == $sort[0]): ?]<br />    [?php echo image_tag(sfConfig::get('sf_admin_module_web_dir').'/images/'.$sort[1].'.png', array('alt' => __($sort[1], array(), 'sf_admin'), 'title' => __($sort[1], array(), 'sf_admin'))) ?]<br />   [?php endif; ?]<br />  <span class="kw2"><?php</span> <span class="kw1">else</span><span class="sy0">:</span> <span class="sy1">?></span><br />   [?php echo __('<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$field</span><span class="sy0">-></span><span class="me1">getConfig</span><span class="br0">&#40;</span><span class="st_h">'label'</span><span class="sy0">,</span> <span class="st_h">''</span><span class="sy0">,</span> <span class="kw4">true</span><span class="br0">&#41;</span> <span class="sy1">?></span>', array(), '<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">getI18nCatalogue</span><span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="sy1">?></span>') ?]<br />  <span class="kw2"><?php</span> <span class="kw1">endif</span><span class="sy1">?></span><br /> <span class="kw2"><?php</span> <span class="kw1">endif</span><span class="sy0">;</span> <span class="sy1">?></span>
    </div>
  </div>

  <p>
    Que venons-nous de faire ? Nous venons simplement de modifier le code pour qu'un lien soit généré lorsqu'une clé "sort_key" est déclarée sur les champs virtuels dans le fichier de configuration generator.yml :
  </p>

  <div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="php codecolorer">
      <span class="kw2"><?php</span> <span class="kw1">if</span><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">configuration</span><span class="sy0">-></span><span class="me1">getValue</span><span class="br0">&#40;</span><span class="st_h">'list.fields.'</span><span class="sy0">.</span><span class="re0">$name</span><span class="sy0">.</span><span class="st_h">'.sort_key'</span><span class="br0">&#41;</span><span class="br0">&#41;</span> <span class="sy0">:</span> <span class="sy1">?></span>
    </div>
  </div>

  <p>
    A présent, modifions le fichier generator.yml pour indiquer à l'Admin Generator que le champ virtuel "Category" doit faire l'objet d'un tri :
  </p>

  <div class="codecolorer-container yaml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="yaml codecolorer">
       <span class="co1"># apps/backend/modules/product/config/generator.yml</span><br /> <span class="co1"># ...</span><span class="co4"><br />     config</span>:<span class="co3"><br />       actions</span><span class="sy2">: </span>~<span class="co3"><br />       fields</span><span class="sy2">: </span> ~<span class="co4"><br />       list</span>:<span class="co3"><br />         display</span><span class="sy2">: </span><span class="br0">&#91;</span> =name, Category, price <span class="br0">&#93;</span><span class="co4"><br />         fields</span>:<span class="co4"><br />           Category</span>:<span class="co3"><br />             sort_key</span><span class="sy2">: </span>cat.name<br /> <span class="co1"># Le reste sans changement</span>
    </div>
  </div>

  <p>
    Si nous rechargeons la page du Backend, nous pouvons constater que l'en-tête de colonne "Category" se présente désormais sous la forme d'un lien hypertexte qui ajoute à l'URL un paramètre "sort_key" ayant pour valeur "cat.name". Il ne nous reste plus qu'à modifier le code des actions et du modèle pour rendre notre lien fonctionnel. Pour commencer, nous allons créer une méthode dans la classe ProductTable permettant de rapatrier les objets de type "Product" avec les jointures nécessaires sur la table "Category" et c'est celle-ci que nous utiliserons dans le Backoffice pour afficher la liste des produits :
  </p>

  <div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="php codecolorer">
      <span class="co2">#lib/model/doctrine/ProductTable.class.php<br /> </span><span class="kw2">class</span> ProductTable <span class="kw2">extends</span> Doctrine_Table<br /> <span class="br0">&#123;</span><br />   <span class="kw2">public</span> <span class="kw2">function</span> retrieveProductsForBackend<span class="br0">&#40;</span><span class="br0">&#41;</span><br />   <span class="br0">&#123;</span><br />     <span class="kw1">return</span> Doctrine_Query<span class="sy0">::</span><span class="me2">create</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">-></span><span class="me1">from</span><span class="br0">&#40;</span><span class="st_h">'Product p'</span><span class="br0">&#41;</span><span class="sy0">-></span><span class="me1">innerJoin</span><span class="br0">&#40;</span><span class="st_h">'p.Category cat'</span><span class="br0">&#41;</span> <span class="sy0">;</span><br />   <span class="br0">&#125;</span><br /> <span class="br0">&#125;</span>
    </div>
  </div>

  <p>
    Noter que l'alias défini sur Category (&#8216;cat') doit correspondre à celui mentionné dans le fichier generator.yml (cat.name). A présent, nous allons modifier le fichier generator.yml pour que notre méthode soit utilisée dans le Backend :
  </p>

  <div class="codecolorer-container yaml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="yaml codecolorer">
      <span class="co1">#apps/backend/modules/product/config/generator.yml</span><span class="co4"><br />       list</span>:<span class="co3"><br />         display</span><span class="sy2">: </span><span class="br0">&#91;</span> =name, Category, price <span class="br0">&#93;</span><span class="co4"><br />         fields</span>:<span class="co4"><br />           Category</span>:<span class="co3"><br />             sort_key</span><span class="sy2">: </span>cat.name<span class="co3"><br />         table_method</span><span class="sy2">: </span>retrieveProductsForBackend
    </div>
  </div>

  <p>
    Rechargeons la liste des produits dans notre navigateur. Vérifions grâce à la Web Debug Toolbar que c'est bien notre méthode qui est appelée :
  </p>

  <div class="codecolorer-container sql vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="sql codecolorer">
      <span class="kw1">SELECT</span> <span class="kw1">COUNT</span><span class="br0">&#40;</span><span class="sy0">*</span><span class="br0">&#41;</span> <span class="kw1">AS</span> num_results <span class="kw1">FROM</span> <span class="br0">&#40;</span><span class="kw1">SELECT</span>  p<span class="sy0">.</span>id <span class="kw1">FROM</span> product p <span class="kw1">INNER</span> <span class="kw1">JOIN</span> category c <span class="kw1">ON</span> p<span class="sy0">.</span>category_id <span class="sy0">=</span> c<span class="sy0">.</span>id <span class="kw1">GROUP</span> <span class="kw1">BY</span> p<span class="sy0">.</span>id<span class="br0">&#41;</span>
    </div>
  </div>

  <p>
    OK, tout est correct, la jointure sur la table Category est bien présente. Maintenant, testons notre lien pour voir si le tri est opérationnel. Et là, c'est la déception ! Les produits demeurent désespérément triés par nom, notre tri ne fonctionne pas ! En effet, il nous reste une petite modification à apporter. Par défaut, l'Admin Generator vérifie que l'on a bien affaire à un champ natif pour appliquer un tri. Nous allons supprimer ce comportement par défaut. Pour cela, il faut modifier le fichier <span style="color: #3366ff;">data/generator/sfDoctrineModule/admin/parts/sortingAction.php</span> :
  </p>

  <div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="php codecolorer">
        protected function isValidSortColumn($column)<br />   {<br />     return Doctrine::getTable('<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">getModelClass</span><span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="sy1">?></span>')->hasColumn($column);<br />   }
    </div>
  </div>

  <p>
    Remplacer le code ci-dessus par celui-ci :
  </p>

  <div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="php codecolorer">
        protected function isValidSortColumn($column)<br />   {<br />     $configVal=false;<br />     foreach($this->configuration->getValue(‘list.fields’) as $field => $value)<br />     {<br />        if($this->configuration->getValue(‘list.fields.’.$field.’.sort_key’,null)==$column)<br />            $configVal=true;<br />     }<br />     return (Doctrine::getTable('<span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">getModelClass</span><span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="sy1">?></span>')->hasColumn($column) || $configVal) ;<br />   }
    </div>
  </div>

  <p>
    Voilà ! Le tri sur notre colonne "Category" est fonctionnel.
  </p>

  <p>
    Nous avons obtenu le résultat que nous souhaitions, mais il existe peut-être d'autres manières de procéder. Si vous avez des suggestions ou des remarques, n'hésitez pas à poster un commentaire ! Ca nous fera plaisir et ça fera sans doute avancer le schmilblick ...
  </p>