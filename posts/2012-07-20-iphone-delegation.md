
En tant que développeur iOS débutant, on utilise sans trop faire attention le système dit de délégation. C&#8217;est un système central, que l&#8217;on est obligé d&#8217;avoir utilisé au moins une fois, par exemple à travers la mise en place d&#8217;une liste d&#8217;éléments comme UITableViewController. Cet article a pour but d&#8217;expliquer comment ça marche, en vue de créer nos propres &#8220;Delegate&#8221;.

<!--more-->

# Utilisation classique sur une UITableView

Quand on débute, on ne jure souvent que par Interface Builder et les tutoriels trouvés à gauche à droite pour afficher une liste de résultats. Sommairement, l&#8217;idée est de placer une UITableView sur notre vue courante, puis de l&#8217;associer via les outlets &#8220;dataSource&#8221; et &#8220;delegate&#8221; à notre contrôleur (file owner).

<a href="http://www.elao.com/blog/iphone/iphone-delegation.html/attachment/capture-de%cc%81cran-2012-07-13-a%cc%80-14-17-28" rel="attachment wp-att-2165"><img class="alignnone size-medium wp-image-2165" title="Votre tableView" src="http://www.elao.com/blog/wp-content/uploads/2012/07/Capture-d’écran-2012-07-13-à-14.17.28-300x158.png" alt="Capture d’écran 2012 07 13 à 14.17.28 300x158 iPhone   Delegation" width="300" height="158" /></a>

Après ça, on dit à notre contrôleur &#8220;Voici mes éléments, et tu les afficheras de telle façon&#8221;.

<pre><div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    <span class="co1">#import &lt;UIKit/UIKit.h&gt;</span><br />
    <br />
    <span class="kw1">@interface</span> SecondViewController <span class="sy0">:</span> UIViewController ‹UITableViewDelegate, UITableViewDataSource›<br />
    <span class="br0">&#123;</span> <br />
        <a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSMutableArray_Class/"><span class="kw5">NSMutableArray</span></a> <span class="sy0">*</span> items;<br />
    <span class="br0">&#125;</span> <br />
    <br />
    <span class="kw1">@end</span>
  </div>
</div>

</pre>

<pre><div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    <span class="co1">#import "SecondViewController.h"</span><br />
    <br />
    <span class="kw1">@implementation</span> SecondViewController<br />
    <span class="sy0">-</span> <span class="br0">&#40;</span><span class="kw4">id</span><span class="br0">&#41;</span>initWithNibName<span class="sy0">:</span><span class="br0">&#40;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/"><span class="kw5">NSString</span></a> <span class="sy0">*</span><span class="br0">&#41;</span>nibNameOrNil bundle<span class="sy0">:</span><span class="br0">&#40;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSBundle_Class/"><span class="kw5">NSBundle</span></a> <span class="sy0">*</span><span class="br0">&#41;</span>nibBundleOrNil<br />
    <span class="br0">&#123;</span><br />
    &nbsp; &nbsp; self <span class="sy0">=</span> <span class="br0">&#91;</span>super initWithNibName<span class="sy0">:</span>nibNameOrNil bundle<span class="sy0">:</span>nibBundleOrNil<span class="br0">&#93;</span>;<br />
    &nbsp; &nbsp; <span class="kw1">if</span> <span class="br0">&#40;</span>self<span class="br0">&#41;</span> <span class="br0">&#123;</span><br />
    &nbsp; &nbsp; &nbsp; &nbsp; self.title <span class="sy0">=</span> NSLocalizedString<span class="br0">&#40;</span><span class="co3">@</span><span class="st0">"Second"</span>, <span class="co3">@</span><span class="st0">"Second"</span><span class="br0">&#41;</span>;<br />
    &nbsp; &nbsp; &nbsp; &nbsp; self.tabBarItem.image <span class="sy0">=</span> <span class="br0">&#91;</span>UIImage imageNamed<span class="sy0">:</span><span class="co3">@</span><span class="st0">"second"</span><span class="br0">&#93;</span>;<br />
    <br />
    &nbsp; &nbsp; &nbsp; &nbsp; <span class="co2">// Mes éléments</span><br />
    &nbsp; &nbsp; &nbsp; &nbsp; items <span class="sy0">=</span> <span class="br0">&#91;</span><span class="br0">&#91;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSMutableArray_Class/"><span class="kw5">NSMutableArray</span></a> alloc<span class="br0">&#93;</span> initWithObjects<span class="sy0">:</span><span class="co3">@</span><span class="st0">"item 1"</span>, <span class="co3">@</span><span class="st0">"item 2"</span>, <span class="co3">@</span><span class="st0">"item 3"</span>, <span class="kw2">nil</span><span class="br0">&#93;</span>;<br />
    <br />
    &nbsp; &nbsp; <span class="br0">&#125;</span><br />
    &nbsp; &nbsp; <span class="kw1">return</span> self;<br />
    <span class="br0">&#125;</span><br />
    <br />
    <span class="co1">#pragma mark - UITableViewDelegate</span><br />
    <br />
    <span class="sy0">-</span> <span class="br0">&#40;</span><span class="kw4">void</span><span class="br0">&#41;</span> tableView<span class="sy0">:</span><span class="br0">&#40;</span>UITableView <span class="sy0">*</span><span class="br0">&#41;</span>tableView didSelectRowAtIndexPath<span class="sy0">:</span><span class="br0">&#40;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSIndexPath_Class/"><span class="kw5">NSIndexPath</span></a> <span class="sy0">*</span><span class="br0">&#41;</span>indexPath<br />
    <span class="br0">&#123;</span><br />
    &nbsp; &nbsp; <a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/"><span class="kw5">NSString</span></a> <span class="sy0">*</span> labelCell <span class="sy0">=</span> <span class="br0">&#91;</span>items objectAtIndex<span class="sy0">:</span>indexPath.row<span class="br0">&#93;</span>;<br />
    &nbsp; &nbsp; <span class="br0">&#91;</span><span class="br0">&#91;</span><span class="br0">&#91;</span>UIAlertView alloc<span class="br0">&#93;</span> initWithTitle<span class="sy0">:</span><span class="co3">@</span><span class="st0">"Bravo"</span> message<span class="sy0">:</span><span class="br0">&#91;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/"><span class="kw5">NSString</span></a> stringWithFormat<span class="sy0">:</span><span class="co3">@</span><span class="st0">" tu as cliqué sur l'élément %@"</span>, labelCell<span class="br0">&#93;</span> delegate<span class="sy0">:</span><span class="kw2">nil</span> cancelButtonTitle<span class="sy0">:</span><span class="co3">@</span><span class="st0">"Fermer"</span> otherButtonTitles<span class="sy0">:</span><span class="kw2">nil</span>, <span class="kw2">nil</span><span class="br0">&#93;</span> show<span class="br0">&#93;</span>;<br />
    <br />
    <span class="br0">&#125;</span><br />
    <br />
    <span class="co1">#pragma mark - UITableViewDataSource</span><br />
    <br />
    <span class="sy0">-</span> <span class="br0">&#40;</span>NSInteger<span class="br0">&#41;</span>tableView<span class="sy0">:</span><span class="br0">&#40;</span>UITableView <span class="sy0">*</span><span class="br0">&#41;</span>tableView numberOfRowsInSection<span class="sy0">:</span><span class="br0">&#40;</span>NSInteger<span class="br0">&#41;</span>section<br />
    <span class="br0">&#123;</span><br />
    &nbsp; &nbsp; <span class="kw1">return</span> <span class="br0">&#91;</span>items count<span class="br0">&#93;</span>;<br />
    <span class="br0">&#125;</span><br />
    <br />
    <span class="sy0">-</span> <span class="br0">&#40;</span>UITableViewCell <span class="sy0">*</span><span class="br0">&#41;</span>tableView<span class="sy0">:</span><span class="br0">&#40;</span>UITableView <span class="sy0">*</span><span class="br0">&#41;</span>tableView cellForRowAtIndexPath<span class="sy0">:</span><span class="br0">&#40;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSIndexPath_Class/"><span class="kw5">NSIndexPath</span></a> <span class="sy0">*</span><span class="br0">&#41;</span>indexPath<br />
    <span class="br0">&#123;</span><br />
    &nbsp; &nbsp; <span class="kw4">static</span> <a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/"><span class="kw5">NSString</span></a> <span class="sy0">*</span>CellIdentifier <span class="sy0">=</span> <span class="co3">@</span><span class="st0">"Cell"</span>;<br />
    <br />
    &nbsp; &nbsp; UITableViewCell <span class="sy0">*</span>cell <span class="sy0">=</span> <span class="br0">&#91;</span>tableView dequeueReusableCellWithIdentifier<span class="sy0">:</span>CellIdentifier<span class="br0">&#93;</span>;<br />
    &nbsp; &nbsp; <span class="kw1">if</span> <span class="br0">&#40;</span>cell <span class="sy0">==</span> <span class="kw2">nil</span><span class="br0">&#41;</span> <span class="br0">&#123;</span><br />
    &nbsp; &nbsp; &nbsp; &nbsp; cell <span class="sy0">=</span> <span class="br0">&#91;</span><span class="br0">&#91;</span>UITableViewCell alloc<span class="br0">&#93;</span> initWithStyle<span class="sy0">:</span>UITableViewCellStyleDefault reuseIdentifier<span class="sy0">:</span>CellIdentifier<span class="br0">&#93;</span>;<br />
    &nbsp; &nbsp; <span class="br0">&#125;</span><br />
    <br />
    &nbsp; &nbsp; <span class="co2">// Configuration de la cellule</span><br />
    &nbsp; &nbsp; <a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/"><span class="kw5">NSString</span></a> <span class="sy0">*</span> labelCell <span class="sy0">=</span> <span class="br0">&#91;</span>items objectAtIndex<span class="sy0">:</span>indexPath.row<span class="br0">&#93;</span>;<br />
    &nbsp; &nbsp; <span class="br0">&#91;</span>cell.textLabel setText<span class="sy0">:</span>labelCell<span class="br0">&#93;</span>;<br />
      &nbsp; <span class="kw1">return</span> cell;<br />
    <span class="br0">&#125;</span><br />
    <br />
    <span class="sy0">-</span> <span class="br0">&#40;</span>NSInteger<span class="br0">&#41;</span> tableView<span class="sy0">:</span><span class="br0">&#40;</span>UITableView <span class="sy0">*</span><span class="br0">&#41;</span>tableView sectionForSectionIndexTitle<span class="sy0">:</span><span class="br0">&#40;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/"><span class="kw5">NSString</span></a> <span class="sy0">*</span><span class="br0">&#41;</span>title atIndex<span class="sy0">:</span><span class="br0">&#40;</span>NSInteger<span class="br0">&#41;</span>index<br />
    <span class="br0">&#123;</span><br />
    &nbsp; &nbsp; <span class="kw1">return</span> <span class="nu0">1</span>;<br />
    <span class="br0">&#125;</span><br />
    <br />
    <span class="kw1">@end</span>
  </div>
</div>

</pre>

Une autre façon de faire, sans Interface Builder, est d&#8217;instancier et lier notre UITableView par nous même :

<pre><div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    <span class="sy0">-</span> <span class="br0">&#40;</span><span class="kw4">id</span><span class="br0">&#41;</span>initWithNibName<span class="sy0">:</span><span class="br0">&#40;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/"><span class="kw5">NSString</span></a> <span class="sy0">*</span><span class="br0">&#41;</span>nibNameOrNil bundle<span class="sy0">:</span><span class="br0">&#40;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSBundle_Class/"><span class="kw5">NSBundle</span></a> <span class="sy0">*</span><span class="br0">&#41;</span>nibBundleOrNil<br />
    <span class="br0">&#123;</span><br />
    &nbsp; &nbsp; self <span class="sy0">=</span> <span class="br0">&#91;</span>super initWithNibName<span class="sy0">:</span>nibNameOrNil bundle<span class="sy0">:</span>nibBundleOrNil<span class="br0">&#93;</span>;<br />
    &nbsp; &nbsp; <span class="kw1">if</span> <span class="br0">&#40;</span>self<span class="br0">&#41;</span> <span class="br0">&#123;</span><br />
    &nbsp; &nbsp; &nbsp; &nbsp; self.title <span class="sy0">=</span> NSLocalizedString<span class="br0">&#40;</span><span class="co3">@</span><span class="st0">"First"</span>, <span class="co3">@</span><span class="st0">"First"</span><span class="br0">&#41;</span>;<br />
    &nbsp; &nbsp; &nbsp; &nbsp; self.tabBarItem.image <span class="sy0">=</span> <span class="br0">&#91;</span>UIImage imageNamed<span class="sy0">:</span><span class="co3">@</span><span class="st0">"first"</span><span class="br0">&#93;</span>;<br />
    <br />
    &nbsp; &nbsp; &nbsp; &nbsp; items <span class="sy0">=</span> <span class="br0">&#91;</span><span class="br0">&#91;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSMutableArray_Class/"><span class="kw5">NSMutableArray</span></a> alloc<span class="br0">&#93;</span> initWithObjects<span class="sy0">:</span><span class="co3">@</span><span class="st0">"item 1"</span>, <span class="co3">@</span><span class="st0">"item 2"</span>, <span class="co3">@</span><span class="st0">"item 3"</span>, <span class="kw2">nil</span><span class="br0">&#93;</span>;<br />
    <br />
    &nbsp; &nbsp; &nbsp; &nbsp; mytableView <span class="sy0">=</span> <span class="br0">&#91;</span><span class="br0">&#91;</span>UITableView alloc<span class="br0">&#93;</span> initWithFrame<span class="sy0">:</span>self.view.frame<span class="br0">&#93;</span>;<br />
    &nbsp; &nbsp; &nbsp; &nbsp; <span class="br0">&#91;</span>mytableView setDelegate<span class="sy0">:</span>self<span class="br0">&#93;</span>;<br />
    &nbsp; &nbsp; &nbsp; &nbsp; <span class="br0">&#91;</span>mytableView setDataSource<span class="sy0">:</span>self<span class="br0">&#93;</span>;<br />
    &nbsp; &nbsp; &nbsp; &nbsp; <span class="br0">&#91;</span>self.view addSubview<span class="sy0">:</span>mytableView<span class="br0">&#93;</span>;<br />
    <br />
    &nbsp; &nbsp; <span class="br0">&#125;</span><br />
    &nbsp; &nbsp; <span class="kw1">return</span> self;<br />
    <span class="br0">&#125;</span>
  </div>
</div>

</pre>

Et de cette façon-là, on entre dans le vif du sujet !  
Interface Builder nous masque cette étape avec ses liaisons &#8220;user friendly&#8221;, mais le fait de dire à la tableView &#8220;Bonjour, ton dataSource et ton delegate c&#8217;est moi&#8221; c&#8217;est tout le principe du fonctionnement par délégation.

À ce stade, la tableView n&#8217;a aucune visibilité sur le contrôleur, pourtant elle va afficher / se configurer grâce à lui.

# Fonctionnement général

Le système de délégation permet à un objet A de dire à un objet B &#8220;Hey toi, implémente cette méthode, je vais l&#8217;appeler et tu as intérêt à répondre&#8221;.

L&#8217;objet A ne saura pas le type de l&#8217;objet B, il va juste tenter d’exécuter une méthode sur un objet &#8220;générique&#8221;. On dit à ce moment-là que l&#8217;objet A va exécuter du code sur son Delegate.

Il devient donc nécessaire d&#8217;implémenter certaines méthodes &#8220;obligatoire&#8221; comme

<pre><div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    <span class="sy0">-</span> <span class="br0">&#40;</span><span class="kw4">void</span><span class="br0">&#41;</span> tableView<span class="sy0">:</span><span class="br0">&#40;</span>UITableView <span class="sy0">*</span><span class="br0">&#41;</span>tableView didSelectRowAtIndexPath<span class="sy0">:</span><span class="br0">&#40;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSIndexPath_Class/"><span class="kw5">NSIndexPath</span></a> <span class="sy0">*</span><span class="br0">&#41;</span>indexPath;<br />
    <span class="sy0">-</span> <span class="br0">&#40;</span>NSInteger<span class="br0">&#41;</span>tableView<span class="sy0">:</span><span class="br0">&#40;</span>UITableView <span class="sy0">*</span><span class="br0">&#41;</span>tableView numberOfRowsInSection<span class="sy0">:</span><span class="br0">&#40;</span>NSInteger<span class="br0">&#41;</span>section;<br />
    <span class="sy0">-</span> <span class="br0">&#40;</span>UITableViewCell <span class="sy0">*</span><span class="br0">&#41;</span>tableView<span class="sy0">:</span><span class="br0">&#40;</span>UITableView <span class="sy0">*</span><span class="br0">&#41;</span>tableView cellForRowAtIndexPath<span class="sy0">:</span><span class="br0">&#40;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSIndexPath_Class/"><span class="kw5">NSIndexPath</span></a> <span class="sy0">*</span><span class="br0">&#41;</span>indexPath;<br />
    <span class="sy0">-</span> <span class="br0">&#40;</span>NSInteger<span class="br0">&#41;</span> tableView<span class="sy0">:</span><span class="br0">&#40;</span>UITableView <span class="sy0">*</span><span class="br0">&#41;</span>tableView sectionForSectionIndexTitle<span class="sy0">:</span><span class="br0">&#40;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/"><span class="kw5">NSString</span></a> <span class="sy0">*</span><span class="br0">&#41;</span>title atIndex<span class="sy0">:</span><span class="br0">&#40;</span>NSInteger<span class="br0">&#41;</span>index;
  </div>
</div>

</pre>

On dit alors que la classe A implémente les méthodes du protocole de B. Certaines méthodes sont à implémenter sinon l&#8217;application crash, d&#8217;autres non.

Pour que xCode soit sympathique avec nous, il est conseillé de se déclarer comme apte à supporter ce protocole, d’où la ligne

<pre><div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    <span class="kw1">@interface</span> SecondViewController <span class="sy0">:</span> UIViewController ‹UITableViewDelegate, UITableViewDataSource›
  </div>
</div>

</pre>

En faisant ça, xCode nous signalera par un warning si l’on oublie d&#8217;implémenter une méthode du protocole et va à ce moment-là proposer l&#8217;auto complétion pour les rajouter. Il serait dommage d&#8217;oublier cette étape.

# Création d&#8217;un delegate

Question : Mais comment a fait l&#8217;objet de type UITableView pour nous forcer à implémenter des méthodes pour l&#8217;utiliser ?  
Réponse: il a prévu / écrit un &#8220;protocole&#8221; et en typant un objet avec ce protocole, il peut exécuter ses méthodes sur l&#8217;objet. À aucun moment, UITableView n&#8217;implémente lui même ces méthodes. Il va supposer qu&#8217;on l&#8217;a fait.

## Let&#8217;s code

Imaginons que l&#8217;on veuille créer un petit jeu de &#8220;TapTap je te tue&#8221;. Pour cela on doit mettre en place des vues (qui représentent un ennemi par exemple) où lorsqu&#8217;on appuie X fois dessus, elles puissent dire à la vue principale &#8220;Hey c&#8217;est bon pour moi, on m&#8217;a assez appuyé dessus ! Tu peux me supprimer, calculer ton score ou autre&#8221;

Pour cela nous allons créer une classe qui hérite de UIView, lui associer un protocole et un objet &#8220;delegate&#8221; puis nous allons faire des appels à cette méthode.

<pre id="line1"><div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    &nbsp;<span class="co2">// &nbsp;TapTapView.h MyCompanyName__. All rights reserved.</span><br />
    <br />
    <span class="co1">#import ‹UIKit/UIKit.h›</span><br />
    <br />
    <span class="co2">// Je vais utiliser une référence à la classe dans le protocole avant de l'utiliser</span><br />
    <span class="kw1">@class</span> TapTapView;<br />
    <br />
    <span class="co2">// Le protocole est la</span><br />
    <span class="kw1">@protocol</span> TapTapViewDelegate ‹<a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSObject_Class/"><span class="kw5">NSObject</span></a>›<br />
    &nbsp; &nbsp; <span class="sy0">-</span> <span class="br0">&#40;</span>NSUInteger<span class="br0">&#41;</span> taptapNumberTapToClose<span class="sy0">:</span><span class="br0">&#40;</span>TapTapView <span class="sy0">*</span><span class="br0">&#41;</span>view;<br />
    &nbsp; &nbsp; <span class="sy0">-</span> <span class="br0">&#40;</span><span class="kw4">void</span><span class="br0">&#41;</span> taptapCloseMe<span class="sy0">:</span><span class="br0">&#40;</span>TapTapView <span class="sy0">*</span><span class="br0">&#41;</span>view;<br />
    <span class="kw1">@end</span><br />
    <br />
    <span class="co2">// Ma classe</span><br />
    <span class="kw1">@interface</span> TapTapView <span class="sy0">:</span> UIView <br />
    <br />
    <span class="sy0">-</span> <span class="br0">&#40;</span><span class="kw4">void</span><span class="br0">&#41;</span> initialize;<br />
    <br />
    <span class="co2">// Mon delegate</span><br />
    <span class="kw1">@property</span> <span class="br0">&#40;</span>nonatomic,assign<span class="br0">&#41;</span> <span class="kw4">id</span> ‹TapTapViewDelegate› delegate;<br />
    <br />
    <span class="kw1">@end</span>
  </div>
</div>

</pre>

*   Une bonne pratique est de préfixer les méthodes du protocole.
*   Une bonne pratique est de toujours envoyer en référence dans les méthodes du protocole l&#8217;objet qui les a déclenchés.
*   Une bonne pratique est d&#8217;appeler son objet de délégation &#8220;delegate&#8221; du moment qu&#8217;on en manipule qu&#8217;un et que les méthodes du protocoles servent à la logique d&#8217;un seul processus

Voyons maintenant son implémentation

<pre id="line1"><div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    <span class="co1">#import "TapTapView.h"</span><br />
    <br />
    <span class="kw1">@implementation</span> TapTapView<br />
    <br />
    <span class="kw1">@synthesize</span> delegate;<br />
    <br />
    <span class="sy0">-</span> <span class="br0">&#40;</span><span class="kw4">void</span><span class="br0">&#41;</span> initialize<br />
    <span class="br0">&#123;</span> &nbsp; &nbsp;<br />
    &nbsp; &nbsp; UITapGestureRecognizer<span class="sy0">*</span> gesture <span class="sy0">=</span> <span class="br0">&#91;</span><span class="br0">&#91;</span>UITapGestureRecognizer alloc<span class="br0">&#93;</span> initWithTarget<span class="sy0">:</span>self action<span class="sy0">:</span><span class="kw1">@selector</span><span class="br0">&#40;</span>onTapped<span class="sy0">:</span><span class="br0">&#41;</span><span class="br0">&#93;</span>;<br />
    <br />
    &nbsp; &nbsp; <span class="co2">// J'attend un retour du delegate</span><br />
    &nbsp; &nbsp; gesture.numberOfTapsRequired <span class="sy0">=</span> <span class="br0">&#91;</span>self.delegate taptapNumberTapToClose<span class="sy0">:</span>self<span class="br0">&#93;</span>;<br />
    &nbsp; &nbsp; gesture.numberOfTouchesRequired <span class="sy0">=</span> <span class="nu0">1</span>;<br />
    <br />
    &nbsp; &nbsp; <span class="br0">&#91;</span>self addGestureRecognizer<span class="sy0">:</span> gesture<span class="br0">&#93;</span>;<br />
    <br />
    <span class="br0">&#125;</span><br />
    <br />
    <span class="sy0">-</span> <span class="br0">&#40;</span><span class="kw4">void</span><span class="br0">&#41;</span> onTapped<span class="sy0">:</span><span class="br0">&#40;</span><span class="kw4">id</span><span class="br0">&#41;</span>sender<br />
    <span class="br0">&#123;</span> <br />
    &nbsp; &nbsp; <span class="co2">// Je signale que la gesture est reconnue</span><br />
    &nbsp; &nbsp; <span class="br0">&#91;</span>self.delegate taptapCloseMe<span class="sy0">:</span>self<span class="br0">&#93;</span>;<br />
    <span class="br0">&#125;</span> <br />
    <br />
    <span class="kw1">@end</span>
  </div>
</div>

</pre>

Et enfin son utilisation.

Ici j&#8217;ai prévu que la classe A doit avoir implémenté la méthode &#8220;taptapNumberTapToClose&#8221; pour configurer le nombre de touches avant la destruction.

Du coup, en passant en paramètre la vue à la méthode taptapNumberToClose (cf bonne pratique) et en utilisant la notion de tag de toute vue, je peux faire en sorte que toutes les vues ayant un tag de &#8220;1664&#8243; doivent recevoir 10 tap avant de se détruire et seulement 2 fois pour les vues de tag &#8220;1&#8243;.

<pre id="line1"><div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    <span class="co1">#import "FirstViewController.h"</span><br />
    <br />
    <span class="kw1">@implementation</span> FirstViewController<br />
    <br />
    <span class="sy0">-</span> <span class="br0">&#40;</span><span class="kw4">id</span><span class="br0">&#41;</span>initWithNibName<span class="sy0">:</span><span class="br0">&#40;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/"><span class="kw5">NSString</span></a> <span class="sy0">*</span><span class="br0">&#41;</span>nibNameOrNil bundle<span class="sy0">:</span><span class="br0">&#40;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSBundle_Class/"><span class="kw5">NSBundle</span></a> <span class="sy0">*</span><span class="br0">&#41;</span>nibBundleOrNil<br />
    <span class="br0">&#123;</span><br />
    &nbsp; &nbsp; self <span class="sy0">=</span> <span class="br0">&#91;</span>super initWithNibName<span class="sy0">:</span>nibNameOrNil bundle<span class="sy0">:</span>nibBundleOrNil<span class="br0">&#93;</span>;<br />
    &nbsp; &nbsp; <span class="kw1">if</span> <span class="br0">&#40;</span>self<span class="br0">&#41;</span> <span class="br0">&#123;</span><br />
    &nbsp; &nbsp; &nbsp; &nbsp; self.title <span class="sy0">=</span> NSLocalizedString<span class="br0">&#40;</span><span class="co3">@</span><span class="st0">"First"</span>, <span class="co3">@</span><span class="st0">"First"</span><span class="br0">&#41;</span>;<br />
    &nbsp; &nbsp; &nbsp; &nbsp; self.tabBarItem.image <span class="sy0">=</span> <span class="br0">&#91;</span>UIImage imageNamed<span class="sy0">:</span><span class="co3">@</span><span class="st0">"first"</span><span class="br0">&#93;</span>;<br />
    <br />
    &nbsp; &nbsp; <span class="br0">&#125;</span><br />
    &nbsp; &nbsp; <span class="kw1">return</span> self;<br />
    <span class="br0">&#125;</span><br />
    <br />
    <span class="sy0">-</span> <span class="br0">&#40;</span><span class="kw4">void</span><span class="br0">&#41;</span> viewDidLoad<br />
    <span class="br0">&#123;</span><br />
    &nbsp; &nbsp; <span class="br0">&#91;</span>super viewDidLoad<span class="br0">&#93;</span>;<br />
    <br />
    &nbsp; &nbsp; TapTapView <span class="sy0">*</span> tap <span class="sy0">=</span> <span class="br0">&#91;</span><span class="br0">&#91;</span>TapTapView alloc<span class="br0">&#93;</span> initWithFrame<span class="sy0">:</span>CGRectMake<span class="br0">&#40;</span><span class="nu0"></span>, <span class="nu0"></span>, <span class="nu0">100</span>, <span class="nu0">100</span><span class="br0">&#41;</span><span class="br0">&#93;</span>;<br />
    &nbsp; &nbsp; <span class="br0">&#91;</span>tap setBackgroundColor<span class="sy0">:</span><span class="br0">&#91;</span>UIColor redColor<span class="br0">&#93;</span><span class="br0">&#93;</span>;<br />
    &nbsp; &nbsp; <span class="br0">&#91;</span>tap setDelegate<span class="sy0">:</span>self<span class="br0">&#93;</span>;<br />
    &nbsp; &nbsp; <span class="br0">&#91;</span>tap initialize<span class="br0">&#93;</span>; &nbsp; &nbsp;<br />
    <br />
    &nbsp; &nbsp; <span class="br0">&#91;</span>self.view addSubview<span class="sy0">:</span>tap<span class="br0">&#93;</span>;<br />
    <br />
    <span class="br0">&#125;</span><br />
    <br />
    <span class="sy0">-</span> <span class="br0">&#40;</span>NSUInteger<span class="br0">&#41;</span> taptapNumberTapToClose<span class="sy0">:</span><span class="br0">&#40;</span>TapTapView <span class="sy0">*</span><span class="br0">&#41;</span>view<br />
    <span class="br0">&#123;</span><br />
    &nbsp; &nbsp; <span class="kw1">return</span> <span class="nu0">5</span>;<br />
    <span class="br0">&#125;</span><br />
    <br />
    <span class="sy0">-</span> <span class="br0">&#40;</span><span class="kw4">void</span><span class="br0">&#41;</span> taptapCloseMe<span class="sy0">:</span><span class="br0">&#40;</span>TapTapView <span class="sy0">*</span><span class="br0">&#41;</span>view<br />
    <span class="br0">&#123;</span><br />
    &nbsp; &nbsp; <span class="br0">&#91;</span>view removeFromSuperview<span class="br0">&#93;</span>;<br />
    <span class="br0">&#125;</span>
  </div>
</div>

</pre>

Dans le cas présent, on a simplement dit &#8220;au bout de 5 tap, l&#8217;objet afficher sera détruit&#8221; sans faire de distinction particulière.

Mais que se passe-t&#8217;il si dans mon controller je n&#8217;ai pas défini ces méthodes ?  
La réponse est simple: ça plante.

Eh oui, on essayera d’exécuter une méthode sur un objet qui ne l&#8217;implémente pas.  
Pour faire des méthodes non obligatoires, par exemple pour définir des méthodes d’initialisations facultatives on va devoir un petit peu modifier TapTapView.

<pre><div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    <span class="kw1">if</span><span class="br0">&#40;</span><span class="br0">&#91;</span>self.delegate respondsToSelector<span class="sy0">:</span><span class="kw1">@selector</span><span class="br0">&#40;</span>taptapNumberTapToClose<span class="sy0">:</span><span class="br0">&#41;</span><span class="br0">&#93;</span><span class="br0">&#41;</span><br />
    <span class="br0">&#123;</span><br />
    &nbsp; &nbsp; gesture.numberOfTapsRequired <span class="sy0">=</span><span class="br0">&#91;</span>self.delegate taptapNumberTapToClose<span class="sy0">:</span>self<span class="br0">&#93;</span>;<br />
    <span class="br0">&#125;</span><br />
    <span class="kw1">else</span><br />
    <span class="br0">&#123;</span><br />
    &nbsp; &nbsp; gesture.numberOfTapsRequired <span class="sy0">=</span> <span class="nu0">2</span>;<br />
    <span class="br0">&#125;</span>
  </div>
</div>

</pre>

# Conclusion

Utiliser le système de délégation est fort pratique, voire encouragé par l&#8217;utilisation massive qu&#8217;il y a dans UIKit, mais peut se révéler fort contraignant quand on commence à chainer les appels de méthodes pour faire remonter une information à un niveau supérieur.

En effet ici on remonte l&#8217;info d&#8217;un niveau, mais pour faire remonter encore plus haut on est vite tenté de faire un delegate &#8220;passerelle&#8221; à chaque passage. Et là bonjour le conflit de nom ou autre.

En général quand on a besoin de faire remonter plus haut c&#8217;est que plusieurs éléments à l&#8217;écran et votre super singleton de gestion de l&#8217;application / jeux veulent l&#8217;info en même temps. Dans ce cas le système de délégation arrive vite à ses limites. Heureusement, le système de délégation n&#8217;est pas le seul moyen pour échanger de l&#8217;information, il y a aussi [le système de notification][1], mais ça, c&#8217;est un autre article <img src="http://old-blog.elao.dev/wp-includes/images/smilies/icon_smile.gif" alt="icon smile iPhone   Delegation" class="wp-smiley" title="iPhone   Delegation" /> 

&nbsp;

&nbsp;

&nbsp;

 [1]: http://developer.apple.com/library/ios/#DOCUMENTATION/Cocoa/Reference/Foundation/Classes/NSNotificationCenter_Class/Reference/Reference.html