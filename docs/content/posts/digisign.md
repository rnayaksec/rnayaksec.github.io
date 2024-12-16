---
title: Crypto Lab 3 - Using Digital Signatures
description: This is our third Crypto Lab that shows the usage of digital signatures
date : 2024-01-04
---

Digital signatures provide an electronic counterpart to physical signatures, using asymmetric cryptography to achieve the following
* **Authentication**: The person owning the public key used to sign the message is the one who signed it.
* **Integrity**: The message was not altered after being signed.
* **Non-repudiation**: The sender can prove these facts to a third party if necessary.

Behind the scenes, digital signatures depend on two things
* A collision-resistant **hash function** that ensures no two inputs produce the same output.
* Asymmetric key pair: Anything encrypted with one key can only be decrypted with the other key from the pair.

Let's walk through an example of using digital signatures on the Linux command line involving two users, mikey and bart, using `gpg` .

Please refer to [pre-requisite](posts\linux_prereq_crypto.md) for this blog to ensure that you have two users setup on Linux.

### Scenario

* **mikey** wants to send a signed file to **bart** for which he does the following steps:
  + creates a hash of the message.
  + encrypts the hash with his private key, creating the digital signature.
  + send the message and hash to bart

* **bart** will do following steps:
 - hashes the received message and decrypts the digital signature using mikey's public key.
 - compares the two hash values. If they match, the message is verified

Here's high level notation of what is happening 
* Sender sends thhe following 
        Digital Signature of Sender = Encrypt with Private Key [ Hash(Message)] 

    -   Message
* Reciever does the following
     - Decrypt with Senders Public Key [ Digital Signature of Sender] == Hash(Message)
     - Compare with Hash(Message) and check if they are the same 
    
***
### mikey's Side (Signing the File)

1. **mikey** generates a key pair ( see previous lab on Asymmetric Keys for this)
   

```bash
   gpg --gen-key
   ```

   
2. **mikey** creates a file that he wants to sign:
   

```bash
   echo "This is an important message for bart." > secret_to_sign
   ```

3. **mikey** signs the file using his private key:
   

```bash
   gpg --output signed_secret.sig --detach-sign secret_to_sign 
   ```

4. **mikey** shared the original file (`secret_to_sign`) and the signature file (`signed_secret.sig`) to **bart**.

### bart's Side (Verifying the Signature)

1. **bart** needs mikey's public key to verify the signature. mikey exports his public key and shares it with bart:
   

```bash
   gpg --export -a "mikey" > mikey_public.key
   ```

2. **bart** imports mikey's public key:
   

```bash
   gpg --import mikey_public.key
   ```

3. **bart** verifies the signature:
   

```bash
   gpg --verify signed_secret.sig secret_to_sign 
   ```

   - If the signature is valid and the file has not been tampered with, GPG will display a message indicating that the signature is good and show mikey's information.
   

```console
    gpg: Signature made Tue 10 Dec 2024 11:58:12 AM PST
    gpg:                using RSA key A6F6AFC9E969E6951FCE5DDC08C537A5ECE4F89D
    gpg: Good signature from "mikey12345 <mikey12345@gmail.com>" [ultimate]
   ```

   - If there's a problem with the signature or the file has been altered, GPG will display an error message.
  
   

```console
    gpg: Signature made Tue 10 Dec 2024 11:58:12 AM PST
    gpg:                using RSA key A6F6AFC9E969E6951FCE5DDC08C537A5ECE4F89D
    gpg: Good signature from "mikey12345 <mikey12345@gmail.com>" [ultimate]
   ```

***
### Key differences between Digital signatures and Asymmetric Key
* Asymmetric Key Encryption 
    - Objective: Create messages that only someone with a private key can read.
    - Public keys are used to encrypt data.
    - Private keys are used to decrypt data.

* Digital Signature 
  + Objective: Create messages that can only have been created by a specific person (who possesses the private key) and can be verified by anyone with a corresponding public key.
  + Private keys are used for encryption.
  + Public keys are used for decryption.
