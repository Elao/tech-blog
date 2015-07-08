# Gérer son contrôleur RAID LSI avec la commande MegaCli

Equipant de nombreux serveurs les contrôleurs RAID LSI se retrouvent chez de nombreux hébergeurs et notamment OVH avec qui nous avons l'habitude de travailler.

Les opérations suivantes n'étant pas celles les plus souvent effectuées il est toujours bon d'avoir un petit aide mémoire sur le sujet.

- [Généralités](#generalite)
    - [Afficher les informations concernant le RAID](#afficher-les-informations-concernant-le-raid)
    - [Compter le nombre de contrôleurs installés sur la machine](#compter-le-nombre-de-contr-leurs-install-s-sur-la-machine)
    - [Enclosure Device ID](#enclosure-device-id)
- [Opérations sur les disques physiques](#op-rations-sur-les-disques-physiques)
    - [Compter le nombre de disques physiques](#compter-le-nombre-de-disques-physiques)
    - [Lister les disques physiques](#lister-les-disques-physiques)
    - [Lister les informations utiles des disques](#lister-les-informations-utiles-des-disques)
    - [Afficher les informations d’un disque physique](#afficher-les-informations-d-un-disque-physique)
- [Opérations sur les disques virtuels](#op-rations-sur-les-disques-virtuels)
    - [Compter le nombre de disques virtuels (VD) du controleur](#compter-le-nombre-de-disques-virtuels-(VD)-du-controleur)
    - [Récupérer les target ID des volumes logiques](#r-cup-rer-les-target-id-des-volumes-logiques)
    - [Afficher les informations des disques virtuels](#afficher-les-informations-des-disques-virtuels)
    - [Lister les disques d'un volume logique](#lister-les-disques-d-un-volume-logique)
- [Opérations sur les volumes RAID](#op-rations-sur-les-volumes-raid)
    - [Détruire un volume logique](#d-truire-un-volume-logique)

## Généralités 

### Afficher les informations concernant le RAID

```
root@rescue:~# lspci | grep -i lsi | grep -v megaraid
05:00.0 Serial Attached SCSI controller: LSI Logic / Symbios Logic SAS2308 PCI-Express Fusion-MPT SAS-2 (rev 05)
06:00.0 RAID bus controller: LSI Logic / Symbios Logic MegaRAID SAS 2208 [Thunderbolt] (rev 05)
```

On constate qu'ici nous disposons d'un controleur MegaRAID LSI.

### Compter le nombre de contrôleurs installés sur la machine 

```bash
root@rescue:~# MegaCli -adpCount                           
Controller Count: 1.
```

### Enclosure Device ID 

Cette information est souvent abrégée par la directive E et correspond à l'identifiant du conteneur où est installé le disque. 

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
- Le second l'identifiant 252 avec 8 emplacements disponibles dont un seul est occupé.

# Opérations sur les disques physiques 

### Compter le nombre de disques physiques 

Pour afficher le nombre de disques physiques reliés à notre contrôleur utilisez la commande

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

### Lister les informations utiles des disques

```bash
MegaCli -PDList -aALL | egrep "Adapter|Drive's position|Enclosure position|Slot|Raw Size|Inquiry|Enclosure Device ID"
```
Cette commande devrait donner en sortie quelque chose ressemblant à ça :

```
Adapter #0

Enclosure Device ID: 8
Slot Number: 0
Drive's position: DiskGroup: 0, Span: 0, Arm: 0
Enclosure position: 1
Raw Size: 5.458 TB [0x2baa0f4b0 Sectors]
Inquiry Data: HGST    HUS726060ALS640 A2801EH7J0RB   

Enclosure Device ID: 8
Slot Number: 1
Drive's position: DiskGroup: 0, Span: 0, Arm: 1
Enclosure position: 1
Raw Size: 5.458 TB [0x2baa0f4b0 Sectors]
Inquiry Data: HGST    HUS726060ALS640 A2801EH9Y34B  

[...]

Enclosure Device ID: 8
Slot Number: 11
Drive's position: DiskGroup: 0, Span: 5, Arm: 1
Enclosure position: 1
Raw Size: 5.458 TB [0x2baa0f4b0 Sectors]
Inquiry Data: HGST    HUS726060ALS640 A2801EH9JX1B

Enclosure Device ID: 252
Slot Number: 4
Drive's position: DiskGroup: 1, Span: 0, Arm: 0
Enclosure position: N/A
Raw Size: 111.790 GB [0xdf94bb0 Sectors]
Inquiry Data: BTWL332507LC120LGN  INTEL SSDSC2BB120G4                     D2010355
```

### Afficher les informations d'un disque physique

Les disques physiques sont identifiés à l'aide de deux informations : 
    - le "[Enclosure Device ID](#enclosure-device-id)" 
    - et le numéro de l'emplacement (Slot) occupé par le disque.
Ainsi pour consulter les informations du disque de l'emplacement numéro 5 de notre premier conteneur (Ayant donc l'identifiant 8) nous exécuterons la commande suivante :

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

## Opérations sur les disques virtuels

### Compter le nombre de disques virtuels (VD) du controleur

Pour compter le nombre de VD nous utilisons la commande suivante :

```
root@rescue:~# MegaCli -LdGetNum -aAll
                                     
Number of Virtual Drives Configured on Adapter 0: 2
```

Il est possible de remplacer la directive "all" par l'identifiant du contrôleur cible.

### Récupérer les target ID des volumes logiques

```
root@rescue:~# MegaCli -ldpdinfo -a0 | grep Target
Virtual Drive: 0 (Target Id: 0)
CacheCade Virtual Drive: 1 (Target Id: 1)
Target Id of the Associated LDs : 0
```

### Afficher les informations des disques virtuels

Utile notamment pour consulter le type de RAID utilisé, l'espace disponible ou encore l'état du disque virtuel.

```
root@rescue:~# MegaCli -LDInfo -Lall -aALL
                                     
Adapter 0 -- Virtual Drive Information:
Virtual Drive: 0 (Target Id: 0)
Name                :
RAID Level          : Primary-1, Secondary-0, RAID Level Qualifier-0
Size                : 32.745 TB
Sector Size         : 512
Is VD emulated      : No
Mirror Data         : 32.745 TB
State               : Optimal
Strip Size          : 256 KB
Number Of Drives per span:2
Span Depth          : 6
Default Cache Policy: WriteBack, ReadAhead, Direct, No Write Cache if Bad BBU
Current Cache Policy: WriteBack, ReadAhead, Direct, No Write Cache if Bad BBU
Default Access Policy: Read/Write
Current Access Policy: Read/Write
Disk Cache Policy   : Disk's Default
Encryption Type     : None
Bad Blocks Exist: No
PI type: No PI

Is VD Cached: Yes
Cache Cade Type : Read Only


CacheCade Virtual Drive: 1 (Target Id: 1)
Virtual Drive Type  : CacheCade 
Name            : 
RAID Level      : Primary-0, Secondary-0
State               : Optimal
Size            : 111.281 GB
Target Id of the Associated LDs : 0
Default Cache Policy    : WriteThrough, ReadAhead, Direct, No Write Cache if Bad BBU
Current Cache Policy    : WriteThrough, ReadAhead, Direct, No Write Cache if Bad BBU
```
### Lister les disques d'un volume logique

La commande suivante permet de lister l'ensemble des disques constituants un volume logique, ATTENTION la sortie peut-être très verbeuse !

```
MegaCli -ldpdinfo -a0
```

## Opérations sur les volumes RAID

Les commandes listées dans cette section permettent la gestion des volumes RAID, elles sont à manipuler avec précaution car elles peuvent entraîner une perte irréversible de vos données.

### Détruire un volume logique

```
MegaCli -CfgLdDel -L1 -Force -a0
 
Adapter 0: Deleted Virtual Drive-1(target id-1)
```

L'option `-L1` permet de spécifier le "target ID" du volume logique ciblé par l'opération. Pour le déterminer voir la section "[Récupérer les target ID des volumes logiques](#r-cup-rer-les-target-id-des-volumes-logiques)"

### Gérer les "Hot Spare"

Pour certaines configurations (je pense notamment au RAID 5), nous aurons à gérer des disques particuliers appelés "[Hot Spare](http://en.wikipedia.org/wiki/Hot_spare)"

__Les commandes ci-dessous permettent leur gestion, à noter:__

    E: Enclosure ID
    S: Numéro de Slot du disque
    N: Identifiant du contrôleur

#### Configurer un Hot spare global

```
MegaCli -PDHSP -Set -PhysDrv [E:S] -aN
```

### Configurer un Hot Spare dédié

```
MegaCli -PDHSP -Set -Dedicated -ArrayN,M,... -PhysDrv [E:S] -aN
```

#### Supprimer un Hot Spare

```
MegaCli -PDHSP -Rmv -PhysDrv [E:S] -aN
```

### Créer un volume logique

Les instructions ci dessous exploitent les informations :

- Enclosure ID appelé E 
- Numéro de Slot S

#### Création d'un RAID 5

```
MegaCli -CfgLdAdd -r5[E:S,E:S,E:S] -Hsp[E:S] -Cache -a0

Adapter 0: Created VD 0
Successfully assigned VD to Cache  
Adapter: 0: Set Physical Drive at EnclId-8 SlotId-4 as Hot Spare Success.

Adapter 0: Configured the Adapter!!
```

Il est possible dans cette configuration de ne pas déclarer de disque de Spare, il suffit dans ce cas de ne pas exploiter l'option `Hsp`.

#### Création d'un RAID 10

```
MegaCli -CfgSpanAdd -r10 -Array0[E:S,E:S] -Array1[E:S,E:S] -aN
```
