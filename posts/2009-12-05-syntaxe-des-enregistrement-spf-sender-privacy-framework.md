Dans cet article je vais tenté d'expliquer comment fonctionne actuellement le SPF pour nos emails, mais également de détailler au maximum les différentes options que fournit le framework afin de créer nos propres enregistrements. L'ensemble de ces options peuvent être retrouvées sur le site officiel (anglais): <a href="http://www.openspf.org/SPF_Record_Syntax" title="OpenSPF">OpenSPF.org</a>

Le SPF est une spécification publiée le 28 Avril 2006, pour la RFC c'est <a href="http://www.openspf.org/RFC_4408" title="RFC_4408">ici</a>

Ce qui suit est une traduction du manuel officiel que l'on peut trouver sur le site ci-dessus: Les noms de domaines définissent 0 ou plusieurs mécanismes. Ces mécanismes peuvent être utilisés pour décrire le paramétrage des hôtes désignés comme serveurs de mail sortant, c'est à dire autorisés à envoyer des emails pour le domaine concerné.


<tt><a href="http://www.openspf.org/SPF_Record_Syntax#all" class="link-wiki link-intrapage">all</a></tt> | <tt><a href="http://www.openspf.org/SPF_Record_Syntax#ip4" class="link-wiki link-intrapage">ip4</a></tt> | <tt><a href="http://www.openspf.org/SPF_Record_Syntax#ip6" class="link-wiki link-intrapage">ip6</a></tt> | <tt><a href="http://www.openspf.org/SPF_Record_Syntax#a" class="link-wiki link-intrapage">a</a></tt> | <tt><a href="http://www.openspf.org/SPF_Record_Syntax#mx" class="link-wiki link-intrapage">mx</a></tt> | <tt><a href="http://www.openspf.org/SPF_Record_Syntax#ptr" class="link-wiki link-intrapage">ptr</a></tt> | <tt><a href="http://www.openspf.org/SPF_Record_Syntax#exists" class="link-wiki link-intrapage">exists</a></tt> | <tt><a href="http://www.openspf.org/SPF_Record_Syntax#include" class="link-wiki link-intrapage">include</a></tt>


Chaque domaine peut également définir des "modificateurs". Chacun d'entre eux ne peut apparaitre <strong>qu'une seule fois</strong> !

<tt><a href="http://www.openspf.org/SPF_Record_Syntax#redirect" class="link-wiki link-intrapage">redirect</a></tt> | <tt><a href="http://www.openspf.org/SPF_Record_Syntax#exp" class="link-wiki link-intrapage">exp</a></tt>

## Les mécanismes

#### Les mécanismes peuvent être préfixer par l'un de ces quatres "qualificateurs":

> "+" Pass (A passé la vérification)

> "-" Fail (Echec)

> "~" SoftFail (Echec non fatal)

> "?" Neutral (Neutre)

* Si un mécanisme abouti, la valeur de son qualificateur est utilisée. Le qualificateur par défaut est "+" (Pass).

```
Par exemple: "v=spf1 -all" "v=spf1 a -all" "v=spf1 a mx -all" "v=spf1 +a +mx -all"
```

Les mécanismes sont évalués dans l'ordre.


* Si aucun mécanisme ou modificateur ne correspond, la réponse par défaut est "Neutral" (Neutre). <br /> Si un domaine n'a pas d'enregistrement SPF, le résultat est "None" (Aucun).

* Si un domaine renvoi une erreur temporaire pendant la lecture DNS, vous aurez la réponse "TempError" (Erreur temporaire), elle est aussi appellée "error" dans les anciennes documentations.
* Si une erreur de syntaxe ou d'évaluation apparait (par exemple le domaine renvoi un mécanisme non reconnu) le résultat est "PermError" (Erreur permanente, autrefois appellée "Unknow" (inconnue)).

####    L'évaluation d'un enregistrement SPF peut retourner l'un de ces résultats:

  <div class="wookee-box-gray">
    <table width="505" height="239" border="1" align="center" style="">
      <tr valign="middle" align="center">
        <td style="text-align: justify;">
          <strong>Résultat</strong>
        </td>

        <td style="text-align: justify;">
          <strong>Explication</strong>
        </td>

        <td style="text-align: justify;">
          <strong>Action résultante</strong>
        </td>
      </tr>

      <tr valign="middle" align="center">
        <td style="text-align: justify;">
          Pass
        </td>

        <td style="text-align: justify;">
          L'enregistrement SPF a désigné l'hôte comme <strong>autorisé</strong> à envoyer
        </td>

        <td style="text-align: justify;">
          Accepté
        </td>
      </tr>

      <tr valign="middle" align="center">
        <td style="text-align: justify;">
          Fail
        </td>

        <td style="text-align: justify;">
          L'enregistrement SPF a désigné l'hôte comme <strong>non autorisé</strong> à envoyer
        </td>

        <td style="text-align: justify;">
          Rejeté
        </td>
      </tr>

      <tr valign="middle" align="center">
        <td style="text-align: justify;">
          SoftFail
        </td>

        <td style="text-align: justify;">
          L'enregistrement SPF a désigné l'hôte comme <strong>non autorisé</strong> à envoyer mais est en cours de transition
        </td>

        <td style="text-align: justify;">
          Accepté mais relevé
        </td>
      </tr>

      <tr valign="middle" align="center">
        <td style="text-align: justify;">
          Neutral
        </td>

        <td style="text-align: justify;">
          L'enregistrement SPF a explicitement renvoyé que rien ne pouvait être dit à propos de la validité de la requête
        </td>

        <td style="text-align: justify;">
          Accepté
        </td>
      </tr>

      <tr valign="middle" align="center">
        <td style="text-align: justify;">
          None
        </td>

        <td style="text-align: justify;">
          Le domaine n'a pas d'enregistrement SPF ou alors l'enregistrement SPF n'évalue pas un résultat
        </td>

        <td style="text-align: justify;">
          Accepté
        </td>
      </tr>

      <tr valign="middle" align="center">
        <td style="text-align: justify;">
          PermError
        </td>

        <td style="text-align: justify;">
          Une erreur permanente a été renvoyée (badly formatted SPF record). (Enregistrement SPF mal formaté)
        </td>

        <td style="text-align: justify;">
          Non spécifié
        </td>
      </tr>

      <tr valign="middle" align="center">
        <td style="text-align: justify;">
          TempError
        </td>

        <td style="text-align: justify;">
          Une erreur passag&egrave;re (temporaire) est arrivée
        </td>

        <td style="text-align: justify;">
          Accepté ou rejeté
        </td>
      </tr>
    </table>
  </div>

### Le mécanisme "all"

Ce mécanisme correspond toujours, on le retrouve habituellement à la fin de l'enregistrement.

<strong>Exemples:</strong>

```
"v=spf1 mx -all"
```
<i>Autorise les noms de domaine de type MX à envoyer des mail pour le domaine, interdit tous les autres.</i>

```
"v=spf1 -all"
```

<i>Le domaine n'envoie pas du tout d'email.</i>

```
"v=spf1 +all"
```

<i>Le propriétaire du domaine consid&egrave;re que le SPF est inutile ou alors ne s'en préoccupe pas.</i>

### Le mécanisme "ip4&#8243;

```
ip4:
ip4:/
```

L'argument correspondant au mécanisme "ip4&#8243; est une d'adresse de réseau IPv4. S'il n'y a pas de "prefix-length" fourni, /32 est considéré par défaut.

<strong>Exemple:</strong>:

```
"v=spf1 ip4:192.168.0.1/16 -all"
```
<i>Autorise toutes les adresses IP comprisent entre 192.168.0.1 et 192.168.255.255.</i>

### Le mécanisme "ip6&#8243;

```
ip6:
ip6:/
```

L'argument correspondant au mécanisme "ip6&#8243; est une d'adresse de réseau IPv6. S'il n'y a pas de "prefix-length" fourni, /128 est considéré par défaut.

<strong>Exemple:</strong>

```
"v=spf1 ip6:1080::8:800:200C:417A/96 -all"
```

<i>Autorise toutes les adresse IPv6 comprise entre 1080::8:800:0000:0000 et 1080::8:800:FFFF:FFFF.</i>

```
"v=spf1 ip6:1080::8:800:68.0.3.1/96 -all"
```
<i>Autorise toutes les adresse IPv6 comprise entre 1080::8:800:0000:0000 et 1080::8:800:FFFF:FFFF.</i>

### Le mécanisme "a"

```
a
a/
a:
a:/
```

Tous les enregistrement de type A pour le domaine sont testés. Si l'IP du client est trouvée parmis elles, le mécanisme correspond. Si le domaine n'est pas spécifié, le domaine courant est utilisé. L'enregistrement A doit correspondre exactement à l'adresse IP du client, sans quoi le <em class="em1">prefix-length</em> est fourni, auquel cas chaque IP retournée par la recherche A sera étendue à son préfix <a href="http://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing" title="CIDR">CIDR</a> correspondant, et l'IP du client sera demandée à l'intérieur de ce sous-réseau.

<strong>Exemple:</strong>
```
"v=spf1 a -all"
```
Le domaine courant est utilisé.
```
"v=spf1 a:example.com -all"
```
Equivalent si le domaine courant est example.com.
```
"v=spf1 a:mailers.example.com -all"
```

Peut-être que example.com a choisi de lister explicitement tous les serveurs de courrier sortant dans un enregistrement A special sous mailers.example.com.

```
"v=spf1 a/24 a:offsite.example.com/24 -all"
```

Si example.com est résolu vers 192.0.2.1, la classe C enti&egrave;re de 192.0.2.0/24 sera recherchée pour l'IP du client. De même pour offsite.example.com. Si plus d'un enregistrement de type A a été retourné, chacun d'entre eux sera étendu au sous réseau <a href="http://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing" title="CIDR">CIDR</a>.

### Le mécanisme "mx"

```
mx
mx/
mx:
mx:/
```

L'ensemble des enregistrements A pour tous les enregistrements MX pour le domaine sont testés dans l'ordre de leur priorité. Si l'IP du client est trouvée parmis eux, ce mécanisme correspond. Si le domaine n'est pas spécifié alors le domaine courant est utilisé. L'enregistrement A doit correspondre exactement à l'adresse IP cliente, sans quoi le <em class="em1">prefix-length</em> est fourni, auquel cas chaque IP retournée par la recherche A sera étendue à son préfix <a href="http://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing" title="CIDR">CIDR</a> correspondant, et l'IP du client sera demandée à l'intérieur de ce sous-réseau.

<strong>Exemples:</strong>

```
"v=spf1 mx mx:deferrals.domain.com -all"
```

Peut être qu'un domaine a envoyé un mail via ce serveur MX ainsi qu'avec un autre ensemble de serveur dont le rôle est de retenter l'envoi pour les domaines dont la distribution a été différée.

```
"v=spf1 mx/24 mx:offsite.domain.com/24 -all"
```

Peut être qu'un serveur MX de domaine à re&ccedil;u un mail sur une adresse IP, mais a envoyé le mail sur une adresse IP différente mais proche.