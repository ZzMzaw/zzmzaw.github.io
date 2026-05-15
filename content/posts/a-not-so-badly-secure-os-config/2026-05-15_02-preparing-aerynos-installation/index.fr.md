+++
title = "Préparer l'installation d'AerynOS"
description = "Comment se préparer à installer AerynOS, du téléchargement de l'image à la préparation du media d'installation"

[taxonomies]
tags = ["os", "aerynos"]

[extra.series_template_variables]
position = "deuxième"
prev_achievement = "nous avons choisi AerynOS comme nouveau système d'exploitation"
curr_objective = "comment préparer son installation"
curr_achievement = "nous sommes prêts à installer AerynOS"
next_objective = "comment procéder"
+++

[La documentation d'AerynOS](https://aerynos.dev/) (en anglais uniquement) contient tout ce qu'il faut pour l'installer, le configurer et l'utiliser au quotidien.
Les bases sont déjà couvertes et les contributeurs l'améliorent jour après jour.

Afin d'être sûr qu'installer AerynOS en vaut la peine, nous devons d'abord nous assurer que notre matériel sera capable de l'exécuter.

[Les prérequis matériels](https://aerynos.dev/users/getting-started/requirements/) sont plutôt bas donc nous allons tester AerynOs en conditions réelles avec l'environnement Live.

# Préparation du média d'installation

Préparer le média d'installation peut se résumer en deux étapes :

1. Télécharger l'image d'installation
2. Le copier sur un clé USB

## Télécharger l'image d'installation

L'image d'installation est un environnement Live d'AerynOS.

Il y a deux moyens de le télécharger, soit depuis leur site Web, soit depuis un lien torrent.

Les deux sont accessibles depuis la page dédiée au téléchargement : <https://aerynos.com/download/>.

Le lien torrent peut être préféré pour limiter la charge sur le CDN (Content Delivery Network).

AerynOS fournit un checksum.
Il doit être vérifié pour s'assurer que l'image est complète et n'a pas été altérée durant le téléchargement.

Assurez-vous toujours de télécharger le fichier checksum depuis le CDN (même si AerynOS ne devrait jamais le rendre disponible autrement).
Vous devez faire confiance au CDN pour le moment vu qu'AerynOS ne signe pas encore son fichier image.
C'est pour l'heure le seul moyen de s'assurer que le fichier image que vous avez téléchargé n'a pas été manipulé.
Si vous récupériez le checksum d'une autre manière (en particulier un torrent), quelqu'un de malveillant pourrait distribuer une image corrompue et son checksum et vous n'auriez aucun moyen de savoir que ce n'est pas l'image AerynOS officielle.

AerynOS signera probablement son fichier image lorsqu'ils seront plus matures pour garantir que l'image téléchargée est bien celle qui a été construite.

Prenez simplement le dernier fichier image `AerynOS-<version>-<desktop>-<architecture>.iso` avec le fichier checksum associé (même nom avec `.sha256sum` ajouté).
Au moment d'écrire ces lignes, il s'agit de :
- <https://cdn.aerynos.dev/isos/AerynOS-2026.05.2-GNOME-live-x86_64.iso>
- <https://cdn.aerynos.dev/isos/AerynOS-2026.05.2-GNOME-live-x86_64.iso.sha256suym>

Une fois que les fichiers image et checksum sont tous les deux téléchargés, vous pouvez vérifier le checksum avant de continuer :
{% wide_container() %}
```bash
sha256sum -c "AerynOS-2026.05.2-GNOME-live-x86_64.iso.sha256sum"
#> AerynOS-2026.05.2-GNOME-live-x86_64.iso: OK
```
{% end %}

Si l'image ne correspond pas au checksum, vous aurez le message suivant :
{% wide_container() %}
```bash
sha256sum -c "AerynOS-2026.05.2-GNOME-live-x86_64.iso.sha256sum"
#> AerynOS-2026.05.2-GNOME-live-x86_64.iso: FAILED
#> sha256sum: WARNING: 1 computed checksum did NOT match
```
{% end %}

Maintenant que l'image a été téléchargée et vérifiée avec succès, créons une clé USB depuis laquelle nous pouvons démarrer un ordinateur (bootable).

## Copier l'image d'installation sur une clé USB

Tout d'abord, vous devez connaître le *device name* de votre clé USB.

Généralement, utiliser la commande `lsblk` est suffisant pour identifier votre clé USB avec ce qui suit :
{% wide_container() %}
```bash
lsblk -o name,vendor,model,size,type,tran,label,mountpoint
#> NAME                                          VENDOR   MODEL                       SIZE TYPE  TRAN   LABEL       MOUNTPOINT
#> sdX                                           ATA      Micron_1100_MTFDDAV256TBN 238,5G disk  sata
#> ├─sdX1                                                                             554M part
#> ├─sdX2                                                                               4G part
#> └─sdX3                                                                             234G part
#>   └─luks-redacted-0000-0000-0000-000000000000                                      234G crypt                    /
#> sdY                                           SanDisk  Ultra Fit                  57,3G disk  usb
#> └─sdY1                                                                            57,3G part
#> zram0                                                                              7,6G disk         zram0       [SWAP]
```
{% end %}

La clé USB que j'utilise est une SanDisk Ultra Fit de 60G donc je sais que son *device name*  est `sdY`.
Prenez garde au fait que le résultat de la commande sera différent et votre *device name* pourrait l'être aussi.

S'il n'est pas possible d'identifier la clé USB à l'aide de la commande, ça devrait être possible d'utiliser `dmesg` en :
1. Débranchant la clé USB
2. Exécutant la commande `sudo dmesg --follow-new` pour afficher les messages à venir
3. Branchant à nouveau la clé USB

Une fois branchée, vous devriez recevoir des messages similaires à ceux qui suivent :
{% wide_container() %}
```bash
sudo dmesg --follow-new
#> [<timestamp>] usb 2-1: new SuperSpeed USB device number 8 using xhci_hcd
#> [<timestamp>] usb 2-1: New USB device found, idVendor=0781, idProduct=5583, bcdDevice= 1.00
#> [<timestamp>] usb 2-1: New USB device strings: Mfr=1, Product=2, SerialNumber=3
#> [<timestamp>] usb 2-1: Product: Ultra Fit
#> [<timestamp>] usb 2-1: Manufacturer: SanDisk
#> [<timestamp>] usb 2-1: SerialNumber: XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
#> [<timestamp>] usb-storage 2-1:1.0: USB Mass Storage device detected
#> [<timestamp>] scsi host1: usb-storage 2-1:1.0
#> [<timestamp>] scsi 1:0:0:0: Direct-Access     SanDisk  Ultra Fit        1.00 PQ: 0 ANSI: 6
#> [<timestamp>] sd 1:0:0:0: [sdY] 120176640 512-byte logical blocks: (61.5 GB/57.3 GiB)
#> [<timestamp>] sd 1:0:0:0: [sdY] Write Protect is off
#> [<timestamp>] sd 1:0:0:0: [sdY] Mode Sense: 43 00 00 00
#> [<timestamp>] sd 1:0:0:0: [sdY] Write cache: disabled, read cache: enabled, doesn't support DPO or FUA
#> [<timestamp>]  sdY: sdY1
#> [<timestamp>] sd 1:0:0:0: [sdY] Attached SCSI removable disk
```
{% end %}

Nous pouvons extraire le *device name* `sdY` des messages.

Avec le *device name*, nous pouvons lancer la commande suivante pour copier l'image sur la clé USB :
{% wide_container() %}
```bash
sudo dd if=/path/to/AerynOS-image.iso of=/dev/sdY bs=4M status=progress oflag=direct
#> <progression de la copie, attendée que la commande se termine>
sudo sync
```
{% end %}

N'oubliez pas `sudo sync` pour être sûr que les derniers bits ont été correctement écrits sur la clé USB (*vide* tout ce qui pourrait rester dans un buffer).

Une fois la copie terminée, nous pouvons vérifier que la clé USB a bien été préparée en utilisant à nouveau la commande `lsblk` :
{% wide_container() %}
```bash
lsblk -o name,vendor,model,size,type,tran,label,mountpoint
#> NAME                                          VENDOR   MODEL                       SIZE TYPE  TRAN   LABEL       MOUNTPOINT
#> sdX                                           ATA      Micron_1100_MTFDDAV256TBN 238,5G disk  sata
#> ├─sdX1                                                                             554M part
#> ├─sdX2                                                                               4G part
#> └─sdX3                                                                             234G part
#>   └─luks-redacted-0000-0000-0000-000000000000                                      234G crypt                    /
#> sdY                                           SanDisk  Ultra Fit                  57,3G disk  usb    AERYNOSLIVE
#> ├─sdY1                                                                             2,4G part         AERYNOSLIVE
#> └─sdY2                                                                             252M part         EFIBOOTISO
#> zram0                                                                              7,6G disk         zram0       [SWAP]
```
{% end %}

# Configurer le BIOS/UEFI

Le BIOS/UEFI est ce qui est exécuté en premier lorsque nous appuyons sur le bouton d'allumage de notre ordinateur.
BIOS [^1] est plus vieux, UEFI [^2] est plus récent.

UEFI amène beaucoup d'améliorations tant d'un point de vue fonctionnel que sécurité, donc j'aurai tendance à le privilégier.
De toute façon, AerynOS ne supporte que UEFI donc nous n'aurons pas le choix ici.

Le BIOS/UEFI est habituellement embarqué dans la carte mère par le fabricant et ne devrait pas être particulièrement orienté vie privée.

Des alternatives libres et open source (FOSS) existent cependant.
Si cela vous intéresse, vous pouvez regarder [coreboot](https://coreboot.org/) et [libreboot](https://libreboot.org/).
Quelques fabricants respectueux de la vie privée l'embarquent même dans leurs ordinateurs [^3].

Malheureusement, mon ordinateur portable a un microgiciel UEFI ASUS propriétaire et n'est pas supporté directement par libreboot ou des alternatives.
Je ne vais pas essayer de le faire supporter maintenant.
Ça me prendrait bien trop de temps parce qu'il faudrait que je monte sérieusement en compétences sur ce sujet.
Peut-être plus tard.

La manière d'accéder au BIOS/UEFI peut varier d'un ordinateur à l'autre vu que ça dépend du fabricant de la carte mère.
D'habitude, ça consiste à taper de manière répétée sur l'une des touches suivantes dès que l'ordinateur démarre : `F1`, `F2`, `F8`, `F10`, `Suppr` ou `Esc`.
Recherchez simplement comment atteindre le BIOS pour votre matériel.
Il devrait y avoir plein d'endroits sur Internet expliquant comment faire.
Quoi qu'il en soit, si vous arrivez sur votre système d'exploitation habituel, ça veut dire que vous avez échoué.
Redémarrez et essayez à nouveau.

À ce stade, le seul conseil orienté sécurité que je peux donner est de mettre en place un mot de passe fort pour votre BIOS/UEFI.
Accéder au BIOS/UEFI permet à un attaquant de modifier les paramètres de lancement de l'ordinateur (incluant l'ordre dans lequel il va chercher les systèmes d'exploitation disponibles, le Secure Boot, ...).
Le verrouiller est la toute première étape pour essayer de protéger votre ordinateur contre quelqu'un pouvant y accéder physiquement (e.g. l'attaque de la femme de chambre malveillante [^4]).
Ça rendra aussi l'ordinateur plus difficile à réutiliser en cas de vol en ralentissant, sans pour autant complètement empêcher, la réinstallation d'un nouveau système.
Même si aujourd'hui vous devez contacter le support du fabricant la plupart du temps, les matériels anciens peuvent avoir leur mot de passe BIOS remis à zéro en agissant sur la carte mère (en enlevant une pile, en court-circuitant des broches).

{% admonition(type="warning") %}
Assurez-vous d'avoir un moyen certain de vous rappeler de votre mot de passe BIOS/UEFI quitte à l'écrire et à le stocker quelque part en sûreté.
Vous ne devriez pas vous en servir tous les jours et c'est plutôt facile de l'oublier.
Néanmoins, soyez sûr que vous en aurez besoin un jour (e.g. pour réinstaller un nouveau système d'exploitation).
{% end %}

Afin de mettre un mot de passe au BIOS/UEFI, nous devons d'abord entrer dans le menu du BIOS/UEFI.
Redémarrons donc notre ordinateur et accédons-y comme expliqué précédemment.

Chaque menu de BIOS/UEFI est différent mais mettre en place un mot de passe devrait être possible depuis l'onglet `Sécurité` (peut-être après être passé en mode avancé s'il y en a un).
Il se peut qu'il y ait différents mots de passe pour différents privilèges (administrateur, utilisateur).
Nous cherchons ici à configurer le mot de passe pour l'administrateur.
Je vous conseille de maintenir les autres désactivés s'il y en a.
Changez-les uniquement s'il est impossible de les désactiver.
Soyez sûr que les mots de passe par défaut seront la première chose qu'un attaquant essaiera.

Enfin, prenez garde à la configuration du clavier qui devrait être QWERTY par défaut. 
Ça ne devrait pas être un gros problème quand vous tapez votre mot de passe pour vous connecter dans la mesure où vous devriez simplement avoir besoin de taper sur les mêmes touches que quand vous l'avez mis en place.
Cependant, cela devrait être pris en considération lorsqu'il est écrit ailleurs.

Dès que vous l'avez mis en place, sauvegardez les modifications, redémarrez l'ordinateur et essayez d'entrer dans le menu du BIOS/UEFI.
Le mot de passe que vous venez de mettre en place devrait vous être demandé.
Assurez-vous qu'il fonctionne comme attendu avant de continuer (vous ne pourrez pas si ce n'est pas le cas de toute façon...).

À ce stade, vous devriez être revenu dans le menu du BIOS/UEFI.

Comme indiqué dans les prérequis, AerynOS ne supporte ni le BIOS ni l'UEFI avec le mode CSM donc nous devons être sûrs qu'il est bien désactivé.
CSM est l'acronyme pour `Compatibility Support Module` (`Module de Support de Compatibilité`).
S'il est activé, UEFI va, en quelque sorte, simuler un BIOS pour être compatible avec d'anciens systèmes.
CSM est aussi parfois appelé `Legacy Mode`.
Trouvez simplement l'option associée dans le BIOS et assurez-vous que vous utilisez UEFI sans CSM.

AerynOS Live ne démarrera pas s'il détecte que vous n'utilisez pas de l'UEFI pur et vous obtiendrez l'écran suivant :

{{ image_figure(src="img/bios-csm-mode-detected.webp", raw_path=true, alt="L'écran affiché par AerynOS Live lorsque BIOS ou UEFI avec CSM est utilisé") }}

Une étape supplémentaire vers une protection complète de l'ordinateur aurait été d'activer UEFI Secure Boot.
Cela permet une chaine de confiance afin de s'assurer que la séquence de démarrage n'a pas été manipulée.
Malheureusement, AerynOS ne supporte pas encore Secure Boot au moment d'écrire ces lignes [^5].
Quoi qu'il en soit, il est généralement préférable de l'activer ultérieurement pour ne pas trop complexifier la première installation.
J'ajouterai un article dédié à cette série si je trouve un moyen de le mettre en oeuvre correctement.

La dernière étape est de configurer l'ordre de démarrage.
Comme son nom l'indique, l'ordre de démarrage décide dans quel ordre BIOS/UEFI va chercher dans les disques ou ailleurs (réseau, ...) pour trouver un système d'exploitation avec lequel démarrer.
Vous devriez toujours avoir uniquement configuré le disque sur lequel vous avez votre système d'exploitation.
Ça permettra d'éviter que n'importe quel autre système d'exploitation ne soit lancé avant le vôtre.
Cette option devrait être disponible dans un onglet `Boot`.

Une fois que votre BIOS/UEFI est correctement configuré, assurez-vous de sauvegarder les modifications.
Cela devrait redémarrer l'ordinateur.

# Démarrer depuis la clé USB

Tout d'abord, insérez la clé USB dans l'ordinateur.
Toutefois, comme nous nous sommes assuré que l'ordre de démarrage est limité au disque de l'ordinateur, il ne va pas se lancer depuis la clé USB par défaut.

La plupart des BIOS/UEFI ont un menu de choix de démarrage permettant de sélectionner d'où démarrer.
Nous devons juste atteindre le BIOS/UEFI comme expliqué précédemment et sélectionner manuellement un lancement depuis la clé USB.

Si tout fonctionne comme prévu, vous devriez maintenant voir l'environnement de bureau d'AerynOS (GNOME) :

{{ image_figure(src="img/aerynos-live-first-screen.webp", raw_path=true, alt="The first screen displayed by AerynOS Live") }}

Vous pouvez maintenant jouer un peu avec la version Live d'AerynOS pour voir si elle vous convient.
Gardez en mémoire que vous avez un espace de stockage limité (tout est dans la RAM pour un environnement Live).

Avant de continuer, configurez votre réseau (Ethernet ou Wi-Fi) et assurez-vous que vous pouvez vous connecter à Internet.
Pour le moment, l'installeur AerynOS a besoin d'une connectivité à Internet car il doit télécharger tous les packages dont vous aurez besoin.

<!-- series_outro -->

[^1]: <https://fr.wikipedia.org/wiki/BIOS_(informatique)>
[^2]: <https://fr.wikipedia.org/wiki/UEFI>
[^3]: <https://doc.coreboot.org/distributions.html> (en anglais)
[^4]: Vous pouvez lire <https://en.wikipedia.org/wiki/Evil_maid_attack> (en anglais) pour une description de l'attaque et <http://theinvisiblethings.blogspot.com/2009/10/evil-maid-goes-after-truecrypt.html> (en anglais aussi) pour un example pratique
[^5]: <https://aerynos.dev/faq/lacking-features/> (en anglais)
