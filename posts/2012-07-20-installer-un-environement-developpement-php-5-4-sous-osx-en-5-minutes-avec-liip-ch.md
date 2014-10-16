
Chez Elao, 90% de notre effectif tourne sous du* Zend Server CE* (que l&#8217;on peut retrouver ici sur le site de [Zend][1]). Parmi mes collègues, ils tournent tous avec du PHP 5.3. J&#8217;ai donc récemment décidé de passer à PHP 5.4 pour plusieurs raisons (performances, aventure&#8230;).

C&#8217;est donc fidèle, que j&#8217;installe la outdated *Zend Server CE Technology Preview avec PHP 5.4.0 *

<img class="alignnone" title="PHP 5.4.0 ZendServer CE" src="http://content.screencast.com/users/tristanbessoussa/folders/Jing/media/ff32e77e-f4cd-420a-8b5e-94375babc2d9/00000169.png" alt="00000169 Installer un environnement de développement PHP 5.4 sous OSX en 5 minutes avec liip.ch" width="926" height="184" />

Voilà, en moins de 5 minutes, vous avez votre environnement de dev PHP 5.4 (Apache2 / MySQL / PhpMyAdmin / PHP) opérationnel.

Simplement, que se passe t&#8217;il lorsque vous essayez d&#8217;installer xDebug  ?  
(oui, parce qu&#8217;il vous arrive parfois que votre code bug)

Eh bien, on tombe sur un magnifique message d&#8217;erreur :  
<noscript>
  <pre><code class="language-shell shell">Failed loading xdebug.so:  dlopen(xdebug.so, 9): image not found

PHP Warning:  PHP Startup: Unable to load dynamic library '/usr/local/zend/lib/php_extensions/xdebug.so' - 
dlopen(/usr/local/zend/lib/php_extensions/xdebug.so, 9): no suitable image found.  

Did find: /usr/local/zend/lib/php_extensions/xdebug.so: mach-o, but wrong architecture in Unknown on line 0
</code></pre>
</noscript>

Et là, si je vous explique la solution de comment compiler xdebug en le forcant en 32bits, **on dépasse la limite des 5 minutes**.

# PHP 5.4 avec le binary package proposé par Liip.ch

C&#8217;est alors que j&#8217;ai décidé d&#8217;utiliser <http://php-osx.liip.ch/> , un package à installer en une ligne de commande qui contient la dernière version de PHP 5.4 (ou de 5.3) dans */usr/local/php5: *

<noscript>
  <pre><code class="language-shell shell">curl -s http://php-osx.liip.ch/install.sh | bash -s 5.4</code></pre>
</noscript>

Le package s&#8217;installe avec pleins d&#8217;extensions utiles (et parfois necessaires) pour le développement en Symfony2.

Voilà donc le résultat :

<img class="alignnone" title="PHP5.4 with liip.ch" src="http://content.screencast.com/users/tristanbessoussa/folders/Jing/media/685752f8-a9f8-4100-bc91-a823588de775/00000171.png" alt="00000171 Installer un environnement de développement PHP 5.4 sous OSX en 5 minutes avec liip.ch" width="587" height="87" />

Il ne reste donc plus qu&#8217;à réactiver le apache2 de OSX si vous l&#8217;aviez désactiver (si comme moi vous migrez de ZendServer) et à s&#8217;assurer que vous avez un MySQL opérationnel.

 [1]: http://www.zend.com/fr/products/server-ce/downloads