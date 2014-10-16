
En tant que développeur iOS débutant, on utilise sans trop faire attention le système dit de délégation. C'est un système central, que l'on est obligé d'avoir utilisé au moins une fois, par exemple à travers la mise en place d'une liste d'éléments comme UITableViewController. Cet article a pour but d'expliquer comment ça marche, en vue de créer nos propres "Delegate".

<!--more-->

# Utilisation classique sur une UITableView

Quand on débute, on ne jure souvent que par Interface Builder et les tutoriels trouvés à gauche à droite pour afficher une liste de résultats. Sommairement, l'idée est de placer une UITableView sur notre vue courante, puis de l'associer via les outlets "dataSource" et "delegate" à notre contrôleur (file owner).

<a href="http://www.elao.com/blog/iphone/iphone-delegation.html/attachment/capture-de%cc%81cran-2012-07-13-a%cc%80-14-17-28" rel="attachment wp-att-2165"><img class="alignnone size-medium wp-image-2165" title="Votre tableView" src="http://www.elao.com/blog/wp-content/uploads/2012/07/Capture-d’écran-2012-07-13-à-14.17.28-300x158.png" alt="Capture d’écran 2012 07 13 à 14.17.28 300x158 iPhone   Delegation" width="300" height="158" /></a>

Après ça, on dit à notre contrôleur "Voici mes éléments, et tu les afficheras de telle façon".

<pre><div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    <span class="co1">#import <UIKit/UIKit.h></span><br />
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
        self <span class="sy0">=</span> <span class="br0">&#91;</span>super initWithNibName<span class="sy0">:</span>nibNameOrNil bundle<span class="sy0">:</span>nibBundleOrNil<span class="br0">&#93;</span>;<br />
        <span class="kw1">if</span> <span class="br0">&#40;</span>self<span class="br0">&#41;</span> <span class="br0">&#123;</span><br />
            self.title <span class="sy0">=</span> NSLocalizedString<span class="br0">&#40;</span><span class="co3">@</span><span class="st0">"Second"</span>, <span class="co3">@</span><span class="st0">"Second"</span><span class="br0">&#41;</span>;<br />
            self.tabBarItem.image <span class="sy0">=</span> <span class="br0">&#91;</span>UIImage imageNamed<span class="sy0">:</span><span class="co3">@</span><span class="st0">"second"</span><span class="br0">&#93;</span>;<br />
    <br />
            <span class="co2">// Mes éléments</span><br />
            items <span class="sy0">=</span> <span class="br0">&#91;</span><span class="br0">&#91;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSMutableArray_Class/"><span class="kw5">NSMutableArray</span></a> alloc<span class="br0">&#93;</span> initWithObjects<span class="sy0">:</span><span class="co3">@</span><span class="st0">"item 1"</span>, <span class="co3">@</span><span class="st0">"item 2"</span>, <span class="co3">@</span><span class="st0">"item 3"</span>, <span class="kw2">nil</span><span class="br0">&#93;</span>;<br />
    <br />
        <span class="br0">&#125;</span><br />
        <span class="kw1">return</span> self;<br />
    <span class="br0">&#125;</span><br />
    <br />
    <span class="co1">#pragma mark - UITableViewDelegate</span><br />
    <br />
    <span class="sy0">-</span> <span class="br0">&#40;</span><span class="kw4">void</span><span class="br0">&#41;</span> tableView<span class="sy0">:</span><span class="br0">&#40;</span>UITableView <span class="sy0">*</span><span class="br0">&#41;</span>tableView didSelectRowAtIndexPath<span class="sy0">:</span><span class="br0">&#40;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSIndexPath_Class/"><span class="kw5">NSIndexPath</span></a> <span class="sy0">*</span><span class="br0">&#41;</span>indexPath<br />
    <span class="br0">&#123;</span><br />
        <a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/"><span class="kw5">NSString</span></a> <span class="sy0">*</span> labelCell <span class="sy0">=</span> <span class="br0">&#91;</span>items objectAtIndex<span class="sy0">:</span>indexPath.row<span class="br0">&#93;</span>;<br />
        <span class="br0">&#91;</span><span class="br0">&#91;</span><span class="br0">&#91;</span>UIAlertView alloc<span class="br0">&#93;</span> initWithTitle<span class="sy0">:</span><span class="co3">@</span><span class="st0">"Bravo"</span> message<span class="sy0">:</span><span class="br0">&#91;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/"><span class="kw5">NSString</span></a> stringWithFormat<span class="sy0">:</span><span class="co3">@</span><span class="st0">" tu as cliqué sur l'élément %@"</span>, labelCell<span class="br0">&#93;</span> delegate<span class="sy0">:</span><span class="kw2">nil</span> cancelButtonTitle<span class="sy0">:</span><span class="co3">@</span><span class="st0">"Fermer"</span> otherButtonTitles<span class="sy0">:</span><span class="kw2">nil</span>, <span class="kw2">nil</span><span class="br0">&#93;</span> show<span class="br0">&#93;</span>;<br />
    <br />
    <span class="br0">&#125;</span><br />
    <br />
    <span class="co1">#pragma mark - UITableViewDataSource</span><br />
    <br />
    <span class="sy0">-</span> <span class="br0">&#40;</span>NSInteger<span class="br0">&#41;</span>tableView<span class="sy0">:</span><span class="br0">&#40;</span>UITableView <span class="sy0">*</span><span class="br0">&#41;</span>tableView numberOfRowsInSection<span class="sy0">:</span><span class="br0">&#40;</span>NSInteger<span class="br0">&#41;</span>section<br />
    <span class="br0">&#123;</span><br />
        <span class="kw1">return</span> <span class="br0">&#91;</span>items count<span class="br0">&#93;</span>;<br />
    <span class="br0">&#125;</span><br />
    <br />
    <span class="sy0">-</span> <span class="br0">&#40;</span>UITableViewCell <span class="sy0">*</span><span class="br0">&#41;</span>tableView<span class="sy0">:</span><span class="br0">&#40;</span>UITableView <span class="sy0">*</span><span class="br0">&#41;</span>tableView cellForRowAtIndexPath<span class="sy0">:</span><span class="br0">&#40;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSIndexPath_Class/"><span class="kw5">NSIndexPath</span></a> <span class="sy0">*</span><span class="br0">&#41;</span>indexPath<br />
    <span class="br0">&#123;</span><br />
        <span class="kw4">static</span> <a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/"><span class="kw5">NSString</span></a> <span class="sy0">*</span>CellIdentifier <span class="sy0">=</span> <span class="co3">@</span><span class="st0">"Cell"</span>;<br />
    <br />
        UITableViewCell <span class="sy0">*</span>cell <span class="sy0">=</span> <span class="br0">&#91;</span>tableView dequeueReusableCellWithIdentifier<span class="sy0">:</span>CellIdentifier<span class="br0">&#93;</span>;<br />
        <span class="kw1">if</span> <span class="br0">&#40;</span>cell <span class="sy0">==</span> <span class="kw2">nil</span><span class="br0">&#41;</span> <span class="br0">&#123;</span><br />
            cell <span class="sy0">=</span> <span class="br0">&#91;</span><span class="br0">&#91;</span>UITableViewCell alloc<span class="br0">&#93;</span> initWithStyle<span class="sy0">:</span>UITableViewCellStyleDefault reuseIdentifier<span class="sy0">:</span>CellIdentifier<span class="br0">&#93;</span>;<br />
        <span class="br0">&#125;</span><br />
    <br />
        <span class="co2">// Configuration de la cellule</span><br />
        <a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/"><span class="kw5">NSString</span></a> <span class="sy0">*</span> labelCell <span class="sy0">=</span> <span class="br0">&#91;</span>items objectAtIndex<span class="sy0">:</span>indexPath.row<span class="br0">&#93;</span>;<br />
        <span class="br0">&#91;</span>cell.textLabel setText<span class="sy0">:</span>labelCell<span class="br0">&#93;</span>;<br />
        <span class="kw1">return</span> cell;<br />
    <span class="br0">&#125;</span><br />
    <br />
    <span class="sy0">-</span> <span class="br0">&#40;</span>NSInteger<span class="br0">&#41;</span> tableView<span class="sy0">:</span><span class="br0">&#40;</span>UITableView <span class="sy0">*</span><span class="br0">&#41;</span>tableView sectionForSectionIndexTitle<span class="sy0">:</span><span class="br0">&#40;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/"><span class="kw5">NSString</span></a> <span class="sy0">*</span><span class="br0">&#41;</span>title atIndex<span class="sy0">:</span><span class="br0">&#40;</span>NSInteger<span class="br0">&#41;</span>index<br />
    <span class="br0">&#123;</span><br />
        <span class="kw1">return</span> <span class="nu0">1</span>;<br />
    <span class="br0">&#125;</span><br />
    <br />
    <span class="kw1">@end</span>
  </div>
</div>

</pre>

Une autre façon de faire, sans Interface Builder, est d'instancier et lier notre UITableView par nous même :

<pre><div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    <span class="sy0">-</span> <span class="br0">&#40;</span><span class="kw4">id</span><span class="br0">&#41;</span>initWithNibName<span class="sy0">:</span><span class="br0">&#40;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/"><span class="kw5">NSString</span></a> <span class="sy0">*</span><span class="br0">&#41;</span>nibNameOrNil bundle<span class="sy0">:</span><span class="br0">&#40;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSBundle_Class/"><span class="kw5">NSBundle</span></a> <span class="sy0">*</span><span class="br0">&#41;</span>nibBundleOrNil<br />
    <span class="br0">&#123;</span><br />
        self <span class="sy0">=</span> <span class="br0">&#91;</span>super initWithNibName<span class="sy0">:</span>nibNameOrNil bundle<span class="sy0">:</span>nibBundleOrNil<span class="br0">&#93;</span>;<br />
        <span class="kw1">if</span> <span class="br0">&#40;</span>self<span class="br0">&#41;</span> <span class="br0">&#123;</span><br />
            self.title <span class="sy0">=</span> NSLocalizedString<span class="br0">&#40;</span><span class="co3">@</span><span class="st0">"First"</span>, <span class="co3">@</span><span class="st0">"First"</span><span class="br0">&#41;</span>;<br />
            self.tabBarItem.image <span class="sy0">=</span> <span class="br0">&#91;</span>UIImage imageNamed<span class="sy0">:</span><span class="co3">@</span><span class="st0">"first"</span><span class="br0">&#93;</span>;<br />
    <br />
            items <span class="sy0">=</span> <span class="br0">&#91;</span><span class="br0">&#91;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSMutableArray_Class/"><span class="kw5">NSMutableArray</span></a> alloc<span class="br0">&#93;</span> initWithObjects<span class="sy0">:</span><span class="co3">@</span><span class="st0">"item 1"</span>, <span class="co3">@</span><span class="st0">"item 2"</span>, <span class="co3">@</span><span class="st0">"item 3"</span>, <span class="kw2">nil</span><span class="br0">&#93;</span>;<br />
    <br />
            mytableView <span class="sy0">=</span> <span class="br0">&#91;</span><span class="br0">&#91;</span>UITableView alloc<span class="br0">&#93;</span> initWithFrame<span class="sy0">:</span>self.view.frame<span class="br0">&#93;</span>;<br />
            <span class="br0">&#91;</span>mytableView setDelegate<span class="sy0">:</span>self<span class="br0">&#93;</span>;<br />
            <span class="br0">&#91;</span>mytableView setDataSource<span class="sy0">:</span>self<span class="br0">&#93;</span>;<br />
            <span class="br0">&#91;</span>self.view addSubview<span class="sy0">:</span>mytableView<span class="br0">&#93;</span>;<br />
    <br />
        <span class="br0">&#125;</span><br />
        <span class="kw1">return</span> self;<br />
    <span class="br0">&#125;</span>
  </div>
</div>

</pre>

Et de cette façon-là, on entre dans le vif du sujet !
Interface Builder nous masque cette étape avec ses liaisons "user friendly", mais le fait de dire à la tableView "Bonjour, ton dataSource et ton delegate c'est moi" c'est tout le principe du fonctionnement par délégation.

À ce stade, la tableView n'a aucune visibilité sur le contrôleur, pourtant elle va afficher / se configurer grâce à lui.

# Fonctionnement général

Le système de délégation permet à un objet A de dire à un objet B "Hey toi, implémente cette méthode, je vais l'appeler et tu as intérêt à répondre".

L'objet A ne saura pas le type de l'objet B, il va juste tenter d’exécuter une méthode sur un objet "générique". On dit à ce moment-là que l'objet A va exécuter du code sur son Delegate.

Il devient donc nécessaire d'implémenter certaines méthodes "obligatoire" comme

<pre><div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    <span class="sy0">-</span> <span class="br0">&#40;</span><span class="kw4">void</span><span class="br0">&#41;</span> tableView<span class="sy0">:</span><span class="br0">&#40;</span>UITableView <span class="sy0">*</span><span class="br0">&#41;</span>tableView didSelectRowAtIndexPath<span class="sy0">:</span><span class="br0">&#40;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSIndexPath_Class/"><span class="kw5">NSIndexPath</span></a> <span class="sy0">*</span><span class="br0">&#41;</span>indexPath;<br />
    <span class="sy0">-</span> <span class="br0">&#40;</span>NSInteger<span class="br0">&#41;</span>tableView<span class="sy0">:</span><span class="br0">&#40;</span>UITableView <span class="sy0">*</span><span class="br0">&#41;</span>tableView numberOfRowsInSection<span class="sy0">:</span><span class="br0">&#40;</span>NSInteger<span class="br0">&#41;</span>section;<br />
    <span class="sy0">-</span> <span class="br0">&#40;</span>UITableViewCell <span class="sy0">*</span><span class="br0">&#41;</span>tableView<span class="sy0">:</span><span class="br0">&#40;</span>UITableView <span class="sy0">*</span><span class="br0">&#41;</span>tableView cellForRowAtIndexPath<span class="sy0">:</span><span class="br0">&#40;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSIndexPath_Class/"><span class="kw5">NSIndexPath</span></a> <span class="sy0">*</span><span class="br0">&#41;</span>indexPath;<br />
    <span class="sy0">-</span> <span class="br0">&#40;</span>NSInteger<span class="br0">&#41;</span> tableView<span class="sy0">:</span><span class="br0">&#40;</span>UITableView <span class="sy0">*</span><span class="br0">&#41;</span>tableView sectionForSectionIndexTitle<span class="sy0">:</span><span class="br0">&#40;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/"><span class="kw5">NSString</span></a> <span class="sy0">*</span><span class="br0">&#41;</span>title atIndex<span class="sy0">:</span><span class="br0">&#40;</span>NSInteger<span class="br0">&#41;</span>index;
  </div>
</div>

</pre>

On dit alors que la classe A implémente les méthodes du protocole de B. Certaines méthodes sont à implémenter sinon l'application crash, d'autres non.

Pour que xCode soit sympathique avec nous, il est conseillé de se déclarer comme apte à supporter ce protocole, d’où la ligne

<pre><div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    <span class="kw1">@interface</span> SecondViewController <span class="sy0">:</span> UIViewController ‹UITableViewDelegate, UITableViewDataSource›
  </div>
</div>

</pre>

En faisant ça, xCode nous signalera par un warning si l’on oublie d'implémenter une méthode du protocole et va à ce moment-là proposer l'auto complétion pour les rajouter. Il serait dommage d'oublier cette étape.

# Création d'un delegate

Question : Mais comment a fait l'objet de type UITableView pour nous forcer à implémenter des méthodes pour l'utiliser ?
Réponse: il a prévu / écrit un "protocole" et en typant un objet avec ce protocole, il peut exécuter ses méthodes sur l'objet. À aucun moment, UITableView n'implémente lui même ces méthodes. Il va supposer qu'on l'a fait.

## Let's code

Imaginons que l'on veuille créer un petit jeu de "TapTap je te tue". Pour cela on doit mettre en place des vues (qui représentent un ennemi par exemple) où lorsqu'on appuie X fois dessus, elles puissent dire à la vue principale "Hey c'est bon pour moi, on m'a assez appuyé dessus ! Tu peux me supprimer, calculer ton score ou autre"

Pour cela nous allons créer une classe qui hérite de UIView, lui associer un protocole et un objet "delegate" puis nous allons faire des appels à cette méthode.

<pre id="line1"><div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
     <span class="co2">//  TapTapView.h MyCompanyName__. All rights reserved.</span><br />
    <br />
    <span class="co1">#import ‹UIKit/UIKit.h›</span><br />
    <br />
    <span class="co2">// Je vais utiliser une référence à la classe dans le protocole avant de l'utiliser</span><br />
    <span class="kw1">@class</span> TapTapView;<br />
    <br />
    <span class="co2">// Le protocole est la</span><br />
    <span class="kw1">@protocol</span> TapTapViewDelegate ‹<a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSObject_Class/"><span class="kw5">NSObject</span></a>›<br />
        <span class="sy0">-</span> <span class="br0">&#40;</span>NSUInteger<span class="br0">&#41;</span> taptapNumberTapToClose<span class="sy0">:</span><span class="br0">&#40;</span>TapTapView <span class="sy0">*</span><span class="br0">&#41;</span>view;<br />
        <span class="sy0">-</span> <span class="br0">&#40;</span><span class="kw4">void</span><span class="br0">&#41;</span> taptapCloseMe<span class="sy0">:</span><span class="br0">&#40;</span>TapTapView <span class="sy0">*</span><span class="br0">&#41;</span>view;<br />
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
*   Une bonne pratique est de toujours envoyer en référence dans les méthodes du protocole l'objet qui les a déclenchés.
*   Une bonne pratique est d'appeler son objet de délégation "delegate" du moment qu'on en manipule qu'un et que les méthodes du protocoles servent à la logique d'un seul processus

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
    <span class="br0">&#123;</span>    <br />
        UITapGestureRecognizer<span class="sy0">*</span> gesture <span class="sy0">=</span> <span class="br0">&#91;</span><span class="br0">&#91;</span>UITapGestureRecognizer alloc<span class="br0">&#93;</span> initWithTarget<span class="sy0">:</span>self action<span class="sy0">:</span><span class="kw1">@selector</span><span class="br0">&#40;</span>onTapped<span class="sy0">:</span><span class="br0">&#41;</span><span class="br0">&#93;</span>;<br />
    <br />
        <span class="co2">// J'attend un retour du delegate</span><br />
        gesture.numberOfTapsRequired <span class="sy0">=</span> <span class="br0">&#91;</span>self.delegate taptapNumberTapToClose<span class="sy0">:</span>self<span class="br0">&#93;</span>;<br />
        gesture.numberOfTouchesRequired <span class="sy0">=</span> <span class="nu0">1</span>;<br />
    <br />
        <span class="br0">&#91;</span>self addGestureRecognizer<span class="sy0">:</span> gesture<span class="br0">&#93;</span>;<br />
    <br />
    <span class="br0">&#125;</span><br />
    <br />
    <span class="sy0">-</span> <span class="br0">&#40;</span><span class="kw4">void</span><span class="br0">&#41;</span> onTapped<span class="sy0">:</span><span class="br0">&#40;</span><span class="kw4">id</span><span class="br0">&#41;</span>sender<br />
    <span class="br0">&#123;</span> <br />
        <span class="co2">// Je signale que la gesture est reconnue</span><br />
        <span class="br0">&#91;</span>self.delegate taptapCloseMe<span class="sy0">:</span>self<span class="br0">&#93;</span>;<br />
    <span class="br0">&#125;</span> <br />
    <br />
    <span class="kw1">@end</span>
  </div>
</div>

</pre>

Et enfin son utilisation.

Ici j'ai prévu que la classe A doit avoir implémenté la méthode "taptapNumberTapToClose" pour configurer le nombre de touches avant la destruction.

Du coup, en passant en paramètre la vue à la méthode taptapNumberToClose (cf bonne pratique) et en utilisant la notion de tag de toute vue, je peux faire en sorte que toutes les vues ayant un tag de "1664&#8243; doivent recevoir 10 tap avant de se détruire et seulement 2 fois pour les vues de tag "1&#8243;.

<pre id="line1"><div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    <span class="co1">#import "FirstViewController.h"</span><br />
    <br />
    <span class="kw1">@implementation</span> FirstViewController<br />
    <br />
    <span class="sy0">-</span> <span class="br0">&#40;</span><span class="kw4">id</span><span class="br0">&#41;</span>initWithNibName<span class="sy0">:</span><span class="br0">&#40;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/"><span class="kw5">NSString</span></a> <span class="sy0">*</span><span class="br0">&#41;</span>nibNameOrNil bundle<span class="sy0">:</span><span class="br0">&#40;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSBundle_Class/"><span class="kw5">NSBundle</span></a> <span class="sy0">*</span><span class="br0">&#41;</span>nibBundleOrNil<br />
    <span class="br0">&#123;</span><br />
        self <span class="sy0">=</span> <span class="br0">&#91;</span>super initWithNibName<span class="sy0">:</span>nibNameOrNil bundle<span class="sy0">:</span>nibBundleOrNil<span class="br0">&#93;</span>;<br />
        <span class="kw1">if</span> <span class="br0">&#40;</span>self<span class="br0">&#41;</span> <span class="br0">&#123;</span><br />
            self.title <span class="sy0">=</span> NSLocalizedString<span class="br0">&#40;</span><span class="co3">@</span><span class="st0">"First"</span>, <span class="co3">@</span><span class="st0">"First"</span><span class="br0">&#41;</span>;<br />
            self.tabBarItem.image <span class="sy0">=</span> <span class="br0">&#91;</span>UIImage imageNamed<span class="sy0">:</span><span class="co3">@</span><span class="st0">"first"</span><span class="br0">&#93;</span>;<br />
    <br />
        <span class="br0">&#125;</span><br />
        <span class="kw1">return</span> self;<br />
    <span class="br0">&#125;</span><br />
    <br />
    <span class="sy0">-</span> <span class="br0">&#40;</span><span class="kw4">void</span><span class="br0">&#41;</span> viewDidLoad<br />
    <span class="br0">&#123;</span><br />
        <span class="br0">&#91;</span>super viewDidLoad<span class="br0">&#93;</span>;<br />
    <br />
        TapTapView <span class="sy0">*</span> tap <span class="sy0">=</span> <span class="br0">&#91;</span><span class="br0">&#91;</span>TapTapView alloc<span class="br0">&#93;</span> initWithFrame<span class="sy0">:</span>CGRectMake<span class="br0">&#40;</span><span class="nu0"></span>, <span class="nu0"></span>, <span class="nu0">100</span>, <span class="nu0">100</span><span class="br0">&#41;</span><span class="br0">&#93;</span>;<br />
        <span class="br0">&#91;</span>tap setBackgroundColor<span class="sy0">:</span><span class="br0">&#91;</span>UIColor redColor<span class="br0">&#93;</span><span class="br0">&#93;</span>;<br />
        <span class="br0">&#91;</span>tap setDelegate<span class="sy0">:</span>self<span class="br0">&#93;</span>;<br />
        <span class="br0">&#91;</span>tap initialize<span class="br0">&#93;</span>;    <br />
    <br />
        <span class="br0">&#91;</span>self.view addSubview<span class="sy0">:</span>tap<span class="br0">&#93;</span>;<br />
    <br />
    <span class="br0">&#125;</span><br />
    <br />
    <span class="sy0">-</span> <span class="br0">&#40;</span>NSUInteger<span class="br0">&#41;</span> taptapNumberTapToClose<span class="sy0">:</span><span class="br0">&#40;</span>TapTapView <span class="sy0">*</span><span class="br0">&#41;</span>view<br />
    <span class="br0">&#123;</span><br />
        <span class="kw1">return</span> <span class="nu0">5</span>;<br />
    <span class="br0">&#125;</span><br />
    <br />
    <span class="sy0">-</span> <span class="br0">&#40;</span><span class="kw4">void</span><span class="br0">&#41;</span> taptapCloseMe<span class="sy0">:</span><span class="br0">&#40;</span>TapTapView <span class="sy0">*</span><span class="br0">&#41;</span>view<br />
    <span class="br0">&#123;</span><br />
        <span class="br0">&#91;</span>view removeFromSuperview<span class="br0">&#93;</span>;<br />
    <span class="br0">&#125;</span>
  </div>
</div>

</pre>

Dans le cas présent, on a simplement dit "au bout de 5 tap, l'objet afficher sera détruit" sans faire de distinction particulière.

Mais que se passe-t'il si dans mon controller je n'ai pas défini ces méthodes ?
La réponse est simple: ça plante.

Eh oui, on essayera d’exécuter une méthode sur un objet qui ne l'implémente pas.
Pour faire des méthodes non obligatoires, par exemple pour définir des méthodes d’initialisations facultatives on va devoir un petit peu modifier TapTapView.

<pre><div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    <span class="kw1">if</span><span class="br0">&#40;</span><span class="br0">&#91;</span>self.delegate respondsToSelector<span class="sy0">:</span><span class="kw1">@selector</span><span class="br0">&#40;</span>taptapNumberTapToClose<span class="sy0">:</span><span class="br0">&#41;</span><span class="br0">&#93;</span><span class="br0">&#41;</span><br />
    <span class="br0">&#123;</span><br />
        gesture.numberOfTapsRequired <span class="sy0">=</span><span class="br0">&#91;</span>self.delegate taptapNumberTapToClose<span class="sy0">:</span>self<span class="br0">&#93;</span>;<br />
    <span class="br0">&#125;</span><br />
    <span class="kw1">else</span><br />
    <span class="br0">&#123;</span><br />
        gesture.numberOfTapsRequired <span class="sy0">=</span> <span class="nu0">2</span>;<br />
    <span class="br0">&#125;</span>
  </div>
</div>

</pre>

# Conclusion

Utiliser le système de délégation est fort pratique, voire encouragé par l'utilisation massive qu'il y a dans UIKit, mais peut se révéler fort contraignant quand on commence à chainer les appels de méthodes pour faire remonter une information à un niveau supérieur.

En effet ici on remonte l'info d'un niveau, mais pour faire remonter encore plus haut on est vite tenté de faire un delegate "passerelle" à chaque passage. Et là bonjour le conflit de nom ou autre.

En général quand on a besoin de faire remonter plus haut c'est que plusieurs éléments à l'écran et votre super singleton de gestion de l'application / jeux veulent l'info en même temps. Dans ce cas le système de délégation arrive vite à ses limites. Heureusement, le système de délégation n'est pas le seul moyen pour échanger de l'information, il y a aussi [le système de notification][1], mais ça, c'est un autre article <img src="http://old-blog.elao.dev/wp-includes/images/smilies/icon_smile.gif" alt="icon smile iPhone   Delegation" class="wp-smiley" title="iPhone   Delegation" />







 [1]: http://developer.apple.com/library/ios/#DOCUMENTATION/Cocoa/Reference/Foundation/Classes/NSNotificationCenter_Class/Reference/Reference.html