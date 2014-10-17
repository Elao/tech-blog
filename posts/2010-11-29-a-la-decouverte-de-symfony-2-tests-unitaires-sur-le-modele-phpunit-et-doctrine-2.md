
Bonjour à toutes et à tous,

A l'heure où nous rédigeons ces lignes, la sortie officielle de <a href="http://symfony-reloaded.org/" target="_blank">Symfony2</a> est prévue pour mars 2011. Cette version de notre framework préféré fait actuellement l'objet d'une veille attentive de la part de la commmunauté des utilisateurs, et ELAO ne déroge pas à la règle. Nous avons donc commencé à manipuler la **bête**, et cet article, qui en appelle sans doute d'autres, n'a pas d'autre ambition que de consigner par écrit notre propre expérimentation du framework. Les voies que nous empruntons sont probablement discutables, car elles constituent davantage un parcours initiatique à la découverte du framework nouvelle mouture, qu'un mode d'emploi exempt de toute critique.

Pour débuter, nous avons concentré nos efforts sur la nouvelle philosophie de Doctrine 2 et sur la mise en oeuvre de tests unitaires du modèle à l'aide de PHPUnit.

**Prérequis :**
Pour réaliser les étapes décrites dans cet article, les bibliothèques et outils suivants sont supposés déjà installés et fonctionnels
 - PHP 5.3.3
 - <a href="http://www.phpunit.de/manual/current/en/installation.html" target="_blank">PHPUnit</a> dans sa version 3.5
 - Une <a href="http://symfony-reloaded.org/code" target="_blank">Sandbox</a> de la Preview de Symfony2, configurée par vos soins pour accéder à une base de données (version 2_0_PR3)

La description de la mise en place et de la configuration de ces outils sort du cadre de cet article.

**Ressources documentaires :**
 - <a href="http://docs.symfony-reloaded.org/?page=learn" target="_blank">Symfony 2 (Preview Release)</a>
 - <a href="http://www.doctrine-project.org/projects/orm/2.0/docs/en" target="_blank">Doctrine 2</a>
 - <a href="http://www.phpunit.de/manual/current/en/index.html" target="_blank">PHPUnit</a>

Pour les besoins de cet article, nous allons mettre en oeuvre un seul **Entity** afin de nous focaliser sur l'essentiel, à savoir, la manipulation des objets à l'aide de Doctrine 2. Noter que celui-ci permet de définir le modèle de plusieurs manières : à l'aide d'annotations saisies dans des classes PHP, ou bien au moyen de fichiers de configuration au format XML ou YML. Nous avons opté ici pour les annotations.

Nous allons commencer par créer un Bundle destiné notamment à héberger notre modèle (avec Symfony 2, tout est Bundle !). Pour cela, nous allons nous placer à la racine de notre Sandbox et lancer la commande Symfony générant un nouveau bundle :

```
cd ~/workspace/sandbox
hello/console init:bundle 'Bundle\ElaoBundle'
```

Pour connaître la liste des commandes disponibles :
```
hello/console
```

Pour obtenir de l'aide sur une commande :

```
hello/console help init:bundle
```

La commande **init:bundle** a créé dans le dossier **src** un sous-répertoire **Bundle/ElaoBundle** dans lequel nous allons rédiger le code de notre modèle. Mais avant toute chose, nous devons déclarer ce nouveau Bundle dans la méthode **registerBundles** du fichier **HelloKernel** :

```
# sandbox/hello/HelloKernel.php
# ...
public function registerBundles()
{
    $bundles = array(
    new Symfony\Framework\KernelBundle(),
    # ...
    new Application\ElaoBundle\ElaoBundle(),
)
# ...
```

A présent, nous pouvons rédiger le code de notre premier Entity, et nous allons pour cela créer dans le répertoire de notre nouveau Bundle un sous-répertoire **Entity** :

```
mkdir src/Bundle/ElaoBundle/Entity
```

Nous allons enregistrer notre premier Entity dans ce nouveau répertoire, et l'enrichir à l'aide d'annotations :

```
# sandbox/src/Bundle/ElaoBundle/Entity/Actor.php

namespace Bundle\ElaoBundle\Entity;

/**
* @Entity
*/

class Actor
{
    /**
    * @Id
    * @Column(type="integer")
    * @GeneratedValue(strategy="IDENTITY")
    *
    */
    protected $id;

    /**
    * @Column(type="string", length="255")
    *
    */
    protected $firstname;

    /**
    * @Column(type="string", length="255", nullable=false)
    *
    */
    protected $lastname;

    /**
    * @Column(type="integer")
    *
    */
    protected $birth_year ;

}
```

A présent, nous allons lancer la commande suivante :

```
hello/console doctrine:generate:entities
```

Si vous observez le code de notre fichier **Actor.php**, vous remarquerez que Doctrine a généré les accesseurs (méthodes **get** et **set**) pour chacune des propriétés que nous avons saisies dans cette classe (noter toutefois que pour le champ **id**, seule la méthode **get** a été générée). Forts de cette première expérience, nous allons pouvoir générer les tables en base de données (nous considérons que la base de données existe déjà ; dans le cas contraire, il faut utiliser la commande **doctrine:database:create**) :

```
hello/console doctrine:schema:create
```

A présent, nous disposons d'un Entity et d'une base de données correctement configurée, nous allons donc essayer d'enregistrer des données en base à l'aide de l'ORM. Comme nous découvrons le framework et que nous souhaitons également mettre en oeuvre PHPUnit, nous allons faire des manipulations d'entités au moyen de tests unitaires. Or, pour communiquer avec la base de données, la nouvelle version de Doctrine 2 nous oblige à passer par des **Entity Managers**. Toute la difficulté pour nous est d'obtenir un **Entity Manager** dans une application de type "console" (depuis les contrôleurs de Symfony 2, il est aisé d'obtenir un **Entity Manager** puisque les contrôleurs disposent d'une instance d'**Entity Manager** obtenue par injection de dépendances par le biais du **\$container** (instance de `Symfony\Component\DependencyInjection\Container`), ce dernier faisant en quelque sorte office de "registre des instances obtenues par injection de dépendance" : **\$em = $this['doctrine.orm.entity_manager']**).

  Dans le cadre des tests unitaires, nous allons passer par une instance de **HelloKernel** pour obtenir un **Entity Manager** (également au moyen de sa propriété **$container**). C'est parti, voici le contenu d'un exemple de fichier de tests (qui nécessite de créer un sous-répertoire **Tests** dans notre Bundle **ElaoBundle**) :

```
# sandbox/src/Bundle/ElaoBundle/Tests/ActorEntityTest.php

namespace Bundle\ElaoBundle\Tests;
require_once __DIR__.'/../../../../hello/HelloKernel.php';

use Bundle\ElaoBundle\Entity\Actor ;

class ActorEntityCase extends \PHPUnit_Framework_TestCase
{
    /**
    * @var \Doctrine\ORM\EntityManager
    */
    private $em;

    public function __construct()
    {
        $kernel = new \HelloKernel('dev', true);
        $kernel->boot();
        $this->em = $kernel->getContainer()->get('doctrine.orm.entity_manager');
    }

    /**
    * Enter description here ...
    * @return \Doctrine\ORM\EntityManager
    */
    protected function getEntityManager ()
    {
        return $this->em;
    }

    public function testNewActor()
    {
        $em = $this->getEntityManager() ;
        $actor = new Actor() ;
        $actor->setFirstname('Patrick') ;
        $actor->setLastname('Dewaere') ;
        $actor->setBirthYear(1947) ;
        $em->persist($actor) ;
        $em->flush() ;

        $query = $em->createQuery('SELECT COUNT(act.id) FROM \Bundle\ElaoBundle\Entity\Actor act');
        $count = $query->getSingleScalarResult();

        $this->assertEquals($count, 1) ;
    }

}
```

Lançons le test pour vérifier que tout s'est correctement déroulé :

```
phpunit src/Bundle/ElaoBundle/Tests/ActorEntityTest.php
```

Le test passe avec succès. Vérifions également à l'aide d'une commande DQL Doctrine que le contenu en base de données est conforme à nos attentes :

```
hello/console doctrine:query:dql "select act.firstname, act.lastname from \Bundle\ElaoBundle\Entity\Actor act"
```

Nous venons donc d'écrire notre premier test unitaire sur Doctrine2 au sein d'une application Symfony 2. Cela étant, un problème subsiste : si nous lançons le test une seconde fois, nous serons confrontés à un échec puisque le nombre d'enregistrement en base de données augmente avec chaque test. Il nous faut trouver un moyen d'isoler nos données de tests ; nous pourrions soit nettoyer la base de données à l'issue du test, soit repartir d'une base "**blanche**" au début des tests. Nous allons opter pour la seconde solution (car elle nous permet de constater l'état de notre base de données à l'issue du test). Nous allons tout simplement invoquer les commandes **doctrine:schema:drop** et **doctrine:schema:create** dans la méthode **setUp** de notre test. Pour cela, nous devons obtenir une instance de `\Symfony\Bundle\FrameworkBundle\Console\Application`. Commençons donc par instancier cette classe dans le constructeur :

```
# sandbox/src/Bundle/ElaoBundle/Tests/ActorEntityTest.php

# ...

public function __construct()
{
    $kernel = new \HelloKernel('dev', true);
    $kernel->boot();
    $this->em = $kernel->getContainer()->get('doctrine.orm.entity_manager');
    $this->application = new \Symfony\Bundle\FrameworkBundle\Console\Application($kernel);
    $this->application->setAutoExit(false);
}

# ...
```

Nous ajoutons ensuite une méthode protégée nous permettant de lancer des commandes (comme nous envisageons de lancer deux commandes distinctes, une pour supprimer la base, l'autre pour la récréer, autant factoriser le code dans une méthode propre) ; cette méthode utilise l'instance de Console\Application que nous avons instanciée dans le constructeur :

```
# sandbox/src/Bundle/ElaoBundle/Tests/ActorEntityTest.php
# …

protected function runConsole($command, Array $options = array())
{
    $options = array_merge($options,array('command' => $command));
    return $this->application->run(new \Symfony\Component\Console\Input\ArrayInput($options));
}

# ...
```

Enfin, nous allons compléter la méthode **setUp** de notre classe de test afin d'invoquer les commandes de suppression et de regénération de la base de données avant le lancement des tests :

```
# sandbox/src/Bundle/ElaoBundle/Tests/ActorEntityTest.php
# …

/**
* Prepares the environment before running a test.
*/
protected function setUp ()
{
    parent::setUp();
    $this->runConsole('doctrine:schema:drop', array( '--force' => true));
    $this->runConsole('doctrine:schema:create');
}

# ...
```

Voilà, si nous relançons le test, nous obtenons le résultat suivant :

```
PHPUnit 3.5.5 by Sebastian Bergmann.

Dropping database schema...
Database schema dropped successfully!
Creating database schema...
Database schema created successfully!

Time: 0 seconds, Memory: 9.00Mb

OK (1 test, 1 assertion)
```

## Conclusion

Nous avons vu comment rédiger un test unitaire "attaquant" la base de données sous Symfony2. En l'état actuel du framework, Doctrine 2 semble avoir un peu d'avance sur Symfony2 en termes de fonctionnalités et de documentation. C'est la raison pour laquelle nous avons axé nos premiers efforts sur le modèle, et eu recours aux tests unitaires pour "faire tourner" Doctrine 2, et nous affranchir ainsi des couches **Contrôleurs** et **Vues**. Il reste toutefois de nombreux points qui n'ont pas été abordés dans cet article, s'agissant du modèle et des tests unitaires. En particulier, les prochains articles aborderont sans doute les **Repositories** de Doctrine 2 destinés notamment à l'écriture de code permettant d'interroger la base de données, les relations dans le modèle, et la configuration de PHPUnit pour lancer une série de tests. **Stay tuned !**