
<p class="standard">
  Bonjour à toutes et à tous,
</p>

<p class="standard">
  Nous allons voir aujourd'hui comment créer un Helper de vue sous Symfony2. Dans l'exemple qui suit, nous allons créer un Helper <i>MyHelper</i> dans le Bundle <i>MyBundle</i> en cinq étapes. Je manque considérablement d'imagination et je n'ai donc pas rédigé de méthode concrète ; je m'en remets à votre propre imagination pour rédiger des traitements plus utiles (exemple : pour un tableau de chaînes donné, l'afficher dans des balises, pour une chaîne passée en paramètre, lui appliquer des traitements, avec l'identifiant d'une vidéo Youtube, retourner l'URL complète, etc.)
</p>

1. Déclarer le service dans un fichier de configuration XML :

<div class="codecolorer-container xml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="xml codecolorer">
    <span class="sc-1"><!-- Application/MyBundle/Resources/config/helper.xml --></span><br /> <span class="sc3"><span class="re1"><?xml</span> <span class="re0">version</span>=<span class="st0">"1.0"</span> <span class="re0">encoding</span>=<span class="st0">"UTF-8"</span> <span class="re2">?></span></span><br /> <br /> <span class="sc3"><span class="re1"><container</span> <span class="re0">xmlns</span>=<span class="st0">"http://www.symfony-project.org/schema/dic/services"</span></span><br /> <span class="sc3">    <span class="re0">xmlns:xsi</span>=<span class="st0">"http://www.w3.org/2001/XMLSchema-instance"</span></span><br /> <span class="sc3">    <span class="re0">xsi:schemaLocation</span>=<span class="st0">"http://www.symfony-project.org/schema/dic/services http://www.symfony-project.org/schema/dic/services/services-1.0.xsd"</span><span class="re2">></span></span><br /> <br />     <span class="sc3"><span class="re1"><services<span class="re2">></span></span></span><br />         <span class="sc3"><span class="re1"><service</span> <span class="re0">id</span>=<span class="st0">"templating.helper.my_helper"</span> <span class="re0">class</span>=<span class="st0">"Application\MyBundleBundle\Helper\MyHelper"</span><span class="re2">></span></span><br />             <span class="sc3"><span class="re1"><tag</span> <span class="re0">name</span>=<span class="st0">"templating.helper"</span> <span class="re0">alias</span>=<span class="st0">"alias.de.mon.helper"</span> <span class="re2">/></span></span><br />         <span class="sc3"><span class="re1"></service<span class="re2">></span></span></span><br />     <span class="sc3"><span class="re1"></services<span class="re2">></span></span></span><br /> <br /> <span class="sc3"><span class="re1"></container<span class="re2">></span></span></span>
  </div>
</div>

2. Déclarer le fichier XML dans la classe chargeant les définitions de services que l'on souhaite obtenir par Injection de dépendance :

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="coMULTI">/* Application/MyBundleBundle/DependencyInjection/MyBundleExtension.php */</span><br /> <br /> <span class="kw2">namespace</span> Application\MyBundleBundle\DependencyInjection<span class="sy0">;</span><br /> <br /> <span class="kw2">use</span> Symfony\Component\DependencyInjection\Extension\Extension<span class="sy0">;</span><br /> <span class="kw2">use</span> Symfony\Component\DependencyInjection\ContainerBuilder<span class="sy0">;</span><br /> <span class="kw2">use</span> Symfony\Component\DependencyInjection\Loader\XmlFileLoader<span class="sy0">;</span><br /> <br /> <span class="kw2">class</span> MyBundleExtension <span class="kw2">extends</span> Extension<br /> <span class="br0">&#123;</span><br /> <br />     <span class="kw2">public</span> <span class="kw2">function</span> configLoad<span class="br0">&#40;</span><span class="re0">$config</span><span class="sy0">,</span> ContainerBuilder <span class="re0">$container</span><span class="br0">&#41;</span><br />     <span class="br0">&#123;</span><br />         <span class="re0">$loader</span> <span class="sy0">=</span> <span class="kw2">new</span> XmlFileLoader<span class="br0">&#40;</span><span class="re0">$container</span><span class="sy0">,</span> __DIR__ <span class="sy0">.</span> <span class="st_h">'/../Resources/config'</span><span class="br0">&#41;</span><span class="sy0">;</span><br />         <span class="re0">$loader</span><span class="sy0">-></span><span class="me1">load</span><span class="br0">&#40;</span><span class="st_h">'helper.xml'</span><span class="br0">&#41;</span><span class="sy0">;</span><br />     <span class="br0">&#125;</span><br /> <br />    <span class="sy0">/</span> <span class="sy0">*</span> le reste sans changement <span class="sy0">*/</span>
  </div>
</div>

3. Eventuellement, modifier le fichier de configuration globale pour prendre en compte l'injection de dépendance de notre Bundle :

<div class="codecolorer-container yaml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="yaml codecolorer">
    <span class="co1"># app/config/config.yml</span><br /> <span class="co3"><br /> mybundle.config</span><span class="sy2">: </span>~
  </div>
</div>

4. Rédiger la classe "Helper". Celle-ci étend la classe abstraite *Symfony\Component\Templating\Helper\Helper* et doit donc implémenter la méthode *getName* :

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
     /* Application/MyBundleBundle/Helper/MyHelper.php */<br /> <br /> <span class="kw2"><?php</span><br /> <br /> <span class="kw2">namespace</span> Application\MyBundleBundle\Helper <span class="sy0">;</span><br /> <br /> <span class="kw2">use</span> Symfony\Component\Templating\Helper\Helper <span class="sy0">;</span><br /> <br /> <span class="kw2">class</span> MyHelper <span class="kw2">extends</span> Helper<br /> <span class="br0">&#123;</span><br />     <span class="kw2">public</span> <span class="kw2">function</span> doSomething<span class="br0">&#40;</span><span class="re0">$myString</span><span class="br0">&#41;</span><br />     <span class="br0">&#123;</span><br />          <span class="co1">// Do something with $myString</span><br />          <span class="kw1">return</span> <span class="re0">$myString</span> <span class="sy0">;</span> <br />     <span class="br0">&#125;</span><br /> <br />     <span class="kw2">public</span> <span class="kw2">function</span> getName<span class="br0">&#40;</span><span class="br0">&#41;</span><br />     <span class="br0">&#123;</span><br />         <span class="kw1">return</span> <span class="st_h">'MyHelper'</span> <span class="sy0">;</span><br />     <span class="br0">&#125;</span><br /> <span class="br0">&#125;</span>
  </div>
</div>

5. Utilisation dans les vues :

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="kw2"><?php</span> <span class="kw1">echo</span> <span class="re0">$view</span><span class="br0">&#91;</span><span class="st_h">'alias.de.mon.helper'</span><span class="br0">&#93;</span><span class="sy0">-></span><span class="me1">doSomething</span><span class="br0">&#40;</span><span class="re0">$myString</span><span class="br0">&#41;</span> <span class="sy1">?></span>
  </div>
</div>