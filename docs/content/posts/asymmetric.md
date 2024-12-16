---
title: Crypto Lab 2 - Using Asymmetric keys
description: This is our second Crypto Lab that shows the usage of asymmetric keys 
date : 2024-01-03
---

Asymmetric encryption, also known as public key cryptography, uses a pair of keys: a public key for encryption and a private key for decryption. Each user has a key pair, and anything encrypted with one key can be decrypted with the other key from the same pair.

Please refer to [pre-requisite](posts\linux_prereq_crypto.md) for this blog to ensure that you have two users setup on Linux.

### Scenario
* **mikey** wants to send an encrypted file to **bart**.
* mikey uses bart's public key to encrypt the file.
* bart uses his private key to decrypt the file.
* The file to be encrypted is `turing_bio` stored at `/srv/shared`

***
### bart's Side (Generating the keys)

1. **bart** generates a key pair (public and private keys):
   

```bash
   gpg --gen-key
   ```

   Follow the prompts to generate the key pair. bart will need to provide his name, email address, and a passphrase. Here's the sample output

   

```console
    GnuPG needs to construct a user ID to identify your key.

   Real name: RN
   Name must be at least 5 characters long
   Real name: bart12345
   Email address: bart12345@gmail.com
   You selected this USER-ID:
      "bart12345 <bart12345@gmail.com>"

   ....public and secret key created and signed.

   pub   rsa3072 2024-12-10 [SC] [expires: 2026-12-10]
         A6F6AFC9E969E6951FCE5DDC08C537A5ECE4F89D
   uid                      Rn12345 <rn12345@gmail.com>
   sub   rsa3072 2024-12-10 [E] [expires: 2026-12-10]
   ```

2. **bart** exports his public key to a file:
   

```bash
   gpg --export -a "bart12345" > bart_public.key
   ```

   This creates a file `bart_public.key` containing bart's public key.

   

```bash
   cat  bart_public.key
   ```

   

```console
   -----BEGIN PGP PUBLIC KEY BLOCK-----

   mQGNBGdY...............
   ............
   =7ZXt
   -----END PGP PUBLIC KEY BLOCK-----
   ```

### mikey's Side (Encrypting the File)

1. **mikey** imports bart's public key:   
   

```bash
   gpg --import bart_public.key
   ```

2. **mikey** encrypts the file using **bart's public key**:
   ```bash   
   gpg --output turing_bio_asymmetric_enc.gpg --encrypt --recipient "bart12345" turing_bio
   

```
   The resulting `turing_bio_asymmetric_enc.gpg` is the encrypted version of the file
   ```bash   
   >head turing_bio_asymmetric_enc.gpg 
   ```

   

```console
   ����^�b5�   
   ```

### bart's Side (Decrypting the File)

1. **bart** decrypts the file using his **private key**:
   

```bash
   >gpg --output turing_bio_asymmetric_dec --decrypt turing_bio_asymmetric_enc.gpg
   ```

2. **bart** can now read the decrypted message and check that it is identical to original:
   

```bash
   >diff turing_bio turing_bio_asymmetric_dec
   ```

###  Private key details

When you use `gpg --decrypt` to decrypt a file, it requires the corresponding private key that matches the public key used to encrypt the file. This private key is stored in your GPG keyring. Here are some details about where and how the private key is stored:

### Accessing Your Keyring

To list the private keys in your keyring, you can use the following command:

```bash
gpg --list-secret-keys
```

This command will output details about the private keys available in your keyring.

```console
/home/bart/.gnupg/pubring.kbx
------------------------------
sec   rsa3072 2024-12-10 [SC] [expires: 2026-12-10]
      A6F6AFC9E969E6951FCE5DDC08C537A5ECE4F89D
uid           [ultimate] bart12345 <bart12345@gmail.com>
ssb   rsa3072 2024-12-10 [E] [expires: 2026-12-10]
```

### Backing up the private key 

* **Passphrase Protection**: Your private key is usually protected by a passphrase, which you set when you generated the key pair. This adds an additional layer of security.
* **Backup**: It's important to keep a secure backup of your private key and remember your passphrase. Losing either means you won't be able to decrypt files encrypted with the corresponding public key.

Here's how to do that
 

```bash
gpg --export-secret-keys -a "Rn12345" > private-key-backup.asc
```

 

```bash
cat private-key-backup.asc 
 ```

 

```console
-----BEGIN PGP PRIVATE KEY BLOCK-----

lQWGBG ...........
=8Ju6
-----END PGP PRIVATE KEY BLOCK-----
```

### High Level concepts to remember

* **Public Key**: Can be shared openly. It's used for encrypting messages.
* **Private Key**: Must be kept secure. It's used for decrypting messages and signing.
