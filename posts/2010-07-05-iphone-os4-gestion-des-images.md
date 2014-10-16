
Aujourd&#8217;hui, un petit truc au sujet des images et de leurs résolutions.

Lorsque votre application doit être compatible iPhone 4 et iPhone 3, le design nous pose toujours la question pour connaitre la résolution des images à utiliser.  
En effet, les deux téléphones ont une résolution différente : 320\*480 pour l&#8217;iPhone 3 et 640\*960 pour l&#8217;iPhone 4.

Il y a deux approches pour être compatibles : soit on ne prévoit qu&#8217;une taille &#8211; la plus grande &#8211; pour avoir la plus belle qualité et laisser l&#8217;iphone 3 redimensionner (on perdra moins en qualité dans ce sens), soit prévoir deux images pour chaque résolution. Cette dernière méthode peut faire peur aux développeurs qui se voient déjà en train de faire des &#8220;si sinon&#8221; en chaines.

En fait pas du tout, Apple a pensé à nous et nous propose de le faire à notre place en se basant sur une nomenclature strict pour le nom des images. Si vous avez une image nommée logo.png il suffit d&#8217;en faire une deuxième nommé logo@2x.png. C&#8217;est le même mécanisme que pour développer sur iPad où l&#8217;on doit nommer ses fichiers logo-ipad.png voir même logo@2x-ipad.png