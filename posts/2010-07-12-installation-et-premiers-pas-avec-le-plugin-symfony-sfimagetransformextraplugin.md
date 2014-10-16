
<p style="font-family: georgia, palatino;">
  Bonjour,
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  Nous allons voir aujourd&#8217;hui comment installer et utiliser le plugin <a href="http://www.symfony-project.org/plugins/sfImageTransformExtraPlugin" target="_blank">sfImageTransformExtraPlugin</a>.
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  Il s&#8217;agit d&#8217;un plugin très puissant permettant d&#8217;appliquer des traitements à des images au moyen de fichiers de configuration, sans qu&#8217;il soit donc nécessaire de polluer le code métier. Par ailleurs, il permet d&#8217;appliquer plusieurs traitements successifs de manière très intuitive. Il gère lui-même l&#8217;emplacement des fichiers générés et propose un système de cache réduisant les délais de transmission des images déjà générées.
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  Le sfImageTransformExtraPlugin est dépendant du plugin <a href="http://www.symfony-project.org/plugins/sfImageTransformPlugin" target="_blank">sfImageTransformPlugin</a> mais ne l&#8217;installez pas avant d&#8217;avoir lu la suite ! Pour faciliter la lecture et éviter toute confusion (les noms des deux plugins sont très proches), dans la suite de l&#8217;article je ferai référence au &#8220;plugin de base&#8221; pour sfImageTransformPlugin et au &#8220;plugin Extra&#8221; pour sfImageTransformExtraPlugin.
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  L&#8217;outil est donc très puissant mais sa documentation contient de nombreuses zones d&#8217;ombre qui ne facilitent absolument pas sa prise en main. Et les ennuis commencent dès l&#8217;installation ! D&#8217;où l&#8217;intérêt de cet article.
</p>

## L&#8217;installation des plugins ou le début <del>des emm&#8230;</del> de nos démarches

<div style="background-color: lightyellow; border: 1px solid lightgrey; padding: 10px; text-align: center; margin-bottom: 15px;">
  Le plugin sfImageTransformPlugin nécessite que GD ou ImageMagick soit installé sur votre machine.
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  En effet, au moment de la rédaction de ce billet, la version courante du plugin &#8220;Extra&#8221; est la version 1.0.5 qui requiert la version 1.3.0 ou 1.3.1 du plugin sfImageTransformPlugin. Or, la version courante de ce dernier est 1.4.0, mais elle est incompatible avec la dernière version du plugin &#8220;Extra&#8221;. Si bien que si vous essayez d&#8217;installer le plugin &#8220;Extra&#8221; via la ligne de commande <em>plugin:install</em>, l&#8217;opération échouera car elle installera préalablement la dernière version de sfImageTransformPlugin, donc une version incompatible ! Vous me suivez ? Oui, je sais que ces histoires de versions ne sont pas passionnantes mais si vous souhaitez utiliser sfImageTransformExtraPlugin (et nous le recommandons !), mieux vaut être au courant de ces incompatibilités. Voici donc la démarche que nous préconisons :
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  Installer le plugin de base (sfImageTransformPlugin) en ligne de commande en prenant soin de préciser la version souhaitée :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    &nbsp; &nbsp; <span class="sy0">./</span>symfony plugin<span class="sy0">:</span>install <span class="sy0">--</span>release<span class="sy0">=</span>1<span class="sy0">.</span>3<span class="sy0">.</span>1 sfImageTransformPlugin
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  Pour le plugin &#8220;Extra&#8221;, il faut l&#8217;installer manuellement. Pour cela :
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  - rendez-vous sur <a title="Télécharger le plugin sfImageTransformExtraPlugin" href="http://www.symfony-project.org/plugins/sfImageTransformExtraPlugin" target="_blank">la page dédiée au plugin</a> et cliquez sur le lien &#8220;Download Package&#8221;
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  - décompressez le contenu de l&#8217;archive dans un répertoire temporaire
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  - copiez le répertoire &#8220;sfImageTransformExtraPlugin-1.0.5&#8243; (la racine de l&#8217;archive est un répertoire qui s&#8217;appelle &#8220;sfImageTransformExtraPlugin-1.0.5&#8243;, et il contient lui-même un sous-répertoire qui porte également ce nom ; c&#8217;est le sous-répertoire qu&#8217;il faut copier et non pas la racine de l&#8217;archive) dans le répertoire &#8220;plugins&#8221; de votre projet Symfony
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  - renommez ce répertoire en &#8220;plugins/sfImageTransformExtraPlugin&#8221; (<em>ie</em>, supprimer le tiret et le numéro de version)
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  Enfin, un article consacré à Symfony qui ne contient pas une instruction <em>symfony cc</em> n&#8217;est pas digne de ce nom ! Donc :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    &nbsp; &nbsp; <span class="sy0">./</span>symfony cc
  </div>
</div>

<div style="background-color: lightyellow; border: 1px solid lightgrey; padding: 10px; text-align: center; margin-bottom: 15px;">
  A ce stade des opérations, nous n&#8217;activons pas encore le plugin &#8220;Extra&#8221;. La configuration du plugin se fera plus tard car pour l&#8217;heure, nous allons tester le plugin sfImageTransformPlugin. Nous nous soucierons du plugin &#8220;Extra&#8221; plus tard.
</div>

## A présent, essayons le plugin de base

<p style="font-family: georgia, palatino; text-align: justify;">
  Nous allons nous assurer dans un premier temps que le plugin de base (sfImageTransformPlugin) est correctement installé et configuré. Pour cela, nous allons créer une page très simple, avec un bouton déclenchant un traitement sur une image de notre choix. Commençons par créer un module &#8220;image&#8221; dans l&#8217;application &#8220;Frontend&#8221;.
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  Si elle n&#8217;existe pas encore, on crée l&#8217;application &#8220;Frontend&#8221; &#8230;
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    &nbsp; &nbsp; <span class="sy0">./</span>symfony generate<span class="sy0">:</span>app frontend
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  &#8230; puis le module :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    &nbsp; &nbsp; <span class="sy0">./</span>symfony generate<span class="sy0">:</span>module frontend image
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  Pour notre démonstration, j&#8217;ai choisi cette image, partant du principe qu&#8217;un prosélytisme de bon aloi ne saurait nuire à la carrière d&#8217;un développeur :
</p>

![zf Installation et premiers pas avec le plugin Symfony sfImageTransformExtraPlugin][1]

<p style="font-family: georgia, palatino; text-align: justify;">
  J&#8217;ai nommé cette image zf.jpg et je l&#8217;ai placée dans le répertoire web/images de notre projet.
</p>

<p style="font-family: georgia, palatino; text-align: justify;">
  Nous allons dans un premier temps écrire le code de notre template pour y insérer le formulaire par lequel nous allons appeler la transformation de notre image :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    &nbsp; &nbsp; <span class="co2"># apps/frontend/modules/image/templates/indexSuccess.php<br /> </span> &nbsp; <span class="sy0"><</span>h1<span class="sy0">></span>Image Transform <span class="sy0">&</span> Extra plugin<span class="sy0"></</span>h1<span class="sy0">></span><br /> <br /> &nbsp; &nbsp;<span class="sy0"><</span>div style<span class="sy0">=</span><span class="st0">"text-align:center; width: 400px; border: 1px solid lightgrey; padding:25px;"</span><span class="sy0">></span><br /> &nbsp; &nbsp;<span class="sy0"><</span>img src<span class="sy0">=</span><span class="st0">"/images/zf.jpg"</span><span class="sy0">></</span>img<span class="sy0">><</span>br<span class="sy0">/><</span>br<span class="sy0">/></span><br /> &nbsp; &nbsp;<span class="sy0"><</span>form method<span class="sy0">=</span><span class="st0">"post"</span><span class="sy0">></span><br /> &nbsp; &nbsp;<span class="sy0"><</span>input type<span class="sy0">=</span><span class="st0">"submit"</span> value<span class="sy0">=</span><span class="st0">"Transform image !"</span><span class="sy0">></</span>input<span class="sy0">></span><br /> &nbsp; &nbsp;<span class="sy0"></</span>form<span class="sy0">></span><br /> &nbsp; &nbsp;<span class="sy0"></</span>div<span class="sy0">></span>
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  Comme nous venons juste de créer le module, pensez à supprimer le code par défaut de la méthode executeIndex du fichier <span style="color: #3366ff;">apps/frontend/modules/actions/actions.class.php</span>. Affichons à présent la page dans notre navigateur : http://mon_hote_virtuel/frontend_dev.php/image. Le résultat est admirable &#8230; je ne m&#8217;en lasse pas &#8230;
</p>

<img src="http://www.elao.org/wp-content/uploads/2010/07/indexSuccess.php_.png" border="1" alt="indexSuccess.php  Installation et premiers pas avec le plugin Symfony sfImageTransformExtraPlugin"  title="Installation et premiers pas avec le plugin Symfony sfImageTransformExtraPlugin" />

<p style="font-family: georgia, palatino; text-align: justify;">
  Cela étant, notre but étant d&#8217;utiliser dans un premier temps le plugin sfImageTransformPlugin, nous allons écrire une méthode sans prétention qui va appliquer un traitement à cette image lorsque nous soumettons le formulaire. Pour cela, nous allons modifier le fichier actions.class.php afin de mettre à jour la méthode executeIndex :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="co2"># apps/frontend/modules/image/actions/actions.class.php<br /> </span><span class="kw2">class</span> imageActions <span class="kw2">extends</span> sfActions<br /> <span class="br0">&#123;</span><br /> &nbsp;<span class="co4">/**<br /> &nbsp; * Executes index action<br /> &nbsp; *<br /> &nbsp; * @param sfRequest $request A request object<br /> &nbsp; */</span><br /> &nbsp; <span class="kw2">public</span> <span class="kw2">function</span> executeIndex<span class="br0">&#40;</span>sfWebRequest <span class="re0">$request</span><span class="br0">&#41;</span><br /> &nbsp; <span class="br0">&#123;</span><br /> &nbsp; &nbsp; <span class="kw1">if</span><span class="br0">&#40;</span>sfRequest<span class="sy0">::</span><span class="me2">POST</span> <span class="sy0">==</span> <span class="re0">$request</span><span class="sy0">-></span><span class="me1">getMethod</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><br /> &nbsp; &nbsp; <span class="br0">&#123;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$file</span> <span class="sy0">=</span> sfConfig<span class="sy0">::</span><span class="me2">get</span><span class="br0">&#40;</span><span class="st_h">'sf_web_dir'</span><span class="br0">&#41;</span><span class="sy0">.</span><span class="kw4">DIRECTORY_SEPARATOR</span><span class="sy0">.</span><span class="st_h">'images'</span><span class="sy0">.</span><span class="kw4">DIRECTORY_SEPARATOR</span><span class="sy0">.</span><span class="st_h">'zf.jpg'</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$img</span> <span class="sy0">=</span> <span class="kw2">new</span> sfImage<span class="br0">&#40;</span><span class="re0">$file</span><span class="sy0">,</span> <span class="st_h">'image/jpg'</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$response</span> <span class="sy0">=</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">getResponse</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$response</span><span class="sy0">-></span><span class="me1">setContentType</span><span class="br0">&#40;</span><span class="re0">$img</span><span class="sy0">-></span><span class="me1">getMIMEType</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$img</span><span class="sy0">-></span><span class="me1">resize</span><span class="br0">&#40;</span><span class="nu0">100</span><span class="sy0">,</span><span class="kw4">null</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$response</span><span class="sy0">-></span><span class="me1">setContent</span><span class="br0">&#40;</span><span class="re0">$img</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <br /> &nbsp; &nbsp; &nbsp; <span class="kw1">return</span> sfView<span class="sy0">::</span><span class="me2">NONE</span><span class="sy0">;</span><br /> &nbsp; &nbsp; <span class="br0">&#125;</span><br /> &nbsp; <span class="br0">&#125;</span><br /> <span class="br0">&#125;</span>
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  Rien de très compliqué, à la soumission du formulaire, nous créons une miniature de notre image d&#8217;origine (dimension 100 px) et nous l&#8217;affichons dans le navigateur. Cette méthode n&#8217;est pas d&#8217;un grand intérêt, je l&#8217;avoue ; elle a pour seule but de s&#8217;assurer que nous avons correctement configuré le plugin de base. A présent, attaquons-nous au plat de résistance :
</p>

## La bête : le plugin &#8220;Extra&#8221;

<p style="font-family: georgia, palatino; text-align: justify;">
  Nous allons commencer par activer le plugin :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="co1">// ...</span><br /> <span class="kw2">class</span> ProjectConfiguration <span class="kw2">extends</span> sfProjectConfiguration<br /> <span class="br0">&#123;</span><br /> &nbsp; <span class="kw2">public</span> <span class="kw2">function</span> setup<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> &nbsp; <span class="br0">&#123;</span><br /> &nbsp; &nbsp; <span class="re0">$this</span><span class="sy0">-></span><span class="me1">enablePlugins</span><span class="br0">&#40;</span><span class="st_h">'sfDoctrinePlugin'</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; &nbsp; <span class="re0">$this</span><span class="sy0">-></span><span class="me1">enablePlugins</span><span class="br0">&#40;</span><span class="st_h">'sfImageTransformPlugin'</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; &nbsp; <span class="re0">$this</span><span class="sy0">-></span><span class="me1">enablePlugins</span><span class="br0">&#40;</span><span class="st_h">'sfImageTransformExtraPlugin'</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; <span class="br0">&#125;</span><br /> <span class="br0">&#125;</span>
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  Le point d&#8217;entrée du plugin &#8220;Extra&#8221; se trouve dans un module que nous allons activer :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="co2"># apps/frontend/config/settings.yml<br /> </span>all<span class="sy0">:</span><br /> &nbsp; <span class="sy0">.</span>settings<span class="sy0">:</span><br /> &nbsp; &nbsp; <span class="co2"># ....<br /> </span> &nbsp; &nbsp;enabled_modules<span class="sy0">:</span> &nbsp; &nbsp; &nbsp; &nbsp;<span class="br0">&#91;</span> sfImageTransformator <span class="br0">&#93;</span>
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  Pour faire fonctionner le plugin, il est nécessaire d&#8217;activer la détection automatique du tyme MIME. Cela se fait dans le fichier app.yml de l&#8217;application :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="co2"># apps/frontend/config/app.yml<br /> </span>all<span class="sy0">:</span><br /> &nbsp; sfImageTransformPlugin<span class="sy0">:</span><br /> &nbsp; &nbsp; mime_type<span class="sy0">:</span><br /> &nbsp; &nbsp; &nbsp; auto_detect<span class="sy0">:</span> &nbsp;<span class="kw4">true</span><br /> &nbsp; &nbsp; &nbsp; library<span class="sy0">:</span> &nbsp; &nbsp; &nbsp;gd_mime_type <span class="co2"># &nbsp;gd_mime_type (GD), Fileinfo (PECL), MIME_Type (PEAR)<br /> </span> &nbsp; &nbsp;font_dir<span class="sy0">:</span> &nbsp; &nbsp; &nbsp; <span class="sy0">%</span>SF_PLUGINS_DIR<span class="sy0">%/</span>sfImageTransformExtraPlugin<span class="sy0">/</span>data<span class="sy0">/</span>example<span class="sy0">-</span>resources<span class="sy0">/</span>fonts
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  Les routes par défaut du plugin &#8220;Extra&#8221; sont configurées avec l&#8217;URL relative /thumbnails/.. pour générer et/ou afficher les miniatures générées. Pour qu&#8217;elles fonctionnent, nous devons créer un répertoire &#8220;thumbnails&#8221; dans le répertoire &#8220;web&#8221; et nous assurer que le serveur possède les droits en écriture sur ce répertoire. Dans la console, en nous plaçant sous la racine du projet, nous entrons donc les commandes suivantes :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    &nbsp; <a href="http://www.php.net/mkdir"><span class="kw3">mkdir</span></a> web<span class="sy0">/</span>thumbnails<br /> &nbsp; <a href="http://www.php.net/chmod"><span class="kw3">chmod</span></a> <span class="nu0">777</span> web<span class="sy0">/</span>thumbnails
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  Comme cela fait un long moment que nous n&#8217;avons pas tapé un <em>symfony cc</em>, il est temps d&#8217;y remédier :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    &nbsp;<span class="sy0">./</span>symfony cc
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  Bon, nous avons passé un bon moment à préparer le terrain mais force est de constater que nous n&#8217;avons pas été très productifs jusqu&#8217;à présent. Il est donc temps d&#8217;exploiter ce plugin ! Mais avant toute chose, quelques remarques utiles. Je vous invite dans un premier temps à recharger la page où nous affichons le formulaire et à consulter les informations qui figurent dans la Web Debug Toolbar. Sélectionnez-y l&#8217;onglet &#8220;config&#8221; et affichez le contenu du sous-menu &#8220;Settings&#8221;. Vous noterez tout en bas de la liste, après les constantes app_* et sf_*, la présence d&#8217;une constante nommée &#8220;thumbnailing_formats&#8221; :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    thumbnailing_formats<span class="sy0">:</span><br /> &nbsp; <span class="kw1">default</span><span class="sy0">:</span> <span class="br0">&#123;</span> quality<span class="sy0">:</span> <span class="nu0">25</span><span class="sy0">,</span> mime_type<span class="sy0">:</span> image<span class="sy0">/</span>gif<span class="sy0">,</span> transformations<span class="sy0">:</span> <span class="sy0">...</span> <span class="br0">&#125;</span><br /> &nbsp; original<span class="sy0">:</span> <span class="br0">&#123;</span> quality<span class="sy0">:</span> <span class="nu0">100</span><span class="sy0">,</span> mime_type<span class="sy0">:</span> image<span class="sy0">/</span>jpg<span class="sy0">,</span> transformations<span class="sy0">:</span> <span class="br0">&#123;</span> &nbsp;<span class="br0">&#125;</span> <span class="br0">&#125;</span>
  </div>
</div>

<p style="font-family: georgia, palatino; text-align: justify;">
  Mais que sont ces &#8220;thumbnailing_formats&#8221; et où sont-ils définis exactement ? Pour répondre à la seconde question, ces formats sont définis dans le fichier plugins/sfImageTransformExtraPlugin/thumbnailing.yml et voici son contenu :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="co2"># plugins/sfImageTransformExtraPlugin/thumbnailing.yml<br /> </span>all<span class="sy0">:</span><br /> &nbsp; <span class="sy0">.</span>settings<span class="sy0">:</span><br /> &nbsp; &nbsp; formats<span class="sy0">:</span><br /> <span class="co2"># 