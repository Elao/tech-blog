
Dans [l'article précédent][1], nous avons vu comment utiliser le système de délégation permettant la communication sur un niveau.

Quand on veut signaler à l'ensemble de l'application une action qui peut potentiellement engendrer diverses opérations (mettre à jour une vue, modifier la base de données locale, provoquer une requête HTTP, etc...), le système par délégation ne suffit pas.

La fausse bonne pratique est de ramener tout ces traitements à la classe mère `AppDelegate`, et comme celle-ci connait pas mal de monde, elle déclenchera des appels sur les objets adéquates.

```
AppDelegate *appDelegate =  (AppDelegate *)[[UIApplication sharedApplication] delegate];
[appDelegate coucouToi];
```

La conséquence est d'avoir une classe fourre-tout, un point d'entrée pour tout et surtout n'importe quoi.

### Le système de notification

Le principe est simple et pour bien le comprendre il faut le voir avec l'image du haut-parleur de certains villages en période estivale "Mesdames, Monsieur, aujourd'hui sur la place du marché à partir de 14h, vente de moules fraiches"

Ceux qui sont attentifs recevront le message, les autres non. Et s’il n’y a personne qui écoute, tant pis.

Concrètement, ça peut servir pour prévenir à toute l'application qu'un évènement "important" s'est déroulé comme une requête http qui finit, une erreur à remonter à l'utilisateur, ou autres.

=> L'idée derrière est que le système de notification permet de faire de l'évènementiel comme en Flash ou autre. On peut envoyer des messages - event, certains objets sont "écouteurs", ils peuvent aussi décider de ne plus écouter certains types de messages.

### Let's code

**Pour poster un message**

```
[[NSNotificationCenter defaultCenter] postNotificationName:@"LE_NOM_DE_MON_EVENT" object:UnObjetOuNil];
```

**Pour se déclarer "écouteur"**

```
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(onEventXX:) name:@"LE_NOM_DE_MON_EVENT" object:nil];
```

**Du coup, on doit implémenter la méthode `onEventXX`**

```
- (void) onEventXX:(NSNotification *)notification
{
    id unObjetOuNil = notification.object;
}
```

**Pour s'enlever de la file des écouteurs, c'est simple aussi**

```
[[NSNotificationCenter defaultCenter] removeObserver:self forKeyPath:@"LE_NOM_DE_MON_EVENT"];
```

### Important à savoir

Les messages sont envoyés dans une file d'attente où l'ordre d'émission / réception des messages n'est pas garanti. On peut émettre une priorité sur l'envoi, mais à la réception on peut recevoir les messages dans le désordre.

L'utilisation de ce système est assez dur à debuger, comme tout le monde peut s’enregistrer comme "écouteurs", et chacun peut émettre à la réception d'un message, ça peut être dur à s'y retrouver quand on n'est pas habitué à l'évènementiel.

Et aussi, utilisez [des constantes ][2]pour le nom des notifications, ici on a copié/collé les strings, mais ce n’est pas évident pour s'y retrouver.

 [1]: http://www.elao.com/blog/iphone/iphone-delegation.html "iPhone – Delegation"
 [2]: http://stackoverflow.com/questions/538996/constants-in-objective-c