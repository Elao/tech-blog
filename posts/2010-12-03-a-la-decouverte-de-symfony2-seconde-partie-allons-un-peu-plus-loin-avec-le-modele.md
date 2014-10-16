
<div style="float: left; margin-right: 15px; margin-bottom: 15px;">
  <img src="/blog/wp-content/uploads/2010/11/sf2_reloaded.png" alt="sf2 reloaded A la découverte de Symfony2 (Seconde partie) : allons un peu plus loin avec le modèle"  title="A la découverte de Symfony2 (Seconde partie) : allons un peu plus loin avec le modèle" />
</div>

<p class="standard">
  Bonjour à toutes et à tous,
</p>

<p class="standard">
  Dans <a href="http://www.elao.org/developpement/a-la-decouverte-de-symfony-2-tests-unitaires-sur-le-modele-phpunit-et-doctrine-2.html" target="_blank">l&#8217;article précédent</a>, nous avons mis en place l&#8217;infrastructure nécessaire pour créer de nouveaux Entities Doctrine et lancer des tests unitaires à l&#8217;aide de PHPUnit. Nous allons aujourd&#8217;hui enrichir le modèle précédemment créé pour manipuler des relations. Le développement de Symfony2 se poursuit, les implémentations sont encore en constante évolution, et nous avons donc adopté le parti-pris de manipuler le modèle non pas au sein de contrôleurs ou dans des vues, mais au travers de tests unitaires, afin de consolider le code de notre modèle.
</p>

<p class="standard">
  Si vous avez suivi le premier article, vous devriez disposer pour le moment d&#8217;une classe Entity <i>Actor</i>. Nous allons dans un premier temps créer un second Entity <i>Movie</i>, puis ensuite définir une relation de type <i>many-to-many</i> entre ces deux Entities. Noter que cette relation donnera elle-même lieu à une Entity car elle ne se contentera pas d&#8217;associer simplement un acteur et un film, mais portera des informations supplémentaires : rôle tenu par l&#8217;acteur dans ce film, rang de l&#8217;acteur (un entier correspondant à l&#8217;importance du rôle dans le film). Cette Entity que nous dénommerons <i>Role</i> sera donc associé à une instance des classes <i>Actor</i> et <i>Movie</i>, au moyen de relations de type <i>many-to-one</i>.
</p>

<div class="aparte">
  <b><u>Avertissement</u> :</b><br /><br /> Cet article s&#8217;appuie sur la Sandbox PR3. Depuis la rédaction de cet article, la version PR4 est disponible. Celle-ci implique de nombreuses modifications, en particulier dans la définition des entités à l&#8217;aide d&#8217;annotations. Désormais, toutes les annotations de Doctrine commencent par le préfixe <i>orm</i>.<br /></p> <p>
    <u>Exemple</u> :<br />
  </p>
  
  <div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="php codecolorer">
      <span class="co4">/**<br /> &nbsp; &nbsp;* @orm:Table(name="actor")<br /> &nbsp; &nbsp;* @orm:Entity(repositoryClass="Bundle\ElaoBundle\Repository\ActorRepository")<br /> &nbsp; &nbsp;*/</span><br /> &nbsp;<span class="co2"># ...<br /> </span><span class="co4">/**<br /> &nbsp; &nbsp;* @orm:Column(name="id", type="integer")<br /> &nbsp; &nbsp;* @orm:Id<br /> &nbsp; &nbsp;* @orm:GeneratedValue(strategy="AUTO")<br /> &nbsp; &nbsp;*/</span>
    </div>
  </div>
</div>

Commençons donc par créer notre Entity ***Movie***. Nous nous soucierons dans un deuxième temps de l&#8217;Entity *Role* et de la définition des relations.

Voici une proposition de classe représentant l&#8217;Entity ***Movie*** :

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    # sandbox/src/Bundle/ElaoBundle/Entity/Movie.php<br /> <span class="kw2"><?php</span><br /> <br /> <span class="kw2">namespace</span> Bundle\ElaoBundle\Entity<span class="sy0">;</span><br /> <br /> <span class="co4">/**<br /> &nbsp;* @Entity<br /> &nbsp;*/</span><br /> <br /> <span class="kw2">class</span> Movie<br /> <span class="br0">&#123;</span><br /> &nbsp; &nbsp; <span class="co4">/**<br /> &nbsp; &nbsp; &nbsp;* @Id<br /> &nbsp; &nbsp; &nbsp;* @Column(type="integer")<br /> &nbsp; &nbsp; &nbsp;* @GeneratedValue(strategy="IDENTITY")<br /> &nbsp; &nbsp; &nbsp;*<br /> &nbsp; &nbsp; &nbsp;*/</span><br /> &nbsp; &nbsp; <span class="kw2">protected</span> <span class="re0">$id</span><span class="sy0">;</span><br /> <br /> &nbsp; &nbsp; <span class="co4">/**<br /> &nbsp; &nbsp; &nbsp;* @Column(type="string", length="255", nullable=false)<br /> &nbsp; &nbsp; &nbsp;*<br /> &nbsp; &nbsp; &nbsp;*/</span><br /> &nbsp; &nbsp; <span class="kw2">protected</span> <span class="re0">$title</span><span class="sy0">;</span><br /> <br /> &nbsp; &nbsp; <span class="co4">/**<br /> &nbsp; &nbsp; &nbsp;* @Column(type="integer", nullable=true)<br /> &nbsp; &nbsp; &nbsp;*<br /> &nbsp; &nbsp; &nbsp;*/</span><br /> &nbsp; &nbsp; <span class="kw2">protected</span> <span class="re0">$release_year</span> <span class="sy0">;</span><br /> <br /> <span class="br0">&#125;</span>
  </div>
</div>

<p class="standard">
  A présent, nous allons demander à Doctrine de construire notre modèle en se basant sur nos annotations :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    &nbsp;hello<span class="sy0">/</span>console doctrine<span class="sy0">:</span>generate<span class="sy0">:</span>entities
  </div>
</div>

<p class="standard">
  Enfin, nous créons les tables en base de données :
</p>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    hello<span class="sy0">/</span>console doctrine:schema:drop<br /> hello<span class="sy0">/</span>console doctrine:schema:create
  </div>
</div>

<div class="aparte">
  <u>Rappel</u> : <br /><br /> La commande [php] doctrine:database:create [/php] permet de créer la base de données vide si elle n&#8217;existait pas auparavant.<br />
</div>

<div class="aparte">
  <u>Remarque</u> :<br /><br /> Il existe une commande permettant de mettre à jour le schéma de la base de données en comparant notre modèle et le schéma de la base de données : </br><br /> [php] hello/console doctrine:schema:update [/php]<br /> L&#8217;option <i>&#8211;dump-sql</i> affiche dans la console les instructions SQL de mise à jour, l&#8217;option <i>&#8211;force</i> les exécute réellement en base de données.<br /><br /> Noter également que la commande <i>doctrine:schema:create</i> comporte aussi une option <i>&#8211;dump-sql</i> permettant d&#8217;afficher les scripts SQL de création des tables.
</div>

<p class="standard">
  Penchons-nous sur l&#8217;Entity <i><b>Role</b></i> et sur ses relations avec les Entities <i>Movie</i> et <i>Actor</i>. Tout d&#8217;abord, voici le code de la classe <i>Role</i> :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="co2"># sandbox/src/Bundle/ElaoBundle/Entity/Role.php<br /> </span><br /> <span class="kw2">namespace</span> Bundle\ElaoBundle\Entity<span class="sy0">;</span><br /> <br /> <span class="kw2">use</span> Doctrine\Common\Collections\ArrayCollection<span class="sy0">;</span><br /> <br /> <span class="co4">/**<br /> &nbsp;* @Entity<br /> &nbsp;*/</span><br /> <br /> <span class="kw2">class</span> Role<br /> <span class="br0">&#123;</span><br /> &nbsp; &nbsp; <span class="co4">/**<br /> &nbsp; &nbsp; &nbsp;* @Id<br /> &nbsp; &nbsp; &nbsp;* @Column(type="integer")<br /> &nbsp; &nbsp; &nbsp;* @GeneratedValue(strategy="IDENTITY")<br /> &nbsp; &nbsp; &nbsp;*<br /> &nbsp; &nbsp; &nbsp;*/</span><br /> &nbsp; &nbsp; <span class="kw2">protected</span> <span class="re0">$id</span><span class="sy0">;</span><br /> <br /> &nbsp; &nbsp; <span class="co4">/**<br /> &nbsp; &nbsp; &nbsp;*<br /> &nbsp; &nbsp; &nbsp;* @ManyToOne(targetEntity="Bundle\ElaoBundle\Entity\Actor", inversedBy="roles")<br /> &nbsp; &nbsp; &nbsp;* @JoinColumn(name="actor_id", referencedColumnName="id")<br /> &nbsp; &nbsp; &nbsp;*<br /> &nbsp; &nbsp; &nbsp;*/</span><br /> &nbsp; &nbsp; <span class="kw2">protected</span> <span class="re0">$actor</span> <span class="sy0">;</span><br /> <br /> &nbsp; &nbsp; <span class="co4">/**<br /> &nbsp; &nbsp; &nbsp;*<br /> &nbsp; &nbsp; &nbsp;* @ManyToOne(targetEntity="Bundle\ElaoBundle\Entity\Movie", inversedBy="roles")<br /> &nbsp; &nbsp; &nbsp;* @JoinColumn(name="movie_id", referencedColumnName="id")<br /> &nbsp; &nbsp; &nbsp;*<br /> &nbsp; &nbsp; &nbsp;*/</span><br /> &nbsp; &nbsp; <span class="kw2">protected</span> <span class="re0">$movie</span> <span class="sy0">;</span><br /> <br /> &nbsp; &nbsp; <span class="co4">/**<br /> &nbsp; &nbsp; &nbsp;* @Column(type="integer")<br /> &nbsp; &nbsp; &nbsp;*/</span><br /> &nbsp; &nbsp; <span class="kw2">protected</span> <span class="re0">$rank</span> <span class="sy0">;</span><br /> <br /> &nbsp; &nbsp; <span class="co4">/**<br /> &nbsp; &nbsp; &nbsp;* @Column(type="string", length="255", nullable=true)<br /> &nbsp; &nbsp; &nbsp;*/</span><br /> &nbsp; &nbsp; <span class="kw2">protected</span> <span class="re0">$plays</span> <span class="sy0">;</span>
  </div>
</div>

<p class="standard">
  Nous allons également définir pour chacune des classes <i>Actor</i> et <i>Movie</i> une relation de type <i>one-to-many</i> vers notre Entity <i>Role</i>, en déclarant un membre <i>$roles</i> :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="co2"># sandbox/src/Bundle/ElaoBundle/Entity/Actor.php<br /> </span><span class="kw2">use</span> Doctrine\Common\Collections\ArrayCollection<span class="sy0">;</span><br /> <span class="co2"># …<br /> </span> &nbsp; &nbsp;<span class="co4">/**<br /> &nbsp; &nbsp; &nbsp;* @OneToMany(targetEntity="Bundle\ElaoBundle\Entity\Role", mappedBy="actor")<br /> &nbsp; &nbsp; &nbsp;*/</span><br /> &nbsp; &nbsp; <span class="kw2">protected</span> <span class="re0">$roles</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; <br /> &nbsp; &nbsp; <span class="co1">// Recommandé dans la documentation de Doctrine 2 ; permet d'utiliser la méthode $this->getRoles()->add(Role) même si $this->roles est vide</span><br /> &nbsp; &nbsp; <span class="kw2">public</span> <span class="kw2">function</span> __construct<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> &nbsp; &nbsp; <span class="br0">&#123;</span><br /> &nbsp; &nbsp; &nbsp; &nbsp; <span class="re0">$this</span><span class="sy0">-></span><span class="me1">roles</span> <span class="sy0">=</span> <span class="kw2">new</span> ArrayCollection<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; &nbsp; <span class="br0">&#125;</span><br /> <span class="co2">#…</span>
  </div>
</div>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="co2"># sandbox/src/Bundle/ElaoBundle/Entity/Movie.php<br /> </span><span class="kw2">use</span> Doctrine\Common\Collections\ArrayCollection<span class="sy0">;</span><br /> <span class="co2"># …<br /> </span> &nbsp; &nbsp;<span class="co4">/**<br /> &nbsp; &nbsp; &nbsp;* @OneToMany(targetEntity="Bundle\ElaoBundle\Entity\Role", mappedBy="movie")<br /> &nbsp; &nbsp; &nbsp;*/</span><br /> &nbsp; &nbsp; <span class="kw2">protected</span> <span class="re0">$roles</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; <br /> &nbsp; &nbsp; <span class="kw2">public</span> <span class="kw2">function</span> __construct<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> &nbsp; &nbsp; <span class="br0">&#123;</span><br /> &nbsp; &nbsp; &nbsp; &nbsp; <span class="re0">$this</span><span class="sy0">-></span><span class="me1">roles</span> <span class="sy0">=</span> <span class="kw2">new</span> ArrayCollection<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; &nbsp; <span class="br0">&#125;</span><br /> <span class="co2">#…</span>
  </div>
</div>

<div class="aparte">
  <u>Remarque</u> :<br /><br /> Il devrait être possible de ne pas mentionner le chemin complet des <i>targetEntity</i> situés dans le même namespace mais il semble exister un bug avec la version de la <i>Preview</i> utilisée dans cet article (PR3). En effet, si l&#8217;on écrit :<br /><br /> [php] @OneToMany(targetEntity=&#8221;Role&#8221;, mappedBy=&#8221;movie&#8221;) [/php]<br /> Nous obtenons alors une signature erronée avec un anti-slash parasite sur la méthode <i>addProducts</i> après avoir invoqué le script <i>doctrine:generate:entities</i> :<br /></p> <div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="php codecolorer">
      &nbsp; &nbsp;<span class="co1">// La signature de la méthode obtenue est erronée ; nous devrions avoir un paramètre "Product $products" et non pas "\Product $products" :</span><br /> &nbsp; &nbsp;<span class="kw2">public</span> <span class="kw2">function</span> addProducts<span class="br0">&#40;</span>\Product <span class="re0">$products</span><span class="br0">&#41;</span>
    </div>
  </div>
  
  <p>
    Si vous ne souhaitez pas écrire le chemin complet de vos <i>targetEntities</i>, pensez à supprimer cet anti-slash parasite. </div> <div class="aparte">
      <ul>
        <li>
          Pour en savoir plus les annotations de Doctrine 2 : <a href="http://www.doctrine-project.org/projects/orm/2.0/docs/reference/annotations-reference/en#annotations-reference" target="_blank">Annotations Reference</a>.
        </li>
        <li>
          Pour approfondir la question des relations dans Doctrine 2 : <a href="http://www.doctrine-project.org/projects/orm/2.0/docs/reference/working-with-associations/en#working-with-associations" target="_blank">Working with Associations</a>.
        </li>
      </ul>
    </div>
    
    <p class="standard">
      Nos relations étant à présent définies, nous allons reconstuire nos classes <i>Entities</i> et les tables en base de données :
    </p>
    
    <div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
      <div class="bash codecolorer">
        &nbsp; &nbsp; hello<span class="sy0">/</span>console doctrine:generate:entities<br /> &nbsp; &nbsp; hello<span class="sy0">/</span>console doctrine:schema:drop <span class="re5">--force</span><br /> &nbsp; &nbsp; hello<span class="sy0">/</span>console doctrine:schema:create
      </div>
    </div>
    
    <p class="standard">
      Noter dans le code des deux entités du côté &#8220;<i>one</i>&#8221; des relations <i>one-to-many</i> (<i>Actor</i> et <i>Movie</i>) l&#8217;ajout par Doctrine d&#8217;une méthode <i>addRoles(Role)</i>. Noter également que pour ces deux entités nous avons redéfini le constructeur pour initiliser une collection vide, instance de <i>ArrayCollection</i>, car elle permet d&#8217;appeler la méthode <i>$this->getRoles()->add(Role)</i> même si l&#8217;entité n&#8217;est encore liée à aucun <i>Role</i>.
    </p>
    
    <p class="standard">
      Place à présent aux tests unitaires pour manipuler nos entités et leurs relations. Nous allons compléter la classe de test <i>ActorEntityTest</i> que nous avions ébauchée dans <a href="http://www.elao.org/developpement/a-la-decouverte-de-symfony-2-tests-unitaires-sur-le-modele-phpunit-et-doctrine-2.html" target="_blank">notre article précédent</a> et en particulier, nous allons y ajouter une méthode <i>testRoles</i> dont voici le contenu (noter que certains passages du code sont inhibés sous formes de commentaires ; nous y reviendrons) :
    </p>
    
    <div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
      <div class="php codecolorer">
        <span class="co2"># src/Bundle/ElaoBundle/Tests/ActorEntityTest:<br /> </span><span class="co2"># …<br /> </span> &nbsp; &nbsp;<span class="kw2">public</span> <span class="kw2">function</span> testRoles<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> &nbsp; &nbsp; <span class="br0">&#123;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$em</span> <span class="sy0">=</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">getEntityManager</span><span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> <br /> &nbsp; &nbsp; &nbsp; <span class="co1">// 1. Create a new Actor and persist him</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$actor</span> <span class="sy0">=</span> <span class="kw2">new</span> Actor<span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$actor</span><span class="sy0">-></span><span class="me1">setFirstname</span><span class="br0">&#40;</span><span class="st_h">'Patrick'</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$actor</span><span class="sy0">-></span><span class="me1">setLastname</span><span class="br0">&#40;</span><span class="st_h">'Dewaere'</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$actor</span><span class="sy0">-></span><span class="me1">setBirthYear</span><span class="br0">&#40;</span><span class="nu0">1947</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$em</span><span class="sy0">-></span><span class="me1">persist</span><span class="br0">&#40;</span><span class="re0">$actor</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$em</span><span class="sy0">-></span><a href="http://www.php.net/flush"><span class="kw3">flush</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> <br /> &nbsp; &nbsp; &nbsp; <span class="re0">$query</span> <span class="sy0">=</span> <span class="re0">$em</span><span class="sy0">-></span><span class="me1">createQuery</span><span class="br0">&#40;</span><span class="st_h">'SELECT COUNT(act.id) FROM \Bundle\ElaoBundle\Entity\Actor act'</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$count</span> <span class="sy0">=</span> <span class="re0">$query</span><span class="sy0">-></span><span class="me1">getSingleScalarResult</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <br /> &nbsp; &nbsp; &nbsp; <span class="re0">$this</span><span class="sy0">-></span><span class="me1">assertEquals</span><span class="br0">&#40;</span><span class="re0">$count</span><span class="sy0">,</span> <span class="nu0">1</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> <br /> &nbsp; &nbsp; &nbsp; <span class="co1">// 2. Create a new female Actor and persist her</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$actress</span> <span class="sy0">=</span> <span class="kw2">new</span> Actor<span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$actress</span><span class="sy0">-></span><span class="me1">setFirstname</span><span class="br0">&#40;</span><span class="st_h">'Brigitte'</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$actress</span><span class="sy0">-></span><span class="me1">setLastname</span><span class="br0">&#40;</span><span class="st_h">'Fossey'</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$actress</span><span class="sy0">-></span><span class="me1">setBirthYear</span><span class="br0">&#40;</span><span class="nu0">1946</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$em</span><span class="sy0">-></span><span class="me1">persist</span><span class="br0">&#40;</span><span class="re0">$actress</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$em</span><span class="sy0">-></span><a href="http://www.php.net/flush"><span class="kw3">flush</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$count</span> <span class="sy0">=</span> <span class="re0">$query</span><span class="sy0">-></span><span class="me1">getSingleScalarResult</span><span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> <br /> &nbsp; &nbsp; &nbsp; <span class="re0">$this</span><span class="sy0">-></span><span class="me1">assertEquals</span><span class="br0">&#40;</span><span class="re0">$count</span><span class="sy0">,</span> <span class="nu0">2</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> <br /> &nbsp; &nbsp; &nbsp; <span class="co1">// 3. Create a new Movie and persist it</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$movie</span> <span class="sy0">=</span> <span class="kw2">new</span> Movie<span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$movie</span><span class="sy0">-></span><span class="me1">setTitle</span><span class="br0">&#40;</span><span class="st_h">'Un Mauvais Fils'</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$movie</span><span class="sy0">-></span><span class="me1">setReleaseYear</span><span class="br0">&#40;</span><span class="nu0">1980</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$em</span><span class="sy0">-></span><span class="me1">persist</span><span class="br0">&#40;</span><span class="re0">$movie</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$em</span><span class="sy0">-></span><a href="http://www.php.net/flush"><span class="kw3">flush</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> <br /> &nbsp; &nbsp; &nbsp; <span class="re0">$movie_query</span> <span class="sy0">=</span> <span class="re0">$em</span><span class="sy0">-></span><span class="me1">createQuery</span><span class="br0">&#40;</span><span class="st_h">'SELECT COUNT(mov.id) FROM \Bundle\ElaoBundle\Entity\Movie mov'</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$movie_count</span> <span class="sy0">=</span> <span class="re0">$movie_query</span><span class="sy0">-></span><span class="me1">getSingleScalarResult</span><span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> <br /> &nbsp; &nbsp; &nbsp; <span class="re0">$this</span><span class="sy0">-></span><span class="me1">assertEquals</span><span class="br0">&#40;</span><span class="re0">$movie_count</span><span class="sy0">,</span> <span class="nu0">1</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> <br /> &nbsp; &nbsp; &nbsp; <span class="co1">// 4. First male character / role</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$m_role</span> <span class="sy0">=</span> <span class="kw2">new</span> Role<span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$m_role</span><span class="sy0">-></span><span class="me1">setActor</span><span class="br0">&#40;</span><span class="re0">$actor</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$m_role</span><span class="sy0">-></span><span class="me1">setMovie</span><span class="br0">&#40;</span><span class="re0">$movie</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$m_role</span><span class="sy0">-></span><span class="me1">setPlays</span><span class="br0">&#40;</span><span class="st_h">'Bruno'</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$m_role</span><span class="sy0">-></span><span class="me1">setRank</span><span class="br0">&#40;</span><span class="nu0">1</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="co1">// $actor->addRoles($m_role) ;</span><br /> &nbsp; &nbsp; &nbsp; <span class="co1">// $movie->addRoles($m_role) ;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$em</span><span class="sy0">-></span><span class="me1">persist</span><span class="br0">&#40;</span><span class="re0">$m_role</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> <br /> &nbsp; &nbsp; &nbsp; <span class="co1">// 5. First female character / role</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$f_role</span> <span class="sy0">=</span> <span class="kw2">new</span> Role<span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$f_role</span><span class="sy0">-></span><span class="me1">setActor</span><span class="br0">&#40;</span><span class="re0">$actress</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$f_role</span><span class="sy0">-></span><span class="me1">setMovie</span><span class="br0">&#40;</span><span class="re0">$movie</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$f_role</span><span class="sy0">-></span><span class="me1">setPlays</span><span class="br0">&#40;</span><span class="st_h">'Catherine'</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$f_role</span><span class="sy0">-></span><span class="me1">setRank</span><span class="br0">&#40;</span><span class="nu0">2</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$em</span><span class="sy0">-></span><span class="me1">persist</span><span class="br0">&#40;</span><span class="re0">$f_role</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> <br /> &nbsp; &nbsp; &nbsp; <span class="re0">$em</span><span class="sy0">-></span><a href="http://www.php.net/flush"><span class="kw3">flush</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="co1">// $em->clear() ;</span><br /> <br /> &nbsp; &nbsp; &nbsp; <span class="re0">$role_query</span> <span class="sy0">=</span> <span class="re0">$em</span><span class="sy0">-></span><span class="me1">createQuery</span><span class="br0">&#40;</span><span class="st_h">'SELECT COUNT(role.id) FROM \Bundle\ElaoBundle\Entity\Role role'</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> &nbsp; &nbsp; &nbsp; <span class="re0">$role_count</span> <span class="sy0">=</span> <span class="re0">$role_query</span><span class="sy0">-></span><span class="me1">getSingleScalarResult</span><span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> <br /> &nbsp; &nbsp; &nbsp; <span class="re0">$this</span><span class="sy0">-></span><span class="me1">assertEquals</span><span class="br0">&#40;</span><span class="re0">$role_count</span><span class="sy0">,</span> <span class="nu0">2</span><span class="br0">&#41;</span> <span class="sy0">;</span><br /> <br /> &nbsp; &nbsp; &nbsp; <span class="co1">// /$dewaere = $em->getRepository('Bundle\ElaoBundle\Entity\Actor')->findOneBy(array('lastname' => 'Dewaere')) ;</span><br /> &nbsp; &nbsp; &nbsp; <span class="co1">// $this->assertEquals($dewaere->getLastname(), 'Dewaere') ;</span><br /> &nbsp; &nbsp; &nbsp; <span class="co1">// $this->assertEquals(1, $dewaere->getRoles()->count()) ;</span><br /> &nbsp; &nbsp;<span class="br0">&#125;</span><br /> &nbsp; &nbsp;<span class="co2">#...</span>
      </div>
    </div>
    
    <div class="aparte">
      <u>Astuce</u> :<br /><br /> Dans les requêtes DQL, il est possible d&#8217;utiliser un raccourci <i>MyBundle:MyEntity</i> très pratique au lieu des namespaces complets : <br /><br /> [php] $role_query = $em->createQuery(&#8216;SELECT COUNT(role.id) FROM ElaoBundle:Role role&#8217;) [/php]
    </div>
    
    <p class="standard">
      Force est de constater que ce code est assez basique : nous instancions deux <i>Actor</i> (repères 1 et 2), nous les sauvegardons en base de données par le biais de l&#8217;<i>EntityManager</i>, puis nous nous assurons que le nombre d&#8217;enregistrements en base de données est conforme à nos attentes. Je vous avais prévenus, le code est primaire ! Idem pour la création et la sauvegarde d&#8217;une instance de <i>Movie</i> (repère 3), puis de deux rôles (repères 4 et 5) pour nos acteurs. Si nous lançons le test, tout devrait se dérouler de manière nominale et nous devrions obtenir le statut <i>OK</i> avec un bilan final d&#8217;un test et quatre assertions :
    </p>
    
    <div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
      <div class="php codecolorer">
        &nbsp;phpunit src<span class="sy0">/</span>Bundle<span class="sy0">/</span>ElaoBundle<span class="sy0">/</span>Tests<span class="sy0">/</span>ActorEntityTest<span class="sy0">.</span>php
      </div>
    </div>
    
    <p class="standard">
      Vous devriez également constater que les enregistrements en base de données ne présentent pas de surprise, en particulier la table <i>Role</i> comporte bien les relations attendues. Nous sommes sur la bonne voie, nous commençons à mieux discerner la philosophie de Doctrine 2, nous pouvons être fiers de nous. Comme nous sommes en confiance, nous allons en profiter pour ajouter trois lignes de code à la fin de notre méthode de test (il suffit de les décommenter dans l&#8217;extrait de code ci-dessus). A priori, rien d&#8217;extraordinaire, nous récupérons en base de données un acteur, nous nous assurons que c&#8217;est bien celui que nous voulions en vérifiant la valeur de la méthode <i>getLastName()</i>, et enfin, nous nous assurons que cet acteur est bien associé à un rôle. Nous sommes impatients de poursuivre l&#8217;exploration de Doctrine 2, nous serions tentés d&#8217;enchaîner immédiatement sur d&#8217;autres aspects de l&#8217;ORM, mais nous allons tout de même lancer le test par acquis de conscience après avoir décommenté les trois dernières lignes de code de la méthode <i>testRoles()</i>. Et là, mauvaise surprise ! Le test échoue.
    </p>
    
    <p class="standard">
      Que s&#8217;est-il exactement passé ? La ligne incriminée est la suivante :
    </p>
    
    <div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
      <div class="php codecolorer">
        <span class="re0">$this</span><span class="sy0">-></span><span class="me1">assertEquals</span><span class="br0">&#40;</span><span class="nu0">1</span><span class="sy0">,</span> <span class="re0">$dewaere</span><span class="sy0">-></span><span class="me1">getRoles</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">-></span><a href="http://www.php.net/count"><span class="kw3">count</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span> <span class="sy0">;</span>
      </div>
    </div>
    
    <p class="standard">
      Nous nous attendions à ce que notre instance d&#8217;acteur soit associé à un rôle, mais PHPUnit nous indique que la valeur retournée par la méthode <i>count()</i> est égale à zéro, alors que nos données en base sont correctes. Il semblerait que ce résultat inattendu (tout du moins pour les novices que nous sommes) s&#8217;explique par le décalage existant entre les informations sur notre acteur présent en base de données et notre instance d&#8217;acteur géré par l&#8217;<i>EntityManager</i>. Il est fort probable que l&#8217;objet <i>$dewaere</i> que nous avons récupéré en base de données continue en fait d&#8217;être géré par l&#8217;<i>EntityManager</i> mais dans son état initial (il correspond à l&#8217;entité <i>$actor</i>) ; et dans cet état initial, la propriété <i>$roles</i> de l&#8217;objet <i>$dewaere</i> représente une collection vide. En effet, dans le repère 4, nous avons créé une nouvelle instance de <i>Role</i> et les informations que nous lui avons fournies étaient suffisantes pour sauvegarder correctement cette instance en base de données. En revanche, du point de vue purement <i>Objet</i>, nous manipulons deux entités liées selon une <b>association bidirectionnelle</b> (l&#8217;entité <i>Role</i>, qui est le porteur de l&#8217;association &#8211; <i>owning side</i> dans le jargon de Doctrine &#8211; possède un attribut <i>$actor</i> et l&#8217;entité <i>Actor</i>, qui constitue l&#8217;opposé de la relation &#8211; <i>inverse side</i> &#8211; possède une collection <i>$roles</i>, ce qui nous permet donc d&#8217;obtenir un acteur à partir d&#8217;un rôle, et une liste de rôles depuis un acteur, d&#8217;où le terme &#8220;<i>bidirectionnelle</i>&#8220;). Or dans le cas d&#8217;une telle association, pour que le modèle Objet reste cohérent, il est nécessaire de définir explicitement les relations de chaque côté de l&#8217;association. Pour nous en convaincre, nous allons décommenter les deux lignes du repère 4 qui permettent d&#8217;ajouter notre rôle récemment instancié dans les collections <i>$roles</i> de nos deux entités <i>Actor</i> et <i>Movie</i> (puisque cette dernière entité est également concernée). Si nous relançons notre test unitaire, celui-ci passe puisque l&#8217;entité <i>$dewaere</i> dispose désormais d&#8217;une collection <i>$roles</i> conforme à nos attentes.
    </p>
    
    <p class="standard">
      Enfin, je vous propose d&#8217;inhiber à nouveau la ligne où nous ajoutons le nouveau rôle à la collection <i>$roles</i> (<i>$actor->addRoles($m_role) ;</i>), puis décommenter la ligne <i>$em->clear()</i>, et enfin relancer le test : celui-ci devrait également passer avec succès. Bien que nous ayons laissé notre entité <i>$actor</i> dans un état incohérent en ne définissant pas les associations nécessaires, l&#8217;entité <i>$dewaere</i> ne correspond plus à l&#8217;entité gérée par l&#8217;<i>EntityManager</i> (puisque nous avons &#8220;nettoyé&#8221; ce dernier) et il possède donc bien une collection de <i>$roles</i> cohérente avec les données présentes en base. Cela étant, cette dernière démonstration visait surtout à nous faire mieux comprendre les mécanismes de Doctrine 2, mais ce n&#8217;est bien entendu pas une manière recommandable de passer des tests unitaires avec succès. Le principal enseignement que nous tirerons de ces manipulations, c&#8217;est que pour obtenir des entités viables dans le cadre d&#8217;une relation bidirectionnelle avec l&#8217;ORM Doctrine 2, nous devons toujours mettre à jour les relations des deux côtés de l&#8217;association.
    </p>
    
    <h2>
      Conclusion
    </h2>
    
    <p class="standard">
      A travers cet exemple à la base très simple, nous avons pu remarquer que Doctrine 2 réservait quelques pièges pour qui souhaite s&#8217;y initier. En particulier, la gestion des entités par l&#8217;<i>EntityManager</i> nous amène à envisager l&#8217;exploitation des objets selon des principes auxquels Doctrine 1 ne nous avait pas habitués.
    </p>
    
    <p class="standard">
      Nous aurons l&#8217;occasion de revenir sur l&#8217;ORM Doctrine 2 et son expérimentation au moyen de tests unitaires lors de futurs articles. Nous y aborderons notamment les <i>EntityRepositories</i>.
    </p>
    
    <h2>
      Remerciements
    </h2>
    
    <p>
      Mille mercis à Benjamin pour ses remarques et notamment pour ses astuces très utiles.
    </p>