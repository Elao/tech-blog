
Dans cet article, nous allons détailler pas à pas comment configurer le serveur Bind intégré à Mac Os X.
Les développeurs web sont souvent confrontés à des problématiques DNS (wildcards, etc...) pour lesquelles le fichier **/etc/hosts** s'avère insuffisant.

Cet article est traduit de <a href="http://www.macshadows.com/kb/index.php?title=How_To:_Enable_BIND_-_Mac_OS_X's_Built-in_DNS_Server" target="_blank">"How To: Enable BIND &#8211; Mac OS X's Built-in DNS Server"</a>

<span style="font-size: x-large;">Etape 1: Configuration de rndc</span>

**rndc** est l'utilitaire qui permet la configuration du serveur DNS. Vous devez tout d'abord créer un fichier de configuration ainsi qu'une clé secrète.
Saisissez les commandes suivantes dans votre terminal pour générer le fichier de configuration et la clé secrète:

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="sy0">></span> <span class="kw2">sudo</span> <span class="re5">-s</span><br /> rndc-confgen <span class="re5">-b</span> <span class="nu0">256</span> <span class="sy0">></span> <span class="sy0">/</span>etc<span class="sy0">/</span>rndc.conf<br /> <span class="kw2">head</span> <span class="re5">-n5</span> <span class="sy0">/</span>etc<span class="sy0">/</span>rndc.conf <span class="sy0">|</span> <span class="kw2">tail</span> <span class="re5">-n4</span> <span class="sy0">></span> <span class="sy0">/</span>etc<span class="sy0">/</span>rndc.key
  </div>
</div>

[<img class="size-full wp-image-146 alignleft" title="warning" src="/blog/wp-content/uploads/2010/04/warning.png" alt="warning Configuration de Bind sous Mac OS X" width="48" height="48" />][1]

Attention : La commande r**ndc-confgen** est extrêmement pratique pour générer les fichiers de configurations **rndc**, mais il se peut qu'il initialise un port par défaut différent de celui de **named**.
Lancez les commandes suivantes, et assurez-vous que les ports sont identiques dans les deux fichiers de configurations /**etc/named.conf** et **/etc/rndc.conf**, si ce n'est pas le cas, éditez les fichiers et uniformisez les.

<p style="text-align: left;">
  <div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="bash codecolorer">
      <span class="kw2">cat</span> <span class="sy0">/</span>etc<span class="sy0">/</span>named.conf <span class="sy0">|</span> <span class="kw2">grep</span> <span class="st_h">'inet'</span><br /> <span class="kw2">cat</span> <span class="sy0">/</span>etc<span class="sy0">/</span>rndc.conf <span class="sy0">|</span> <span class="kw2">grep</span> <span class="st_h">'default-port'</span>
    </div>
  </div>

  <p>
    <span style="font-size: x-large;">Etape 2 : Activer BIND</span>
  </p>

  <p>
    Nous devons maintenant configurer <strong>Bind</strong> pour qu'il se lance au démarrage. Pour se faire, on utilisera les commandes suivantes:
  </p>

  <div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="bash codecolorer">
      launchctl load <span class="re5">-w</span> <span class="sy0">/</span>System<span class="sy0">/</span>Library<span class="sy0">/</span>LaunchDaemons<span class="sy0">/</span>org.isc.named.plist<br /> <span class="kw3">echo</span> <span class="st0">"launchctl start org.isc.named"</span> <span class="sy0">>></span> <span class="sy0">/</span>etc<span class="sy0">/</span>launchd.conf
    </div>
  </div>

  <p>
    Puis nous n'avons plus qu'à démarrer le démon.
  </p>

  <div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="bash codecolorer">
      <span class="sy0">/</span>usr<span class="sy0">/</span>bin<span class="sy0">/</span>named
    </div>
  </div>

  <p>
    ou encore
  </p>

  <div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="bash codecolorer">
      launchctl start org.isc.named
    </div>
  </div>

  <p>
    <span style="font-size: x-large;">Etape 3 : Configuration de named<br /> </span>
  </p>

  <p>
    Cette étape peut nécessiter quelques recherches supplémentaires si vous avez des besoins très spécifiques.<br /> Dans cet article, nous allons voir un cas simple de configuration où nous souhaitons que notre interface locale (localhost) réponde pour tous les dns de la forme <strong>*.local<br /> <span style="font-weight: normal;"><em>Ce type de configuration peut être très utile pour les développeurs web qui souhaitent gagner du temps et éviter d'éditer systématiquement leur fichiers /etc/host lorsqu'ils rajoutent un virtualhost dans Apache.</em></span></strong>
  </p>

  <p>
    <span style="font-style: normal;">La création de nouveaux dns passe par deux étapes. La première consiste à créer le fichier de zone correspondant à notre dns ".local", la seconde va permettre d'indiquer à named la nouvelle zone ainsi que l'emplacement du fichier de configuration associé.</span>
  </p>

  <p>
    <span style="font-weight: normal;">1) Nous devons tout d'abord créer notre fichier de zone.</span>
  </p>

  <div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="bash codecolorer">
      <span class="kw2">vi</span> <span class="sy0">/</span>var<span class="sy0">/</span>named<span class="sy0">/</span>local.zone
    </div>
  </div>

  <p>
    Passez en mode insertion (touche i) et copiez/collez la configuration suivante en remplaçant user.domain.com par votre adresse email (prenez garde à remplacer le "@" par un "." et suffixer également d'un ".").
  </p>

  <div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="bash codecolorer">
      <span class="re1">$TTL</span>    <span class="nu0">86400</span><br /> <span class="re1">$ORIGIN</span> local.<br /> <span class="sy0">@</span>       IN      SOA     localhost.      user.domain.com. <span class="br0">&#40;</span><br />                                         <span class="nu0">42</span>              ; serial<br />                                         3H              ; refresh<br />                                         15M             ; retry<br />                                         1W              ; expiry<br />                                         1D <span class="br0">&#41;</span>            ; minimum<br /> <br />                         1D IN NS        <span class="sy0">@</span><br />                         1D IN A         127.0.0.1<br /> <br /> <span class="sy0">*</span> IN A 127.0.0.1
    </div>
  </div>

  <p>
    Assurez vous que la dernière ligne est vide, sauvegardez et quittez (ESC puis ":wq" et ENTER to sauvegarder et quitter sous <strong>VI</strong>).
  </p>

  <p>
    2) Reconfiguration de named.conf pour indiquer notre nouvelle zone
  </p>

  <div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="bash codecolorer">
      <span class="kw2">vi</span> <span class="sy0">/</span>etc<span class="sy0">/</span>named.conf
    </div>
  </div>

  <p>
    Puis nous ajoutons les lignes suivantes à la suite des configurations de zones présentes:
  </p>

  <div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="bash codecolorer">
      zone <span class="st0">"local"</span> IN <span class="br0">&#123;</span><br />         <span class="kw3">type</span> master;<br />         <span class="kw2">file</span> <span class="st0">"local.zone"</span>;<br />         allow-update <span class="br0">&#123;</span> none; <span class="br0">&#125;</span>;<br /> <span class="br0">&#125;</span>;
    </div>
  </div>

  <p>
    <span style="font-family: Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif; line-height: 19px; white-space: normal; font-size: 13px;">Sauvegardez et le tour est joué !</span><br /> <span style="font-family: Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif; line-height: 19px; white-space: normal; font-size: 13px;"><br /> </span>
  </p>

  <p>
    <span style="font-size: x-large;">Etape 4 : Rechargement de la configuration</span>
  </p>

  <p>
    A chaque fois que vous modifier les fichiers de configuration ou de zones, vous aurez besoin de recharger la configuration par la commande:
  </p>

  <div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="bash codecolorer">
      rndc reload
    </div>
  </div>

  <p>
    Vous pouvez également réinitialiser le cache DNS par la commande suivante:
  </p>

  <div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="bash codecolorer">
      rndc flush
    </div>
  </div></pre>

 [1]: /blog/wp-content/uploads/2010/04/warning.png