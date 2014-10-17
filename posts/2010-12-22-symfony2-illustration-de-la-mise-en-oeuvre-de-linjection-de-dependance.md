
<div style="float: rigth; margin-left: 15px; margin-bottom: 15px;">
  <img src="/blog/medias/2010-12-22-symfony2-illustration-de-la-mise-en-oeuvre-de-linjection-de-dependance/sf2_reloaded.png" alt="sf2 reloaded Symfony2   Illustration de la mise en oeuvre de lInjection de dépendance"  title="Symfony2   Illustration de la mise en oeuvre de lInjection de dépendance" />
</div>

Bonjour à toutes et à tous,

Le Framework Symfony a été totalement repensé, et la version 2, attendue pour mars 2011, nous réserve énormément de nouveautés. Parmi les évolutions du Framework, un des aspects les plus marquants concerne la mise en oeuvre de **l'Injection de dépendance**. C'est sans doute le composant sur lequel Fabien Potencier a le plus communiqué lors des premières annonces concernant Symfony2 et il a déjà eu l'occasion d'exposer sa philosophie à maintes reprises. En particulier, les principes de l'injection de dépendance et sa mise en oeuvre sous Symfony2 ont fait l'objet d'une série d'articles parus sur le <a href="http://components.symfony-project.org/dependency-injection/documentation" target="_blank">site de Sensio Labs</a>.

Noter également que Vincent avait publié sur notre blog un article dans lequel il expliquait notamment l'organisation des différentes classes du Framework concourant à la mise en oeuvre de l'<a href="http://www.elao.org/symfony-2/symfony-2-linjection-de-dependances.html" target="_blank">Injection de dépendance</a>. Le coeur du framework a notablement évolué depuis la rédaction de ce billet mais les principes qui y sont décrits restent d'actualité.

A présent, Symfony2 est disponible dans sa version "Preview Release 4&#8243;, et cette version ne devrait pas connaître d'évolution majeure par rapport à la version finale, attendue donc à la fin du premier trimestre 2011. L'occasion nous est donnée de constater de visu comment les principes énoncés jusqu'ici seront concrètement appliqués dans la version finale. Comme de nombreux développeurs à l'affût des évolutions du Framework, nous avons commencé à nous pencher sérieusement sur cette nouvelle mouture, et nous avons également observé quelques Bundles Symfony2 en cours de développement, histoire de nous imprégner de la nouvelle philosophie du Framework. Parmi tous ces projets, il en est un qui a particulièrement attiré notre attention, car il offre un exemple concret de mise en pratique de l'injection de dépendance avec Symfony2, mais également parce qu'il permet, à notre sens, de mesurer pleinement l'apport indéniable de l'injection de dépendance dans nos projets. Le code de ce Bundle, toujours en cours de développement, est consultable sur l'hébergeur GitHub ; il s'agit du <a href="https://github.com/Herzult/ForumBundle" target="_blank">ForumBundle</a>.

### Un Bundle ouvert grâce à l'injection de dépendance

En analysant le code du Bundle `ForumBundle`, on constate immédiatement que les développeurs se sont attachés à produire un Bundle ouvert, facilement configurable, pour permettre notamment aux applications qui l'hébergeront d'utiliser soit <a href="http://www.doctrine-project.org/projects/orm" target="_blank">l'ORM de Doctrine</a>, soit un <a href="http://www.doctrine-project.org/projects/mongodb_odm" target="_blank">Object Document Manager</a> de Doctrine. Or, vous n'êtes pas sans savoir que la base de données que vous utiliserez, relationnelle ou No-SQL, conditionnera en grande partie l'écriture du code de vos contrôleurs ou de vos repositories. En effet, selon que vous optiez pour une base de données relationnelles ou une base No-SQL, vous ne manipulerez pas les mêmes `Managers`, que ce soit dans vos controleurs ou dans vos repositories. Dans un cas, vous serez amenés à instancier un `EntityManager` (cas de l'ORM avec une base de données relationnelle), dans l'autre cas, un `DocumentManager` (cas de MongoDB ou CouchDB). Si vous envisagez de créer un Bundle destiné à être distribué à des tiers, toute la problématique est donc de proposer un code qui laisse le choix du type de base de données à ses futurs utilisateurs. Et c'est là qu'intervient l'injection de dépendance. Nous allons donc voir concrètement comment `ForumBundle` propose une solution élégante, grâce à l'injection de dépendance.

**Les Bundles qui enrichiront vos applications pourront embarquer leurs propres définitions de services. Ainsi, dans le cas du `ForumBundle`, après installation, il faut dans un premier temps déclarer l'ajout d'un nouveau Bundle en mettant à jour la méthode `registerBundles` de votre Kernel :**

```
# app/AppKernel.php

// …

public function registerBundles()
{
    $bundles = array(
    // ...

    // register your bundles
    new Application\HelloBundle\HelloBundle(),
    new Bundle\ForumBundle\ForumBundle(),
)
```

**Puis, dans un second temps, modifier votre fichier de configuration principale (dans l'exemple qui suit, c'est un fichier YML, mais vous avez peut-être opté pour le format PHP ou XML) :**

```
# app/config/config.yml

# …

forum.config:
    db_driver: orm
    class:
      model:
        category: Bundle\Application\ForumBundle\Entity\Category
        topic: Bundle\Application\ForumBundle\Entity\Topic
        post: Bundle\Application\ForumBundle\Entity\Post
        user: Bundle\Application\DoctrineUserBundle\Entity\User
    #...
```

C'est ainsi que vous avez défini le Driver souhaité pour utiliser le Bundle. Si nous avions souhaité utiliser MongoDB, nous aurions écrit `odm` au lieu d'`orm`. De même, comme nous avons opté pour l'ORM, nous devons déclarer les classes du modèle utilisées.

**En effet, si vous observez les répertoires de premier niveau du Bundle, vous noterez la présence des trois dossiers suivants :**

```
+ Document
+ Entity
+ Model
```

Le répertoire `Model` contient des classes du Modèle, dont le code est commun à tous les objets du modèle, qu'ils soient gérés par l'ORM ou par le MongoDB Mapper. Ces classes, abstraites, ne sont donc liées à aucune technologie particulière. Ensuite, les répertoires `Document` et `Entity` contiennent les classes concrètes du modèle (qui héritent de celles définies dans le répertoire `Model`) et les repositories propres à la technologie adoptée. Noter également dans le répertoire "commun" `Model` la présence d'interfaces `XXXRepositoryInterface` qui seront implémentées par les classes de Repository ; elles permettent notamment de définir le contrat à remplir par les classes qui les implémenteront, quelle que soit la technologie employée.

A présent, nous allons voir le contenu du fichier `src/Bundle/ForumBundle/DependencyInjection/ForumExtension.php`, et en particulier la méthode `configLoad`.

**C'est en effet là qu'a lieu toute la "magie" qui va permettre au Bundle de recourir au bon Manager ; c'est d'une simplicité déconcertante :**

```
class ForumExtension extends Extension
{

    public function configLoad($config, ContainerBuilder $container)
    {
        $loader = new XmlFileLoader($container, __DIR__ . '/../Resources/config'); // (1)
        $loader->load('model.xml');
        $loader->load('controller.xml');
        $loader->load('form.xml');
        $loader->load('blamer.xml');
        $loader->load('templating.xml');
        $loader->load('paginator.xml');

        if (!isset($config['db_driver'])) {
            throw new \InvalidArgumentException('You must provide the forum.db_driver configuration'); // (2)
        }

        try {
            $loader->load(sprintf('%s.xml', $config['db_driver'])); // (3)
        } catch (\InvalidArgumentException $e) {
            throw new \InvalidArgumentException(sprintf('The db_driver "%s" is not supported by forum', $config['db_driver']));
        }
    }
}
```

Dans un premier temps, le loader charge les fichiers de définition XML présents dans le répertoire `Resources/config` du Bundle (1). Ensuite, la méthode s'assure que nous avons bien défini un Driver dans notre configuration principale (2). Enfin, en fonction du Driver que nous avons choisi, le loader charge le fichier approprié, `orm.xml` ou `odm.xml`.

**Allons consulter le contenu de ces deux fichiers :**

```
<!-- orm.xml  -->
<services>
    <!-- Object Manager Service -->
    <service id="forum.object_manager" alias="doctrine.orm.entity_manager" />
</services>
```

```
<!-- odm.xml  -->
<services>
    <!-- Object Manager Service -->
    <service id="forum.object_manager" alias="doctrine.odm.mongodb.document_manager" />
</services>
```

Voilà, chacun des fichiers définit un service portant le même identifiant, `forum.object_manager`, mais correspondant à des alias différents. Comme nous avons opté pour l'ORM, le service ayant cet identifiant correspond au service ayant pour clé `doctrine.orm.entity_manager`. Et désormais, les autres services utilisant un `Manager` font référence à cet identifiant, sans se soucier de savoir s'ils ont affaire à un EntityManager ou à un DocumentManager.

**De la même manière, le code des contrôleurs fait référence à la clé `forum.object_manager` pour récupérer un manager :**

```
<!-- Le fichier Resources/config/model.xml déclare par exemple un service Repository qui a lui même recours au service "forum.object_manager" -->
<services>
    <!-- Category Repository Service -->
    <service id="forum.repository.category" class="Bundle\ForumBundle\ForumBundle" factory-method="getRepository" shared="true">
        <argument type="service" id="forum.object_manager" />
        <argument type="string">%forum.model.category.class%</argument>
    </service>
</services>
```

```
// La méthode savePost de la classe Controller/PostController récupère un Manager de la même manière :

public function savePost(Post $post)
{
    $objectManager = $this->get('forum.object_manager');
    $objectManager->persist($post);
    $objectManager->flush();
}
```

### Conclusion

En analysant la structure globale du Bundle `ForumBundle`, nous avons pu voir comment Symfony2, grâce aux mécanismes d'injection de dépendance qu'il propose, offre une solution élégante pour créer des Bundles aisément configurables. S'agissant de l'injection de dépendance, Symfony2 s'est très largement inspiré du célèbre framework Java <a href="http://www.springsource.org/" target="_blank">Spring</a>. Et c'est sans doute là que réside la plus grande force de Symfony2 : il est l'aboutissement du travail fourni par des personnes qui ont fait leurs les concepts éprouvés dans d'autres langages et technologies (Ruby-on-Rails, Django, Spring, pour ne citer que les plus connus).