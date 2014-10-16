
*&#8220;Error inserting dm_snapshot&#8221; *est une erreur rencontrée au moment d&#8217;effectuer un backup de VM (Container ou KVM). C&#8217;est une erreur que j&#8217;ai pu avoir sur une machine que j&#8217;ai &#8220;oublié&#8221; de redémarrer suite à une mise à jour du kernel.

Le problème dans ce cas de figure vient de la mise à jour d&#8217;un kernel portant le même nom que l&#8217;ancien (dans mon  cas un 2.6.32-16-pve), le nom des modules nécessaires à notre opération de backup étant, du coup, les mêmes mais compilés pour notre ancien kernel.

Si vous m&#8217;avez suivi jusque là vous aurez compris qu&#8217;un &#8220;simple&#8221; reboot de la machine est donc nécessaire pour que les modules correspondant à notre nouveau kernel soit correctement chargés.