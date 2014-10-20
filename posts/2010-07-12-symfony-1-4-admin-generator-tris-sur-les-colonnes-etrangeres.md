Bonjour,

Je vous propose une méthode permettant d'effectuer des tris sur les colonnes étrangères dans les modules générés grâce à l'Admin Generator de Symfony. Ce n'est sans doute pas la seule manière de procéder, aussi, toute suggestion ou critique constructive est la bienvenue !

L'exemple qui suit a été réalisé avec Symfony dans sa version 1.4 et l'ORM Doctrine. Il met en oeuvre deux entités reliées par une association de type 1:n :

- l'objet du modèle **Produit** qui appartient à une catégorie,
- l'objet du modèle **Catégorie** qui contient plusieurs produits.

Le schéma de la base de données, volontairement simplifié, est le suivant :

```
Product:
  columns:
    id:            { type: integer(4), primary: true, autoincrement: true }
    name:          { type: string(255), notnull: true }
    category_id:   { type: integer(4), notnull: true }
    price:         { type: decimal, notnull: true }
  relations:
    Category:      { local: category_id, foreign: id, foreignAlias: Products, onDelete: CASCADE }

Category:
  columns:
    id:            { type: integer(4), primary: true, autoincrement: true }
    name:          { type: string(100), notnull: true }
```

Nous partons du principe que vous disposez d'un projet Symfony "blanc" déjà créé et que la connexion à la base de données a déjà été configurée. Si ce n'est pas le cas, je vous invite à <a title="installer Symfony" href="http://www.symfony-project.org/getting-started/1_4/en/03-Symfony-Installation" target="_blank">installer Symfony</a> et <a title="créer un projet Symfony" href="http://www.symfony-project.org/getting-started/1_4/en/04-Project-Setup" target="_blank">créer votre premier projet</a> selon la méthode préconisée par les créateurs de Symfony. Pour les plus pressés d'entre vous, vous avez la possibilité de télécharger une <a title="Sandbox Symfony" href="http://www.symfony-project.org/get/sf_sandbox_1_4.zip" target="_blank">sandbox Symfony</a> prête à l'emploi.

Commençons par construire les classes de notre modèle ainsi que notre base de données, créer une application Backend, puis à l'aide de l'Admin Generator, générer le module associé à notre OM "Product". Pour cela, ouvrons une ligne de commande, plaçons-nous à la racine de notre projet et lançons successivement les commandes symfony suivantes :

```
./symfony doctrine:build --all
./symfony generate:app backend
./symfony doctrine:generate-admin backend Product
./symfony cc
```

A présent, à l'aide de notre navigateur préféré, rendons-nous à l'URL `http://mon_hote_virtuel/backend_dev.php/product` (remplacez **mon_hote_virtuel** par celui que vous avez défini !) et admirons le résultat :

<img src="/blog/medias/2010-07-12-symfony-1-4-admin-generator-tris-sur-les-colonnes-etrangeres/sf14_tris_fk_1.png" alt="sf14 tris fk 1 Symfony 1.4   Admin Generator   Tris sur les colonnes étrangères" width="75%" />

Bon, je vous le concède, il n'y a pas de quoi s'enthousiasmer, nous pourrions améliorer l'apparence de notre page et surtout, tout cela manque un peu de matière ... Nous allons donc injecter quelques données dans la base. Concernant le style de la page, je vous fais confiance pour améliorer tout cela à l'aide d'une CSS ; pour ma part, je ne m'y risquerai pas de peur de faire étalage de mon manque flagrant de talent graphique ...

Pour alimenter la base de données, voici le jeu de fixtures que nous allons charger :

```
# data/fixtures/fixtures.yml
Category:
  Category_1:
    id: 1
    name: Categorie 1
  Category_2:
    id: 2
    name: Categorie 2

Product:
<?php for($i = 1 ; $i <= 10 ; $i++) : ?>
  Produit_<?php echo $i ?>:
    id: <?php echo $i."\\n" ?>
    name: Produit_<?php echo $i."\\n" ?>
    price: <?php echo ($i * 1.0)."\\n" ?>
    Category: Category_<?php echo ($i % 2 == 0) ? 1 : 2 ?><?php echo "\\n" ?>
<?php endfor ?>
```

Pour injecter les données en base, lançons la commande adéquate ...

```
./symfony doctrine:data-load
```

... et émerveillons-nous devant le résultat ainsi obtenu :

<img title="sf14_tris_fk_2" src="/blog/medias/2010-07-12-symfony-1-4-admin-generator-tris-sur-les-colonnes-etrangeres/sf14_tris_fk_2.png" alt="sf14 tris fk 2 Symfony 1.4   Admin Generator   Tris sur les colonnes étrangères" height="50%" />

Nous allons à présent modifier le fichier generator.yml (apps/backend/modules/product/config) pour afficher l'intitulé de la catégorie plutôt que son identifiant :

```
config:
    actions: ~
    fields: ~
    list:
      display: [ =name, Category, price ]
    filter: ~
    form:   ~
    edit:   ~
    new:    ~
```

Vous remarquerez que les en-têtes de colonnes des champs natifs de la table "Product" permettent de trier le contenu de la colonne mais ça n'est pas le cas de la colonne "Category". Cela étant, vu le titre de cet article, cela ne devrait pas réellement vous étonner ... Car nous allons justement voir une manière d'activer les tris sur ces colonnes étrangères. Et pour cela, nous allons surcharger le thème de l'Admin Generator, de manière notamment à bénéficier de cette fonctionnalité sur tous les modules que nous créerons.

Comme nous utilisons l'ORM Doctrine, le thème sur lequel s'appuie l'Admin Generator se trouve dans la distribution de Symfony, dans le sous-répertoire `lib/plugins/sfDoctrinePlugin/data/generator/sfDoctrineModule` :

<img title="Themes de l'admin generator de Doctrine" src="/blog/medias/2010-07-12-symfony-1-4-admin-generator-tris-sur-les-colonnes-etrangeres/sf14_tris_fk_4.png" alt="sf14 tris fk 4 Symfony 1.4   Admin Generator   Tris sur les colonnes étrangères" height="25%" />

Nous constatons qu'il existe deux thèmes dans ce sous-répertoire : "admin" et "default". Lequel des deux devons-nous surcharger ? Celui que nous utilisons actuellement ! Et pour connaître ce thème, il suffit de consulter le fichier generator.yml de notre module "Product", qui nous informe que le thème utilisé est "admin" :

```
generator:
  class: sfDoctrineGenerator
  param:
    model_class:          Product
    theme:                 admin  # Le thème utilisé par l'admin generator ; autre possibilité : "default"
```

Pour pouvoir surcharger le thème, il va nous falloir copier le contenu du sous-répertoire "admin" du plugin Doctrine (**ie** les trois sous-dossiers **parts**, **skeleton** et **template**) dans le dossier `data/generator/sfDoctrineModule/admin` de notre projet Symfony (ce dossier n'existe pas, nous devons le créer manuellement) afin d'obtenir le résultat suivant :

<img title="Theme admin generator surchargé" src="/blog/medias/2010-07-12-symfony-1-4-admin-generator-tris-sur-les-colonnes-etrangeres/sf14_tris_fk_5.png" alt="sf14 tris fk 5 Symfony 1.4   Admin Generator   Tris sur les colonnes étrangères" height="20%" />

**Attention :** si vous avez installé Symfony depuis un gestionnaire de sources, prenez soin de copier ces trois répertoires en excluant les dossiers de versioning (.svn, .git ...) :

On copie les répertoires dans un dossier temporaire (SYMFONY_DIR = le répertoire où vous avez placé votre distribution Symfony).

```
cp -r SYMFONY_DIR/lib/plugins/sfDoctrinePlugin/data/generator/sfDoctrineModule/admin ~/Temp/admin
cd ~/Temp/admin
```

On supprime les répertoires .svn récursivement

```
rm -r $(grep -lnr ".svn" *)
```

On crée le répertoire /data/generator/sfDoctrineModule dans notre projet. Adaptez le chemin et le nom du projet en fonction de votre propre configuration !

```
cd /path_to_my_workspace/my_project_name/data
mkdir generator
cd generator
mkdir sfDoctrineModule
```

Copions le dossier admin que nous avions placé dans un répertoire temporaire dans le répertoire que nous venons de créer.

```
cd sfDoctrineModule
cp -r ~/Temp/admin/ ./admin
```

A présent, nous allons nous assurer que les fichiers du thème que nous venons de placer dans notre projet sont bien ceux qu'utilise l'Admin Generator. Pour cela, on va modifier un des fichiers du répertoire "templates" :

```
# data/generator/sfDoctrineModule/admin/template/templates/_list_header.php
<h1>Une template que j'ai surchargée !</h1>
```

Rechargeons la page du Backend listant les produits et constatons le résultat : notre modification a bien été prise en compte. Si ce n'est pas le cas, l'incontournable **symfony cc** devrait résoudre le problème ! Maintenant que nous savons comment surcharger un thème, nous pouvons le redéfinir afin d'inclure le tri sur les clés étrangères. Par ailleurs, en modifiant le partial _list_header.php, vous aurez sans doute remarqué le découpage des différentes vues et les partiels utilisés par l'Admin Generator ; les noms des fichiers sont suffisamment explicites pour comprendre à quoi chacun de ces fichiers correspond. En l'occurrence, nous souhaitons modifier les en-têtes de colonnes afin d'ajouter notre fameux tri sur les colonnes étrangères et nous nous doutons bien qu'il va falloir pour cela modifier le partial "_list_th_tabular.php". Voici le contenu de ce fichier :

```
#data/generator/sfDoctrineModule/admin/template/templates/_list_th_tabular.php
<?php foreach ($this->configuration->getValue('list.display') as $name => $field): ?>
[?php slot('sf_admin.current_header') ?]
<th class="sf_admin_<?php echo strtolower($field->getType()) ?> sf_admin_list_th_<?php echo $name ?>">
<?php if ($field->isReal()): ?>
  [?php if ('<?php echo $name ?>' == $sort[0]): ?]
    [?php echo link_to(__('<?php echo $field->getConfig('label', '', true) ?>', array(), '<?php echo $this->getI18nCatalogue() ?>'), '@<?php echo $this->getUrlForAction('list') ?>', array('query_string' => 'sort=<?php echo $name ?>&amp;sort_type='.($sort[1] == 'asc' ? 'desc' : 'asc'))) ?]
    [?php echo image_tag(sfConfig::get('sf_admin_module_web_dir').'/images/'.$sort[1].'.png', array('alt' => __($sort[1], array(), 'sf_admin'), 'title' => __($sort[1], array(), 'sf_admin'))) ?]
  [?php else: ?]
    [?php echo link_to(__('<?php echo $field->getConfig('label', '', true) ?>', array(), '<?php echo $this->getI18nCatalogue() ?>'), '@<?php echo $this->getUrlForAction('list') ?>', array('query_string' => 'sort=<?php echo $name ?>&amp;sort_type=asc')) ?]
  [?php endif; ?]
<?php else: ?>
  [?php echo __('<?php echo $field->getConfig('label', '', true) ?>', array(), '<?php echo $this->getI18nCatalogue() ?>') ?]
<?php endif; ?>
</th>
[?php end_slot(); ?]
<?php echo $this->addCredentialCondition("[?php include_slot('sf_admin.current_header') ?]", $field->getConfig()) ?>
<?php endforeach; ?>
```

Avouez que le code est un petit peu intimidant ... On note la présence de balises <?php ?> comme nous avons l'habitude d'en rencontrer mais également des pseudo-balises du type [?php ... ?]. L'Admin Generator va parser ce fichier une première fois afin de générer un fichier PHP et le placer dans le cache. Les balises

```
#cache/dev/backend/modules/autoProduct/templates/_list_th_tabular.php
<?php slot('sf_admin.current_header') ?>
<th class="sf_admin_text sf_admin_list_th_name">
  <?php if ('name' == $sort[0]): ?>
    <?php echo link_to(__('Name', array(), 'messages'), '@product', array('query_string' => 'sort=name&amp;sort_type='.($sort[1] == 'asc' ? 'desc' : 'asc'))) ?>
    <?php echo image_tag(sfConfig::get('sf_admin_module_web_dir').'/images/'.$sort[1].'.png', array('alt' => __($sort[1], array(), 'sf_admin'), 'title' => __($sort[1], array(), 'sf_admin'))) ?>
  <?php else: ?>
    <?php echo link_to(__('Name', array(), 'messages'), '@product', array('query_string' => 'sort=name&amp;sort_type=asc')) ?>
  <?php endif; ?>
</th>
<?php end_slot(); ?>
<?php include_slot('sf_admin.current_header') ?><?php slot('sf_admin.current_header') ?>
<th class="sf_admin_text sf_admin_list_th_Category">
  <?php echo __('Category', array(), 'messages') ?>
</th>
<?php end_slot(); ?>
<?php include_slot('sf_admin.current_header') ?><?php slot('sf_admin.current_header') ?>
<th class="sf_admin_text sf_admin_list_th_price">
  <?php if ('price' == $sort[0]): ?>
    <?php echo link_to(__('Price', array(), 'messages'), '@product', array('query_string' => 'sort=price&amp;sort_type='.($sort[1] == 'asc' ? 'desc' : 'asc'))) ?>
    <?php echo image_tag(sfConfig::get('sf_admin_module_web_dir').'/images/'.$sort[1].'.png', array('alt' => __($sort[1], array(), 'sf_admin'), 'title' => __($sort[1], array(), 'sf_admin'))) ?>
  <?php else: ?>
    <?php echo link_to(__('Price', array(), 'messages'), '@product', array('query_string' => 'sort=price&amp;sort_type=asc')) ?>
  <?php endif; ?>
</th>
<?php end_slot(); ?>
<?php include_slot('sf_admin.current_header') ?>
```

Ce code nous est tout de même un peu plus familier. Mais revenons à nos moutons ! Vous aurez sans doute noté dans le premier fichier la condition if ($field->isReal()) ; comme vous vous en doutez, c'est dans la clause "else" que nous allons gérer le cas des colonnes "virtuelles" afin de créer un lien permettant d'effectuer un tri sur ces colonnes.

```
<?php if ($field->isReal()): ?>
   # Si le champ est "réel" (comprendre "natif", "correspondant réellement à un champ de la table en BD")
   # Le code original reste inchangé
   # ....
<?php else : ?>
   # Nous allons ici créer un lien hypertexte permettant d'effectuer un tri sur les colonnes "virtuelles"
<?php endif ?>
```

Voici le code à placer entre les balises "**else**" et "**endif**" dans le fichier `/data/generator/sfDoctrineModule/admin/template/templates/_list_th_tabular.php` :

```
<?php else: ?>
 <?php if($this->configuration->getValue('list.fields.'.$name.'.sort_key')) : ?>
  [?php echo link_to(__('<?php echo $field->getConfig('label', '', true) ?>', array(), '<?php echo $this->getI18nCatalogue() ?>'), '@<?php echo $this->getUrlForAction('list') ?>', array('query_string' => 'sort=<?php echo $this->configuration->getValue('list.fields.'.$name.'.sort_key') ?>&amp;sort_type='.($sort[1] == 'asc' ? 'desc' : 'asc'))) ?]
  [?php if('<?php echo $this->configuration->getValue('list.fields.'.$name.'.sort_key') ?>' == $sort[0]): ?]
   [?php echo image_tag(sfConfig::get('sf_admin_module_web_dir').'/images/'.$sort[1].'.png', array('alt' => __($sort[1], array(), 'sf_admin'), 'title' => __($sort[1], array(), 'sf_admin'))) ?]
  [?php endif; ?]
 <?php else: ?>
  [?php echo __('<?php echo $field->getConfig('label', '', true) ?>', array(), '<?php echo $this->getI18nCatalogue() ?>') ?]
 <?php endif?>
<?php endif; ?>
```

Que venons-nous de faire ? Nous venons simplement de modifier le code pour qu'un lien soit généré lorsqu'une clé "sort_key" est déclarée sur les champs virtuels dans le fichier de configuration generator.yml :

```
<?php if($this->configuration->getValue('list.fields.'.$name.'.sort_key')) : ?>
```

A présent, modifions le fichier generator.yml pour indiquer à l'Admin Generator que le champ virtuel "Category" doit faire l'objet d'un tri :

```
# apps/backend/modules/product/config/generator.yml
# ...
  config:
    actions: ~
    fields: ~
    list:
      display: [ =name, Category, price ]
      fields:
        Category:
          sort_key: cat.name
# Le reste sans changement
```

Si nous rechargeons la page du Backend, nous pouvons constater que l'en-tête de colonne "Category" se présente désormais sous la forme d'un lien hypertexte qui ajoute à l'URL un paramètre "sort_key" ayant pour valeur "cat.name". Il ne nous reste plus qu'à modifier le code des actions et du modèle pour rendre notre lien fonctionnel. Pour commencer, nous allons créer une méthode dans la classe ProductTable permettant de rapatrier les objets de type "Product" avec les jointures nécessaires sur la table "Category" et c'est celle-ci que nous utiliserons dans le Backoffice pour afficher la liste des produits :

```
#lib/model/doctrine/ProductTable.class.php
class ProductTable extends Doctrine_Table
{
  public function retrieveProductsForBackend()
  {
    return Doctrine_Query::create()->from('Product p')->innerJoin('p.Category cat') ;
  }
}
```

Noter que l'alias défini sur Category ('cat') doit correspondre à celui mentionné dans le fichier generator.yml (cat.name). A présent, nous allons modifier le fichier generator.yml pour que notre méthode soit utilisée dans le Backend :

```
#apps/backend/modules/product/config/generator.yml
      list:
        display: [ =name, Category, price ]
        fields:
          Category:
            sort_key: cat.name
        table_method: retrieveProductsForBackend
```

Rechargeons la liste des produits dans notre navigateur. Vérifions grâce à la Web Debug Toolbar que c'est bien notre méthode qui est appelée :

```
SELECT COUNT(*) AS num_results FROM (SELECT  p.id FROM product p INNER JOIN category c ON p.category_id = c.id GROUP BY p.id)
```

OK, tout est correct, la jointure sur la table Category est bien présente. Maintenant, testons notre lien pour voir si le tri est opérationnel. Et là, c'est la déception ! Les produits demeurent désespérément triés par nom, notre tri ne fonctionne pas ! En effet, il nous reste une petite modification à apporter. Par défaut, l'Admin Generator vérifie que l'on a bien affaire à un champ natif pour appliquer un tri. Nous allons supprimer ce comportement par défaut. Pour cela, il faut modifier le fichier `data/generator/sfDoctrineModule/admin/parts/sortingAction.php` :

```
protected function isValidSortColumn($column)
{
  return Doctrine::getTable('<?php echo $this->getModelClass() ?>')->hasColumn($column);
}
```

Remplacer le code ci-dessus par celui-ci :

```
protected function isValidSortColumn($column)
{
  $configVal=false;
  foreach($this->configuration->getValue(‘list.fields’) as $field => $value)
  {
     if($this->configuration->getValue(‘list.fields.’.$field.’.sort_key’,null)==$column)
         $configVal=true;
  }
  return (Doctrine::getTable('<?php echo $this->getModelClass() ?>')->hasColumn($column) || $configVal) ;
}
```

Voilà ! Le tri sur notre colonne "Category" est fonctionnel.

Nous avons obtenu le résultat que nous souhaitions, mais il existe peut-être d'autres manières de procéder. Si vous avez des suggestions ou des remarques, n'hésitez pas à poster un commentaire ! Ca nous fera plaisir et ça fera sans doute avancer le schmilblick ...