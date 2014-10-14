Dans cet article je vais tent&eacute; d&#8217;expliquer comment fonctionne actuellement le SPF pour nos emails, mais &eacute;galement de d&eacute;tailler au maximum les diff&eacute;rentes options que fournit le framework afin de cr&eacute;er nos propres enregistrements. L&#8217;ensemble de ces options peuvent &ecirc;tre retrouv&eacute;es sur le site officiel (anglais): <a href="http://www.openspf.org/SPF_Record_Syntax" title="OpenSPF">OpenSPF.org</a>

Le SPF est une sp&eacute;cification publi&eacute;e le 28 Avril 2006, pour la RFC c&#8217;est <a href="http://www.openspf.org/RFC_4408" title="RFC_4408">ici</a>

Ce qui suit est une traduction du manuel officiel que l&#8217;on peut trouver sur le site ci-dessus: Les noms de domaines d&eacute;finissent 0 ou plusieurs m&eacute;canismes. Ces m&eacute;canismes peuvent &ecirc;tre utilis&eacute;s pour d&eacute;crire le param&eacute;trage des h&ocirc;tes d&eacute;sign&eacute;s comme serveurs de mail sortant, c&#8217;est &agrave; dire autoris&eacute;s &agrave; envoyer des emails pour le domaine concern&eacute;.


<tt><a href="http://www.openspf.org/SPF_Record_Syntax#all" class="link-wiki link-intrapage">all</a></tt> | <tt><a href="http://www.openspf.org/SPF_Record_Syntax#ip4" class="link-wiki link-intrapage">ip4</a></tt> | <tt><a href="http://www.openspf.org/SPF_Record_Syntax#ip6" class="link-wiki link-intrapage">ip6</a></tt> | <tt><a href="http://www.openspf.org/SPF_Record_Syntax#a" class="link-wiki link-intrapage">a</a></tt> | <tt><a href="http://www.openspf.org/SPF_Record_Syntax#mx" class="link-wiki link-intrapage">mx</a></tt> | <tt><a href="http://www.openspf.org/SPF_Record_Syntax#ptr" class="link-wiki link-intrapage">ptr</a></tt> | <tt><a href="http://www.openspf.org/SPF_Record_Syntax#exists" class="link-wiki link-intrapage">exists</a></tt> | <tt><a href="http://www.openspf.org/SPF_Record_Syntax#include" class="link-wiki link-intrapage">include</a></tt>

  
Chaque domaine peut &eacute;galement d&eacute;finir des &#8220;modificateurs&#8221;. Chacun d&#8217;entre eux ne peut apparaitre <strong>qu&#8217;une seule fois</strong> !
  
<tt><a href="http://www.openspf.org/SPF_Record_Syntax#redirect" class="link-wiki link-intrapage">redirect</a></tt> | <tt><a href="http://www.openspf.org/SPF_Record_Syntax#exp" class="link-wiki link-intrapage">exp</a></tt>

## Les m&eacute;canismes

#### Les m&eacute;canismes peuvent &ecirc;tre pr&eacute;fixer par l&#8217;un de ces quatres &#8220;qualificateurs&#8221;:
  
> &#8220;+&#8221; Pass (A pass&eacute; la v&eacute;rification)

> &#8220;-&#8221; Fail (Echec)

> &#8220;~&#8221; SoftFail (Echec non fatal)

> &#8220;?&#8221; Neutral (Neutre)

* Si un m&eacute;canisme abouti, la valeur de son qualificateur est utilis&eacute;e. Le qualificateur par d&eacute;faut est &#8220;+&#8221; (Pass).

```
Par exemple: "v=spf1 -all" "v=spf1 a -all" "v=spf1 a mx -all" "v=spf1 +a +mx -all"
```

Les m&eacute;canismes sont &eacute;valu&eacute;s dans l&#8217;ordre.


* Si aucun m&eacute;canisme ou modificateur ne correspond, la r&eacute;ponse par d&eacute;faut est &#8220;Neutral&#8221; (Neutre). <br /> Si un domaine n&#8217;a pas d&#8217;enregistrement SPF, le r&eacute;sultat est &#8220;None&#8221; (Aucun).
 
* Si un domaine renvoi une erreur temporaire pendant la lecture DNS, vous aurez la r&eacute;ponse &#8220;TempError&#8221; (Erreur temporaire), elle est aussi appell&eacute;e &#8220;error&#8221; dans les anciennes documentations. 
* Si une erreur de syntaxe ou d&#8217;&eacute;valuation apparait (par exemple le domaine renvoi un m&eacute;canisme non reconnu) le r&eacute;sultat est &#8220;PermError&#8221; (Erreur permanente, autrefois appell&eacute;e &#8220;Unknow&#8221; (inconnue)).
  
####    L&#8217;&eacute;valuation d&#8217;un enregistrement SPF peut retourner l&#8217;un de ces r&eacute;sultats:

  <div class="wookee-box-gray">
    <table width="505" height="239" border="1" align="center" style="">
      <tr valign="middle" align="center">
        <td style="text-align: justify;">
          <strong>R&eacute;sultat</strong>
        </td>
        
        <td style="text-align: justify;">
          <strong>Explication</strong>
        </td>
        
        <td style="text-align: justify;">
          <strong>Action r&eacute;sultante</strong>
        </td>
      </tr>
      
      <tr valign="middle" align="center">
        <td style="text-align: justify;">
          Pass
        </td>
        
        <td style="text-align: justify;">
          L&#8217;enregistrement SPF a d&eacute;sign&eacute; l&#8217;h&ocirc;te comme <strong>autoris&eacute;</strong> &agrave; envoyer
        </td>
        
        <td style="text-align: justify;">
          Accept&eacute;
        </td>
      </tr>
      
      <tr valign="middle" align="center">
        <td style="text-align: justify;">
          Fail
        </td>
        
        <td style="text-align: justify;">
          L&#8217;enregistrement SPF a d&eacute;sign&eacute; l&#8217;h&ocirc;te comme <strong>non autoris&eacute;</strong> &agrave; envoyer
        </td>
        
        <td style="text-align: justify;">
          Rejet&eacute;
        </td>
      </tr>
      
      <tr valign="middle" align="center">
        <td style="text-align: justify;">
          SoftFail
        </td>
        
        <td style="text-align: justify;">
          L&#8217;enregistrement SPF a d&eacute;sign&eacute; l&#8217;h&ocirc;te comme <strong>non autoris&eacute;</strong> &agrave; envoyer mais est en cours de transition
        </td>
        
        <td style="text-align: justify;">
          Accept&eacute; mais relev&eacute;
        </td>
      </tr>
      
      <tr valign="middle" align="center">
        <td style="text-align: justify;">
          Neutral
        </td>
        
        <td style="text-align: justify;">
          L&#8217;enregistrement SPF a explicitement renvoy&eacute; que rien ne pouvait &ecirc;tre dit &agrave; propos de la validit&eacute; de la requ&ecirc;te
        </td>
        
        <td style="text-align: justify;">
          Accept&eacute;
        </td>
      </tr>
      
      <tr valign="middle" align="center">
        <td style="text-align: justify;">
          None
        </td>
        
        <td style="text-align: justify;">
          Le domaine n&#8217;a pas d&#8217;enregistrement SPF ou alors l&#8217;enregistrement SPF n&#8217;&eacute;value pas un r&eacute;sultat
        </td>
        
        <td style="text-align: justify;">
          Accept&eacute;
        </td>
      </tr>
      
      <tr valign="middle" align="center">
        <td style="text-align: justify;">
          PermError
        </td>
        
        <td style="text-align: justify;">
          Une erreur permanente a &eacute;t&eacute; renvoy&eacute;e (badly formatted SPF record). (Enregistrement SPF mal format&eacute;)
        </td>
        
        <td style="text-align: justify;">
          Non sp&eacute;cifi&eacute;
        </td>
      </tr>
      
      <tr valign="middle" align="center">
        <td style="text-align: justify;">
          TempError
        </td>
        
        <td style="text-align: justify;">
          Une erreur passag&egrave;re (temporaire) est arriv&eacute;e
        </td>
        
        <td style="text-align: justify;">
          Accept&eacute; ou rejet&eacute;
        </td>
      </tr>
    </table>
  </div>
  
### Le m&eacute;canisme &#8220;all&#8221;

Ce m&eacute;canisme correspond toujours, on le retrouve habituellement &agrave; la fin de l&#8217;enregistrement.
  
<strong>Exemples:</strong>

```
"v=spf1 mx -all"
```
<i>Autorise les noms de domaine de type MX &agrave; envoyer des mail pour le domaine, interdit tous les autres.</i>

```
"v=spf1 -all"
```

<i>Le domaine n&#8217;envoie pas du tout d&#8217;email.</i>

```
"v=spf1 +all"
```

<i>Le propri&eacute;taire du domaine consid&egrave;re que le SPF est inutile ou alors ne s&#8217;en pr&eacute;occupe pas.</i>

### Le m&eacute;canisme &#8220;ip4&#8243;

```
ip4:
ip4:/
```

L&#8217;argument correspondant au m&eacute;canisme &#8220;ip4&#8243; est une d&#8217;adresse de r&eacute;seau IPv4. S&#8217;il n&#8217;y a pas de &#8220;prefix-length&#8221; fourni, /32 est consid&eacute;r&eacute; par d&eacute;faut.
  
<strong>Exemple:</strong>:
 
```
"v=spf1 ip4:192.168.0.1/16 -all"
```
<i>Autorise toutes les adresses IP comprisent entre 192.168.0.1 et 192.168.255.255.</i>

### Le m&eacute;canisme &#8220;ip6&#8243;

```
ip6:
ip6:/
```

L&#8217;argument correspondant au m&eacute;canisme &#8220;ip6&#8243; est une d&#8217;adresse de r&eacute;seau IPv6. S&#8217;il n&#8217;y a pas de &#8220;prefix-length&#8221; fourni, /128 est consid&eacute;r&eacute; par d&eacute;faut.

<strong>Exemple:</strong>

```
"v=spf1 ip6:1080::8:800:200C:417A/96 -all"
```

<i>Autorise toutes les adresse IPv6 comprise entre 1080::8:800:0000:0000 et 1080::8:800:FFFF:FFFF.</i>

```
"v=spf1 ip6:1080::8:800:68.0.3.1/96 -all"
```
<i>Autorise toutes les adresse IPv6 comprise entre 1080::8:800:0000:0000 et 1080::8:800:FFFF:FFFF.</i>

### Le m&eacute;canisme &#8220;a&#8221;

```
a
a/
a:
a:/
```

Tous les enregistrement de type A pour le domaine sont test&eacute;s. Si l&#8217;IP du client est trouv&eacute;e parmis elles, le m&eacute;canisme correspond. Si le domaine n&#8217;est pas sp&eacute;cifi&eacute;, le domaine courant est utilis&eacute;. L&#8217;enregistrement A doit correspondre exactement &agrave; l&#8217;adresse IP du client, sans quoi le <em class="em1">prefix-length</em> est fourni, auquel cas chaque IP retourn&eacute;e par la recherche A sera &eacute;tendue &agrave; son pr&eacute;fix <a href="http://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing" title="CIDR">CIDR</a> correspondant, et l&#8217;IP du client sera demand&eacute;e &agrave; l&#8217;int&eacute;rieur de ce sous-r&eacute;seau.

<strong>Exemple:</strong>
```
"v=spf1 a -all"
```
Le domaine courant est utilis&eacute;.
```
"v=spf1 a:example.com -all"
```
Equivalent si le domaine courant est example.com.
```
"v=spf1 a:mailers.example.com -all"
```

Peut-&ecirc;tre que example.com a choisi de lister explicitement tous les serveurs de courrier sortant dans un enregistrement A special sous mailers.example.com.

```
"v=spf1 a/24 a:offsite.example.com/24 -all"
```

Si example.com est r&eacute;solu vers 192.0.2.1, la classe C enti&egrave;re de 192.0.2.0/24 sera recherch&eacute;e pour l&#8217;IP du client. De m&ecirc;me pour offsite.example.com. Si plus d&#8217;un enregistrement de type A a &eacute;t&eacute; retourn&eacute;, chacun d&#8217;entre eux sera &eacute;tendu au sous r&eacute;seau <a href="http://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing" title="CIDR">CIDR</a>.

### Le m&eacute;canisme &#8220;mx&#8221;

```
mx
mx/
mx:
mx:/
```

L&#8217;ensemble des enregistrements A pour tous les enregistrements MX pour le domaine sont test&eacute;s dans l&#8217;ordre de leur priorit&eacute;. Si l&#8217;IP du client est trouv&eacute;e parmis eux, ce m&eacute;canisme correspond. Si le domaine n&#8217;est pas sp&eacute;cifi&eacute; alors le domaine courant est utilis&eacute;. L&#8217;enregistrement A doit correspondre exactement &agrave; l&#8217;adresse IP cliente, sans quoi le <em class="em1">prefix-length</em> est fourni, auquel cas chaque IP retourn&eacute;e par la recherche A sera &eacute;tendue &agrave; son pr&eacute;fix <a href="http://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing" title="CIDR">CIDR</a> correspondant, et l&#8217;IP du client sera demand&eacute;e &agrave; l&#8217;int&eacute;rieur de ce sous-r&eacute;seau. 

<strong>Exemples:</strong>

```
"v=spf1 mx mx:deferrals.domain.com -all"
```

Peut &ecirc;tre qu&#8217;un domaine a envoy&eacute; un mail via ce serveur MX ainsi qu&#8217;avec un autre ensemble de serveur dont le r&ocirc;le est de retenter l&#8217;envoi pour les domaines dont la distribution a &eacute;t&eacute; diff&eacute;r&eacute;e.

```
"v=spf1 mx/24 mx:offsite.domain.com/24 -all"
```

Peut &ecirc;tre qu&#8217;un serveur MX de domaine &agrave; re&ccedil;u un mail sur une adresse IP, mais a envoy&eacute; le mail sur une adresse IP diff&eacute;rente mais proche.