+++
title = "Compatibilité du matériel avec le logiciel libre"
description = "Comment savoir à quel point the matériel que nous avons ou voulons est compatible avec le logiciel libre"

[taxonomies]
tags = ["philosophie", "os"]
+++

Que ce soit pour mieux connaître son matériel ou pour se préparer à passer à un autre système d'exploitation (e.g. Guix System [^1]), il peut être utile de savoir combien notre matériel est compatible avec le logiciel *libre*.

# Qu'est-ce que le logiciel *libre* ?

Le logiciel *libre* a une définition claire fournie par le GNU [^2]:
> "Logiciel libre" [free software] désigne des logiciels qui respectent la liberté des utilisateurs.
> **En gros, cela veut dire que les utilisateurs ont la liberté d'exécuter, copier, distribuer, étudier, modifier et améliorer ces logiciels.**
>
> -- <https://www.gnu.org/philosophy/free-sw.fr.html>

Vous devez comprendre *libre* dans le sens de la liberté d'expression et non pas dans le sens de gratuit (comme dans entrée libre).

Le logiciel *libre* ne doit pas être confondu avec le logiciel en source ouverte (Open Source) qui ne poursuit pas exactement la même philosophie [^3].

Que l'on veuille adhérer strictement au logiciel *libre*, juste essayer d'y tendre ou l'ignorer complètement, c'est un choix de vie, comme n'importe quel autre dans d'autres domaines.
En tout cas, c'est une décision qui demande du dévouement et, comme tout autre choix de vie, elle mérite le respect.

S'il y a de plus en plus de logiciels *libre*s dans les différentes strates de l'informatique, il y a toujours une couche indispensable qui en manque : les pilotes.

Un pilote est un composant logiciel qui permet à un système d'exploitation et à un appareil de communiquer.
C'est un bout de code bas niveau étroitement lié à l'appareil et généralement produit par son fabricant.
Si l'on tend à voir de plus en plus de pilotes *libre*s, c'est impossible pour la communauté de supporter tous les appareils existants.

Je sais que des pilotes compatibles existent pour mes composants matériels car mon ordinateur portable tourne sous Linux depuis un moment sans problème.
Ils pourraient néanmoins ne pas être *libre*s.

La question est maintenant de savoir à quel point les composants matériels que nous avons sont compatibles avec des pilotes *libre*s.

# Mon ordinateur portable fonctionnerait-il uniquement avec du logiciel libre ?

J'utilise actuellement un ASUS Notebook UX330U avec pour CPU un Core i7 7500, 8GB de RAM et environ 500GB de disque SSD.

Nous pouvons [chercher dans h-node](https://www.h-node.org/search/form/fr) pour vérifier à quel point il est compatible avec les logiciels libres.

{{ image_figure(src="img/h-node-search.fr.webp", raw_path=true, alt="La page de recherche de h-node") }}

`h-node` est une base de données de matériels utilisée pour identifier les appareils capables de fonctionner avec un système d'exploitation complètement *libre*.
Il s'appuie sur la communauté pour alimenter la base de données donc n'hésitez pas à contribuer.

Utiliser les fonctionnalités de recherche est plutôt simple :
- Copiez et collez juste le résultat de `lspci -vmmnn` dans *"analysez le retour de la commande lspci"* (comme sur la page) et la recherche listera ce qui a été identifié et si c'est compatible.
Vous pouvez toujours cliquer sur le nom du modèle pour avoir plus de détails.
- Copiez et collez simplement le `<vendorid>:<productid>` du résultat de la commande `lsusb` dans *"cherchez par vendorid:productid"* et la recherche dira s'il est dans la base de données h-node ou pas.
Cliquer sur le nom du modèle donne plus de détails et s'il est compatible.

Ainsi, vous pouvez vérifier si les éléments matériels les plus importants de votre laptop sont supportés : l'adaptateur Wi-Fi, le contrôleur USB, l'interface Bluetooth, la webcam, le lecteur d'empreintes digitales...

Je peux voir que l'adaptateur Wi-Fi de mon ordinateur portable n'est pas compatible avec le logiciel *libre* :

{{ image_figure(src="img/h-node-lspci-search-result.fr.webp", raw_path=true, alt="Le résultat de recherche lspci de h-node montrant que mon adaptateur Wi-Fi n'est pas compatible avec le logiciel libre") }}

Toutefois, j'ai un adaptateur USB Ethernet qui l'est :

{{ image_figure(src="img/h-node-lsusb-search-result.fr.webp", raw_path=true, alt="Le résultat de recherche lsusb de h-node montrant que mon adaptateur USB Ethernet est compatible avec le logiciel libre") }}

Comme vous pouvez le voir, nous devrions penser au logiciel *libre* au moment de l'achat de nouveau matériel.
Comme la situation continue d'évoluer chaque jour, il n'y a pas de recette miracle pour savoir comment procéder.

Si vous cherchez un nouvel ordinateur portable ou du nouveau matériel, le [programme Respect Your Freedom](https://ryf.fsf.org/) vaut la peine d'être considéré.
Cela pourrait être un bon point de départ, même si la liste des ordinateurs portables est plutôt limitée pour le moment.
La liste des vendeurs peut être utilisée pour regarder plus de produits et passer à d'autres vendeurs similaires.

[Minifree Ltd](https://minifree.org/), pas listé ici au moment d'écrire ces lignes, vaut la peine d'être nommé, même si je n'ai personnellement jamais utilisé aucun de leurs produits.
Des sociétés, comme Purism (<https://puri.sm/>), ont le logiciel *libre* au coeur de leur modèle économique.
C'est juste un exemple car je ne suis pas client non plus et il doit y en avoir d'autres.

Une autre façon de faire peut être de commencer avec la liste des systèmes d'exploitation *libre*s fournie par le GNU : <https://www.gnu.org/distros/free-distros.fr.html>.
A partir de là, il est possible de parcourir Internet avec votre moteur de recherche favori et de chercher des retours récents de personnes à propos du système d'exploitation qui vous intéresse et du matériel sur lequel il a été installé.

[^1]: Guix adhère strictement à la "Free Software Definition".  
Vous pouvez en savoir plus sur ses [limitations](https://guix.gnu.org/manual/fr/html_node/Limitations.html) et [considérations matérielles](https://guix.gnu.org/manual/fr/html_node/Considerations-materielles.html).
[^2]: <https://www.gnu.org/gnu/gnu.fr.html>
[^3]: <https://www.gnu.org/philosophy/open-source-misses-the-point.fr.html>
