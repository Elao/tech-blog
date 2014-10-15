
La petite astuce du jour <img src="http://old-blog.elao.dev/wp-includes/images/smilies/icon_smile.gif" alt="icon smile Authentifications multiples à partir de clés SSH" class="wp-smiley" title="Authentifications multiples à partir de clés SSH" /> 

<p style="text-align: justify;">
  Lorsque l&#8217;on commence à avoir pas mal de serveurs à administrer, les clés SSH se multiplient. En effet même si en règle générale l&#8217;on utilise une seule et même clé pour s&#8217;authentifier sur différentes machines, il est parfois nécessaire d&#8217;utiliser des clés différentes &#8230;<br /> Soit pour  se connecter avec un compte utilisateur différent sur une même machine physique, soit parce que l&#8217;on utilise plusieurs type de clé, ou encore parce que l&#8217;on veut éviter qu&#8217;une seule clé permette  d&#8217;accéder à plusieurs machine.
</p>

<p style="text-align: justify;">
  Vous me direz que l&#8217;on peut choisir quelle clé utilisée à l&#8217;aide de l&#8217;option -i de ssh, c&#8217;est vrai, mais comme tout bon développeur, je suis un peu fainéant, et si la machine peut le faire à ma place &#8230; pourquoi m&#8217;ennuyer ?
</p>

<p style="text-align: justify;">
  Il est donc possible à l&#8217;aide du fichier <span style="font-size: small;"><span style="font-family: terminal, monaco;">~/.ssh/config</span></span> d&#8217;indiquer au client SSH quelle clé il doit utiliser pour se connecter et à quelle machine.
</p>

<span style="text-decoration: underline;">Sa syntaxe est la suivante:</span>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    Host mon-serveur.domain.tld<br /> &nbsp; HostName mon-serveur<br /> &nbsp; User guewen<br /> &nbsp; IdentityFile ~<span class="sy0">/</span>.ssh<span class="sy0">/</span>ma-cle-ssh<br /> <br /> Host mon-autre-serveur.domain.tld<br /> &nbsp; Hostname mon-autre-serveur<br /> &nbsp; User root<br /> &nbsp; IdentityFile ~<span class="sy0">/</span>.ssh<span class="sy0">/</span>mon-autre-cle-ssh
  </div>
</div>

<p style="text-align: justify;">
  De cette façon notre client SSH utilisera systématiquement la clé attachée à un hôte particulier.<br /> La directive permet de spécifier un nom alternatif, elle accepte également une IP.
</p>

<p style="text-align: justify;">
  Il est tout à fait possible de spécifier plusieurs noms d&#8217;hôtes pour la directive &#8220;Host&#8221;, ceux-ci, devant dans ce cas là, être séparés par des espaces. De même un wildcard peut être renseigné dans le cas ou plusieurs machines partagent le même domaine. Très pratique lorsque l&#8217;on a un parc complet à administrer.<br /> Il existe un nombre de directives assez impressionnantes, aussi je vous conseille pour plus d&#8217;informations de faire un petit <strong>man ssh_config.</strong>
</p>

**<span style="text-decoration: underline;">Exemple d&#8217;un host configuré avec un wildcard:</span>**

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    Host <span class="sy0">*</span>.domain.tld<br /> &nbsp; HostName mon-alias<br /> &nbsp; User guewen<br /> &nbsp; IdentityFile ~<span class="sy0">/</span>.ssh<span class="sy0">/</span>ma-cle-ssh
  </div>
</div>