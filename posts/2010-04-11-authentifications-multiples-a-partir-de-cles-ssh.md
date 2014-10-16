
La petite astuce du jour <img src="http://old-blog.elao.dev/wp-includes/images/smilies/icon_smile.gif" alt="icon smile Authentifications multiples à partir de clés SSH" class="wp-smiley" title="Authentifications multiples à partir de clés SSH" />

<p style="text-align: justify;">
  Lorsque l'on commence à avoir pas mal de serveurs à administrer, les clés SSH se multiplient. En effet même si en règle générale l'on utilise une seule et même clé pour s'authentifier sur différentes machines, il est parfois nécessaire d'utiliser des clés différentes ...<br /> Soit pour  se connecter avec un compte utilisateur différent sur une même machine physique, soit parce que l'on utilise plusieurs type de clé, ou encore parce que l'on veut éviter qu'une seule clé permette  d'accéder à plusieurs machine.
</p>

<p style="text-align: justify;">
  Vous me direz que l'on peut choisir quelle clé utilisée à l'aide de l'option -i de ssh, c'est vrai, mais comme tout bon développeur, je suis un peu fainéant, et si la machine peut le faire à ma place ... pourquoi m'ennuyer ?
</p>

<p style="text-align: justify;">
  Il est donc possible à l'aide du fichier <span style="font-size: small;"><span style="font-family: terminal, monaco;">~/.ssh/config</span></span> d'indiquer au client SSH quelle clé il doit utiliser pour se connecter et à quelle machine.
</p>

<span style="text-decoration: underline;">Sa syntaxe est la suivante:</span>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    Host mon-serveur.domain.tld<br />   HostName mon-serveur<br />   User guewen<br />   IdentityFile ~<span class="sy0">/</span>.ssh<span class="sy0">/</span>ma-cle-ssh<br /> <br /> Host mon-autre-serveur.domain.tld<br />   Hostname mon-autre-serveur<br />   User root<br />   IdentityFile ~<span class="sy0">/</span>.ssh<span class="sy0">/</span>mon-autre-cle-ssh
  </div>
</div>

<p style="text-align: justify;">
  De cette façon notre client SSH utilisera systématiquement la clé attachée à un hôte particulier.<br /> La directive permet de spécifier un nom alternatif, elle accepte également une IP.
</p>

<p style="text-align: justify;">
  Il est tout à fait possible de spécifier plusieurs noms d'hôtes pour la directive "Host", ceux-ci, devant dans ce cas là, être séparés par des espaces. De même un wildcard peut être renseigné dans le cas ou plusieurs machines partagent le même domaine. Très pratique lorsque l'on a un parc complet à administrer.<br /> Il existe un nombre de directives assez impressionnantes, aussi je vous conseille pour plus d'informations de faire un petit <strong>man ssh_config.</strong>
</p>

**<span style="text-decoration: underline;">Exemple d'un host configuré avec un wildcard:</span>**

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    Host <span class="sy0">*</span>.domain.tld<br />   HostName mon-alias<br />   User guewen<br />   IdentityFile ~<span class="sy0">/</span>.ssh<span class="sy0">/</span>ma-cle-ssh
  </div>
</div>