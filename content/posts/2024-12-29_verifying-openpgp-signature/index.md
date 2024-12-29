+++
title = "Verifying the OpenPGP signature of dowloaded files"
description = "How to verify an OpenPGP signature and why it is important"

[taxonomies]
tags = ["security"]

[extra]
mermaid = true
+++

When an important file is available for download, there is usually a signature file not far away from it.

If it is not the case, you should think twice before using the file you just downloaded.

Anyway, when a signature is made available, you should take the time to verify it.

# Why verifying the signature of a downloaded file?

We can consider there are multiple actions and actors when a file is made available for download and downloaded:

{% mermaid() %}
sequenceDiagram
    actor provider as File Provider
    participant storage as File Storage
    actor user as File User
    provider ->> provider: Generate the file
    provider ->> storage: Make the file available for download
    activate storage
    user ->> storage: Download the file
    note right of storage: File may stay in the storage for a very long time
    deactivate storage
{% end %}

How do you know that the file you downloaded is the one which has been generated?
Maybe someone accessed the storage and modified it.
Maybe someone is able to modify it on the fly while you download it.

Verifying the signature is important because this is the only way to make sure the file has not been tampered with since it was signed, neither in its storage nor during its transfer, when you downloaded it.

In order to allow signature verification, a signature of the file is created with a private key owned by the file provider just after the file has been generated.
This operation usually happens in a safe place (at least as safe as possible) before the file is uploaded to the file storage, where it may stay unmonitored for a very long time.

{% mermaid() %}
sequenceDiagram
    actor provider as File Provider
    participant storage as File Storage
    actor user as File User
    provider ->> provider: Generate the file
    provider ->> provider: Sign the file
    provider ->>+ storage: Make the file available for download
    activate storage
    provider ->> storage: Make the signature available for download
    user ->> storage: Download the file
    user ->> storage: Download the signature
    user ->> user: Verify the signature
    deactivate storage
{% end %}

For sure, signature verification doesn't prevent the file to be poisoned before or during its generation or signature.
It doesn't prevent neither private key misuse nor abuse.

Nevertheless, those are all active threats which happen at a time when the file provider is usually careful about the file and this should be covered by the trust you have in the file provider.

# How does digital signature work?

Signature and signature verification rely on asymmetric cryptography [^1] and cryptographic hashes [^2].
It applies not only to a file but to any kind of data.

"Asymmetric cryptography" just means we're using a key pair, a private key and a public key, to perform cryptographic operations.
I won't go into too many details here, but you just have to keep in mind that asymmetric cryptography relies on the assumption that there is no way to compute or guess the private key from the public key.
The private key must be kept secret and used by only one person (or a small group) and the public key is... public and can be shared to anyone.

Cryptographic hash can be defined as a one-way function which transforms any input in a fixed-length unique output.
It relies on output uniqueness, whatever the input is and the assumption that the function cannot be reverted (you cannot compute or guess the input from the output).
If collisions exist (i.e. two different inputs generate the same output) then the hash function is considered weak and should not be used anymore.

The process of signing some data usually involves hashing it and encrypting the hash with the private key to create a signature, then providing the signature alongside the data in a way or another.

{% mermaid() %}
flowchart LR
    e_data@{ label: "Data", shape: in-out }
    e_privkey@{ label: "Private Key", shape: in-out }
    e_hash@{ label: "Hash", shape: in-out }
    e_sign@{ label: "Signature", shape: in-out }
    a_hash@{ label: "Hash the data", shape: process }
    a_encrypt@{ label: "Encrypt the hash", shape: process }
    a_store@{ label: "Glue the data and the signature together", shape: double-circle }
    
    e_data --> a_hash --> e_hash
    e_hash --> a_encrypt --> e_sign
    e_privkey ----> a_encrypt
    e_data --> a_store
    e_sign --> a_store
{% end %}

The signature of the data can be verified by retrieving both the signature and the data, decrypting the hash from the signature with the public key, and hashing the data again.
The signature is considered valid if the two hashes match each other.

{% mermaid() %}
flowchart LR
    e_data@{ label: "Data", shape: in-out }
    e_sign@{ label: "Signature", shape: in-out }
    e_pubkey@{ label: "Public Key", shape: in-out }
    e_hash_data@{ label: "Hash from data", shape: in-out }
    e_hash_sig@{ label: "Hash from signature", shape: in-out }
    a_hash@{ label: "Hash the data", shape: process }
    a_decrypt@{ label: "Decrypt the signature", shape: process }
    a_compare{Compare hashes}
    s_sign_ok@{ label: "&nbsp;Signature is **valid**&nbsp;", shape: double-circle }
    s_sign_ko@{ label: "Signature is **invalid**", shape: double-circle }
    
    e_data --> a_hash --> e_hash_data
    e_sign --> a_decrypt --> e_hash_sig
    e_pubkey --> a_decrypt
    e_hash_data --> a_compare
    e_hash_sig --> a_compare
    a_compare -- Equals --> s_sign_ok
    a_compare -- Not equals --> s_sign_ko
{% end %}

# Verifying the OpenPGP signature of a downloaded file

PGP [^3] stands for Pretty Good Privacy and lead to the creation of the OpenPGP standard [^4], which is widely used to sign and/or encrypt emails and files.

Verifying the PGP signature of a downloaded file usually involves GNU Privacy Guard [^5] also known as `gpg` (even if there could be other methods).

On Linux, it is as simple as issuing one of the following commands, assuming both the file and its signature are in the same folder (e.g. `/path/to/the/files`):
{% wide_container() %}
```bash
cd /path/to/the/files
# Assuming signature file name is the same as the signed file name
# with an additional extension (e.g. `.sig`, `.asc`...)
gpg --verify '<your-signature-file>'
# If file name cannot be infered from signature name
gpg --verify '<your-signature-file>' '<your-signed-file>'
```
{% end %}

If you try to verify the signature of a file without importing the associated public key, `gpg` throws a `Can't check signature: No public key` error:
{% wide_container() %}
```bash
gpg --verify '<your-signature-file>'
#> gpg: assuming signed data in '<your-signed-file>'
#> gpg: Signature made <signature-date>
#> gpg:                using RSA key XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
#> gpg: Can't check signature: No public key
```
{% end %}

Nothing has been verified there, so you need to import the public key associated to the signature first.

In order to import the key, you need to first know where you can retrieve it from.
This is usually provided with the file and its signature or in the file installation documentation.

Once known, downloading and importing the key is as simple as issuing the following command:
{% wide_container() %}
```bash
# Download the key and import it at once
wget '<remote-key-location' -q0 - | gpg --import -
#> gpg: key YYYYYYYYYYYYYYYY: Z signatures not checked due to missing keys
#> gpg: key YYYYYYYYYYYYYYYY: public key "<key-owner>" imported
#> gpg: Total number processed: 1
#> gpg:               imported: 1
#> gpg: no ultimately trusted keys found
```
{% end %}

But how do you make sure the key you imported is the one you expected?

Keys can be nested (as it is for Certificates Authorities used for HTTPS) and you can sign a key with another key.
If you want to deeply trust a key, you should start trusting its parent keys first.
Here, we directly import the key we need which explain why we have the message `no ultimately trusted keys found`.
If we had imported and trusted the root key and imported all the intermediary keys the key which signed the file depends on if any, this message would not be there.

Most of the time though, the key you import is standalone such as a root key (which has no parent).
In that case, the only thing you can do is to compare its fingerprint with the one provided (when it is provided) before trusting it.

You can also look for other sources of the public key to compare its fingerprint with.
Usually, developers tend to put their public key in several independent places (public profiles, repos, websites, ...) so it might be possible to compare several fingerprints to prevent a bit more  public key tampering.

You can also check a public key fingerprint without importing it by downloading it and displaying its content:
{% wide_container() %}
```bash
wget '<remote-key-location>' -q0 -O /path/to/local/key
# Key fingerprint is on the second line, a string composed of
# 40 alphanumerical characters (replaced by all the X here)
gpg --show-keys /path/to/local/key
#> pub   <pub-key-algorithm> <pub-key-generation-date> [<pub-key-usages>] [expires: <pub-key-expiration-date>]
#>       XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
#> uid                      <uid1>
#> uid                      <uid2>
#> uid                      ...
#> sub   <sub-key-algorithm> <sub-key-generation-date> [<sub-key-usages>]
```
{% end %}

If you consider the key as trusted, you can import it with the following command:
{% wide_container() %}
```bash
gpg --import /path/to/local/key
```
{% end %}

Last but not least, You can retrieve the fingerprint of already imported keys with the following command:
{% wide_container() %}
```bash
# Key fingerprint is on the second line, a string composed of
# 40 alphanumerical characters (replaced by all the X here)
gpg --fingerprint
#> /path/to/keyring
#> ----------------
#> pub   <pub-key-algorithm> <pub-key-generation-date> [<pub-key-usages>] [expires: <pub-key-expiration-date>]
#>       XXXX XXXX XXXX XXXX XXXX  XXXX XXXX XXXX XXXX XXXX
#> uid           [uid1-trust] <uid1>
#> uid           [uid2-trust] <uid2>
#> uid           ...
#> sub   <sub-key-algorithm> <sub-key-generation-date> [<sub-key-usages>]
#> ... same for other keys ...
```
{% end %}

PGP is all about building trust in a digital world.
`gpg` is able to adapt its behavior depending on the level of trust you declare on each imported key.

I won't go in the details of this feature as it should not be so useful in the case of a one-shot signature verification for the file unless you plan to do it regularly.

If you want to go deeper on this topic, you can look at [the explanations and instructions provided by Qubes OS regarding signature verification](https://www.qubes-os.org/security/verifying-signatures/) which use nested keys and explains how to trust a key.

Now keys are imported, let's verify the file again.

If the signature is valid, we have a `Good signature` successful message:
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

We have a `BAD signature` error message otherwise:
{% wide_container() %}
```bash
#> gpg: assuming signed data in '<your-signed-file>'
#> gpg: Signature made <signature-date>
#> gpg:                using RSA key XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
#> gpg: BAD signature from <uid1> [<uid1-trust>]
```
{% end %}

If the signature is valid, you can use the file safely.
But if it is not the case, I would strongly advise discarding the file.
You can try to download and verify it again, but if the bad signature persists, you should get in touch with the file provider before going further.

[^1]: <https://en.wikipedia.org/wiki/Public-key_cryptography>
[^2]: <https://en.wikipedia.org/wiki/Cryptographic_hash_function>
[^3]: <https://en.wikipedia.org/wiki/Pretty_Good_Privacy>
[^4]: <https://datatracker.ietf.org/doc/html/rfc9580>
[^5]: <https://en.wikipedia.org/wiki/GNU_Privacy_Guard>