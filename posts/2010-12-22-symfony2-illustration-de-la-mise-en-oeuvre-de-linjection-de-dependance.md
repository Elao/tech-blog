
<div style="float: left; margin-right: 15px; margin-bottom: 15px;">
  <img src="/blog/wp-content/uploads/2010/11/sf2_reloaded.png" alt="sf2 reloaded Symfony2   Illustration de la mise en oeuvre de lInjection de dépendance"  title="Symfony2   Illustration de la mise en oeuvre de lInjection de dépendance" />
</div>

<p class="standard">
  Bonjour à toutes et à tous,
</p>

<p class="standard">
  Le Framework Symfony a été totalement repensé, et la version 2, attendue pour mars 2011, nous réserve énormément de nouveautés. Parmi les évolutions du Framework, un des aspects les plus marquants concerne la mise en oeuvre de <strong>l'Injection de dépendance</strong>. C'est sans doute le composant sur lequel Fabien Potencier a le plus communiqué lors des premières annonces concernant Symfony2 et il a déjà eu l'occasion d'exposer sa philosophie à maintes reprises. En particulier, les principes de l'injection de dépendance et sa mise en oeuvre sous Symfony2 ont fait l'objet d'une série d'articles parus sur le <a href="http://components.symfony-project.org/dependency-injection/documentation" target="_blank">site de Sensio Labs</a>.
</p>

<p class="standard">
  Noter également que Vincent avait publié sur notre blog un article dans lequel il expliquait notamment l'organisation des différentes classes du Framework concourant à la mise en oeuvre de l'<a href="http://www.elao.org/symfony-2/symfony-2-linjection-de-dependances.html" target="_blank">Injection de dépendance</a>. Le coeur du framework a notablement évolué depuis la rédaction de ce billet mais les principes qui y sont décrits restent d'actualité.
</p>

<p class="standard">
  A présent, Symfony2 est disponible dans sa version "Preview Release 4&#8243;, et cette version ne devrait pas connaître d'évolution majeure par rapport à la version finale, attendue donc à la fin du premier trimestre 2011. L'occasion nous est donnée de constater de visu comment les principes énoncés jusqu'ici seront concrètement appliqués dans la version finale. Comme de nombreux développeurs à l'affût des évolutions du Framework, nous avons commencé à nous pencher sérieusement sur cette nouvelle mouture, et nous avons également observé quelques Bundles Symfony2 en cours de développement, histoire de nous imprégner de la nouvelle philosophie du Framework. Parmi tous ces projets, il en est un qui a particulièrement attiré notre attention, car il offre un exemple concret de mise en pratique de l'injection de dépendance avec Symfony2, mais également parce qu'il permet, à notre sens, de mesurer pleinement l'apport indéniable de l'injection de dépendance dans nos projets. Le code de ce Bundle, toujours en cours de développement, est consultable sur l'hébergeur GitHub ; il s'agit du <a href="https://github.com/Herzult/ForumBundle" target="_blank">ForumBundle</a>.
</p>

## Un Bundle ouvert grâce à l'injection de dépendance

<p class="standard">
  En analysant le code du Bundle <strong>ForumBundle</strong>, on constate immédiatement que les développeurs se sont attachés à produire un Bundle ouvert, facilement configurable, pour permettre notamment aux applications qui l'hébergeront d'utiliser soit <a href="http://www.doctrine-project.org/projects/orm" target="_blank">l'ORM de Doctrine</a>, soit un <a href="http://www.doctrine-project.org/projects/mongodb_odm" target="_blank">Object Document Manager</a> de Doctrine. Or, vous n'êtes pas sans savoir que la base de données que vous utiliserez, relationnelle ou No-SQL, conditionnera en grande partie l'écriture du code de vos contrôleurs ou de vos repositories. En effet, selon que vous optiez pour une base de données relationnelles ou une base No-SQL, vous ne manipulerez pas les mêmes <i>Managers</i>, que ce soit dans vos controleurs ou dans vos repositories. Dans un cas, vous serez amenés à instancier un <i>EntityManager</i> (cas de l'ORM avec une base de données relationnelle), dans l'autre cas, un <i>DocumentManager</i> (cas de MongoDB ou CouchDB). Si vous envisagez de créer un Bundle destiné à être distribué à des tiers, toute la problématique est donc de proposer un code qui laisse le choix du type de base de données à ses futurs utilisateurs. Et c'est là qu'intervient l'injection de dépendance. Nous allons donc voir concrètement comment <i>ForumBundle</i> propose une solution élégante, grâce à l'injection de dépendance.
</p>

<p class="standard">
  Les Bundles qui enrichiront vos applications pourront embarquer leurs propres définitions de services. Ainsi, dans le cas du <i>ForumBundle</i>, après installation, il faut dans un premier temps déclarer l'ajout d'un nouveau Bundle en mettant à jour la méthode <i>registerBundles</i> de votre Kernel :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="co2"># app/AppKernel.php<br /> </span><br />     <span class="co1">// …</span><br /> <br />     <span class="kw2">public</span> <span class="kw2">function</span> registerBundles<span class="br0">&#40;</span><span class="br0">&#41;</span><br />     <span class="br0">&#123;</span><br />         <span class="re0">$bundles</span> <span class="sy0">=</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><br />             <span class="co1">// ...</span><br /> <br />             <span class="co1">// register your bundles</span><br />             <span class="kw2">new</span> Application\HelloBundle\HelloBundle<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">,</span><br />             <span class="kw2">new</span> Bundle\ForumBundle\ForumBundle<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">,</span><br />         <span class="br0">&#41;</span><span class="sy0">;</span>
  </div>
</div>

<p class="standard">
  Puis, dans un second temps, modifier votre fichier de configuration principale (dans l'exemple qui suit, c'est un fichier YML, mais vous avez peut-être opté pour le format PHP ou XML) :
</p>

<div class="codecolorer-container yaml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="yaml codecolorer">
    <span class="co1"># app/config/config.yml</span><br /> <br /> <span class="co1"># …</span><br /> <span class="co4"><br /> forum.config</span>:<span class="co3"><br />     db_driver</span><span class="sy2">: </span>orm<span class="co4"><br />     class</span>:<span class="co4"><br />       model</span>:<span class="co3"><br />         category</span><span class="sy2">: </span>Bundle\Application\ForumBundle\Entity\Category<span class="co3"><br />         topic</span><span class="sy2">: </span>Bundle\Application\ForumBundle\Entity\Topic<span class="co3"><br />         post</span><span class="sy2">: </span>Bundle\Application\ForumBundle\Entity\Post<span class="co3"><br />         user</span><span class="sy2">: </span>Bundle\Application\DoctrineUserBundle\Entity\User    <br />     <span class="co1">#...</span>
  </div>
</div>

<p class="standard">
  C'est ainsi que vous avez défini le Driver souhaité pour utiliser le Bundle. Si nous avions souhaité utiliser MongoDB, nous aurions écrit <i>odm</i> au lieu d'<i>orm</i>. De même, comme nous avons opté pour l'ORM, nous devons déclarer les classes du modèle utilisées. En effet, si vous observez les répertoires de premier niveau du Bundle, vous noterez la présence des trois dossiers suivants :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
        <span class="sy0">+</span> Document<br />     <span class="sy0">+</span> Entity<br />     <span class="sy0">+</span> Model
  </div>
</div>

<p class="standard">
  Le répertoire <i>Model</i> contient des classes du Modèle, dont le code est commun à tous les objets du modèle, qu'ils soient gérés par l'ORM ou par le MongoDB Mapper. Ces classes, abstraites, ne sont donc liées à aucune technologie particulière. Ensuite, les répertoires <i>Document</i> et <i>Entity</i> contiennent les classes concrètes du modèle (qui héritent de celles définies dans le répertoire <i>Model</i>) et les repositories propres à la technologie adoptée. Noter également dans le répertoire "commun" <i>Model</i> la présence d'interfaces <i>XXXRepositoryInterface</i> qui seront implémentées par les classes de Repository ; elles permettent notamment de définir le contrat à remplir par les classes qui les implémenteront, quelle que soit la technologie employée.
</p>

<p class="standard">
  A présent, nous allons voir le contenu du fichier <i>src/Bundle/ForumBundle/DependencyInjection/ForumExtension.php</i>, et en particulier la méthode <i>configLoad</i>. C'est en effet là qu'a lieu toute la "magie" qui va permettre au Bundle de recourir au bon Manager ; c'est d'une simplicité déconcertante :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="kw2">class</span> ForumExtension <span class="kw2">extends</span> Extension<br /> <span class="br0">&#123;</span><br /> <br />     <span class="kw2">public</span> <span class="kw2">function</span> configLoad<span class="br0">&#40;</span><span class="re0">$config</span><span class="sy0">,</span> ContainerBuilder <span class="re0">$container</span><span class="br0">&#41;</span><br />     <span class="br0">&#123;</span><br />         <span class="re0">$loader</span> <span class="sy0">=</span> <span class="kw2">new</span> XmlFileLoader<span class="br0">&#40;</span><span class="re0">$container</span><span class="sy0">,</span> __DIR__ <span class="sy0">.</span> <span class="st_h">'/../Resources/config'</span><span class="br0">&#41;</span><span class="sy0">;</span> <span class="co1">// (1)</span><br />         <span class="re0">$loader</span><span class="sy0">-></span><span class="me1">load</span><span class="br0">&#40;</span><span class="st_h">'model.xml'</span><span class="br0">&#41;</span><span class="sy0">;</span><br />         <span class="re0">$loader</span><span class="sy0">-></span><span class="me1">load</span><span class="br0">&#40;</span><span class="st_h">'controller.xml'</span><span class="br0">&#41;</span><span class="sy0">;</span><br />         <span class="re0">$loader</span><span class="sy0">-></span><span class="me1">load</span><span class="br0">&#40;</span><span class="st_h">'form.xml'</span><span class="br0">&#41;</span><span class="sy0">;</span><br />         <span class="re0">$loader</span><span class="sy0">-></span><span class="me1">load</span><span class="br0">&#40;</span><span class="st_h">'blamer.xml'</span><span class="br0">&#41;</span><span class="sy0">;</span><br />         <span class="re0">$loader</span><span class="sy0">-></span><span class="me1">load</span><span class="br0">&#40;</span><span class="st_h">'templating.xml'</span><span class="br0">&#41;</span><span class="sy0">;</span><br />         <span class="re0">$loader</span><span class="sy0">-></span><span class="me1">load</span><span class="br0">&#40;</span><span class="st_h">'paginator.xml'</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <br />         <span class="kw1">if</span> <span class="br0">&#40;</span><span class="sy0">!</span><a href="http://www.php.net/isset"><span class="kw3">isset</span></a><span class="br0">&#40;</span><span class="re0">$config</span><span class="br0">&#91;</span><span class="st_h">'db_driver'</span><span class="br0">&#93;</span><span class="br0">&#41;</span><span class="br0">&#41;</span> <span class="br0">&#123;</span><br />             <span class="kw1">throw</span> <span class="kw2">new</span> \InvalidArgumentException<span class="br0">&#40;</span><span class="st_h">'You must provide the forum.db_driver configuration'</span><span class="br0">&#41;</span><span class="sy0">;</span> <span class="co1">// (2)</span><br />         <span class="br0">&#125;</span><br /> <br />         try <span class="br0">&#123;</span><br />             <span class="re0">$loader</span><span class="sy0">-></span><span class="me1">load</span><span class="br0">&#40;</span><a href="http://www.php.net/sprintf"><span class="kw3">sprintf</span></a><span class="br0">&#40;</span><span class="st_h">'%s.xml'</span><span class="sy0">,</span> <span class="re0">$config</span><span class="br0">&#91;</span><span class="st_h">'db_driver'</span><span class="br0">&#93;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span> <span class="co1">// (3)</span><br />         <span class="br0">&#125;</span> catch <span class="br0">&#40;</span>\InvalidArgumentException <span class="re0">$e</span><span class="br0">&#41;</span> <span class="br0">&#123;</span><br />             <span class="kw1">throw</span> <span class="kw2">new</span> \InvalidArgumentException<span class="br0">&#40;</span><a href="http://www.php.net/sprintf"><span class="kw3">sprintf</span></a><span class="br0">&#40;</span><span class="st_h">'The db_driver "%s" is not supported by forum'</span><span class="sy0">,</span> <span class="re0">$config</span><span class="br0">&#91;</span><span class="st_h">'db_driver'</span><span class="br0">&#93;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br />         <span class="br0">&#125;</span>
  </div>
</div>

<p class="standard">
  Dans un premier temps, le loader charge les fichiers de définition XML présents dans le répertoire <i>Resources/config</i> du Bundle (1). Ensuite, la méthode s'assure que nous avons bien défini un Driver dans notre configuration principale (2). Enfin, en fonction du Driver que nous avons choisi, le loader charge le fichier approprié, <i>orm.xml</i> ou <i>odm.xml</i>. Allons consulter le contenu de ces deux fichiers :
</p>

<div class="codecolorer-container xml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="xml codecolorer">
    <span class="sc-1"><!-- orm.xml  --></span><br />     <span class="sc3"><span class="re1"><services<span class="re2">></span></span></span><br />         <span class="sc-1"><!-- Object Manager Service --></span><br />         <span class="sc3"><span class="re1"><service</span> <span class="re0">id</span>=<span class="st0">"forum.object_manager"</span> <span class="re0">alias</span>=<span class="st0">"doctrine.orm.entity_manager"</span> <span class="re2">/></span></span><br />     <span class="sc3"><span class="re1"></services<span class="re2">></span></span></span>
  </div>
</div>

<div class="codecolorer-container xml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="xml codecolorer">
    <span class="sc-1"><!-- odm.xml  --></span><br />     <span class="sc3"><span class="re1"><services<span class="re2">></span></span></span><br />         <span class="sc-1"><!-- Object Manager Service --></span><br />         <span class="sc3"><span class="re1"><service</span> <span class="re0">id</span>=<span class="st0">"forum.object_manager"</span> <span class="re0">alias</span>=<span class="st0">"doctrine.odm.mongodb.document_manager"</span> <span class="re2">/></span></span><br />     <span class="sc3"><span class="re1"></services<span class="re2">></span></span></span>
  </div>
</div>

<p class="standard">
  Voilà, chacun des fichiers définit un service portant le même identifiant, "forum.object_manager", mais correspondant à des alias différents. Comme nous avons opté pour l'ORM, le service ayant cet identifiant correspond au service ayant pour clé "doctrine.orm.entity_manager". Et désormais, les autres services utilisant un <i>Manager</i> font référence à cet identifiant, sans se soucier de savoir s'ils ont affaire à un EntityManager ou à un DocumentManager. De la même manière, le code des contrôleurs fait référence à la clé "forum.object_manager" pour récupérer un manager :
</p>

<div class="codecolorer-container xml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="xml codecolorer">
        <span class="sc-1"><!-- Le fichier Resources/config/model.xml déclare par exemple un service Repository qui a lui même recours au service "forum.object_manager" --></span><br />     <span class="sc3"><span class="re1"><services<span class="re2">></span></span></span><br />         <span class="sc-1"><!-- Category Repository Service --></span><br />         <span class="sc3"><span class="re1"><service</span> <span class="re0">id</span>=<span class="st0">"forum.repository.category"</span> <span class="re0">class</span>=<span class="st0">"Bundle\ForumBundle\ForumBundle"</span> <span class="re0">factory-method</span>=<span class="st0">"getRepository"</span> <span class="re0">shared</span>=<span class="st0">"true"</span><span class="re2">></span></span><br />             <span class="sc3"><span class="re1"><argument</span> <span class="re0">type</span>=<span class="st0">"service"</span> <span class="re0">id</span>=<span class="st0">"forum.object_manager"</span> <span class="re2">/></span></span><br />             <span class="sc3"><span class="re1"><argument</span> <span class="re0">type</span>=<span class="st0">"string"</span><span class="re2">></span></span>%forum.model.category.class%<span class="sc3"><span class="re1"></argument<span class="re2">></span></span></span><br />         <span class="sc3"><span class="re1"></service<span class="re2">></span></span></span>
  </div>
</div>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
        <span class="co1">// La méthode savePost de la classe Controller/PostController récupère un Manager de la même manière :</span><br />     <br />     <span class="kw2">public</span> <span class="kw2">function</span> savePost<span class="br0">&#40;</span>Post <span class="re0">$post</span><span class="br0">&#41;</span><br />     <span class="br0">&#123;</span><br />         <span class="re0">$objectManager</span> <span class="sy0">=</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">get</span><span class="br0">&#40;</span><span class="st_h">'forum.object_manager'</span><span class="br0">&#41;</span><span class="sy0">;</span><br />         <span class="re0">$objectManager</span><span class="sy0">-></span><span class="me1">persist</span><span class="br0">&#40;</span><span class="re0">$post</span><span class="br0">&#41;</span><span class="sy0">;</span><br />         <span class="re0">$objectManager</span><span class="sy0">-></span><a href="http://www.php.net/flush"><span class="kw3">flush</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br />     <span class="br0">&#125;</span>
  </div>
</div>

## Conclusion

<p class="standard">
  En analysant la structure globale du Bundle <i>ForumBundle</i>, nous avons pu voir comment Symfony2, grâce aux mécanismes d'injection de dépendance qu'il propose, offre une solution élégante pour créer des Bundles aisément configurables. S'agissant de l'injection de dépendance, Symfony2 s'est très largement inspiré du célèbre framework Java <a href="http://www.springsource.org/" target="_blank">Spring</a>. Et c'est sans doute là que réside la plus grande force de Symfony2 : il est l'aboutissement du travail fourni par des personnes qui ont fait leurs les concepts éprouvés dans d'autres langages et technologies (Ruby-on-Rails, Django, Spring, pour ne citer que les plus connus).
</p>