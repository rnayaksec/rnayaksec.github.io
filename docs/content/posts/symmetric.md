---
title: Crypto Lab 1 - Using Symmetric keys
description: This is our first Crypto Lab that shows the usage of symmetric keys 
date : 2024-01-02
---
Symmetric encryption, also known as shared secret encryption, uses the same key for both encryption and decryption. If one user encrypts a message with a secret key, the recipient must use the same key to decrypt it.

Let's walk through an example of symmetric key encryption on the Linux command line involving two users, mikey and bart, using `gpg` . In this case the shared key is the passphrase used to encrypt and decrypt.

Please refer to [pre-requisite](posts\linux_prereq_crypto.md) for this blog to ensure that you have two users setup on Linux.

### Scenario

* **mikey** wants to send an encrypted file to **bart** using a shared passphrase.

* Both mikey and bart will use the same symmetric key (passphrase) for encryption and decryption.

* The file to be encrypted is `turing_bio` stored at `/srv/shared`

```bash
>head turing_bio
```

```console
Alan Mathison Turing was an English mathematician, computer scientist, logician, cryptanalyst, philosopher and theoretical biologist.
```
***
### mikey's Side (Encrypting the File)

1. **mikey** encrypts the file using a symmetric key with `gpg`:
   

```bash
>gpg --symmetric --cipher-algo AES256 turing_bio
```

This command will prompt mikey to enter a passphrase. The result is a file called `turing_bio.gpg` created in the same folder

```bash
>ls 
```

```console
turing_bio  turing_bio.gpg
```

Checking contents of the file reveals that contains encrypted text 

```bash
>head turing_bio.gpg 
```

```console
�   ��������z���%�׈�?�
```

### bart's Side (Decrypting the File)

1. **bart** decrypts the file using the same passphrase:
   

```bash
   gpg --output turing_bio_decrypted --decrypt turing_bio.gpg
   ```

   This command will prompt bart to enter the passphrase that mikey used to encrypt the file.

3. **bart** can now read the decrypted message and check that it is identical to original
   

```bash
   >diff turing_bio turing_bio_decrypted 
   ```

```
