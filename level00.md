# Level00

This level requires you to _find_ a Set User ID program that will run as the
“flag00” account. You could also find this by carefully looking in top level
directories in / for suspicious looking directories.

Alternatively, look at the find man page.

To access this level, log in as level00 with the password of level00.

### Source code

There is no source code available for this level.

### Set User ID

The [Set User ID](https://en.wikipedia.org/wiki/Setuid) is a flag that can be set on linux files. When a user executes an executable binary with the SetUserID flag present the process created will have the same permissions as the owner of the file. You can think of it as a "Run as owner" flag.
Suppose we have two users john and root. If root owns the executable binary [whoami](https://en.wikipedia.org/wiki/Whoami) , when john executes the binary it will print out "root", because the binary is being runned with the same permissions of the owner, in this case root.

### Solution

As suggested by the challenge text we take a look at the find page and craft a command to find a file with the set user id bit set belonging to the `flag00` account:

```bash
# cd to top level so we can search all the fs as supposed to only our home directory
cd /
find -perm /4000 -user flag00 2> /dev/null
```

We additionally redirect the stderr to the /dev/null file so we can have a clear output.
To understand how -perm /4000 works as a "find all files with set user id bit set" filter, one will find useful the man page for the chmod command. Specifically the octal mode perms explanation.
After getting the file path the next step is to execute it voila we get this output

```
level00@nebula:/$ ./bin/.../flag00
Congrats, now run getflag to get your flag!
flag00@nebula:/$
```

We can see that the prompt changed from level00 to flag00 after running the flag00 binary, meaning the binary spawned a shell as the owner of the file - the flag00 user. Following the instruction we execute the getflag command and get the following output:

```
You have successfully executed getflag on a target account
```
