
<div style="border: 1px solid lightgrey; padding: 10px; background-color: lightyellow; margin: 20px 0px;">
  <strong><span style="text-decoration: underline;">Avertissement</span> :</strong><br /> Depuis la rédaction de cet article sur l&#8217;injection de dépendances, le coeur de Symfony2 a évolué de façon notable, en particulier certaines classes du répertoire <em>DependencyInjection</em>mentionnées dans cet article ont été renommées, révisées, ou refondues. Toutefois, les principes généraux décrits dans cet article restent d&#8217;actualité.Le 21 décembre 2010</p>
</div>

<img class="alignleft" title="dependency-injection-symfony" src="/blog/wp-content/uploads/2010/06/dependency-injection-symfony.png" alt="dependency injection symfony Symfony 2   Linjection de dépendances" width="278" height="236" />

<div style="text-align: justify;">
  Cet article est le premier d&#8217;une série à venir sur Symfony 2. Pour commencer, je vous invite à télécharger la <a href="http://symfony-reloaded.org/code" target="_blank">sandbox</a> de Symfony 2. J&#8217;ai décidé de commencer par l&#8217;injection de dépendances car il s&#8217;agit d&#8217;un composant clé de Symfony 2, et que la bonne compréhension de cette nouvelle version du Framework doit forcément en passer par là. L&#8217;injection de dépendance est réellement au coeur du Framework.
</div>

<p style="text-align: justify;">
  Nous allons commencer par tenter d&#8217;éclaircir cette notion (ou ce design pattern), puis nous verrons plus concrètement l&#8217;implémentation qui en est faite au sein de Symfony 2. Commençons par une petite définition.
</p>

<h5 style="text-align: justify;">
  Définition Wikipedia:
</h5>

<p style="text-align: justify;">
  &#8220;<strong>L&#8217;injection de dépendances</strong> (Dependency Injection) est un mécanisme qui permet d&#8217;implanter le principe de l&#8217;Inversion de contrôle. Il consiste à créer dynamiquement (injecter) les dépendances entre les différentes classes en s&#8217;appuyant généralement sur une description (fichier de configuration). Ainsi les dépendances entre composants logiciels ne sont plus exprimées dans le code de manière statique mais déterminées dynamiquement à l&#8217;exécution.&#8221;
</p>

#### Qu&#8217;est ce que l&#8217;injection de dépendances ?

<p style="text-align: justify;">
  En programmation objet, et particulièrement avec les Frameworks, nous sommes souvent amenés à manipuler un grand nombre de classes d&#8217;objet différentes. Ces différents objets sont chargés de fournir une fonctionnalité ou un &#8220;service&#8221;. Dans une application standard, nous serons par exemple amenés à manipuler :
</p>

*   Un objet **User**, supposé représenter l&#8217;utilisateur connecté à l&#8217;application
*   Un objet **Session**, pour la gestion de la session de l&#8217;utilisateur
*   Un objet **Requete**, représentant la requête envoyée par un client
*   Un objet **Réponse**, pour la réponse que l&#8217;application va renvoyer
*   Un objet **Log**, pour pouvoir gérer des logs
*   Un objet **Mailer**, pour pouvoir envoyer des mails
*   etc &#8230;

<p style="text-align: justify;">
  La nécessité de l&#8217;injecteur de dépendances provient, d&#8217;une part, du fait que certains de ces objets sont liés entre eux, ou plutôt &#8220;dépendants&#8221; les uns des autres, d&#8217;autre part, que les objets sont de plus en plus découplés et scindés en classes bien particulières. Par exemple, le service &#8220;<strong>Mailer</strong>&#8221; utilisera peut être le service &#8220;<strong>Log</strong>&#8221; pour enregistrer les mails qui sont envoyés par l&#8217;application.<br /> L&#8217;idée de l&#8217;injection de dépendances est d&#8217;utiliser un fichier de configuration pour auto-générer une classe (un &#8220;container&#8221;) qui contiendra des méthodes nous permettant d&#8217;accéder à ces services ; elle contiendra l&#8217;initialisation des services (via l&#8217;instanciation de la classe qui les représente), gérera les dépendances de ces services (via les constructeurs ou l&#8217;appel de méthodes), la configuration des services, etc&#8230;.
</p>

Pour illustrer ce concept, voyons un petit exemple.  
Imaginons que nous ayons trois services:

*   un mailer (chargé d&#8217;envoyer des mails)
*   un logger (chargé d&#8217;enregistrer des logs)
*   un router (chargé du routing)

L&#8217;injecteur de dépendances pourrait ressembler à quelque chose comme ça :

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
          <span class="kw2">class</span> Container<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="br0">&#123;</span><br /> &nbsp; <span class="kw2">public</span> <span class="kw2">function</span> getServiceMailer<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> &nbsp; <span class="br0">&#123;</span><br /> &nbsp; &nbsp; <span class="re0">$service</span> <span class="sy0">=</span> <span class="kw2">new</span> MonMailer<span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">getServiceLogger</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; &nbsp; <span class="re0">$service</span><span class="sy0">-></span><span class="me1">initialiser</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; &nbsp; <span class="kw1">return</span> <span class="re0">$mailer</span><span class="sy0">;</span><br /> &nbsp; <span class="br0">&#125;</span><br /> <br /> &nbsp; <span class="kw2">public</span> <span class="kw2">function</span> getServiceLogger<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> &nbsp; <span class="br0">&#123;</span><br /> &nbsp; &nbsp; <span class="re0">$service</span> <span class="sy0">=</span> <span class="kw2">new</span> MonLogger<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; &nbsp; <span class="kw1">return</span> <span class="re0">$service</span><span class="sy0">;</span><br /> &nbsp; <span class="br0">&#125;</span><br /> <br /> &nbsp; <span class="kw2">public</span> <span class="kw2">function</span> getServiceRouteur<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> &nbsp; <span class="br0">&#123;</span><br /> &nbsp; &nbsp; <span class="re0">$service</span> <span class="sy0">=</span> <span class="kw2">new</span> MonRouteur<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; &nbsp; <span class="re0">$service</span><span class="sy0">-></span><span class="me1">setLogger</span><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">getServiceLogger</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; &nbsp; <span class="kw1">return</span> <span class="re0">$service</span><span class="sy0">;</span><br /> &nbsp; <span class="br0">&#125;</span><br /> <span class="br0">&#125;</span>
        </div>
      </div>
    </td>
  </tr>
</table>

<p style="text-align: justify;">
  L&#8217;injection de dépendances permet donc une gestion beaucoup plus claire et souple de ces différents &#8220;services&#8221;. En modifiant ce fichier de configuration nous pourrions très simplement:
</p>

*   Changer la classe utilisée pour représenter un service
*   Ajouter un service
*   Modifier / Ajouter des dépendances

<span style="font-size: x-small;"><br /> </span>

#### Symfony 1.x et le factories.yml

<p style="text-align: justify;">
  A l&#8217;origine, sous Symfony 1.x, ce qui remplaçait plus ou moins l&#8217;injecteur de dépendances était le fichier <em>factories.yml</em> avec comme container l&#8217;objet <em>sfContext</em>. Ce fichier était chargé en cache (<em>factories.yml.php</em>) et les différents &#8216;services&#8217; inclus dans l&#8217;objet <em>sfContext</em> au travers de sa propriété <em>factories</em>.
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="kw2">class</span> sfContext implements ArrayAccess<br /> <span class="br0">&#123;</span><br /> &nbsp; <span class="kw2">protected</span><br /> &nbsp; <span class="re0">$dispatcher</span> <span class="sy0">=</span> <span class="kw4">null</span><span class="sy0">,</span><br /> &nbsp; <span class="re0">$configuration</span> <span class="sy0">=</span> <span class="kw4">null</span><span class="sy0">,</span><br /> &nbsp; <span class="re0">$mailerConfiguration</span> <span class="sy0">=</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">,</span><br /> &nbsp; <span class="re0">$factories</span> <span class="sy0">=</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; &nbsp; <span class="sy0">....</span><br /> &nbsp; <span class="co4">/**<br /> &nbsp; * Loads the symfony factories.<br /> &nbsp; */</span><br /> &nbsp; <span class="kw2">public</span> <span class="kw2">function</span> loadFactories<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> &nbsp; <span class="br0">&#123;</span><br /> &nbsp; &nbsp; <span class="sy0">....</span><br /> &nbsp; &nbsp; <span class="co1">// include the factories configuration</span><br /> &nbsp; &nbsp; <span class="kw1">require</span><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">configuration</span><span class="sy0">-></span><span class="me1">getConfigCache</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">-></span><span class="me1">checkConfig</span><span class="br0">&#40;</span><span class="st_h">'config/factories.yml'</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; &nbsp; <span class="sy0">....</span><br /> &nbsp; <span class="br0">&#125;</span><br /> <span class="br0">&#125;</span>
  </div>
</div>

<p style="text-align: justify;">
  Le fichier <em>factories.yml</em> était géré par la classe <em>sfFactoryConfigHandler</em> et aboutissait dans le cache au fichier <em>factories.yml.php</em> qui se contentait d&#8217;enrichir le tableau <em>$factories</em> du <em>sfContext</em>.
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="re0">$class</span> <span class="sy0">=</span> sfConfig<span class="sy0">::</span><span class="me2">get</span><span class="br0">&#40;</span><span class="st_h">'sf_factory_controller'</span><span class="sy0">,</span> <span class="st_h">'sfFrontWebController'</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">factories</span><span class="br0">&#91;</span><span class="st_h">'controller'</span><span class="br0">&#93;</span> <span class="sy0">=</span> <span class="kw2">new</span> <span class="re0">$class</span><span class="br0">&#40;</span><span class="re0">$this</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <span class="re0">$class</span> <span class="sy0">=</span> sfConfig<span class="sy0">::</span><span class="me2">get</span><span class="br0">&#40;</span><span class="st_h">'sf_factory_request'</span><span class="sy0">,</span> <span class="st_h">'sfWebRequest'</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">factories</span><span class="br0">&#91;</span><span class="st_h">'request'</span><span class="br0">&#93;</span> <span class="sy0">=</span> <span class="kw2">new</span> <span class="re0">$class</span><span class="br0">&#40;</span><span class="sy0">...</span><span class="br0">&#41;</span>
  </div>
</div>

Les différents services étaient ensuite accessibles au travers du *sfContext : *

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="kw2">class</span> sfContext implements ArrayAccess<br /> <span class="br0">&#123;</span><br /> &nbsp; <span class="kw2">public</span> <span class="kw2">function</span> getController<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> &nbsp; <span class="sy0">....</span><br /> &nbsp; <span class="kw2">public</span> <span class="kw2">function</span> getRequest<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> &nbsp; <span class="sy0">...</span><br /> <span class="br0">&#125;</span>
  </div>
</div>

#### Symfony 2 et l&#8217;injection de dépendances

<p style="text-align: justify;">
  Nous arrivons maintenant à la partie intéressante: L&#8217;implémentation de l&#8217;injection de dépendances dans Symfony 2.<br /> En premier lieu, je vous conseille vivement la lecture de la documentation de Fabien Potencier dédiée au composant:<br /> <a href="http://components.symfony-project.org/dependency-injection/trunk/book/00-Introduction" target="_blank">Symfony Dependency Injection</a> (il s&#8217;agit du composant php 5.2, mais la documentation est toujours d&#8217;actualité dans le sens où le fonctionnement est identique).
</p>

Jetons un oeil aux sources du composant (*/sandbox/src/vendor/symfony/src/Symfony/Components/DependencyInjection*):  
<img class="alignleft size-full wp-image-480" title="di-folder" src="/blog/wp-content/uploads/2010/06/di-folder.png" alt="di folder Symfony 2   Linjection de dépendances" width="284" height="253" /></td> 
*   Le répertoire ***Dumper** *va contenir les classes permettant de dumper un ***Builder*** sous un format spécifique (Yaml, Xml, PHP, Graphviz).
*   Le répertoire ***Loader*** var contenir les classes permettant de charger des ***BuilderConfiguration*** à partir de formats spécifiques (Yaml, Xml, Ini)
*   ***Container*** est notre classe de base, ie le container qui va contenir les différents services et les paramètres associés.
*   ***Builder*** est une classe qui étend ***Container*** et qui va permettre d&#8217;auto-générer le ***Container*** à partir de la configuration, elle va consommer des objets de la classe ***BuilderConfiguration*** afin d&#8217;enrichir le ***Container***.
*   ***BuilderConfiguration*** est la classe qui permet de configurer le ***Builder***. Les objets ***BuilderConfiguration*** vont contenir des définitions de services, des ressources nécessaires au fonctionnement des services déclarés, etc&#8230;
*   ***Definition*** est la classe qui représente la définition d&#8217;un service. C&#8217;est à partir des objets de cette classe que le builder va pouvoir construire les services.

<p style="text-align: justify;">
  Le fonctionnement est somme toute assez simple. Les différents loaders vont permettre de générer des objets du type BuilderConfiguration (qui contiendront des objets Definition) qui seront mergés au sein du Builder pour aboutir à notre Container. Les dumpers permettront de faire l&#8217;opération inverse, à savoir : générer un fichier de définitions pour les formats Yaml et Xml, un output Graphviz et enfin un objet php Container en ce qui concerne le dumper PHP.<br /> Un schéma valant mieux qu&#8217;un long discours, on peut synthétiser le fonctionnement comme suit :<br /> Note: Le loader de fichiers .ini n&#8217;a pas été représenté puisqu&#8217;il ne permet que de définir des paramètres.
</p>

[<img class="aligncenter size-full wp-image-486" title="injection-dependances" src="/blog/wp-content/uploads/2010/06/injection-dependances.png" alt="injection dependances Symfony 2   Linjection de dépendances" width="846" height="468" />][1]

<p style="text-align: justify;">
  <strong><em>Le dumper Graphviz</em></strong>: Ce dumper a pour objectif de générer un dump exploitable par le logiciel <a href="http://graphviz.org/" target="_blank">Graphviz</a> ; il va nous permettre d&#8217;obtenir un graphique représentant nos services et leurs dépendances.<br /> <strong><em>Le cache:</em></strong> Afin d&#8217;accélérer le fonctionnement des applications utilisant l&#8217;injection de dépendances, il est évident que les différents fichiers de configuration ne seront pas reparsés à chaque appel. En réalité, le container sera dumpé sous la forme d&#8217;une classe PHP et mise en cache.
</p>

<p style="text-align: justify;">
  Attardons nous un peu sur ces fameuses définitions de services qui sont un peu la pierre angulaire de l&#8217;injection de dépendances.
</p>

<div>
  <table style="width: 100%;" border="0">
    <tr>
      <th colspan="2">
        Définition d&#8217;un service (La classe Definition)
      </th>
    </tr>
    
    <tr>
      <td>
        <span style="-webkit-border-horizontal-spacing: 0px; -webkit-border-vertical-spacing: 0px; font-size: 13px; line-height: 19px;">$class</span>
      </td>
      
      <td>
        Il s&#8217;agit de la classe de base représentant le service
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

A partir de ces informations, le builder sera capable de recréer la méthode permettant d&#8217;accéder au service.  
De façon très synthétique et grossière, la définition nous permettrait d&#8217;aboutir à une méthode comme ceci:

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="kw2">public</span> <span class="kw2">function</span> getServiceXXX<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="br0">&#123;</span><br /> &nbsp; <span class="kw1">require</span> <span class="re0">$file</span><span class="sy0">;</span><br /> &nbsp; <span class="re0">$service</span> <span class="sy0">=</span> <span class="kw2">new</span> <span class="re0">$class</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; <span class="re0">$service</span><span class="sy0">-></span><span class="re0">$constructor</span><span class="br0">&#40;</span><span class="re0">$arguments</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; <span class="kw1">foreach</span> <span class="br0">&#40;</span><span class="re0">$calls</span> <span class="kw1">as</span> <span class="re0">$call</span><span class="br0">&#41;</span><br /> &nbsp; <span class="re0">$service</span><span class="sy0">-></span><span class="re0">$call</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; <span class="kw1">return</span> <span class="re0">$service</span><span class="sy0">;</span><br /> <span class="br0">&#125;</span>
  </div>
</div>

<p style="text-align: justify;">
  <strong>Note: </strong>Dans Symfony 2, les bundles de base utilisent le loader XML pour définir leurs services. Le format XML a l&#8217;avantage de pouvoir être validé via <em>Components/DependencyInjection/Loader/schema/dic/services/services-1.0.xsd</em>
</p>

#### Exemple de service: le logger du ZendBundle

<p style="text-align: justify;">
  Dans le répertoire Ressources/config/ du Bundle (<em> /sandbox/src/vendor/symfony/src/Symfony/Framework/ZendBundle</em>), on trouve le fichier : logger.xml. Il s&#8217;agit de notre fichier de configuration. Notre fichier qui sera chargé par le loader pour enrichir notre container.
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
    <span class="kw2">protected</span> <span class="kw2">function</span> getZend_LoggerService<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="br0">&#123;</span><br /> &nbsp; <span class="kw1">if</span> <span class="br0">&#40;</span><a href="http://www.php.net/isset"><span class="kw3">isset</span></a><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'zend.logger'</span><span class="br0">&#93;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><br /> &nbsp; <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'zend.logger'</span><span class="br0">&#93;</span><span class="sy0">;</span><br /> <br /> &nbsp; <span class="re0">$instance</span> <span class="sy0">=</span> <span class="kw2">new</span> Symfony\Framework\ZendBundle\Logger\Logger<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; <span class="re0">$instance</span><span class="sy0">-></span><span class="me1">addWriter</span><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">getZend_Logger_Writer_FilesystemService</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; <span class="re0">$instance</span><span class="sy0">-></span><span class="me1">addWriter</span><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">getZend_Logger_Writer_DebugService</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <br /> &nbsp; <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'zend.logger'</span><span class="br0">&#93;</span> <span class="sy0">=</span> <span class="re0">$instance</span><span class="sy0">;</span><br /> <span class="br0">&#125;</span><br /> <br /> <span class="kw2">protected</span> <span class="kw2">function</span> getZend_Logger_Writer_FilesystemService<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="br0">&#123;</span><br /> &nbsp; <span class="kw1">if</span> <span class="br0">&#40;</span><a href="http://www.php.net/isset"><span class="kw3">isset</span></a><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'zend.logger.writer.filesystem'</span><span class="br0">&#93;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><br /> &nbsp; <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'zend.logger.writer.filesystem'</span><span class="br0">&#93;</span><span class="sy0">;</span><br /> <br /> &nbsp; <span class="re0">$instance</span> <span class="sy0">=</span> <span class="kw2">new</span> Zend_Log_Writer_Stream<span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">getParameter</span><span class="br0">&#40;</span><span class="st_h">'zend.logger.path'</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; <span class="re0">$instance</span><span class="sy0">-></span><span class="me1">addFilter</span><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">getZend_Logger_FilterService</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; <span class="re0">$instance</span><span class="sy0">-></span><span class="me1">setFormatter</span><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">getZend_Formatter_FilesystemService</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <br /> &nbsp; <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'zend.logger.writer.filesystem'</span><span class="br0">&#93;</span> <span class="sy0">=</span> <span class="re0">$instance</span><span class="sy0">;</span><br /> <span class="br0">&#125;</span><br /> <br /> <span class="kw2">protected</span> <span class="kw2">function</span> getZend_Formatter_FilesystemService<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="br0">&#123;</span><br /> &nbsp; <span class="kw1">if</span> <span class="br0">&#40;</span><a href="http://www.php.net/isset"><span class="kw3">isset</span></a><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'zend.formatter.filesystem'</span><span class="br0">&#93;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><br /> &nbsp; <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'zend.formatter.filesystem'</span><span class="br0">&#93;</span><span class="sy0">;</span><br /> <br /> &nbsp; <span class="re0">$instance</span> <span class="sy0">=</span> <span class="kw2">new</span> Zend_Log_Formatter_Simple<span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">getParameter</span><span class="br0">&#40;</span><span class="st_h">'zend.formatter.filesystem.format'</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <br /> &nbsp; <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'zend.formatter.filesystem'</span><span class="br0">&#93;</span> <span class="sy0">=</span> <span class="re0">$instance</span><span class="sy0">;</span><br /> <span class="br0">&#125;</span><br /> <br /> <span class="kw2">protected</span> <span class="kw2">function</span> getZend_Logger_Writer_DebugService<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="br0">&#123;</span><br /> &nbsp; <span class="kw1">if</span> <span class="br0">&#40;</span><a href="http://www.php.net/isset"><span class="kw3">isset</span></a><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'zend.logger.writer.debug'</span><span class="br0">&#93;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><br /> &nbsp; <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'zend.logger.writer.debug'</span><span class="br0">&#93;</span><span class="sy0">;</span><br /> <br /> &nbsp; <span class="re0">$instance</span> <span class="sy0">=</span> <span class="kw2">new</span> Symfony\Framework\ZendBundle\Logger\DebugLogger<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; <span class="re0">$instance</span><span class="sy0">-></span><span class="me1">addFilter</span><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">getZend_Logger_FilterService</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <br /> &nbsp; <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'zend.logger.writer.debug'</span><span class="br0">&#93;</span> <span class="sy0">=</span> <span class="re0">$instance</span><span class="sy0">;</span><br /> <span class="br0">&#125;</span><br /> <br /> <span class="kw2">protected</span> <span class="kw2">function</span> getZend_Logger_FilterService<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="br0">&#123;</span><br /> &nbsp; <span class="kw1">if</span> <span class="br0">&#40;</span><a href="http://www.php.net/isset"><span class="kw3">isset</span></a><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'zend.logger.filter'</span><span class="br0">&#93;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><br /> &nbsp; <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'zend.logger.filter'</span><span class="br0">&#93;</span><span class="sy0">;</span><br /> <br /> &nbsp; <span class="re0">$instance</span> <span class="sy0">=</span> <span class="kw2">new</span> Zend_Log_Filter_Priority<span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">getParameter</span><span class="br0">&#40;</span><span class="st_h">'zend.logger.priority'</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <br /> &nbsp; <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'zend.logger.filter'</span><span class="br0">&#93;</span> <span class="sy0">=</span> <span class="re0">$instance</span><span class="sy0">;</span><br /> <span class="br0">&#125;</span><br /> <br /> <span class="kw2">protected</span> <span class="kw2">function</span> getLoggerService<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="br0">&#123;</span><br /> &nbsp; <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">getZend_LoggerService</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <span class="br0">&#125;</span>
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
  <span style="text-decoration: underline;"><strong>Les annotations :</strong></span><br /> Les annotations permettent de rajouter des propriétés à un service. Ces annotations ne sont pas exploitées directement par l&#8217;injecteur de dépendances mais par des composants tierces. Une annotation possède un nom et des attributs. Par exemple, dans le fichier de définitions: Framework/WebBundle/Resources/config/web.xml, on trouve la définition suivante sur le service <strong>request_parser</strong>:
</p>

<div class="codecolorer-container xml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="xml codecolorer">
    &nbsp;
  </div>
</div>

<p style="text-align: justify;">
  Cette définition rajoute donc une annotation &#8220;<strong>kernel.listener</strong>&#8221; sur le service <strong>request_parser</strong> avec comme attributs <strong>event</strong> et <strong>method</strong>.<br /> Dans l&#8217;exemple précédent, le <strong>Kernel</strong> se servira de cette annotation pour définir le service en tant qu&#8217;écouteur (listener) sur l&#8217;event &#8220;core.request&#8221; (nous y reviendrons dans le prochain article).
</p>

**<span style="text-decoration: underline;">Les extensions : </span>**

<p style="text-align: justify;">
  Lorsque les différents Bundles vont définir leurs services, ceux-ci ne seront pas directement chargés dans la configuration du Builder global. Au lieu de ça, les Bundles vont définir des extensions. Les extensions ne sont qu&#8217;un wrapper permettant de charger la configuration d&#8217;un bundle &#8220;à la demande&#8221;. Les extensions vont permettre de mieux organiser les différents services. Pour expliquer le fonctionnement des extensions, nous allons étudier le Bundle Web (Framework/WebBundle).
</p>

Le fichier Bundle.php du WebBundle ressemble à ceci:

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="kw2">class</span> Bundle <span class="kw2">extends</span> BaseBundle<br /> <span class="br0">&#123;</span><br /> &nbsp; <span class="kw2">public</span> <span class="kw2">function</span> buildContainer<span class="br0">&#40;</span>ContainerInterface <span class="re0">$container</span><span class="br0">&#41;</span><br /> &nbsp; <span class="br0">&#123;</span><br /> &nbsp; &nbsp; Loader<span class="sy0">::</span><span class="me2">registerExtension</span><span class="br0">&#40;</span><span class="kw2">new</span> WebExtension<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; &nbsp; <span class="sy0">....</span><br /> &nbsp; <span class="br0">&#125;</span><br /> <span class="br0">&#125;</span>
  </div>
</div>

<p style="text-align: justify;">
  La méthode <strong>buildContainer()</strong> définie sur chaque Bundle permet d&#8217;indiquer au <strong>Kernel</strong> comment configurer le Container pour ce Bundle. Ici, le Bundle va se contenter de déclarer une extension sur le loader. Il enregistre donc l&#8217;extension <strong>WebExtension</strong> dans le loader.<br /> Voyons maintenant le fichier WebExtension.php
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="kw2">class</span> WebExtension <span class="kw2">extends</span> LoaderExtension<br /> <span class="br0">&#123;</span><br /> &nbsp; <span class="kw2">protected</span> <span class="re0">$resources</span> <span class="sy0">=</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><br /> &nbsp; <span class="st_h">'templating'</span><span class="sy0">=></span> <span class="st_h">'templating.xml'</span><span class="sy0">,</span><br /> &nbsp; <span class="st_h">'web'</span> <span class="sy0">=></span> <span class="st_h">'web.xml'</span><span class="sy0">,</span><br /> &nbsp; <span class="st_h">'debug'</span> <span class="sy0">=></span> <span class="st_h">'debug.xml'</span><span class="sy0">,</span><br /> &nbsp; <span class="st_h">'user'</span> <span class="sy0">=></span> <span class="st_h">'user.xml'</span><span class="sy0">,</span><br /> &nbsp; <span class="br0">&#41;</span><span class="sy0">;</span><br /> <br /> &nbsp; <span class="kw2">public</span> <span class="kw2">function</span> webLoad<span class="br0">&#40;</span><span class="re0">$config</span><span class="br0">&#41;</span><br /> &nbsp; <span class="br0">&#123;</span><br /> &nbsp; &nbsp; <span class="re0">$configuration</span> <span class="sy0">=</span> <span class="kw2">new</span> BuilderConfiguration<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <br /> &nbsp; &nbsp; <span class="re0">$loader</span> <span class="sy0">=</span> <span class="kw2">new</span> XmlFileLoader<span class="br0">&#40;</span>__DIR__<span class="sy0">.</span><span class="st_h">'/../Resources/config'</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; &nbsp; <span class="re0">$configuration</span><span class="sy0">-></span><span class="me1">merge</span><span class="br0">&#40;</span><span class="re0">$loader</span><span class="sy0">-></span><span class="me1">load</span><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">resources</span><span class="br0">&#91;</span><span class="st_h">'web'</span><span class="br0">&#93;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <br /> &nbsp; &nbsp; <span class="kw1">return</span> <span class="re0">$configuration</span><span class="sy0">;</span><br /> &nbsp; <span class="br0">&#125;</span><br /> <br /> &nbsp; <span class="kw2">public</span> <span class="kw2">function</span> userLoad<span class="br0">&#40;</span><span class="re0">$config</span><span class="br0">&#41;</span><br /> &nbsp; <span class="br0">&#123;</span><span class="sy0">....</span><span class="br0">&#125;</span><br /> <br /> &nbsp; <span class="kw2">public</span> <span class="kw2">function</span> templatingLoad<span class="br0">&#40;</span><span class="re0">$config</span><span class="br0">&#41;</span><br /> &nbsp; <span class="br0">&#123;</span><span class="sy0">....</span><span class="br0">&#125;</span><br /> <br /> &nbsp; <span class="kw2">public</span> <span class="kw2">function</span> debugLoad<span class="br0">&#40;</span><span class="re0">$config</span><span class="br0">&#41;</span><br /> &nbsp; <span class="br0">&#123;</span><span class="sy0">....</span><span class="br0">&#125;</span><br /> &nbsp; <span class="sy0">...</span><br /> &nbsp; <span class="kw2">public</span> <span class="kw2">function</span> getAlias<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> &nbsp; <span class="br0">&#123;</span><br /> &nbsp; &nbsp; <span class="kw1">return</span> <span class="st_h">'web'</span><span class="sy0">;</span><br /> &nbsp; <span class="br0">&#125;</span>
  </div>
</div>

<p style="text-align: justify;">
  On voit que les extensions permettent de charger des configurations de Builder un peu différemment. Dans les différents fichiers de configuration manipulés, quel que soit le format du loader utilisé, on se contente de déclarer des services. Cependant, il faut savoir qu&#8217;il est également possible de charger et configurer des extensions ainsi définies.<br /> Ce qu&#8217;on trouve en général dans les fichiers config.yml / config_dev.yml / config_prod.yml d&#8217;une application utilise principalement la déclaration des extensions prédéfinies par les Bundles et ne déclare pas de nouveaux services.<br /> Exemple:
</p>

<div class="codecolorer-container yaml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="yaml codecolorer">
    <span class="co3">kernel.config</span><span class="sy2">: </span>~<span class="co3"><br /> web.web</span><span class="sy2">: </span>~<span class="co3"><br /> web.templating</span><span class="sy2">: </span>~<span class="co4"><br /> web.debug</span>:<span class="co3"><br /> exception</span><span class="sy2">: </span><span class="co2">%kernel.debug%</span><span class="co3"><br /> toolbar</span><span class="sy2">: </span><span class="co2">%kernel.debug%</span><span class="co4"><br /> zend.logger</span>:<span class="co3"><br /> priority</span><span class="sy2">: </span>info<span class="co3"><br /> path</span><span class="sy2">: </span><span class="co2">%kernel.root_dir%/logs/%kernel.environment%.log</span><span class="co4"><br /> swift.mailer</span>:<span class="co3"><br /> transport</span><span class="sy2">: </span>gmail
  </div>
</div>

<p style="text-align: justify;">
  Dans ce fichier de configuration, seules des extensions sont déclarées. A noter que si une extension n&#8217;est pas déclarée dans le fichier de configuration d&#8217;un Container, ses services ne seront pas inclus dans le Container final.<br /> Le but des extensions est de fournir une méthode plus &#8220;propre&#8221; pour déclarer ses services, mais le fonctionnement reste identique, les Bundles vont déclarer des services comme vu précédemment, les extensions ne fournissant qu&#8217;un moyen de charger ou configurer ces déclarations.
</p>

#### Mise en pratique

<p style="text-align: justify;">
  Afin de bien comprendre le fonctionnement, je vous propose une petite mise en pratique au travers de quelques exemples. Les exemples seront basés sur la sandbox proposée sur le site de <a href="http://www.symfony-reloaded.org" target="_blank">Symfony 2</a> et l&#8217;application Hello World !
</p>

<p style="text-align: justify;">
  Au niveau de la sandbox, les fichiers de configuration du Builder sont : config.yml, config_dev.yml et config_prod.yml. La méthode qui va charger ces fichiers est la méthode registerContainerConfiguration() de la classe HelloKernel. Nous allons travailler dans le cadre de nos exemples sur l&#8217;environnement de production. Le Container final généré se trouve dans le cache hello/cache/prod/helloProjectContainer.php
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="kw2">class</span> HelloKernel <span class="kw2">extends</span> Kernel<br /> <span class="br0">&#123;</span><br /> &nbsp; <span class="sy0">...</span><br /> &nbsp; <span class="kw2">public</span> <span class="kw2">function</span> registerContainerConfiguration<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> &nbsp; <span class="br0">&#123;</span><br /> &nbsp; &nbsp; <span class="re0">$loader</span> <span class="sy0">=</span> <span class="kw2">new</span> ContainerLoader<span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">getBundleDirs</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; &nbsp; <span class="re0">$configuration</span> <span class="sy0">=</span> <span class="re0">$loader</span><span class="sy0">-></span><span class="me1">load</span><span class="br0">&#40;</span>__DIR__<span class="sy0">.</span><span class="st_h">'/config/config_'</span><span class="sy0">.</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">getEnvironment</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">.</span><span class="st_h">'.yml'</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; &nbsp; <span class="kw1">return</span> <span class="re0">$configuration</span><span class="sy0">;</span><br /> &nbsp; <span class="br0">&#125;</span>
  </div>
</div>

<p style="text-align: justify;">
  C&#8217;est au niveau de cette méthode que nous allons intervenir dans un premier temps.
</p>

<p style="text-align: justify;">
  <span style="text-decoration: underline;"><strong>Modifier la classe d&#8217;un service:</strong></span><br /> Dans notre premier exemple, nous allons tenter de modifier un service. En l&#8217;occurrence, le service représentant la requête, le bien nommé &#8220;RequestService&#8221;. Notre container (HelloProjectContainer.php) définit le service ainsi:
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="kw2">protected</span> <span class="kw2">function</span> getRequestService<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="br0">&#123;</span><br /> &nbsp; <span class="kw1">if</span> <span class="br0">&#40;</span><a href="http://www.php.net/isset"><span class="kw3">isset</span></a><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'request'</span><span class="br0">&#93;</span><span class="br0">&#41;</span><span class="br0">&#41;</span> <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'request'</span><span class="br0">&#93;</span><span class="sy0">;</span><br /> &nbsp; <span class="re0">$instance</span> <span class="sy0">=</span> <span class="kw2">new</span> Symfony\Components\RequestHandler\Request<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'request'</span><span class="br0">&#93;</span> <span class="sy0">=</span> <span class="re0">$instance</span><span class="sy0">;</span><br /> <span class="br0">&#125;</span>
  </div>
</div>

<p style="text-align: justify;">
  Pour modifier la classe utilisée pour ce service, rien de plus simple, il nous suffit de modifier la configuration du container.<br /> Nous allons simplement modifier la classe associée dans la définition de ce service de cette façon :
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="kw2">public</span> <span class="kw2">function</span> registerContainerConfiguration<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="br0">&#123;</span><br /> &nbsp; <span class="re0">$loader</span> <span class="sy0">=</span> <span class="kw2">new</span> ContainerLoader<span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">getBundleDirs</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; <span class="re0">$configuration</span> <span class="sy0">=</span> <span class="re0">$loader</span><span class="sy0">-></span><span class="me1">load</span><span class="br0">&#40;</span>__DIR__<span class="sy0">.</span><span class="st_h">'/config/config_'</span><span class="sy0">.</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">getEnvironment</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">.</span><span class="st_h">'.yml'</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; <span class="re0">$configuration</span><span class="sy0">-></span><span class="me1">getDefinition</span><span class="br0">&#40;</span><span class="st_h">'request'</span><span class="br0">&#41;</span><span class="sy0">-></span><span class="me1">setClass</span><span class="br0">&#40;</span><span class="st_h">'MaClasseRequest'</span><span class="br0">&#41;</span><span class="sy0">;</span> <span class="co1">// Nous modifions la définition du service "request"</span><br /> &nbsp; <span class="kw1">return</span> <span class="re0">$configuration</span><span class="sy0">;</span><br /> <span class="br0">&#125;</span>
  </div>
</div>

<p style="text-align: justify;">
  C&#8217;est fait ! Si on vide le cache et que l&#8217;on rappelle une url sur le projet, la définition de notre service dans le container ressemble maintenant à ça:
</p>

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="kw2">protected</span> <span class="kw2">function</span> getRequestService<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="br0">&#123;</span><br /> &nbsp; &nbsp; <span class="kw1">if</span> <span class="br0">&#40;</span><a href="http://www.php.net/isset"><span class="kw3">isset</span></a><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'request'</span><span class="br0">&#93;</span><span class="br0">&#41;</span><span class="br0">&#41;</span> <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'request'</span><span class="br0">&#93;</span><span class="sy0">;</span><br /> &nbsp; &nbsp; <span class="re0">$instance</span> <span class="sy0">=</span> <span class="kw2">new</span> MaClasseRequest<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; &nbsp; <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'request'</span><span class="br0">&#93;</span> <span class="sy0">=</span> <span class="re0">$instance</span><span class="sy0">;</span><br /> <span class="br0">&#125;</span>
  </div>
</div>

<p style="text-align: justify;">
  <span style="text-decoration: underline;"><strong>Créer un service:</strong></span><br /> Dans cette exemple, nous allons voir comment déclarer un nouveau service. Nous allons tenter de déclarer le service &#8220;Monservice&#8221;, utilisant la classe &#8220;Maclasse&#8221;, dépendante du service &#8220;logger&#8221; sur laquelle on appellera les méthodes init1() et init2() à qui on passera le paramètre &#8220;coucou&#8221;. Nous avions vu que le fichier config.yml déclarait les extensions à utiliser, mais c&#8217;est un fichier de configuration de Container comme un autre, nous pouvons donc y définir des services.
</p>

<div class="codecolorer-container yaml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="yaml codecolorer">
    <span class="co4">services</span>:<span class="co4"><br /> &nbsp; monservice</span>:<span class="co3"><br /> &nbsp; &nbsp; id</span><span class="sy2">: </span>monservice<span class="co3"><br /> &nbsp; &nbsp; class</span><span class="sy2">: </span>Maclasse<span class="co4"><br /> &nbsp; &nbsp; arguments</span><span class="sy2">:<br /> </span> &nbsp; &nbsp; &nbsp;- @logger<span class="co4"><br /> &nbsp; &nbsp; calls</span><span class="sy2">:<br /> </span> &nbsp; &nbsp; &nbsp;- <span class="br0">&#91;</span>init1, <span class="br0">&#91;</span><span class="br0">&#93;</span><span class="br0">&#93;</span><br /> &nbsp; &nbsp; &nbsp; - <span class="br0">&#91;</span>init2, <span class="br0">&#91;</span>'coucou'<span class="br0">&#93;</span><span class="br0">&#93;</span>
  </div>
</div>

C&#8217;est tout. Nous venons de déclarer un nouveau service dans notre application. Si nous jetons un oeil au Container généré:

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="kw2">protected</span> <span class="kw2">function</span> getMonserviceService<span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="br0">&#123;</span><br /> &nbsp; &nbsp; <span class="kw1">if</span> <span class="br0">&#40;</span><a href="http://www.php.net/isset"><span class="kw3">isset</span></a><span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'monservice'</span><span class="br0">&#93;</span><span class="br0">&#41;</span><span class="br0">&#41;</span> <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'monservice'</span><span class="br0">&#93;</span><span class="sy0">;</span><br /> <br /> &nbsp; &nbsp; <span class="re0">$instance</span> <span class="sy0">=</span> <span class="kw2">new</span> Maclasse<span class="br0">&#40;</span><span class="re0">$this</span><span class="sy0">-></span><span class="me1">getService</span><span class="br0">&#40;</span><span class="st_h">'logger'</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; &nbsp; <span class="re0">$instance</span><span class="sy0">-></span><span class="me1">init1</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> &nbsp; &nbsp; <span class="re0">$instance</span><span class="sy0">-></span><span class="me1">init2</span><span class="br0">&#40;</span><span class="st_h">'coucou'</span><span class="br0">&#41;</span><span class="sy0">;</span><br /> <br /> &nbsp; &nbsp; <span class="kw1">return</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">shared</span><span class="br0">&#91;</span><span class="st_h">'monservice'</span><span class="br0">&#93;</span> <span class="sy0">=</span> <span class="re0">$instance</span><span class="sy0">;</span><br /> <span class="br0">&#125;</span>
  </div>
</div>

#### En conclusion

<p style="text-align: justify;">
  Comme vous avez pu le découvrir dans cet article, l&#8217;injecteur de dépendances de Symfony 2 est vraiment puissant. Nous pouvons customizer, tweaker, étendre le fonctionnement du coeur même du Framework par ce biais.
</p>

<p style="text-align: justify;">
  L&#8217;injection de dépendances ne devrait plus avoir de secrets pour vous !
</p>

<p style="text-align: justify;">
  PS: Merci à Xavier R pour sa relecture et ses remarques avisées.
</p>

 [1]: /blog/wp-content/uploads/2010/06/injection-dependances.png