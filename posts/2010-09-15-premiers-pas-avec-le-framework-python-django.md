
<p class="standard">
  Bonjour à toutes et à tous,
</p>

<p class="standard">
  Aujourd&#8217;hui, nous allons faire la connaissance de <a href="http://www.djangoproject.com/">Django</a>, le framework Web à l&#8217;usage des perfectionnistes soumis à des délais (ce n&#8217;est pas moi qui le dis, ce sont les pères fondateurs du Framework eux-mêmes !).
</p>

<p class="standard">
  Il s&#8217;agira d&#8217;une simple initiation au framework, destinée essentiellement aux développeurs qui souhaitent, comme moi, découvrir l&#8217;outil à travers un cas concret d&#8217;une affligeante banalité, puisque nous allons nous limiter pour l&#8217;heure à un très modeste &#8220;Hello, World&#8221;. Mais rassurez-vous, au-delà de cet exemple simple, pour ne pas dire simplet, ce sera surtout l&#8217;occasion d&#8217;aborder par la pratique quelques concepts-clés du Framework. A mesure que nous avancerons dans ce projet (d&#8217;une ambition démesurée), nous nous attarderons sur certains aspects du framework, parfois pour les comparer aux autres frameworks Web. Enfin, je n&#8217;exclus pas de rédiger d&#8217;autres articles sur Django s&#8217;il s&#8217;avère que ce billet reçoit quelques échos favorables.
</p>

<p class="standard" style="font-weight: bold;">
  <em>A qui s&#8217;adresse ce tutoriel ? Quels sont les pré-requis pour suivre ce tutoriel ? Je n&#8217;y connais rien en Python, est-ce que ça vaut la peine que je poursuive la lecture de cet article ? Et puis qu&#8217;ai-je à gagner à découvrir un nouveau framework, le mien me convient parfaitement, merci ! Je ne connais pas non plus les autres frameworks, ai-je intérêt à lire ce billet ?</em>
</p>

<p class="standard">
  Houlala, nous ne sommes pas encore entrés dans le vif du sujet que déjà les questions fusent de toute part ! Votre enthousiasme fait plaisir à voir.
</p>

<p class="standard">
  Bon, vu la manière dont j&#8217;ai introduit le sujet, vous vous doutez bien que ce tutoriel s&#8217;adresse avant tout à des développeurs qui ne connaissent absolument pas Django ou qui, comme moi, en ont vaguement entendu parler mais souhaitent creuser la question. Si vous vous targuez de maîtriser l&#8217;outil, vous risquez fort de vous ennuyer à la lecture de cet article. Pour tirer pleinement profit de ce tutoriel, il est préférable de posséder quelques notions de développement, telles que le modèle MVC (Modèle-Vue-Contrôleur) ou la programmation objet. En revanche, si vous ne connaissez pas d&#8217;autre framework, ni le langage Python, cela ne devrait pas être un frein à la compréhension du tutoriel. Je mentionnerai sans doute d&#8217;autres frameworks tout au long de cet article mais uniquement dans le but d&#8217;apporter un éclairage supplémentaire. Quant au langage Python, je l&#8217;ai moi-même découvert en même temps que Django, la méconnaissance de ce langage ne semble donc pas constituer un obstacle insurmontable pour découvrir Django.
</p>

<p class="standard">
  Enfin, pour suivre ce tutoriel, il vous faudra, et c&#8217;est sans doute le principal pré-requis, disposer d&#8217;un ordinateur sur lequel sont installés Python et Django ! Si ce n&#8217;est pas votre cas, pas de panique, l&#8217;installation des outils nécessaires fait justement l&#8217;objet du prochain chapitre.
</p>

## Installation de Python et Django

<p class="standard">
  L&#8217;installation de Python et de Django ne pose pas de problème particulier, aussi vais-je aborder cet aspect de manière très succincte.
</p>

<p class="standard">
  Tout d&#8217;abord, il faut savoir que la version actuelle de Django (1.2) requiert Python dans ses versions 2.4 à 2.7. Bonne nouvelle pour les utilisateurs d&#8217;une distribution Linux ou de MacOS, il est fort probable que Python soit déjà installé sur votre machine. Pour vous en assurer, ouvrez une console et saisissez simplement la commande suivante :
</p>

<div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="python codecolorer">
    &nbsp;python
  </div>
</div>

<p class="standard">
  Vous devriez alors voir apparaître une invitation de commande Python assez semblable à celle-ci :
</p>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    &nbsp; Python 2.5.2 <span class="br0">&#40;</span>r252:<span class="nu0">60911</span>, Jan <span class="nu0">20</span> <span class="nu0">2010</span>, <span class="nu0">23</span>:<span class="nu0">16</span>:<span class="nu0">55</span><span class="br0">&#41;</span><br /> &nbsp; <span class="br0">&#91;</span>GCC 4.3.2<span class="br0">&#93;</span> on linux2<br /> &nbsp; Type <span class="st_h">'help'</span>, <span class="st_h">'copyright'</span>, <span class="st_h">'credits'</span> or <span class="st_h">'license'</span> <span class="kw1">for</span> <span class="kw2">more</span> information.<br /> &nbsp; <span class="sy0">>>></span>
  </div>
</div>

<p class="standard">
  Si ce n&#8217;est pas le cas, ou si vous utilisez l&#8217;OS Windows, il vous suffira de télécharger Python et l&#8217;installer :
</p>

> <div class="aparte">
>   Télécharger Python : <a href="http://www.python.org/download/">http://www.python.org/download/</a></p> <p>
>     <strong><span style="text-decoration: underline;">Rappel</span></strong> : prenez soin d&#8217;installer une version 2.x de Python. En effet, à l&#8217;heure où j&#8217;écris ces lignes, la dernière version de Python est la 3.1.2 mais <strong>Django est pour l&#8217;heure incompatible avec les versions 3.x de Python</strong>.
>   </p>
>   
>   <p>
>     <span style="text-decoration: underline;">Nota</span> : A partir de la version 2.5, Python inclut Sqlite dans sa distribution. Privilégiez donc une version supérieure ou égale à 2.5.
>   </p>
> </div>

<p class="standard">
  A présent, il est temps de télécharger et installer Django :
</p>

> <div class="aparte">
>   Télécharger Django : <a href="http://www.djangoproject.com/download/">http://www.djangoproject.com/download/</a></p> <p>
>     Documentation d&#8217;installation : <a href="http://docs.djangoproject.com/en/1.2/topics/install/#installing-official-release">http://docs.djangoproject.com/en/1.2/topics/install/#installing-official-release</a>
>   </p>
>   
>   <p>
>     <span style="text-decoration: underline;">Nota</span> : Pour les utilisateurs de distributions Linux, il est probable que votre gestionnaire de paquetages vous permette d&#8217;installer Django. Assurez-vous néanmoins de bien installer la version 1.2, sur laquelle se base ce tutoriel.
>   </p>
> </div>

<p class="standard">
  Si vous avez opté pour le téléchargement de l&#8217;archive de la distribution Django, l&#8217;installation consiste principalement à décompresser le contenu de l&#8217;archive dans un répertoire temporaire et à lancer le script d&#8217;installation. Voici un exemple en partant du principe que votre archive se trouve dans le répertoire ~/Temp :
</p>

<div class="codecolorer-container text vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="text codecolorer">
    &nbsp; &nbsp; cd ~/Temp<br /> &nbsp; &nbsp; tar -zxvf Django-1.2.1.tar.gz<br /> &nbsp; &nbsp; cd Django-1.2.1<br /> &nbsp; &nbsp; sudo python setup.py install
  </div>
</div>

<p class="standard">
  L&#8217;installation devrait se dérouler normalement.
</p>

<p class="standard">
  Le script Django permettant d&#8217;initialiser un projet s&#8217;appelle <em>django-admin.py</em>. Il se trouve dans votre distribution Django fraîchement installée. Normalement, lors de l&#8217;installation, Python a dû mettre à jour le PATH pour rendre ce script disponible depuis n&#8217;importe quel emplacement. Pour nous en assurer, lançons les commandes suivantes :
</p>

<div class="codecolorer-container text vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="text codecolorer">
    &nbsp; cd ~<br /> &nbsp; django-admin.py
  </div>
</div>

<p class="standard">
  Si tout s&#8217;est déroulé correctement, vous devriez obtenir le résultat suivant (et notamment la liste des sous-commandes Django disponibles) :
</p>

<div class="codecolorer-container text vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="text codecolorer">
    &nbsp; Usage: django-admin.py subcommand [options] [args]<br /> &nbsp; &nbsp; &nbsp; &nbsp; .....<br /> &nbsp; Type 'django-admin.py help <subcommand>' for help on a specific subcommand.<br /> <br /> &nbsp; Available subcommands:<br /> &nbsp; &nbsp; cleanup<br /> &nbsp; &nbsp; compilemessages<br /> &nbsp; &nbsp; createcachetable<br /> &nbsp; &nbsp; dbshell<br /> &nbsp; &nbsp; diffsettings<br /> &nbsp; &nbsp; ...
  </div>
</div>

<p class="standard">
  Si vous n&#8217;obtenez pas le résultat escompté, il va falloir rendre le script disponible. Pour les utilisateurs de Linux, cela consistera par exemple à créer un lien symbolique pointant sur ce fichier pour y accéder depuis n&#8217;importe quel emplacement. Quant aux utilisateurs de Windows, il leur suffira de copier ce fichier dans un répertoire inclus dans la variable d&#8217;environnement PATH, ou bien encore modifier la variable PATH en y ajoutant le chemin du répertoire parent du fichier <em>django-admin.py</em>
</p>

<p class="standard" style="font-weight: bold;">
  <em>Il est bien gentil, lui, mais elle se trouve où cette fichue distribution ? Comment je fais pour le retrouver ce fameux fichier?</em>
</p>

> <div class="aparte">
>   Pour connaître l&#8217;emplacement de vos paquetages Python sur votre disque dur, ouvrez une console (Linux, MacOS) ou une fenêtre DOS et saisissez l&#8217;instruction suivante : [php]python -c &#8216;from distutils.sysconfig import get_python_lib; print get_python_lib()&#8217; [/php]</p> <p>
>     C&#8217;est dans le répertoire qui s&#8217;affiche que se trouvent tous les paquets Python, parmi lesquels votre distribution Django. Exemple d&#8217;emplacement du fichier django-admin.py dans une distribution Linux :
>   </p>
>   
>   <div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
>     <div class="bash codecolorer">
>       &nbsp; <span class="sy0">/</span>usr<span class="sy0">/</span>lib<span class="sy0">/</span>python2.7<span class="sy0">/</span>site-packages<span class="sy0">/</span>django<span class="sy0">/</span>bin<span class="sy0">/</span>django-admin.py
>     </div>
>   </div>
> </div>

<p class="standard">
  Création d&#8217;un lien symbolique sous Linux ou MacOS :
</p>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    &nbsp; <span class="kw2">ln</span> <span class="re5">-s</span> <span class="sy0">/</span>usr<span class="sy0">/</span>lib<span class="sy0">/</span>python2.7<span class="sy0">/</span>site-packages<span class="sy0">/</span>django<span class="sy0">/</span>bin<span class="sy0">/</span>django-admin.py <span class="sy0">/</span>usr<span class="sy0">/</span>local<span class="sy0">/</span>bin
  </div>
</div>

<p class="standard">
  Maintenant que tout le monde est là, nous pouvons aborder la phase la plus intéressante : la création de notre premier projet Django !
</p>

## Création d&#8217;un projet Django

<p class="standard">
  A présent, nous disposons d&#8217;un environnement de développement qui nous permet de créer notre premier projet Django. Pour cela, nous allons lancer le script Django de création d&#8217;un projet, le fameux <em>django-admin.py</em>.
</p>

> <div class="aparte">
>   La question que l&#8217;on se pose constamment lorsque l&#8217;on utilise un framework permettant notamment de générer l&#8217;arborescence d&#8217;un nouveau projet (Ruby on Rails, Symfony, Grails, Zend_Tool &#8230;) est : &#8220;<em>Dois-je créer le répertoire qui va contenir mon projet avant de lancer la commande ou est-ce le script du framework qui s&#8217;en charge ?</em>&#8220;.</p> <p>
>     Dans le cas de Django, lorsque l&#8217;on exécute la sous-commande de création d&#8217;un nouveau projet, <strong>un nouveau répertoire portant le nom du projet est créé</strong> et les fichiers générés sont placés à la racine de ce nouveau répertoire.
>   </p>
> </div>

<p class="standard">
  Nous allons donc ouvrir (si ce n&#8217;est pas déjà fait) un terminal, nous placer dans le répertoire contenant nos projets Django (dans mon cas, ce répertoire s&#8217;appelle &#8220;<em>workspace-django</em>&#8220;, il est situé dans ma &#8220;home&#8221;) et exécuter le script de création du projet :
</p>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    &nbsp; <span class="kw3">cd</span> ~<span class="sy0">/</span>workspace-django<br /> &nbsp; django-admin.py startproject monprojet<br /> &nbsp; <span class="co0"># Ca y est, le projet est initialisé, allons voir ce qu'il contient :</span><br /> &nbsp; <span class="kw3">cd</span> monprojet<br /> &nbsp; <span class="kw2">ls</span> <span class="re5">-l</span>
  </div>
</div>

> <div class="aparte">
>   Voici le contenu du projet immédiatement après sa création :</p> <p>
>     (1) __init.py__<br /> (2) manage.py<br /> (3) settings.py<br /> (4) urls.py
>   </p>
>   
>   <p>
>     <strong>(1)</strong> Ce fichier sert à indiquer à Python que le répertoire courant est un <strong>package</strong> Python. Nous reviendrons brièvement sur la notion de package et de module en Python. Sachez juste pour l&#8217;heure que nous ne modifierons pas ce fichier.<br /> <strong>(2)</strong> Pour schématiser, c&#8217;est un <em>wrapper</em>, un raccourci en quelque sorte, vers le script original <em>django-admin.py</em>, placé ici pour des raisons pratiques. Désormais, c&#8217;est ce script que nous invoquerons pour exécuter des commandes Django. Nous ne modifierons pas ce fichier.<br /> <strong>(3)</strong> C&#8217;est dans ce fichier que nous définirons la configuration de notre projet. Nous aurons l&#8217;occasion d&#8217;y revenir.<br /> <strong>(4)</strong> C&#8217;est dans ce fichier que l&#8217;on définit le <strong>routing</strong> de nos applications. Il contient essentiellement les URLs utilisables dans notre projet. Nous aurons également l&#8217;occasion d&#8217;y revenir.
>   </p>
> </div>

<p class="standard">
  Maintenant que notre projet est initialisé, nous allons l&#8217;exécuter. Pour cela, nous allons démarrer le serveur de développement intégré nativement à Django : ouvrez un nouveau terminal (ou un nouvel onglet) et saisissez les commandes suivantes :
</p>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    &nbsp; <span class="kw3">cd</span> ~<span class="sy0">/</span>workspace-django<span class="sy0">/</span>monprojet<br /> &nbsp; python manage.py runserver
  </div>
</div>

<p class="standard">
  Le serveur démarre et nous pouvons aller voir notre projet en consultant cette url : <a href="http://localhost:8000" target="_blank">http://localhost:8000</a>. Par défaut, le serveur de Django utilise le port 8000. Si par hasard, ce port était déjà utilisé, vous pouvez démarrer le serveur en passant un port différent :
</p>

<div class="codecolorer-container text vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="text codecolorer">
    &nbsp; python manage.py runserver 8090
  </div>
</div>

> <div class="aparte">
>   <strong><span style="text-decoration: underline;">Nota bene</span> : </strong>Le serveur HTTP utilisé par défaut est un <strong>serveur de développement</strong>, comparable au serveur Webrick de Rails. <strong>Vous ne devez en aucun cas utiliser ce serveur en production !!!</strong>.<br /> Pour les plus pressés d&#8217;entre vous qui souhaitent en savoir plus sur le déploiement d&#8217;un projet Django en production, je vous invite à visiter la page de la documentation officielle qui traite de ce sujet : <a href="http://djangobook.com/en/2.0/chapter12/" target="_blank">Deploying Django</a>.
> </div>

<p class="standard">
  Vous avez pu consulter la page par défaut de votre projet ; Django vous y souhaite la bienvenue et vous confirme par la même occasion que votre projet a été correctement initialisé. Bon le design de la page n&#8217;a vraiment pas de quoi faire rêver, mais l&#8217;on s&#8217;en contentera &#8230; Par ailleurs, nous y apprenons que nous sommes en mode DEBUG, que l&#8217;on peut définir les paramètres de connexion à une base de données dans le fichier <em>settings.py</em> ou bien encore que, si nous le souhaitons, nous avons la possibilité de créer une application à l&#8217;aide de la commande <em>python manage.py startapp [myapp]</em>. Comme nous ne sommes pas contrariants, nous allons nous exécuter et créer notamment une application destinée à contenir notre très attendu &#8220;Hello World&#8221; ! Revenons donc à notre terminal et créons cette application que nous appellerons &#8220;hello&#8221;, avec toute l&#8217;originalité qui sied aux développeurs dans de tels cas &#8230;
</p>

<div class="codecolorer-container text vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="text codecolorer">
    &nbsp; python manage.py startapp hello
  </div>
</div>

<p class="standard">
  Cette commande a ajouté à la racine de notre projet un répertoire nommé <em>hello</em>, qui correspond à une &#8220;application&#8221; dans le jargon de Django.
</p>

<p class="standard" style="font-weight: bold; font-style: italic;">
  C&#8217;est quoi une application au juste ? Quelle est la différence entre &#8220;projet&#8221; et &#8220;application&#8221; ?
</p>

<p class="standard">
  Bon, je vous avoue que je redoutais un peu cette question, car je manque moi-même du recul nécessaire pour décrire avec exactitude les contours d&#8217;une application selon la terminologie employée par Django. Mais je vais m&#8217;efforcer tout de même d&#8217;y répondre de manière relativement claire en me basant sur un exemple concret et je vous prie de m&#8217;excuser par avance si ma réponse reste approximative et/ou trop schématique. Admettons que vous souhaitiez développer un site Internet consacré à votre sport préféré, le curling ou le crachat de pépins acrobatique. Votre site se déclinera sans doute en plusieurs grandes fonctionnalités : vous souhaiterez sans doute développer un module principal dans lequel vous afficherez les actualités liées à votre sport favori, un module de vente d&#8217;articles et vêtements nécessaires à la pratique de votre sport préféré, une gallerie de photographies ou de vidéos, un forum dans lequel pourront s&#8217;exprimer tous ceux qui souhaitent partager leur passion immodérée du curling, et j&#8217;en passe. Dans cet exemple, je serais tenté de dire que le site internet constituerait le projet au sens de Django, tandis que le forum, la gallerie photos, le module de news, etc. en constitueraient des applications. Mais une des notions qui m&#8217;apparaît fondamentale pour définir une application, c&#8217;est la possibilité de réutiliser une application dans un autre projet (exemple avec le forum). Noter que chaque application peut contenir ses propres classes du Modèle (au sens MVC du terme). Quant au projet, je serais tenté de le définir comme un ensemble d&#8217;applications partageant des paramètrages communs (base de données, définitions de constantes, layout commun, nom de domaine &#8230;). Dans notre exemple, compte tenu de la modestie et de la pauvreté fonctionnelle du projet que nous développons, celui-ci ne comportera qu&#8217;une seule application.
</p>

<p class="standard">
  Quoi qu&#8217;il en soit, observons la structure de notre projet après que nous avons créé notre première application :
</p>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    + hello<br /> &nbsp; __init.py__<br /> &nbsp; models.py<br /> &nbsp; test.py<br /> &nbsp; views.py<br /> <br /> __init.py__<br /> manage.py<br /> ........
  </div>
</div>

<p class="standard">
  Maintenant que nous disposons d&#8217;une structure enrichie, nous avons l&#8217;occasion d&#8217;aborder les concepts de <em>packages</em> et <em>modules</em> du langage Python. Il n&#8217;est pas question ici d&#8217;entrer dans le détail, mais simplement de bien s&#8217;entendre sur la terminologie du langage Python, car cela nous sera utile lorsque Django nous signalera en mode DEBUG des exceptions survenues dans tel ou tel module. Pour ceux qui souhaitent approfondir la question de l&#8217;organisation du code des applications Python, je les invite à consulter la documentation officielle de Python : <a href="http://docs.python.org/tutorial/modules.html">Modules in Python</a>.
</p>

> <div class="aparte">
>   <strong><span style="text-decoration: underline;">Packages et modules Python</span> :</strong></p> <p>
>     Les packages et modules Python permettent un découpage et une structuration du code constituant un projet Python.
>   </p>
>   
>   <p>
>     En règle générale, les packages Python correspondent à une organisation des fichiers selon une arborescence sur le disque dur, et l&#8217;on reconnaît les packages Python à la présence du fichier __init.py__ à la racine des répertoires. Ainsi, dans notre exemple, notre projet est lui-même un package nommé <em>monprojet</em>, il contient un sous-paquetage nommé <em>monprojet.hello</em>. Les packages permettent notamment de regrouper des modules.
>   </p>
>   
>   <p>
>     Les modules Python correspondent eux à des fichiers d&#8217;extension *.py qui peuvent contenir plusieurs définitions de classes. Par exemple, dans un projet Django, nous enregistrerons les classes de notre modèle dans le fichier models.py des différentes applications. Ainsi, <em>models</em> est un module qui peut contenir par exemple le code des classe Client, Product, Order, ou bien encore celui des classes Blog, Post &#8230;
>   </p>
>   
>   <p>
>     <strong><span style="text-decoration: underline;">Packages et modules vs projets et applications</span> :</strong>
>   </p>
>   
>   <p>
>     Pour résumer, lorsque l&#8217;on parle de <em>packages</em> et de <em>modules</em>, on envisage le code du point de vue de Python. Tandis que les termes <em>applications</em> et <em>projet</em> décrivent une organisation du code du point de vue de Django. Bien entendu, si l&#8217;on superpose ces différents concepts de Python et Django en alignant leurs contours, il est fort probable que rien ne dépasse &#8230; Ce sont simplement deux manières complémentaires d&#8217;envisager l&#8217;organisation du code.
>   </p>
> </div>

<p class="standard">
  Nous avons donc créé le squelette de notre application, mais dans un projet Django, toute application doit être déclarée dans le fichier <em>settings.py</em> pour être rendue disponible. Nous allons donc modifier ce fichier et ajouter une simple ligne (en toute fin de code) :
</p>

<div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="python codecolorer">
    &nbsp; <span class="co1"># monprojet/settings.py</span><br /> &nbsp; ...<br /> &nbsp; <span class="me1">INSTALLED_APPS</span> <span class="sy0">=</span> <span class="br0">&#40;</span><br /> &nbsp; &nbsp; &nbsp; <span class="st0">'django.contrib.auth'</span><span class="sy0">,</span><br /> &nbsp; &nbsp; &nbsp; <span class="st0">'django.contrib.contenttypes'</span><span class="sy0">,</span><br /> &nbsp; &nbsp; &nbsp; <span class="st0">'django.contrib.sessions'</span><span class="sy0">,</span><br /> &nbsp; &nbsp; &nbsp; <span class="st0">'django.contrib.sites'</span><span class="sy0">,</span><br /> &nbsp; &nbsp; &nbsp; <span class="st0">'django.contrib.messages'</span><span class="sy0">,</span><br /> &nbsp; &nbsp; &nbsp; <span class="co1"># Uncomment the next line to enable the admin:</span><br /> &nbsp; &nbsp; &nbsp; <span class="co1"># 'django.contrib.admin',</span><br /> &nbsp; &nbsp; &nbsp; <span class="co1"># Ici, nous ajoutons notre application :</span><br /> &nbsp; &nbsp; &nbsp; <span class="st0">'hello'</span><span class="sy0">,</span><br /> &nbsp; <span class="br0">&#41;</span>
  </div>
</div>

<p class="standard">
  Profitez de cette modification pour consulter le contenu de ce fichier. C&#8217;est notamment dans ce fichier que l&#8217;on définit le mode debug (<em>DEBUG = True</em>), les paramètres de connexion à la base de données, l&#8217;emplacement où nous enregistrerons les templates, le répertoire &#8220;<em>media</em>&#8221; destiné à accueillir les fichiers statiques (feuilles de style, images, fichiers javascript &#8230;) et bien d&#8217;autre choses. Pour connaître les principaux paramètres enregistrés dans ce fichier, je vous encourage à consulter la page de la documentation de Django traitant de la configuration d&#8217;un projet : <a href="http://docs.djangoproject.com/en/1.2/topics/settings/" target="_blank">Django Settings</a>.
</p>

<p class="standard">
  A présent, nous pouvons nous atteler à la rédaction du code qui va nous permettre d&#8217;afficher le tant attendu &#8220;Hello World&#8221; dans notre navigateur préféré. Cela va consister à :
</p>

1.  Ajouter une URL dans le fichier *monprojet/urls.py*
2.  Ajouter une fonction dans le fichier *monprojet/hello/views.py*
3.  Ecrire la template

> <div class="aparte">
>   Noter le nom du fichier dans lequel nous rédigerons le code de notre fonction : <strong>views.py</strong>. En fait, Django est un Framework Web mettant en oeuvre le principe <strong>MVC</strong>, tel que vous avez l&#8217;habitude de le rencontrer dans d&#8217;autres frameworks. Pour être plus précis, dans le cas de Django, les créateurs du framework préfèrent parler de MTV : <strong>Model-Template-View</strong>. Il s&#8217;agit d&#8217;une nuance dans l&#8217;interprétation du MVC. En fait, sous Django, les fonctions qui s&#8217;apparenteraient dans d&#8217;autres frameworks à des actions ou des contrôleurs sont enregistrées dans le fichier views.py. Cela peut être déroutant pour un développeur familier avec les principes MVC. Sachez simplement que c&#8217;est avant tout affaire de terminologie. Pour l&#8217;heure, acceptez le fait que le code de ce que vous considérez comme vos actions ou contrôleurs se trouve dans le fichier views.py et que c&#8217;est donc dans ce fichier que l&#8217;on trouve la logique correspondant au &#8220;C&#8221; de MVC &#8230; Quant aux vues (dans le sens MVC du terme), elles sont représentées par les templates. Mais fondamentalement, cela ne change en rien les habitudes acquises sur d&#8217;autres frameworks MVC, à partir du moment où vous avez digéré cette subtilité sémantique, quelque peu déroutante, il faut l&#8217;admettre, lorsque l&#8217;on débute sous Django.
> </div>

<p class="standard">
  1. <span style="text-decoration: underline;">Créer une URL (fichier urls.py</span>)
</p>

<p class="standard">
  Pour ajouter l&#8217;URL pointant sur notre méthode, nous allons ajouter une ligne à la fin du fichier <em>urls.py</em> :
</p>

<div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="python codecolorer">
    &nbsp; urlpatterns <span class="sy0">=</span> patterns<span class="br0">&#40;</span><span class="st0">''</span><span class="sy0">,</span><br /> &nbsp; &nbsp; &nbsp; <span class="co1"># Example:</span><br /> &nbsp; &nbsp; &nbsp; <span class="co1"># (r'^monprojet/', include('monprojet.foo.urls')),</span><br /> <br /> &nbsp; &nbsp; &nbsp; <span class="co1"># Uncomment the next line to enable the admin:</span><br /> &nbsp; &nbsp; &nbsp; <span class="co1"># (r'^admin/', include(admin.site.urls)),</span><br /> &nbsp; &nbsp; &nbsp; <span class="br0">&#40;</span>r<span class="st0">'^hello/$'</span><span class="sy0">,</span> <span class="st0">'hello.views.hello'</span><span class="br0">&#41;</span><span class="sy0">,</span><br /> &nbsp; <span class="br0">&#41;</span>
  </div>
</div>

<p class="standard">
  Nous venons simplement de préciser que c&#8217;est la fonction &#8220;hello&#8221; du module &#8220;views&#8221; de l&#8217;application &#8220;hello&#8221; qui doit être appelée lorsque l&#8217;URL invoquée correspond exactement au pattern &#8220;hello/&#8221;. Pour créer des URLS sous Django, vous noterez qu&#8217;il faut connaître un peu les expressions régulières. Ca, je me suis bien gardé de le préciser en introduction de peur de faire fuir le lecteur. J&#8217;avoue que le procédé est assez malhonnête, j&#8217;ai quelques scrupules, mais sincèrement, combien d&#8217;entre vous seraient parvenus jusqu&#8217;ici si j&#8217;avais mentionné les expressions régulières dès le début de cet article ? Et puis, sachez que moi-même je ne suis pas très rompu à la syntaxe des REGEXP, alors voici quelques exemples qui vont vous permettre de gérer 90 % des cas que vous serez amenés à rencontrer dans le cadre de l&#8217;ajout d&#8217;URLs sous Django :
</p>

> <div class="aparte">
>   <strong><span style="text-decoration: underline;">Quelques expressions régulières utiles</span> :</strong></p> <p>
>     - <strong>^videos/</strong> : toute URL commençant par &#8220;videos/&#8221;<br /> - <strong>extract/$</strong> : toute URL se terminant par &#8220;extract/&#8221;<br /> - <strong>^gallery/$</strong> : toute URL correspondant exactement à l&#8217;expression &#8220;gallery/&#8221;<br /> - <strong>^photo/show/(?P<id>\\d+)/$</strong> : toute URL commençant par &#8220;photo/show/&#8221; suivi d&#8217;un nombre (correspondant au paramètre &#8220;id&#8221;)<br /> - <strong>^photo/show/(?P<slug>[a-z0-9\\-]+)/$</strong> : la même chose mais avec un paramètre &#8220;slug&#8221; (ne contenant donc que des chiffres, des tirets et des lettres minuscules).
>   </p>
> </div>

<p class="standard">
  A présent, si nous nous rendons sur la page <a href="http://localhost:8000/hello/">http://localhost:8000/hello/</a>, nous recevons une exception de type &#8220;ViewDoesNotExist&#8221; qui indique que nous sommes sur la bonne voie. En effet, cette erreur implique que notre route a bien été prise en compte et qu&#8217;il nous faut donc rédiger la fonction &#8220;hello.views.hello&#8221;.
</p>

<p class="standard">
  2. <span style="text-decoration: underline;">Rédiger la fonction <em>hello</em> (fichier hello/views.py</span>)
</p>

<p class="standard">
  Voici le code complet du fichier views.py contenant notre fonction &#8220;hello&#8221; :
</p>

<div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="python codecolorer">
    &nbsp; <span class="co1"># monprojet/hello/views.py</span><br /> &nbsp; <span class="kw1">from</span> django.<span class="me1">shortcuts</span> <span class="kw1">import</span> render_to_response<br /> <br /> &nbsp; <span class="kw1">def</span> hello<span class="br0">&#40;</span>request<span class="br0">&#41;</span>:<br /> &nbsp; &nbsp; <span class="kw1">return</span> render_to_response<span class="br0">&#40;</span><span class="st0">'hello.html'</span><span class="br0">&#41;</span>
  </div>
</div>

<p class="standard">
  Si vous découvrez le langage Python, il n&#8217;y a rien de très compliqué. Noter qu&#8217;en Python, les délimitations des blocs de code (comme la définition du corps d&#8217;une méthode par exemple) sont marquées au moyen de l&#8217;indentation (là où les langages avec une syntaxe héritée du C utilisent les accolades). Nous utilisons également une méthode raccourcie de Django très utile, <em>render_to_response</em>, qui permet de retourner une réponse HTTP en lui passant en paramètre le nom de la template à afficher. Cette méthode appartenant au package django.shortcuts, il est nécessaire d&#8217;importer ce paquetage au début du code source.
</p>

<p class="standard">
  Si nous rechargeons la page <a href="http://localhost:8000/hello/">http://localhost:8000/hello/</a>, nous recevons cette fois une exception de type &#8220;TemplateDoesNotExist&#8221;. Notre fonction a bien été appelée mais il nous reste à créer la template.
</p>

<p class="standard">
  3. <span style="text-decoration: underline;">Rédiger la template</span>
</p>

<p class="standard">
  Avec Django, il est nécessaire de mettre à jour le fichier settings.py pour indiquer au framework dans quel répertoire du disque dur il doit rechercher les fichiers de templating. Nous allons donc créer à la racine de notre projet un répertoire &#8220;templates&#8221; destiné à recevoir nos templates et modifier le fichier de configuration pour y mentionner ce répertoire (rechercher dans le fichier settings.py la ligne contenant <em>TEMPLATE_DIRS</em>) :
</p>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    &nbsp; <span class="kw2">mkdir</span> templates
  </div>
</div>

<div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="python codecolorer">
    <span class="co1"># monprojet/settings.py</span><br /> <br /> ....<br /> <span class="me1">TEMPLATE_DIRS</span> <span class="sy0">=</span> <span class="br0">&#40;</span><br /> &nbsp; &nbsp; <span class="co1"># Put strings here, like &quot;/home/html/django_templates&quot; or &quot;C:/www/django/templates&quot;.</span><br /> &nbsp; &nbsp; <span class="co1"># Always use forward slashes, even on Windows.</span><br /> &nbsp; &nbsp; <span class="co1"># Don't forget to use absolute paths, not relative paths.</span><br /> &nbsp; &nbsp; <span class="st0">'/home/roldo/workspace-django/monprojet/templates/'</span><span class="sy0">,</span><br /> <span class="br0">&#41;</span><br /> ...
  </div>
</div>

<p class="standard">
  Et enfin, créer la template et y ajouter le contenu suivant :
</p>

<div class="codecolorer-container html4strict vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="html4strict codecolorer">
    &nbsp; <span class="sc-1"><!-- monprojet/templates/hello.html --></span><br /> &nbsp; <span class="sc2"><<a href="http://december.com/html/4/element/h1.html"><span class="kw2">h1</span></a>></span>Hello World ! <span class="sc2"><<span class="sy0">/</span><a href="http://december.com/html/4/element/h1.html"><span class="kw2">h1</span></a>></span>
  </div>
</div>

<p class="standard">
  Voilà, si vous rechargez la page dans votre navigateur, vous devriez voir apparaître la célèbre phrase connue de tous les développeurs de la planète. Pour finir, je vous propose quelques aménagements afin de permettre de passer un paramètre prénom à notre fonction, et afficher ensuite la valeur de cette variable dans la template.
</p>

<p class="standard">
  Commençons par modifier le pattern de la route &#8230;
</p>

<div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="python codecolorer">
    &nbsp; <span class="co1"># monprojet/urls.py</span><br /> &nbsp; ...<br /> &nbsp; <span class="br0">&#40;</span>r<span class="st0">'^hello/(?P<firstname>[a-zA-Z]+)$'</span><span class="sy0">,</span> <span class="st0">'hello.views.hello'</span><span class="br0">&#41;</span><span class="sy0">,</span><br /> &nbsp; ...
  </div>
</div>

<p class="standard">
  &#8230; puis la fonction &#8220;hello&#8221; &#8230;
</p>

<div class="codecolorer-container python vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="python codecolorer">
    &nbsp; <span class="co1"># monprojet/hello/views.py</span><br /> &nbsp; ...<br /> &nbsp; <span class="kw1">def</span> hello<span class="br0">&#40;</span>request<span class="sy0">,</span> firstname<span class="br0">&#41;</span>:<br /> &nbsp; &nbsp; <span class="kw1">return</span> render_to_response<span class="br0">&#40;</span><span class="st0">'hello.html'</span><span class="sy0">,</span> <span class="br0">&#123;</span> <span class="st0">'firstname'</span> : firstname <span class="br0">&#125;</span><span class="br0">&#41;</span>
  </div>
</div>

<p class="standard">
  &#8230; et enfin, la template :
</p>

<div class="codecolorer-container html4strict vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="html4strict codecolorer">
    &nbsp; <span class="sc-1"><!-- monprojet/templates/hello.html --></span><br /> &nbsp; <span class="sc2"><<a href="http://december.com/html/4/element/h1.html"><span class="kw2">h1</span></a>></span>Hello {{ firstname }} ! <span class="sc2"><<span class="sy0">/</span><a href="http://december.com/html/4/element/h1.html"><span class="kw2">h1</span></a>></span>
  </div>
</div>

<p class="standard">
  Pour visualiser le résultat, rendez-vous à cette URL : <a href="http://localhost:8000/hello/Georges" target="_blank">http://localhost:8000/hello/Georges</a>
</p>

## Conclusion (plus que précaire &#8230;)

<p class="standard">
  Que venons-nous de faire ? Eh bien, nous venons tout simplement de développer un projet informatique, basé sur le Framework Django et reposant sur le paradigme MVC à trois couches. Nous sommes donc à présent en mesure d&#8217;honorer tous les besoins d&#8217;un client qui souhaiterait se doter d&#8217;une application basée sur le Framework Django, bâtie sur le paradigme MVC à trois couches, et destinée à &#8230; afficher &#8220;Hello World&#8221;.
</p>

<p class="standard">
  Bon, je vous le concède, les projets informatiques avec un cahier des charges aussi faméliques ne sont pas légion &#8230; Il nous reste donc de nombreux aspects à aborder pour prétendre développer des applications un tant soit peu évoluées avec Django. Qu&#8217;à cela ne tienne, nous aurons sans doute l&#8217;occasion d&#8217;y revenir à l&#8217;occasion de prochains tutoriels &#8230;
</p>