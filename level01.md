# Level01

There is a vulnerability in the below program that allows arbitrary programs to
be executed, can you find it?
To do this level, log in as the **level01** account with the password
**level01**. Files for this level can be found in /home/flag01.

## Source Code

```c
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <sys/types.h>
#include <stdio.h>

int main(int argc, char **argv, char **envp)
{
  gid_t gid;
  uid_t uid;
  gid = getegid();
  uid = geteuid();

  setresgid(gid, gid, gid);
  setresuid(uid, uid, uid);

  system("/usr/bin/env echo and now what?");
}
```

## Solution

The /home/flag01 contains a Setuid binary owned by the user flag01 - the target account.
Analysing the source code of the flag01 program we can notice the `system` function call which will execute the given command inside a SHELL. But the program take no input from the user so how can we execute arbitrary programs? Well let's take a closer look to the system function call:

```c
system("/usr/bin/env echo and now what?");
```

To understand the vulnerability we must first understand how commands are executed in Linux shell. When we type:

```bash
echo "Hello World"
```

in the terminal, echo is the **command** and "Hello World" is the argument. Looking at the man page for **sh** which is the shell used by system function call to run the program we find a section about PATH search:
Path Search
(0) When locating a command, the shell first looks to see if it has a shell function by that name. Then it looks for a builtin command by that name. If a builtin command is not
found, one of two things happen: 1. Command names containing a slash are simply executed without performing any searches. 2. The shell searches each entry in PATH in turn for the command. The value of the PATH variable should be a series of entries separated by colons. Each entry consists of a
directory name. The current directory may be indicated implicitly by an empty directory name, or explicitly by a single period.

So if the function call was simply `system("echo ...");` it would been executed inside a shell, and since "echo" is a "shell builtin" which we we can check by running `type echo`, we would fall in the (0) scenario.
The command being executed in this case however, is the `/usr/bin/env` which looking at the man page for the env command, will simply run the argument, in this case `echo`, with the current environment variables. The key part is how that env is executed. The env command does not has any idea about shells and shell builtins, so every command must be lcoated using strategies 1 and 2. Since in this case the echo comand does not have a slash, it must be located using the 2nd strattegy, meaning it has to be located using the PATH environemnt variable. Well we have control of the environment in which we are executing the flag01 binary, so here's the plan:
In a directory we control, in this case /home/level01 we place the following script name as "echo"

```bash
#!/bin/bash
getflag:w

```

2 We then set it as executable

```bash
chmod +x ./echo
```

3. execute the flag01 binary using the following command:

```bash
env PATH=/home/level01:$PATH /home/flag01/flag01
```

So when we execute the binary in step 3, we set the PATH env variable to include our home directory "using env command in which we placed our handcrafted echo script. That script will execute the getflag solving the challenge
