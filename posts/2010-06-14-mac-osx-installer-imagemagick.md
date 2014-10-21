
Bonjour !

Petit memo aujourd'hui sur l'installation d'ImageMagick sur un Mac OSX (Leopard 10.6)

Pour ceux qui ne connaissent pas imageMagick fournit des outils de traitements d'image, translation, flip, miroir, rotation, scale, shear et bien sur transformation, ajustement des couleurs, sans oublier l'application "d'effects", l'écriture de texte, lignes, polygones, ellipses et courbes de Bézier.

### Prérequis

XCode 3.0 (Téléchargez le si nécessaire <a href="http://developer.apple.com/programs/mac/" target="_blank">ici</a>)

Les sources ImageMagick

```
wget ftp://ftp.imagemagick.org/pub/ImageMagick/ImageMagick.tar.gz
```

### Compilation

```
$> cd ImageMagick-6.6.2
$> ./configure
$> make
```

Si tout s'est bien passé vous devriez avoir quelque chose de ce style:

```
X11 Configuration:
    X_CFLAGS        =
    X_PRE_LIBS      = -lSM -lICE
    X_LIBS          = -L/usr/X11/lib -R/usr/X11/lib
    X_EXTRA_LIBS    =

Options used to compile and link:
    PREFIX          = /usr/local
    EXEC-PREFIX     = /usr/local
    VERSION         = 6.6.2
    CC              = gcc -std=gnu99 -std=gnu99
    CFLAGS          = -D_THREAD_SAFE -D_THREAD_SAFE -fopenmp -g -O2 -Wall -D_THREAD_SAFE
    CPPFLAGS        = -I/usr/local/include/ImageMagick
    PCFLAGS         = -fopenmp
    DEFS            = -DHAVE_CONFIG_H
    LDFLAGS         = -L/usr/X11/lib -R/usr/X11/lib
    MAGICK_LDFLAGS  = -L/usr/local/lib -L/usr/X11/lib -R/usr/X11/lib
    LIBS            = -lMagickCore -lfreetype -lpng -lXext -lXt -lSM -lICE -lX11 -lbz2 -lxml2 -lz -lm -lgomp -lclparser -framework OpenCL -L/System/Library/Frameworks/OpenCL.framework/Versions/A/Libraries -lm -lpthread
    CXX             = g++
    CXXFLAGS        = -g -O2 -D_THREAD_SAFE

### Installation

La commande ci dessous doit vous créer le logo image magick !

```
make install
```

### Vérification

```
/usr/local/bin/convert logo: logo.gif
```