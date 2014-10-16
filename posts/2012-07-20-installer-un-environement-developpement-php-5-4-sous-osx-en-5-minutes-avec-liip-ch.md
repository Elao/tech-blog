
Chez Elao, 90% de notre effectif tourne sous du `Zend Server CE` (que l'on peut retrouver ici sur le site de [Zend][1]). Parmi mes collègues, ils tournent tous avec du PHP 5.3. J'ai donc récemment décidé de passer à PHP 5.4 pour plusieurs raisons (performances, aventure...).

C'est donc fidèle, que j'installe la outdated `Zend Server CE Technology Preview avec PHP 5.4.0`

<img class="alignnone" title="PHP 5.4.0 ZendServer CE" src="http://content.screencast.com/users/tristanbessoussa/folders/Jing/media/ff32e77e-f4cd-420a-8b5e-94375babc2d9/00000169.png" alt="00000169 Installer un environnement de développement PHP 5.4 sous OSX en 5 minutes avec liip.ch" width="926" height="184" />

Voilà, en moins de 5 minutes, vous avez votre environnement de dev PHP 5.4 (Apache2 / MySQL / PhpMyAdmin / PHP) opérationnel.

Simplement, que se passe t'il lorsque vous essayez d'installer xDebug  ?
(oui, parce qu'il vous arrive parfois que votre code bug)

**Eh bien, on tombe sur un magnifique message d'erreur :**
```
Failed loading xdebug.so:  dlopen(xdebug.so, 9): image not found

PHP Warning:  PHP Startup: Unable to load dynamic library '/usr/local/zend/lib/php_extensions/xdebug.so' -
dlopen(/usr/local/zend/lib/php_extensions/xdebug.so, 9): no suitable image found.

Did find: /usr/local/zend/lib/php_extensions/xdebug.so: mach-o, but wrong architecture in Unknown on line 0
```

Et là, si je vous explique la solution de comment compiler xdebug en le forcant en 32bits, **on dépasse la limite des 5 minutes**.

### PHP 5.4 avec le binary package proposé par Liip.ch

C'est alors que j'ai décidé d'utiliser <http://php-osx.liip.ch/> , un package à installer en une ligne de commande qui contient la dernière version de PHP 5.4 (ou de 5.3) dans `/usr/local/php5` :

```
curl -s http://php-osx.liip.ch/install.sh | bash -s 5.4
```

Le package s'installe avec pleins d'extensions utiles (et parfois necessaires) pour le développement en Symfony2.

Voilà donc le résultat :

<img title="PHP5.4 with liip.ch" src="/blog/medias/2012-07-20-installer-un-environement-developpement-php-5-4-sous-osx-en-5-minutes-avec-liip-ch/00000171.png" alt="00000171 Installer un environnement de développement PHP 5.4 sous OSX en 5 minutes avec liip.ch" width="587" height="87" />

Il ne reste donc plus qu'à réactiver le apache2 de OSX si vous l'aviez désactiver (si comme moi vous migrez de ZendServer) et à s'assurer que vous avez un MySQL opérationnel.

 [1]: http://www.zend.com/fr/products/server-ce/downloads