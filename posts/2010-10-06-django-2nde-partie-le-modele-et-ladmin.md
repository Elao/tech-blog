
<div style="float: left; margin-right: 15px; margin-bottom: 15px;">
  <img src="/blog/wp-content/uploads/2010/09/Django.gif" alt="Django Django (2nde partie) : le Modèle et lAdmin"  title="Django (2nde partie) : le Modèle et lAdmin" />
</div>

<p class="standard">
  Bonjour à toutes et à tous,
</p>

<p class="standard">
  Aujourd&#8217;hui, dans notre série consacrée à Django, nous abordons un second volet autrement plus intéressant que le premier (pour ceux qui ont raté l&#8217;épisode précédent, c&#8217;est <a href="http://www.elao.org/python/premiers-pas-avec-le-framework-python-django.html" target="_blank">ici</a> que ça se passe). En effet, ce billet sera consacré d&#8217;une part au Modèle, que nous avions négligé précédemment, d&#8217;autre part à l&#8217;interface d&#8217;administration fournie par Django (<i>Django Admin Site</i>). J&#8217;aurais pu consacrer un article entier à la couche &#8220;Modèle&#8221; et aborder les nombreuses possibilités offertes par le Framework dans ce domaine. Mais mon précédent billet avait essentiellement pour but de nous familiariser avec Django, aussi d&#8217;un point de vue purement fonctionnel, le résultat finalement obtenu pouvait nous laisser sur notre faim. J&#8217;ai donc choisi ici de dépeindre le modèle dans ses grandes lignes, plutôt que d&#8217;entrer dans le détail, afin que nous puissions rapidement mettre en pratique nos connaissances, par le biais de l&#8217;interface d&#8217;Administration de Django. Mais sachez que les points importants sur lesquels nous ferons l&#8217;impasse aujourd&#8217;hui feront l&#8217;objet de futurs articles.
</p>

<p class="standard">
  Avant de débuter ce tutoriel, assurez-vous que Python et Django sont installés sur votre ordinateur. Si tel n&#8217;est pas le cas, référez-vous à notre <a href="http://www.elao.org/python/premiers-pas-avec-le-framework-python-django.html" target="_blank">introduction au Framework Django</a> pour connaître la marche à suivre.
</p>

### Présentation du projet et du modèle

<p class="standard">
  Le projet qui va nous servir de fil rouge tout au long de cet article (et ceux qui suivront) consiste en un site de vente d&#8217;articles de sport. Nous allons donc dans un premier temps créer notre projet (je nommerai ce projet &#8220;elao&#8221;, c&#8217;est un nom court, concis, idéal donc pour un nom de projet) et une application que je nommerai &#8220;shop&#8221;. Allez, c&#8217;est parti !
</p>

<p class="standard">
  Nous ouvrons donc une ligne de commande, nous nous plaçons dans le répertoire qui contient nos projets Django (&#8220;workspace-django&#8221; en ce qui me concerne), et nous lançons les commandes nécessaires pour initialiser le projet et créer l&#8217;application &#8220;shop&#8221; :
</p>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    &nbsp; <span class="kw3">cd</span> ~<span class="sy0">/</span>workspace-django<br /> &nbsp; django-admin.py startproject elao<br /> &nbsp; <span class="kw3">cd</span> elao<br /> &nbsp; python manage.py startapp shop
  </div>
</div>

<p class="standard">
  Nous allons à présent configurer les accès à notre base de données et activer l&#8217;application que nous venons de créer. Pour cela, nous allons éditer le fichier <i>settings.py</i> qui se trouve à la racine de notre projet. Comme nous souhaitons nous initier à Django, nous opterons pour le moteur de base de données SQLite qui présente l&#8217;avantage d&#8217;être inclus dans les dernières distributions de Python et ne nécessite donc pas d&#8217;installation supplémentaire. Noter pour la petite histoire que les créateurs du Framework ne semblent pas être de fervents défenseurs de MySQL, auquel ils préfèrent Postgres &#8230;
</p>

> <div class="aparte">
>   Si vous souhaitez utiliser MySQL à la place de SQLite, il vous faudra installer un driver Python permettant d&#8217;accéder aux bases de données MySQL, <a href="http://sourceforge.net/projects/mysql-python/" target="_blank">MySQL for Python</a>, par exemple. Sachez toutefois que ce driver nécessite la présence des headers de MySQL et que son installation requiert donc, si vous aviez installé une version binaire de MySQL, quelques compétences en administration que je ne possède hélas pas.</p> <p>
>     <b><u>Nota bene</u> : </b>Si vous utilisez une distribution <b>Linux</b>, il est probable que votre gestionnaire de paquets vous permette d&#8217;installer le driver MySQL sans douleur. Par exemple, sous Ubuntu, recherchez un paquet nommé &#8220;python-mysqldb&#8221;. En revanche, si vous utilisez <b>MacOS</b>, je vous invite à faire une recherche sur <a href="http://www.google.fr/search?hl=fr&#038;q=Python+MySQL+MacOS+trouble">Google</a> ; a priori, l&#8217;installation du driver sous cet OS ne relève pas de la sinécure, de nombreuses personnes s&#8217;y sont déjà cassé les dents. Pour les utilisateurs de <b>Windows</b>, il existe des exécutables permettant d&#8217;installer un driver MySQL, mais je n&#8217;ai pas pu les tester. Je vous renvoie donc à <a href="http://www.google.fr/search?q=Python+MySQL+Windows&#038;hl=fr" target="blank">Google</a>, mais <i>a priori</i>, veillez à télécharger un driver compatible avec votre version de Python. </div> </blockquote> <p class="standard">
>       Dans le fichier <i>settings.py</i>, nous allons mentionner les chemins absolus de notre base de données, du répertoire hébergeant les templates, celui contenant les fichiers statiques &#8230; Aussi, pour rendre notre projet portable, nous allons dans un premier temps créer une constante ROOT_DIR correspondant à la racine de notre projet, et par la suite, tous les chemins absolus se baseront sur cette constante. Pour cela, ajoutez les deux lignes suivantes au début du fichier de configuration du projet :
>     </p>
>     
>     <div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
>       <div class="python codecolorer">
>         &nbsp; <span class="co1"># settings.py</span><br /> &nbsp; <span class="kw1">import</span> <span class="kw3">os</span><br /> &nbsp; ROOT_DIR <span class="sy0">=</span> <span class="kw3">os</span>.<span class="me1">path</span>.<span class="me1">dirname</span><span class="br0">&#40;</span>__file__<span class="br0">&#41;</span> + <span class="st0">'/'</span>
>       </div>
>     </div>
>     
>     <p class="standard">
>       Pour configurer les accès à notre base de données, nous allons modifier la constante <i>DATABASES</i> (qui correspond à la structure de données de type &#8220;<a href="http://docs.python.org/tutorial/datastructures.html#dictionaries" target="_blank">dictionnaire</a>&#8221; dans le langage Python) :
>     </p>
>     
>     <div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
>       <div class="python codecolorer">
>         &nbsp; <span class="co1"># settings.py</span><br /> &nbsp; <span class="co1"># ...</span><br /> &nbsp; DATABASES <span class="sy0">=</span> <span class="br0">&#123;</span><br /> &nbsp; &nbsp; &nbsp; <span class="st0">'default'</span>: <span class="br0">&#123;</span><br /> &nbsp; &nbsp; &nbsp; &nbsp; <span class="st0">'ENGINE'</span>: <span class="st0">'django.db.backends.sqlite3'</span><span class="sy0">,</span> <br /> &nbsp; &nbsp; &nbsp; &nbsp; <span class="st0">'NAME'</span>: <span class="kw3">os</span>.<span class="me1">path</span>.<span class="me1">join</span><span class="br0">&#40;</span>ROOT_DIR<span class="sy0">,</span> <span class="st0">'elao.db'</span><span class="br0">&#41;</span><span class="sy0">,</span> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <br /> &nbsp; &nbsp; &nbsp; <span class="br0">&#125;</span><br /> &nbsp; <span class="br0">&#125;</span>
>       </div>
>     </div>
>     
>     <p class="standard">
>       Enfin, activons l&#8217;application &#8220;shop&#8221; que nous venons de créer, et profitons-en au passage pour activer l&#8217;Admin de Django en décommentant la dernière ligne du fichier <i>settings.py</i> (<i>INSTALLED_APPS</i> correspond à une structure Python de type <a href="http://docs.python.org/tutorial/datastructures.html#tuples-and-sequences" target="_blank">tuple</a>) :
>     </p>
>     
>     <div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
>       <div class="python codecolorer">
>         &nbsp; <span class="co1"># settings.py</span><br /> &nbsp; <span class="co1"># ...</span><br /> &nbsp; INSTALLED_APPS <span class="sy0">=</span> <span class="br0">&#40;</span><br /> &nbsp; &nbsp; &nbsp; <span class="st0">'django.contrib.auth'</span><span class="sy0">,</span><br /> &nbsp; &nbsp; &nbsp; <span class="st0">'django.contrib.contenttypes'</span><span class="sy0">,</span><br /> &nbsp; &nbsp; &nbsp; <span class="st0">'django.contrib.sessions'</span><span class="sy0">,</span><br /> &nbsp; &nbsp; &nbsp; <span class="st0">'django.contrib.sites'</span><span class="sy0">,</span><br /> &nbsp; &nbsp; &nbsp; <span class="st0">'django.contrib.messages'</span><span class="sy0">,</span><br /> &nbsp; &nbsp; &nbsp; <span class="co1"># Uncomment the next line to enable the admin:</span><br /> &nbsp; &nbsp; &nbsp; <span class="st0">'django.contrib.admin'</span><span class="sy0">,</span><br /> &nbsp; &nbsp; &nbsp; <span class="st0">'shop'</span><span class="sy0">,</span><br /> &nbsp; <span class="br0">&#41;</span>
>       </div>
>     </div>
>     
>     <p class="standard">
>       Nous allons à présent entamer la définition de notre modèle. Pour l&#8217;heure, nous nous contenterons de rédiger deux classes du Modèle, mais sachez que ce modèle s&#8217;enrichira à mesure que nous monterons en compétences. Nous créons donc dans le fichier <i>shop/models.py</i> une classe &#8220;Item&#8221; correspondant aux articles que nous mettrons en vente sur notre site et une classe &#8220;Sport&#8221; qui nous permettra de regrouper les articles par sport (un item appartient à un sport, un sport comporte plusieurs articles, nous sommes donc dans le cadre d&#8217;une relation one-to-many).
>     </p>
>     
>     <div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
>       <div class="python codecolorer">
>         <span class="co1"># shop/models.py</span><br /> <br /> <span class="kw1">from</span> django.<span class="me1">db</span> <span class="kw1">import</span> models<br /> <span class="kw1">import</span> <span class="kw3">datetime</span><br /> <br /> <span class="kw1">class</span> Sport<span class="br0">&#40;</span>models.<span class="me1">Model</span><span class="br0">&#41;</span>:<br /> &nbsp; name <span class="sy0">=</span> models.<span class="me1">CharField</span><span class="br0">&#40;</span>max_length<span class="sy0">=</span><span class="nu0">100</span><span class="sy0">,</span> null<span class="sy0">=</span><span class="kw2">False</span><span class="sy0">,</span> blank<span class="sy0">=</span><span class="kw2">False</span><span class="br0">&#41;</span><br /> &nbsp; slug <span class="sy0">=</span> models.<span class="me1">SlugField</span><span class="br0">&#40;</span>null<span class="sy0">=</span><span class="kw2">False</span><span class="sy0">,</span> blank<span class="sy0">=</span><span class="kw2">False</span><span class="sy0">,</span> unique<span class="sy0">=</span><span class="kw2">True</span><span class="br0">&#41;</span><br /> <br /> &nbsp; <span class="kw1">def</span> <span class="kw4">__unicode__</span><span class="br0">&#40;</span><span class="kw2">self</span><span class="br0">&#41;</span>:<br /> &nbsp; &nbsp; <span class="kw1">return</span> <span class="kw2">self</span>.<span class="me1">name</span><br /> <br /> &nbsp; <span class="kw1">class</span> Meta:<br /> &nbsp; &nbsp; db_table <span class="sy0">=</span> <span class="st0">'elao_sport'</span><br /> &nbsp; &nbsp; ordering <span class="sy0">=</span> <span class="br0">&#91;</span> <span class="st0">'name'</span> <span class="br0">&#93;</span><br /> <br /> <br /> <span class="kw1">class</span> Item<span class="br0">&#40;</span>models.<span class="me1">Model</span><span class="br0">&#41;</span>:<br /> &nbsp; name <span class="sy0">=</span> models.<span class="me1">CharField</span><span class="br0">&#40;</span>max_length<span class="sy0">=</span><span class="nu0">255</span><span class="sy0">,</span> null<span class="sy0">=</span><span class="kw2">False</span><span class="sy0">,</span> blank<span class="sy0">=</span><span class="kw2">False</span><span class="sy0">,</span> unique<span class="sy0">=</span><span class="kw2">True</span><span class="br0">&#41;</span><br /> &nbsp; slug <span class="sy0">=</span> models.<span class="me1">SlugField</span><span class="br0">&#40;</span>null<span class="sy0">=</span><span class="kw2">False</span><span class="sy0">,</span> blank<span class="sy0">=</span><span class="kw2">False</span><span class="sy0">,</span> unique<span class="sy0">=</span><span class="kw2">True</span><span class="br0">&#41;</span><br /> &nbsp; description <span class="sy0">=</span> models.<span class="me1">TextField</span><span class="br0">&#40;</span>max_length<span class="sy0">=</span><span class="nu0">500</span><span class="sy0">,</span> null<span class="sy0">=</span><span class="kw2">True</span><span class="sy0">,</span> blank<span class="sy0">=</span><span class="kw2">True</span><span class="br0">&#41;</span><br /> &nbsp; picture <span class="sy0">=</span> models.<span class="me1">ImageField</span><span class="br0">&#40;</span>upload_to <span class="sy0">=</span> <span class="st0">'pictures/items/'</span><span class="sy0">,</span> null<span class="sy0">=</span><span class="kw2">True</span><span class="br0">&#41;</span><br /> &nbsp; created_at <span class="sy0">=</span> models.<span class="me1">DateField</span><span class="br0">&#40;</span>null<span class="sy0">=</span><span class="kw2">False</span><span class="sy0">,</span> default<span class="sy0">=</span> <span class="kw3">datetime</span>.<span class="kw3">datetime</span>.<span class="me1">now</span><span class="br0">&#41;</span><br /> &nbsp; stock <span class="sy0">=</span> models.<span class="me1">IntegerField</span><span class="br0">&#40;</span>blank<span class="sy0">=</span><span class="kw2">False</span><span class="sy0">,</span> null<span class="sy0">=</span><span class="kw2">False</span><span class="br0">&#41;</span><br /> &nbsp; price <span class="sy0">=</span> models.<span class="me1">FloatField</span><span class="br0">&#40;</span>blank<span class="sy0">=</span><span class="kw2">False</span><span class="sy0">,</span> null<span class="sy0">=</span><span class="kw2">False</span><span class="br0">&#41;</span><br /> &nbsp; public <span class="sy0">=</span> models.<span class="me1">BooleanField</span><span class="br0">&#40;</span>null<span class="sy0">=</span><span class="kw2">False</span><span class="sy0">,</span> blank<span class="sy0">=</span><span class="kw2">False</span><span class="sy0">,</span> default<span class="sy0">=</span><span class="kw2">False</span><span class="br0">&#41;</span><br /> &nbsp; sport <span class="sy0">=</span> models.<span class="me1">ForeignKey</span><span class="br0">&#40;</span>Sport<span class="br0">&#41;</span><br /> <br /> &nbsp; <span class="kw1">def</span> <span class="kw4">__unicode__</span><span class="br0">&#40;</span><span class="kw2">self</span><span class="br0">&#41;</span>:<br /> &nbsp; &nbsp; <span class="kw1">return</span> <span class="kw2">self</span>.<span class="me1">name</span><br /> <br /> &nbsp; <span class="kw1">class</span> Meta:<br /> &nbsp; &nbsp; db_table <span class="sy0">=</span> <span class="st0">'elao_item'</span><br /> &nbsp; &nbsp; ordering <span class="sy0">=</span> <span class="br0">&#91;</span> <span class="st0">'name'</span> <span class="br0">&#93;</span>
>       </div>
>     </div>
>     
>     <p class="standard">
>       Avant de commenter ce code, vérifions que notre modèle est valide :
>     </p>
>     
>     <div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
>       <div class="python codecolorer">
>         &nbsp;python manage.<span class="me1">py</span> validate
>       </div>
>     </div>
>     
>     <p class="standard">
>       Corrigez si nécessaire le code de votre modèle, jusqu&#8217;à obtenir le message suivant après avoir relancé la commande <i>validate</i> :
>     </p>
>     
>     <div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
>       <div class="bash codecolorer">
>         &nbsp;<span class="nu0"></span> errors found
>       </div>
>     </div>
>     
>     <blockquote>
>       <div class="aparte">
>         Comme nous utilisons un champ de type <i>ImageField</i>, il est nécessaire que la librairie PIL (Python Image Library) soit installée sur votre ordinateur. Pour les utilisateurs de Linux, il est probable que votre distribution vous permette de l&#8217;installer à partir de votre gestionnaire de paquetages. Si vous utilisez MacOS ou Windows, téléchargez et installez PIL en suivant les instructions disponibles sur cette page consacrée à la <a href="http://www.pythonware.com/products/pil/" target="_blank">Bibliothèque PIL</a>.<br /><br /><br /> Si vous ne souhaitez pas installer PIL, il vous suffit de changer le type du champ <i>picture</i> en <b>CharField</b>.
>       </div>
>     </blockquote>
>     
>     <p class="standard">
>       La définition des classes de notre modèle appelle quelques remarques. Tout d&#8217;abord, notez que les objets du modèle destinés à être manipulées par l&#8217;ORM (<a href="http://en.wikipedia.org/wiki/Object-relational_mapping" target="_blank"><i>Object-Relational Mapping</i></a>) de Django héritent de la classe <i>django.db.models.Model</i>. En Python, la syntaxe de l&#8217;héritage se présente donc sous la forme <i> class MaClasse(ClasseHéritée)</i>
>     </p>
>     
>     <blockquote>
>       <div class="aparte">
>         Pour en savoir plus sur la programmation objet avec Python, n&#8217;hésitez pas à consulter la <a href="http://docs.python.org/tutorial/classes.html" target="_blank">documentation officielle</a>. On notera en particulier que Python supporte l&#8217;héritage multiple.
>       </div>
>     </blockquote>
>     
>     <p class="standard">
>       Ensuite, chaque propriété de la classe se voit attribuer un type de champ (FloatField, CharField, etc.). Pour connaître tous les types proposés par Django, visitez cette page : <a href="http://docs.djangoproject.com/en/1.2/ref/models/fields/" target="_blank">Model Field Reference</a>. Nous remarquerons au passage que les types proposés par Django sont généralement plus riches que les types élémentaires fournis par votre serveur de base de données. Il est ainsi possible de définir une propriété de type slug, XML, email, url, fichier, fichier image &#8230; Choisir un de ces types permet de garantir non seulement une vérification des données selon les contraintes définies au niveau de la base de données (taille du champ, unicité, <i>null</i> non autorisé &#8230;) mais également une validation des objets par l&#8217;ORM de Django, qui est fonction du type choisi (par exemple, pour un champ de type <i>EmailField</i>, la valeur est-elle conforme au pattern attendu par une adresse email ?). Noter enfin les types <i>ForeignKey</i> (utilisé dans notre exemple dans la classe <i>Item</i> pour référencer un objet de la classe <i>Sport</i>), ou bien <i>models.ManyToManyField</i>, que nous utiliserons sans doute à l&#8217;occasion d&#8217;un prochain tutoriel.
>     </p>
>     
>     <p class="standard">
>       Pour chaque champ, on définit des options (<i>null</i> autorisé, valeur par défaut &#8230;). Encore une fois, celles proposées par Django sont plus riches que celles des serveurs de bases de données ; je citerais par exemple l&#8217;option <i>unique_for_date</i> ou <i>unique_for_month</i>. Pour découvrir ces options et toutes celles proposées par Django, n&#8217;hésitez pas à consulter la documentation officielle de Django : <a href="http://docs.djangoproject.com/en/1.2/ref/models/fields/#field-options" target="_blank">Field Options</a>.
>     </p>
>     
>     <p class="standard">
>       La méthode <i>__unicode__(self)</i> (attention, elle commence et se termine par un caractère &#8220;_&#8221; double !) nous permet de définir sous quelle forme littérale nous souhaitons afficher l&#8217;objet, par exemple lorsque nous passons cet objet à la méthode <i>print</i> ou lorsque nous afficherons cet objet dans les vues (templates).
>     </p>
>     
>     <p class="standard">
>       Enfin, pour chaque classe, nous pouvons définir une classe interne <i>Meta</i> qui nous permet de définir des propriétés au niveau de la table et non pas de chaque objet. Nous pouvons ainsi préciser le nom de la table en base de données, le champ sur lequel nous souhaitons trier les enregistrements par défaut, les contraintes d&#8217;unicité portant sur plusieurs champs, etc. Pour connaître toutes les options de cette classe, voir la documentation de Django : <a href="http://docs.djangoproject.com/en/1.2/ref/models/options/" target="_blank">Model Meta options</a>.
>     </p>
>     
>     <p class="standard">
>       A présent, nous allons générer les tables en base de données (si vous utilisez SQLite, la base de données sera créée, sinon, il vous faudra préalablement créer une base de données portant le nom que vous avez défini dans le fichier <i>settings.py</i>) :
>     </p>
>     
>     <div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
>       <div class="python codecolorer">
>         &nbsp; python manage.<span class="me1">py</span> syncdb
>       </div>
>     </div>
>     
>     <p class="standard">
>       Par défaut, Django active les applications nécessaires pour gérer les utilisateurs et les permissions, et lorsque vous lancez la commande ci-dessus, il vous est demandé si vous souhaitez créer un <i>superuser</i>. Répondez &#8220;yes&#8221; et saisissez lorsque l&#8217;invitation vous le demande le nom du <i>superuser</i> ainsi que son email et son mot de passe (nous aurons par la suite besoin de ces identifiants pour accéder à l&#8217;interface d&#8217;administration de Django).
>     </p>
>     
>     <blockquote>
>       <div class="aparte">
>         Si vous avez répondu &#8220;no&#8221; par erreur, sachez qu&#8217;il est possible par la suite de créer un super-utilisateur au moyen de la commande suivante :</p> <div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
>           <div class="python codecolorer">
>             &nbsp;python manage.<span class="me1">py</span> createsuperuser
>           </div>
>         </div>
>       </div>
>     </blockquote>
>     
>     <p class="standard">
>       Si vous voulez connaître les instruction SQL exécutées par Django pour créer ces tables, vous pouvez lancer une de ces deux commandes :
>     </p>
>     
>     <div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
>       <div class="python codecolorer">
>         &nbsp; python manage.<span class="me1">py</span> sql shop<br /> &nbsp; python manage.<span class="me1">py</span> sqlall shop <span class="co1"># où 'shop' correspond à notre application</span>
>       </div>
>     </div>
>     
>     <blockquote>
>       <div class="aparte">
>         <b><u>Rappel</u> :</b> Pour connaître toutes les commandes Django disponibles, tapez simplement :<br /><br /> [php] python manage.py [/php]<br /> Page de la documentation officielle de Django : <a href="http://docs.djangoproject.com/en/1.2/ref/django-admin/" target="_blank">django-admin.py & manage.py</a>
>       </div>
>     </blockquote>
>     
>     <p class="standard">
>       Nous allons à présent manipuler la classe <i>Sport</i> au moyen d&#8217;un shell interactif très pratique, en particulier lorsque l&#8217;on débute sous Django et Python. N&#8217;hésitez pas à en abuser lorsque vous souhaitez expérimenter Python et/ou Django pour vous familiariser avec ces deux technologies !
>     </p>
>     
>     <div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
>       <div class="python codecolorer">
>         &nbsp;python manage.<span class="me1">py</span> shell
>       </div>
>     </div>
>     
>     <p class="standard">
>       Saisissez dans cette console les instructions suivantes :
>     </p>
>     
>     <div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
>       <div class="python codecolorer">
>         &nbsp; <span class="sy0">>>></span> <span class="kw1">from</span> elao.<span class="me1">shop</span>.<span class="me1">models</span> <span class="kw1">import</span> Sport<br /> &nbsp; <span class="sy0">>>></span> sport <span class="sy0">=</span> Sport<span class="br0">&#40;</span>name<span class="sy0">=</span><span class="st0">"Football"</span><span class="sy0">,</span> slug<span class="sy0">=</span><span class="st0">"football"</span><span class="br0">&#41;</span><br /> &nbsp; <span class="sy0">>>></span> sport.<span class="me1">save</span><span class="br0">&#40;</span><span class="br0">&#41;</span><br /> &nbsp; <span class="sy0">>>></span> sport.<span class="kw2">id</span><br /> &nbsp; <span class="nu0">1</span><br /> &nbsp; <span class="sy0">>>></span> sport.<span class="me1">name</span><br /> &nbsp; <span class="st0">'Football'</span><br /> &nbsp; <span class="sy0">>>></span> sport <span class="sy0">=</span> Sport<span class="br0">&#40;</span>name<span class="sy0">=</span><span class="st0">"Arts martiaux"</span><span class="sy0">,</span> slug<span class="sy0">=</span><span class="st0">"arts-martiaux"</span><span class="br0">&#41;</span><br /> &nbsp; <span class="sy0">>>></span> sport.<span class="me1">save</span><span class="br0">&#40;</span><span class="br0">&#41;</span><br /> &nbsp; <span class="sy0">>>></span> sport.<span class="me1">name</span><br /> &nbsp; <span class="st0">'Arts martiaux'</span><br /> &nbsp; <span class="sy0">>>></span> Sport.<span class="me1">objects</span>.<span class="kw2">all</span><span class="br0">&#40;</span><span class="br0">&#41;</span><br /> &nbsp; <span class="br0">&#91;</span><span class="sy0"><</span>Sport: Arts martiaux<span class="sy0">>,</span> <span class="sy0"><</span>Sport: Football<span class="sy0">></span><span class="br0">&#93;</span><br /> &nbsp; <span class="sy0">>>></span> Sport.<span class="me1">objects</span>.<span class="me1">get</span><span class="br0">&#40;</span><span class="kw2">id</span><span class="sy0">=</span><span class="nu0">1</span><span class="br0">&#41;</span><br /> &nbsp; <span class="sy0"><</span>Sport: Football<span class="sy0">></span><br /> &nbsp; <span class="sy0">>>></span> Sport.<span class="me1">objects</span>.<span class="kw2">filter</span><span class="br0">&#40;</span>name<span class="sy0">=</span><span class="st0">"Football"</span><span class="br0">&#41;</span><br /> &nbsp; <span class="br0">&#91;</span><span class="sy0"><</span>Sport: Football<span class="sy0">></span><span class="br0">&#93;</span>
>       </div>
>     </div>
>     
>     <p class="standard">
>       Rien de très compliqué. Nous venons de créer deux objets de la classe &#8220;Sport&#8221;, que nous avons sauvegardés en base de données. Ensuite, nous avons utilisé un gestionnaire de Modèle, <i>ModelManager</i>, en l&#8217;occurrence <i>sport.objects</i>, pour exécuter des requêtes sur la table <i>elao_sport</i>. Nous aurons l&#8217;occasion de revenir sur cette notion de <i>ModelManager</i> à l&#8217;occasion de futurs articles. Pour quitter le shell interactif, il suffit de saisir <b>CTL + D</b> au clavier.
>     </p>
>     
>     <p class="standard">
>       Voilà, nous n&#8217;avons fait qu&#8217;effleurer le sujet du Modèle de Django, nous aurons l&#8217;occasion d&#8217;y revenir, mais pour l&#8217;heure, il est temps de passer à l&#8217;Admin de Django, histoire de constater de visu les ajouts que nous avons faits en base de données au moyen de l&#8217;ORM !
>     </p>
>     
>     <h3>
>       Le site d&#8217;Administration de Django
>     </h3>
>     
>     <p class="standard">
>       Pour accéder à l&#8217;interface d&#8217;administration de Django, il faut dans un premier temps activer l&#8217;admin en mettant à jour le tuple INSTALLED_APPS ; cela consiste à décommenter la ligne <i>django.contrib.admin</i> du fichier <i>settings.py</i> (si vous avez suivi les étapes depuis le début, nous l&#8217;avons déjà fait). Il faut également décommenter la route permettant d&#8217;accéder à l&#8217;Admin. Pour cela, il faut éditer le fichier <i>urls.py</i> et décommenter la ligne correspondante :
>     </p>
>     
>     <div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
>       <div class="python codecolorer">
>         &nbsp; <span class="co1"># urls.py</span><br /> &nbsp; <span class="co1"># ...</span><br /> &nbsp; <span class="co1"># Uncomment the next line to enable the admin:</span><br /> &nbsp; <span class="br0">&#40;</span>r<span class="st0">'^admin/'</span><span class="sy0">,</span> include<span class="br0">&#40;</span>admin.<span class="kw3">site</span>.<span class="me1">urls</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">,</span>
>       </div>
>     </div>
>     
>     <p class="standard">
>       Et également décommenter les lignes suivantes (toujours dans le fichier urls.py).
>     </p>
>     
>     <div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
>       <div class="python codecolorer">
>         &nbsp; <span class="kw1">from</span> django.<span class="me1">contrib</span> <span class="kw1">import</span> admin<br /> &nbsp; admin.<span class="me1">autodiscover</span><span class="br0">&#40;</span><span class="br0">&#41;</span>
>       </div>
>     </div>
>     
>     <p class="standard">
>       A présent, nous allons démarrer le serveur &#8230;
>     </p>
>     
>     <div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
>       <div class="python codecolorer">
>         &nbsp; python manage.<span class="me1">py</span> runserver
>       </div>
>     </div>
>     
>     <p class="standard">
>       &#8230; et admirer le résultat en nous rendant à l&#8217;URL suivante : <a href="http://localhost:8000/admin/" target="_blank">http://localhost:8000/admin/</a>. Nous sommes alors invités à nous loguer :
>     </p>
>     
>     <div style="text-align:center;">
>       <img src="/wp-content/uploads/2010/09/django-admin.gif" title="Django Admin Login Form" alt="django admin Django (2nde partie) : le Modèle et lAdmin" /></img>
>     </div>
>     
>     <p class="standard">
>       Saisissez les identifiants que vous aviez précisés lorsque nous avions lancé la commande <i>python manage.py syncdb</i>. Voici le résultat après login :
>     </p>
>     
>     <div style="text-align:center;">
>       <img src="/wp-content/uploads/2010/09/Capture-Site-administration-Django-site-admin-Mozilla-Firefox.png" title="Django Admin Home" alt="Capture Site administration Django site admin Mozilla Firefox Django (2nde partie) : le Modèle et lAdmin" width="75%" /></img>
>     </div>
>     
>     <p class="standard">
>       A présent, nous allons éditer le fichier contenant la définition de nos deux classes du modèle, <i>Item</i> et <i>Sport</i>, afin qu&#8217;elles soient prises en compte par l&#8217;Admin de Django. Pour cela, nous allons éditer le fichier <i>shop/models.py</i> et y ajouter les lignes suivantes :
>     </p>
>     
>     <div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
>       <div class="python codecolorer">
>         <span class="co1"># shop/models.py</span><br /> <span class="kw1">from</span> django.<span class="me1">contrib</span> <span class="kw1">import</span> admin<br /> <span class="co1"># …</span><br /> &nbsp; <br /> <span class="kw1">class</span> SportAdmin<span class="br0">&#40;</span>admin.<span class="me1">ModelAdmin</span><span class="br0">&#41;</span>:<br /> &nbsp; list_display <span class="sy0">=</span> <span class="br0">&#91;</span> <span class="st0">'name'</span><span class="sy0">,</span> <span class="st0">'slug'</span> <span class="br0">&#93;</span><br /> &nbsp; search_fields <span class="sy0">=</span> <span class="br0">&#91;</span> <span class="st0">'name'</span> <span class="br0">&#93;</span><br /> &nbsp; &nbsp; <br /> <span class="kw1">class</span> ItemAdmin<span class="br0">&#40;</span>admin.<span class="me1">ModelAdmin</span><span class="br0">&#41;</span>:<br /> &nbsp; list_display <span class="sy0">=</span> <span class="br0">&#91;</span> <span class="st0">'name'</span><span class="sy0">,</span> <span class="st0">'slug'</span><span class="sy0">,</span> <span class="st0">'description'</span><span class="sy0">,</span> <span class="st0">'picture'</span><span class="sy0">,</span> <span class="st0">'stock'</span><span class="sy0">,</span> <span class="st0">'price'</span><span class="sy0">,</span> <span class="st0">'public'</span><span class="sy0">,</span> <span class="st0">'sport'</span> <span class="br0">&#93;</span><br /> &nbsp; search_fields <span class="sy0">=</span> <span class="br0">&#91;</span> <span class="st0">'name'</span> <span class="br0">&#93;</span><br /> &nbsp; <br /> admin.<span class="kw3">site</span>.<span class="me1">register</span><span class="br0">&#40;</span>Sport<span class="sy0">,</span> SportAdmin<span class="br0">&#41;</span><br /> admin.<span class="kw3">site</span>.<span class="me1">register</span><span class="br0">&#40;</span>Item<span class="sy0">,</span> ItemAdmin<span class="br0">&#41;</span>
>       </div>
>     </div>
>     
>     <p class="standard">
>       Rendez-vous à présent sur la page <a href="http://localhost:8000/admin/" target="_blank">http://localhost:8000/admin/</a> pour constater que l&#8217;Admin de Django a bien enregistré nos deux classes du modèle. Si tout s&#8217;est correctement déroulé, vous devriez voir la liste des entités gérées sur la page d&#8217;accueil :
>     </p>
>     
>     <div style="text-align: center;">
>       <img src="/wp-content/uploads/2010/09/Django-Admin-Model-Classes.png" title="Django Admin - Model classes" alt="Django Admin Model Classes Django (2nde partie) : le Modèle et lAdmin" /></img>
>     </div>
>     
>     <p class="standard">
>       N&#8217;hésitez pas à naviguer dans l&#8217;interface d&#8217;Administration de Django pour découvrir les nombreuses fonctionnalités offertes, mais avant de créer des articles, nous devons préalablement déterminer le chemin du répertoire &#8220;<i>media</i>&#8221; où seront notamment placées les images liées aux articles. Pour cela, nous allons préciser la valeur de la constante <i>MEDIA_ROOT</i> dans le fichier <i>settings.py</i>, puis créer une nouvelle route destinée à servir les fichiers statiques contenus dans ce répertoire &#8220;<i>media</i>&#8220;.
>     </p>
>     
>     <blockquote>
>       <div class="aparte">
>         Pour savoir comment faire appel à des fichiers statiques (fichiers javascript, feuilles de style, images, …) sous Django, n&#8217;hésitez pas à consulter cette page : <a href="http://docs.djangoproject.com/en/dev/howto/static-files/" target="_blank">How to Serve Static Files</a>. Il y est notamment rappelé que Django n&#8217;a pas vocation à gérer les fichiers statiques car il préfère déléguer cette tâche au serveur Web, dont c&#8217;est une des responsabilités. Cependant, en mode développement, c&#8217;est exceptionnellement Django qui s&#8217;acquitte de cette tâche, et la page mentionnée ci-dessus décrit la manière de procéder (et c&#8217;est cette méthode que nous allons mettre en oeuvre dans notre exemple).
>       </div>
>     </blockquote>
>     
>     <p class="standard">
>       Nous allons donc modifier le fichier <i>settings.py</i> pour préciser le nom du répertoire contenant les fichiers statiques …
>     </p>
>     
>     <div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
>       <div class="python codecolorer">
>         <span class="co1"># settings.py</span><br /> <span class="co1"># Absolute path to the directory that holds media.</span><br /> <span class="co1"># Example: "/home/media/media.lawrence.com/"</span><br /> MEDIA_ROOT <span class="sy0">=</span> <span class="kw3">os</span>.<span class="me1">path</span>.<span class="me1">join</span><span class="br0">&#40;</span>ROOT_DIR<span class="sy0">,</span> <span class="st0">'media'</span><span class="br0">&#41;</span>
>       </div>
>     </div>
>     
>     <p class="standard">
>       … puis créer ce répertoire …
>     </p>
>     
>     <div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
>       <div class="bash codecolorer">
>         &nbsp; <span class="kw2">mkdir</span> media<br /> &nbsp; <span class="kw2">chmod</span> <span class="re5">-R</span> <span class="nu0">777</span> media
>       </div>
>     </div>
>     
>     <p class="standard">
>       … et enfin, créer une route responsable des contenus statiques, en mentionnant dans le fichier <i>urls.py</i> la constante <i>MEDIA_ROOT</i> précédemment définie :
>     </p>
>     
>     <div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
>       <div class="python codecolorer">
>         <span class="co1"># urls.py</span><br /> <span class="co1"># …</span><br /> <br /> <span class="kw1">if</span> settings.<span class="me1">DEBUG</span>:<br /> &nbsp; &nbsp; urlpatterns +<span class="sy0">=</span> patterns<span class="br0">&#40;</span><span class="st0">''</span><span class="sy0">,</span><br /> &nbsp; &nbsp; &nbsp; &nbsp; <span class="br0">&#40;</span>r<span class="st0">'^site-media/(?P<path>.*)$'</span><span class="sy0">,</span> <span class="st0">'django.views.static.serve'</span><span class="sy0">,</span> <span class="br0">&#123;</span><span class="st0">'document_root'</span>: settings.<span class="me1">MEDIA_ROOT</span> <span class="br0">&#125;</span><span class="br0">&#41;</span><span class="sy0">,</span><br /> &nbsp; &nbsp; <span class="br0">&#41;</span>
>       </div>
>     </div>
>     
>     <blockquote>
>       <div class="aparte">
>         Noter que nous avons choisi <i>site-media</i> pour URL : évitez d&#8217;utiliser l&#8217;URL <i>media</i>, car elle est déjà réservée par l&#8217;Admin de Django.<br />
>       </div>
>     </blockquote>
>     
>     <p class="standard">
>       L&#8217;Admin de Django facilite notamment la saisie des champs de type &#8220;slug&#8221;. Pour cela, nous allons ajouter une ligne dans chacune des classes <i>XXXAdmin</i> concernées par un champ de type &#8220;<i>slug</i>&#8221; :
>     </p>
>     
>     <div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
>       <div class="python codecolorer">
>         <span class="co1"># shop/models.py</span><br /> <span class="co1"># …</span><br /> <span class="kw1">class</span> SportAdmin<span class="br0">&#40;</span>admin.<span class="me1">ModelAdmin</span><span class="br0">&#41;</span>:<br /> &nbsp; <span class="co1"># ...</span><br /> &nbsp; prepopulated_fields <span class="sy0">=</span> <span class="br0">&#123;</span><span class="st0">"slug"</span>: <span class="br0">&#40;</span><span class="st0">"name"</span><span class="sy0">,</span><span class="br0">&#41;</span><span class="br0">&#125;</span><br /> <br /> <span class="kw1">class</span> ItemAdmin<span class="br0">&#40;</span>admin.<span class="me1">ModelAdmin</span><span class="br0">&#41;</span>:<br /> &nbsp; <span class="co1"># ... &nbsp;</span><br /> &nbsp; prepopulated_fields <span class="sy0">=</span> <span class="br0">&#123;</span><span class="st0">"slug"</span>: <span class="br0">&#40;</span><span class="st0">"name"</span><span class="sy0">,</span><span class="br0">&#41;</span><span class="br0">&#125;</span>
>       </div>
>     </div>
>     
>     <p class="standard">
>       A présent, lorsque vous créez ou éditez un sport ou un article, à mesure que vous remplissez le champ &#8220;<i>name</i>&#8220;, vous remarquerez que le champ &#8220;<i>slug</i>&#8221; se met à jour automatiquement. Sympa !
>     </p>
>     
>     <p class="standard">
>       Nous allons à présent mettre en oeuvre une fonctionnalité très intéressante de l&#8217;Admin Django : les formulaires <i>inline</i>. Cette fonctionnalité permet notamment de faciliter la saisie de plusieurs enregistrements dans le cadre d&#8217;une relation <i>one-to-many</i>. Ainsi, dans notre exemple, lorsque nous créons ou éditons un sport, nous allons également autoriser l&#8217;édition et l&#8217;ajout d&#8217;articles associés au sport courant. Pour cela, nous modifions comme suit le fichier <i>shop/models.py</i> :
>     </p>
>     
>     <div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
>       <div class="python codecolorer">
>         &nbsp; <span class="co1"># shop/models.py</span><br /> &nbsp; <span class="co1"># …</span><br /> &nbsp; <br /> &nbsp;<span class="kw1">class</span> Item<span class="br0">&#40;</span>models.<span class="me1">Model</span><span class="br0">&#41;</span>:<br /> &nbsp; &nbsp; <span class="co1"># …</span><br /> &nbsp; &nbsp; <br /> &nbsp;<span class="kw1">class</span> ItemInline<span class="br0">&#40;</span>admin.<span class="me1">TabularInline</span><span class="br0">&#41;</span>:<br /> &nbsp; &nbsp; &nbsp; model <span class="sy0">=</span> Item<br /> &nbsp; &nbsp; &nbsp; extra <span class="sy0">=</span> <span class="nu0"></span><br /> &nbsp; &nbsp; &nbsp; <br /> &nbsp;<span class="kw1">class</span> SportAdmin<span class="br0">&#40;</span>admin.<span class="me1">ModelAdmin</span><span class="br0">&#41;</span>: <br /> &nbsp; &nbsp; &nbsp; <span class="co1"># …</span><br /> &nbsp; &nbsp; &nbsp; inlines <span class="sy0">=</span> <span class="br0">&#91;</span>ItemInline<span class="br0">&#93;</span>
>       </div>
>     </div>
>     
>     <p class="standard">
>       Nous venons donc de créer une nouvelle classe <i>ItemInline</i> héritant de <i>admin.TabularInline</i>, puis nous avons ajouté un attribut <i>inlines</i> dans la classe <i>SportAdmin</i>, faisant référence à la classe <i>ItemInline</i>. A présent, si vous créez ou éditez un nouveau sport, vous remarquerez que le formulaire principal embarque désormais plusieurs formulaires associés à la classe &#8220;<i>Item</i>&#8220;. Bon, là je dois admettre que cela donne un résultat assez chargé compte tenu du nombre d&#8217;attributs définis dans la classe <i>Item</i>. Cet exemple avait essentiellement pour but de vous faire connaître l&#8217;existence des formulaires <i>inlines</i> dans l&#8217;Admin de Django. Ce type de formulaires imbriqués est sans doute plus adapté dans le cas d&#8217;une relation one-to-many dans laquelle le modèle &#8220;enfant&#8221; est moins riche … Cela étant, Django propose un système de formulaires orientés objets qui nous permettraient de définir une version &#8220;allégée&#8221; des formulaires &#8220;<i>ItemForm</i>&#8221; et donc davantage candidate à une imbrication dans un formulaire parent. Nous pourrions alors les utiliser dans notre exemple, mais comme nous n&#8217;avons pas encore étudié les formulaires orientés Objet de Django, nous remettons ces améliorations à plus tard … Pour l&#8217;heure, soyez conscients de l&#8217;existence de ces formulaires <i>inlines</i>, qui peuvent s&#8217;avérer très pratiques.
>     </p>
>     
>     <blockquote>
>       <div class="aparte">
>         Si vous souhaitez dès à présent en savoir plus sur l&#8217;imbrication de formulaires dans l&#8217;Admin de Django, et notamment connaître les différentes options possibles (model, extra …), je vous invite à consulter le chapitre consacré aux <a href="http://docs.djangoproject.com/en/1.2/ref/contrib/admin/#inlinemodeladmin-objects" target="_blank">objets de la classe InlineModelAdmin</a>.<br /><br /> Sachez que ces objets sont également utilisables dans le cadre d&#8217;une relation de type <i>many-to-many</i>. Quant aux formulaires orientés objets proposés par Django, plusieurs chapitres de la <a href="http://docs.djangoproject.com/en/1.2/" target="_blank">documentation de Django</a> leur sont consacrés, mais nous aurons l&#8217;occasion d&#8217;y revenir.
>       </div>
>     </blockquote>
>     
>     <p class="standard">
>       Et pour finir notre étude de l&#8217;Admin, nous allons rédiger une méthode qui va nous permettre de modifier le statut d&#8217;un article (attribut dénommé &#8220;<i>public</i>&#8221; de la classe <i>Item</i>) à partir de la liste des articles. Cela nous donne l&#8217;occasion de voir comment afficher des colonnes personnalisées dans la vue &#8220;<i>liste</i>&#8221; et d&#8217;ajouter des fonctionnalités supplémentaires dans l&#8217;Admin. Nous allons donc créer une nouvelle méthode permettant d&#8217;inverser le statut d&#8217;un article, puis ajouter une route pointant sur cette méthode, et enfin ajouter une colonne dans la liste des articles, affichant le lien permettant d&#8217;appeler cette méthode. <p class="standard">
>         Nous ajoutons donc la méthode <i>toggle_public</i> dans le fichier <i>shop/views.py</i> &#8230;
>       </p>
>       
>       <div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
>         <div class="python codecolorer">
>           <span class="co1"># shop/views.py</span><br /> <span class="kw1">from</span> shop.<span class="me1">models</span> <span class="kw1">import</span> Item<br /> <span class="kw1">from</span> django.<span class="me1">http</span> <span class="kw1">import</span> HttpResponseRedirect<br /> <span class="kw1">from</span> django.<span class="me1">core</span>.<span class="me1">urlresolvers</span> <span class="kw1">import</span> reverse<br /> <br /> <span class="kw1">def</span> toggle_public<span class="br0">&#40;</span>request<span class="sy0">,</span> <span class="kw2">id</span><span class="br0">&#41;</span>:<br /> &nbsp; item <span class="sy0">=</span> Item.<span class="me1">objects</span>.<span class="me1">get</span><span class="br0">&#40;</span>pk<span class="sy0">=</span><span class="kw2">id</span><span class="br0">&#41;</span><br /> &nbsp; item.<span class="me1">public</span> <span class="sy0">=</span> <span class="kw1">not</span> item.<span class="me1">public</span><br /> &nbsp; item.<span class="me1">save</span><span class="br0">&#40;</span><span class="br0">&#41;</span><br /> &nbsp; <span class="kw1">return</span> HttpResponseRedirect<span class="br0">&#40;</span>reverse<span class="br0">&#40;</span><span class="st0">"admin:shop_item_changelist"</span><span class="br0">&#41;</span><span class="br0">&#41;</span>
>         </div>
>       </div>
>       
>       <p class="standard">
>         … puis créons une route pointant sur cette nouvelle méthode …
>       </p>
>       
>       <div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
>         <div class="python codecolorer">
>           &nbsp; <span class="co1"># urls.py</span><br /> &nbsp; <span class="co1"># …</span><br /> &nbsp; <span class="br0">&#40;</span>r<span class="st0">'^admin/item_toggle/(?P<id><span class="es0">\\</span>d+)/$'</span><span class="sy0">,</span> <span class="st0">'shop.views.toggle_public'</span><span class="br0">&#41;</span><span class="sy0">,</span><br /> &nbsp; <span class="br0">&#40;</span>r<span class="st0">'^admin/'</span><span class="sy0">,</span> include<span class="br0">&#40;</span>admin.<span class="kw3">site</span>.<span class="me1">urls</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">,</span><br /> &nbsp; <span class="co1"># …</span>
>         </div>
>       </div>
>       
>       <p class="standard">
>         … puis une méthode de la classe <i>Item</i> permettant d&#8217;afficher un lien pointant sur cette méthode …
>       </p>
>       
>       <div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
>         <div class="python codecolorer">
>           &nbsp; <span class="co1"># shop/models.py</span><br /> &nbsp; <span class="kw1">from</span> django.<span class="me1">core</span>.<span class="me1">urlresolvers</span> <span class="kw1">import</span> reverse<br /> &nbsp; <span class="co1"># ...</span><br /> &nbsp; <span class="kw1">class</span> Item<span class="br0">&#40;</span>models.<span class="me1">Model</span><span class="br0">&#41;</span>:<br /> &nbsp; <span class="co1"># …</span><br /> &nbsp; &nbsp;<span class="kw1">def</span> toggle_public<span class="br0">&#40;</span><span class="kw2">self</span><span class="br0">&#41;</span>:<br /> &nbsp; &nbsp; &nbsp;<span class="kw1">return</span> <span class="st0">'<a href="%s">Toggle</a>'</span> % reverse<span class="br0">&#40;</span><span class="st0">'shop.views.toggle_public'</span><span class="sy0">,</span> args<span class="sy0">=</span><span class="br0">&#91;</span><span class="kw2">self</span>.<span class="me1">pk</span><span class="br0">&#93;</span><span class="br0">&#41;</span><br /> &nbsp; &nbsp;toggle_public.<span class="me1">allow_tag</span> <span class="sy0">=</span> <span class="kw2">True</span><br /> &nbsp; <span class="co1"># …</span>
>         </div>
>       </div>
>       
>       <p class="standard">
>         … et enfin, on affiche cette nouvelle colonne dans l&#8217;Admin :
>       </p>
>       
>       <div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
>         <div class="python codecolorer">
>           &nbsp; <span class="co1"># models.py</span><br /> &nbsp; <span class="co1"># …</span><br /> &nbsp; <span class="kw1">class</span> ItemAdmin<span class="br0">&#40;</span>admin.<span class="me1">ModelAdmin</span><span class="br0">&#41;</span>:<br /> &nbsp; &nbsp; list_display <span class="sy0">=</span> <span class="br0">&#91;</span> <span class="st0">'name'</span><span class="sy0">,</span> <span class="st0">'slug'</span><span class="sy0">,</span> <span class="st0">'description'</span><span class="sy0">,</span> <span class="st0">'picture'</span><span class="sy0">,</span> <span class="st0">'stock'</span><span class="sy0">,</span> <span class="st0">'price'</span><span class="sy0">,</span> <span class="st0">'public'</span><span class="sy0">,</span> <span class="st0">'toggle_public'</span><span class="sy0">,</span> <span class="st0">'sport'</span> <span class="br0">&#93;</span><br /> &nbsp; <span class="co1"># ...</span>
>         </div>
>       </div>
>       
>       <p class="standard">
>         Voilà qui clôt le chapitre consacré à la découverte de l&#8217;Admin de Django. Sachez qu&#8217;il existe d&#8217;autres fonctionnalités que nous n&#8217;avons pas explorées (notamment la pagination) et sachez également que si vos besoins le justifient, vous avez la possibilité de rédéfinir les méthodes et les templates de l&#8217;Admin. Pour ceux qui souhaitent en savoir plus sur cette possibilité, il existe plusieurs ressources disponibles sur Internet. Je citerais notamment la documentation officielle de Django qui consacre un paragraphe à ce sujet : <a href="http://docs.djangoproject.com/en/dev/ref/contrib/admin/#overriding-admin-templates" target="_blank">Overriding Admin Templates</a>. Pour ce qui concerne plus généralement l&#8217;Admin, si vous souhaitez connaître l&#8217;ensemble des fonctionnalités disponibles, voici les deux chapitres de la documentation qui traitent de ce sujet : <a href="http://docs.djangoproject.com/en/1.2/ref/contrib/admin/" target="_blank">Admin Site</a> et <a href="http://docs.djangoproject.com/en/1.2/ref/contrib/admin/actions/" target="_blank">Admin Actions</a>.
>       </p>
>       
>       <h3>
>         Conclusion (partielle)
>       </h3>
>       
>       <p class="standard">
>         Aujourd&#8217;hui, nous avons étendu nos connaissances du framework Django, en abordant le modèle dans ses grandes lignes, et en exploitant certaines fonctionnalités proposées par l&#8217;Admin de Django. Au cours d&#8217;un prochain article, nous développerons une page qui nous permettra de lister les différents articles disponibles. Cette page comportera un moteur de recherche (assez basique), les résultats seront paginés et pourront être triés à partir des en-têtes de colonnes. Ce sera donc l&#8217;occasion de consolider certaines notions déja étudiées, et d&#8217;aborder de nouveaux concepts par la pratique, parmi lesquels l&#8217;organisation et la réutilisation des templates, la pagination, l&#8217;interrogation des données en base de données à l&#8217;aide des <i>ModelManagers</i>, la génération de vignettes, la création de <i>fixtures</i> … <i>Stay tuned !</i>
>       </p>