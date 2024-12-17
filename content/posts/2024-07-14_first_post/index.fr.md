+++
title = "Mon tout premier post"
updated = 2024-12-17
description = "Pourquoi Zola, tabi et le reste"

[taxonomies]
tags = ["blog", "zola", "tabi"]
+++

Ceci est mon tout premier post en utilisant Zola (<https://www.getzola.org/>) avec tabi (<https://welpo.github.io/tabi/>) comme thème.

Comme tous ceux qui commencent un nouveau blog en utilisant un SSG [^1], j'allais écrire mon premier post sur comment je l'avais mis en place

Cependant, presque tout est déjà documenté :
- Comment installer Zola :\
<https://www.getzola.org/documentation/getting-started/installation/>
- Comment configurer Zola :\
<https://www.getzola.org/documentation/getting-started/configuration/>
- Comment installer un thème dans Zola :\
<https://www.getzola.org/documentation/themes/installing-and-using-themes/>
- Comment configurer tabi :\
<https://welpo.github.io/tabi/blog/mastering-tabi-settings/>
- Comment héberger un site fait avec Zola sur GitHub Pages :\
<https://www.getzola.org/documentation/deployment/github-pages/>

Toutes les informations ci-dessus font qu'il est plutôt facile de mettre en place un site comme celui que vous êtes en train de lire.

Au lieu de réécrire ce qui est déjà bien écrit, je vais juste expliquer comment j'ai fini par utiliser Zola et tabi.

J'apprécie beaucoup le langage de programmation Rust [^2] et j'ai décidé de regarder s'il existait un SSG écrit en Rust qui soit FOSS [^3].

Zola a été le premier résultat de ma recherche.
Il est non seulement FOSS, mais possède aussi toutes les fonctionnalités que je recherche : multilingue, coloration syntaxique, tags et shortcodes.

Il y a quelques années, j'ai joué avec Hugo (<https://gohugo.io/>) et j'ai essayé de créer mon propre thème.
Ça m'a pris tellement de temps que j'ai fini par abandonner à la fois le thème et le blog...

Pour éviter de faire deux fois la même erreur, j'ai donc commencé à regarder les thèmes existants dans Zola.

Je cherchais un thème minimaliste, correctement documenté, capable de fonctionner sans javascript et, a minima, conscient des enjeux de sécurité.

tabi coche toutes ces cases et bien plus encore.
Qui plus est, son mainteneur est vraiment ouvert aux contributions et j'ai beaucoup apprécié la première que j'ai faite.

Quoi qu'il en soit, c'est le premier pas d'une nouvelle aventure où je vais essayer de documenter ce que je fais à la maison, à la fois en français et en anglais.

Pour ceux qui ne lisent que le français, je ne peux que vous conseiller d'apprendre l'anglais, car un grand nombre de ressources utiles sur Internet sont en anglais.
Ce blog est écrit en français et en anglais, mais si vous voulez utiliser ses traductions pour passer d'une langue à l'autre, n'oubliez pas qu'elles ne sont pas littérales et qu'elles sont loin d'être dépourvues d'erreurs (de syntaxe, de grammaire ou d'orthographe).
Toutefois, si ça peut vous mettre le pied à l'étrier, n'hésitez pas.

À bientôt...

[^1]: **SSG** signifie **S**tatic **S**ite **G**enerator, un logiciel qui génère des pages web en HTML/JS/CSS à partir de texte. Ces pages peuvent être hébergées n'importe où sans avoir besoin de quoi que ce soit d'autre côté serveur.
[^2]: <https://www.rust-lang.org/>
[^3]: **FOSS** signifie **F**ree and **O**pen **S**ource **S**oftware qui caractérise un logiciel libre et en source ouverte
