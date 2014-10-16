
[<img class="alignleft size-full wp-image-128" title="amazon" src="/blog/wp-content/uploads/2010/01/amazon.png" alt="amazon Installation de FUSE et s3fs sur une Debian Lenny" width="48" height="48" />][1]Aujourd'hui nous allons installer s3fs sur nos serveurs.
Ce paquet permet de monter des systèmes de fichiers distant de type Amazon S3. Les applications peuvent êtres multiples, le but avoué étant d'avoir la possibilité d'avoir notre "bucket" Amazon S3 disponible localement.

<span style="text-decoration: underline;"><strong>Pour se faire:</strong></span>

*   Installez les paquets suivants, nécessaire à la compilation des sources:

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">apt-get install</span> build-essential libcurl4-openssl-dev libxml2-dev libfuse-dev comerr-dev libfuse2 libidn11-dev libkadm55 libkrb5-dev libldap2-dev libselinux1-dev libsepol1-dev pkg-config fuse-utils
  </div>
</div>

Récupérer la dernière version des sources s3fs sur le <a title="s3fs: Downloads" onclick="javascript:pageTracker._trackPageview('/outgoing/code.google.com/p/s3fs/downloads/list');" href="http://code.google.com/p/s3fs/downloads/list">Google Code project</a> (version 118 au moment où j'écris ce billet):

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">wget</span> http:<span class="sy0">//</span>s3fs.googlecode.com<span class="sy0">/</span>files<span class="sy0">/</span>s3fs-r188-source.tar.gz
  </div>
</div>

*   Compiler les sources

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw3">cd</span> s3fs<br /> <span class="kw2">make</span><br /> <span class="kw2">make</span> <span class="kw2">install</span>
  </div>
</div>

Une fois ces 3 étapes accomplies le système est prêt nous allons pouvoir monter notre système de fichier.

Commençons par modifier la configuration de FUSE en dé-commentant la ligne <tt>user_allow_other</tt>, et c'est parti pour le montage du notre S3 bucket:

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">sudo</span> <span class="kw2">mkdir</span> <span class="re5">-p</span> <span class="sy0">/</span>mnt<span class="sy0">/</span>s3<br /> s3fs bucketname <span class="re5">-o</span> <span class="re2">accessKeyId</span>=XXX <span class="re5">-o</span> <span class="re2">secretAccessKey</span>=YYY <span class="re5">-o</span> <span class="re2">use_cache</span>=<span class="sy0">/</span>tmp <span class="re5">-o</span> allow_other <span class="sy0">/</span>mnt<span class="sy0">/</span>s3
  </div>
</div>

<span style="text-decoration: underline;">accessKeyId:</span> Votre "Amazon Access Key"
<span style="text-decoration: underline;">secretAccessKey: </span>Votre "Secret Key"

L'option **<tt>use_cache</tt>** permet d'indiquer à s3fs de cacher les fichiers du *bucket* Amazon localement (dans /tmp en l'occurrence), l'option allow_other permet quant à elle d'autoriser des utilisateurs non root à manipuler les fichiers du montage.
N'hésitez pas à jeter un oeil au wiki du projet en cas de problème: [wiki][2].

A présent tous les fichiers écrits dans /mnt/s3 seront en fait écris dans votre espace Amazon S3.

Si vous êtes comme moi et que vous n'aimez pas laisser trainer des mots de passe, clés et autre dans les lignes de commande vous pouvez conserver votre couple **</p> <div class="codecolorer-container text vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="text codecolorer">
    accessKeyId /
  </div>
</div>

<div class="codecolorer-container text vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="text codecolorer">
    secretAccessKey
  </div>
</div>

</strong> dans le fichier

<div class="codecolorer-container text vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="text codecolorer">
     /etc/passwd-s3fs
  </div>
</div>

sous la forme d'une seule ligne formatée comme ceci:

<div class="codecolorer-container text vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="text codecolorer">
    accessKeyId:secretAccessKey
  </div>
</div>

Nous obtenons dans ce cas la ligne de commande suivante:

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    s3fs bucketname <span class="re5">-o</span> <span class="re2">use_cache</span>=<span class="sy0">/</span>tmp <span class="re5">-o</span> allow_other <span class="sy0">/</span>mnt<span class="sy0">/</span>s3
  </div>
</div>

*<span style="font-size: smaller;">(Le fichier /etc/passwd-s3fs est lu automatiquement)</span>*

 [1]: /blog/wp-content/uploads/2010/01/amazon.png
 [2]: http://code.google.com/p/s3fs/wiki/FuseOverAmazon