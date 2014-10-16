
Dans[ l'article précédent][1], nous avons vu comment utiliser le système de délégation permettant la communication sur un niveau.

Quand on veut signaler à l'ensemble de l'application une action qui peut potentiellement engendrer diverses opérations (mettre à jour une vue, modifier la base de données locale, provoquer une requête HTTP, etc...), le système par délégation ne suffit pas.

La fausse bonne pratique est de ramener tout ces traitements à la classe mère "AppDelegate", et comme celle-ci connait pas mal de monde, elle déclenchera des appels sur les objets adéquates.

<pre id="line1"><div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    AppDelegate <span class="sy0">*</span>appDelegate <span class="sy0">=</span>  <span class="br0">&#40;</span>AppDelegate <span class="sy0">*</span><span class="br0">&#41;</span><span class="br0">&#91;</span><span class="br0">&#91;</span>UIApplication sharedApplication<span class="br0">&#93;</span> delegate<span class="br0">&#93;</span>;<br />
    <span class="br0">&#91;</span>appDelegate coucouToi<span class="br0">&#93;</span>;
  </div>
</div>

</pre>

La conséquence est d'avoir une classe fourre-tout, un point d'entrée pour tout et surtout n'importe quoi.

# Le système de notification

Le principe est simple et pour bien le comprendre il faut le voir avec l'image du haut-parleur de certains villages en période estivale "Mesdames, Monsieur, aujourd'hui sur la place du marché à partir de 14h, vente de moules fraiches"

Ceux qui sont attentifs recevront le message, les autres non. Et s’il n’y a personne qui écoute, tant pis.

Concrètement, ça peut servir pour prévenir à toute l'application qu'un évènement "important" s'est déroulé comme une requête http qui finit, une erreur à remonter à l'utilisateur, ou autres.

=> L'idée derrière est que le système de notification permet de faire de l'évènementiel comme en Flash ou autre. On peut envoyer des messages &#8211; event, certains objets sont "écouteurs", ils peuvent aussi décider de ne plus écouter certains types de messages.

# Let's code

Pour poster un message

<pre id="line1"><div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    <span class="br0">&#91;</span><span class="br0">&#91;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSNotificationCenter_Class/"><span class="kw5">NSNotificationCenter</span></a> defaultCenter<span class="br0">&#93;</span> postNotificationName<span class="sy0">:</span><span class="co3">@</span><span class="st0">"LE_NOM_DE_MON_EVENT"</span> object<span class="sy0">:</span>UnObjetOuNil<span class="br0">&#93;</span>;
  </div>
</div>

</pre>

Pour se déclarer "écouteur"

<pre id="line1"><div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    <span class="br0">&#91;</span><span class="br0">&#91;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSNotificationCenter_Class/"><span class="kw5">NSNotificationCenter</span></a> defaultCenter<span class="br0">&#93;</span> addObserver<span class="sy0">:</span>self selector<span class="sy0">:</span><span class="kw1">@selector</span><span class="br0">&#40;</span>onEventXX<span class="sy0">:</span><span class="br0">&#41;</span> name<span class="sy0">:</span><span class="co3">@</span><span class="st0">"LE_NOM_DE_MON_EVENT"</span> object<span class="sy0">:</span><span class="kw2">nil</span><span class="br0">&#93;</span>;
  </div>
</div>

</pre>

Du coup, on doit implémenter la méthode "onEventXX"

<pre id="line1"><div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    <span class="sy0">-</span> <span class="br0">&#40;</span><span class="kw4">void</span><span class="br0">&#41;</span> onEventXX<span class="sy0">:</span><span class="br0">&#40;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSNotification_Class/"><span class="kw5">NSNotification</span></a> <span class="sy0">*</span><span class="br0">&#41;</span>notification<br />
    <span class="br0">&#123;</span><br />
        <span class="kw4">id</span> unObjetOuNil <span class="sy0">=</span> notification.object;  <br />
    <span class="br0">&#125;</span>
  </div>
</div>

</pre>

Pour s'enlever de la file des écouteurs, c'est simple aussi:

<pre id="line1"><div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    <span class="br0">&#91;</span><span class="br0">&#91;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSNotificationCenter_Class/"><span class="kw5">NSNotificationCenter</span></a> defaultCenter<span class="br0">&#93;</span> removeObserver<span class="sy0">:</span>self forKeyPath<span class="sy0">:</span><span class="co3">@</span><span class="st0">"LE_NOM_DE_MON_EVENT"</span><span class="br0">&#93;</span>;
  </div>
</div>

</pre>

# Important à savoir

Les messages sont envoyés dans une file d'attente où l'ordre d'émission / réception des messages n'est pas garanti. On peut émettre une priorité sur l'envoi, mais à la réception on peut recevoir les messages dans le désordre.

L'utilisation de ce système est assez dur à debuger, comme tout le monde peut s’enregistrer comme "écouteurs", et chacun peut émettre à la réception d'un message, ça peut être dur à s'y retrouver quand on n'est pas habitué à l'évènementiel.

Et aussi, utilisez [des constantes ][2]pour le nom des notifications, ici on a copié/collé les strings, mais ce n’est pas évident pour s'y retrouver.

 [1]: http://www.elao.com/blog/iphone/iphone-delegation.html "iPhone – Delegation"
 [2]: http://stackoverflow.com/questions/538996/constants-in-objective-c