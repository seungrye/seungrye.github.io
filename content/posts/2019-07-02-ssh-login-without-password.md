---
title: "SSH Login Without Password"
date: 2019-07-02T02:48:42+09:00
draft: false
categories: [studylog]
tags: [tip,ssh,howto]
---

When I connect other pc via ssh command, I always type password.
That's very annoying. :(

Is it possible connect without type password? And, the answer is YES.

<!--more-->

tl;dr

1. copy my pc's public key to remote pc.
2. append public key to `authorized_keys` file on remote pc.
3. connect it.

## Generate ssh key.

```shell
[enji@localhost ~]$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/enji/.ssh/id_rsa):
Created directory '/home/enji/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/enji/.ssh/id_rsa.
Your public key has been saved in /home/enji/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:nCnkdmVbuXjHY0NyP+bROE1J/NVP3HMUdMR7tDDOLU8 enji@localhost
The key's randomart image is:
+---[RSA 3072]----+
|              oOB|
|             +.o%|
|      .   o * *+X|
|     o . = + X E=|
|      + S o o %+=|
|     . o   . oo=o|
|               . |
|                 |
|                 |
+----[SHA256]-----+
[enji@localhost ~]$
```

## Copy to remote pc

```shell
[enji@localhost ~]$ scp $HOME/.ssh/id_rsa.pub enji@192.168.0.4:id_rsa.pub
enji@192.168.0.4's password:
id_rsa.pub                                                                                    100%  408    46.9KB/s   00:00
```

## Append to `authorized_keys`

```shell
[enji@localhost ~]$ ssh enji@192.168.0.4
enji@192.168.0.4's password:
Linux localhost 4.9.0-9-amd64 #1 SMP Debian 4.9.168-1+deb9u3 (2019-06-16) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
No mail.
Last login: Thu Jun 27 21:42:31 2019 from 192.168.0.1
enji@localhost:~$ cat id_rsa.pub >> $HOME/.ssh/authorized_keys
enji@localhost:~$ exit
```

## Connect

```shell
[enji@localhost ~]$ ssh enji@192.168.0.4
Linux localhost 4.9.0-9-amd64 #1 SMP Debian 4.9.168-1+deb9u3 (2019-06-16) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
No mail.
Last login: Tue Jul  2 02:57:51 2019 from 192.168.0.45
enji@localhost:~$ exit
```

Actually, this tip is for me. :P
