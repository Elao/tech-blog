
<img src="/blog/wp-content/uploads/image/articles/1270396242_Login%20Manager.png" alt="1270396242 Login%20Manager Sécuriser ses accès serveur: Authentification par clés SSH" width="128" height="128" align="left" title="Sécuriser ses accès serveur: Authentification par clés SSH" />

Il n&#8217;est pas rare aujourd&#8217;hui que les administrateurs système soit amené à manipuler plusieurs dizaine de mots de passe, il existe, bien entendu, diversent façon de les &#8220;stocker&#8221;, comme par exemple le logiciel Keepass, permettant de gérer vos différents mots de passe. Il est cependant encore plus facile d&#8217;utiliser une authentification basée sur une clé publique.

Nous allons donc voir comment utiliser une authentification par clé ssh.  
Cela facilitera l&#8217;administration des serveurs mais permettra également de mettre en place certains scripts. Comme par exemple la sauvegarde des données à partir d&#8217;un autre serveur <img src="http://old-blog.elao.dev/wp-includes/images/smilies/icon_wink.gif" alt="icon wink Sécuriser ses accès serveur: Authentification par clés SSH" class="wp-smiley" title="Sécuriser ses accès serveur: Authentification par clés SSH" /> 

<span style="text-decoration: underline;"><strong>Pré-requis</strong></span>: Un serveur ssh fonctionnel et bien sur un client ssh, si vous travaillez sous linux ou sous Mac OS pas de problème vous devriez avoir le nécessaire d&#8217;installé.

<span style="text-decoration: underline;"><strong>Si vous ne disposez pas de ssh sur votre machine il suffit de l&#8217;installer:</strong></span>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">apt-get install</span> openssh-server openssh-client
  </div>
</div>

Si vous êtes sous Windows, vous pouvez utiliser l&#8217;excellent <a title="puTTY" href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">puTTY</a> qui est un client SSH ainsi que PuttyGen pour la génération de clés.  
Nous allons partir du principe que notre utilisateur **amartin** souhaite se connecter à notre serveur en utilisant une authentification basée sur un couple clé publique / clé privée.

Pour mettre en place notre authentification nous allons devoir agir à la fois coté serveur mais également coté client.

<div>
  Imaginons que nous souhaitions que notre utilisateur amartin puisse, à partir de sa machine cliente, se connecter sur notre serveur en temps que root.
</div>

## <span style="text-decoration: underline;">Coté client:</span>

<div>
  Le mécanisme consiste à justifier de l&#8217;identité auprès du serveur en se basant sur une paire clés publique / privée. Il faut par contre bien garder en tête que cette clé privée est le seul moyen pour le serveur d&#8217;être sûr que la connexion est bien initié par un tiers de confiance, de ce fait il est indispensable de conserver cette clé confidentielle car quiconque est en sa possession est capable de se faire passer pour notre client.<br /> Il est toutefois possible d&#8217;ajouter une protection sur cette clé en la protégeant par une passphrase qu&#8217;il sera nécessaire de saisir lorsque l&#8217;on souhaitera utiliser notre clé.
</div>

<div>
  La clé publique, comme son nom l&#8217;indique est celle qui est connue par le serveur, et qui permet de d&#8217;assurer que notre client est bien celui attendu pour l&#8217;utilisateur demandé.
</div>

<div>
  <p>
    Pour commencer il va nous falloir générer une paire de clés pour notre utilisateur, il existe deux principaux algorithmes asymétriques de cryptage <a href="http://fr.wikipedia.org/wiki/Digital_Signature_Algorithm">DSA</a> et <a href="http://fr.wikipedia.org/wiki/Rivest_Shamir_Adleman">RSA</a>, vous pouvez utiliser l&#8217;un ou l&#8217;autre.
  </p>
</div>

<div>
  Pour la petite histoire sachez juste que le <strong>Digital Signature Algorithm</strong>, plus connu sous le sigle <strong>DSA</strong>, est un algorithme de signature numérique standardisée par le NSIT aux Etats-Unis, du temps où le RSA était encore breveté.
</div>

  


<div>
  <span style="text-decoration: underline;">Générons notre paire de clé:</span>
</div>

<div>
  <div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
    <div class="bash codecolorer">
      <span class="kw2">ssh-keygen</span> <span class="re5">-t</span> rsa <span class="re5">-C</span> <span class="sy0">&</span>quot;Alexandre Martin<span class="sy0">&</span>quot; <span class="re5">-f</span> amartin
    </div>
  </div>
</div>

Cette commande crééra une clé publique ET une clé privée, (si l&#8217;option &#8220;-f&#8221; n&#8217;est pas spécifiée, par défaut dans le répertoire .ssh de votre compte utilisateur).

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="sy0">&</span>gt;Generating public<span class="sy0">/</span>private rsa key pair.<br /> Enter passphrase <span class="br0">&#40;</span>empty <span class="kw1">for</span> no passphrase<span class="br0">&#41;</span>:<br /> Enter same passphrase again:<br /> Your identification has been saved <span class="kw1">in</span> amartin.<br /> Your public key has been saved <span class="kw1">in</span> amartin.pub.<br /> The key fingerprint is:<br /> 5c:ae:c4:<span class="nu0">36</span>:<span class="nu0">79</span>:fd:f6:4e:<span class="nu0">64</span>:<span class="nu0">75</span>:<span class="nu0">77</span>:<span class="nu0">92</span>:<span class="nu0">26</span>:<span class="nu0">19</span>:3a:ff Alexandre Martin<br /> The key<span class="st_h">'s randomart image is:<br /> +--[ RSA 2048]