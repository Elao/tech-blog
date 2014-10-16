
<p style="font-family: georgia, palatino;">
  Bonjour,
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  Nous allons voir aujourd'hui comment installer et utiliser le plugin <a href="http://www.symfony-project.org/plugins/sfImageTransformExtraPlugin" target="_blank">sfImageTransformExtraPlugin</a>.
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  Il s'agit d'un plugin très puissant permettant d'appliquer des traitements à des images au moyen de fichiers de configuration, sans qu'il soit donc nécessaire de polluer le code métier. Par ailleurs, il permet d'appliquer plusieurs traitements successifs de manière très intuitive. Il gère lui-même l'emplacement des fichiers générés et propose un système de cache réduisant les délais de transmission des images déjà générées.
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  Le sfImageTransformExtraPlugin est dépendant du plugin <a href="http://www.symfony-project.org/plugins/sfImageTransformPlugin" target="_blank">sfImageTransformPlugin</a> mais ne l'installez pas avant d'avoir lu la suite ! Pour faciliter la lecture et éviter toute confusion (les noms des deux plugins sont très proches), dans la suite de l'article je ferai référence au "plugin de base" pour sfImageTransformPlugin et au "plugin Extra" pour sfImageTransformExtraPlugin.
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  L'outil est donc très puissant mais sa documentation contient de nombreuses zones d'ombre qui ne facilitent absolument pas sa prise en main. Et les ennuis commencent dès l'installation ! D'où l'intérêt de cet article.
</p>

## L'installation des plugins ou le début <del>des emm...</del> de nos démarches

<div style="background-color: lightyellow; border: 1px solid lightgrey; padding: 10px; text-align: center; margin-bottom: 15px;">
  Le plugin sfImageTransformPlugin nécessite que GD ou ImageMagick soit installé sur votre machine.
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  En effet, au moment de la rédaction de ce billet, la version courante du plugin "Extra" est la version 1.0.5 qui requiert la version 1.3.0 ou 1.3.1 du plugin sfImageTransformPlugin. Or, la version courante de ce dernier est 1.4.0, mais elle est incompatible avec la dernière version du plugin "Extra". Si bien que si vous essayez d'installer le plugin "Extra" via la ligne de commande <em>plugin:install</em>, l'opération échouera car elle installera préalablement la dernière version de sfImageTransformPlugin, donc une version incompatible ! Vous me suivez ? Oui, je sais que ces histoires de versions ne sont pas passionnantes mais si vous souhaitez utiliser sfImageTransformExtraPlugin (et nous le recommandons !), mieux vaut être au courant de ces incompatibilités. Voici donc la démarche que nous préconisons :
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  Installer le plugin de base (sfImageTransformPlugin) en ligne de commande en prenant soin de préciser la version souhaitée :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
        <span class="sy0">./</span>symfony plugin<span class="sy0">:</span>install <span class="sy0">--</span>release<span class="sy0">=</span>1<span class="sy0">.</span>3<span class="sy0">.</span>1 sfImageTransformPlugin
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  Pour le plugin "Extra", il faut l'installer manuellement. Pour cela :
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  - rendez-vous sur <a title="Télécharger le plugin sfImageTransformExtraPlugin" href="http://www.symfony-project.org/plugins/sfImageTransformExtraPlugin" target="_blank">la page dédiée au plugin</a> et cliquez sur le lien "Download Package"
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  - décompressez le contenu de l'archive dans un répertoire temporaire
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  - copiez le répertoire "sfImageTransformExtraPlugin-1.0.5&#8243; (la racine de l'archive est un répertoire qui s'appelle "sfImageTransformExtraPlugin-1.0.5&#8243;, et il contient lui-même un sous-répertoire qui porte également ce nom ; c'est le sous-répertoire qu'il faut copier et non pas la racine de l'archive) dans le répertoire "plugins" de votre projet Symfony
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  - renommez ce répertoire en "plugins/sfImageTransformExtraPlugin" (<em>ie</em>, supprimer le tiret et le numéro de version)
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  Enfin, un article consacré à Symfony qui ne contient pas une instruction <em>symfony cc</em> n'est pas digne de ce nom ! Donc :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
        <span class="sy0">./</span>symfony cc
  </div>
</div>

<div style="background-color: lightyellow; border: 1px solid lightgrey; padding: 10px; text-align: center; margin-bottom: 15px;">
  A ce stade des opérations, nous n'activons pas encore le plugin "Extra". La configuration du plugin se fera plus tard car pour l'heure, nous allons tester le plugin sfImageTransformPlugin. Nous nous soucierons du plugin "Extra" plus tard.
</div>

## A présent, essayons le plugin de base

<p style="font-family: georgia, palatino; text-align: justify;">
  Nous allons nous assurer dans un premier temps que le plugin de base (sfImageTransformPlugin) est correctement installé et configuré. Pour cela, nous allons créer une page très simple, avec un bouton déclenchant un traitement sur une image de notre choix. Commençons par créer un module "image" dans l'application "Frontend".
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  Si elle n'existe pas encore, on crée l'application "Frontend" ...
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
        <span class="sy0">./</span>symfony generate<span class="sy0">:</span>app frontend
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  ... puis le module :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
        <span class="sy0">./</span>symfony generate<span class="sy0">:</span>module frontend image
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  Pour notre démonstration, j'ai choisi cette image, partant du principe qu'un prosélytisme de bon aloi ne saurait nuire à la carrière d'un développeur :
</p>

![zf Installation et premiers pas avec le plugin Symfony sfImageTransformExtraPlugin][1]

<p style="font-family: georgia, palatino; text-align: justify;">
  J'ai nommé cette image zf.jpg et je l'ai placée dans le répertoire web/images de notre projet.
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  Nous allons dans un premier temps écrire le code de notre template pour y insérer le formulaire par lequel nous allons appeler la transformation de notre image :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
        <span class="co2"># apps/frontend/modules/image/templates/indexSuccess.php<br /> </span>   <span class="sy0"><</span>h1<span class="sy0">></span>Image Transform <span class="sy0">&</span> Extra plugin<span class="sy0"></</span>h1<span class="sy0">></span><br /> <br />    <span class="sy0"><</span>div style<span class="sy0">=</span><span class="st0">"text-align:center; width: 400px; border: 1px solid lightgrey; padding:25px;"</span><span class="sy0">></span><br />    <span class="sy0"><</span>img src<span class="sy0">=</span><span class="st0">"/images/zf.jpg"</span><span class="sy0">></</span>img<span class="sy0">><</span>br<span class="sy0">/><</span>br<span class="sy0">/></span><br />    <span class="sy0"><</span>form method<span class="sy0">=</span><span class="st0">"post"</span><span class="sy0">></span><br />    <span class="sy0"><</span>input type<span class="sy0">=</span><span class="st0">"submit"</span> value<span class="sy0">=</span><span class="st0">"Transform image !"</span><span class="sy0">></</span>input<span class="sy0">></span><br />    <span class="sy0"></</span>form<span class="sy0">></span><br />    <span class="sy0"></</span>div<span class="sy0">></span>
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  Comme nous venons juste de créer le module, pensez à supprimer le code par défaut de la méthode executeIndex du fichier <span style="color: #3366ff;">apps/frontend/modules/actions/actions.class.php</span>. Affichons à présent la page dans notre navigateur : http://mon_hote_virtuel/frontend_dev.php/image. Le résultat est admirable ... je ne m'en lasse pas ...
</p>

<img src="http://www.elao.org/wp-content/uploads/2010/07/indexSuccess.php_.png" border="1" alt="indexSuccess.php  Installation et premiers pas avec le plugin Symfony sfImageTransformExtraPlugin"  title="Installation et premiers pas avec le plugin Symfony sfImageTransformExtraPlugin" />

<p style="font-family: georgia, palatino; text-align: justify;">
  Cela étant, notre but étant d'utiliser dans un premier temps le plugin sfImageTransformPlugin, nous allons écrire une méthode sans prétention qui va appliquer un traitement à cette image lorsque nous soumettons le formulaire. Pour cela, nous allons modifier le fichier actions.class.php afin de mettre à jour la méthode executeIndex :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="co2"># apps/frontend/modules/image/actions/actions.class.php<br /> </span><span class="kw2">class</span> imageActions <span class="kw2">extends</span> sfActions<br /> <span class="br0">&#123;</span><br />  <span class="co4">/**<br />   * Executes index action<br />   *<br />   * @param sfRequest $request A request object<br />   */</span><br />   <span class="kw2">public</span> <span class="kw2">function</span> executeIndex<span class="br0">&#40;</span>sfWebRequest <span class="re0">$request</span><span class="br0">&#41;</span><br />   <span class="br0">&#123;</span><br />     <span class="kw1">if</span><span class="br0">&#40;</span>sfRequest<span class="sy0">::</span><span class="me2">POST</span> <span class="sy0">==</span> <span class="re0">$request</span><span class="sy0">-></span><span class="me1">getMethod</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><br />     <span class="br0">&#123;</span><br />       <span class="re0">$file</span> <span class="sy0">=</span> sfConfig<span class="sy0">::</span><span class="me2">get</span><span class="br0">&#40;</span><span class="st_h">'sf_web_dir'</span><span class="br0">&#41;</span><span class="sy0">.</span><span class="kw4">DIRECTORY_SEPARATOR</span><span class="sy0">.</span><span class="st_h">'images'</span><span class="sy0">.</span><span class="kw4">DIRECTORY_SEPARATOR</span><span class="sy0">.</span><span class="st_h">'zf.jpg'</span> <span class="sy0">;</span><br />       <span class="re0">$img</span> <span class="sy0">=</span> <span class="kw2">new</span> sfImage<span class="br0">&#40;</span><span class="re0">$file</span><span class="sy0">,</span> <span class="st_h">'image/jpg'</span><span class="br0">&#41;</span> <span class="sy0">;</span><br />       <span class="re0">$response</span> <span class="sy0">=</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">getResponse</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br />       <span class="re0">$response</span><span class="sy0">-></span><span class="me1">setContentType</span><span class="br0">&#40;</span><span class="re0">$img</span><span class="sy0">-></span><span class="me1">getMIMEType</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br />       <span class="re0">$img</span><span class="sy0">-></span><span class="me1">resize</span><span class="br0">&#40;</span><span class="nu0">100</span><span class="sy0">,</span><span class="kw4">null</span><span class="br0">&#41;</span> <span class="sy0">;</span><br />       <span class="re0">$response</span><span class="sy0">-></span><span class="me1">setContent</span><span class="br0">&#40;</span><span class="re0">$img</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <br />       <span class="kw1">return</span> sfView<span class="sy0">::</span><span class="me2">NONE</span><span class="sy0">;</span><br />     <span class="br0">&#125;</span><br />   <span class="br0">&#125;</span><br /> <span class="br0">&#125;</span>
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  Rien de très compliqué, à la soumission du formulaire, nous créons une miniature de notre image d'origine (dimension 100 px) et nous l'affichons dans le navigateur. Cette méthode n'est pas d'un grand intérêt, je l'avoue ; elle a pour seule but de s'assurer que nous avons correctement configuré le plugin de base. A présent, attaquons-nous au plat de résistance :
</p>

## La bête : le plugin "Extra"

<p style="font-family: georgia, palatino; text-align: justify;">
  Nous allons commencer par activer le plugin :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="co1">// ...</span><br /> <span class="kw2">class</span> ProjectConfiguration <span class="kw2">extends</span> sfProjectConfiguration<br /> <span class="br0">&#123;</span><br />   <span class="kw2">public</span> <span class="kw2">function</span> setup<span class="br0">&#40;</span><span class="br0">&#41;</span><br />   <span class="br0">&#123;</span><br />     <span class="re0">$this</span><span class="sy0">-></span><span class="me1">enablePlugins</span><span class="br0">&#40;</span><span class="st_h">'sfDoctrinePlugin'</span><span class="br0">&#41;</span><span class="sy0">;</span><br />     <span class="re0">$this</span><span class="sy0">-></span><span class="me1">enablePlugins</span><span class="br0">&#40;</span><span class="st_h">'sfImageTransformPlugin'</span><span class="br0">&#41;</span><span class="sy0">;</span><br />     <span class="re0">$this</span><span class="sy0">-></span><span class="me1">enablePlugins</span><span class="br0">&#40;</span><span class="st_h">'sfImageTransformExtraPlugin'</span><span class="br0">&#41;</span><span class="sy0">;</span><br />   <span class="br0">&#125;</span><br /> <span class="br0">&#125;</span>
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  Le point d'entrée du plugin "Extra" se trouve dans un module que nous allons activer :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="co2"># apps/frontend/config/settings.yml<br /> </span>all<span class="sy0">:</span><br />   <span class="sy0">.</span>settings<span class="sy0">:</span><br />     <span class="co2"># ....<br /> </span>    enabled_modules<span class="sy0">:</span>        <span class="br0">&#91;</span> sfImageTransformator <span class="br0">&#93;</span>
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  Pour faire fonctionner le plugin, il est nécessaire d'activer la détection automatique du tyme MIME. Cela se fait dans le fichier app.yml de l'application :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="co2"># apps/frontend/config/app.yml<br /> </span>all<span class="sy0">:</span><br />   sfImageTransformPlugin<span class="sy0">:</span><br />     mime_type<span class="sy0">:</span><br />       auto_detect<span class="sy0">:</span>  <span class="kw4">true</span><br />       library<span class="sy0">:</span>      gd_mime_type <span class="co2">#  gd_mime_type (GD), Fileinfo (PECL), MIME_Type (PEAR)<br /> </span>    font_dir<span class="sy0">:</span>       <span class="sy0">%</span>SF_PLUGINS_DIR<span class="sy0">%/</span>sfImageTransformExtraPlugin<span class="sy0">/</span>data<span class="sy0">/</span>example<span class="sy0">-</span>resources<span class="sy0">/</span>fonts
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  Les routes par défaut du plugin "Extra" sont configurées avec l'URL relative /thumbnails/.. pour générer et/ou afficher les miniatures générées. Pour qu'elles fonctionnent, nous devons créer un répertoire "thumbnails" dans le répertoire "web" et nous assurer que le serveur possède les droits en écriture sur ce répertoire. Dans la console, en nous plaçant sous la racine du projet, nous entrons donc les commandes suivantes :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
      <a href="http://www.php.net/mkdir"><span class="kw3">mkdir</span></a> web<span class="sy0">/</span>thumbnails<br />   <a href="http://www.php.net/chmod"><span class="kw3">chmod</span></a> <span class="nu0">777</span> web<span class="sy0">/</span>thumbnails
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  Comme cela fait un long moment que nous n'avons pas tapé un <em>symfony cc</em>, il est temps d'y remédier :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
     <span class="sy0">./</span>symfony cc
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  Bon, nous avons passé un bon moment à préparer le terrain mais force est de constater que nous n'avons pas été très productifs jusqu'à présent. Il est donc temps d'exploiter ce plugin ! Mais avant toute chose, quelques remarques utiles. Je vous invite dans un premier temps à recharger la page où nous affichons le formulaire et à consulter les informations qui figurent dans la Web Debug Toolbar. Sélectionnez-y l'onglet "config" et affichez le contenu du sous-menu "Settings". Vous noterez tout en bas de la liste, après les constantes app_* et sf_*, la présence d'une constante nommée "thumbnailing_formats" :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    thumbnailing_formats<span class="sy0">:</span><br />   <span class="kw1">default</span><span class="sy0">:</span> <span class="br0">&#123;</span> quality<span class="sy0">:</span> <span class="nu0">25</span><span class="sy0">,</span> mime_type<span class="sy0">:</span> image<span class="sy0">/</span>gif<span class="sy0">,</span> transformations<span class="sy0">:</span> <span class="sy0">...</span> <span class="br0">&#125;</span><br />   original<span class="sy0">:</span> <span class="br0">&#123;</span> quality<span class="sy0">:</span> <span class="nu0">100</span><span class="sy0">,</span> mime_type<span class="sy0">:</span> image<span class="sy0">/</span>jpg<span class="sy0">,</span> transformations<span class="sy0">:</span> <span class="br0">&#123;</span>  <span class="br0">&#125;</span> <span class="br0">&#125;</span>
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  Mais que sont ces "thumbnailing_formats" et où sont-ils définis exactement ? Pour répondre à la seconde question, ces formats sont définis dans le fichier plugins/sfImageTransformExtraPlugin/thumbnailing.yml et voici son contenu :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="co2"># plugins/sfImageTransformExtraPlugin/thumbnailing.yml<br /> </span>all<span class="sy0">:</span><br />   <span class="sy0">.</span>settings<span class="sy0">:</span><br />     formats<span class="sy0">:</span><br /> <span class="co2">#