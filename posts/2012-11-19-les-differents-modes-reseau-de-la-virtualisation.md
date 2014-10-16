
Bonjour à tous,

<p style="text-align: justify;">
  Rapide mémo aujourd&#8217;hui concernant la virtualisation ou plutôt la configuration du réseau lorsque l&#8217;on virtualise des systèmes.<br /> La plupart des problématiques directement liées à la gestion matérielle étant gérées par l’hyperviseur ou plus généralement par la solution de virtualisation choisie, la seule chose restant à choisir et configurer, est la technique qu’utilisera la machine virtuelle pour accéder au réseau, c’est en général le premier problème que l’on rencontre lorsque l’on fait ses débuts avec la virtualisation.
</p>

<p style="text-align: justify;">
  <span style="text-decoration: underline;"><strong>Le mode Bridge:</strong></span>
</p>

<p style="text-align: justify;">
  Le principe du “bridge” est de faire apparaitre, la ou les machines virtuelles, comme des machines physique sur votre réseau, pour simplifier cela signifie que la machine virtuelle a accès au LAN et se comporte comme n’importe quelle machine physique. Elle peut ainsi disposer de sa propre adresse IP statique ou dynamique, répondre au ping, partager des fichiers ou plus généralement exposer des services …
</p>

 <img class="aligncenter size-full wp-image-2536" title="Machines virtuelles en mode bridge" src="http://www.elao.com/blog/wp-content/uploads/2012/11/vm-bridge.png" alt="vm bridge Les différents modes réseau de la virtualisation" width="347" height="520" />

<p style="text-align: justify;">
  En d’autre terme la machine virtuelle est autorisée à communiquer avec le réseau physique.<br /> Attention toutefois, si l’affectation d’une adresse IP ne pose généralement pas de problème, cela implique par contre de créer une <strong>adresse MAC virtuelle</strong>.
</p>

<p style="text-align: justify;">
  C’est la configuration la plus généralement utilisée.
</p>

<span style="text-decoration: underline;"><strong>Le mode NAT:</strong></span>

<p style="text-align: justify;">
  Comme pour le mode Bridge, la machine virtuelle dispose d’un accès complet au réseau. Elle n’est, pour autant, pas visible par les autres et donc n’est pas directement accessible.
</p>

<p style="text-align: justify;">
  <img class="aligncenter size-full wp-image-2522" title="Machines virtuelles en mode NAT" src="http://www.elao.com/blog/wp-content/uploads/2012/11/vm-nat.png" alt="vm nat Les différents modes réseau de la virtualisation" width="426" height="710" />
</p>

<p style="text-align: justify;">
  Pour rendre accessible des services dans ce mode, il faut effectuer un routage NAT au niveau de la machine hôte, ce mode est particulièrement intéressant lorsque les machines virtuelles doivent être protégées à travers un point d’entrée unique (la machine hôte).<br /> De plus, avec ce mode réseau les adresses IP des machines virtuelles ne sont jamais exposées, d&#8217;un point de vue externe, peu importe la machine virtuelle ayant envoyé un paquet réseau, c&#8217;est l&#8217;adresse IP de la machine hôte qui sera exposée.
</p>

<span style="text-decoration: underline;"><strong>Le mode Host only:</strong></span>

<img class="aligncenter size-full wp-image-2526" title="Machines virtuelles en mode Host Only" src="http://www.elao.com/blog/wp-content/uploads/2012/11/vm-host-only.png" alt="vm host only Les différents modes réseau de la virtualisation" width="496" height="712" />

<p style="text-align: justify;">
  Dans ce cas de figure la ou les machines virtuelles ne sont capables de communiquer qu’avec la machine hôte, à travers un réseau virtuel, il est impossible pour elles dans sortir et d’aller dialoguer avec une machine distante, tout comme il est impossible pour une machine du réseau d’accéder à la machine virtuelle.
</p>

<p style="text-align: justify;">
  A noter que ce mode &#8220;fournit&#8221; son propre DHCP.
</p>