
L'un des principaux problèmes rencontrés au cours de projets nécessitants l'intervention de plusieurs développeurs est l'uniformité du code et le respect des conventions de codage, qui ont (normalement) dû être mise en place.
Avec l'avènement de PHP depuis quelques années dans le milieu professionel, de plus en plus d'outils facilitent la vérification de ces normes de codage, leur but étant d'assurer la normalisation du code et leur respect.

Nous pouvons citer, pour PHP [PHP_CodeSniffer][1].
En complément de cet outil, il sera nécessaire de définir un ensemble de règles de configuration, nous utilisons aujourd'hui celles définies pour Symfony2 par [OpenSky][2].

## Installation et Configuration de "PHPCS"

[PHP_CodeSniffer][1] est disponible via PEAR, et est donc relativement facile à installer.

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    pear <span class="kw2">install</span> PHP_CodeSniffer
  </div>
</div>

On doit ensuite ajouter les règles pour Symfony2.

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw3">cd</span> <span class="sy0">/</span>path<span class="sy0">/</span>to<span class="sy0">/</span>pear<span class="sy0">/</span>PHP<span class="sy0">/</span>CodeSniffer<span class="sy0">/</span>Standards<br /> <span class="kw2">git clone</span> git:<span class="sy0">//</span>github.com<span class="sy0">/</span>opensky<span class="sy0">/</span>Symfony2-coding-standard.git Symfony2<br /> phpcs <span class="re5">--config-set</span> default_standard Symfony2
  </div>
</div>

Pour l'utiliser il suffit à présent de se rendre dans un de nos projets Symfony2 et de taper la commande suivante :

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    phpcs src<span class="sy0">/</span>
  </div>
</div>

Si vous ne respectez pas la norme de codage de Symfony2 PHPCS vous répondra de la manière suivante:

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    FILE: ...<span class="sy0">/</span>src<span class="sy0">/</span>Benji07<span class="sy0">/</span>BlogBundle<span class="sy0">/</span>Entity<span class="sy0">/</span>Post.php<br /> <span class="re5">