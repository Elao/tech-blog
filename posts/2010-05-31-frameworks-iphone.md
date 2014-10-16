
Aujourd'hui, les frameworks sont devenus incontournables. Il en existe dans chaque langage de programmation (même dans le CSS). Il en va de même pour l'iPhone.

Même si le SDK de l'iPhone fournit déjà des frameworks officiels pour faciliter la gestion du son, la géolocalisation et bien d'autres choses. Il existe aussi des frameworks indépendants qui fournissent des composants graphiques et divers utilitaires. C'est le cas de celui qu'on utilise : **[Three20][1]**.

> Three20 is a collection of iPhone UI classes, like a photo viewer, and general utilities, like an HTTP disk cache. Three20 is derived from the Facebook iPhone app, which is one of the most downloaded iPhone apps ever.

Nous aimons particulièrement sa fonctionnalité de **navigation par URL**, le principe est similaire à symfony et son routing. Nous définissons des routes et lorsqu'on les appelle, le framework instancie notre classe, la rajoute dans la navigation puis l'affiche. L'avantage est qu'il s'occupe automatiquement de la navigation et la pile d'écrans, choses assez redondantes. Il fournit aussi un mécanisme pour mettre en cache le dernier écran vu sur l'application pour pouvoir l'afficher à nouveau lors de la réouverture de l'application, **la navigation devient donc persistante** automatiquement (oui il faut écrire la ligne de code pour activer la persistance encore :D).

Prenons un exemple, imaginons que nous avons une liste de références et une page de détail. Sans framework, il faut définir de manière explicite tout le processus pour l'afficher. Dans l'ordre, on crée donc notre contrôleur, on le "push" à notre navigationController...etc. Ce n'est pas spécialement long mais c'est très redondant. En revanche, avec une navigation par URL, on ne fait plus ce genre de choses, et on découple (un peu) les classes entre elles.

Voyons plus en détail comme ça fonctionne :

Comme dans symfony, il faut au préalable déclarer nos routes ; ici, c'est dans notre fichier XXAppDelegate.m qu'on le fait :

<div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    <span class="sy0">-</span> <span class="br0">&#40;</span><span class="kw4">void</span><span class="br0">&#41;</span>applicationDidFinishLaunching<span class="sy0">:</span><span class="br0">&#40;</span>UIApplication<span class="sy0">*</span><span class="br0">&#41;</span>application <span class="br0">&#123;</span><br />     TTNavigator<span class="sy0">*</span> navigator <span class="sy0">=</span> <span class="br0">&#91;</span>TTNavigator navigator<span class="br0">&#93;</span>;<br />     navigator.persistenceMode <span class="sy0">=</span> TTNavigatorPersistenceModeTop;<br />     navigator.window <span class="sy0">=</span> <span class="br0">&#91;</span><span class="br0">&#91;</span><span class="br0">&#91;</span>UIWindow alloc<span class="br0">&#93;</span> initWithFrame<span class="sy0">:</span>TTScreenBounds<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#93;</span> autorelease<span class="br0">&#93;</span>;<br />     TTURLMap<span class="sy0">*</span> map <span class="sy0">=</span> navigator.URLMap;<br />     <span class="br0">&#91;</span>map from<span class="sy0">:</span><span class="co3">@</span><span class="st0">"tt://pageReference"</span> toSharedViewController<span class="sy0">:</span><span class="br0">&#91;</span>ReferenceController class<span class="br0">&#93;</span><span class="br0">&#93;</span>;<br /> <span class="br0">&#125;</span>
  </div>
</div>

Noter que nous venons de remplacer l'écran principal de l'application par le navigateur de Three20 donc si on compile à ce moment précis, nous obtenons une page noire puisque nous n'avons lancé aucune URL comme première page.

Pour lancer une URL, rien de plus simple. Il faut simplement appeler la méthode "openURLAction sur notre objet navigateur.

<div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    <span class="kw1">if</span> <span class="br0">&#40;</span><span class="sy0">!</span><span class="br0">&#91;</span>navigator restoreViewControllers<span class="br0">&#93;</span><span class="br0">&#41;</span> <span class="br0">&#123;</span><br />     <span class="br0">&#91;</span>navigator openURLAction<span class="sy0">:</span><span class="br0">&#91;</span>TTURLAction actionWithURLPath<span class="sy0">:</span><span class="co3">@</span><span class="st0">"tt://pageReference"</span><span class="br0">&#93;</span><span class="br0">&#93;</span>;<br /> <span class="br0">&#125;</span>
  </div>
</div>

Cet objet étant un singleton, nous pouvons faire la même chose partout à travers l'application comme ceci :

<div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    <span class="br0">&#91;</span><span class="br0">&#91;</span>TTNavigator  navigator<span class="br0">&#93;</span> openURLAction<span class="sy0">:</span><span class="br0">&#91;</span><span class="br0">&#91;</span>TTURLAction actionWithURLPath<span class="sy0">:</span><span class="co3">@</span><span class="st0">"tt://pageReference"</span><span class="br0">&#93;</span> applyAnimated<span class="sy0">:</span><span class="kw2">YES</span><span class="br0">&#93;</span><span class="br0">&#93;</span>;
  </div>
</div>

On peut évidemment passer des paramètres à ces routes afin de définir le constructeur (appelé par défaut "init").Le plus fort, c'est que tout nos objets peuvent même être transformer/mapper en route très facilement ! Si on veut avoir une route unique pour accéder à la page de détail, nous définissons une route (1), on mappe l'objet sur la route (2) et lorsque l'on veut utiliser cette route ça devient encore plus simple (3).

<div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    <span class="co2">// 1 : route principale</span><br /> <span class="br0">&#91;</span>map from<span class="sy0">:</span><span class="co3">@</span><span class="st0">"tt://reference-detail/(initWithObject:)"</span> toViewController<span class="sy0">:</span><span class="br0">&#91;</span>ReferenceDetailController class<span class="br0">&#93;</span><span class="br0">&#93;</span>;<br /> <br /> <span class="co2">// 2 : mappage de l'objet sur la route, ici (uid) est une propriété de notre objet.</span><br /> <span class="br0">&#91;</span>map from<span class="sy0">:</span><span class="br0">&#91;</span>WebObject class<span class="br0">&#93;</span> name<span class="sy0">:</span><span class="co3">@</span><span class="st0">"reference"</span> toURL<span class="sy0">:</span><span class="co3">@</span><span class="st0">"tt://reference-detail/(uid))"</span><span class="br0">&#93;</span>;<br /> <br /> <span class="co2">// 3 : utilisation</span><br /> WebObject <span class="sy0">*</span>t <span class="sy0">=</span> <span class="br0">&#91;</span><span class="br0">&#91;</span>WebObject alloc<span class="br0">&#93;</span> initWithUid<span class="sy0">:</span><span class="nu0">123</span><span class="br0">&#93;</span>;<br /> <a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/"><span class="kw5">NSString</span></a> <span class="sy0">*</span> url <span class="sy0">=</span> <span class="br0">&#91;</span>t URLValueWithName<span class="sy0">:</span><span class="co3">@</span><span class="st0">"detail"</span><span class="br0">&#93;</span>;<br /> <br /> NSLog<span class="br0">&#40;</span>t<span class="br0">&#41;</span>; <span class="co2">// affiche : tt://reference-detail/123</span><br /> <span class="br0">&#91;</span><span class="br0">&#91;</span>TTNavigator  navigator<span class="br0">&#93;</span> openURLAction<span class="sy0">:</span><span class="br0">&#91;</span><span class="br0">&#91;</span>TTURLAction actionWithURLPath<span class="sy0">:</span>url<span class="br0">&#93;</span> applyAnimated<span class="sy0">:</span><span class="kw2">YES</span><span class="br0">&#93;</span><span class="br0">&#93;</span>;
  </div>
</div>

Vous notez l'existence de la méthode URLValueWithName qui retourne la bonne url. En fait, nous n'avons pas eu à la créer vu qu'elle est ajouté automatiquement par Three20 sur tous nos objets grâce au merveilleux système de "catégorie". Les catégories en Objective C permettent d'ajouter dynamiquement des méthodes à n'importe quelle classe sans devoir l'étendre comme dans la plupart des langages ! Ici Three20 a du rajouter une catégorie sur NSObject.

Nous venons de faire un rapide tour sur la navigation par URL. Pour en savoir plus, la documentation officielle est disponible ici : <http://three20.info/ui/navigation>

 [1]: http://three20.info/