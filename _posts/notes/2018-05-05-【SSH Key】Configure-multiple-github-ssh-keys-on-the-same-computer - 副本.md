---
layout: post
title: 【SSH Key】Configure multiple github ssh keys on the same computer
categories: notes
---

### 1.Open the git bash and input the codes:

```
 ssh-keygen -t rsa -b 4096 -C "shinepans@live.com (Your Email)"
```

And it outcomes:

```
Generating public/private rsa key pair.
Enter file in which to save the key (/c/Users/shinepans/.ssh/id_rsa): id_rsa_cess (id_rsa_cess:We need to create a new name that is different from the previous file name )
```

And it outcomes:

```
Enter passphrase (empty for no passphrase): (Enter)
Enter same passphrase again:(Enter)
Your identification has been saved in id_rsa_cess.
Your public key has been saved in id_rsa_cess.pub.
The key fingerprint is:
SHA256:jgkhEZIjeQNF6CZWU+sIpCUbZBIip+AdgQcZlnXlon0 shinepans@live.com
The key's randomart image is:
+---[RSA 4096]----+
|%/#=oo..         |
|^%+=o o          |
|*=+ooo .         |
|.+..=..          |
|+  o.o ES        |
|     ..+         |
|      o .        |
|                 |
|                 |
+----[SHA256]-----+
```

### 2.Configure the ~/.ssh/config file

```
Host github.com
    HostName github.com
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa
Host cess.github.com
    HostName github.com
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa_cess
```

### 2.Add the new generated SSH key to the associated Github account

> Add the newly generated key in id_rsa_cess.pub to the associated Github account. 

**And Then Use**

```
ssh -T cess.github.com
```

**to test whether the association is successful. The xxx.github.com used here is the name of the second host in the previous config.**

And it should be outcomes:

```
$ ssh -T git@cess.github.com
Hi cessuser! You've successfully authenticated, but GitHub does not provide shell access.
```

### 3.Use multiple SSH Key

If we need use default account whose ssh key is id_ras.pub, we can use 

```
git clone git@github.com:SOMETHING.git
```

If we need use another account whose ssh key is id_ras_cess.pub, we can use 

```
git clone git@cess.github.com:SOMETHING.git
```
