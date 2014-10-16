
En tant que développeur iOS débutant, on utilise sans trop faire attention le système dit de délégation. C'est un système central, que l'on est obligé d'avoir utilisé au moins une fois, par exemple à travers la mise en place d'une liste d'éléments comme UITableViewController. Cet article a pour but d'expliquer comment ça marche, en vue de créer nos propres "Delegate".

### Utilisation classique sur une `UITableView`

Quand on débute, on ne jure souvent que par Interface Builder et les tutoriels trouvés à gauche à droite pour afficher une liste de résultats. Sommairement, l'idée est de placer une `UITableView` sur notre vue courante, puis de l'associer via les outlets `dataSource` et `delegate` à notre contrôleur (file owner).

Après ça, on dit à notre contrôleur "Voici mes éléments, et tu les afficheras de telle façon".

```
#import &lt;UIKit/UIKit.h&gt;

@interface SecondViewController : UIViewController ‹UITableViewDelegate, UITableViewDataSource›
{
    NSMutableArray * items;
}

@end
```

```
#import "SecondViewController.h"

@implementation SecondViewController
- (id)initWithNibName:(NSString *)nibNameOrNil bundle:(NSBundle *)nibBundleOrNil
{
    self = [super initWithNibName:nibNameOrNil bundle:nibBundleOrNil];
    if (self) {
        self.title = NSLocalizedString(@"Second", @"Second");
        self.tabBarItem.image = [UIImage imageNamed:@"second"];

        // Mes éléments
        items = [[NSMutableArray alloc] initWithObjects:@"item 1", @"item 2", @"item 3", nil];

    }
    return self;
}

#pragma mark - UITableViewDelegate

- (void) tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath
{
    NSString * labelCell = [items objectAtIndex:indexPath.row];
    [[[UIAlertView alloc] initWithTitle:@"Bravo" message:[NSString stringWithFormat:@" tu as cliqué sur l'élément %@", labelCell] delegate:nil cancelButtonTitle:@"Fermer" otherButtonTitles:nil, nil] show];

}

#pragma mark - UITableViewDataSource

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section
{
    return [items count];
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    static NSString *CellIdentifier = @"Cell";

    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:CellIdentifier];
    if (cell == nil) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:CellIdentifier];
    }

    // Configuration de la cellule
    NSString * labelCell = [items objectAtIndex:indexPath.row];
    [cell.textLabel setText:labelCell];
    return cell;
}

- (NSInteger) tableView:(UITableView *)tableView sectionForSectionIndexTitle:(NSString *)title atIndex:(NSInteger)index
{
    return 1;
}

@end
```

**Une autre façon de faire, sans Interface Builder, est d'instancier et lier notre `UITableView` par nous même :**

```
- (id)initWithNibName:(NSString *)nibNameOrNil bundle:(NSBundle *)nibBundleOrNil
{
    self = [super initWithNibName:nibNameOrNil bundle:nibBundleOrNil];
    if (self) {
        self.title = NSLocalizedString(@"First", @"First");
        self.tabBarItem.image = [UIImage imageNamed:@"first"];

        items = [[NSMutableArray alloc] initWithObjects:@"item 1", @"item 2", @"item 3", nil];

        mytableView = [[UITableView alloc] initWithFrame:self.view.frame];
        [mytableView setDelegate:self];
        [mytableView setDataSource:self];
        [self.view addSubview:mytableView];

    }
    return self;
}
```

Et de cette façon-là, on entre dans le vif du sujet !
Interface Builder nous masque cette étape avec ses liaisons "user friendly", mais le fait de dire à la tableView "Bonjour, ton dataSource et ton delegate c'est moi" c'est tout le principe du fonctionnement par délégation.

À ce stade, la tableView n'a aucune visibilité sur le contrôleur, pourtant elle va afficher / se configurer grâce à lui.

### Fonctionnement général

Le système de délégation permet à un objet A de dire à un objet B "Hey toi, implémente cette méthode, je vais l'appeler et tu as intérêt à répondre".

L'objet A ne saura pas le type de l'objet B, il va juste tenter d’exécuter une méthode sur un objet "générique". On dit à ce moment-là que l'objet A va exécuter du code sur son `Delegate`.

**Il devient donc nécessaire d'implémenter certaines méthodes "obligatoire" comme**

```
- (void) tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath;
- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section;
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath;
- (NSInteger) tableView:(UITableView *)tableView sectionForSectionIndexTitle:(NSString *)title atIndex:(NSInteger)index;
```

On dit alors que la classe A implémente les méthodes du protocole de B. Certaines méthodes sont à implémenter sinon l'application crash, d'autres non.

Pour que xCode soit sympathique avec nous, il est conseillé de se déclarer comme apte à supporter ce protocole, d’où la ligne

```
@interface SecondViewController : UIViewController ‹UITableViewDelegate, UITableViewDataSource›
```

En faisant ça, xCode nous signalera par un warning si l’on oublie d'implémenter une méthode du protocole et va à ce moment-là proposer l'auto complétion pour les rajouter. Il serait dommage d'oublier cette étape.

### Création d'un delegate

Question : Mais comment a fait l'objet de type `UITableView` pour nous forcer à implémenter des méthodes pour l'utiliser ?
Réponse: il a prévu / écrit un "protocole" et en typant un objet avec ce protocole, il peut exécuter ses méthodes sur l'objet. À aucun moment, `UITableView` n'implémente lui même ces méthodes. Il va supposer qu'on l'a fait.

#### Let's code

Imaginons que l'on veuille créer un petit jeu de "TapTap je te tue". Pour cela on doit mettre en place des vues (qui représentent un ennemi par exemple) où lorsqu'on appuie X fois dessus, elles puissent dire à la vue principale "Hey c'est bon pour moi, on m'a assez appuyé dessus ! Tu peux me supprimer, calculer ton score ou autre"

Pour cela nous allons créer une classe qui hérite de `UIView`, lui associer un protocole et un objet `delegate` puis nous allons faire des appels à cette méthode.

```
//  TapTapView.h MyCompanyName__. All rights reserved.

#import ‹UIKit/UIKit.h›

// Je vais utiliser une référence à la classe dans le protocole avant de l'utiliser
@class TapTapView;

// Le protocole est la
@protocol TapTapViewDelegate ‹NSObject›
    - (NSUInteger) taptapNumberTapToClose:(TapTapView *)view;
    - (void) taptapCloseMe:(TapTapView *)view;
@end

// Ma classe
@interface TapTapView : UIView

- (void) initialize;

// Mon delegate
@property (nonatomic,assign) id ‹TapTapViewDelegate› delegate;

@end
```

*   Une bonne pratique est de préfixer les méthodes du protocole.
*   Une bonne pratique est de toujours envoyer en référence dans les méthodes du protocole l'objet qui les a déclenchés.
*   Une bonne pratique est d'appeler son objet de délégation `delegate` du moment qu'on en manipule qu'un et que les méthodes du protocoles servent à la logique d'un seul processus

**Voyons maintenant son implémentation**

```
#import "TapTapView.h"

@implementation TapTapView

@synthesize delegate;

- (void) initialize
{
    UITapGestureRecognizer* gesture = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(onTapped:)];

    // J'attend un retour du delegate
    gesture.numberOfTapsRequired = [self.delegate taptapNumberTapToClose:self];
    gesture.numberOfTouchesRequired = 1;

    [self addGestureRecognizer: gesture];

}

- (void) onTapped:(id)sender
{
    // Je signale que la gesture est reconnue
    [self.delegate taptapCloseMe:self];
}

@end
```

Et enfin son utilisation.

Ici j'ai prévu que la classe A doit avoir implémenté la méthode `taptapNumberTapToClose` pour configurer le nombre de touches avant la destruction.

Du coup, en passant en paramètre la vue à la méthode taptapNumberToClose (cf bonne pratique) et en utilisant la notion de tag de toute vue, je peux faire en sorte que toutes les vues ayant un tag de "1664&#8243; doivent recevoir 10 tap avant de se détruire et seulement 2 fois pour les vues de tag "1&#8243;.

```
#import "FirstViewController.h"

@implementation FirstViewController

- (id)initWithNibName:(NSString *)nibNameOrNil bundle:(NSBundle *)nibBundleOrNil
{
    self = [super initWithNibName:nibNameOrNil bundle:nibBundleOrNil];
    if (self) {
        self.title = NSLocalizedString(@"First", @"First");
        self.tabBarItem.image = [UIImage imageNamed:@"first"];

    }
    return self;
}

- (void) viewDidLoad
{
    [super viewDidLoad];

    TapTapView * tap = [[TapTapView alloc] initWithFrame:CGRectMake(0, 0, 100, 100)];
    [tap setBackgroundColor:[UIColor redColor]];
    [tap setDelegate:self];
    [tap initialize];

    [self.view addSubview:tap];

}

- (NSUInteger) taptapNumberTapToClose:(TapTapView *)view
{
    return 5;
}

- (void) taptapCloseMe:(TapTapView *)view
{
    [view removeFromSuperview];
}
```

Dans le cas présent, on a simplement dit "au bout de 5 tap, l'objet afficher sera détruit" sans faire de distinction particulière.

Mais que se passe-t'il si dans mon controller je n'ai pas défini ces méthodes ?
La réponse est simple : ça plante.

Eh oui, on essayera d’exécuter une méthode sur un objet qui ne l'implémente pas.
Pour faire des méthodes non obligatoires, par exemple pour définir des méthodes d’initialisations facultatives on va devoir un petit peu modifier `TapTapView`.

```
if([self.delegate respondsToSelector:@selector(taptapNumberTapToClose:)])
{
    gesture.numberOfTapsRequired =[self.delegate taptapNumberTapToClose:self];
}
else
{
    gesture.numberOfTapsRequired = 2;
}
```

### Conclusion

Utiliser le système de délégation est fort pratique, voire encouragé par l'utilisation massive qu'il y a dans UIKit, mais peut se révéler fort contraignant quand on commence à chainer les appels de méthodes pour faire remonter une information à un niveau supérieur.

En effet ici on remonte l'info d'un niveau, mais pour faire remonter encore plus haut on est vite tenté de faire un delegate "passerelle" à chaque passage. Et là bonjour le conflit de nom ou autre.

En général quand on a besoin de faire remonter plus haut c'est que plusieurs éléments à l'écran et votre super singleton de gestion de l'application / jeux veulent l'info en même temps. Dans ce cas le système de délégation arrive vite à ses limites. Heureusement, le système de délégation n'est pas le seul moyen pour échanger de l'information, il y a aussi [le système de notification][1], mais ça, c'est un autre article...

 [1]: http://developer.apple.com/library/ios/#DOCUMENTATION/Cocoa/Reference/Foundation/Classes/NSNotificationCenter_Class/Reference/Reference.html