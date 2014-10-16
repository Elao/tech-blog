
<div style="border: 1px solid lightgrey; padding: 10px; background-color: lightyellow; margin: 20px 0px;">
  <strong><span style="text-decoration: underline;">Avertissement</span> :</strong><br /> Depuis la rédaction de cet article sur l'injection de dépendances, le coeur de Symfony2 a évolué de façon notable, en particulier certaines classes du répertoire <em>DependencyInjection</em>mentionnées dans cet article ont été renommées, révisées, ou refondues. Toutefois, les principes généraux décrits dans cet article restent d'actualité.Le 21 décembre 2010</p>
</div>

<img class="alignleft" title="dependency-injection-symfony" src="/blog/wp-content/uploads/2010/06/dependency-injection-symfony.png" alt="dependency injection symfony Symfony 2   Linjection de dépendances" width="278" height="236" />

<div style="text-align: justify;">
  Cet article est le premier d'une série à venir sur Symfony 2. Pour commencer, je vous invite à télécharger la <a href="http://symfony-reloaded.org/code" target="_blank">sandbox</a> de Symfony 2. J'ai décidé de commencer par l'injection de dépendances car il s'agit d'un composant clé de Symfony 2, et que la bonne compréhension de cette nouvelle version du Framework doit forcément en passer par là. L'injection de dépendance est réellement au coeur du Framework.
</div>

<p style="text-align: justify;">
  Nous allons commencer par tenter d'éclaircir cette notion (ou ce design pattern), puis nous verrons plus concrètement l'implémentation qui en est faite au sein de Symfony 2. Commençons par une petite définition.
</p>

<h5 style="text-align: justify;">
  Définition Wikipedia:
</h5>

<p style="text-align: justify;">
  "<strong>L'injection de dépendances</strong> (Dependency Injection) est un mécanisme qui permet d'implanter le principe de l'Inversion de contrôle. Il consiste à créer dynamiquement (injecter) les dépendances entre les différentes classes en s'appuyant généralement sur une description (fichier de configuration). Ainsi les dépendances entre composants logiciels ne sont plus exprimées dans le code de manière statique mais déterminées dynamiquement à l'exécution."
</p>

#### Qu'est ce que l'injection de dépendances ?

<p style="text-align: justify;">
  En programmation objet, et particulièrement avec les Frameworks, nous sommes souvent amenés à manipuler un grand nombre de classes d'objet différentes. Ces différents objets sont chargés de fournir une fonctionnalité ou un "service". Dans une application standard, nous serons par exemple amenés à manipuler :
</p>

*   Un objet **User**, supposé représenter l'utilisateur connecté à l'application
*   Un objet **Session**, pour la gestion de la session de l'utilisateur
*   Un objet **Requete**, représentant la requête envoyée par un client
*   Un objet **Réponse**, pour la réponse que l'application va renvoyer
*   Un objet **Log**, pour pouvoir gérer des logs
*   Un objet **Mailer**, pour pouvoir envoyer des mails
*   etc ...

<p style="text-align: justify;">
  La nécessité de l'injecteur de dépendances provient, d'une part, du fait que certains de ces objets sont liés entre eux, ou plutôt "dépendants" les uns des autres, d'autre part, que les objets sont de plus en plus découplés et scindés en classes bien particulières. Par exemple, le service "<strong>Mailer</strong>" utilisera peut être le service "<strong>Log</strong>" pour enregistrer les mails qui sont envoyés par l'application.<br /> L'idée de l'injection de dépendances est d'utiliser un fichier de configuration pour auto-générer une classe (un "container") qui contiendra des méthodes nous permettant d'accéder à ces services ; elle contiendra l'initialisation des services (via l'instanciation de la classe qui les représente), gérera les dépendances de ces services (via les constructeurs ou l'appel de méthodes), la configuration des services, etc....
</p>

Pour illustrer ce concept, voyons un petit exemple.
Imaginons que nous ayons trois services:

*   un mailer (chargé d'envoyer des mails)
*   un logger (chargé d'enregistrer des logs)
*   un router (chargé du routing)

L'injecteur de dépendances pourrait ressembler à quelque chose comme ça :

<table style="width: 100%;">
  <tr>
    <th>
      Fichier de configuration
    </th>

    <th>
      Container généré
    </th>
  </tr>

  <tr>
    <td valign="top" width="50%">
      <div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
        <div class="php codecolorer">
          <span class="sy0">*</span> Service <span class="st_h">'mailer'</span><br /> <span class="sy0">-</span> Utiliser la classe <span class="st_h">'MonMailer'</span><br /> <span class="sy0">-</span> Passer le service <span class="st_h">'logger'</span> dans son constructeur<br /> <span class="sy0">-</span> Appeler la méthode <span class="st_h">'initialiser'</span><span class="sy0">*</span> Service <span class="st_h">'logger'</span><br /> <span class="sy0">-</span> Utiliser la classe <span class="st_h">'MonLogger'</span><span class="sy0">*</span> Service <span class="st_h">'routeur'</span><br /> <span class="sy0">-</span> Utiliser la classe <span class="st_h">'MonRouteur'</span><br /> <span class="sy0">-</span> Appeler la méthode <span class="st_h">'setLogger'</span> en lui passant le service <span class="st_h">'logger'</span> en paramètre
        </div>
      </div>
    </td>

    <td valign="top" width="50%">
      <div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
        <div class="php codecolorer">
          <span class="kw2">class</span> Container<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="br0">&#123;</span><br />   <span class="kw2">public</span> <span class="kw2">function</span> getServiceMailer<span class="br0">&#40;</span><span class="br0">&#41;</span><br />   <span class="br0">&#123;</span><br />     <span class="re0">$service</span> <span class="sy0">=</span> <span class="kw2">new</span> MonMailer<span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">getServiceLogger</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br />     <span class="re0">$service</span><span class="sy0">-></span><span class="me1">initialiser</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br />     <span class="kw1">return</span> <span class="re0">$mailer</span><span class="sy0">;</span><br />   <span class="br0">&#125;</span><br /> <br />   <span class="kw2">public</span> <span class="kw2">function</span> getServiceLogger<span class="br0">&#40;</span><span class="br0">&#41;</span><br />   <span class="br0">&#123;</span><br />     <span class="re0">$service</span> <span class="sy0">=</span> <span class="kw2">new</span> MonLogger<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br />     <span class="kw1">return</span> <span class="re0">$service</span><span class="sy0">;</span><br />   <span class="br0">&#125;</span><br /> <br />   <span class="kw2">public</span> <span class="kw2">function</span> getServiceRouteur<span class="br0">&#40;</span><span class="br0">&#41;</span><br />   <span class="br0">&#123;</span><br />     <span class="re0">$service</span> <span class="sy0">=</span> <span class="kw2">new</span> MonRouteur<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br />     <span class="re0">$service</span><span class="sy0">-></span><span class="me1">setLogger</span><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">getServiceLogger</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br />     <span class="kw1">return</span> <span class="re0">$service</span><span class="sy0">;</span><br />   <span class="br0">&#125;</span><br /> <span class="br0">&#125;</span>
        </div>
      </div>
    </td>
  </tr>
</table>

<p style="text-align: justify;">
  L'injection de dépendances permet donc une gestion beaucoup plus claire et souple de ces différents "services". En modifiant ce fichier de configuration nous pourrions très simplement:
</p>

*   Changer la classe utilisée pour représenter un service
*   Ajouter un service
*   Modifier / Ajouter des dépendances

<span style="font-size: x-small;"><br /> </span>

#### Symfony 1.x et le factories.yml

<p style="text-align: justify;">
  A l'origine, sous Symfony 1.x, ce qui remplaçait plus ou moins l'injecteur de dépendances était le fichier <em>factories.yml</em> avec comme container l'objet <em>sfContext</em>. Ce fichier était chargé en cache (<em>factories.yml.php</em>) et les différents &#8216;services' inclus dans l'objet <em>sfContext</em> au travers de sa propriété <em>factories</em>.
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="kw2">class</span> sfContext implements ArrayAccess<br /> <span class="br0">&#123;</span><br />   <span class="kw2">protected</span><br />   <span class="re0">$dispatcher</span> <span class="sy0">=</span> <span class="kw4">null</span><span class="sy0">,</span><br />   <span class="re0">$configuration</span> <span class="sy0">=</span> <span class="kw4">null</span><span class="sy0">,</span><br />   <span class="re0">$mailerConfiguration</span> <span class="sy0">=</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">,</span><br />   <span class="re0">$factories</span> <span class="sy0">=</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br />     <span class="sy0">....</span><br />   <span class="co4">/**<br />   * Loads the symfony factories.<br />   */</span><br />   <span class="kw2">public</span> <span class="kw2">function</span> loadFactories<span class="br0">&#40;</span><span class="br0">&#41;</span><br />   <span class="br0">&#123;</span><br />     <span class="sy0">....</span><br />     <span class="co1">// include the factories configuration</span><br />     <span class="kw1">require</span><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">configuration</span><span class="sy0">-></span><span class="me1">getConfigCache</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">-></span><span class="me1">checkConfig</span><span class="br0">&#40;</span><span class="st_h">'config/factories.yml'</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br />     <span class="sy0">....</span><br />   <span class="br0">&#125;</span><br /> <span class="br0">&#125;</span>
  </div>
</div>

<p style="text-align: justify;">
  Le fichier <em>factories.yml</em> était géré par la classe <em>sfFactoryConfigHandler</em> et aboutissait dans le cache au fichier <em>factories.yml.php</em> qui se contentait d'enrichir le tableau <em>$factories</em> du <em>sfContext</em>.
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="re0">$class</span> <span class="sy0">=</span> sfConfig<span class="sy0">::</span><span class="me2">get</span><span class="br0">&#40;</span><span class="st_h">'sf_factory_controller'</span><span class="sy0">,</span> <span class="st_h">'sfFrontWebController'</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">factories</span><span class="br0">&#91;</span><span class="st_h">'controller'</span><span class="br0">&#93;</span> <span class="sy0">=</span> <span class="kw2">new</span> <span class="re0">$class</span><span class="br0">&#40;</span><span class="re0">$this</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <span class="re0">$class</span> <span class="sy0">=</span> sfConfig<span class="sy0">::</span><span class="me2">get</span><span class="br0">&#40;</span><span class="st_h">'sf_factory_request'</span><span class="sy0">,</span> <span class="st_h">'sfWebRequest'</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">factories</span><span class="br0">&#91;</span><span class="st_h">'request'</span><span class="br0">&#93;</span> <span class="sy0">=</span> <span class="kw2">new</span> <span class="re0">$class</span><span class="br0">&#40;</span><span class="sy0">...</span><span class="br0">&#41;</span>
  </div>
</div>

Les différents services étaient ensuite accessibles au travers du *sfContext : *

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="kw2">class</span> sfContext implements ArrayAccess<br /> <span class="br0">&#123;</span><br />   <span class="kw2">public</span> <span class="kw2">function</span> getController<span class="br0">&#40;</span><span class="br0">&#41;</span><br />   <span class="sy0">....</span><br />   <span class="kw2">public</span> <span class="kw2">function</span> getRequest<span class="br0">&#40;</span><span class="br0">&#41;</span><br />   <span class="sy0">...</span><br /> <span class="br0">&#125;</span>
  </div>
</div>

#### Symfony 2 et l'injection de dépendances

<p style="text-align: justify;">
  Nous arrivons maintenant à la partie intéressante: L'implémentation de l'injection de dépendances dans Symfony 2.<br /> En premier lieu, je vous conseille vivement la lecture de la documentation de Fabien Potencier dédiée au composant:<br /> <a href="http://components.symfony-project.org/dependency-injection/trunk/book/00-Introduction" target="_blank">Symfony Dependency Injection</a> (il s'agit du composant php 5.2, mais la documentation est toujours d'actualité dans le sens où le fonctionnement est identique).
</p>

Jetons un oeil aux sources du composant (*/sandbox/src/vendor/symfony/src/Symfony/Components/DependencyInjection*):
<img class="alignleft size-full wp-image-480" title="di-folder" src="/blog/wp-content/uploads/2010/06/di-folder.png" alt="di folder Symfony 2   Linjection de dépendances" width="284" height="253" /></td>
*   Le répertoire ***Dumper** *va contenir les classes permettant de dumper un ***Builder*** sous un format spécifique (Yaml, Xml, PHP, Graphviz).
*   Le répertoire ***Loader*** var contenir les classes permettant de charger des ***BuilderConfiguration*** à partir de formats spécifiques (Yaml, Xml, Ini)
*   ***Container*** est notre classe de base, ie le container qui va contenir les différents services et les paramètres associés.
*   ***Builder*** est une classe qui étend ***Container*** et qui va permettre d'auto-générer le ***Container*** à partir de la configuration, elle va consommer des objets de la classe ***BuilderConfiguration*** afin d'enrichir le ***Container***.
*   ***BuilderConfiguration*** est la classe qui permet de configurer le ***Builder***. Les objets ***BuilderConfiguration*** vont contenir des définitions de services, des ressources nécessaires au fonctionnement des services déclarés, etc...
*   ***Definition*** est la classe qui représente la définition d'un service. C'est à partir des objets de cette classe que le builder va pouvoir construire les services.

<p style="text-align: justify;">
  Le fonctionnement est somme toute assez simple. Les différents loaders vont permettre de générer des objets du type BuilderConfiguration (qui contiendront des objets Definition) qui seront mergés au sein du Builder pour aboutir à notre Container. Les dumpers permettront de faire l'opération inverse, à savoir : générer un fichier de définitions pour les formats Yaml et Xml, un output Graphviz et enfin un objet php Container en ce qui concerne le dumper PHP.<br /> Un schéma valant mieux qu'un long discours, on peut synthétiser le fonctionnement comme suit :<br /> Note: Le loader de fichiers .ini n'a pas été représenté puisqu'il ne permet que de définir des paramètres.
</p>

[<img class="aligncenter size-full wp-image-486" title="injection-dependances" src="/blog/wp-content/uploads/2010/06/injection-dependances.png" alt="injection dependances Symfony 2   Linjection de dépendances" width="846" height="468" />][1]

<p style="text-align: justify;">
  <strong><em>Le dumper Graphviz</em></strong>: Ce dumper a pour objectif de générer un dump exploitable par le logiciel <a href="http://graphviz.org/" target="_blank">Graphviz</a> ; il va nous permettre d'obtenir un graphique représentant nos services et leurs dépendances.<br /> <strong><em>Le cache:</em></strong> Afin d'accélérer le fonctionnement des applications utilisant l'injection de dépendances, il est évident que les différents fichiers de configuration ne seront pas reparsés à chaque appel. En réalité, le container sera dumpé sous la forme d'une classe PHP et mise en cache.
</p>

<p style="text-align: justify;">
  Attardons nous un peu sur ces fameuses définitions de services qui sont un peu la pierre angulaire de l'injection de dépendances.
</p>

<div>
  <table style="width: 100%;" border="0">
    <tr>
      <th colspan="2">
        Définition d'un service (La classe Definition)
      </th>
    </tr>

    <tr>
      <td>
        <span style="-webkit-border-horizontal-spacing: 0px; -webkit-border-vertical-spacing: 0px; font-size: 13px; line-height: 19px;">$class</span>
      </td>

      <td>
        Il s'agit de la classe de base représentant le service
      </td>
    </tr>

    <tr>
      <td>
        <span style="-webkit-border-horizontal-spacing: 0px; -webkit-border-vertical-spacing: 0px; font-size: 13px; line-height: 19px;">$file</span>
      </td>

      <td>
        Eventuel fichier à inclure avant de déclarer le fichier
      </td>
    </tr>

    <tr>
      <td>
        <span style="-webkit-border-horizontal-spacing: 0px; -webkit-border-vertical-spacing: 0px; font-size: 13px; line-height: 19px;">$constructor</span>
      </td>

      <td>
        Le constructeur de la classe représentant le service
      </td>
    </tr>

    <tr>
      <td>
        <span style="-webkit-border-horizontal-spacing: 0px; -webkit-border-vertical-spacing: 0px; font-size: 13px; line-height: 19px;">$arguments</span>
      </td>

      <td>
        Les arguments à passer au constructeur
      </td>
    </tr>

    <tr>
      <td>
        <span style="-webkit-border-horizontal-spacing: 0px; -webkit-border-vertical-spacing: 0px; font-size: 13px; line-height: 19px;">$shared</span>
      </td>

      <td>
        Booléen définissant si le service est partagé (singleton) ou réinstancié quand on y accède
      </td>
    </tr>

    <tr>
      <td>
        <span style="-webkit-border-horizontal-spacing: 0px; -webkit-border-vertical-spacing: 0px; font-size: 13px; line-height: 19px;">$calls</span>
      </td>

      <td>
        Des éventuelles méthodes et leurs paramètres à appeler après avoir initialisé le service
      </td>
    </tr>

    <tr>
      <td>
        <span style="-webkit-border-horizontal-spacing: 0px; -webkit-border-vertical-spacing: 0px; font-size: 13px; line-height: 19px;">$annotations</span>
      </td>

      <td>
        Annotations associées au service (nous y reviendrons plus tard)
      </td>
    </tr>
  </table>
</div>

A partir de ces informations, le builder sera capable de recréer la méthode permettant d'accéder au service.
De façon très synthétique et grossière, la définition nous permettrait d'aboutir à une méthode comme ceci:

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="kw2">public</span> <span class="kw2">function</span> getServiceXXX<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="br0">&#123;</span><br />   <span class="kw1">require</span> <span class="re0">$file</span><span class="sy0">;</span><br />   <span class="re0">$service</span> <span class="sy0">=</span> <span class="kw2">new</span> <span class="re0">$class</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br />   <span class="re0">$service</span><span class="sy0">-></span><span class="re0">$constructor</span><span class="br0">&#40;</span><span class="re0">$arguments</span><span class="br0">&#41;</span><span class="sy0">;</span><br />   <span class="kw1">foreach</span> <span class="br0">&#40;</span><span class="re0">$calls</span> <span class="kw1">as</span> <span class="re0">$call</span><span class="br0">&#41;</span><br />   <span class="re0">$service</span><span class="sy0">-></span><span class="re0">$call</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br />   <span class="kw1">return</span> <span class="re0">$service</span><span class="sy0">;</span><br /> <span class="br0">&#125;</span>
  </div>
</div>

<p style="text-align: justify;">
  <strong>Note: </strong>Dans Symfony 2, les bundles de base utilisent le loader XML pour définir leurs services. Le format XML a l'avantage de pouvoir être validé via <em>Components/DependencyInjection/Loader/schema/dic/services/services-1.0.xsd</em>
</p>

#### Exemple de service: le logger du ZendBundle

<p style="text-align: justify;">
  Dans le répertoire Ressources/config/ du Bundle (<em> /sandbox/src/vendor/symfony/src/Symfony/Framework/ZendBundle</em>), on trouve le fichier : logger.xml. Il s'agit de notre fichier de configuration. Notre fichier qui sera chargé par le loader pour enrichir notre container.
</p>

<div class="codecolorer-container xml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="xml codecolorer">
    <span class="sc-1"><!--?xml version="1.0" ?--></span><br /> <br /> Symfony\Framework\ZendBundle\Logger\LoggerZend_Log::CRITSymfony\Framework\ZendBundle\Logger\DebugLoggerZend_Log_Writer_StreamZend_Log_Formatter_Simple%%timestamp%% %%priorityName%%: %%message%%<br /> <br /> %zend.logger.path%<br /> <br /> %zend.formatter.filesystem.format%<br /> <br /> %zend.logger.priority%
  </div>
</div>

<p style="text-align: justify;">
  5 services sont définis: zend.logger, zend.logger.writer.filesystem, zend.formatter.filesystem, zend.logger.writer.debug et zend.logger.filter.<br /> Les paramètres sont également définis dans le fichier.
</p>

<p style="text-align: justify;">
  Ces différentes déclaration aboutiront à la création de méthodes de ce type dans le container généré par le Dumper PHP.
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="kw2">protected</span> <span class="kw2">function</span> getZend_LoggerService<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="br0">&#123;</span><br />   <span class="kw1">if</span> <span class="br0">&#40;</span><a href="http://www.php.net/isset"><span class="kw3">isset</span></a><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'zend.logger'</span><span class="br0">&#93;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><br />   <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'zend.logger'</span><span class="br0">&#93;</span><span class="sy0">;</span><br /> <br />   <span class="re0">$instance</span> <span class="sy0">=</span> <span class="kw2">new</span> Symfony\Framework\ZendBundle\Logger\Logger<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br />   <span class="re0">$instance</span><span class="sy0">-></span><span class="me1">addWriter</span><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">getZend_Logger_Writer_FilesystemService</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br />   <span class="re0">$instance</span><span class="sy0">-></span><span class="me1">addWriter</span><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">getZend_Logger_Writer_DebugService</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <br />   <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'zend.logger'</span><span class="br0">&#93;</span> <span class="sy0">=</span> <span class="re0">$instance</span><span class="sy0">;</span><br /> <span class="br0">&#125;</span><br /> <br /> <span class="kw2">protected</span> <span class="kw2">function</span> getZend_Logger_Writer_FilesystemService<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="br0">&#123;</span><br />   <span class="kw1">if</span> <span class="br0">&#40;</span><a href="http://www.php.net/isset"><span class="kw3">isset</span></a><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'zend.logger.writer.filesystem'</span><span class="br0">&#93;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><br />   <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'zend.logger.writer.filesystem'</span><span class="br0">&#93;</span><span class="sy0">;</span><br /> <br />   <span class="re0">$instance</span> <span class="sy0">=</span> <span class="kw2">new</span> Zend_Log_Writer_Stream<span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">getParameter</span><span class="br0">&#40;</span><span class="st_h">'zend.logger.path'</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br />   <span class="re0">$instance</span><span class="sy0">-></span><span class="me1">addFilter</span><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">getZend_Logger_FilterService</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br />   <span class="re0">$instance</span><span class="sy0">-></span><span class="me1">setFormatter</span><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">getZend_Formatter_FilesystemService</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <br />   <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'zend.logger.writer.filesystem'</span><span class="br0">&#93;</span> <span class="sy0">=</span> <span class="re0">$instance</span><span class="sy0">;</span><br /> <span class="br0">&#125;</span><br /> <br /> <span class="kw2">protected</span> <span class="kw2">function</span> getZend_Formatter_FilesystemService<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="br0">&#123;</span><br />   <span class="kw1">if</span> <span class="br0">&#40;</span><a href="http://www.php.net/isset"><span class="kw3">isset</span></a><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'zend.formatter.filesystem'</span><span class="br0">&#93;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><br />   <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'zend.formatter.filesystem'</span><span class="br0">&#93;</span><span class="sy0">;</span><br /> <br />   <span class="re0">$instance</span> <span class="sy0">=</span> <span class="kw2">new</span> Zend_Log_Formatter_Simple<span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">getParameter</span><span class="br0">&#40;</span><span class="st_h">'zend.formatter.filesystem.format'</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <br />   <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'zend.formatter.filesystem'</span><span class="br0">&#93;</span> <span class="sy0">=</span> <span class="re0">$instance</span><span class="sy0">;</span><br /> <span class="br0">&#125;</span><br /> <br /> <span class="kw2">protected</span> <span class="kw2">function</span> getZend_Logger_Writer_DebugService<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="br0">&#123;</span><br />   <span class="kw1">if</span> <span class="br0">&#40;</span><a href="http://www.php.net/isset"><span class="kw3">isset</span></a><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'zend.logger.writer.debug'</span><span class="br0">&#93;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><br />   <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'zend.logger.writer.debug'</span><span class="br0">&#93;</span><span class="sy0">;</span><br /> <br />   <span class="re0">$instance</span> <span class="sy0">=</span> <span class="kw2">new</span> Symfony\Framework\ZendBundle\Logger\DebugLogger<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br />   <span class="re0">$instance</span><span class="sy0">-></span><span class="me1">addFilter</span><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">getZend_Logger_FilterService</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <br />   <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'zend.logger.writer.debug'</span><span class="br0">&#93;</span> <span class="sy0">=</span> <span class="re0">$instance</span><span class="sy0">;</span><br /> <span class="br0">&#125;</span><br /> <br /> <span class="kw2">protected</span> <span class="kw2">function</span> getZend_Logger_FilterService<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="br0">&#123;</span><br />   <span class="kw1">if</span> <span class="br0">&#40;</span><a href="http://www.php.net/isset"><span class="kw3">isset</span></a><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'zend.logger.filter'</span><span class="br0">&#93;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><br />   <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'zend.logger.filter'</span><span class="br0">&#93;</span><span class="sy0">;</span><br /> <br />   <span class="re0">$instance</span> <span class="sy0">=</span> <span class="kw2">new</span> Zend_Log_Filter_Priority<span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">getParameter</span><span class="br0">&#40;</span><span class="st_h">'zend.logger.priority'</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <br />   <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'zend.logger.filter'</span><span class="br0">&#93;</span> <span class="sy0">=</span> <span class="re0">$instance</span><span class="sy0">;</span><br /> <span class="br0">&#125;</span><br /> <br /> <span class="kw2">protected</span> <span class="kw2">function</span> getLoggerService<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="br0">&#123;</span><br />   <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">getZend_LoggerService</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <span class="br0">&#125;</span>
  </div>
</div>

<p style="text-align: justify;">
  <span style="text-decoration: underline;"><strong>Les alias :<br /> </strong></span>Comme vous pouvez le constater, une méthode getLoggerService est générée. Cette méthode est générée car un alias a été défini lors de la configuration du builder (ligne 59 du fichier ZendExtension.php).
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="re0">$configuration</span><span class="sy0">-></span><span class="me1">setAlias</span><span class="br0">&#40;</span><span class="st_h">'logger'</span><span class="sy0">,</span> <span class="st_h">'zend.logger'</span><span class="br0">&#41;</span><span class="sy0">;</span>
  </div>
</div>

<p style="text-align: justify;">
  <span style="text-decoration: underline;"><strong>Les annotations :</strong></span><br /> Les annotations permettent de rajouter des propriétés à un service. Ces annotations ne sont pas exploitées directement par l'injecteur de dépendances mais par des composants tierces. Une annotation possède un nom et des attributs. Par exemple, dans le fichier de définitions: Framework/WebBundle/Resources/config/web.xml, on trouve la définition suivante sur le service <strong>request_parser</strong>:
</p>

<div class="codecolorer-container xml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="xml codecolorer">

  </div>
</div>

<p style="text-align: justify;">
  Cette définition rajoute donc une annotation "<strong>kernel.listener</strong>" sur le service <strong>request_parser</strong> avec comme attributs <strong>event</strong> et <strong>method</strong>.<br /> Dans l'exemple précédent, le <strong>Kernel</strong> se servira de cette annotation pour définir le service en tant qu'écouteur (listener) sur l'event "core.request" (nous y reviendrons dans le prochain article).
</p>

**<span style="text-decoration: underline;">Les extensions : </span>**

<p style="text-align: justify;">
  Lorsque les différents Bundles vont définir leurs services, ceux-ci ne seront pas directement chargés dans la configuration du Builder global. Au lieu de ça, les Bundles vont définir des extensions. Les extensions ne sont qu'un wrapper permettant de charger la configuration d'un bundle "à la demande". Les extensions vont permettre de mieux organiser les différents services. Pour expliquer le fonctionnement des extensions, nous allons étudier le Bundle Web (Framework/WebBundle).
</p>

Le fichier Bundle.php du WebBundle ressemble à ceci:

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="kw2">class</span> Bundle <span class="kw2">extends</span> BaseBundle<br /> <span class="br0">&#123;</span><br />   <span class="kw2">public</span> <span class="kw2">function</span> buildContainer<span class="br0">&#40;</span>ContainerInterface <span class="re0">$container</span><span class="br0">&#41;</span><br />   <span class="br0">&#123;</span><br />     Loader<span class="sy0">::</span><span class="me2">registerExtension</span><span class="br0">&#40;</span><span class="kw2">new</span> WebExtension<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br />     <span class="sy0">....</span><br />   <span class="br0">&#125;</span><br /> <span class="br0">&#125;</span>
  </div>
</div>

<p style="text-align: justify;">
  La méthode <strong>buildContainer()</strong> définie sur chaque Bundle permet d'indiquer au <strong>Kernel</strong> comment configurer le Container pour ce Bundle. Ici, le Bundle va se contenter de déclarer une extension sur le loader. Il enregistre donc l'extension <strong>WebExtension</strong> dans le loader.<br /> Voyons maintenant le fichier WebExtension.php
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="kw2">class</span> WebExtension <span class="kw2">extends</span> LoaderExtension<br /> <span class="br0">&#123;</span><br />   <span class="kw2">protected</span> <span class="re0">$resources</span> <span class="sy0">=</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><br />   <span class="st_h">'templating'</span><span class="sy0">=></span> <span class="st_h">'templating.xml'</span><span class="sy0">,</span><br />   <span class="st_h">'web'</span> <span class="sy0">=></span> <span class="st_h">'web.xml'</span><span class="sy0">,</span><br />   <span class="st_h">'debug'</span> <span class="sy0">=></span> <span class="st_h">'debug.xml'</span><span class="sy0">,</span><br />   <span class="st_h">'user'</span> <span class="sy0">=></span> <span class="st_h">'user.xml'</span><span class="sy0">,</span><br />   <span class="br0">&#41;</span><span class="sy0">;</span><br /> <br />   <span class="kw2">public</span> <span class="kw2">function</span> webLoad<span class="br0">&#40;</span><span class="re0">$config</span><span class="br0">&#41;</span><br />   <span class="br0">&#123;</span><br />     <span class="re0">$configuration</span> <span class="sy0">=</span> <span class="kw2">new</span> BuilderConfiguration<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <br />     <span class="re0">$loader</span> <span class="sy0">=</span> <span class="kw2">new</span> XmlFileLoader<span class="br0">&#40;</span>__DIR__<span class="sy0">.</span><span class="st_h">'/../Resources/config'</span><span class="br0">&#41;</span><span class="sy0">;</span><br />     <span class="re0">$configuration</span><span class="sy0">-></span><span class="me1">merge</span><span class="br0">&#40;</span><span class="re0">$loader</span><span class="sy0">-></span><span class="me1">load</span><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">resources</span><span class="br0">&#91;</span><span class="st_h">'web'</span><span class="br0">&#93;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <br />     <span class="kw1">return</span> <span class="re0">$configuration</span><span class="sy0">;</span><br />   <span class="br0">&#125;</span><br /> <br />   <span class="kw2">public</span> <span class="kw2">function</span> userLoad<span class="br0">&#40;</span><span class="re0">$config</span><span class="br0">&#41;</span><br />   <span class="br0">&#123;</span><span class="sy0">....</span><span class="br0">&#125;</span><br /> <br />   <span class="kw2">public</span> <span class="kw2">function</span> templatingLoad<span class="br0">&#40;</span><span class="re0">$config</span><span class="br0">&#41;</span><br />   <span class="br0">&#123;</span><span class="sy0">....</span><span class="br0">&#125;</span><br /> <br />   <span class="kw2">public</span> <span class="kw2">function</span> debugLoad<span class="br0">&#40;</span><span class="re0">$config</span><span class="br0">&#41;</span><br />   <span class="br0">&#123;</span><span class="sy0">....</span><span class="br0">&#125;</span><br />   <span class="sy0">...</span><br />   <span class="kw2">public</span> <span class="kw2">function</span> getAlias<span class="br0">&#40;</span><span class="br0">&#41;</span><br />   <span class="br0">&#123;</span><br />     <span class="kw1">return</span> <span class="st_h">'web'</span><span class="sy0">;</span><br />   <span class="br0">&#125;</span>
  </div>
</div>

<p style="text-align: justify;">
  On voit que les extensions permettent de charger des configurations de Builder un peu différemment. Dans les différents fichiers de configuration manipulés, quel que soit le format du loader utilisé, on se contente de déclarer des services. Cependant, il faut savoir qu'il est également possible de charger et configurer des extensions ainsi définies.<br /> Ce qu'on trouve en général dans les fichiers config.yml / config_dev.yml / config_prod.yml d'une application utilise principalement la déclaration des extensions prédéfinies par les Bundles et ne déclare pas de nouveaux services.<br /> Exemple:
</p>

<div class="codecolorer-container yaml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="yaml codecolorer">
    <span class="co3">kernel.config</span><span class="sy2">: </span>~<span class="co3"><br /> web.web</span><span class="sy2">: </span>~<span class="co3"><br /> web.templating</span><span class="sy2">: </span>~<span class="co4"><br /> web.debug</span>:<span class="co3"><br /> exception</span><span class="sy2">: </span><span class="co2">%kernel.debug%</span><span class="co3"><br /> toolbar</span><span class="sy2">: </span><span class="co2">%kernel.debug%</span><span class="co4"><br /> zend.logger</span>:<span class="co3"><br /> priority</span><span class="sy2">: </span>info<span class="co3"><br /> path</span><span class="sy2">: </span><span class="co2">%kernel.root_dir%/logs/%kernel.environment%.log</span><span class="co4"><br /> swift.mailer</span>:<span class="co3"><br /> transport</span><span class="sy2">: </span>gmail
  </div>
</div>

<p style="text-align: justify;">
  Dans ce fichier de configuration, seules des extensions sont déclarées. A noter que si une extension n'est pas déclarée dans le fichier de configuration d'un Container, ses services ne seront pas inclus dans le Container final.<br /> Le but des extensions est de fournir une méthode plus "propre" pour déclarer ses services, mais le fonctionnement reste identique, les Bundles vont déclarer des services comme vu précédemment, les extensions ne fournissant qu'un moyen de charger ou configurer ces déclarations.
</p>

#### Mise en pratique

<p style="text-align: justify;">
  Afin de bien comprendre le fonctionnement, je vous propose une petite mise en pratique au travers de quelques exemples. Les exemples seront basés sur la sandbox proposée sur le site de <a href="http://www.symfony-reloaded.org" target="_blank">Symfony 2</a> et l'application Hello World !
</p>

<p style="text-align: justify;">
  Au niveau de la sandbox, les fichiers de configuration du Builder sont : config.yml, config_dev.yml et config_prod.yml. La méthode qui va charger ces fichiers est la méthode registerContainerConfiguration() de la classe HelloKernel. Nous allons travailler dans le cadre de nos exemples sur l'environnement de production. Le Container final généré se trouve dans le cache hello/cache/prod/helloProjectContainer.php
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="kw2">class</span> HelloKernel <span class="kw2">extends</span> Kernel<br /> <span class="br0">&#123;</span><br />   <span class="sy0">...</span><br />   <span class="kw2">public</span> <span class="kw2">function</span> registerContainerConfiguration<span class="br0">&#40;</span><span class="br0">&#41;</span><br />   <span class="br0">&#123;</span><br />     <span class="re0">$loader</span> <span class="sy0">=</span> <span class="kw2">new</span> ContainerLoader<span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">getBundleDirs</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br />     <span class="re0">$configuration</span> <span class="sy0">=</span> <span class="re0">$loader</span><span class="sy0">-></span><span class="me1">load</span><span class="br0">&#40;</span>__DIR__<span class="sy0">.</span><span class="st_h">'/config/config_'</span><span class="sy0">.</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">getEnvironment</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">.</span><span class="st_h">'.yml'</span><span class="br0">&#41;</span><span class="sy0">;</span><br />     <span class="kw1">return</span> <span class="re0">$configuration</span><span class="sy0">;</span><br />   <span class="br0">&#125;</span>
  </div>
</div>

<p style="text-align: justify;">
  C'est au niveau de cette méthode que nous allons intervenir dans un premier temps.
</p>

<p style="text-align: justify;">
  <span style="text-decoration: underline;"><strong>Modifier la classe d'un service:</strong></span><br /> Dans notre premier exemple, nous allons tenter de modifier un service. En l'occurrence, le service représentant la requête, le bien nommé "RequestService". Notre container (HelloProjectContainer.php) définit le service ainsi:
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="kw2">protected</span> <span class="kw2">function</span> getRequestService<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="br0">&#123;</span><br />   <span class="kw1">if</span> <span class="br0">&#40;</span><a href="http://www.php.net/isset"><span class="kw3">isset</span></a><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'request'</span><span class="br0">&#93;</span><span class="br0">&#41;</span><span class="br0">&#41;</span> <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'request'</span><span class="br0">&#93;</span><span class="sy0">;</span><br />   <span class="re0">$instance</span> <span class="sy0">=</span> <span class="kw2">new</span> Symfony\Components\RequestHandler\Request<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br />   <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'request'</span><span class="br0">&#93;</span> <span class="sy0">=</span> <span class="re0">$instance</span><span class="sy0">;</span><br /> <span class="br0">&#125;</span>
  </div>
</div>

<p style="text-align: justify;">
  Pour modifier la classe utilisée pour ce service, rien de plus simple, il nous suffit de modifier la configuration du container.<br /> Nous allons simplement modifier la classe associée dans la définition de ce service de cette façon :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="kw2">public</span> <span class="kw2">function</span> registerContainerConfiguration<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="br0">&#123;</span><br />   <span class="re0">$loader</span> <span class="sy0">=</span> <span class="kw2">new</span> ContainerLoader<span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">getBundleDirs</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br />   <span class="re0">$configuration</span> <span class="sy0">=</span> <span class="re0">$loader</span><span class="sy0">-></span><span class="me1">load</span><span class="br0">&#40;</span>__DIR__<span class="sy0">.</span><span class="st_h">'/config/config_'</span><span class="sy0">.</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">getEnvironment</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">.</span><span class="st_h">'.yml'</span><span class="br0">&#41;</span><span class="sy0">;</span><br />   <span class="re0">$configuration</span><span class="sy0">-></span><span class="me1">getDefinition</span><span class="br0">&#40;</span><span class="st_h">'request'</span><span class="br0">&#41;</span><span class="sy0">-></span><span class="me1">setClass</span><span class="br0">&#40;</span><span class="st_h">'MaClasseRequest'</span><span class="br0">&#41;</span><span class="sy0">;</span> <span class="co1">// Nous modifions la définition du service "request"</span><br />   <span class="kw1">return</span> <span class="re0">$configuration</span><span class="sy0">;</span><br /> <span class="br0">&#125;</span>
  </div>
</div>

<p style="text-align: justify;">
  C'est fait ! Si on vide le cache et que l'on rappelle une url sur le projet, la définition de notre service dans le container ressemble maintenant à ça:
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="kw2">protected</span> <span class="kw2">function</span> getRequestService<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="br0">&#123;</span><br />     <span class="kw1">if</span> <span class="br0">&#40;</span><a href="http://www.php.net/isset"><span class="kw3">isset</span></a><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'request'</span><span class="br0">&#93;</span><span class="br0">&#41;</span><span class="br0">&#41;</span> <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'request'</span><span class="br0">&#93;</span><span class="sy0">;</span><br />     <span class="re0">$instance</span> <span class="sy0">=</span> <span class="kw2">new</span> MaClasseRequest<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br />     <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'request'</span><span class="br0">&#93;</span> <span class="sy0">=</span> <span class="re0">$instance</span><span class="sy0">;</span><br /> <span class="br0">&#125;</span>
  </div>
</div>

<p style="text-align: justify;">
  <span style="text-decoration: underline;"><strong>Créer un service:</strong></span><br /> Dans cette exemple, nous allons voir comment déclarer un nouveau service. Nous allons tenter de déclarer le service "Monservice", utilisant la classe "Maclasse", dépendante du service "logger" sur laquelle on appellera les méthodes init1() et init2() à qui on passera le paramètre "coucou". Nous avions vu que le fichier config.yml déclarait les extensions à utiliser, mais c'est un fichier de configuration de Container comme un autre, nous pouvons donc y définir des services.
</p>

<div class="codecolorer-container yaml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="yaml codecolorer">
    <span class="co4">services</span>:<span class="co4"><br />   monservice</span>:<span class="co3"><br />     id</span><span class="sy2">: </span>monservice<span class="co3"><br />     class</span><span class="sy2">: </span>Maclasse<span class="co4"><br />     arguments</span><span class="sy2">:<br /> </span>      - @logger<span class="co4"><br />     calls</span><span class="sy2">:<br /> </span>      - <span class="br0">&#91;</span>init1, <span class="br0">&#91;</span><span class="br0">&#93;</span><span class="br0">&#93;</span><br />       - <span class="br0">&#91;</span>init2, <span class="br0">&#91;</span>'coucou'<span class="br0">&#93;</span><span class="br0">&#93;</span>
  </div>
</div>

C'est tout. Nous venons de déclarer un nouveau service dans notre application. Si nous jetons un oeil au Container généré:

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="kw2">protected</span> <span class="kw2">function</span> getMonserviceService<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="br0">&#123;</span><br />     <span class="kw1">if</span> <span class="br0">&#40;</span><a href="http://www.php.net/isset"><span class="kw3">isset</span></a><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'monservice'</span><span class="br0">&#93;</span><span class="br0">&#41;</span><span class="br0">&#41;</span> <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'monservice'</span><span class="br0">&#93;</span><span class="sy0">;</span><br /> <br />     <span class="re0">$instance</span> <span class="sy0">=</span> <span class="kw2">new</span> Maclasse<span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">getService</span><span class="br0">&#40;</span><span class="st_h">'logger'</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br />     <span class="re0">$instance</span><span class="sy0">-></span><span class="me1">init1</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br />     <span class="re0">$instance</span><span class="sy0">-></span><span class="me1">init2</span><span class="br0">&#40;</span><span class="st_h">'coucou'</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <br />     <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'monservice'</span><span class="br0">&#93;</span> <span class="sy0">=</span> <span class="re0">$instance</span><span class="sy0">;</span><br /> <span class="br0">&#125;</span>
  </div>
</div>

#### En conclusion

<p style="text-align: justify;">
  Comme vous avez pu le découvrir dans cet article, l'injecteur de dépendances de Symfony 2 est vraiment puissant. Nous pouvons customizer, tweaker, étendre le fonctionnement du coeur même du Framework par ce biais.
</p>

<p style="text-align: justify;">
  L'injection de dépendances ne devrait plus avoir de secrets pour vous !
</p>

<p style="text-align: justify;">
  PS: Merci à Xavier R pour sa relecture et ses remarques avisées.
</p>

 [1]: /blog/wp-content/uploads/2010/06/injection-dependances.png