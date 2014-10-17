# **[EDIT]** Cet article est devenu obsolète, il n'est donc pas recommandé de s'en servir comme base de travail.

Bonjour à toutes et à tous,

Nous allons voir aujourd'hui comment créer un Helper de vue sous Symfony2. Dans l'exemple qui suit, nous allons créer un Helper <strong><i>MyHelper</i></strong> dans le Bundle <strong><i>MyBundle</i></strong> en cinq étapes. Je manque considérablement d'imagination et je n'ai donc pas rédigé de méthode concrète ; je m'en remets à votre propre imagination pour rédiger des traitements plus utiles (exemple : pour un tableau de chaînes donné, l'afficher dans des balises, pour une chaîne passée en paramètre, lui appliquer des traitements, avec l'identifiant d'une vidéo Youtube, retourner l'URL complète, etc.)

1. Déclarer le service dans un fichier de configuration XML :

  ```
  <!-- Application/MyBundle/Resources/config/helper.xml -->
  <?xml version="1.0" encoding="UTF-8" ?>

  <container xmlns="http://www.symfony-project.org/schema/dic/services"
     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xsi:schemaLocation="http://www.symfony-project.org/schema/dic/services http://www.symfony-project.org/schema/dic/services/services-1.0.xsd">

      <services>
          <service id="templating.helper.my_helper" class="Application\MyBundleBundle\Helper\MyHelper">
              <tag name="templating.helper" alias="alias.de.mon.helper" />
          </service>
      </services>

  </container>
  ```

2. Déclarer le fichier XML dans la classe chargeant les définitions de services que l'on souhaite obtenir par Injection de dépendance :

  ```
  /* Application/MyBundleBundle/DependencyInjection/MyBundleExtension.php */

  namespace Application\MyBundleBundle\DependencyInjection;

  use Symfony\Component\DependencyInjection\Extension\Extension;
  use Symfony\Component\DependencyInjection\ContainerBuilder;
  use Symfony\Component\DependencyInjection\Loader\XmlFileLoader;

  class MyBundleExtension extends Extension
  {

      public function configLoad($config, ContainerBuilder $container)
      {
          $loader = new XmlFileLoader($container, __DIR__ . '/../Resources/config');
          $loader->load('helper.xml');
      }

     / * le reste sans changement */
  ```

3. Eventuellement, modifier le fichier de configuration globale pour prendre en compte l'injection de dépendance de notre Bundle :

  ```
  # app/config/config.yml

  mybundle.config: ~
  ```

4. Rédiger la classe "Helper". Celle-ci étend la classe abstraite *Symfony\Component\Templating\Helper\Helper* et doit donc implémenter la méthode *getName* :

  ``
  /* Application/MyBundleBundle/Helper/MyHelper.php */

  <?php

  namespace Application\MyBundleBundle\Helper ;

  use Symfony\Component\Templating\Helper\Helper ;

  class MyHelper extends Helper
  {
      public function doSomething($myString)
      {
           // Do something with $myString
           return $myString ;
      }

      public function getName()
      {
          return 'MyHelper' ;
      }
  }
  ```

5. Utilisation dans les vues :

  ```
  <?php echo $view['alias.de.mon.helper']->doSomething($myString) ?>
  ```