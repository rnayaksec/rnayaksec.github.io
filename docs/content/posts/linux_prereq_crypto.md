---
title: Linux Basics - Adding users and groups
description: This is pre-requisite for subsquent set posts called Crypto Labs
date : 2024-01-01
---

This short introductory post will walk you through adding a user, creating a group, adding the user to the group and sudoers, and setting up a shared folder with proper permissions on Ubuntu Linux. 

The end goal is to end up with two user who can play the role of Alice and Bob as is typically the case for lot of cryptographic operations.

> [! IMPORTANT]
> This blog assumes you already are logged into Ubuntu Linux with one user and all we are doing here is creating a second one. Alternately you can reuse the same script and create two distinct users if you wish to do so.

### 1. Add a User Named bart

Firstly, let's create a new user named `bart` :

```bash
sudo adduser bart
```

You will be prompted to set a password and provide some additional information (which you can skip by pressing Enter).

```console
Adding user `bart' ...
Adding new group `bart' (1005) ...
Adding new user `bart' (1004) with group `bart' ...
The home directory `/home/bart' already exists.  Not copying from `/etc/skel'.
New password: 
Retype new password: 
passwd: password updated successfully
Changing the user information for bart
Enter the new value, or press ENTER for the default
	Full Name []: Bart Simpson
	Room Number []: 
	Work Phone []: 
	Home Phone []: 
	Other []: 
Is the information correct? [Y/n] Y
```

The `adduser` command will create a home directory for Bart at `/home/bart` and configure appropriate default settings.

### 2. Create a Group Called cryptogrp

Next, we'll create a new group named `cryptogrp` :

```bash
sudo groupadd cryptogrp
```

This command will create a group with the name `cryptogrp` on your system.

### 3. Add the user bart to Both cryptogrp and sudo

Now, let's add user bart to the `cryptogrp` group and also to the `sudo` group, which grants administrative privileges:

```bash
sudo usermod -aG cryptogrp bart
sudo usermod -aG sudo bart
```

The `usermod` command with the `-aG` option appends user bart to the specified groups.

Use this command to check that group has been created

```bash
grep cryptogrp /etc/group
```

```console
cryptogrp:x:1006:mikey,bart
```

### 4. Create a Shared Folder Location for Files

We'll create a shared directory where members of `cryptogrp` can store files:

```bash
sudo mkdir /srv/shared
```

### 5. Give cryptogrp Access to the Shared Folder

Finally, we need to set the permissions so that members of `cryptogrp` can read, write, and execute files in the shared directory:

```bash
sudo chown :cryptogrp srv/shared
sudo chmod 770 srv/shared
```

* `chown :cryptogrp /shared` changes the group ownership of the `/shared` directory to `cryptogrp`.
* `chmod 770 /shared` sets the permissions so that the owner and the group have read, write, and execute permissions, while others have no access.

