
Petit billet pour se souvenir de cette erreur rencontrée récemment en mettant en place un reverse proxy Apache.
Celle-ci peut passer relativement inaperçue car elle n'empêche pas Apache de démarrer et un **apachectl -S** ne remonte pas d'erreur de configuration.

Si vous avez donc dans vos logs serveur quelque chose ressemblant à ceci:

<div class="codecolorer-container apache vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="apache codecolorer">
    [warn] proxy: No <span class="kw1">protocol</span> handler was valid for the URL /. If you are using a DSO version of mod_proxy, make sure the proxy submodules are included in the configuration using <span class="kw1">LoadModule</span>.
  </div>
</div>

C'est que vous avez sans doutes chargé le mod <a href="http://httpd.apache.org/docs/2.0/mod/mod_proxy.html" title="Apache mod proxy" target="_blank">proxy</a> d'Apache mais pas le mod <a href="http://httpd.apache.org/docs/2.0/mod/mod_proxy_http.html" title="Apache mod proxy_http" target="_blank">proxy_http</a>, nécessaire pour "proxiser" des requêtes HTTP.

Sachez également que le mod **proxy_http** nécessite le mod **proxy** pour fonctionner.

Bref pour résoudre le problème un petit a2enmod devrait suffire:

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    a2enmod proxy proxy_http
  </div>
</div>