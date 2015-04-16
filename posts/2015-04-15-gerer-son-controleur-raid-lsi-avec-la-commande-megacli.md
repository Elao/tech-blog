# Gérer son contrôleur RAID LSI avec la commande MegaCli

Equipant de nombreux serveurs les contrôleurs RAID LSI se retrouvent chez de nombreux hébergeurs et notamment OVH avec qui nous avons l'habitude de travailler.

Les opérations suivantes n'étant pas celles les plus souvent effectuées il est toujours bon d'avoir un petit aide mémoire sur le sujet.

- [Généralités](#generalite)
    - [Compter le nombre de contrôleurs installés sur la machine](#compter-le-nombre-de-contr-leurs-install-s-sur-la-machine)
    - [Enclosure Device ID](#enclosure-device-id)
- [Opérations sur les disques physiques](#op-rations-sur-les-disques-physiques)
    - [Compter le nombre de disques physiques](#compter-le-nombre-de-disques-physiques)
    - [Lister les disques physiques](#lister-les-disques-physiques)
    - [Afficher les informations d’un disque physique](#afficher-les-informations-d-un-disque-physique)

## Généralités 

### Compter le nombre de contrôleurs installés sur la machine 

```bash
root@rescue:~# MegaCli -adpCount                           
Controller Count: 1.
```

### Enclosure Device ID 

Cette information est souvent abrégée par la directive E et correspond à l'identifiant du containeur ou est installé le disque. 

Elle est consultable via la commande `MegaCli -EncInfo -a{numéro_du_contrôleur}` (Il est possible de spécifier l'option -aALL pour interroger l'ensemble des contrôleurs de la machine).
    
```bash
root@rescue:~# MegaCli -EncInfo -a0
                                     
    Number of enclosures on adapter 0 -- 2

    Enclosure 0:
    Device ID                     : 8
    Number of Slots               : 12
    Number of Power Supplies      : 2
    Number of Fans                : 3
    Number of Temperature Sensors : 1
    Number of Alarms              : 0
    Number of SIM Modules         : 0
    Number of Physical Drives     : 12
    Status                        : Normal
    Position                      : 1
    Connector Name                : Port 0 - 3
    Enclosure type                : SES
    FRU Part Number               : N/A
    Enclosure Serial Number       : N/A 
    ESM Serial Number             : N/A 
    Enclosure Zoning Mode         : N/A 
    Partner Device Id             : 65535
   
    Enclosure 1:
    Device ID                     : 252
    Number of Slots               : 8
    Number of Power Supplies      : 0
    Number of Fans                : 0
    Number of Temperature Sensors : 0
    Number of Alarms              : 0
    Number of SIM Modules         : 1
    Number of Physical Drives     : 1
    Status                        : Normal
    Position                      : 1
    Connector Name                : Unavailable
    Enclosure type                : SGPIO
    FRU Part Number               : N/A
    Enclosure Serial Number       : N/A 
    ESM Serial Number             : N/A 
    Enclosure Zoning Mode         : N/A 
    Partner Device Id             : Unavailable

    Inquiry data                  :
        Vendor Identification     : LSI     
        Product Identification    : SGPIO           
        Product Revision Level    : N/A 
        Vendor Specific           : 
```

__Dans notre cas nous disposons donc de 2 conteneurs:__

- Le premier ayant l'identifiant 8 avec 12 emplacements remplis
- Le second l'identifiant 252 avec 8 emplacement dont un seul est occupé.

# Opérations sur les disques physiques 

### Compter le nombre de disques physiques 

Pour afficher le nombre de disques physiques reliés à notre contrôleur utiliser la commande

```bash
root@rescue:~# MegaCli -PDGetNum -a0                          
Number of Physical Drives on Adapter 0: 13
```

### Lister les disques physiques

Pour lister les disques physiques disponibles sur un contrôleur (#0 pour l'exemple)

```bash
root@rescue:~# MegaCli -pdlist -a0
                                     
Adapter #0

Enclosure Device ID: 8
Slot Number: 0
Drive's position: DiskGroup: 0, Span: 0, Arm: 0
Enclosure position: 1
Device Id: 16
WWN: 5000CCA232460903
Sequence Number: 2
Media Error Count: 0
Other Error Count: 0
Predictive Failure Count: 0
Last Predictive Failure Event Seq Number: 0
PD Type: SAS

[...]

Raw Size: 111.790 GB [0xdf94bb0 Sectors]
Non Coerced Size: 111.290 GB [0xde94bb0 Sectors]
Coerced Size: 111.281 GB [0xde90000 Sectors]
Sector Size:  512
Logical Sector Size:  512
Physical Sector Size:  512
Firmware state: Online, Spun Up
Commissioned Spare : No
Emergency Spare : No
Device Firmware Level: 0355
Shield Counter: 0
Successful diagnostics completion on :  N/A
SAS Address(0): 0x4433221107000000
Connected Port Number: 1(path0) 
Inquiry Data: BTWL332507LC120LGN  INTEL SSDSC2BB120G4                     D2010355
FDE Capable: Not Capable
FDE Enable: Disable
Secured: Unsecured
Locked: Unlocked
Needs EKM Attention: No
Foreign State: None 
Device Speed: 6.0Gb/s 
Link Speed: 6.0Gb/s 
Media Type: Solid State Device
Drive:  Not Certified
Drive Temperature :38C (100.40 F)
PI Eligibility:  No 
Drive is formatted for PI information:  No
PI: No PI
Port-0 :
Port status: Active
Port's Linkspeed: 6.0Gb/s 
Drive has flagged a S.M.A.R.T alert : No
```

### Afficher les informations d'un disque physique

Les disques physiques sont identifiés à l'aide de deux informations le "[Inclosure Device ID](#enclosure-device-id)" et le numéro d'emplacement (Slot) occupé par le disque.
Ainsi pour consulter les informations du disque de l'emplacement numéro 5 de notre premier conteneur (Ayant donc l'identifiant 8) nous exécuterons la commande suivante:

```bash
root@rescue:~# MegaCli -pdinfo -PhysDrv[8:5] -a0
                                     
Enclosure Device ID: 8
Slot Number: 5
Drive's position: DiskGroup: 0, Span: 2, Arm: 1
Enclosure position: 1
Device Id: 18
WWN: 5000CCA2324BD83F
Sequence Number: 2
Media Error Count: 0
Other Error Count: 0
Predictive Failure Count: 0
Last Predictive Failure Event Seq Number: 0
PD Type: SAS

Raw Size: 5.458 TB [0x2baa0f4b0 Sectors]
Non Coerced Size: 5.457 TB [0x2ba90f4b0 Sectors]
Coerced Size: 5.457 TB [0x2ba900000 Sectors]
Sector Size:  512
Logical Sector Size:  512
Physical Sector Size:  512
Firmware state: Online, Spun Up
Commissioned Spare : No
Emergency Spare : No
Device Firmware Level: A280
Shield Counter: 0
Successful diagnostics completion on :  N/A
SAS Address(0): 0x5000cca2324bd83d
SAS Address(1): 0x0
Connected Port Number: 0(path0) 
Inquiry Data: HGST    HUS726060ALS640 A2801EHAR24C            
FDE Capable: Not Capable
FDE Enable: Disable
Secured: Unsecured
Locked: Unlocked
Needs EKM Attention: No
Foreign State: None 
Device Speed: 6.0Gb/s 
Link Speed: 6.0Gb/s 
Media Type: Hard Disk Device
Drive:  Not Certified
Drive Temperature :39C (102.20 F)
PI Eligibility:  No 
Drive is formatted for PI information:  No
PI: No PI
Port-0 :
Port status: Active
Port's Linkspeed: 6.0Gb/s 
Port-1 :
Port status: Active
Port's Linkspeed: 6.0Gb/s 
Drive has flagged a S.M.A.R.T alert : No
```