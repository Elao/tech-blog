
Bonjour à tous !

Petit memo aujourd'hui pour les admin sys <img src="http://old-blog.elao.dev/wp-includes/images/smilies/icon_smile.gif" alt="icon smile Créer une autorité de certification et des certificats SSL auto signés" class="wp-smiley" title="Créer une autorité de certification et des certificats SSL auto signés" />

Nous allons voir comment créer sa propre autorité de certification et créer ses propres certificats SSL auto-signés, toujours très utiles lorsque l'on a des problématiques de connexion sécurisée.
L'ensemble de ces manipulations ont été réalisées sur des serveurs Linux / Debian Lenny.

## Créer son propre CA (Certificate Authority)

Nous allons commencer par créer une clé privée

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="co4">root@my_server:~# </span>openssl genrsa <span class="re5">-des3</span> <span class="re5">-out</span> elao-ca.key <span class="nu0">2048</span><br /> Generating RSA private key, <span class="nu0">2048</span> bit long modulus<br /> .........................................................................................+++<br /> ..+++<br /> e is <span class="nu0">65537</span> <span class="br0">&#40;</span>0x10001<span class="br0">&#41;</span><br /> Enter pass phrase <span class="kw1">for</span> elao-ca.key:
  </div>
</div>

La protection par une "pass phrase" de votre clé est essentielle ici, en effet cette clé nous servira à signer **tous** les certificats de notre organisation, sa confidentialité doit donc être **primordiale**. Il va sans dire que le stockage de cette clé, dans l'idéal, doit être fait sur un support amovible  (type clé USB), et ne doit pas être conservée sur une machine accessible "publiquement" (j'entend ici directement connectée à internet).

<span style="text-decoration: underline;"><strong>Note:</strong></span>

Il est possible d'améliorer la qualité de la clé en fournissant comme source de données aléatoires un ou plusieurs fichiers avec l'option -rand, par exemple:

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="re5">-rand</span> <span class="sy0">/</span>var<span class="sy0">/</span>log<span class="sy0">/</span>messages
  </div>
</div>

Par défaut openssl utilisera /dev/urandom s'il existe, dans le cas contraire /dev/random, la source de données aléatoire est très importante pour la qualité du cryptage, si votre système ne dispose d'aucun de ces deux fichiers vous pouvez installer un générateur de nombre aléatoire comme [egd][1].

Maintenant que nous disposons d'une clé nous allons procéder à la création de notre certificat CA privé

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    openssl req <span class="re5">-new</span> <span class="re5">-x509</span> <span class="re5">-days</span> <span class="nu0">3650</span> <span class="re5">-key</span> elao-ca.key <span class="re5">-out</span> elao-ca.crt<br /> Enter pass phrase <span class="kw1">for</span> elao-ca.key:
  </div>
</div>

Le contenu de notre certificat est vérifiable grâce à la commande suivante:

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    openssl x509 <span class="re5">-in</span> elao-ca.crt <span class="re5">-text</span> <span class="re5">-noout</span>
  </div>
</div>

## Créer une clé et un certificat pour le serveur

Maintenant que nous disposons de notre autorité de certification nous allons pouvoir créer et signer les certificats à destination de notre serveur.

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    openssl genrsa <span class="re5">-des3</span> <span class="re5">-out</span> elao-server.key <span class="nu0">1024</span>
  </div>
</div>

Comme vous le constatez nous restons sur un cryptage à 1024 bits, en effet il est risqué d'aller au delà car certains navigateurs ne supportent pas les cryptages supérieurs.

La prochaine étape consiste à générer une requête de signature de certificat, appelée comme ceci car elle est normalement transmise à une autorité de certification (Certificate Authority ou CA) pour signature.
Un certificat n’est ni plus, ni moins qu’une clé qui a été signée par une autorité autorisée garantissant ainsi que celle-ci est valide est correspond bien à la bonne entité.

**Note:**

Une autorité de certification est une entité autorisée à signer les certificats SSL, elles ne sont que quelques unes à faire autorité. Lorsqu’un certificat est signé par l’une d’entre elles, les navigateurs accepterons automatiquement le certificat comme étant valide, si un certificat est signé par une autorité de certification absente de la liste des autorités fiables du navigateur celui-ci renverra un avertissement à l’utilisateur lui précisant que le certificat a été signé par une autorité non reconnue et lui demandera de confirmer qu’il accepte bien le certificat.

Ce qui se traduit sous Firefox par exemple par ce type d’écran:

[<img class="aligncenter size-full wp-image-1028" title="firefox-ssl" src="/blog/wp-content/uploads/2010/10/firefox-ssl.png" alt="firefox ssl Créer une autorité de certification et des certificats SSL auto signés" width="700" height="386" />][2]

Au cours de cette étape plusieurs questions vous serons posées et se présenteront ainsi, vos réponses seront utilisées par le certificat et utilisées par les navigateurs afin de vérifier que le certificat provient d’une source sûre. Les utilisateurs seront en mesure d’inspecter ces détails à n’importe quel moment à partir de l’instant où ils se connectent à votre site.

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    openssl req <span class="re5">-new</span> <span class="re5">-key</span> elao-server.key <span class="re5">-out</span> elao-server.csr
  </div>
</div>

... qui devrait aboutir sur:

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    Enter pass phrase <span class="kw1">for</span> elao-server.tld.key:<br /> You are about to be asked to enter information that will be incorporated into your certificate request.<br /> What you are about to enter is what is called a Distinguished Name or a DN.<br /> There are quite a few fields but you can leave some blank<br /> For some fields there will be a default value,<br /> If you enter <span class="st_h">'.'</span>, the field will be left blank.<br /> <span class="re5">