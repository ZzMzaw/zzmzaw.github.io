+++
title = "Une config d'OS pas si mal sécurisée"
sort_by = "slug"
template = "series.html"
insert_anchor_links = "left"

description = "Cette série décrit comment je suis passé de Qubes OS à un tout autre OS tout en essayant de maintenir un maximum de priorités de sécurité."

transparent = true

[extra]
series = true

series_template_placeholders = ["$POSITION", "$PREV_ACHIEVEMENT", "$CURR_OBJECTIVE", "$CURR_ACHIEVEMENT", "$NEXT_OBJECTIVE"]

[extra.series_intro_templates]
default = """
<div>
  <details>
    <summary>Cet article est le $POSITION de la série: $SERIES_HTML_LINK</summary>
    $SERIES_PAGES_OLIST
  </details>
</div>
"""
has_prev = """
<div>
  <details>
    <summary>Cet article est le $POSITION de la série: $SERIES_HTML_LINK</summary>
    $SERIES_PAGES_OLIST
  </details>
</div>

Dans <a href="$PREV_PERMALINK" aria_label="$PREV_TITLE">le précédent article de cette série</a>, $PREV_ACHIEVEMENT.

Maintenant, nous allons voir $CURR_OBJECTIVE.
"""

[extra.series_outro_templates]
has_next = """
Maintenant que $CURR_ACHIEVEMENT, voyons $NEXT_OBJECTIVE dans <a href="$NEXT_PERMALINK" aria_label="$NEXT_TITLE">le prochain article de cette série</a>.
"""
+++

Depuis plusieurs années maintenant, j'utilise Qubes OS (<https://www.qubes-os.org/>).
C'est un excellent système d'exploitation (OS pour Operating System en anglais) généraliste, l'un des plus sécurisés qu'il m'ait été donné d'essayer et un que j'ai apprécié utiliser tous les jours pendant toutes ces années.
Il convient parfaitement à mes besoins de base comme la navigation sur Internet ou le développement logiciel.
Jouer à des jeux vidéo peut se révéler un peu plus complexe, mais je n'en ai pas eu besoin.

Quoi qu'il en soit, utiliser Qubes OS quand on a un besoin de sécurité fort est une évidence pour moi.

Après l'avoir utilisé pendant un moment, j'ai réalisé que c'était peut-être un peu trop pour répondre à mon modèle de menaces actuel au regard des contraintes qu'il impose.
Les plus importantes contraintes pour moi sont la consommation mémoire et le fait qu'il soit compliqué de générer et de configurer mes qubes [^1] de manière déclarative.

Concernant la première contrainte, je fais généralement plusieurs choses en même temps, comme regarder mes comptes bancaires tout en cherchant des choses sur Internet pour la maison pendant une session de développement qui n'en finit pas.
Comme tout cela implique des contextes de sécurité différents, ça nécessite des qubes différents et j'ai souvent besoin d'arrêter un qube pour pouvoir en démarrer un autre dû au manque de mémoire.
8G de RAM n'est définitivement pas suffisant pour des besoins basiques.

Concernant la seconde contrainte, j'ai essayé de jouer avec SaltStack qui est nativement intégré Qubes OS.
Toutefois, il a des limitations comme la nécessité de séparer la création d'un qube de sa configuration et une réutilisabilité limitée, il implique d'interagir avec dom0 [^2] et la plupart des packages sont liés à Fedora ou Debian pour la partie système d'exploitation.
De plus, il nécessite pas mal de maintenance, les générations de qubes sont complexes à reproduire dans le temps et les bugs logiciels sont difficiles à fixer dans la mesure où dom0 évolue lentement par conception [^3].

L'objectif de cette série est de trouver une manière déclarative et reproductible de configurer un ordinateur portable avec un Linux plutôt sécurisé.

Soyons honnêtes, c'est aussi un prétexte pour jouer un peu plus avec les systèmes d'exploitation en source ouverte du moment.

Je ne pense pas être en mesure de mitiger autant de risques que le peut Qubes OS, mais il se peut que je n'aie pas un besoin impératif de ces protections non plus.

[^1]: Un qube est un compartiment isolé dans Qubes OS, actuellement implémenté en tant que machine virtuelle dans un environnement virtualisé basé sur Xen.
[^2]: dom0 est l'élément de confiance de plus haut niveau dans Qubes OS et il est préférable de ne pas trop y mettre le bazar. Compromettre dom0 revient à compromettre le système complet.
[^3]: Vu que dom0 est l'élément de confiance de plus haut niveau dans Qubes OS, des mises à jour fréquentes de son OS peuvent être une menace et dom0 va généralement s'appuyer sur une vieille version de Fedora (voir [la documentation de Qubes OS](https://www.qubes-os.org/doc/supported-releases/#note-on-dom0-and-eol) pour plus de détail)
