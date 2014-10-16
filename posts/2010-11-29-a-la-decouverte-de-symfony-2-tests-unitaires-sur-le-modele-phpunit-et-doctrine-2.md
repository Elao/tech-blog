
<div style="float: left; margin-right: 15px; margin-bottom: 15px;">
</div>

<p class="standard">
  Bonjour à toutes et à tous,
</p>

<p class="standard">
  A l'heure où nous rédigeons ces lignes, la sortie officielle de <a href="http://symfony-reloaded.org/" target="_blank">Symfony2</a> est prévue pour mars 2011. Cette version de notre framework préféré fait actuellement l'objet d'une veille attentive de la part de la commmunauté des utilisateurs, et ELAO ne déroge pas à la règle. Nous avons donc commencé à manipuler la <em>bête</em>, et cet article, qui en appelle sans doute d'autres, n'a pas d'autre ambition que de consigner par écrit notre propre expérimentation du framework. Les voies que nous empruntons sont probablement discutables, car elles constituent davantage un parcours initiatique à la découverte du framework nouvelle mouture, qu'un mode d'emploi exempt de toute critique.
</p>

Pour débuter, nous avons concentré nos efforts sur la nouvelle philosophie de Doctrine 2 et sur la mise en oeuvre de tests unitaires du modèle à l'aide de PHPUnit.

> <div class="aparte">
>   <strong><span style="text-decoration: underline;">Prérequis</span> :</strong><br /> Pour réaliser les étapes décrites dans cet article, les bibliothèques et outils suivants sont supposés déjà installés et fonctionnels :</p> <ul>
>     <li>
>       PHP 5.3.3
>     </li>
>     <li>
>       <a href="http://www.phpunit.de/manual/current/en/installation.html" target="_blank">PHPUnit</a> dans sa version 3.5
>     </li>
>     <li>
>       Une <a href="http://symfony-reloaded.org/code" target="_blank">Sandbox</a> de la Preview de Symfony2, configurée par vos soins pour accéder à une base de données (version 2_0_PR3)
>     </li>
>   </ul>
>
>   <p>
>     La description de la mise en place et de la configuration de ces outils sort du cadre de cet article.<br /> <strong><span style="text-decoration: underline;">Ressources documentaires</span> :</strong>
>   </p>
>
>   <ul>
>     <li>
>       <a href="http://docs.symfony-reloaded.org/?page=learn" target="_blank">Symfony 2 (Preview Release)</a>
>     </li>
>     <li>
>       <a href="http://www.doctrine-project.org/projects/orm/2.0/docs/en" target="_blank">Doctrine 2</a>
>     </li>
>     <li>
>       <a href="http://www.phpunit.de/manual/current/en/index.html" target="_blank">PHPUnit</a>
>     </li>
>   </ul>
> </div>

<p class="standard">
  Pour les besoins de cet article, nous allons mettre en oeuvre un seul <em>Entity</em> afin de nous focaliser sur l'essentiel, à savoir, la manipulation des objets à l'aide de Doctrine 2. Noter que celui-ci permet de définir le modèle de plusieurs manières : à l'aide d'annotations saisies dans des classes PHP, ou bien au moyen de fichiers de configuration au format XML ou YML. Nous avons opté ici pour les annotations.
</p>

<p class="standard">
  Nous allons commencer par créer un Bundle destiné notamment à héberger notre modèle (avec Symfony 2, tout est Bundle !). Pour cela, nous allons nous placer à la racine de notre Sandbox et lancer la commande Symfony générant un nouveau bundle :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    cd ~<span class="sy0">/</span>workspace<span class="sy0">/</span>sandbox<br /> hello<span class="sy0">/</span>console init<span class="sy0">:</span>bundle <span class="st_h">'Bundle\ElaoBundle'</span>
  </div>
</div>

<div class="aparte">
  Pour connaître la liste des commandes disponibles :</p> <div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="php codecolorer">
       hello<span class="sy0">/</span>console
    </div>
  </div>

  <p>
    Pour obtenir de l'aide sur une commande :
  </p>

  <div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="php codecolorer">
       hello<span class="sy0">/</span>console help init<span class="sy0">:</span>bundle
    </div>
  </div>
</div>

<p class="standard">
  La commande <em>init:bundle</em> a créé dans le dossier <em>src</em> un sous-répertoire <em>Bundle/ElaoBundle</em> dans lequel nous allons rédiger le code de notre modèle. Mais avant toute chose, nous devons déclarer ce nouveau Bundle dans la méthode <em>registerBundles</em> du fichier <em>HelloKernel</em> :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="co2"># sandbox/hello/HelloKernel.php<br /> </span><span class="co2"># …<br /> </span><span class="kw2">public</span> <span class="kw2">function</span> registerBundles<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="br0">&#123;</span><br /> <span class="re0">$bundles</span> <span class="sy0">=</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><br /> <span class="kw2">new</span> Symfony\Framework\KernelBundle<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">,</span><br /> <span class="co2"># ...<br /> </span><span class="kw2">new</span> Application\ElaoBundle\ElaoBundle<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">,</span><br /> <span class="br0">&#41;</span><span class="sy0">;</span><br /> <span class="co2"># ...</span>
  </div>
</div>

<p class="standard">
  A présent, nous pouvons rédiger le code de notre premier Entity, et nous allons pour cela créer dans le répertoire de notre nouveau Bundle un sous-répertoire <em>Entity</em> :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
     <a href="http://www.php.net/mkdir"><span class="kw3">mkdir</span></a> src<span class="sy0">/</span>Bundle<span class="sy0">/</span>ElaoBundle<span class="sy0">/</span>Entity
  </div>
</div>

<p class="standard">
  Nous allons enregistrer notre premier Entity dans ce nouveau répertoire, et l'enrichir à l'aide d'annotations :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="co2"># sandbox/src/Bundle/ElaoBundle/Entity/Actor.php<br /> </span><span class="sy0"><!--</span>?php <span class="sy0"></</span>p<span class="sy0">--></span><br /> <br /> <span class="kw2">namespace</span> Bundle\ElaoBundle\Entity<span class="sy0">;</span><br /> <br /> <span class="co4">/**<br /> * @Entity<br /> */</span><br /> <br /> <span class="kw2">class</span> Actor<br /> <span class="br0">&#123;</span><br /> <span class="co4">/**<br /> * @Id<br /> * @Column(type="integer")<br /> * @GeneratedValue(strategy="IDENTITY")<br /> *<br /> */</span><br /> <span class="kw2">protected</span> <span class="re0">$id</span><span class="sy0">;</span><br /> <br /> <span class="co4">/**<br /> * @Column(type="string", length="255")<br /> *<br /> */</span><br /> <span class="kw2">protected</span> <span class="re0">$firstname</span><span class="sy0">;</span><br /> <br /> <span class="co4">/**<br /> * @Column(type="string", length="255", nullable=false)<br /> *<br /> */</span><br /> <span class="kw2">protected</span> <span class="re0">$lastname</span><span class="sy0">;</span><br /> <br /> <span class="co4">/**<br /> * @Column(type="integer")<br /> *<br /> */</span><br /> <span class="kw2">protected</span> <span class="re0">$birth_year</span> <span class="sy0">;</span><br /> <br /> <span class="br0">&#125;</span>
  </div>
</div>

<p class="standard">
  A présent, nous allons lancer la commande suivante :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
     hello<span class="sy0">/</span>console doctrine<span class="sy0">:</span>generate<span class="sy0">:</span>entities
  </div>
</div>

<p class="standard">
  Si vous observez le code de notre fichier <em>Actor.php</em>, vous remarquerez que Doctrine a généré les accesseurs (méthodes <em>get</em> et <em>set</em>) pour chacune des propriétés que nous avons saisies dans cette classe (noter toutefois que pour le champ <em>id</em>, seule la méthode <em>get</em> a été générée). Forts de cette première expérience, nous allons pouvoir générer les tables en base de données (nous considérons que la base de données existe déjà ; dans le cas contraire, il faut utiliser la commande <em>doctrine:database:create</em>) :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
     hello<span class="sy0">/</span>console doctrine<span class="sy0">:</span>schema<span class="sy0">:</span>create
  </div>
</div>

<p class="standard">
  A présent, nous disposons d'un Entity et d'une base de données correctement configurée, nous allons donc essayer d'enregistrer des données en base à l'aide de l'ORM. Comme nous découvrons le framework et que nous souhaitons également mettre en oeuvre PHPUnit, nous allons faire des manipulations d'entités au moyen de tests unitaires. Or, pour communiquer avec la base de données, la nouvelle version de Doctrine 2 nous oblige à passer par des <em>Entity Managers</em>. Toute la difficulté pour nous est d'obtenir un <em>Entity Manager</em> dans une application de type "console" (depuis les contrôleurs de Symfony 2, il est aisé d'obtenir un <em>Entity Manager</em> puisque les contrôleurs disposent d'une instance d'<em>Entity Manager</em> obtenue par injection de dépendances par le biais du <em>$container</em> (instance de Symfony\Component\DependencyInjection\Container), ce dernier faisant en quelque sorte office de "registre des instances obtenues par injection de dépendance" : <strong><em>$em = $this['doctrine.orm.entity_manager']</em></strong>).
</p>

<p class="standard">
  Dans le cadre des tests unitaires, nous allons passer par une instance de <em>HelloKernel</em> pour obtenir un <em>Entity Manager</em> (également au moyen de sa propriété <em>$container</em>). C'est parti, voici le contenu d'un exemple de fichier de tests (qui nécessite de créer un sous-répertoire <em>Tests</em> dans notre Bundle <em>ElaoBundle</em>) :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="sy0"><!--</span>?php <span class="sy0"></</span>p<span class="sy0">--></span><br /> <br /> <span class="co2"># sandbox/src/Bundle/ElaoBundle/Tests/ActorEntityTest.php<br /> </span><br /> <span class="kw2">namespace</span> Bundle\ElaoBundle\Tests<span class="sy0">;</span><br /> <span class="kw1">require_once</span> __DIR__<span class="sy0">.</span><span class="st_h">'/../../../../hello/HelloKernel.php'</span><span class="sy0">;</span><br /> <br /> <span class="kw2">use</span> Bundle\ElaoBundle\Entity\Actor <span class="sy0">;</span><br /> <br /> <span class="kw2">class</span> ActorEntityCase <span class="kw2">extends</span> \PHPUnit_Framework_TestCase<br /> <span class="br0">&#123;</span><br /> <span class="co4">/**<br /> * @var \Doctrine\ORM\EntityManager<br /> */</span><br /> <span class="kw2">private</span> <span class="re0">$em</span><span class="sy0">;</span><br /> <br /> <span class="kw2">public</span> <span class="kw2">function</span> __construct<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="br0">&#123;</span><br /> <span class="re0">$kernel</span> <span class="sy0">=</span> <span class="kw2">new</span> \HelloKernel<span class="br0">&#40;</span><span class="st_h">'dev'</span><span class="sy0">,</span> <span class="kw4">true</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <span class="re0">$kernel</span><span class="sy0">-&</span>gt<span class="sy0">;</span>boot<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <span class="re0">$this</span><span class="sy0">-&</span>gt<span class="sy0">;</span>em <span class="sy0">=</span> <span class="re0">$kernel</span><span class="sy0">-&</span>gt<span class="sy0">;</span>getContainer<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">-&</span>gt<span class="sy0">;</span>get<span class="br0">&#40;</span><span class="st_h">'doctrine.orm.entity_manager'</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <span class="br0">&#125;</span><br /> <br /> <span class="co4">/**<br /> * Enter description here ...<br /> * @return \Doctrine\ORM\EntityManager<br /> */</span><br /> <span class="kw2">protected</span> <span class="kw2">function</span> getEntityManager <span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="br0">&#123;</span><br /> <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-&</span>gt<span class="sy0">;</span>em<span class="sy0">;</span><br /> <span class="br0">&#125;</span><br /> <br /> <span class="kw2">public</span> <span class="kw2">function</span> testNewActor<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="br0">&#123;</span><br /> <span class="re0">$em</span> <span class="sy0">=</span> <span class="re0">$this</span><span class="sy0">-&</span>gt<span class="sy0">;</span>getEntityManager<span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> <span class="re0">$actor</span> <span class="sy0">=</span> <span class="kw2">new</span> Actor<span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> <span class="re0">$actor</span><span class="sy0">-&</span>gt<span class="sy0">;</span>setFirstname<span class="br0">&#40;</span><span class="st_h">'Patrick'</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> <span class="re0">$actor</span><span class="sy0">-&</span>gt<span class="sy0">;</span>setLastname<span class="br0">&#40;</span><span class="st_h">'Dewaere'</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> <span class="re0">$actor</span><span class="sy0">-&</span>gt<span class="sy0">;</span>setBirthYear<span class="br0">&#40;</span><span class="nu0">1947</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> <span class="re0">$em</span><span class="sy0">-&</span>gt<span class="sy0">;</span>persist<span class="br0">&#40;</span><span class="re0">$actor</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> <span class="re0">$em</span><span class="sy0">-&</span>gt<span class="sy0">;</span><a href="http://www.php.net/flush"><span class="kw3">flush</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> <br /> <span class="re0">$query</span> <span class="sy0">=</span> <span class="re0">$em</span><span class="sy0">-&</span>gt<span class="sy0">;</span>createQuery<span class="br0">&#40;</span><span class="st_h">'SELECT COUNT(act.id) FROM \Bundle\ElaoBundle\Entity\Actor act'</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <span class="re0">$count</span> <span class="sy0">=</span> <span class="re0">$query</span><span class="sy0">-&</span>gt<span class="sy0">;</span>getSingleScalarResult<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <br /> <span class="re0">$this</span><span class="sy0">-&</span>gt<span class="sy0">;</span>assertEquals<span class="br0">&#40;</span><span class="re0">$count</span><span class="sy0">,</span> <span class="nu0">1</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> <span class="br0">&#125;</span><br /> <br /> <span class="br0">&#125;</span>
  </div>
</div>

<p class="standard">
  Lançons le test pour vérifier que tout s'est correctement déroulé :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
     phpunit src<span class="sy0">/</span>Bundle<span class="sy0">/</span>ElaoBundle<span class="sy0">/</span>Tests<span class="sy0">/</span>ActorEntityTest<span class="sy0">.</span>php
  </div>
</div>

<p class="standard">
  Le test passe avec succès. Vérifions également à l'aide d'une commande DQL Doctrine que le contenu en base de données est conforme à nos attentes :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
     hello<span class="sy0">/</span>console doctrine<span class="sy0">:</span>query<span class="sy0">:</span>dql <span class="st0">"select act.firstname, act.lastname from \Bundle\ElaoBundle\Entity\Actor act"</span>
  </div>
</div>

<p class="standard">
  Nous venons donc d'écrire notre premier test unitaire sur Doctrine2 au sein d'une application Symfony 2. Cela étant, un problème subsiste : si nous lançons le test une seconde fois, nous serons confrontés à un échec puisque le nombre d'enregistrement en base de données augmente avec chaque test. Il nous faut trouver un moyen d'isoler nos données de tests ; nous pourrions soit nettoyer la base de données à l'issue du test, soit repartir d'une base "<em>blanche</em>" au début des tests. Nous allons opter pour la seconde solution (car elle nous permet de constater l'état de notre base de données à l'issue du test). Nous allons tout simplement invoquer les commandes <em>doctrine:schema:drop</em> et <em>doctrine:schema:create</em> dans la méthode <em>setUp</em> de notre test. Pour cela, nous devons obtenir une instance de \Symfony\Bundle\FrameworkBundle\Console\Application. Commençons donc par instancier cette classe dans le constructeur :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="co2"># sandbox/src/Bundle/ElaoBundle/Tests/ActorEntityTest.php<br /> </span><br /> <span class="co2"># ...<br /> </span><br /> <span class="kw2">public</span> <span class="kw2">function</span> __construct<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="br0">&#123;</span><br /> <span class="re0">$kernel</span> <span class="sy0">=</span> <span class="kw2">new</span> \HelloKernel<span class="br0">&#40;</span><span class="st_h">'dev'</span><span class="sy0">,</span> <span class="kw4">true</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <span class="re0">$kernel</span><span class="sy0">-&</span>gt<span class="sy0">;</span>boot<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <span class="re0">$this</span><span class="sy0">-&</span>gt<span class="sy0">;</span>em <span class="sy0">=</span> <span class="re0">$kernel</span><span class="sy0">-&</span>gt<span class="sy0">;</span>getContainer<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">-&</span>gt<span class="sy0">;</span>get<span class="br0">&#40;</span><span class="st_h">'doctrine.orm.entity_manager'</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <span class="re0">$this</span><span class="sy0">-&</span>gt<span class="sy0">;</span>application <span class="sy0">=</span> <span class="kw2">new</span> \Symfony\Bundle\FrameworkBundle\Console\Application<span class="br0">&#40;</span><span class="re0">$kernel</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <span class="re0">$this</span><span class="sy0">-&</span>gt<span class="sy0">;</span>application<span class="sy0">-&</span>gt<span class="sy0">;</span>setAutoExit<span class="br0">&#40;</span><span class="kw4">false</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <span class="br0">&#125;</span><br /> <br /> <span class="co2"># ...</span>
  </div>
</div>

<p class="standard">
  Nous ajoutons ensuite une méthode protégée nous permettant de lancer des commandes (comme nous envisageons de lancer deux commandes distinctes, une pour supprimer la base, l'autre pour la récréer, autant factoriser le code dans une méthode propre) ; cette méthode utilise l'instance de Console\Application que nous avons instanciée dans le constructeur :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="co2"># sandbox/src/Bundle/ElaoBundle/Tests/ActorEntityTest.php<br /> </span><span class="co2"># …<br /> </span><br /> <span class="kw2">protected</span> <span class="kw2">function</span> runConsole<span class="br0">&#40;</span><span class="re0">$command</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">Array</span></a> <span class="re0">$options</span> <span class="sy0">=</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><br /> <span class="br0">&#123;</span><br /> <span class="re0">$options</span> <span class="sy0">=</span> <a href="http://www.php.net/array_merge"><span class="kw3">array_merge</span></a><span class="br0">&#40;</span><span class="re0">$options</span><span class="sy0">,</span><a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="st_h">'command'</span> <span class="sy0">=&</span>gt<span class="sy0">;</span> <span class="re0">$command</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-&</span>gt<span class="sy0">;</span>application<span class="sy0">-&</span>gt<span class="sy0">;</span>run<span class="br0">&#40;</span><span class="kw2">new</span> \Symfony\Component\Console\Input\ArrayInput<span class="br0">&#40;</span><span class="re0">$options</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <span class="br0">&#125;</span><br /> <br /> <span class="co2"># ...</span>
  </div>
</div>

<p class="standard">
  Enfin, nous allons compléter la méthode <em>setUp</em> de notre classe de test afin d'invoquer les commandes de suppression et de regénération de la base de données avant le lancement des tests :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="co2"># sandbox/src/Bundle/ElaoBundle/Tests/ActorEntityTest.php<br /> </span><span class="co2"># …<br /> </span><br /> <span class="co4">/**<br /> * Prepares the environment before running a test.<br /> */</span><br /> <span class="kw2">protected</span> <span class="kw2">function</span> setUp <span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="br0">&#123;</span><br /> parent<span class="sy0">::</span><span class="me2">setUp</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <span class="re0">$this</span><span class="sy0">-&</span>gt<span class="sy0">;</span>runConsole<span class="br0">&#40;</span><span class="st_h">'doctrine:schema:drop'</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span> <span class="st_h">'--force'</span> <span class="sy0">=&</span>gt<span class="sy0">;</span> <span class="kw4">true</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <span class="re0">$this</span><span class="sy0">-&</span>gt<span class="sy0">;</span>runConsole<span class="br0">&#40;</span><span class="st_h">'doctrine:schema:create'</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <br /> <span class="br0">&#125;</span><br /> <br /> <span class="co2"># ...</span>
  </div>
</div>

<p class="standard">
  Voilà, si nous relançons le test, nous obtenons le résultat suivant :
</p>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    PHPUnit 3.5.5 by Sebastian Bergmann.<br /> <br /> Dropping database schema...<br /> Database schema dropped successfully<span class="sy0">!</span><br /> Creating database schema...<br /> Database schema created successfully<span class="sy0">!</span><br /> .<br /> <br /> Time: <span class="nu0"></span> seconds, Memory: 9.00Mb<br /> <br /> OK <span class="br0">&#40;</span><span class="nu0">1</span> <span class="kw3">test</span>, <span class="nu0">1</span> assertion<span class="br0">&#41;</span>
  </div>
</div>

## Conclusion

<p class="standard">
  Nous avons vu comment rédiger un test unitaire "attaquant" la base de données sous Symfony2. En l'état actuel du framework, Doctrine 2 semble avoir un peu d'avance sur Symfony2 en termes de fonctionnalités et de documentation. C'est la raison pour laquelle nous avons axé nos premiers efforts sur le modèle, et eu recours aux tests unitaires pour "faire tourner" Doctrine 2, et nous affranchir ainsi des couches <em>Contrôleurs</em> et <em>Vues</em>. Il reste toutefois de nombreux points qui n'ont pas été abordés dans cet article, s'agissant du modèle et des tests unitaires. En particulier, les prochains articles aborderont sans doute les <em>Repositories</em> de Doctrine 2 destinés notamment à l'écriture de code permettant d'interroger la base de données, les relations dans le modèle, et la configuration de PHPUnit pour lancer une série de tests. <em>Stay tuned !</em>
</p>