+++
title = "Formater une clé USB pour Linux et Windows"
description = "Comment préparer une clé USB sur Linux pour être utilisée à la fois sur Linux et Windows (et probablement sur macOS aussi)"

[taxonomies]
tags = ["linux"]
+++

Il peut être utile de pouvoir partager des choses entre deux ordinateurs en utilisant une clé USB.
S'ils fonctionnent tous les deux sur le même système d'exploitation, il ne devrait pas y avoir de problème majeur.
Cependant, si l'un tourne sous Linux et l'autre sous Windows, vous devez faire attention au système de fichiers que vous allez utiliser pour formater la clé USB.
Vous devez choisir un système de fichiers qui peut être lu par les deux systèmes d'exploitation.

Pour faire court, essayez `exFAT`[^1].

`exFAT`[^2] est un système de fichiers conçu par Microsoft en 2006 et particulièrement optimisé pour les mémoires flash telles que les clés USB et les cartes SD.
Il a été adopté par la SD Association comme système de fichiers par défaut pour quelques cartes SD de taille supérieure à 32GB.
Il est non seulement supporté par Windows (depuis Windows Vista SP1 et Windows Server 2008) mais aussi par macOS (depuis 10.6.5) et Linux (depuis le noyau 5.4 et même avant via FUSE).

## Pouvez-vous formater une partition avec `exFAT` ?

Le noyau Linux 5.4 a été publié en novembre 2019.
Je vais supposer que votre système est à jour et me concentrer sur l'utilisation du support natif d'`exFAT` dans le noyau Linux.
Si ce n'est pas le cas, je vous conseille fortement de mettre à jour votre noyau Linux à une version maintenue.
Au moment d'écrire ces lignes, tous les noyaux supportés à long terme sont capables de gérer `exFAT` nativement (car postérieurs à la version 5.4).

Pour s'assurer que les outils pour `exFAT` sont là, vérifiez juste que `mkfs.exfat` est présent sur votre système.

Si utiliser `exFAT` pour formater une partition est possible, vous aurez la réponse suivante:
{% wide_container() %}
```bash
which mkfs.exfat
#> /usr/sbin/mkfs.exfat
```
{% end %}

Sinon, vous aurez quelque chose comme la réponse suivante :
{% wide_container() %}
```bash
which mkfs.exfat
#> which: no mkfs.exfat in (/usr/local/bin:/usr/local/sbin:/usr/bin:/usr/sbin)
```
{% end %}

Attention, votre chemin d'accès peut varier en fonction du système Linux que vous utilisez.

Dans le cas où les outils `exFAT` ne sont pas là, installez-les simplement en fonction de votre système :
{% wide_container() %}
```bash
sudo apt install exfatprogs # Debian/Ubuntu
sudo dnf install exfatprogs # Red Hat/Fedora/CentOS
sudo pacman -S exfatprogs # Arch Linux/Manjaro
sudo moss install exfatprogs # AerynOS
```
{% end %}

Maintenant que les outils `exFAT` sont disponibles, partitionnons la clé USB.

## Identifier la clé USB

Avant de partitionner la clé USB, vous devez d'abord connaître son *device name*.

Utiliser `lsblk` est généralement suffisant pour identifier votre clé USB avec la commande suivante :
{% wide_container() %}
```bash
lsblk -o name,vendor,model,size,type,tran,mountpoint
#> NAME                                          VENDOR   MODEL                       SIZE TYPE  TRAN   MOUNTPOINT
#> sdX                                           ATA      Micron_1100_MTFDDAV256TBN 238,5G disk  sata
#> ├─sdX1                                                                             554M part
#> ├─sdX2                                                                               4G part
#> └─sdX3                                                                             234G part
#>   └─luks-redacted-0000-0000-0000-000000000000                                      234G crypt        /
#> sdY                                           SanDisk  Ultra Fit                  57,3G disk  usb
#> ├─sdY1                                                                               2G part
#> └─sdY2                                                                             173M part
#> zram0                                                                              7,6G disk         [SWAP]
```
{% end %}

La clé USB que j'utilise est une SanDisk Ultra Fit de 60 GB donc je sais que son *device name* est `sdY`.
Prenez garde au fait que le vôtre devrait être différent.

S'il est impossible d'identifier la clé grâce à cette commande, nous pouvons utiliser `dmesg` en :
1. Débranchant la clé USB
2. Exécutant la commande `sudo dmesg --follow-new` pour afficher les messages à venir
3. Branchant la clé USB

Une fois branchée, vous devriez récupérer des messages similaires à ceux qui suivent :
{% wide_container() %}
```bash
sudo dmesg --follow-new
#> [ <timestamp>] usb 2-2: new SuperSpeed USB device number 3 using xhci_hcd
#> [ <timestamp>] usb 2-2: New USB device found, idVendor=0781, idProduct=5583, bcdDevice= 1.00
#> [ <timestamp>] usb 2-2: New USB device strings: Mfr=1, Product=2, SerialNumber=3
#> [ <timestamp>] usb 2-2: Product: Ultra Fit
#> [ <timestamp>] usb 2-2: Manufacturer: SanDisk
#> [ <timestamp>] usb 2-2: SerialNumber: XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
#> [ <timestamp>] usb-storage 2-2:1.0: USB Mass Storage device detected
#> [ <timestamp>] scsi host1: usb-storage 2-2:1.0
#> [ <timestamp>] scsi 1:0:0:0: Direct-Access     SanDisk  Ultra Fit        1.00 PQ: 0 ANSI: 6
#> [ <timestamp>] sd 1:0:0:0: [sdY] 120176640 512-byte logical blocks: (61.5 GB/57.3 GiB)
#> [ <timestamp>] sd 1:0:0:0: [sdY] Write Protect is off
#> [ <timestamp>] sd 1:0:0:0: [sdY] Mode Sense: 43 00 00 00
#> [ <timestamp>] sd 1:0:0:0: [sdY] Write cache: disabled, read cache: enabled, doesn't support DPO or FUA
#> [ <timestamp>]  sdY: sdY1 sdY2
#> [ <timestamp>] sd 1:0:0:0: [sdY] Attached SCSI removable disk
```
{% end %}

Nous pouvons extraire le *device name* `sdY` des messages.

## Partitionner la clé USB

Maintenant que nous avons trouvé l'équipement avec lequel nous devons travailler, partitionnons-le.

{% admonition(type="danger") %}
Partitionner un disque va rendre toutes ses données inaccessibles.
Les récupérer pourrait être possible mais c'est complexe et sans garantie de succès.
Veuillez sauvegarder vos données avant de partitionner la clé USB si vous voulez les conserver.
{% end %}

Nous devons d'abord choisir le format de la table de partitionnement entre [MBR](<https://fr.wikipedia.org/wiki/Master_boot_record>) et [GPT](<https://fr.wikipedia.org/wiki/GUID_Partition_Table>).

Vous trouverez beaucoup de comparaisons entre ces deux tables de partitionnement sur Internet[^3].
Pour faire simple, MBR est plus vieux, devrait avoir une meilleure compatibilité avec les systèmes d'exploitation plus anciens et est limité à 4 partitions primaires pour un total de 2TB maximum.
GPT est plus récent, plus robuste, a un nombre illimité de partitions et peut gérer jusqu'à 9,4 ZB de stockage.

Enfin, GPT n'est pris en charge que par des micrologiciels UEFI modernes pour la séquence de démarrage (boot) alors que MBR devrait être capable de démarrer à peu près partout.
Cela ne nous concerne pas ici car nous nous concentrons uniquement sur le transfert de données entre Linux et Windows.

Étant plus récent, je pensais que GPT serait plus approprié.
Cependant, après plusieurs tests, je me suis aperçu qu'utiliser une table de partitionnement GPT empêche le système Windows avec lequel je jouais de détecter convenablement la clé USB.
Elle n'est pas accessible depuis l'Explorateur même si nous pouvons la voir dans la gestion des disques.

Les tests consistent juste à partitionner et formater la clé USB comme indiqué ci-dessous depuis un système Linux, même un bête fichier texte dessus, la débrancher, la rebrancher sur un système Windows et essayer de récupérer le fichier.

Dernier point, et pas des moindres : si je branche la clé USB ayant une table de partitionnement GPT dans Windows, détruit la partition et la recrée et la formate avec exFAT, cela fonctionne...
La clé USB est correctement reconnue par le système sous Windows et reste reconnue par le système sous Linux.
Il doit me manquer des options dans la création de la partition pour GPT afin qu'elle soit correctement gérée par Windows.

Les deux prochaines parties vont décrire l'utilisation de chaque table de partitionnement.

Vous devez juste en appliquer une seule et je vous conseille [de partir sur MBR](#mbr-partition).

### Utiliser la table de partitionnement MBR {#mbr-partition}

{% admonition(type="warning") %}
D'après les tests que j'ai faits, c'est la table de partitionnement que je recommanderais.
Ignorez cette partie si vous souhaitez utiliser une [table de partitionnement GPT](#gpt-partition) quand même.
{% end %}

D'abord, créez le disque :
{% wide_container() %}
```bash
sudo parted /dev/sdY mklabel msdos
#> Warning: The existing disk label on /dev/sdY will be destroyed and all data on this disk will be lost. Do you want to continue?
#> Yes/No? yes
#> Information: You may need to update /etc/fstab.
```
{% end %}

Ensuite, créez l'unique partition qui utilise toute la clé USB :
{% wide_container() %}
```bash
sudo parted /dev/sdY mkpart primary ntfs 0% 100%
#> Information: You may need to update /etc/fstab.
```
{% end %}

Vu que `parted` ne propose pas `exfat` comme type de système de fichiers, il est possible d'utiliser `ntfs` à la place.

La partition étant créée, nous devons maintenant [la formater](#format-partition).

### Utiliser la table de partitionnement GPT {#gpt-partition}

{% admonition(type="warning") %}
D'après les tests que j'ai faits, je ne recommande pas cette table de partitionnement.
À moins que vous sachiez ce que vous êtes en train de faire, je vous conseille d'utiliser plutôt une [table de partitionnement MBR](#mbr-partition).
{% end %}

D'abord, créez le disque :
{% wide_container() %}
```bash
sudo parted /dev/sdY mklabel gpt
#> Warning: The existing disk label on /dev/sdY will be destroyed and all data on this disk will be lost. Do you want to continue?
#> Yes/No? yes
#> Information: You may need to update /etc/fstab.
```
{% end %}

Ensuite, créez l'unique partition qui utilise toute la clé USB :
{% wide_container() %}
```bash
sudo parted /dev/sdY mkpart ntfs 0% 100%
#> Information: You may need to update /etc/fstab.
```
{% end %}

Vu que `parted` ne propose pas `exfat` comme type de système de fichiers, il est possible d'utiliser `ntfs` à la place.

La partition étant créée, nous devons maintenant [la formater](#format-partition).

### Formater la partition avec `exFAT` {#format-partition}

{% wide_container() %}
```bash
sudo mkfs.exfat /dev/sdY1 -L 'Data'
#> exfatprogs version : 1.3.0
#> Creating exFAT filesystem(/dev/sdY1, cluster size=131072)
#> 
#> Writing volume boot record: done
#> Writing backup volume boot record: done
#> Fat table creation: done
#> Allocation bitmap creation: done
#> Upcase table creation: done
#> Writing root directory entry: done
#> Synchronizing...
#> 
#> exFAT format complete!
```
{% end %}

La partition a pour label `Data` mais vous pouvez le changer pour mettre ce que vous voulez.
Il doit être court (limité à 11 caractères) et je n'y mettrais aucun espace ou caractère spécial vu que je ne suis pas sûr de comment ce serait géré par les différents systèmes d'exploitation.

Vous pouvez maintenant utiliser votre clé USB à la fois sous Linux et Windows (et probablement sur macOS mais je ne peux pas le tester).

{{ image_figure(src="img/usb-stick-linux.webp", raw_path=true, alt="La clé USB détectée par un ordinateur fonctionnant avec un système d'exploitation Linux (AerynOS)") }}

{{ image_figure(src="img/usb-stick-windows.webp", raw_path=true, alt="La clé USB détectée par un ordinateur fonctionnant avec un système d'exploitation Windows (Windows 11)") }}

[^1]: Le lien est en anglais : <https://askubuntu.com/questions/1281698/what-is-the-best-way-to-format-a-usb-stick-such-that-it-can-be-used-with-both-li>
[^2]: <https://fr.wikipedia.org/wiki/ExFAT>
[^3]: Rechercher "MBR vs GPT" ou "MBR vs GPT usb drive" devrait être suffisant.  
J'ai trouvé cette référence intéressante (en anglais) : <https://darwinsdata.com/what-type-of-partition-for-usb-drive/>

