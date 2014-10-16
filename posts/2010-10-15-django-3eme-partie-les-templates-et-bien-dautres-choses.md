
<p class="standard">
  Bonjour à toutes et à tous,
</p>

<p class="standard">
  Aujourd'hui nous abordons notre troisième volet consacré à <a href="http://www.djangoproject.com/" target="_blank">Django</a> et autant vous le dire tout de suite : nous avons du pain sur la planche !
</p>

<p class="standard">
  En effet, nous allons enrichir le projet initié lors de notre <a href="/blog/python/django-2nde-partie-le-modele-et-ladmin.html" target="_blank">précédent article</a>. Pour mémoire, j'ai choisi de mettre en pratique nos connaissances de Django à travers un cas concret, assez simple pour être didactique mais assez riche pour couvrir un large éventail de fonctionnalités : un site de vente d'articles de sport (ou, soyons honnêtes, plutôt une ébauche de site). Dans l'épisode précédent, nous avons créé deux objets métiers, "<em>Sport</em>" et "<em>Item</em>", et mis en place l'interface permettant de les gérer, grâce à l'Admin de Django (affichage des listes, création, édition, suppression). Aujourd'hui, nous allons construire une page d'accueil dans laquelle nous afficherons une liste des produits disponibles. Histoire de rendre la partie plus intéressante, nous nous fixons les objectifs suivants : la liste pourra faire l'objet d'un tri sur un critère donné, elle devra être paginée et il sera possible de faire des recherches sur des critères assez simples. Du travail en perspective !
</p>

<p class="standard">
  Voici le résultat à atteindre :
</p>

<div style="text-align: center;">
  <img title="Elao - Boutique de sport - Accueil" src="/blog/wp-content/uploads/2010/10/elao_shop_index_vfinale.png" alt="elao shop index vfinale Django (3ème partie) : les templates, et bien dautres choses ..." border="1" />
</div>

<p class="standard">
  Tout au long de cet article, nous allons travailler principalement sur trois fichiers : la template globale (<em>base.html</em>), la template correspondant à la page d'accueil (<em>index.html</em>) et le fichier <em>shop/views.py</em>. A mesure que nous avancerons en fonctionnalités, nous compléterons le code de chacun de ces fichiers. Toutefois, si à un moment ou un autre, vous êtiez perdu(e)s, voici une archive contenant le projet obtenu lorsque toutes les étapes décrites dans cet article ont été réalisées (codes, feuilles de style, images, fixtures …) : <a href="http://www.elao.org/wp-content/uploads/2010/10/django_shop_v1.zip">Elao shop - code source - part 3</a>. Quoi qu'il en soit, pour tirer pleinement profit de cet article, je vous encourage plutôt à suivre chacune des étapes décrites ci-après, à ne télécharger cette archive que pour récupérer les fixtures, les styles et les images, et à ne consulter le code qu'en cas de difficulté à "reconstituer le puzzle". Bien entendu, le code fourni ne sert qu'à des fins didactiques et ne devrait en aucun cas être utilisé tel quel en production. Ce code est sans nul doute perfectible à bien des égards, et toute critique constructive pour l'améliorer est évidemment la bienvenue.
</p>

<p class="standard">
  Vous êtes prêts ? C'est parti ! Nous allons donc nous atteler à la création de notre page d'accueil, assez basique dans sa forme et son contenu dans un premier temps.
</p>

### Première étape : création d'un jeu de données

<p class="standard">
  Vouloir afficher des articles sur un site de vente, c'est bien, encore faut-il pour cela que nous disposions d'un jeu de données. Et c'est là qu'entrent en scène les <em>fixtures</em>. Nous allons donc voir une méthode permettant de créer un jeu de fixtures, puis alimenter la base de données avec les données ainsi obtenues (pour ceux qui le souhaitent, un exemple de fixtures est disponible dans le répertoire <em>shop/fixtures</em> de <a href="/blog/wp-content/uploads/2010/10/django_shop_v1.zip">l'archive</a> proposée au début de cet article. Copier ce fichier dans le répertoire <em>elao/shop/fixtures</em> de votre propre projet. Ces fixtures ont été obtenus en appliquant la méthode décrite ci-après).
</p>

<p class="standard">
  Il faut savoir que Django gère les fixtures dans plusieurs formats : XML, YAML et JSON. Nous allons opter ici, de manière assez arbitraire, pour le format XML, qui n'est pas nécessairement mon format préféré, mais il fallait bien en choisir un … J'aurais tendance à considérer que le format YML est le plus lisible des trois (cela reste une opinion) mais …
</p>

> <div class="aparte">
>   Pour générer des fixtures au format YAML, <a href="http://www.pyyaml.org/" target="_blank">PyYAML</a> doit être installé !
> </div>

<p class="standard">
  La manière la plus simple pour générer un jeu de données est dans un premier temps de créer en base de données un enregistrement pour chaque classe, à l'aide de l'Admin de Django, puis de lancer la sous-commande <em>dumpdata</em> qui va générer un fichier exemple de fixtures à partir des données que nous aurons enregistrées. Si vous souhaitez créer vos propres fixtures, je vous laisse donc le temps d'aller créer une instance de la classe "<em>shop.Sport</em>" et une autre de la classe "sport.Item" via l'Admin … Ca y est ? C'est fait ? Nous pouvons donc générer un squelette de fixtures qui va nous servir de base pour créer des données supplémentaires :
</p>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    python manage.py dumpdata <span class="re5">--indent</span>=<span class="nu0">2</span> <span class="re5">--format</span>=xml shop
  </div>
</div>

<p class="standard">
  Voici un exemple de data qui s'affiche à l'écran après avoir lancé cette commande :
</p>

<div class="codecolorer-container xml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="xml codecolorer">
    <span class="sc-1"><!--?xml version="1.0" encoding="utf-8"?--></span>
  </div>
</div>

<p class="standard">
  En nous basant sur cet exemple, nous allons pouvoir créer des données supplémentaires que nous enregistrerons dans le fichier <em>elao/shop/fixtures/initial_data.xml</em>. Si vous ne souhaitez pas créer vos propres fixtures, je rappelle qu'un fichier XML de fixtures est disponible dans l'archive. Attention, si vous êtes un amateur de foot éclairé, je vous déconseille d'utiliser ce fichier XML, dont le contenu pourrait heurter la sensibilité de certains footballeurs … Les images associées à nos articles sont également disponibles dans l'archive.
</p>

<p class="standard">
  A présent, nous allons charger les données en base en lançant la commande <em>loaddata</em> :
</p>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
     python manage.py loaddata shop<span class="sy0">/</span>fixtures<span class="sy0">/</span>initial_data.xml
  </div>
</div>

<p class="standard">
  La console devrait vous retourner le résultat suivant :
</p>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    Installing xml fixture <span class="st_h">'shop/fixtures/initial_data'</span> from absolute path.<br /> Installed <span class="nu0">12</span> object<span class="br0">&#40;</span>s<span class="br0">&#41;</span> from <span class="nu0">1</span> fixture<span class="br0">&#40;</span>s<span class="br0">&#41;</span>
  </div>
</div>

<p class="standard">
  Voilà, nous disposons désormais d'un jeu de données qui va nous permettre d'afficher tous les articles sur la page d'accueil de notre site. Nous pouvons donc nous atteler à la rédaction de la page d'accueil. Et pour cela, nous allons définir une template de base (le layout global) dont nos vues hériteront.
</p>

### Création de la page d'accueil et du layout global

<p class="standard">
  Dans un premier temps, nous allons écrire le squelette du code nécessaire à l'affichage d'une page d'accueil (nous nous soucierons ensuite de l'habillage). Comme souvent avec Django, cela signifie :
</p>

1.  Ecrire une route (dans notre cas, l'URL correspondra à la racine du site)
2.  Ecrire une méthode *index* dans le fichier *views.py*
3.  Ecrire la template correspondante

<p class="standard">
  Tout d'abord, la route permettant d'appeler la méthode <em>index</em> du module <em>views</em> de notre application <em>shop</em> lorsque l'URL invoquée correspond à la racine de notre site ...
</p>

<div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="python codecolorer">
    <span class="co1"># elao/urls.py</span><br /> <span class="co1"># …</span><br /> urlpatterns <span class="sy0">=</span> patterns<span class="br0">&#40;</span><span class="st0">''</span><span class="sy0">,</span><br /> <span class="br0">&#40;</span>r<span class="st0">'^$'</span><span class="sy0">,</span> <span class="st0">'elao.shop.views.index'</span><span class="br0">&#41;</span><span class="sy0">,</span><br /> <span class="co1"># ...</span>
  </div>
</div>

<p class="standard">
  … ensuite la méthode <em>index</em> ...
</p>

<div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="python codecolorer">
    <span class="co1"># elao/shop/views.py</span><br /> <span class="kw1">from</span> django.<span class="me1">shortcuts</span> <span class="kw1">import</span> render_to_response<br /> <br /> <span class="kw1">def</span> index<span class="br0">&#40;</span>request<span class="br0">&#41;</span>:<br /> <span class="kw1">return</span> render_to_response<span class="br0">&#40;</span><span class="st0">'index.html'</span><span class="br0">&#41;</span>
  </div>
</div>

<p class="standard">
  … et pour finir, la template <em>index.html</em>
</p>

<div class="codecolorer-container html4strict vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="html4strict codecolorer">
    <span class="sc-1"><!-- elao/templates/index.html --></span><br /> {% block content %}<br /> <span class="sc2"><<a href="http://december.com/html/4/element/h1.html"><span class="kw2">h1</span></a>></span>Catalogue des articles<span class="sc2"><<span class="sy0">/</span><a href="http://december.com/html/4/element/h1.html"><span class="kw2">h1</span></a>></span><br /> Ici, nous allons afficher la liste des articles disponibles.<br /> <br /> {% endblock %}
  </div>
</div>

<p class="standard">
  A présent, si vous vous rendez sur la page <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a>, vous verrez s'afficher une magnifique page, dont la sobriété graphique a de quoi refiler le cafard à tous les designers de la planète … Même le développeur indécrottable que je suis est à peine satisfait du rendu, c'est vous dire …
</p>

<p class="standard">
  Nous allons tenter d'améliorer tout cela, mais le but de cet article n'étant pas de révéler notre sensibilité artistique, mais bien d'étudier le framework Django, nous allons voir comment une template de Django peut hériter d'une template "mère", laquelle va servir de layout pour ses "filles". Je vous rassure, nous en profiterons au passage pour améliorer le rendu à l'aide d'une feuille de style … En ce qui me concerne, je ne suis absolument pas doué pour le graphisme, mais même lorsque je développe, j'aime bien améliorer un peu le rendu global avec quelques styles basiques qui rendent tout de suite le travail plus agréable. Nous allons donc créer dans un premier temps cette template "mère", que nous appellerons <em>base.html</em> (c'est une convention adoptée par la plupart des développeurs Django, donc, autant nous y conformer !) et que nous placerons à la racine du répertoire <em>templates</em> :
</p>

<div class="codecolorer-container html4strict vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="html4strict codecolorer">
    <span class="sc-1"><!-- elao/templates/base.html --></span><br /> <span class="sc2"><<a href="http://december.com/html/4/element/div.html"><span class="kw2">div</span></a> <span class="kw3">class</span><span class="sy0">=</span><span class="st0">"container"</span> <span class="kw3">style</span><span class="sy0">=</span><span class="st0">"margin: 10px auto; width: 1100px; background-color: white; padding: 15px; border: 2px outset lightgrey;"</span>></span><br /> <span class="sc2"><<a href="http://december.com/html/4/element/div.html"><span class="kw2">div</span></a> <span class="kw3">id</span><span class="sy0">=</span><span class="st0">"header"</span>></span><br /> <span class="sc2"><<a href="http://december.com/html/4/element/div.html"><span class="kw2">div</span></a> <span class="kw3">id</span><span class="sy0">=</span><span class="st0">"header-logo"</span> <span class="kw3">style</span><span class="sy0">=</span><span class="st0">"float: left;"</span>><<a href="http://december.com/html/4/element/a.html"><span class="kw2">a</span></a> <span class="kw3">href</span><span class="sy0">=</span><span class="st0">"/"</span>></span><br /> <span class="sc2"><<a href="http://december.com/html/4/element/img.html"><span class="kw2">img</span></a> <span class="kw3">style</span><span class="sy0">=</span><span class="st0">"margin-right: 10px;"</span> <span class="kw3">title</span><span class="sy0">=</span><span class="st0">"Django"</span> <span class="kw3">src</span><span class="sy0">=</span><span class="st0">"/site-media/pictures/django_logo.gif"</span> <span class="kw3">alt</span><span class="sy0">=</span><span class="st0">"Django"</span> <span class="kw3">align</span><span class="sy0">=</span><span class="st0">"absmiddle"</span> <span class="sy0">/</span>></span><br /> <span class="sc2"><<a href="http://december.com/html/4/element/span.html"><span class="kw2">span</span></a> <span class="kw3">style</span><span class="sy0">=</span><span class="st0">"font-size: 26px;"</span>></span>La boutique de sport d'ELAO<span class="sc2"><<span class="sy0">/</span><a href="http://december.com/html/4/element/span.html"><span class="kw2">span</span></a>></span><br /> <span class="sc2"><<span class="sy0">/</span><a href="http://december.com/html/4/element/a.html"><span class="kw2">a</span></a>><<span class="sy0">/</span><a href="http://december.com/html/4/element/div.html"><span class="kw2">div</span></a>></span><br /> <span class="sc2"><<a href="http://december.com/html/4/element/div.html"><span class="kw2">div</span></a> <span class="kw3">id</span><span class="sy0">=</span><span class="st0">"header-navigation"</span> <span class="kw3">style</span><span class="sy0">=</span><span class="st0">"float: right;"</span>><<a href="http://december.com/html/4/element/a.html"><span class="kw2">a</span></a> <span class="kw3">href</span><span class="sy0">=</span><span class="st0">"/"</span>></span>Home<span class="sc2"><<span class="sy0">/</span><a href="http://december.com/html/4/element/a.html"><span class="kw2">a</span></a>></span> | <span class="sc2"><<a href="http://december.com/html/4/element/a.html"><span class="kw2">a</span></a> <span class="kw3">href</span><span class="sy0">=</span><span class="st0">"/admin"</span>></span>Admin<span class="sc2"><<span class="sy0">/</span><a href="http://december.com/html/4/element/a.html"><span class="kw2">a</span></a>><<span class="sy0">/</span><a href="http://december.com/html/4/element/div.html"><span class="kw2">div</span></a>></span><br /> <span class="sc2"><<span class="sy0">/</span><a href="http://december.com/html/4/element/div.html"><span class="kw2">div</span></a>></span><br /> {% block content %}{% endblock %}<br /> <br /> <span class="sc2"><<span class="sy0">/</span><a href="http://december.com/html/4/element/div.html"><span class="kw2">div</span></a>></span><br /> <span class="sc1"> </span>
  </div>
</div>

<p class="standard">
  Pas grand chose à signaler ici, il s'agit d'un fichier HTML assez classique. Vous remarquerez que j'ai inclus un lien vers l'Admin afin d'y accéder rapidement, ce qui n'est pas nécessairement une pratique recommandable dans la mesure où nous développons une page destinée au grand public. Mais nous verrons à l'occasion d'un prochain article comment masquer ce lien en fonction du statut de l'utilisateur courant. Noter également comment nous incluons la feuille de style "<em>style.css</em>" : le chemin de la feuille de style fait référence à une route <em>site-media</em> que nous avions créée dans l'article précédent, et qui est destinée à servir les fichiers statiques. Il vous faudra donc enregistrer vos styles dans un fichier nommé <em>style.css</em> placé à la racine de votre répertoire <em>media</em>. Pour ceux qui le souhaitent, le fichier CSS que j'ai utilisé est disponible dans l'archive téléchargeable que j'ai mentionnée au début de cet article. Cette feuille de style recourt à quelques images de poids très léger également disponibles dans l'archive. Enfin, notez surtout la présence du bloc <em>{% block content %}{% endblock %}</em> : toutes les pages qui hériteront de la page <em>base.html</em> verront le contenu de leur bloc <em>content</em> injecté dans le bloc <em>content</em> de la template "mère".
</p>

<p class="standard">
  A présent, pour que notre page <em>index.html</em> soit incluse dans le layout global (en l'occurrence, dans le fichier <em>base.html</em>), il nous suffit d'ajouter la ligne suivante au début de ce fichier :
</p>

<div class="codecolorer-container html4strict vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="html4strict codecolorer">
    <span class="sc-1"><!-- elao/templates/index.html --></span><br /> {% extends "base.html" %}
  </div>
</div>

<p class="standard">
  Si vous avez récupéré la feuille de style et les images que je vous proposais, la page d'accueil <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> devrait désormais ressembler à ceci :
</p>

<div style="text-align: center;">
  <img src="http://www.elao.org/wp-content/uploads/2010/10/elao_shop_index_v1.png" alt="elao shop index v1 Django (3ème partie) : les templates, et bien dautres choses ..."  title="Django (3ème partie) : les templates, et bien dautres choses ..." />
</div>

<p class="standard">
  Bon, tout cela commence à prendre forme, à présent, nous allons afficher nos articles dans notre template fraichement créée !
</p>

### L'affichage des articles

<p class="standard">
  La première chose à faire consiste à modifier la méthode <em>index</em> du module <em>views.py</em> pour récupérer tous les articles afin de les passer à la vue …
</p>

<div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="python codecolorer">
    <span class="co1"># elao/shop/views.py</span><br /> <span class="co1"># ...</span><br /> <span class="kw1">from</span> shop.<span class="me1">models</span> <span class="kw1">import</span> Item<br /> <br /> <span class="kw1">def</span> index<span class="br0">&#40;</span>request<span class="br0">&#41;</span>:<br /> item_list <span class="sy0">=</span> Item.<span class="me1">objects</span>.<span class="kw2">all</span><span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="kw1">return</span> render_to_response<span class="br0">&#40;</span><span class="st0">'index.html'</span><span class="sy0">,</span> <span class="br0">&#123;</span> <span class="st0">'items'</span> : item_list<span class="br0">&#125;</span><span class="br0">&#41;</span>
  </div>
</div>

<p class="standard">
  … puis les afficher dans la vue :
</p>

<div class="codecolorer-container html4strict vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="html4strict codecolorer">
    <span class="sc-1"><!-- elao/templates/index.html --></span><br /> {% extends "base.html" %}<br /> <br /> {% block content %}<br /> <span class="sc2"><<a href="http://december.com/html/4/element/h1.html"><span class="kw2">h1</span></a>></span>Catalogue des articles<span class="sc2"><<span class="sy0">/</span><a href="http://december.com/html/4/element/h1.html"><span class="kw2">h1</span></a>></span><br /> {% for item in items %}<br /> <span class="sc2"><<a href="http://december.com/html/4/element/div.html"><span class="kw2">div</span></a> <span class="kw3">class</span><span class="sy0">=</span><span class="st0">"article_div"</span>></span><br /> <span class="sc2"><<a href="http://december.com/html/4/element/h2.html"><span class="kw2">h2</span></a>></span>{{ item.name }}<span class="sc2"><<span class="sy0">/</span><a href="http://december.com/html/4/element/h2.html"><span class="kw2">h2</span></a>></span><br /> <span class="sc2"><<a href="http://december.com/html/4/element/div.html"><span class="kw2">div</span></a> <span class="kw3">style</span><span class="sy0">=</span><span class="st0">"padding: 5px; text-align: center; margin-top: 10px;"</span>><<a href="http://december.com/html/4/element/img.html"><span class="kw2">img</span></a> <span class="kw3">title</span><span class="sy0">=</span><span class="st0">"{{ item.description }}"</span> <span class="kw3">src</span><span class="sy0">=</span><span class="st0">"/site-media/{{ item.picture }}"</span> <span class="kw3">alt</span><span class="sy0">=</span><span class="st0">"{{ item.name }}"</span> <span class="kw3">width</span><span class="sy0">=</span><span class="st0">"150px"</span> <span class="kw3">height</span><span class="sy0">=</span><span class="st0">"150px"</span> <span class="kw3">border</span><span class="sy0">=</span><span class="st0">"1"</span> <span class="sy0">/</span>><<span class="sy0">/</span><a href="http://december.com/html/4/element/div.html"><span class="kw2">div</span></a>></span><br /> <span class="sc2"><<a href="http://december.com/html/4/element/div.html"><span class="kw2">div</span></a> <span class="kw3">style</span><span class="sy0">=</span><span class="st0">"text-align: center; height: 15px; margin-top: 17px; font-size: 15px;"</span>></span>{{ item.price }} <span class="sc1">&</span>euro;<br /> {{ item.sport.name }}<span class="sc2"><<span class="sy0">/</span><a href="http://december.com/html/4/element/div.html"><span class="kw2">div</span></a>></span><br /> <span class="sc2"><<span class="sy0">/</span><a href="http://december.com/html/4/element/div.html"><span class="kw2">div</span></a>></span><br /> {% endfor %}<br /> <br /> <span class="sc1"> </span><br /> <br /> {% endblock %}
  </div>
</div>

<p class="standard">
  Dernière chose avant de passer à la pagination, aux tris, et au moteur de recherche : si vous observez les champs définis dans la classe "<em>Item</em>", vous remarquerez que nous avions défini un champ <em>stock</em> et un champ <em>public</em>. Comme nous travaillons sur une page destinée aux internautes (et non pas aux administrateurs du site), il est tout naturel de ne pas afficher les articles dont le stock est nul et ceux qui ont un statut "public" à false. Nous allons donc définir un <em>ModelManager</em> supplémentaire pour la classe <em>Item</em>, qui tienne compte de ces contraintes métiers. Nous allons donc dans un premier temps rédiger le code de la classe <em>PublicItemManager</em> qui hérite de <em>models.Manager</em>, puis modifier le code de la classe <em>Item</em> pour que celle-ci utilise ce nouveau <em>PublicItemManager</em> en plus du <em>manager</em> par défaut. Tout cela se passe bien évidemment dans le fichier <em>shop/models.py</em>.
</p>

<div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="python codecolorer">
    <span class="co1"># shop/models.py</span><br /> <span class="co1"># ...</span><br /> <span class="kw1">class</span> PublicItemManager<span class="br0">&#40;</span>models.<span class="me1">Manager</span><span class="br0">&#41;</span>:<br /> <span class="kw1">def</span> get_query_set<span class="br0">&#40;</span><span class="kw2">self</span><span class="br0">&#41;</span>:<br /> <span class="kw1">return</span> <span class="kw2">super</span><span class="br0">&#40;</span>PublicItemManager<span class="sy0">,</span> <span class="kw2">self</span><span class="br0">&#41;</span>.<span class="me1">get_query_set</span><span class="br0">&#40;</span><span class="br0">&#41;</span>.<span class="kw2">filter</span><span class="br0">&#40;</span>public<span class="sy0">=</span><span class="kw2">True</span><span class="br0">&#41;</span>.<span class="kw2">filter</span><span class="br0">&#40;</span>stock__gt<span class="sy0">=</span><span class="nu0"></span><span class="br0">&#41;</span><br /> <br /> <span class="kw1">class</span> Item<span class="br0">&#40;</span>models.<span class="me1">Model</span><span class="br0">&#41;</span>:<br /> <span class="co1"># …</span><br /> objects <span class="sy0">=</span> models.<span class="me1">Manager</span><span class="br0">&#40;</span><span class="br0">&#41;</span><br /> public_items <span class="sy0">=</span> PublicItemManager<span class="br0">&#40;</span><span class="br0">&#41;</span>
  </div>
</div>

<p class="standard">
  Noter que l'on a défini un second manager, mais si on ne souhaite pas qu'il se substitue au manager <em>objects</em> par défaut, il est nécessaire de déclarer explicitement ce dernier (l'interface d'Admin, par exemple, utiliserait le <em>manager public_items</em> si <em>objects</em> n'était pas déclaré et nous n'aurions donc plus accès dans l'Admin aux articles en rupture de stock et non publics, ce qui n'est pas le comportement souhaité !). A présent, nous allons modifier la méthode <em>index</em> dans le fichier <em>views.py</em> afin que les articles extraits de la base de données soient gérés par notre <em>Manager</em> personnalisé :
</p>

<div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="python codecolorer">
    <span class="co1"># shop/views.py</span><br /> <span class="co1"># …</span><br /> <br /> <span class="kw1">def</span> index<span class="br0">&#40;</span>request<span class="br0">&#41;</span>:<br /> item_list <span class="sy0">=</span> Item.<span class="me1">public_items</span>.<span class="kw2">all</span><span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="kw1">return</span> render_to_response<span class="br0">&#40;</span><span class="st0">'index.html'</span><span class="sy0">,</span> <span class="br0">&#123;</span> <span class="st0">'items'</span> : item_list<span class="br0">&#125;</span><span class="br0">&#41;</span>
  </div>
</div>

<p class="standard">
  A présent, si vous modifiez, via l'Admin, le stock ou le statut d'un article pour le rendre impropre à la publication, vous remarquerez que celui-ci ne s'affiche plus sur notre page d'accueil : mission accomplie ! Nous pouvons donc continuer à améliorer notre page, en y ajoutant la pagination des articles par exemple.
</p>

> <div class="aparte">
>   <p>
>     Pour en savoir plus sur la personnalisation des <em>ModelManagers</em>, voir la page de la documentation Django qui traite de ce sujet : <a href="http://www.djangoproject.com/documentation/models/custom_managers/" target="_blank">Giving models a custom manager</a>.Pour savoir comment exécuter des requêtes avec l'ORM de Django, voir la page dédiée : <a href="http://docs.djangoproject.com/en/1.2/topics/db/queries/" target="_blank">Making queries</a>.
>   </p>
> </div>

### Pagination des résultats

> <div class="aparte">
>   La pagination fait l'objet d'un chapitre complet dans la documentation officielle : <a href="http://docs.djangoproject.com/en/1.2/topics/pagination/" target="_blank">Pagination</a>.
> </div>

<p class="standard">
  Nous allons limiter le nombre d'articles affichés sur la page d'accueil à trois (dans la mesure où nous disposons d'un jeu de dix articles en base de données, le nombre de trois me paraît être un bon compromis, en tenant compte du fait que nous ajouterons des fonctionnalités de filtre par la suite). Dans un premier temps, nous allons modifier la méthode <em>index</em> du module <em>shop.views</em> :
</p>

<div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="python codecolorer">
    <span class="co1"># shop/views.py</span><br /> <span class="co1"># …</span><br /> <span class="kw1">from</span> django.<span class="me1">core</span>.<span class="me1">paginator</span> <span class="kw1">import</span> Paginator<span class="sy0">,</span> InvalidPage<span class="sy0">,</span> EmptyPage<br /> <br /> <span class="kw1">def</span> index<span class="br0">&#40;</span>request<span class="br0">&#41;</span>:<br /> item_list <span class="sy0">=</span> Item.<span class="me1">public_items</span>.<span class="kw2">all</span><span class="br0">&#40;</span><span class="br0">&#41;</span><br /> paginator <span class="sy0">=</span> Paginator<span class="br0">&#40;</span>item_list<span class="sy0">,</span> <span class="nu0">3</span><span class="br0">&#41;</span><br /> <br /> <span class="co1"># On s'assure que le parametre 'page' est bien de type integer, sinon, on fixe le numero de page courante a 1</span><br /> <span class="kw1">try</span>:<br /> page <span class="sy0">=</span> <span class="kw2">int</span><span class="br0">&#40;</span>request.<span class="me1">GET</span>.<span class="me1">get</span><span class="br0">&#40;</span><span class="st0">'page'</span><span class="sy0">,</span> <span class="st0">'1'</span><span class="br0">&#41;</span><span class="br0">&#41;</span><br /> <span class="kw1">except</span> <span class="kw2">ValueError</span>:<br /> page <span class="sy0">=</span> <span class="nu0">1</span><br /> <br /> <span class="co1"># Si la variable 'page' depasse le nombre de pages total, on fixe sa valeur au numero de la derniere page</span><br /> <span class="kw1">try</span>:<br /> items <span class="sy0">=</span> paginator.<span class="me1">page</span><span class="br0">&#40;</span>page<span class="br0">&#41;</span><br /> <span class="kw1">except</span> <span class="br0">&#40;</span>EmptyPage<span class="sy0">,</span> InvalidPage<span class="br0">&#41;</span>:<br /> items <span class="sy0">=</span> paginator.<span class="me1">page</span><span class="br0">&#40;</span>paginator.<span class="me1">num_pages</span><span class="br0">&#41;</span><br /> <br /> <span class="kw1">return</span> render_to_response<span class="br0">&#40;</span><span class="st0">'index.html'</span><span class="sy0">,</span> <span class="br0">&#123;</span> <span class="st0">'items'</span> : items <span class="br0">&#125;</span><span class="br0">&#41;</span>
  </div>
</div>

<p class="standard">
  Ensuite, nous devons modifier la template <em>index.html</em> car désormais, nous ne bouclons plus sur <em>items</em>, mais sur <em>items.object_list</em> :
</p>

> <div class="aparte">
>   Ici, <em>items</em> est une instance de la classe <a id="search_div" class="search" lang="html" type="text" name="search_sport" href="?page={{ items.next_page_number }}{{ sort_query_string }}" target="_blank""></a>
> </div>

<div class="codecolorer-container html4strict vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="html4strict codecolorer">
    -- Sélectionnez un sport --<br /> <br /> {% for sport in sports %}<br /> <br /> {{ sport.name }}<br /> <br /> {% endfor %}<br /> <br /> <span class="sc1">&</span>nbsp;<span class="sc1">&</span>nbsp;<br /> <br /> <span class="sc2"><<a href="http://december.com/html/4/element/input.html"><span class="kw2">input</span></a> <span class="kw3">type</span><span class="sy0">=</span><span class="st0">"submit"</span> <span class="kw3">value</span><span class="sy0">=</span><span class="st0">"Search"</span> <span class="sy0">/</span>></span><span class="sc1">&</span>nbsp;<span class="sc1">&</span>nbsp;<br /> <span class="sc2"><<a href="http://december.com/html/4/element/input.html"><span class="kw2">input</span></a> <span class="kw3">type</span><span class="sy0">=</span><span class="st0">"reset"</span> <span class="kw3">value</span><span class="sy0">=</span><span class="st0">"Reset"</span> <span class="sy0">/</span>></span><br /> <br /> <span class="sc-1"><!-- … --></span>
  </div>
</div>

<p class="standard">
  Le code de notre formulaire appelle plusieurs remarques :
</p>

<p class="standard">
  Première remarque : pour le moment nous ne faisons pas encore appel aux formulaires orientés objets proposés par Django. Nous aborderons cet aspect dans un autre article ; pour l'heure nous continuons à construire nos formulaires "<em>à la main</em>".
</p>

<p class="standard">
  Seconde remarque : vous noterez que les balises de templates (<em>template tags</em> dans le jargon de Django) font référence à des variables en session. En effet, en combinant les paramètres de tri et les paramètres de pagination, vous avez pu constater à quel point il était délicat de maintenir une certaine cohérence, et notamment de conserver une trace des paramètres GET pour les réinjecter dans les liens de pagination. Pour notre exemple, j'ai donc choisi de transmettre les paramètres du formulaire de recherche via la méthode POST, et non pas la méthode GET, et utiliser les sessions pour conserver leur trace. Nous y reviendrons.
</p>

<p class="standard">
  Troisième remarque : vous avez pu noter que nous bouclons sur une variable <em>sports</em> qui n'a pas encore été définie et pourtant la page s'affiche malgré tout. Quel enseignement en tirer ? Eh bien, tout simplement que les templates de Django, lorsqu'elles rencontrent une variable non définie, ne provoquent pas une Exception, mais les ignorent silencieusement.
</p>

<p class="standard">
  Dernière remarque : nous protégeons notre formulaire des attaques CSRF à l'aide de la balise <em>{% csrf_token %}</em>. Cette protection implique des modifications dans notre code que nous allons voir.
</p>

> <div class="aparte">
>   Pour en savoir plus sur la protection CSRF avec Django 1.2, consulter cette page : <a href="http://docs.djangoproject.com/en/1.2/ref/contrib/csrf/" target="_blank">Cross Site Request Forgery protection</a>
> </div>

<p class="standard">
  A présent, si vous soumettez le formulaire de recherche, Django vous gratifiera d'une erreur 403 <em>Forbidden</em> ; il a même la gentillesse de vous indiquer la marche à suivre, en trois points :
</p>

<div style="text-align: center;">
  <img title="CSRF Protection : 403 Forbidden" src="http://www.elao.org/wp-content/uploads/2010/10/elao_shop_csrf_403.png" alt="elao shop csrf 403 Django (3ème partie) : les templates, et bien dautres choses ..." width="750px" border="1" />
</div>

<p class="standard">
  Concernant les deux derniers points, nous sommes "<em>dans les clous</em>". En particulier, la classe <em>CsrfViewMiddleware</em> est bien déclarée par défaut dans notre fichier <em>settings.py</em>. En revanche, concernant le premier point, nous devons modifier notre méthode <em>index</em> pour passer à la méthode <em>render_to_response</em> un troisième paramètre de type <em>RequestContext</em>. Nous allons donc nous exécuter :
</p>

<div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="python codecolorer">
    <span class="co1"># shop/views.py</span><br /> <span class="kw1">from</span> django.<span class="me1">template</span> <span class="kw1">import</span> RequestContext<br /> <span class="co1"># …</span><br /> <span class="kw1">def</span> index<span class="br0">&#40;</span>request<span class="br0">&#41;</span>:<br /> <span class="co1"># …</span><br /> <span class="kw1">return</span> render_to_response<span class="br0">&#40;</span><span class="st0">'index.html'</span><span class="sy0">,</span> <span class="br0">&#123;</span> <span class="st0">'items'</span> : items<span class="sy0">,</span> <span class="st0">'sort_query_string'</span> : sort_query_string<span class="sy0">,</span><span class="br0">&#125;</span><span class="sy0">,</span> context_instance<span class="sy0">=</span>RequestContext<span class="br0">&#40;</span>request<span class="br0">&#41;</span><span class="br0">&#41;</span>
  </div>
</div>

> <div class="aparte">
>   Pour en savoir un peu plus sur les classes <em>Context</em> et <em>RequestContext</em>, référez-vous à la documentation de Django : <a href="http://docs.djangoproject.com/en/1.2/ref/templates/api/#playing-with-context-objects" target="_blank">Playing with Context objects</a>.
> </div>

<p class="standard">
  A présent, nous allons alimenter la liste de sélection avec les enregistrements contenus en base de données. Rien de très compliqué, cela consiste à récupérer tous les sports dans la méthode <em>index</em> et les transmettre à la vue (template) :
</p>

<div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="python codecolorer">
    <span class="co1"># shop/views.py</span><br /> <span class="kw1">from</span> shop.<span class="me1">models</span> <span class="kw1">import</span> Item<span class="sy0">,</span> Sport<br /> <span class="co1"># …</span><br /> <span class="kw1">def</span> index<span class="br0">&#40;</span>request<span class="br0">&#41;</span>:<br /> item_list <span class="sy0">=</span> Item.<span class="me1">public_items</span>.<span class="kw2">all</span><span class="br0">&#40;</span><span class="br0">&#41;</span><br /> sports <span class="sy0">=</span> Sport.<span class="me1">objects</span>.<span class="kw2">all</span><span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <span class="co1"># …</span><br /> <span class="kw1">return</span> render_to_response<span class="br0">&#40;</span><span class="st0">'index.html'</span><span class="sy0">,</span> <span class="br0">&#123;</span> <span class="st0">'items'</span> : items<span class="sy0">,</span> <span class="st0">'sort_query_string'</span> : sort_query_string<span class="sy0">,</span> <span class="st0">'sports'</span>: sports<span class="br0">&#125;</span><span class="sy0">,</span> context_instance<span class="sy0">=</span>RequestContext<span class="br0">&#40;</span>request<span class="br0">&#41;</span><span class="br0">&#41;</span>
  </div>
</div>

<p class="standard">
  Notre formulaire de filtres est désormais correctement configuré, il nous reste donc à traiter la soumission de ce formulaire. Comme je l'ai déjà évoqué, pour tracer les variables POST soumises via le formulaire, nous allons utiliser les sessions : lorsque l'on soumet le formulaire, on place toutes les variables du formulaire en session. Par la suite, si l'utilisateur navigue au moyen des liens de tri ou de pagination (donc via la méthode GET), nous nous baserons sur les variables en session pour retrouver les critères de filtre courants ; pour tenir à jour les variables en session, c'est très simple, seule une soumission du formulaire peut modifier ces variables ; en l'absence de variables POST, les variables en session restent inchangées. Quant aux variables de tri et de pagination, nous n'avons plus à nous en soucier : nous considérons qu'à partir du moment où l'utilisateur soumet une nouvelle requête via le formulaire, les variables de tri et de pagination n'ont plus à être maintenues, et elles sont systématiquement repositionnées à leur valeur par défaut. <strong>En résumé</strong> : on vérifie dans un premier temps si des données ont été postées ; si c'est le ce cas, on injecte les données postées en session ; dans tous les cas, on construit ensuite la requête en se basant sur les données présentes en session. Ce qui nous donne :
</p>

<div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="python codecolorer">
    <span class="co1"># shop/views.py</span><br /> <span class="co1"># …</span><br /> <span class="kw1">def</span> index<span class="br0">&#40;</span>request<span class="br0">&#41;</span>:<br /> item_list <span class="sy0">=</span> Item.<span class="me1">public_items</span>.<span class="kw2">all</span><span class="br0">&#40;</span><span class="br0">&#41;</span><br /> sports <span class="sy0">=</span> Sport.<span class="me1">objects</span>.<span class="kw2">all</span><span class="br0">&#40;</span><span class="br0">&#41;</span><br /> <br /> <span class="co1"># Parametres de recherche :</span><br /> <span class="co1"># On recupere les donnees de session dans une variable 'search'</span><br /> search <span class="sy0">=</span> request.<span class="me1">session</span>.<span class="me1">get</span><span class="br0">&#40;</span><span class="st0">'search'</span><span class="sy0">,</span> <span class="br0">&#123;</span><span class="br0">&#125;</span><span class="br0">&#41;</span><br /> <span class="co1"># Si le formulaire a ete soumis :</span><br /> <span class="kw1">if</span> request.<span class="me1">method</span> <span class="sy0">==</span> <span class="st0">'POST'</span> :<br /> <span class="co1"># On reinitialise la variable 'search'</span><br /> search <span class="sy0">=</span> <span class="br0">&#123;</span><span class="br0">&#125;</span><br /> <span class="kw1">if</span> request.<span class="me1">POST</span>.<span class="me1">has_key</span><span class="br0">&#40;</span><span class="st0">'search_name'</span><span class="br0">&#41;</span> <span class="kw1">and</span> request.<span class="me1">POST</span>.<span class="me1">get</span><span class="br0">&#40;</span><span class="st0">'search_name'</span><span class="br0">&#41;</span>:<br /> search<span class="br0">&#91;</span><span class="st0">'name'</span><span class="br0">&#93;</span> <span class="sy0">=</span> request.<span class="me1">POST</span>.<span class="me1">get</span><span class="br0">&#40;</span><span class="st0">'search_name'</span><span class="br0">&#41;</span><br /> <span class="kw1">if</span> request.<span class="me1">POST</span>.<span class="me1">has_key</span><span class="br0">&#40;</span><span class="st0">'search_sport'</span><span class="br0">&#41;</span> <span class="kw1">and</span> request.<span class="me1">POST</span>.<span class="me1">get</span><span class="br0">&#40;</span><span class="st0">'search_sport'</span><span class="br0">&#41;</span>:<br /> search<span class="br0">&#91;</span><span class="st0">'sport'</span><span class="br0">&#93;</span> <span class="sy0">=</span> <span class="nu0">1</span><br /> <span class="co1"># Et on reinjecte la variable 'search' dans la session</span><br /> request.<span class="me1">session</span><span class="br0">&#91;</span><span class="st0">'search'</span><span class="br0">&#93;</span> <span class="sy0">=</span> search<br /> <br /> <span class="kw1">if</span> search.<span class="me1">has_key</span><span class="br0">&#40;</span><span class="st0">'name'</span><span class="br0">&#41;</span> <span class="kw1">and</span> search<span class="br0">&#91;</span><span class="st0">'name'</span><span class="br0">&#93;</span>:<br /> item_list <span class="sy0">=</span> item_list.<span class="kw2">filter</span><span class="br0">&#40;</span>name__icontains<span class="sy0">=</span>search<span class="br0">&#91;</span><span class="st0">'name'</span><span class="br0">&#93;</span><span class="br0">&#41;</span><br /> <span class="kw1">if</span> search.<span class="me1">has_key</span><span class="br0">&#40;</span><span class="st0">'sport'</span><span class="br0">&#41;</span>:<br /> item_list <span class="sy0">=</span> item_list.<span class="kw2">filter</span><span class="br0">&#40;</span>sport<span class="sy0">=</span>search<span class="br0">&#91;</span><span class="st0">'sport'</span><span class="br0">&#93;</span><span class="br0">&#41;</span><br /> <span class="co1"># ...</span>
  </div>
</div>

<p class="standard">
  Vous pouvez désormais naviguer dans la liste des articles à l'aide du formulaire, des liens de pagination et de tri, tout devrait se dérouler normalement. A une exception près : lorsque l'on soumet le formulaire, les valeurs courantes des critères de filtre ne sont pas conservées par le formulaire. Le problème est lié à la transmission des variables de session à la template. En effet, par défaut, Django ne rend pas les variables de session directement disponibles dans les templates. Nous devons donc encore apporter une dernière modification, qui consiste à ajouter le tuple <em>TEMPLATE_CONTEXT_PROCESSORS</em> défini ci-dessous dans notre fichier <em>settings.py</em> :
</p>

<div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="python codecolorer">
    <span class="co1"># settings.py</span><br /> <span class="co1"># …</span><br /> TEMPLATE_CONTEXT_PROCESSORS <span class="sy0">=</span> <span class="br0">&#40;</span><br /> <span class="st0">'django.core.context_processors.auth'</span><span class="sy0">,</span><br /> <span class="st0">'django.core.context_processors.debug'</span><span class="sy0">,</span><br /> <span class="st0">'django.core.context_processors.i18n'</span><span class="sy0">,</span><br /> <span class="st0">'django.core.context_processors.request'</span><span class="sy0">,</span><br /> <span class="br0">&#41;</span><br /> <span class="co1"># …</span>
  </div>
</div>

<p class="standard">
  Il s'agit d'une liste de méthodes (<em>callables</em>) permettant d'injecter des variables issues par exemple de la requête, des sessions, ou de l'utilisateur courant, dans l'objet <em>context_instance</em> (de la classe <em>RequestContext</em>) que nous transmettons aux templates.
</p>

<div class="aparte">
  Les <em>context processors</em> sont abordés dans le chapitre de la documentation Django consacré à la classe <a href="http://docs.djangoproject.com/en/1.2/ref/templates/api/#subclassing-context-requestcontext" target="_blank">RequestContext</a>.
</div>

### Conclusion (absolument pas définitive)

<p class="standard">
  Aujourd'hui, en construisant une page d'accueil somme toute très classique, nous avons été amenés à appliquer de nombreuses fonctionnalités proposées par Django, parmi lesquelles les fixtures, l'héritage des templates, les variables de session, les gestionnaires de modèle personnalisés, la pagination, et j'en passe. Il nous reste malgré tout encore de nombreux concepts à étudier (l'internationalisation, les vues génériques, les tests unitaires et fonctionnels, l'authentification, les formulaires objets, le cache …). Je tâcherai de rédiger des articles dans lesquels j'aborderai ces différents points, mais avant d'en arriver là, je dois moi-même monter en compétences … Quoi qu'il en soit, je ne manquerai pas de vous faire profiter des connaissances que j'aurai acquises, avec toujours la ferme volonté de privilégier la pratique par rapport à la théorie.
</p>