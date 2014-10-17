# **[EDIT]** Cet article est devenu obsolète, il n'est donc pas recommandé de s'en servir comme base de travail.

<div style="float: left; margin-right: 15px; margin-bottom: 15px;">
  <img src="/blog/medias/2010-12-03-a-la-decouverte-de-symfony2-seconde-partie-allons-un-peu-plus-loin-avec-le-modele/sf2_reloaded.png" alt="sf2 reloaded A la découverte de Symfony2 (Seconde partie) : allons un peu plus loin avec le modèle"  title="A la découverte de Symfony2 (Seconde partie) : allons un peu plus loin avec le modèle" />
</div>

Bonjour à toutes et à tous,

Dans <a href="/blog/a-la-decouverte-de-symfony-2-tests-unitaires-sur-le-modele-phpunit-et-doctrine-2" target="_blank">l'article précédent</a>, nous avons mis en place l'infrastructure nécessaire pour créer de nouveaux Entities Doctrine et lancer des tests unitaires à l'aide de PHPUnit. Nous allons aujourd'hui enrichir le modèle précédemment créé pour manipuler des relations. Le développement de Symfony2 se poursuit, les implémentations sont encore en constante évolution, et nous avons donc adopté le parti-pris de manipuler le modèle non pas au sein de contrôleurs ou dans des vues, mais au travers de tests unitaires, afin de consolider le code de notre modèle.

Si vous avez suivi le premier article, vous devriez disposer pour le moment d'une classe Entity <i>Actor</i>. Nous allons dans un premier temps créer un second Entity <i>Movie</i>, puis ensuite définir une relation de type <i>many-to-many</i> entre ces deux Entities. Noter que cette relation donnera elle-même lieu à une Entity car elle ne se contentera pas d'associer simplement un acteur et un film, mais portera des informations supplémentaires : rôle tenu par l'acteur dans ce film, rang de l'acteur (un entier correspondant à l'importance du rôle dans le film). Cette Entity que nous dénommerons <i>Role</i> sera donc associé à une instance des classes <i>Actor</i> et <i>Movie</i>, au moyen de relations de type <i>many-to-one</i>.

<strong>Avertissement</strong>: Cet article s'appuie sur la Sandbox PR3. Depuis la rédaction de cet article, la version PR4 est disponible. Celle-ci implique de nombreuses modifications, en particulier dans la définition des entités à l'aide d'annotations. Désormais, toutes les annotations de Doctrine commencent par le préfixe <i>orm</i>.

** Exemple: **
```
/**
 * 
 * @orm:Table(name="actor")
 * @orm:Entity(repositoryClass="Bundle\ElaoBundle\Repository\ActorRepository")
 */

# ...

/**
 * @orm:Column(name="id", type="integer")
 * @orm:Id
 * @orm:GeneratedValue(strategy="AUTO")
 */
```

Commençons donc par créer notre Entity ***Movie***. Nous nous soucierons dans un deuxième temps de l'Entity *Role* et de la définition des relations.

Voici une proposition de classe représentant l'Entity ***Movie*** :

```
# sandbox/src/Bundle/ElaoBundle/Entity/Movie.php
<?php

namespace Bundle\ElaoBundle\Entity;

/**
 * @Entity
 */

class Movie
{
    /**
     * @Id
     * @Column(type="integer")
     * @GeneratedValue(strategy="IDENTITY")
     *
     */
    protected $id;

    /**
     * @Column(type="string", length="255", nullable=false)
     *
     */
    protected $title;

    /**
     * @Column(type="integer", nullable=true)
     *
     */
    protected $release_year ;

}
```

A présent, nous allons demander à Doctrine de construire notre modèle en se basant sur nos annotations :

```
hello/console doctrine:generate:entities
```

Enfin, nous créons les tables en base de données :

```
hello/console doctrine:schema:drop
hello/console doctrine:schema:create
```

** Rappel: **

La commande ``` doctrine:database:create ``` permet de créer la base de données vide si elle n'existait pas auparavant.

** Remarque: ** 

Il existe une commande permettant de mettre à jour le schéma de la base de données en comparant notre modèle et le schema de la base de données : ``` hello/console doctrine:schema:update ``` L'option <i>-dump-sql</i> affiche dans la console les instructions SQL de mise à jour, l'option <i>-force</i> les exécute réellement en base de données.<br /><br /> Noter également que la commande <i>doctrine:schema:create</i> comporte aussi une option <i>-dump-sql</i> permettant d'afficher les scripts SQL de création des tables.


Penchons-nous sur l'Entity <i><b>Role</b></i> et sur ses relations avec les Entities <i>Movie</i> et <i>Actor</i>. Tout d'abord, voici le code de la classe <i>Role</i> :

```
# sandbox/src/Bundle/ElaoBundle/Entity/Role.php

namespace Bundle\ElaoBundle\Entity;

use Doctrine\Common\Collections\ArrayCollection;

/**
 * @Entity
 */

class Role
{
    /**
     * @Id
     * @Column(type="integer")
     * @GeneratedValue(strategy="IDENTITY")
     *
     */
    protected $id;

    /**
     *
     * @ManyToOne(targetEntity="Bundle\ElaoBundle\Entity\Actor", inversedBy="roles")
     * @JoinColumn(name="actor_id", referencedColumnName="id")
     *
     */
    protected $actor ;

    /**
     *
     * @ManyToOne(targetEntity="Bundle\ElaoBundle\Entity\Movie", inversedBy="roles")
     * @JoinColumn(name="movie_id", referencedColumnName="id")
     *
     */
    protected $movie ;

    /**
     * @Column(type="integer")
     */
    protected $rank ;

    /**
     * @Column(type="string", length="255", nullable=true)
     */
    protected $plays ;
```

Nous allons également définir pour chacune des classes <i>Actor</i> et <i>Movie</i> une relation de type <i>one-to-many</i> vers notre Entity <i>Role</i>, en déclarant un membre <i>$roles</i> :

```
# sandbox/src/Bundle/ElaoBundle/Entity/Actor.php
use Doctrine\Common\Collections\ArrayCollection;
# ...
/**
 * @OneToMany(targetEntity="Bundle\ElaoBundle\Entity\Role", mappedBy="actor")
 */
protected $roles ;

// Recommandé dans la documentation de Doctrine 2 ; permet d'utiliser la méthode $this->getRoles()->add(Role) même si $this->roles est vide
public function __construct()
{
    $this->roles = new ArrayCollection();
}
```

```
# sandbox/src/Bundle/ElaoBundle/Entity/Movie.php
use Doctrine\Common\Collections\ArrayCollection;
# ...
   /**
     * @OneToMany(targetEntity="Bundle\ElaoBundle\Entity\Role", mappedBy="movie")
     */
    protected $roles ;
   
    public function __construct()
    {
        $this->roles = new ArrayCollection();
    }
#…
```

** Remarque : ** 
Il devrait être possible de ne pas mentionner le chemin complet des <i>targetEntity</i> situés dans le même namespace mais il semble exister un bug avec la version de la <i>Preview</i> utilisée dans cet article (PR3). En effet, si l'on écrit : ``` @OneToMany(targetEntity="Role", mappedBy="movie") ``` 

Nous obtenons alors une signature erronée avec un anti-slash parasite sur la méthode <i>addProducts</i> après avoir invoqué le script ``` doctrine:generate:entities ```

```
  // La signature de la méthode obtenue est erronée ; nous devrions avoir un paramètre "Product $products" et non pas "\Product $products" :
   public function addProducts(\Product $products)
```

Si vous ne souhaitez pas écrire le chemin complet de vos <i>targetEntities</i>, pensez à supprimer cet anti-slash parasite.

* Pour en savoir plus les annotations de Doctrine 2 : <a href="http://www.doctrine-project.org/projects/orm/2.0/docs/reference/annotations-reference/en#annotations-reference" target="_blank">Annotations Reference</a>.
        
* Pour approfondir la question des relations dans Doctrine 2 : <a href="http://www.doctrine-project.org/projects/orm/2.0/docs/reference/working-with-associations/en#working-with-associations" target="_blank">Working with Associations</a>.
  
Nos relations étant à présent définies, nous allons reconstuire nos classes <i>Entities</i> et les tables en base de données :

```
    hello/console doctrine:generate:entities
    hello/console doctrine:schema:drop --force
    hello/console doctrine:schema:create
```

Noter dans le code des deux entités du côté "<i>one</i>" des relations <i>one-to-many</i> (<i>Actor</i> et <i>Movie</i>) l'ajout par Doctrine d'une méthode <i>addRoles(Role)</i>. Noter également que pour ces deux entités nous avons redéfini le constructeur pour initiliser une collection vide, instance de <i>ArrayCollection</i>, car elle permet d'appeler la méthode <i>$this->getRoles()->add(Role)</i> même si l'entité n'est encore liée à aucun <i>Role</i>.

Place à présent aux tests unitaires pour manipuler nos entités et leurs relations. Nous allons compléter la classe de test <i>ActorEntityTest</i> que nous avions ébauchée dans <a href="http://www.elao.org/developpement/a-la-decouverte-de-symfony-2-tests-unitaires-sur-le-modele-phpunit-et-doctrine-2.html" target="_blank">notre article précédent</a> et en particulier, nous allons y ajouter une méthode <i>testRoles</i> dont voici le contenu (noter que certains passages du code sont inhibés sous formes de commentaires ; nous y reviendrons) :

```
# src/Bundle/ElaoBundle/Tests/ActorEntityTest:
# …
   public function testRoles()
    {
      $em = $this->getEntityManager() ;

      // 1. Create a new Actor and persist him
      $actor = new Actor() ;
      $actor->setFirstname('Patrick') ;
      $actor->setLastname('Dewaere') ;
      $actor->setBirthYear(1947) ;
      $em->persist($actor) ;
      $em->flush() ;

      $query = $em->createQuery('SELECT COUNT(act.id) FROM \Bundle\ElaoBundle\Entity\Actor act');
      $count = $query->getSingleScalarResult();

      $this->assertEquals($count, 1) ;

      // 2. Create a new female Actor and persist her
      $actress = new Actor() ;
      $actress->setFirstname('Brigitte') ;
      $actress->setLastname('Fossey') ;
      $actress->setBirthYear(1946) ;
      $em->persist($actress) ;
      $em->flush() ;
      $count = $query->getSingleScalarResult() ;

      $this->assertEquals($count, 2) ;

      // 3. Create a new Movie and persist it
      $movie = new Movie() ;
      $movie->setTitle('Un Mauvais Fils') ;
      $movie->setReleaseYear(1980) ;
      $em->persist($movie) ;
      $em->flush() ;

      $movie_query = $em->createQuery('SELECT COUNT(mov.id) FROM \Bundle\ElaoBundle\Entity\Movie mov') ;
      $movie_count = $movie_query->getSingleScalarResult() ;

      $this->assertEquals($movie_count, 1) ;

      // 4. First male character / role
      $m_role = new Role() ;
      $m_role->setActor($actor) ;
      $m_role->setMovie($movie) ;
      $m_role->setPlays('Bruno') ;
      $m_role->setRank(1) ;
      // $actor->addRoles($m_role) ;
      // $movie->addRoles($m_role) ;
      $em->persist($m_role) ;

      // 5. First female character / role
      $f_role = new Role() ;
      $f_role->setActor($actress) ;
      $f_role->setMovie($movie) ;
      $f_role->setPlays('Catherine') ;
      $f_role->setRank(2) ;
      $em->persist($f_role) ;

      $em->flush() ;
      // $em->clear() ;

      $role_query = $em->createQuery('SELECT COUNT(role.id) FROM \Bundle\ElaoBundle\Entity\Role role') ;
      $role_count = $role_query->getSingleScalarResult() ;

      $this->assertEquals($role_count, 2) ;

      // /$dewaere = $em->getRepository('Bundle\ElaoBundle\Entity\Actor')->findOneBy(array('lastname' => 'Dewaere')) ;
      // $this->assertEquals($dewaere->getLastname(), 'Dewaere') ;
      // $this->assertEquals(1, $dewaere->getRoles()->count()) ;
   }
   #...
```

** Astuce ** :

Dans les requêtes DQL, il est possible d'utiliser un raccourci <i>MyBundle:MyEntity</i> très pratique au lieu des namespaces complets : ``` $role_query = $em->createQuery('SELECT COUNT(role.id) FROM ElaoBundle:Role role') ```

Force est de constater que ce code est assez basique : nous instancions deux <i>Actor</i> (repères 1 et 2), nous les sauvegardons en base de données par le biais de l'<i>EntityManager</i>, puis nous nous assurons que le nombre d'enregistrements en base de données est conforme à nos attentes. Je vous avais prévenus, le code est primaire ! Idem pour la création et la sauvegarde d'une instance de <i>Movie</i> (repère 3), puis de deux rôles (repères 4 et 5) pour nos acteurs. Si nous lançons le test, tout devrait se dérouler de manière nominale et nous devrions obtenir le statut <i>OK</i>avec un bilan final d'un test et quatre assertions :

```
 phpunit src/Bundle/ElaoBundle/Tests/ActorEntityTest.php
```

Vous devriez également constater que les enregistrements en base de données ne présentent pas de surprise, en particulier la table <i>Role</i> comporte bien les relations attendues. Nous sommes sur la bonne voie, nous commençons à mieux discerner la philosophie de Doctrine 2, nous pouvons être fiers de nous. Comme nous sommes en confiance, nous allons en profiter pour ajouter trois lignes de code à la fin de notre méthode de test (il suffit de les décommenter dans l'extrait de code ci-dessus). A priori, rien d'extraordinaire, nous récupérons en base de données un acteur, nous nous assurons que c'est bien celui que nous voulions en vérifiant la valeur de la méthode <i>getLastName()</i>, et enfin, nous nous assurons que cet acteur est bien associé à un rôle. Nous sommes impatients de poursuivre l'exploration de Doctrine 2, nous serions tentés d'enchaîner immédiatement sur d'autres aspects de l'ORM, mais nous allons tout de même lancer le test par acquis de conscience après avoir décommenté les trois dernières lignes de code de la méthode <i>testRoles()</i>. Et là, mauvaise surprise ! Le test échoue.


Que s'est-il exactement passé ? La ligne incriminée est la suivante :

```
$this->assertEquals(1, $dewaere->getRoles()->count()) ;
```

Nous nous attendions à ce que notre instance d'acteur soit associé à un rôle, mais PHPUnit nous indique que la valeur retournée par la méthode <i>count()</i> est égale à zéro, alors que nos données en base sont correctes. Il semblerait que ce résultat inattendu (tout du moins pour les novices que nous sommes) s'explique par le décalage existant entre les informations sur notre acteur présent en base de données et notre instance d'acteur géré par l'<i>EntityManager</i>. Il est fort probable que l'objet <i>$dewaere</i> que nous avons récupéré en base de données continue en fait d'être géré par l'<i>EntityManager</i> mais dans son état initial (il correspond à l'entité <i>$actor</i>) ; et dans cet état initial, la propriété <i>$roles</i> de l'objet <i>$dewaere</i> représente une collection vide. En effet, dans le repère 4, nous avons créé une nouvelle instance de <i>Role</i> et les informations que nous lui avons fournies étaient suffisantes pour sauvegarder correctement cette instance en base de données. En revanche, du point de vue purement <i>Objet</i>, nous manipulons deux entités liées selon une <b>association bidirectionnelle</b> (l'entité <i>Role</i>, qui est le porteur de l'association - <i>owning side</i> dans le jargon de Doctrine - possède un attribut <i>$actor</i> et l'entité <i>Actor</i>, qui constitue l'opposé de la relation - <i>inverse side</i> - possède une collection <i>$roles</i>, ce qui nous permet donc d'obtenir un acteur à partir d'un rôle, et une liste de rôles depuis un acteur, d'où le terme "<i>bidirectionnelle</i>"). Or dans le cas d'une telle association, pour que le modèle Objet reste cohérent, il est nécessaire de définir explicitement les relations de chaque côté de l'association. Pour nous en convaincre, nous allons décommenter les deux lignes du repère 4 qui permettent d'ajouter notre rôle récemment instancié dans les collections <i>$roles</i> de nos deux entités <i>Actor</i> et <i>Movie</i> (puisque cette dernière entité est également concernée). Si nous relançons notre test unitaire, celui-ci passe puisque l'entité <i>$dewaere</i> dispose désormais d'une collection <i>$roles</i> conforme à nos attentes.


Enfin, je vous propose d'inhiber à nouveau la ligne où nous ajoutons le nouveau rôle à la collection <i>$roles</i> (```$actor->addRoles($m_role) ;```), puis décommenter la ligne ```$em->clear()```, et enfin relancer le test : celui-ci devrait également passer avec succès. Bien que nous ayons laissé notre entité <i>$actor</i> dans un état incohérent en ne définissant pas les associations nécessaires, l'entité <i>$dewaere</i> ne correspond plus à l'entité gérée par l'<i>EntityManager</i> (puisque nous avons "nettoyé" ce dernier) et il possède donc bien une collection de <i>$roles</i> cohérente avec les données présentes en base. Cela étant, cette dernière démonstration visait surtout à nous faire mieux comprendre les mécanismes de Doctrine 2, mais ce n'est bien entendu pas une manière recommandable de passer des tests unitaires avec succès. Le principal enseignement que nous tirerons de ces manipulations, c'est que pour obtenir des entités viables dans le cadre d'une relation bidirectionnelle avec l'ORM Doctrine 2, nous devons toujours mettre à jour les relations des deux côtés de l'association.

## Conclusion

A travers cet exemple à la base très simple, nous avons pu remarquer que Doctrine 2 réservait quelques pièges pour qui souhaite s'y initier. En particulier, la gestion des entités par l'<i>EntityManager</i> nous amène à envisager l'exploitation des objets selon des principes auxquels Doctrine 1 ne nous avait pas habitués.

Nous aurons l'occasion de revenir sur l'ORM Doctrine 2 et son expérimentation au moyen de tests unitaires lors de futurs articles. Nous y aborderons notamment les <i>EntityRepositories</i>.

## Remerciements
  
Mille mercis à Benjamin pour ses remarques et notamment pour ses astuces très utiles.
  