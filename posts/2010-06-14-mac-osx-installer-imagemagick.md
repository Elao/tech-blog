
Bonjour !

Petit memo aujourd'hui sur l'installation d'ImageMagick sur un Mac OSX (Leopard 10.6)<br /> Pour ceux qui ne connaissent pas imageMagick fournit des outils de traitements d'image, translation, flip, miroir, rotation, scale, shear et bien sur transformation, ajustement des couleurs, sans oublier l'application "d'effects", l'écriture de texte, lignes, polygones, ellipses et courbes de Bézier.

### Prérequis

XCode 3.0 (Téléchargez le si nécessaire <a href="http://developer.apple.com/programs/mac/" target="_blank">ici</a>)

Les sources ImageMagick

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">wget</span> ftp:<span class="sy0">//</span>ftp.imagemagick.org<span class="sy0">/</span>pub<span class="sy0">/</span>ImageMagick<span class="sy0">/</span>ImageMagick.tar.gz
  </div>
</div>

### Compilation

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    $<span class="sy0">></span> <span class="kw3">cd</span> ImageMagick-6.6.2<br /> $<span class="sy0">></span> .<span class="sy0">/</span>configure<br /> $<span class="sy0">></span> <span class="kw2">make</span>
  </div>
</div>

Si tout s'est bien passé vous devriez avoir quelque chose de ce style:

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    X11 Configuration:<br />       X_CFLAGS        =<br />       X_PRE_LIBS      = <span class="re5">-lSM</span> <span class="re5">-lICE</span><br />       X_LIBS          = -L<span class="sy0">/</span>usr<span class="sy0">/</span>X11<span class="sy0">/</span>lib -R<span class="sy0">/</span>usr<span class="sy0">/</span>X11<span class="sy0">/</span>lib<br />       X_EXTRA_LIBS    =<br /> <br /> Options used to compile and link:<br />   PREFIX          = <span class="sy0">/</span>usr<span class="sy0">/</span><span class="kw3">local</span><br />   EXEC-PREFIX     = <span class="sy0">/</span>usr<span class="sy0">/</span><span class="kw3">local</span><br />   VERSION         = 6.6.2<br />   CC              = <span class="kw2">gcc</span> <span class="re5">-std</span>=gnu99 <span class="re5">-std</span>=gnu99<br />   CFLAGS          = -D_THREAD_SAFE -D_THREAD_SAFE <span class="re5">-fopenmp</span> <span class="re5">-g</span> <span class="re5">-O2</span> <span class="re5">-Wall</span> -D_THREAD_SAFE<br />   CPPFLAGS        = -I<span class="sy0">/</span>usr<span class="sy0">/</span>local<span class="sy0">/</span>include<span class="sy0">/</span>ImageMagick<br />   PCFLAGS         = <span class="re5">-fopenmp</span><br />   DEFS            = -DHAVE_CONFIG_H<br />   LDFLAGS         = -L<span class="sy0">/</span>usr<span class="sy0">/</span>X11<span class="sy0">/</span>lib -R<span class="sy0">/</span>usr<span class="sy0">/</span>X11<span class="sy0">/</span>lib<br />   MAGICK_LDFLAGS  = -L<span class="sy0">/</span>usr<span class="sy0">/</span>local<span class="sy0">/</span>lib -L<span class="sy0">/</span>usr<span class="sy0">/</span>X11<span class="sy0">/</span>lib -R<span class="sy0">/</span>usr<span class="sy0">/</span>X11<span class="sy0">/</span>lib<br />   LIBS            = <span class="re5">-lMagickCore</span> <span class="re5">-lfreetype</span> <span class="re5">-lpng</span> <span class="re5">-lXext</span> <span class="re5">-lXt</span> <span class="re5">-lSM</span> <span class="re5">-lICE</span> <span class="re5">-lX11</span> <span class="re5">-lbz2</span> <span class="re5">-lxml2</span> <span class="re5">-lz</span> <span class="re5">-lm</span> <span class="re5">-lgomp</span> <span class="re5">-lclparser</span> <span class="re5">-framework</span> OpenCL -L<span class="sy0">/</span>System<span class="sy0">/</span>Library<span class="sy0">/</span>Frameworks<span class="sy0">/</span>OpenCL.framework<span class="sy0">/</span>Versions<span class="sy0">/</span>A<span class="sy0">/</span>Libraries <span class="re5">-lm</span> <span class="re5">-lpthread</span><br />   CXX             = <span class="kw2">g++</span><br />   CXXFLAGS        = <span class="re5">-g</span> <span class="re5">-O2</span> -D_THREAD_SAFE
  </div>
</div>

### Installation

La commande ci dessous doit vous créer le logo image magick !

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">make</span> <span class="kw2">install</span>
  </div>
</div>

### Vérification

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="sy0">/</span>usr<span class="sy0">/</span>local<span class="sy0">/</span>bin<span class="sy0">/</span>convert logo: logo.gif
  </div>
</div>