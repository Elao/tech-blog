
Cela fait quelques temps que j'ai l'erreur "**Processing Dirty Regions**" en éditant mes classes de formulaires **Symfony** sous **Zend Studio**.
Le problème a été localisé par Krav3n sur le forum de Zend (un grand merci à lui). Ci-joint, son post traduit :

> J'ai trouvé la cause du problème.
> Mon soucis provenait du fait que j'utilisais Symfony 1.4 en tant que projet séparé, et chacune de mes applications Symfony l'utilisait dans leur include paths.
> Cela fonctionnait très bien à part pour les classes Filter et Form. Elles étendent  lib/form/addon/sfFormFilter.class.php et lib/form/addon/sfFormSymfony.class.php sous Symfony 1.4 qui étendent elles même le BaseForm.class.php qui est une classe créée dans vos applications Symfony (lib/form/BaseForm.class.php) lors de la première exécution de propel:build-model ou doctrine:build-mlodel. Cela pose un problème car le projet Symfony 1.4 ne sait rien des autres projets Symfony, ce qui pose donc un problème et l'erreur que l'on obtient.
>
> <span style="text-decoration: underline;">Pour résoudre ce problème, il suffit d'ajouter un des projets Symfony dans l'include path du projet Symfony 1.4 puis de lancer un "clean" du projet.</span>

J'espère que ça en aidera certains !

Source: <a href="http://forums.zend.com/viewtopic.php?f=59&t=6046" target="_blank">Forum Zend Studio</a>