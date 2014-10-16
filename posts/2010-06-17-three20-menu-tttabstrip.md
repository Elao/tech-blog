
<p style="text-align: justify;">
  Nous avons déjà évoqué Three20 comme étant un framework iPhone qui propose des fonctionnalités intéressantes pour la navigation (<a href="/blog/iphone/frameworks-iphone.html">via des URL pour rappel</a>), aujourd'hui nous allons nous intéresser à son composant pour créer un menu horizontal avec un nombre non restreint de boutons. Ce genre de menu, nous pouvons en voir sur l'application de l'<a href="http://itunes.apple.com/fr/app/lequipe-fr/id318133983?mt=8" target="_blank">Equipe.fr</a> ou bien même celle d'<a href="http://itunes.apple.com/fr/app/elao/id375943180?mt=8" target="_blank">ELAO</a>
</p>

[<img class="alignleft" title="menu" src="/blog/wp-content/uploads/2010/06/menu-200x300.png" alt="menu 200x300 Three20 pour créer un menu intelligent avec TTTabStrip" width="200" height="300" />][1]

<div>
  <table>
    <tr>
      <td style="text-align: justify;">
        Nous pouvons voir que le menu continue sur sa droite ; lorsqu'on le survole, les éléments se déplacent horizontalement, les flèches "gauche /droite" de défilement s'ajoutent et disparaissent automatiquement et il y a un état sélectionné sur les items du menu.</p> <h2>
          Mise en œuvre
        </h2>

        <p>
          Tout d'abord, il nous faut un projet Three20 vierge. Pour cela, il faut juste suivre les instructions de cette page <a href="http://three20.info/setup/templates" target="_blank">http://three20.info/setup/templates</a>
        </p>

        <p>
          Ensuite, dans ce projet, j'ai crée un MainController et rajouté une route dans three20 pour lancer l'application sur ce contrôleur.
        </p>

        <div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
          <div class="objc codecolorer">
            <span class="sy0">-</span> <span class="br0">&#40;</span><span class="kw4">void</span><span class="br0">&#41;</span>applicationDidFinishLaunching<span class="sy0">:</span><span class="br0">&#40;</span>UIApplication <span class="sy0">*</span><span class="br0">&#41;</span>application <span class="br0">&#123;</span><br /> TTNavigator<span class="sy0">*</span> navigator <span class="sy0">=</span> <span class="br0">&#91;</span>TTNavigator navigator<span class="br0">&#93;</span>;<br /> navigator.persistenceMode <span class="sy0">=</span> TTNavigatorPersistenceModeAll;<br /> TTURLMap<span class="sy0">*</span> map <span class="sy0">=</span> navigator.URLMap;<br /> <span class="br0">&#91;</span>map from<span class="sy0">:</span><span class="co3">@</span><span class="st0">"*"</span> toViewController<span class="sy0">:</span><span class="br0">&#91;</span>TTWebController class<span class="br0">&#93;</span><span class="br0">&#93;</span>;<br /> <span class="br0">&#91;</span>map from<span class="sy0">:</span><span class="co3">@</span><span class="st0">"tt://main"</span> toSharedViewController<span class="sy0">:</span><span class="br0">&#91;</span>MainController class<span class="br0">&#93;</span><span class="br0">&#93;</span>;<br /> <span class="kw1">if</span> <span class="br0">&#40;</span><span class="sy0">!</span><span class="br0">&#91;</span>navigator restoreViewControllers<span class="br0">&#93;</span><span class="br0">&#41;</span> <span class="br0">&#123;</span><br /> <span class="br0">&#91;</span>navigator openURLAction<span class="sy0">:</span><span class="br0">&#91;</span>TTURLAction actionWithURLPath<span class="sy0">:</span><span class="co3">@</span><span class="st0">"tt://main"</span><span class="br0">&#93;</span><span class="br0">&#93;</span>;<br /> <span class="br0">&#125;</span><br /> <span class="br0">&#125;</span>
          </div>
        </div>
      </td>
    </tr>
  </table>
</div>

<p style="text-align: justify;">
  Pour l'exemple, je veux 10 items et lorsque je clique sur l'un d'eux, un label se modifie. Je définis dans mon contrôleur un objet de type menu et un label :
</p>

<div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    <span class="kw1">@interface</span> MainController <span class="sy0">:</span> TTViewController <span class="sy0">&</span>amp;lt;TTTabDelegate<span class="sy0">&</span>amp;gt;<br /> <span class="br0">&#123;</span><br /> TTTabStrip <span class="sy0">*</span> _tabBar;<br /> UILabel <span class="sy0">*</span> label;<br /> <span class="br0">&#125;</span>
  </div>
</div>

TTTabStrip est notre objet menu, nous allons donc l'instancier et l'ajouter juste en dessous de notre barre de navigation.

<div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    <span class="sy0">-</span><span class="br0">&#40;</span><span class="kw4">void</span><span class="br0">&#41;</span>viewDidLoad<br /> <span class="br0">&#123;</span><br /> self.title <span class="sy0">=</span> <span class="co3">@</span><span class="st0">"Menu Sample"</span>;<br /> <br /> _tabBar <span class="sy0">=</span> <span class="br0">&#91;</span><span class="br0">&#91;</span>TTTabStrip alloc<span class="br0">&#93;</span> initWithFrame<span class="sy0">:</span>CGRectMake<span class="br0">&#40;</span><span class="nu0"></span>, <span class="nu0"></span>, <span class="nu0">320</span>, <span class="nu0">41</span><span class="br0">&#41;</span><span class="br0">&#93;</span>;<br /> _tabBar.tabItems <span class="sy0">=</span> <span class="br0">&#91;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSArray_Class/"><span class="kw5">NSArray</span></a> arrayWithObjects<span class="sy0">:</span><br /> <span class="br0">&#91;</span><span class="br0">&#91;</span><span class="br0">&#91;</span>TTTabItem alloc<span class="br0">&#93;</span> initWithTitle<span class="sy0">:</span><span class="co3">@</span><span class="st0">"Menu 1"</span><span class="br0">&#93;</span> autorelease<span class="br0">&#93;</span>,<br /> <span class="br0">&#91;</span><span class="br0">&#91;</span><span class="br0">&#91;</span>TTTabItem alloc<span class="br0">&#93;</span> initWithTitle<span class="sy0">:</span><span class="co3">@</span><span class="st0">"Menu 2"</span><span class="br0">&#93;</span> autorelease<span class="br0">&#93;</span>,<br /> <span class="br0">&#91;</span><span class="br0">&#91;</span><span class="br0">&#91;</span>TTTabItem alloc<span class="br0">&#93;</span> initWithTitle<span class="sy0">:</span><span class="co3">@</span><span class="st0">"Menu 3"</span><span class="br0">&#93;</span> autorelease<span class="br0">&#93;</span>,<br /> <span class="br0">&#91;</span><span class="br0">&#91;</span><span class="br0">&#91;</span>TTTabItem alloc<span class="br0">&#93;</span> initWithTitle<span class="sy0">:</span><span class="co3">@</span><span class="st0">"Menu 4"</span><span class="br0">&#93;</span> autorelease<span class="br0">&#93;</span>,<br /> <span class="br0">&#91;</span><span class="br0">&#91;</span><span class="br0">&#91;</span>TTTabItem alloc<span class="br0">&#93;</span> initWithTitle<span class="sy0">:</span><span class="co3">@</span><span class="st0">"Menu 5"</span><span class="br0">&#93;</span> autorelease<span class="br0">&#93;</span>,<br /> <span class="br0">&#91;</span><span class="br0">&#91;</span><span class="br0">&#91;</span>TTTabItem alloc<span class="br0">&#93;</span> initWithTitle<span class="sy0">:</span><span class="co3">@</span><span class="st0">"Menu 6"</span><span class="br0">&#93;</span> autorelease<span class="br0">&#93;</span>,<br /> <span class="br0">&#91;</span><span class="br0">&#91;</span><span class="br0">&#91;</span>TTTabItem alloc<span class="br0">&#93;</span> initWithTitle<span class="sy0">:</span><span class="co3">@</span><span class="st0">"Menu 7"</span><span class="br0">&#93;</span> autorelease<span class="br0">&#93;</span>,<br /> <span class="br0">&#91;</span><span class="br0">&#91;</span><span class="br0">&#91;</span>TTTabItem alloc<span class="br0">&#93;</span> initWithTitle<span class="sy0">:</span><span class="co3">@</span><span class="st0">"Menu 8"</span><span class="br0">&#93;</span> autorelease<span class="br0">&#93;</span>,<br /> <span class="br0">&#91;</span><span class="br0">&#91;</span><span class="br0">&#91;</span>TTTabItem alloc<span class="br0">&#93;</span> initWithTitle<span class="sy0">:</span><span class="co3">@</span><span class="st0">"Menu 9"</span><span class="br0">&#93;</span> autorelease<span class="br0">&#93;</span>,<br /> <span class="kw2">nil</span><span class="br0">&#93;</span>;<br /> _tabBar.delegate <span class="sy0">=</span> self;<br /> _tabBar.selectedTabIndex <span class="sy0">=</span> <span class="nu0"></span>;<br /> <br /> <span class="br0">&#91;</span>self.view addSubview<span class="sy0">:</span>_tabBar<span class="br0">&#93;</span>;<br /> <br /> label <span class="sy0">=</span> <span class="br0">&#91;</span><span class="br0">&#91;</span>UILabel alloc<span class="br0">&#93;</span> initWithFrame<span class="sy0">:</span>CGRectMake<span class="br0">&#40;</span><span class="nu0"></span>, <span class="nu0">150</span>, <span class="nu0">320</span>, <span class="nu0">50</span><span class="br0">&#41;</span><span class="br0">&#93;</span>;<br /> label.text <span class="sy0">=</span> <span class="co3">@</span><span class="st0">"default text"</span>;<br /> <br /> label.font <span class="sy0">=</span> <span class="br0">&#91;</span>UIFont fontWithName<span class="sy0">:</span><span class="co3">@</span><span class="st0">"Arial"</span> size<span class="sy0">:</span><span class="nu0">24</span><span class="br0">&#93;</span>;<br /> label.textAlignment <span class="sy0">=</span> UITextAlignmentCenter;<br /> <br /> <span class="co2">// First Menu is clicked</span><br /> <span class="br0">&#91;</span>_tabBar setSelectedTabIndex<span class="sy0">:</span><span class="nu0"></span><span class="br0">&#93;</span>;<br /> <br /> <span class="br0">&#91;</span>self.view addSubview<span class="sy0">:</span>label<span class="br0">&#93;</span>;<br /> <br /> <span class="br0">&#125;</span>
  </div>
</div>

<p style="text-align: justify;">
  Maintenant, il ne reste plus qu’à récupérer l’évènement « l’utilisateur a appuyé sur un bouton ». Pour se faire, vous aurez remarqué que mon MainController doit implémenter les méthodes du protocole TTTabDelegate (soit dans sa déclaration "@interface MainController : TTViewController <strong>TTTabDelegate</strong>", soit dans la création de l’objet _tabbar (_tabbar.delegate = self).
</p>

Le protocole TTTabDelegate a besoin d'une méthode :

<div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    <span class="sy0">-</span> <span class="br0">&#40;</span><span class="kw4">void</span><span class="br0">&#41;</span>tabBar<span class="sy0">:</span><span class="br0">&#40;</span>TTTabBar<span class="sy0">*</span><span class="br0">&#41;</span>tabBar tabSelected<span class="sy0">:</span><span class="br0">&#40;</span>NSInteger<span class="br0">&#41;</span>selectedIndex<br /> <span class="br0">&#123;</span><br /> label.text <span class="sy0">=</span> <span class="br0">&#91;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/"><span class="kw5">NSString</span></a> stringWithFormat<span class="sy0">:</span><span class="co3">@</span><span class="st0">"Menu %i"</span>,selectedIndex<span class="br0">&#93;</span>;<br /> <br /> <span class="kw1">switch</span> <span class="br0">&#40;</span>selectedIndex<span class="br0">&#41;</span> <span class="br0">&#123;</span><br /> <span class="kw1">case</span> <span class="nu0"></span><span class="sy0">:</span><br /> label.text <span class="sy0">=</span> <span class="co3">@</span><span class="st0">"Nice the first Menu"</span>;<br /> <span class="kw2">break</span>;<br /> <span class="kw1">case</span> <span class="nu0">1</span><span class="sy0">:</span><br /> label.text <span class="sy0">=</span> <span class="co3">@</span><span class="st0">"Amazing the second Menu"</span>;<br /> <span class="kw2">break</span>;<br /> <span class="br0">&#125;</span><br /> <span class="br0">&#125;</span>
  </div>
</div>

Et le tour est joué ! Pour les plus pressés, voici le code de cet exemple aux couleurs d'<a href="http://www.elao.com" target="_blank">elao</a>, à [télécharger ici ][2]

 [1]: /blog/wp-content/uploads/2010/06/menu.png
 [2]: /blog/wp-content/uploads/2010/06/elMenuSample.zip