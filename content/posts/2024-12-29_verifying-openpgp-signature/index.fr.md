+++
title = "Vérifier la signature OpenPGP de fichiers téléchargés"
description = "Comment vérifier une signature OpenPGP et pourquoi c'est important"

[taxonomies]
tags = ["sécurité"]

[extra]
mermaid = true
+++

Quand un fichier important est disponible au téléchargement, il y a généralement un fichier de signature pas très loin.

Si ce n'est pas le cas, vous devriez y réfléchir à deux fois avant d'utiliser le fichier que vous venez de télécharger.

Quoi qu'il en soit, si une signature est disponible, vous devriez prendre le temps de la vérifier.

# Pourquoi vérifier la signature d'un fichier téléchargé ?

Nous pouvons considérer qu'il y a plusieurs actions et acteurs lorsqu'un fichier est rendu disponible au téléchargement et téléchargé :

{% mermaid() %}
sequenceDiagram
    actor provider as Fournisseur du fichier
    participant storage as Stockage du fichier
    actor user as Utilisateur du fichier
    provider ->> provider: Génère le fichier
    provider ->> storage: Rend le fichier disponible au téléchargement
    activate storage
    user ->> storage: Télécharge le fichier
    note right of storage: Le fichier peut rester dans le stockage pour un long moment
    deactivate storage
{% end %}

Comment savez-vous que le fichier téléchargé est celui qui a été généré ?
Peut-être que quelqu'un a eu accès au stockage et l'a modifié.
Peut-être que quelqu'un est capable de le modifier à la volée pendant que vous le téléchargez.

Vérifier la signature est important car c'est le seul moyen de s'assurer que le fichier n'a pas été altéré depuis qu'il a été signé, ni dans son stockage, ni pendant son transfert, quand vous l'avez téléchargé.

Afin de permettre la vérification de la signature, une signature du fichier doit être créée avec une clé privée appartenant au fournisseur du fichier, juste après que le fichier a été généré.
Cette opération prend généralement place dans un endroit sûr (aussi sûr que possible) avant que le fichier ne soit envoyé à son stockage, où il pourrait rester sans surveillance pour un très long moment.

{% mermaid() %}
sequenceDiagram
    actor provider as Fournisseur du fichier
    participant storage as Stockage du fichier
    actor user as Utilisateur du fichier
    provider ->> provider: Génère le fichier
    provider ->> provider: Signe le fichier
    provider ->>+ storage: Rend le fichier disponible au téléchargement
    activate storage
    provider ->> storage: Rend la signature disponible au téléchargement
    user ->> storage: Télécharge le fichier
    user ->> storage: Télécharge la signature
    user ->> user: Vérifie la signature
    deactivate storage
{% end %}

Évidemment, la vérification de la signature n'empêche pas que le fichier puisse être empoisonné avant ou pendant sa génération ou sa signature.
Cela n'empêche pas non plus une mauvaise utilisation ou un abus de la clé privée.

Toutefois, il s'agit là de menaces actives qui se produisent à un moment où le fournisseur du fichier est habituellement attentif au fichier et cela devrait être couvert par la confiance que vous avez envers le fournisseur du fichier.

# Comment fonctionne une signature numérique ?

La signature et la vérification de la signature s'appuient sur la cryptographie asymétrique [^1] et le hachage cryptographique [^2].
Cela s'applique non seulement à un fichier, mais aussi à n'importe quel type de données.

"Cryptographie asymétrique" signifie juste que nous utilisons une paire de clés, une clé privée et une clé publique, pour exécuter les opérations cryptographiques.
Je ne vais pas rentrer dans trop de détails ici, mais il vous faut juste garder à l'esprit que la cryptographie asymétrique s'appuie sur le fait qu'il n'y a pas de moyen de calculer ou de deviner la clé privée à partir de la clé publique. 
La clé privée doit être gardée secrète et utilisée par seulement une personne (ou un petit groupe) et la clé publique est... publique, et peut être partagée à n'importe qui.

Le hachage cryptographique peut être défini comme une fonction unidirectionnelle qui transforme n'importe quelle entrée en une sortie unique de longueur fixe.
Cela s'appuie sur l'unicité de la sortie, quelle que soit l'entrée, et sur le fait que la fonction ne peut pas être inversée (vous ne pouvez pas calculer ou deviner l'entrée depuis la sortie).
Si des collisions existent (i.e. deux entrées différentes génèrent la même sortie) alors la fonction de hachage est considérée faible et ne devrait plus être utilisée.

Le processus de signer de la donnée implique généralement de la hacher et de chiffre le résultat avec la clé privée pour créer la signature, puis de fournir la signature aux côtés de la donnée, d'une manière ou d'une autre.

{% mermaid() %}
flowchart LR
    e_data@{ label: "Donnée", shape: in-out }
    e_privkey@{ label: "Clé privée", shape: in-out }
    e_hash@{ label: "Empreinte", shape: in-out }
    e_sign@{ label: "Signature", shape: in-out }
    a_hash@{ label: "Hacher la donnée", shape: process }
    a_encrypt@{ label: "Chiffrer l'empreinte", shape: process }
    a_store@{ label: "Associer ensemble la donnée et la signature", shape: double-circle }
    
    e_data --> a_hash --> e_hash
    e_hash --> a_encrypt --> e_sign
    e_privkey ----> a_encrypt
    e_data --> a_store
    e_sign --> a_store
{% end %}

La signature de la donnée peut être vérifiée en récupérant à la fois la signature et la donnée, en déchiffrant l'empreinte depuis la signature avec la clé publique et en hachant la donnée à nouveau.
La signature est considérée valide si les deux empreintes correspondent.

{% mermaid() %}
flowchart LR
    e_data@{ label: "Donnée", shape: in-out }
    e_sign@{ label: "Signature", shape: in-out }
    e_pubkey@{ label: "Clé publique", shape: in-out }
    e_hash_data@{ label: "Empreinte depuis la donnée", shape: in-out }
    e_hash_sig@{ label: "Empreinte depuis la signature", shape: in-out }
    a_hash@{ label: "Hacher la donnée", shape: process }
    a_decrypt@{ label: "Déchiffrer la signature", shape: process }
    a_compare{Comparer les empreintes}
    s_sign_ok@{ label: "&nbsp;La signature est **valide**&nbsp;", shape: double-circle }
    s_sign_ko@{ label: "La signature est **invalide**", shape: double-circle }
    
    e_data --> a_hash --> e_hash_data
    e_sign --> a_decrypt --> e_hash_sig
    e_pubkey --> a_decrypt
    e_hash_data --> a_compare
    e_hash_sig --> a_compare
    a_compare -- Égal --> s_sign_ok
    a_compare -- Pas égal --> s_sign_ko
{% end %}

# Vérifier la signature OpenPGP d'un fichier téléchargé

PGP [^3] signifie Pretty Good Privacy (qu'on peut traduire par "Plutôt bon niveau de confidentialité") et a donné naissance au standard OpenPGP [^4] qui est largement utilisé pour signer et/ou chiffrer des e-mails et des fichiers.

Vérifie la signature PGP d'un fichier téléchargé implique généralement GNU Privacy Guard [^5], plus connu sous le nom de `gpg` (même s'il pourrait y avoir d'autres méthodes).

Sur Linux, c'est aussi simple que d'exécuter l'une des commandes suivantes, en partant du principe que le fichier et la signature sont dans le même dossier (e.g. `/path/to/the/files`) :
{% wide_container() %}
```bash
cd /path/to/the/files
# Faisant l'hypothèse que la signature a le même nom que le fichier signé
# avec une extension en plus (e.g. `.sig`, `.asc`...)
gpg --verify '<your-signature-file>'
# Si le nom du fichier ne peut pas être inféré depuis le nom de la signature
gpg --verify '<your-signature-file>' '<your-signed-file>'
```
{% end %}

Si vous essayez de vérifier la signature d'un fichier sans importer la clé publique associée, `gpg` lève une erreur `Can't check signature: No public key` :
{% wide_container() %}
```bash
gpg --verify '<your-signature-file>'
#> gpg: assuming signed data in '<your-signed-file>'
#> gpg: Signature made <signature-date>
#> gpg:                using RSA key XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
#> gpg: Can't check signature: No public key
```
{% end %}

Rien n'a été vérifié ici, et vous devez donc d'abord importer la clé publique associée à la signature.

Afin d'importer la clé, vous devez d'abord savoir où vous pouvez la récupérer.
Cette information est normalement fournie avec le fichier et sa signature ou dans la documentation d'installation du fichier.

Une fois identifiée, télécharger et importer la clé est aussi simple qu'exécuter la commande suivante :
{% wide_container() %}
```bash
# Télécharge la clé et l'importe en une seule commande
wget '<remote-key-location' -q0 - | gpg --import -
#> gpg: key YYYYYYYYYYYYYYYY: Z signatures not checked due to missing keys
#> gpg: key YYYYYYYYYYYYYYYY: public key "<key-owner>" imported
#> gpg: Total number processed: 1
#> gpg:               imported: 1
#> gpg: no ultimately trusted keys found
```
{% end %}

Mais comment pouvez-vous être sûr que la clé que vous avez importée est bien celle que vous vouliez ?

Les clés peuvent être imbriquées (comme les Autorités de Certification utilisées pour le HTTPS) et vous pouvez signer avec une autre clé.
Si vous voulez faire réellement confiance à une clé, vous devriez commencer par faire confiance à sa clé parente d'abord.
Ici, nous importons directement la clé dont nous avons besoin, ce qui explique pourquoi nous avons le message `no ultimately trusted keys found`.
Si nous avions importé et fait confiance à la clé racine et importé toutes les clés intermédiaires pour la clé qui a signé le fichier (si tant est qu'il y en ait), ce message ne serait pas là.

La plupart du temps, la clé que vous importez est autonome comme l'est une clé racine (i.e. qui n'a pas de parent).
Dans ce cas, la seule chose que vous pouvez faire est de comparer l'empreinte de clé avec celle qui est fournie (quand elle l'est) avant de lui faire confiance.

Vous pouvez aussi chercher d'autres sources de la clé publique pour comparer les empreintes.
D'habitude, les développeurs ont tendance à mettre leur clé publique dans plusieurs endroits indépendants (profils publics, dépôts, sites web ...) donc il devrait être possible de comparer plusieurs empreintes pour résister un peu plus à une altération éventuelle de la clé publique.

Vous pouvez aussi vérifier l'empreinte d'une clé publique sans l'importer en la téléchargeant et en affichant son contenu :
{% wide_container() %}
```bash
wget '<remote-key-location>' -q0 -O /path/to/local/key
# L'empreinte de la clé est sur la seconde ligne, un chaîne de caractères composée de
# 40 caractères alphanumériques (tous remplacés par des X ici)
gpg --show-keys /path/to/local/key
#> pub   <pub-key-algorithm> <pub-key-generation-date> [<pub-key-usages>] [expires: <pub-key-expiration-date>]
#>       XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
#> uid                      <uid1>
#> uid                      <uid2>
#> uid                      ...
#> sub   <sub-key-algorithm> <sub-key-generation-date> [<sub-key-usages>]
```
{% end %}

Si vous considérez la clé comme étant de confiance, vous pouvez alors l'importer avec la commande suivante :
{% wide_container() %}
```bash
gpg --import /path/to/local/key
```
{% end %}

Enfin et surtout, vous pouvez récupérer l'empreinte de toutes les clés déjà importées avec la commande suivante :
{% wide_container() %}
```bash
# L'empreinte de la clé est sur la seconde ligne, un chaîne de caractères composée de
# 40 caractères alphanumériques (tous remplacés par des X ici)
gpg --fingerprint
#> /path/to/keyring
#> ----------------
#> pub   <pub-key-algorithm> <pub-key-generation-date> [<pub-key-usages>] [expires: <pub-key-expiration-date>]
#>       XXXX XXXX XXXX XXXX XXXX  XXXX XXXX XXXX XXXX XXXX
#> uid           [uid1-trust] <uid1>
#> uid           [uid2-trust] <uid2>
#> uid           ...
#> sub   <sub-key-algorithm> <sub-key-generation-date> [<sub-key-usages>]
#> ... même chose pour les autres clés ...
```
{% end %}

PGP vise à instaurer la confiance dans un monde numérique.
`gpg` est capable d'adapter son comportement selon le niveau de confiance que vous avez déclaré sur chaque clé importée.

Je ne vais pas rentrer dans les détails de cette fonctionnalité, car cela ne devrait pas être utile dans une unique vérification de signature pour un fichier, à moins que vous prévoyiez de le faire régulièrement.

Si vous voulez approfondir ce sujet, vous pouvez regarder [les explications et les instructions fournies par Qubes OS concernant la vérification de signature](https://www.qubes-os.org/security/verifying-signatures/) qui utilise des clés imbriquées et explique comment faire confiance à une clé.

Maintenant que les clés sont importées, vérifions le fichier à nouveau.

Si la signature est valide, nous avons un message de succès `Good signature` :
{% wide_container() %}
```bash
gpg --verify '<your-signature-file>'
#> gpg: assuming signed data in '<your-signed-file>'
#> gpg: Signature made <signature-date>
#> gpg:                using RSA key XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
#> gpg: Good signature from <uid1> [<uid1-trust]
#> gpg:                 aka <uid2> [<uid2-trust]
#> gpg:                 aka ...
#> gpg: WARNING: This key is not certified with a trusted signature!
#> gpg:          There is no indication that the signature belongs to the owner.
#> Primary key fingerprint: XXXX XXXX XXXX XXXX XXXX  XXXX XXXX XXXX XXXX XXXX
```
{% end %}

Dans le cas contraire, nous avons un message d'erreur `BAD signature` :
{% wide_container() %}
```bash
#> gpg: assuming signed data in '<your-signed-file>'
#> gpg: Signature made <signature-date>
#> gpg:                using RSA key XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
#> gpg: BAD signature from <uid1> [<uid1-trust>]
```
{% end %}

Si la signature est valide, vous pouvez utiliser le fichier en toute sécurité.
Mais si ce n'est pas le cas, je vous conseille vivement de le supprimer.
Vous pouvez essayer de le télécharger et de le vérifier à nouveau, mais si la mauvaise signature persiste, vous devriez contacter le fournisseur du fichier avant d'aller plus loin.

[^1]: <https://fr.wikipedia.org/wiki/Cryptographie_asymétrique>
[^2]: <https://fr.wikipedia.org/wiki/Fonction_de_hachage_cryptographique>
[^3]: <https://fr.wikipedia.org/wiki/Pretty_Good_Privacy>
[^4]: <https://datatracker.ietf.org/doc/html/rfc9580>
[^5]: <https://fr.wikipedia.org/wiki/GNU_Privacy_Guard>
