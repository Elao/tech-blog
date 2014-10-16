
*"Error inserting dm_snapshot" *est une erreur rencontrée au moment d"effectuer un backup de VM (Container ou KVM). C'est une erreur que j'ai pu rencontrer sur une machine que j'ai "oublié" de redémarrer suite à une mise à jour du kernel.

Le problème dans ce cas de figure vient de la mise à jour d'un kernel portant le même nom que l'ancien (dans mon  cas un 2.6.32-16-pve), le nom des modules nécessaires à notre opération de backup étant, du coup, les mêmes mais compilés pour notre ancien kernel.

Si vous m'avez suivi jusque là vous aurez compris qu'un "simple" reboot de la machine est donc nécessaire pour que les modules correspondant à notre nouveau kernel soit correctement chargés.