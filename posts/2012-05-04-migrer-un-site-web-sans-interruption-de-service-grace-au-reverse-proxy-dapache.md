
Bonjour à tous,

petit mémo aujourd&#8217;hui concernant la migration d&#8217;une application web d&#8217;un (ancien) serveur vers un nouveau serveur.  
Tout admin système à été, un jour ou l&#8217;autre, confronté à la problématique du &#8220;downtime&#8221; ou en bon français de l&#8217;interruption de service lors du &#8220;déménagement&#8221; d&#8217;une application web vers une nouvelle machine.

En effet dans le cas de la modification d&#8217;un pointage DNS, celui-ci peut mettre plusieurs heures, voir même une journée à se propager sur les différents serveurs de noms.  
Pour un site statique cela ne pose guère de problème puisque nous sommes dans une stratégie de lecture, par contre pour un site dynamique le délai de propagation peut-être hautement problématique. Il est évident que l&#8217;on ne peut se permettre d&#8217;avoir des écritures sur l&#8217;un et l&#8217;autre des serveurs.

Une bonne habitude pour minimiser ce problème est d&#8217;utiliser les systèmes d&#8217;IP failover, qui permettent d&#8217;éviter de faire une modification au niveau du nom de domaine concerné lors d&#8217;une migration applicative.

> <div class="aparte">
>   Les IP failover sont, pour résumer, des interfaces &#8220;virtuelles&#8221; qui peuvent être facilement redirigées vers une machine physique, OVH propose par exemple ce système sur tous ses serveurs dédiés. Grâce à celles-ci, la problématique du downtime se pose moins, en effet une fois que notre site a été &#8220;déménagé&#8221;, il suffit de modifier le pointage de cette IP failover sans impact sur le pointage des DNS.
> </div>

**<u>Cette stratégie trouve par contre ses limites dans différents cas:</u>**

- Si l&#8217;on est dans le cas d&#8217;un serveur mutualisé (qui n&#8217;héberge donc pas qu&#8217;une seule application).  
- S&#8217;il est impossible de faire un transfert complet des différents sites en même temps.  
- Si l&#8217;on ne souhaite tout simplement &#8220;déménager&#8221; qu&#8217;une seule application

Dans le cas d&#8217;applications à l&#8217;architecture complexe pour lesquelles il est quasiment impossible d&#8217;assurer le transfert applicatif en une seule fois. On se retrouve donc souvent avec une partie des applications sur la nouvelle machine et le reste sur l&#8217;ancienne.

Il est heureusement possible de se servir des serveurs web comme de &#8220;reverse proxy&#8221;.  
Dans le cadre de ce mémo nous utiliseront [Apache][1] mais il est également possible d&#8217;utiliser [Nginx][2], voir même [Squid][3] qui doit être capable de faire du reverse proxy.

**<span style="text-decoration: underline;">Nous partirons du principe que:</span>**

- Votre (vos) application(s) et base(s) de données ont été transférées sur leur nouveau serveur, que tout fonctionne et que tout a été vérifié par qui de droit.  
- Un nouveau &#8220;Virtual Host&#8221; (désolé ça ne veux pas avec &#8220;Hôte virtuel&#8221; &#8230;), a été configuré et est fonctionnel.  
- Les mods **proxy** et **[proxy_http][4]** sont &#8220;chargés&#8221; par Apache sur notre serveur d&#8217;origine.  
- Le site à déplacer sera **www.my-domain.com**.  
- L&#8217;IP du &#8220;nouveau&#8221; serveur sera XXX.XXX.XXX.XXX

Tout le reste se passe du coté de &#8220;l&#8217;ancien&#8221; serveur, nous commencerons par modifier la résolution de nom local concernant le domaine concerné en éditant le fichier /etc/hosts pour y rajouter l&#8217;entrée suivante:

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    XXX.XXX.XXX.XXX www.my-domain.com
  </div>
</div>

De cette façon nous forçons le pointage de notre domaine vers la nouvelle machine uniquement pour le serveur d&#8217;origine.  
Nous allons ensuite modifier le &#8220;virtual host&#8221; original en y ajoutant les lignes suivantes:

<div class="codecolorer-container apache vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="apache codecolorer">
    <span class="kw1">ProxyRequests</span> <span class="kw2">Off</span><br /> <<span class="kw3">Proxy</span> *><br /> &nbsp; <span class="kw1">Order</span> <span class="kw1">deny</span>,<span class="kw1">allow</span><br /> &nbsp; <span class="kw1">Allow</span> from <span class="kw2">all</span><br /> </<span class="kw3">Proxy</span>><br /> <br /> <span class="kw1">ProxyPass</span> / http://www.my-domain.com/<br /> <span class="kw1">ProxyPassReverse</span> / http://www.my-domain.com/
  </div>
</div>

Il suffit, pour finir, de redémarrer Apache, le traffic est à présent redirigé vers le nouveau serveur de manière transparente pour l&#8217;internaute.  
Nous pouvons dès lors demander la redirection du nom de domaine vers l&#8217;IP du &#8220;nouveau&#8221; serveur sans se soucier des délais de propagation des serveurs de noms. 

Une fois tout cela terminé jetez de temps en temps un oeil à vos logs sur le serveur d&#8217;origine, une fois que vous constatez que plus aucun traffic n&#8217;arrive sur votre ancien serveur c&#8217;est que le nom de domaine a été propagé.

 [1]: http://httpd.apache.org/ "Apache"
 [2]: http://nginx.com/ "Nginx"
 [3]: http://www.squid-cache.org/ "Squid"
 [4]: http://www.elao.com/blog/linux/reverse-proxy-apache-no-protocol-handler-was-valid-for-the-url.html "Reverse proxy Apache: No protocol handler was valid for the URL"