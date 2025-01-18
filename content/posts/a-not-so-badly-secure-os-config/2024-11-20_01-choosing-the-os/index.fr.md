+++
title = "Choisir un OS en fonction de ses attentes et des menaces de sécurité"
description = "Une définition succincte de mes attentes et menaces en matière de systèmes d'exploitation et le choix de celui qui pourrait y répondre le mieux, tout du moins sur le papier"

[taxonomies]
tags = ["os", "guix", "nix", "sécurité"]

[extra.series_template_variables]
position = "premier"
curr_achievement = "nous avons choisi Guix System comme nouveau système d'exploitation"
next_objective = "comment se préparer à l'installer"
+++

Je prévois de passer à un autre système d'exploitation (OS pour Operating System en anglais) dans l'objectif de simplifier ma vie numérique quotidienne qui consiste principalement à surfer sur Internet, à développer et à jouer avec des défis de sécurité (challenges en anglais).
En plus de cela, il doit également limiter les risques correspondant à mon modèle de menace.

Je vais essayer de décrire clairement ce que j'attends d'un tel système et le type de menaces auxquelles je fais face de manière à m'assurer qu'ils soient tous couverts, autant que possible.

Je tiens à souligner qu'il ne s'agit pas ici d'une comparaison complète et équitable de tous les systèmes d'exploitation existants.
Cette analyse est adaptée à ma situation personnelle et la vôtre pourrait et devrait être différente.
Je conseille donc d'utiliser cet article comme un support pour vous aider à identifier vos propres attentes et menaces que comme une liste concrète à suivre.

Enfin et surtout, cette comparaison succincte est basée sur des informations collectées depuis Internet.
Je n'ai encore fait aucune expérimentation au moment où j'écris ces lignes et le résultat pourrait être très loin des attentes décrites ici.

# Quelles sont mes attentes ?

## Une agréable expérience développeur

Je passe beaucoup de temps à développer, donc le système doit permettre unei excellente expérience en tant que développeur, incluant pour du mobile.
Être capable de me connecter avec un téléphone Android est indispensable afin de garder le mien à jour.

Je veux que ce système améliore ma productivité au niveau du développement de manière fiable, reproductible et durable.

Enfin, être capable de tester de nouveaux outils, de nouvelles bibliothèques ou de nouvelles façons de travailler doit être rendu facile sans mettre à risque le système entier.
Pouvoir se débarrasser de tout ce qui ne m'est pas utile est indispensable.

## Facile à maintenir à jour

Je veux être capable de garder le système en état de fonctionnement jusqu'à pouvoir investir un peu de temps pour gérer proprement une mise à jour ou pouvoir mettre à jour une partie de ce dernier sans être obligé de regarder l'ensemble du système.

Si le système me force à mettre à jour, installer et tester à nouveau tout quand je n'en ai pas le temps, je vais finir par faire des choses vite et mal faites pour utiliser le système avec un minimum de fonctionnalités.
Plus je l'utilise dans cet état, moins je suis motivé pour prendre le temps de le mettre à jour et je finis par penser comment complètement le remplacer avec quelque chose de mieux.

## Complètement configurable de manière déclarative avec des fichiers texte

Je n'aime pas les interfaces graphiques... tout du moins, pour configurer mon système d'exploitation.
Je veux être en capacité d'écrire dans des fichiers texte ce que j'attends de mon système et le laisser se configurer tout seul pour atteindre l'état désiré correctement.
L'avantage des fichiers texte est qu'ils sont faciles à stocker, faciles à sauvegarder, faciles à partager, faciles à comparer et leur génération peut être facilement automatisée.

Cerise sur le gâteau, si la configuration du système est reproductible, cela m'assurerait que je peux tout reconstruire à partir de zéro sans souci.

## Faciliter la compilation de logiciels depuis leur code source, mais seulement quand c'est nécessaire

Compiler des logiciels depuis leur code source peut prendre énormément de temps et devenir un cauchemar.
Avoir une construction incrémentale et pouvoir s'appuyer sur un cache pour les fichiers compilés est donc indispensable.
Pouvoir héberger ces caches localement est encore mieux même si cela nécessite suffisamment de maturité pour les gérer (ce qui ne sera pas le cas au début).

Cependant, de temps en temps, pour une configuration inhabituelle ou une optimisation particulière, compiler depuis le code source peut être vraiment utile.

## Aussi libre et en source ouverte que possible

Je suis complètement en phase avec la "Définition du Logiciel Libre" [^1] et je suis d'accord que le fait d'être en source ouverte n'est pas toujours équivalent au fait d'être libre.
Toutefois, je reconnais aussi que je ne suis pas en capacité, aussi au niveau du temps investi qu'au niveau financier, d'éviter d'utiliser des logiciels non libres.

Ma situation actuelle est que je fais de mon mieux pour utiliser des logiciels libres à chaque fois que c'est possible et, a minima, des logiciels en source ouverte.
Il peut m'arriver d'utiliser des logiciels en source fermée dans des endroits où je ne suis pas confiant d'intervenir (e.g. la séquence de boot, le driver de la carte graphique...).
Je me sentirai, un jour peut-être, suffisamment confiant pour investir plus de temps et d'énergie dans le fait de me débarrasser de tous les logiciels non libres que j'utilise.

# Un modèle de menaces simple

Une menace est un événement indésirable potentiel ou actuel qui peut être malicieux (comme une attaque par ransomware) ou accidentel (comme une panne d'ordinateur).

Dans mon cas, le modèle de menaces sera contextualisé à ce qui peut arriver à mon ordinateur portable personnel, vivant dans un endroit où je ne ressens aucun risque pour ma vie si je ne parviens pas à le sécuriser correctement.

Cela doit être gardé à l'esprit non seulement en regardant ce modèle, mais aussi en appliquant la configuration que je vais proposer car cela pourrait ne pas convenir à votre propre situation.
Cependant, je vais essayer d'amener le niveau de sécurité au-dessus de ce dont j'ai besoin par rapport à cette analyse à chaque fois que c'est possible et que ça reste utilisable par rapport à mon ressenti personnel.

## Indisponibilité à cause d'une panne ou de la destruction de l'ordinateur portable

Un ordinateur peut casser n'importe quand, que ce soit dû à l'usure normale ou à un accident.

Dans la mesure où je suis concentré sur l'ordinateur portable en tant que tel, je pars du principe que les données qu'il contient sont correctement répliquées ailleurs et que je suis capable d'y accéder sans lui.
Je vais essayer de couvrir le sujet de la sauvegarde et de la restauration de données dans le futur, mais cela va au-delà du sujet de cette série.

Quoi qu'il en soit, si mon ordinateur portable devient impossible à utiliser, je veux pouvoir le réinstaller facilement dans l'état dans lequel il était une fois réparé (ou remplacé).

## Divulgation d'information à cause d'un vol de l'ordinateur portable

Un ordinateur portable est considéré comme un objet de valeur et peut donc être la cible d'un vol.

Si cela arrive, je veux faire en sorte que ce soit impossible ou au moins très difficile d'accéder aux données qu'il contient.

## Compromission du système à cause de la navigation sur Internet

Je passe beaucoup de temps sur Internet et je développe à chaque fois que je peux. 
Il y a toujours un risque de compromission soit par la navigation elle-même, soit en téléchargeant de mauvaises choses (binaires, code, dépendances).

Si cela arrive, je veux que la compromission soit limitée à la session de navigation, avec aussi peu de possibilités de persistance après un redémarrage que possible.
De plus, la compromission devrait être limitée à un contexte particulier (e.g. la session de développement) sans pouvoir s'étendre facilement à l'ensemble du système.

# Choisir mon nouveau système d'exploitation

Du fait que je voulais configurer mon système d'exploitation de manière déclarative et reproductible, je suis rapidement arrivé aux deux suivants :

- NixOS (<https://nixos.org/>)
- Guix System (<https://guix.gnu.org>)

Ces deux systèmes d'exploitation sont très similaires.
Ils s'appuient tous les deux sur un gestionnaire de paquets déclaratif pour faire en sorte que la configuration du système soit fiable, réversible et reproductible :

- Le gestionnaire de paquets de NixOs est nix, s'appuyant sur le langage nix
- Le gestionnaire de paquets de Guix System est guix, s'appuyant sur le langage Guile Scheme

Vu qu'il y a déjà beaucoup de comparaison entre ces deux systèmes d'exploitation [^2], je vais juste expliquer pourquoi j'ai choisi Guix System par rapport à NixOs.
Ce choix est principalement subjectif car les deux sont d'excellents systèmes d'exploitation.
Il faut parfois simplement suivre son instinct.

Tout d'abord, Guile Scheme paraît plus propre que nix.
C'est juste mon goût personnel, mais vu que toute la configuration sera faite en utilisant l'un de ces langages, c'est mieux de se sentir à l'aise avec.

Nix possède beaucoup plus de paquets que Guix et ils sont plus à jour.
Toutefois, comme la majorité de mon équipement se base sur Rust, je m'attends à ce que ce ne soit pas trop compliqué de le compiler (mais je peux me tromper complètement).
In addition, it seems possible to use nix from Guix System which would allow to take advantage of huge nix package repository.
De plus, il semble possible d'utiliser nix depuis Guix System ce qui permettrait de bénéficier de l'énorme dépôt de paquets de nix.
La réciproque semble vraie même si elle présente peut-être moins d'intérêt.

Les deux possèdent des caches pour les binaires qui pourraient permettre de précompiler les logiciels que j'utilise.

Guix paraît plus unifié et intégré.
Cela peut être dû à la communauté plus petite ou au rythme d'évolution plus lent, mais ça me rend confiant dans ma capacité à suivre et à bouger seulement quand je le veux.
Je ne sais pas si c'est mieux ou pas.
Être unifié est intéressant si suffisamment de choses sont couvertes pour être utiles.
Je pars du principe que c'est le cas pour Guix System.

Bien sûr, j'ai un peu peur de devoir packager trop de logiciels par moi-même, en particulier des logiciels non libres dont mon ordinateur portable actuel pourrait avoir besoin.

Mais je pense que je peux donner une chance à Guix System et me replier sur NixOS si cela me prend trop de temps.

Qui plus est, si NixOS se révèle également peu pratique pour moi, j'ai identifié CachyOS (<https://cachyos.org/>) qui est basé sur Arch Linux et devrait être plus rapide à configurer.
Même si sa configuration n'est pas déclarative, il semble que la sécurité soit une de ses fonctionnalités principales avec la performance.

Avant de terminer, je voudrais mentionner RedoxOS (<https://www.redox-os.org/>).
C'est un tout nouveau système d'exploitation généraliste avec une architecture micro-noyau et activement développé.
Malheureusement, il n'est pas encore assez mature pour une utilisation en production.
Si sa configuration est rendue facile ou, encore mieux, déclarative (on peut toujours rêver un peu), cela pourrait devenir un système d'exploitation très intéressant à regarder dans le futur.

[^1]: <https://www.gnu.org/philosophy/free-sw.fr.html>
[^2]: J'ai identifié quelques articles pour commencer.
      Par contre, c'est en anglais.
      Il n'y a pas d'ordre particulier et c'est incomplet, donc utilisez simplement votre moteur de recherche préféré.
      - <https://forum.systemcrafters.net/t/nixos-vs-guix-a-non-programmers-novice-perspective/875>
      - <https://lwn.net/Articles/962788/>
      - <http://www.willghatch.net/blog/2020/06/27/nixos-the-good-the-bad-and-the-ugly/>
      - <https://www.reddit.com/r/NixOS/comments/1d2s6r1/why_nixos_won_over_guix/>
      - <https://news.ycombinator.com/item?id=32927469>
      - <https://www.youtube.com/watch?v=PiAMRXYIri0>
      - <https://doronbehar.com/articles/why-I-chose-NixOS/>
      - <https://gist.github.com/abcdw/e54807b0a25e61fe2cf1bf8991410f83> 