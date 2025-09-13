# Level04

This level requires you to read the **token** file, but the code restricts the
files that can be read. Find a way to bypass it :)

To do this level, log in as the level04 account with the password level04.
Files for this level can be found in /home/flag04.

## Source code

```c
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <sys/types.h>
#include <stdio.h>
#include <fcntl.h>

int main(int argc, char **argv, char **envp)
{
char buf[1024];
int fd, rc;

if(argc == 1) {
    printf("%s [file to read]\n", argv[0]);
    exit(EXIT_FAILURE);
}

if(strstr(argv[1], "token") != NULL) {
    printf("You may not access '%s'\n", argv[1]);
    exit(EXIT_FAILURE);
}

fd = open(argv[1], O_RDONLY);
if(fd == -1) {
    err(EXIT_FAILURE, "Unable to open %s", argv[1]);
}

rc = read(fd, buf, sizeof(buf));

if(rc == -1) {
    err(EXIT_FAILURE, "Unable to read fd %d", fd);
}

write(1, buf, rc);
}
```

## Solution

Analysing the source code we see that the flag04 program accepts as an argument the name of the file to read, and if the file contains `token` in the name it will refuse to read. Well the target file we want to read is the token file which obviously has token in it's name. So we need to tell the program to read the token file but we cannot refer to the file by it's name. Concatenating the token file name at the shell level doesn't help us because the resulting string is the one being checked by the flag04 program.
The solution is to create a symbolic link that points to the token file and invoke the flag04 program passing that symbolic link, as such we will pass the check in line 18, and when `open` is called in line 23 it will follow the symbolic link and open the token file for read, giving us access to the contents of the file.
So here are the steps:
Back in our home directory, we create a symbolic link named `link` to the token file:
logseq.order-list-type:: number

```bash
ln -s /home/flag04/token link
```

Then we invoke the flag04 acccount passing the symbolic link we just created to be read
logseq.order-list-type:: number

```bash
/home/flag04/flag04 /home/level04/link
```

We get the contents of the token as expected. and using it as a password we are able to ssh into the flag04 account and execute the getflag command finishing the challenge
logseq.order-list-type:: number
