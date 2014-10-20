Nous avons déjà évoqué Three20 comme étant un framework iPhone qui propose des fonctionnalités intéressantes pour la navigation (<a href="/blog/frameworks-iphone.html">via des URL pour rappel</a>), aujourd'hui nous allons nous intéresser à son composant pour créer un menu horizontal avec un nombre non restreint de boutons. Ce genre de menu, nous pouvons en voir sur l'application de l'<a href="http://itunes.apple.com/fr/app/lequipe-fr/id318133983?mt=8" target="_blank">Equipe.fr</a> ou bien même celle d'<a href="http://itunes.apple.com/fr/app/elao/id375943180?mt=8" target="_blank">ELAO</a>

<img title="menu" src="/blog/medias/three20-menu-tttabstrip/menu.png" alt="menu 200x300 Three20 pour créer un menu intelligent avec TTTabStrip" width="200" height="300" />

Nous pouvons voir que le menu continue sur sa droite ; lorsqu'on le survole, les éléments se déplacent horizontalement, les flèches "gauche /droite" de défilement s'ajoutent et disparaissent automatiquement et il y a un état sélectionné sur les items du menu.

### Mise en œuvre

Tout d'abord, il nous faut un projet Three20 vierge. Pour cela, il faut juste suivre les instructions de cette page <a href="http://three20.info/setup/templates" target="_blank">http://three20.info/setup/templates</a>

Ensuite, dans ce projet, j'ai crée un MainController et rajouté une route dans three20 pour lancer l'application sur ce contrôleur.

```
- (void)applicationDidFinishLaunching:(UIApplication *)application {
    TTNavigator* navigator = [TTNavigator navigator];
    navigator.persistenceMode = TTNavigatorPersistenceModeAll;
    TTURLMap* map = navigator.URLMap;
    [map from:@"*" toViewController:[TTWebController class]];
    [map from:@"tt://main" toSharedViewController:[MainController class]];

    if (![navigator restoreViewControllers]) {
        [navigator openURLAction:[TTURLAction actionWithURLPath:@"tt://main"]];
    }
}
```

Pour l'exemple, je veux 10 items et lorsque je clique sur l'un d'eux, un label se modifie. Je définis dans mon contrôleur un objet de type menu et un label :

```
@interface MainController : TTViewController &amp;lt;TTTabDelegate&amp;gt;
{
    TTTabStrip * _tabBar;
    UILabel * label;
}
```

TTTabStrip est notre objet menu, nous allons donc l'instancier et l'ajouter juste en dessous de notre barre de navigation.

```
-(void)viewDidLoad
{
    self.title = @"Menu Sample";

    _tabBar = [[TTTabStrip alloc] initWithFrame:CGRectMake(0, 0, 320, 41)];
    _tabBar.tabItems = [NSArray arrayWithObjects:
    [[[TTTabItem alloc] initWithTitle:@"Menu 1"] autorelease],
    [[[TTTabItem alloc] initWithTitle:@"Menu 2"] autorelease],
    [[[TTTabItem alloc] initWithTitle:@"Menu 3"] autorelease],
    [[[TTTabItem alloc] initWithTitle:@"Menu 4"] autorelease],
    [[[TTTabItem alloc] initWithTitle:@"Menu 5"] autorelease],
    [[[TTTabItem alloc] initWithTitle:@"Menu 6"] autorelease],
    [[[TTTabItem alloc] initWithTitle:@"Menu 7"] autorelease],
    [[[TTTabItem alloc] initWithTitle:@"Menu 8"] autorelease],
    [[[TTTabItem alloc] initWithTitle:@"Menu 9"] autorelease],
    nil];
    _tabBar.delegate = self;
    _tabBar.selectedTabIndex = 0;

    [self.view addSubview:_tabBar];

    label = [[UILabel alloc] initWithFrame:CGRectMake(0, 150, 320, 50)];
    label.text = @"default text";

    label.font = [UIFont fontWithName:@"Arial" size:24];
    label.textAlignment = UITextAlignmentCenter;

    // First Menu is clicked
    [_tabBar setSelectedTabIndex:0];

    [self.view addSubview:label];
}
```

Maintenant, il ne reste plus qu’à récupérer l’évènement « l’utilisateur a appuyé sur un bouton ». Pour se faire, vous aurez remarqué que mon MainController doit implémenter les méthodes du protocole TTTabDelegate (soit dans sa déclaration "@interface MainController : TTViewController **TTTabDelegate**", soit dans la création de l’objet _tabbar (_tabbar.delegate = self).

Le protocole TTTabDelegate a besoin d'une méthode :

```
- (void)tabBar:(TTTabBar*)tabBar tabSelected:(NSInteger)selectedIndex
{
    label.text = [NSString stringWithFormat:@"Menu %i",selectedIndex];

    switch (selectedIndex) {
        case 0:
            label.text = @"Nice the first Menu";
            break;
        case 1:
            label.text = @"Amazing the second Menu";
            break;
    }
}
```

Et le tour est joué ! Pour les plus pressés, voici le code de cet exemple aux couleurs d'<a href="http://www.elao.com" target="_blank">elao</a>, à [télécharger ici ][2]

[2]: /blog/medias/three20-menu-tttabstrip/elMenuSample.zip