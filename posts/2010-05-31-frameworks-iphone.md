
Aujourd&#8217;hui, les frameworks sont devenus incontournables. Il en existe dans chaque langage de programmation (même dans le CSS). Il en va de même pour l&#8217;iPhone.

Même si le SDK de l&#8217;iPhone fournit déjà des frameworks officiels pour faciliter la gestion du son, la géolocalisation et bien d&#8217;autres choses. Il existe aussi des frameworks indépendants qui fournissent des composants graphiques et divers utilitaires. C&#8217;est le cas de celui qu&#8217;on utilise : **[Three20][1]**.

> Three20 is a collection of iPhone UI classes, like a photo viewer, and general utilities, like an HTTP disk cache. Three20 is derived from the Facebook iPhone app, which is one of the most downloaded iPhone apps ever.

Nous aimons particulièrement sa fonctionnalité de **navigation par URL**, le principe est similaire à symfony et son routing. Nous définissons des routes et lorsqu&#8217;on les appelle, le framework instancie notre classe, la rajoute dans la navigation puis l&#8217;affiche. L&#8217;avantage est qu&#8217;il s&#8217;occupe automatiquement de la navigation et la pile d&#8217;écrans, choses assez redondantes. Il fournit aussi un mécanisme pour mettre en cache le dernier écran vu sur l&#8217;application pour pouvoir l&#8217;afficher à nouveau lors de la réouverture de l&#8217;application, **la navigation devient donc persistante** automatiquement (oui il faut écrire la ligne de code pour activer la persistance encore :D).

Prenons un exemple, imaginons que nous avons une liste de références et une page de détail. Sans framework, il faut définir de manière explicite tout le processus pour l&#8217;afficher. Dans l&#8217;ordre, on crée donc notre contrôleur, on le &#8220;push&#8221; à notre navigationController&#8230;etc. Ce n&#8217;est pas spécialement long mais c&#8217;est très redondant. En revanche, avec une navigation par URL, on ne fait plus ce genre de choses, et on découple (un peu) les classes entre elles.

Voyons plus en détail comme ça fonctionne :

Comme dans symfony, il faut au préalable déclarer nos routes ; ici, c&#8217;est dans notre fichier XXAppDelegate.m qu&#8217;on le fait :

<div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    <span class="sy0">-</span> <span class="br0">&#40;</span><span class="kw4">void</span><span class="br0">&#41;</span>applicationDidFinishLaunching<span class="sy0">:</span><span class="br0">&#40;</span>UIApplication<span class="sy0">*</span><span class="br0">&#41;</span>application <span class="br0">&#123;</span><br /> &nbsp; &nbsp; TTNavigator<span class="sy0">*</span> navigator <span class="sy0">=</span> <span class="br0">&#91;</span>TTNavigator navigator<span class="br0">&#93;</span>;<br /> &nbsp; &nbsp; navigator.persistenceMode <span class="sy0">=</span> TTNavigatorPersistenceModeTop;<br /> &nbsp; &nbsp; navigator.window <span class="sy0">=</span> <span class="br0">&#91;</span><span class="br0">&#91;</span><span class="br0">&#91;</span>UIWindow alloc<span class="br0">&#93;</span> initWithFrame<span class="sy0">:</span>TTScreenBounds<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#93;</span> autorelease<span class="br0">&#93;</span>;<br /> &nbsp; &nbsp; TTURLMap<span class="sy0">*</span> map <span class="sy0">=</span> navigator.URLMap;<br /> &nbsp; &nbsp; <span class="br0">&#91;</span>map from<span class="sy0">:</span><span class="co3">@</span><span class="st0">"tt://pageReference"</span> toSharedViewController<span class="sy0">:</span><span class="br0">&#91;</span>ReferenceController class<span class="br0">&#93;</span><span class="br0">&#93;</span>;<br /> <span class="br0">&#125;</span>
  </div>
</div>

Noter que nous venons de remplacer l&#8217;écran principal de l&#8217;application par le navigateur de Three20 donc si on compile à ce moment précis, nous obtenons une page noire puisque nous n&#8217;avons lancé aucune URL comme première page.

Pour lancer une URL, rien de plus simple. Il faut simplement appeler la méthode &#8220;openURLAction sur notre objet navigateur.

<div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    <span class="kw1">if</span> <span class="br0">&#40;</span><span class="sy0">!</span><span class="br0">&#91;</span>navigator restoreViewControllers<span class="br0">&#93;</span><span class="br0">&#41;</span> <span class="br0">&#123;</span><br /> &nbsp; &nbsp; <span class="br0">&#91;</span>navigator openURLAction<span class="sy0">:</span><span class="br0">&#91;</span>TTURLAction actionWithURLPath<span class="sy0">:</span><span class="co3">@</span><span class="st0">"tt://pageReference"</span><span class="br0">&#93;</span><span class="br0">&#93;</span>;<br /> <span class="br0">&#125;</span>
  </div>
</div>

Cet objet étant un singleton, nous pouvons faire la même chose partout à travers l&#8217;application comme ceci :

<div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    <span class="br0">&#91;</span><span class="br0">&#91;</span>TTNavigator &nbsp;navigator<span class="br0">&#93;</span> openURLAction<span class="sy0">:</span><span class="br0">&#91;</span><span class="br0">&#91;</span>TTURLAction actionWithURLPath<span class="sy0">:</span><span class="co3">@</span><span class="st0">"tt://pageReference"</span><span class="br0">&#93;</span> applyAnimated<span class="sy0">:</span><span class="kw2">YES</span><span class="br0">&#93;</span><span class="br0">&#93;</span>;
  </div>
</div>

On peut évidemment passer des paramètres à ces routes afin de définir le constructeur (appelé par défaut &#8220;init&#8221;).Le plus fort, c&#8217;est que tout nos objets peuvent même être transformer/mapper en route très facilement ! Si on veut avoir une route unique pour accéder à la page de détail, nous définissons une route (1), on mappe l&#8217;objet sur la route (2) et lorsque l&#8217;on veut utiliser cette route ça devient encore plus simple (3).

<div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    <span class="co2">// 1 : route principale</span><br /> <span class="br0">&#91;</span>map from<span class="sy0">:</span><span class="co3">@</span><span class="st0">"tt://reference-detail/(initWithObject:)"</span> toViewController<span class="sy0">:</span><span class="br0">&#91;</span>ReferenceDetailController class<span class="br0">&#93;</span><span class="br0">&#93;</span>;<br /> <br /> <span class="co2">// 2 : mappage de l'objet sur la route, ici (uid) est une propriété de notre objet.</span><br /> <span class="br0">&#91;</span>map from<span class="sy0">:</span><span class="br0">&#91;</span>WebObject class<span class="br0">&#93;</span> name<span class="sy0">:</span><span class="co3">@</span><span class="st0">"reference"</span> toURL<span class="sy0">:</span><span class="co3">@</span><span class="st0">"tt://reference-detail/(uid))"</span><span class="br0">&#93;</span>;<br /> <br /> <span class="co2">// 3 : utilisation</span><br /> WebObject <span class="sy0">*</span>t <span class="sy0">=</span> <span class="br0">&#91;</span><span class="br0">&#91;</span>WebObject alloc<span class="br0">&#93;</span> initWithUid<span class="sy0">:</span><span class="nu0">123</span><span class="br0">&#93;</span>;<br /> <a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/"><span class="kw5">NSString</span></a> <span class="sy0">*</span> url <span class="sy0">=</span> <span class="br0">&#91;</span>t URLValueWithName<span class="sy0">:</span><span class="co3">@</span><span class="st0">"detail"</span><span class="br0">&#93;</span>;<br /> <br /> NSLog<span class="br0">&#40;</span>t<span class="br0">&#41;</span>; <span class="co2">// affiche : tt://reference-detail/123</span><br /> <span class="br0">&#91;</span><span class="br0">&#91;</span>TTNavigator &nbsp;navigator<span class="br0">&#93;</span> openURLAction<span class="sy0">:</span><span class="br0">&#91;</span><span class="br0">&#91;</span>TTURLAction actionWithURLPath<span class="sy0">:</span>url<span class="br0">&#93;</span> applyAnimated<span class="sy0">:</span><span class="kw2">YES</span><span class="br0">&#93;</span><span class="br0">&#93;</span>;
  </div>
</div>

Vous notez l&#8217;existence de la méthode URLValueWithName qui retourne la bonne url. En fait, nous n&#8217;avons pas eu à la créer vu qu&#8217;elle est ajouté automatiquement par Three20 sur tous nos objets grâce au merveilleux système de &#8220;catégorie&#8221;. Les catégories en Objective C permettent d&#8217;ajouter dynamiquement des méthodes à n&#8217;importe quelle classe sans devoir l&#8217;étendre comme dans la plupart des langages ! Ici Three20 a du rajouter une catégorie sur NSObject.

Nous venons de faire un rapide tour sur la navigation par URL. Pour en savoir plus, la documentation officielle est disponible ici : <http://three20.info/ui/navigation>

 [1]: http://three20.info/