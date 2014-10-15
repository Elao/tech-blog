
Cela fait quelques temps que j&#8217;ai l&#8217;erreur &#8220;**Processing Dirty Regions**&#8221; en éditant mes classes de formulaires **Symfony** sous **Zend Studio**.  
Le problème a été localisé par Krav3n sur le forum de Zend (un grand merci à lui). Ci-joint, son post traduit :

> J&#8217;ai trouvé la cause du problème.  
> Mon soucis provenait du fait que j&#8217;utilisais Symfony 1.4 en tant que projet séparé, et chacune de mes applications Symfony l&#8217;utilisait dans leur include paths.  
> Cela fonctionnait très bien à part pour les classes Filter et Form. Elles étendent  lib/form/addon/sfFormFilter.class.php et lib/form/addon/sfFormSymfony.class.php sous Symfony 1.4 qui étendent elles même le BaseForm.class.php qui est une classe créée dans vos applications Symfony (lib/form/BaseForm.class.php) lors de la première exécution de propel:build-model ou doctrine:build-mlodel. Cela pose un problème car le projet Symfony 1.4 ne sait rien des autres projets Symfony, ce qui pose donc un problème et l&#8217;erreur que l&#8217;on obtient.
> 
> <span style="text-decoration: underline;">Pour résoudre ce problème, il suffit d&#8217;ajouter un des projets Symfony dans l&#8217;include path du projet Symfony 1.4 puis de lancer un &#8220;clean&#8221; du projet.</span>

J&#8217;espère que ça en aidera certains !

Source: <a href="http://forums.zend.com/viewtopic.php?f=59&t=6046" target="_blank">Forum Zend Studio</a>