
**Je vous propose dans cet article de mettre en place un moteur de recherche géolocalisé dans votre projet Symfony 2.**

Il existe à ce jour de nombreux moyens de faire des recherches sur un site web : Google Search, recherche full text MySql, Lucene / Solr, Zend Lucene...
Aujourd'hui j'ai décidé d'utiliser ElasticSearch (<a title="ElasticSearch" href="http://www.elasticsearch.org/" target="_blank">http://www.elasticsearch.org</a>) qui est un moteur de recherche sous Java équivalent à Solr (Solr et Elasticsearch étant tout deux des surcouches de Apache Lucene). Le but de cet article n'étant pas de vous démontrer que untel est meilleur que l'autre... mais plutôt d'attirer votre attention sur une fonctionnalité spécifique à ElasticSearch : **la recherche géolocalisée**.

**Sommaire :**

*   [1 &#8211; Rappel théorique][1]
*   [2 &#8211; Installation][2]
*   [3 &#8211; Configuration][3]
*   [4 &#8211; Indexation][4]
*   [5 &#8211; Recherche][5]
*   [6 &#8211; Conclusion][6]



<a name="rappel-theorique"></a>

# 1 &#8211; Rappel théorique

Avant de rentrer dans le vif du sujet, voici une formule de calcul de distance entre 2 points géolocalisés (appelée "distance orthodromique") :

R x acos[cos(LatA) x cos(LatB) x cos(LongB-LongA)+sin(LatA) x sin(LatB)] = Distance en Km
avec R étant le rayon de la terre (environ 6 378 Km)



<a name="installation"></a>

# 2 &#8211; Installation

## 2.1 &#8211; Installation de ElasticSearch

Tout d'abord, nous allons télécharger la dernière version stable de ElasticSearch sur :
<a href="http://www.elasticsearch.org/download/" target="_blank">http://www.elasticsearch.org/download/</a>

Concernant l'installation d'ElasticSearch, il vous suffit d'extraire les sources téléchargées dans le dossier bin/ de votre projet Symfony 2 par exemple. Vous pouvez aussi l'installer ailleurs si vous souhaitez partager l'instance de votre moteur de recherche entre plusieurs projets.

Ensuite, lancer en ligne de commande votre moteur de recherche  :

<noscript>
  <pre><code class="language- ">dossier_installation/bin/elasticsearch -f</code></pre>
</noscript>

Pour plus d'informations sur l'installation : <a href="http://www.elasticsearch.org/guide/reference/setup/installation.html" target="_blank">http://www.elasticsearch.org/guide/reference/setup/installation.html</a>

## 2.2 &#8211; Installation de FOQElasticaBundle

Nous allons ensuite installer le bundle FOQElasticaBundle de Exercise. Ajoutez dans votre composer.json :

<noscript>
  <pre><code class="language-json json"># composer.json

"require": {
    "php": ">=5.3.2",
    "symfony/symfony": "2.1.*",
    # ...
    "exercise/elastica-bundle": "dev-master"
}</code></pre>
</noscript>

Lancez ensuite l'installation de ce package et de ses dépendances (ruflin/elastica)

Pour plus d'information sur l'installation : <a href="https://github.com/Exercise/FOQElasticaBundle" target="_blank">https://github.com/Exercise/FOQElasticaBundle</a>

## 2.3 &#8211; Déclaration du bundle

<noscript>
  <pre><code class="language-php php">// app/AppKernel.php

public function registerBundles() {
    return array(
        // ...
        new FOQ\ElasticaBundle\FOQElasticaBundle(),
        // ...
    );
}</code></pre>
</noscript>



## 2.4 &#8211; Test du fonctionnement du bundle

<noscript>
  <pre><code class="language-shell shell">elao:/Volumes/Elao/workspace/myproject$ ./app/console list foq
  foq:elastica:populate                 Populates search indexes from providers
  foq:elastica:search                   Searches documents in a given type and index</code></pre>
</noscript>

En faisant un "app/console list foq" dans votre terminal, vous verrez que le bundle FOQElasticaBundle propose 2 commandes :

*   foq:elastica:populate va vous permettre de lancer l'indexation
*   foq:elastica:search va vous permettre de faire des recherche dans vos indexes



<a name="configuraton"></a>

# 3 &#8211; Configuration

## 3.1 &#8211; Déclaration du client et du mapping

<noscript>
  <pre><code class="language-yaml yaml">// app/config/config.yml

# Foq Elastica Search
foq_elastica:
    clients:
        default: { host: localhost, port: 9200 }
    indexes:
        website:
            client: default
            types:
                user:
                    mappings:
                        username: { boost: 5 }
                        presentation: { boost: 2 }
                        location: { type: geo_point, boost: 3, lat_lon: true }
                    persistence:
                        driver: orm # Le mode de stockage des données à indexer  (orm, mongodb, propel)
                        model: Elao\UserBundle\Entity\User # L'entité sur laquelle porte le mapping défini ci-dessus
                        provider:
                            query_builder_method: findValidQueryBuilder # Méthode qui va renvoyer les objets à indexer
                        finder: ~ # Indique que l'on va utiliser un finder (voir la doc officielle pour plus d'infos)
                        listener: ~ # Indique que l'on va utiliser un listener sur l'entité pour la MàJ de l'index</code></pre>
</noscript>

Concernant le mapping, lors de l'indexation, le système va faire appel aux getters correspondants sur l'entité indexée. Concrètement dans notre exemple, le système fera appel aux méthodes getUsername(), getPresentation() et getLocation() de l'entité Elao\UserBundle\Entity\User

## 3.2 &#8211; Configuration de l'entité à indexer

<noscript>
  <pre><code class="language-php php">// src/Elao/UserBundle/Entity/User.php

<?php
namespace Elao\UserBundle\Entity;

use Doctrine\Common\Collections\ArrayCollection;
use Doctrine\ORM\Mapping as ORM;

/**
 * Elao\UserBundle\Entity\User
 *
 * @ORM\Table(name="user")
 */
class User
{
    /**
     * @var string $username
     *
     * @ORM\Column(name="username type="text", nullable=true)
     */
    protected $username;

    /**
     * @var string $presentation
     *
     * @ORM\Column(name="presentation", type="text", nullable=true)
     */
    protected $presentation;

    /**
     * @var $float $locationLat
     *
     * @ORM\Column(name="location_lat", type="float", nullable=true)
     */
    protected $locationLat;

    /**
     * @var float $locationLng
     *
     * @ORM\Column(name="location_lng", type="float", nullable=true)
     */
    protected $locationLng;

    /**
     * Get username
     *
     * @return text
     */
    public function getUsername()
    {
        return $this->username;
    }

    /**
     * Get presentation
     *
     * @return text
     */
    public function getPresentation()
    {
        return $this->presentation;
    }

    /**
     * Return the location for Elasticsearch
     *
     * @return array
     */
    public function getLocation()
    {
        $location = array();
        $location["lat"] = $this->getLocationLat();
        $location["lon"] = $this->getLocationLng();

        return $location;
    }
}</code></pre>
</noscript>

Dans l'exemple ci-dessus, nous pouvons voir que nous renvoyons les propriétés $locationLat et $locationLng sous forme de tableau par l'intermédiaire de la méthode getLocation().
<a name="indexation"></a>

# 4 &#8211; Indexation

Maintenant que tout est installé et bien configuré, nous allons pouvoir lancer l'indexation.

Pour cela, rien de plus simple, il vous suffit de lancer la commande d'indexation (dont nous avons parlé quelques lignes plus haut) :

<noscript>
  <pre><code class="language-shell shell">elao:/Volumes/Elao/workspace/myProject$ ./app/console foq:elastica:populate
Resetting website
Populating website/user, 62,5% (15/24), 80 objects/s
Refreshing website</code></pre>
</noscript>



<a name="recherche"></a>

# 5 &#8211; Recherche

## 5.1 &#8211; Test de la recherche en ligne de commande

On va commencer par faire un test tout bête en ligne de commande de notre moteur de recherche :

<noscript>
  <pre><code class="language-shell shell">elao:/Volumes/Elao/workspace/myProject$ ./app/console foq:elastica:search user admin
Found 1 results
[1,88] 'admin'</code></pre>
</noscript>

Cette commande propose pas mal d'options qui vous aideront à débugger vos requêtes vers le moteur de recherche ou vous permettront aussi de mieux comprendre la syntaxe des requêtes JSON vers ElasticSearch.

## 5.2 &#8211; Contrôleur / Manager

En amont, nous avons un formulaire de recherche composé de 2 champs : "What", "Where" (un peu à la façon du site des pages jaunes par exemple). Les valeurs de ces champs vont être envoyées à un contrôleur qui va les traiter et les envoyer à son tour à un manager.

On va tout d'abord créer ce Manager qui contiendra toute notre logique métier pour rechercher dans l'index du moteur de recherche :

<noscript>
  <pre><code class="language-php php">// src/Elao/SearchBundle/Manager/UserManager.php

<?php
namespace Elao\SearchBundle\Manager;

use Doctrine\ORM\EntityManager;
use FOQ\ElasticaBundle\Finder\TransformedFinder;

/**
 * Project manager
 */
class UserManager
{
    protected $em;
    protected $finder;

    public function __construct(EntityManager $em, TransformedFinder $finder)
    {
        $this->em     = $em;
        $this->finder = $finder;
    }

    public function getRepository()
    {
        return $this->em->getRepository('ElaoUserBundle:User');
    }

    public function search($data)
    {
        if (isset($data["what"])) {
            $searchTerm = $data["what"];

            $elasticaQuery = new \Elastica_Query_QueryString($searchTerm);
        } else {
            $elasticaQuery = new \Elastica_Query_MatchAll();
        }

        if (isset($data["where"])) {
            $searchTerm = $data["where"];

            try {
                $geocoder = file_get_contents("http://maps.googleapis.com/maps/api/geocode/json?address=" . urlencode($searchTerm) . "&sensor=true");

                if (!$geocoder) {
                    throw new Exception("Impossible de joindre le service Geocoder");
                }

                $result = json_decode($geocoder, true);

                if ($result['status'] != "OK") {
                    throw new Exception("Le service Geocoder a renvoyé une erreur. Code : " . $result['status']);
                }

                if (!isset($result['results'][0]['geometry']['location'])) {
                    throw new Exception("Impossible de trouver la node location");
                }

                $location = $result['results'][0]['geometry']['location'];

                $geoFilter = new \Elastica_Filter_GeoDistance('location', $location['lat'], $location['lng'], '10km');

                $elasticaQuery = new \Elastica_Query_Filtered($elasticaQuery, $geoFilter);
            } catch (Exception $e) {
            }
        }

        $data = $this->finder->find($elasticaQuery);

        return $data;
    }
}
</code></pre>
</noscript>

Ce manager va construire une requête en fonction des paramètres passés à la fonction search(). Pour la recherche, nous avons besoin de passer au moteur de recherche une latitude et une longitude du point autour duquel nous souhaitons rechercher. Il vous est aussi possible de définir le périmètre dans lequel vous souhaitez rechercher.

Cette fonction renverra une Collection d'objets Doctrine. A vous ensuite de faire le nécessaire dans votre contrôleur/vue pour les afficher.



<a name="conclusion"></a>

# 6 &#8211; Conclusion

On a pu voir à travers cet article qu'il était possible de mettre en place un moteur de recherche géolocalisée rapidement et facilement sur un projet Symfony 2. Concernant les performances, je n'ai pas fait de mesures mais elles semblent globalement satisfaisantes.

Vos commentaires ou vos questions sont les bienvenus et me permettront de compléter cet article si besoin.

 [1]: #rappel-theorique
 [2]: #installation
 [3]: #configuration
 [4]: #indexation
 [5]: #recherche
 [6]: #conclusion