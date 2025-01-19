+++
title = "Préparer l'installation de Guix System"
description = "Comment être prêt à installer Guix System sur un ordinateur portable, de la vérification du matériel à la prépartion du média d'installation"

[taxonomies]
tags = ["os", "guix"]

[extra.series_template_variables]
position = "deuxième"
prev_achievement = "nous avons choisi Guix System comme nouveau système d'exploitation"
curr_objective = "comment préparer son installation"
curr_achievement = "nous sommes prêts à installer Guix System"
next_objective = "comment le faire réellement"
+++

La [documentation de Guix](https://guix.gnu.org/manual/) est très bien organisée et complète.
Elle existe en plusieurs langues et formats.

Ce qui est intéressant pour nous est qu'elle contient [une section entière sur comment installer Guix System](https://guix.gnu.org/manual/fr/html_node/Installation-du-systeme.html).

Avant de commencer, je ne peux que conseiller de prendre connaissance des [limitations](https://guix.gnu.org/manual/fr/html_node/Limitations.html) et des [considérations matérielles](https://guix.gnu.org/manual/fr/html_node/Considerations-materielles.html).
Guix adhère strictement à la "Définition du Logiciel Libre" [^1].
Cela signifie que les logiciels non libres ne seront pas supportés et qu'il n'y a même pas lieu d'en discuter.
Ce point de vue doit être respecté lors des interactions avec les équipes qui maintiennent Guix.

Toutefois, ajouter le support des logiciels non libres est possible.
Pour Guix, cela se fait principalement grâce au [dépôt Nonguix](https://gitlab.com/nonguix/nonguix).

Sinon, je ne serai pas en capacité d'exécuter Guix System sur mon ordinateur portable.
Je l'ai acheté il y a quelques années lorsque j'étais beaucoup moins conscient du logiciel libre, mais il fonctionne toujours bien jusqu'à maintenant.
Je n'ai donc pas envie de le changer pour le moment.

# Considérations matérielles

Avant d'aller plus loin, je veux mieux connaître l'ordinateur portable que je possède afin de m'assurer qu'il soit compatible avec ce que je veux faire.
Ce paragraphe sera mis à jour avec les futurs articles pour avoir une place unique où sont listés tous les prérequis matériels nécessaires pour atteindre la fin de série.

J'utilise actuellement un ASUS Notebook UX330U avec pour CPU un Core i7 7500, 8GB de RAM et environ 500GB de disque SSD.
Linux est déjà installé dessus, donc je sais qu'il existe des pilotes compatibles pour les composants matériels les plus importants.

## Fonctionnera-t-il qu'avec du logiciel libre ?

Il n'y a aucune chance pour qu'un ancien ordinateur portable fourni par une marque commerciale telle qu'Asus puisse être certifié par le [programme Respect Your Freedom](https://ryf.fsf.org/).
Si vous cherchez un nouvel ordinateur portable, cela pourrait être un bon point de départ, même si la liste des ordinateurs portables est plutôt limitée pour le moment.
La liste des vendeurs peut être utilisée pour regarder plus de produits et passer à d'autres vendeurs similaires.
[Minifree Ltd](https://minifree.org/), pas listé ici au moment d'écrire ces lignes, vaut la peine d'être nommé, même si je n'ai personnellement jamais utilisé aucun de leurs produits.
La meilleure solution pour ceux qui veulent acheter un nouvel ordinateur portable pour y installer Guix System reste de parcourir Internet avec votre moteur de recherche favori et de chercher des retours récents de personnes à ce sujet.

Si, comme moi, vous avez déjà un ordinateur portable, vous pouvez [chercher dans h-node](https://www.h-node.org/search/form/fr) pour vérifier à quel point il est compatible avec les logiciels libres.

{{ image_figure(src="img/h-node-search.fr.webp", raw_path=true, alt="La page de recherche de h-node") }}

Utiliser les fonctionnalités de recherche est plutôt simple :
- Copiez et collez juste le résultat de `lspci -vmmnn` dans *analysez le retour de la commande lspci* (comme sur la page) et la recherche listera ce qui a été identifié et si c'est compatible.
Vous pouvez toujours cliquer sur le nom du modèle pour avoir plus de détails.
- Copiez et collez simplement le `<vendorid>:<productid>` du résultat de la commande `lsusb` dans *cherchez par vendorid:productid* et la recherche dira s'il est dans la base de données h-node ou pas.
Cliquer sur le nom du modèle donne plus de détails et s'il est compatible.

Ainsi, vous pouvez vérifier que l'adaptateur Wi-Fi, le contrôleur USB, l'interface Bluetooth, la webcam, le lecteur d'empreintes digitales... sont supportés.

L'adaptateur Wi-Fi et les contrôleurs USB sont particulièrement à vérifier, car vous allez avoir besoin du réseau pendant l'installation pour télécharger des packages.
Le reste peut être réparé après l'installation si des pilotes compatibles avec Linux existent.

Quoi qu'il en soit, le programme d'installation de Guix System vous prévient dans le cas où il détecte des appareils qui manquent d'un firmware ou d'un pilote libre.

Dans mon cas, je sais déjà que l'adaptateur Wi-Fi n'est pas compatible avec le logiciel libre :

{{ image_figure(src="img/h-node-lspci-search-result.fr.webp", raw_path=true, alt="Le résultat de recherche lspci de h-node montrant que mon adaptateur Wi-Fi n'est pas compatible avec le logiciel libre") }}

Toutefois, j'ai un adaptateur USB Ethernet qui l'est :

{{ image_figure(src="img/h-node-lsusb-search-result.fr.webp", raw_path=true, alt="Le résultat de recherche lsusb de h-node montrant que mon adaptateur USB Ethernet est compatible avec le logiciel libre") }}

J'ai prévu d'utiliser ce dernier dans tous les cas pendant l'installation, car l'Ethernet est plus rapide que le Wi-Fi là où je suis.

# Préparation du média d'installation

La préparation du média d'installation est rendue simple en suivant la section dédiée à l'[installation depuis une clef USB ou un DVD](https://guix.gnu.org/manual/fr/html_node/Installation-depuis-une-clef-USB-ou-un-DVD.html) du manuel Guix.

On peut la résumer en deux étapes :

1. Récupérer l'image d'installation
2. La copier sur une clef USB

## Récupérer l'image d'installation

Il y a plusieurs façons de récupérer une image d'installation pour Guix System.
Choisir la méthode à laquelle vous faites le plus confiance dépend de vous, en adéquation avec votre modèle de menaces.
Personnellement, j'ai préféré l'image officielle dans la mesure où j'avais une alternative à l'adaptateur Wi-Fi incompatible.

Une image d'OS téléchargée depuis l'Internet ne devrait être utilisée qu'après avoir vérifié sa signature pour s'assurer qu'elle n'a pas été altérée depuis sa génération.
Vous pouvez vous référer à [l'article dédié à la vérification de signature](@/posts/2024-12-29_verifying-openpgp-signature/index.fr.md) pour plus de détails.

### Télécharger l'image officielle

L'image officielle peut être récupérée depuis <https://guix.gnu.org/en/download/>.

Elle est construite par l'équipe qui maintient Guix et est la base de toutes les autres images.
Cela devrait être votre choix préféré, sauf si vous avez besoin de logiciels non libres pour le réseau et que vous n'avez pas d'alternative.

Récupérez juste la dernière version de `guix-system-install` pour votre architecture (`x86_64` et `i686` sont toutes les deux supportées) avec le fichier de signature associé (même nom avec `.sig` en plus).
Au moment d'écrire ces lignes, il s'agit de :
* <https://ftpmirror.gnu.org/gnu/guix/guix-system-install-1.4.0.x86_64-linux.iso>
* <https://ftpmirror.gnu.org/gnu/guix/guix-system-install-1.4.0.x86_64-linux.iso.sig>

{% admonition(type="tip", title="Utilisation du dépôt original") %}
<https://ftpmirror.gnu.org/gnu/guix/> vous redirige au dépôt miroir le plus proche de vous.  
Si vous avez un doute quelconque concernant la confiance que vous pouvez avoir dans ce dépôt miroir du point de vue de la vie privée ou de la sécurité ou si votre façon de télécharger ne supporte pas les redirections, utilisez simplement <https://ftp.gnu.org/gnu/guix/> à la place.  
Il s'agit du dépôt principal fourni par ceux qui maintiennent Guix.
{% end %}

Une fois que l'image et la signature ont été téléchargées, vous pouvez essayer de vérifier la signature avant d'importer la clé :
{% wide_container() %}
```bash
gpg --verify 'guix-system-install-1.4.0.x86_64-linux.iso.sig'
#> gpg: assuming signed data in 'guix-system-install-1.4.0.x86_64-linux.iso'
#> gpg: Signature made Sun Dec 18 22:09:26 2022 CET
#> gpg:                using RSA key 3CE464558A84FDC69DB40CFB090B11993D9AEBB5
#> gpg: Can't check signature: No public key
```
{% end %}

Téléchargeons la clé comme indiqué dans la [documentation Guix System](https://guix.gnu.org/manual/fr/html_node/Installation-depuis-une-clef-USB-ou-un-DVD.html) :
{% wide_container() %}
```bash
wget 'https://sv.gnu.org/people/viewgpg.php?user_id=15145' -O 'guix-system-install-key.gpg'
gpg --show-keys 'guix-system-install-key.gpg'
#> pub   rsa4096 2014-08-11 [SC] [expires: 2025-04-11]
#>       3CE464558A84FDC69DB40CFB090B11993D9AEBB5
#> uid                      Ludovic Courtès <ludo@gnu.org>
#> uid                      Ludovic Courtès <ludo@chbouib.org>
#> uid                      Ludovic Courtès (Inria) <ludovic.courtes@inria.fr>
#> sub   rsa4096 2014-08-11 [E]
```
{% end %}

L'empreinte de la clé est la même, donc il s'agit bien de la clé qui a signé l'image.

La clé prétend être celle de Ludovic Courtès, mais il n'y a aucun moyen de le confirmer dans la documentation.

Chercher `Ludovic Courtès` avec votre moteur de recherche préféré devrait vous amener à sa page Inria parmi les premiers résultats : <https://people.bordeaux.inria.fr/lcourtes/>.

L'Inria est l'Institut national de recherche en sciences et technologies du numérique, un organisme français, et j'aurais tendance à lui faire confiance.

Ludovic Courtès fournit l'empreinte de sa clé PGP sur sa page et elle correspond à celle de la clé ci-dessus :

{{ image_figure(src="img/ludovic-courtes-inria-page.webp", raw_path=true, alt="La page Inria de Ludovic Courtès", full_width=true) }}

Ayant deux sources différentes pointant vers la même clé, je pars du principe que je peux lui faire confiance et je l'importe :
{% wide_container() %}
```bash
gpg --import 'guix-system-install-key.gpg' 
#> gpg: key 090B11993D9AEBB5: 127 signatures not checked due to missing keys
#> gpg: key 090B11993D9AEBB5: public key "Ludovic Courtès <ludo@gnu.org>" imported
#> gpg: Total number processed: 1
#> gpg:               imported: 1
#> gpg: no ultimately trusted keys found
```
{% end %}

Une fois la clé importée, on peut maintenant vérifier que la signature est valide :
{% wide_container() %}
```bash
gpg --verify 'guix-system-install-1.4.0.x86_64-linux.iso.sig' 
#> gpg: assuming signed data in 'guix-system-install-1.4.0.x86_64-linux.iso'
#> gpg: Signature made Sun Dec 18 22:09:26 2022 CET
#> gpg:                using RSA key 3CE464558A84FDC69DB40CFB090B11993D9AEBB5
#> gpg: Good signature from "Ludovic Courtès <ludo@gnu.org>" [unknown]
#> gpg:                 aka "Ludovic Courtès <ludo@chbouib.org>" [unknown]
#> gpg:                 aka "Ludovic Courtès (Inria) <ludovic.courtes@inria.fr>" [unknown]
#> gpg: WARNING: This key is not certified with a trusted signature!
#> gpg:          There is no indication that the signature belongs to the owner.
#> Primary key fingerprint: 3CE4 6455 8A84 FDC6 9DB4  0CFB 090B 1199 3D9A EBB5
```
{% end %}

L'image a été vérifiée avec succès et il est maintenant possible de [la copier sur une clé USB](#copy-image-to-usb-stick).

### Télécharger une image alternative

Si l'image officielle ne vous convient pas pour des raisons de compatibilité, vous pouvez essayer d'utiliser celle de Nonguix depuis <https://gitlab.com/nonguix/nonguix/-/releases>.

Elle est construite par l'équipe (ou la personne) qui maintient Nonguix et vous allez, quoi qu'il en soit, finir par utiliser ce dépôt pour tous les logiciels non libres.

Récupérez simplement la dernière version de `nonguix-system-install` pour votre architecture (seule `x86_64` est supportée) avec le fichier de signature associé (même nom avec `.asc` en plus).
Au moment d'écrire ces lignes, il s'agit de :
* <https://substitutes.nonguix.org/nonguix-system-install-1.4.0.x86_64-linux.iso>
* <https://substitutes.nonguix.org/nonguix-system-install-1.4.0.x86_64-linux.iso.asc>

Une fois que l'image et la signature ont été téléchargées, vous pouvez essayer de vérifier la signature avant d'importer la clé :
{% wide_container() %}
```bash
gpg --verify 'nonguix-system-install-1.4.0.x86_64-linux.iso.asc'
#> gpg: assuming signed data in 'nonguix-system-install-1.4.0.x86_64-linux.iso'
#> gpg: Signature made Wed Dec 21 00:24:38 2022 CET
#> gpg:                using RSA key 81416036E81A5CF78F801071ECFC83988B4E4B9F
#> gpg: Can't check signature: No public key
```
{% end %}

Téléchargeons la clé comme indiqué dans la [page de releases Nonguix](https://gitlab.com/nonguix/nonguix/-/releases/v1.4.0) :
{% wide_container() %}
```bash
wget 'https://gitlab.com/jonsger.gpg' -O 'nonguix-system-install-key.gpg'
gpg --show-keys 'nonguix-system-install-key.gpg'
#> pub   rsa2048 2014-01-31 [SCA] [expires: 2025-01-28]
#>       81416036E81A5CF78F801071ECFC83988B4E4B9F
#> uid                      Jonathan Brielmaier <j.brielmaier@pantherx.org>
#> uid                      Jonathan Brielmaier <jbrielmaier@opensuse.org>
#> uid                      jonsger <jonathan.brielmaier@web.de>
#> sub   rsa2048 2014-01-31 [E] [expires: 2025-01-28]
```
{% end %}

L'empreinte de la clé est la même, donc il s'agit bien de la clé qui a signé l'image.

La clé prétend être celle de Jonathan Brielmaier, mais il n'y a aucun moyen de le confirmer dans la documentation.

Chercher `Jonathan Brielmaier` n'a pas fourni de résultats probants, mais chercher `Jonathan Brielmaier gpg` devrait vous amener à sa page sur Savanah: <https://savannah.gnu.org/users/jonsger>.

La page permet de télécharger sa clé GPG qui correspond à celle ci-dessus :
{% wide_container() %}
```bash
wget 'https://savannah.gnu.org/people/viewgpg.php?user_id=223505' -O 'nonguix-system-install-key2.gpg'
gpg --show-keys 'nonguix-system-install-key2.gpg'
#> pub   rsa2048 2014-01-31 [SCA] [expired: 2024-01-29]
#>       81416036E81A5CF78F801071ECFC83988B4E4B9F
#> uid                      Jonathan Brielmaier <jbrielmaier@opensuse.org>
#> uid                      jonsger <jonathan.brielmaier@web.de>
#> uid                      Jonathan Brielmaier <j.brielmaier@pantherx.org>
#> sub   rsa2048 2014-01-31 [E] [expired: 2024-01-29]
```
{% end %}

Ayant deux sources différentes pointant vers la même clé, je pars du principe que je peux lui faire confiance et je l'importe :
{% wide_container() %}
```bash
gpg --import 'nonguix-system-install-key.gpg' 
#> gpg: key ECFC83988B4E4B9F: 2 signatures not checked due to missing keys
#> gpg: key ECFC83988B4E4B9F: public key "Jonathan Brielmaier <j.brielmaier@pantherx.org>" imported
#> gpg: Total number processed: 1
#> gpg:               imported: 1
#> gpg: no ultimately trusted keys found
```
{% end %}

Une fois la clé importée, on peut maintenant vérifier que la signature est valide :
{% wide_container() %}
```bash
gpg --verify 'nonguix-system-install-1.4.0.x86_64-linux.iso.asc'
#> gpg: assuming signed data in 'nonguix-system-install-1.4.0.x86_64-linux.iso'
#> gpg: Signature made Wed Dec 21 00:24:38 2022 CET
#> gpg:                using RSA key 81416036E81A5CF78F801071ECFC83988B4E4B9F
#> gpg: Good signature from "Jonathan Brielmaier <j.brielmaier@pantherx.org>" [unknown]
#> gpg:                 aka "Jonathan Brielmaier <jbrielmaier@opensuse.org>" [unknown]
#> gpg:                 aka "jonsger <jonathan.brielmaier@web.de>" [unknown]
#> gpg: WARNING: This key is not certified with a trusted signature!
#> gpg:          There is no indication that the signature belongs to the owner.
#> Primary key fingerprint: 8141 6036 E81A 5CF7 8F80  1071 ECFC 8398 8B4E 4B9F
```
{% end %}

L'image a été vérifiée avec succès et il est maintenant possible de [la copier sur une clé USB](#copy-image-to-usb-stick).

Le seul problème de cette image est qu'elle n'est rafraîchie que lorsqu'une nouvelle version de Guix System est livrée (ce qui n'est pas souvent).
Comme dit plus haut, j'ai préféré utiliser l'image officielle, donc je ne suis pas sûr à quel point il est facile d'installer Guix System avec cette image et s'il y a des impacts.

Au moment d'écrire ces lignes, il y a aussi une note importante pour la version 1.4.0 nécessitant de manuellement importer les canaux Nonguix:

{{ image_figure(src="img/nonguix-1.4.0-notice.webp", raw_path=true, alt="La note importante concernant la version 1.4.0 de Nonguix", full_width=true) }}

Des tests que j'ai pu faire, j'ai pu utiliser de manière brute cette image, sans plus de configuration, et l'adaptateur Wi-Fi a fonctionné dans l'installeur.
Cependant, je ne suis pas allé plus loin.

Si vous voulez suivre cette direction, je vous conseille de jeter un oeil aux articles suivants (en anglais) :

- <https://systemcrafters.net/craft-your-system-with-guix/full-system-install/>
- <https://wiki.systemcrafters.net/guix/nonguix-installation-guide/>
- <https://port19.xyz/tech/nonguix/>

Il est important de mentionner que System Crafters fournit une image d'installation basée sur Nonguix et rafraîchie régulièrement.
Elle est hébergée à <https://github.com/SystemCrafters/guix-installer>, mais je n'ai pas vérifié moi-même comment elle est construite (pas sûr d'en être capable de toute façon) et l'image n'est pas signée.

Quelle que soit l'image d'installation que vous choisissez, les prochains articles de la série devraient s'appliquer quand même, vu qu'ils s'appuient sur le même Guix System.

### Compiler depuis les sources

C'est une voie que je n'ai pas empruntée (tout du moins, pas encore), mais vous pouvez trouver des explications utiles dans la documentation officielle sur [comment construire l'image d'installation](https://guix.gnu.org/manual/fr/html_node/Construire-l_0027image-d_0027installation.html).
Comme cela nécessite Guix, vous devriez d'abord avoir besoin de mettre en oeuvre un Guix System Live ou d'installer Guix sur une autre distribution Linux.

Quoi qu'il en soit, une fois que l'image a été générée, il est maintenant possible de [la copier sur une clé USB](#copy-image-to-usb-stick).

## Copier l'image d'installation sur une clé USB {#copy-image-to-usb-stick}

Tout d'abord, vous devez connaître le *device name* de votre clé USB.

Habituellement, utiliser `lsblk` est suffisant pour identifier votre clé USB avec la commande suivante :
{% wide_container() %}
```bash
lsblk -o name,vendor,model,size,type,tran
#> NAME    VENDOR   MODEL       SIZE TYPE TRAN
#> sdX     SanDisk  Ultra Fit  57.3G disk usb
#> ├─sdX1                       1.4G part 
#> └─sdX2                       2.8M part 
#> xvda                          10G disk 
#> ├─xvda1                      200M part 
#> ├─xvda2                        2M part 
#> └─xvda3                      9.8G part 
#> xvdb                           2G disk 
#> xvdc                          12G disk 
#> ├─xvdc1                        1G part 
#> └─xvdc3                       11G part 
#> xvdd                       526.4M disk
```
{% end %}

La clé USB que j'utilise est une SanDisk Ultra Fit de 60G donc je sais que son *device name* est `sdX`.
Prenez garde au fait que le vôtre pourrait être différent.

S'il s'avère impossible d'identifier la clé avec cette commande, il reste possible d'utiliser `dmesg` :
1. Retirer la clé USB
2. Exécuter la commande `sudo dmesg --follow-new` pour afficher tous les messages à venir
3. Brancher la clé USB

Une fois branchée, vous devriez recevoir des messages similaires à ceux qui suivent :
{% wide_container() %}
```bash
sudo dmesg --follow-new
#> [ <timestamp>] usb 3-2: new SuperSpeed USB device number 5 using xhci_hcd
#> [ <timestamp>] usb 3-2: New USB device found, idVendor=0781, idProduct=5583, bcdDevice= 1.00
#> [ <timestamp>] usb 3-2: New USB device strings: Mfr=1, Product=2, SerialNumber=3
#> [ <timestamp>] usb 3-2: Product: Ultra Fit
#> [ <timestamp>] usb 3-2: Manufacturer: SanDisk
#> [ <timestamp>] usb 3-2: SerialNumber: XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
#> [ <timestamp>] usb-storage 3-2:1.0: USB Mass Storage device detected
#> [ <timestamp>] scsi host2: usb-storage 3-2:1.0
#> [ <timestamp>] scsi 2:0:0:0: Direct-Access     SanDisk  Ultra Fit        1.00 PQ: 0 ANSI: 6
#> [ <timestamp>] scsi 2:0:0:0: Attached scsi generic sg0 type 0
#> [ <timestamp>] sd 2:0:0:0: [sdX] 120176640 512-byte logical blocks: (61.5 GB/57.3 GiB)
#> [ <timestamp>] sd 2:0:0:0: [sdX] Write Protect is off
#> [ <timestamp>] sd 2:0:0:0: [sdX] Mode Sense: 43 00 00 00
#> [ <timestamp>] sd 2:0:0:0: [sdX] Write cache: disabled, read cache: enabled, doesn't support DPO or FUA
#> [ <timestamp>]  sdX: sdX1 sdX2
#> [ <timestamp>] sd 2:0:0:0: [sdX] Attached SCSI removable disk
```
{% end %}

Nous pouvons extraire le *device name* `sdX` des messages.

Avec le *device name*, nous pouvons exécuter la commande suivante pour copier l'image vers la clé USB :
{% wide_container() %}
```bash
sudo dd if=/path/to/guix-image.iso of=/dev/sdX bs=4M status=progress oflag=sync
```
{% end %}

N'oubliez pas `oflag=sync` pour s'assurer que les derniers bits sont bien écrits sur la clé (*flush*).

[^1]: <https://www.gnu.org/philosophy/free-sw.en.html>
