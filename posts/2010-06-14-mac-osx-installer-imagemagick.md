
Bonjour !

<p style="text-align: justify;">
  Petit memo aujourd&#8217;hui sur l&#8217;installation d&#8217;ImageMagick sur un Mac OSX (Leopard 10.6)<br /> Pour ceux qui ne connaissent pas imageMagick fournit des outils de traitements d&#8217;image, translation, flip, miroir, rotation, scale, shear et bien sur transformation, ajustement des couleurs, sans oublier l&#8217;application &#8220;d&#8217;effects&#8221;, l&#8217;écriture de texte, lignes, polygones, ellipses et courbes de Bézier.
</p>

**Prérequis:**

XCode 3.0 (Téléchargez le si nécessaire <a href="http://developer.apple.com/programs/mac/" target="_blank">ici</a>)

Les sources ImageMagick

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">wget</span> ftp:<span class="sy0">//</span>ftp.imagemagick.org<span class="sy0">/</span>pub<span class="sy0">/</span>ImageMagick<span class="sy0">/</span>ImageMagick.tar.gz
  </div>
</div>

**Compilation:**

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    $<span class="sy0">></span> <span class="kw3">cd</span> ImageMagick-6.6.2<br /> $<span class="sy0">></span> .<span class="sy0">/</span>configure<br /> $<span class="sy0">></span> <span class="kw2">make</span>
  </div>
</div>

Si tout s&#8217;est bien passé vous devriez avoir quelque chose de ce style:

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    X11 Configuration:<br /> &nbsp; &nbsp; &nbsp; X_CFLAGS &nbsp; &nbsp; &nbsp; &nbsp;=<br /> &nbsp; &nbsp; &nbsp; X_PRE_LIBS &nbsp; &nbsp; &nbsp;= <span class="re5">-lSM</span> <span class="re5">-lICE</span><br /> &nbsp; &nbsp; &nbsp; X_LIBS &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;= -L<span class="sy0">/</span>usr<span class="sy0">/</span>X11<span class="sy0">/</span>lib -R<span class="sy0">/</span>usr<span class="sy0">/</span>X11<span class="sy0">/</span>lib<br /> &nbsp; &nbsp; &nbsp; X_EXTRA_LIBS &nbsp; &nbsp;=<br /> <br /> Options used to compile and link:<br /> &nbsp; PREFIX &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;= <span class="sy0">/</span>usr<span class="sy0">/</span><span class="kw3">local</span><br /> &nbsp; EXEC-PREFIX &nbsp; &nbsp; = <span class="sy0">/</span>usr<span class="sy0">/</span><span class="kw3">local</span><br /> &nbsp; VERSION &nbsp; &nbsp; &nbsp; &nbsp; = 6.6.2<br /> &nbsp; CC &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;= <span class="kw2">gcc</span> <span class="re5">-std</span>=gnu99 <span class="re5">-std</span>=gnu99<br /> &nbsp; CFLAGS &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;= -D_THREAD_SAFE -D_THREAD_SAFE <span class="re5">-fopenmp</span> <span class="re5">-g</span> <span class="re5">-O2</span> <span class="re5">-Wall</span> -D_THREAD_SAFE<br /> &nbsp; CPPFLAGS &nbsp; &nbsp; &nbsp; &nbsp;= -I<span class="sy0">/</span>usr<span class="sy0">/</span>local<span class="sy0">/</span>include<span class="sy0">/</span>ImageMagick<br /> &nbsp; PCFLAGS &nbsp; &nbsp; &nbsp; &nbsp; = <span class="re5">-fopenmp</span><br /> &nbsp; DEFS &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;= -DHAVE_CONFIG_H<br /> &nbsp; LDFLAGS &nbsp; &nbsp; &nbsp; &nbsp; = -L<span class="sy0">/</span>usr<span class="sy0">/</span>X11<span class="sy0">/</span>lib -R<span class="sy0">/</span>usr<span class="sy0">/</span>X11<span class="sy0">/</span>lib<br /> &nbsp; MAGICK_LDFLAGS &nbsp;= -L<span class="sy0">/</span>usr<span class="sy0">/</span>local<span class="sy0">/</span>lib -L<span class="sy0">/</span>usr<span class="sy0">/</span>X11<span class="sy0">/</span>lib -R<span class="sy0">/</span>usr<span class="sy0">/</span>X11<span class="sy0">/</span>lib<br /> &nbsp; LIBS &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;= <span class="re5">-lMagickCore</span> <span class="re5">-lfreetype</span> <span class="re5">-lpng</span> <span class="re5">-lXext</span> <span class="re5">-lXt</span> <span class="re5">-lSM</span> <span class="re5">-lICE</span> <span class="re5">-lX11</span> <span class="re5">-lbz2</span> <span class="re5">-lxml2</span> <span class="re5">-lz</span> <span class="re5">-lm</span> <span class="re5">-lgomp</span> <span class="re5">-lclparser</span> <span class="re5">-framework</span> OpenCL -L<span class="sy0">/</span>System<span class="sy0">/</span>Library<span class="sy0">/</span>Frameworks<span class="sy0">/</span>OpenCL.framework<span class="sy0">/</span>Versions<span class="sy0">/</span>A<span class="sy0">/</span>Libraries <span class="re5">-lm</span> <span class="re5">-lpthread</span><br /> &nbsp; CXX &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; = <span class="kw2">g++</span><br /> &nbsp; CXXFLAGS &nbsp; &nbsp; &nbsp; &nbsp;= <span class="re5">-g</span> <span class="re5">-O2</span> -D_THREAD_SAFE
  </div>
</div>

**Installation:**

La commande ci dessous doit vous créer le logo image magick !

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw2">make</span> <span class="kw2">install</span>
  </div>
</div>

**Vérification:**

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="sy0">/</span>usr<span class="sy0">/</span>local<span class="sy0">/</span>bin<span class="sy0">/</span>convert logo: logo.gif
  </div>
</div>