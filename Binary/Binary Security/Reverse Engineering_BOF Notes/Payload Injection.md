# Getting inputs from `char *argv[]`

In this case, the arguments are read from the initial command line, so the most convenient thing is:

```
$> ./program $(echo -ne "\xef\xbe\xad\xde") 
```

In `gdb`, you need to pass the arguments through the `run` command line like this:

```
(gdb) run $(echo -ne "\xef\xbe\xad\xde") 
```

# Getting inputs from a file

Here, you have no other choice but write in the file and then feed your program with this file like this:

```
$> ./program ./myfile.txt 
```

And, within `gdb`, it should look like this:

```
(gdb) run myfile.txt 
```

Then, outside of `gdb` you can rewrite the content of the file and run your program again and again in `gdb`.

# Getting inputs from `stdin`

Getting the input through `stdin` can be achieve through a wide variety of functions such as `fgets()`, `scanf()`, `getline()`, `read()` and others. It raises a few problems because the program stop while executing and wait to be fed with characters. And, you would prefer to just have one feed at the beginning to try your stuff and see the result. Let see how to deal with this input.

In case you have to deal with several inputs (eg login, password, ...), you need to use separators between the inputs. Usually the separator between each input is just a newline character (`\n` or `\r` depending on the system you are in).

Now, you have two ways of doing to feed the `stdin`. Either you go through a file, like this:

```
$> cat ./mycommands.txt | ./program 
```

The `stdin` requires to run the command either through a file like this:

```
(gdb) run < ./mycommands.txt 
```

And do as said in the previous case.

The other option is to pipe the output of a command to the `stdin` of the program like this:

```
$> echo -ne "\xef\xbe\xad\xde" | ./program 
```

In `gdb` you can use the `bash` [process substitution](https://www.gnu.org/software/bash/manual/bashref.html#Process-Substitution) `<(cmd)` trick:

```
(gdb) run < <(echo -ne "\xef\xbe\xad\xde") 
```

This way is much quicker than effectively creating a named pipe and branch your program on it as recommended on several websites. Creating the named pipe outside of `gdb` requires a lot of unnecessary steps where you have it instantly with the previous technique.

Note also that, some people are using `<<$(cmd)` like this:

```
(gdb) run <<< $(echo -ne "\xef\xbe\xad\xde") 
```

But, this last technique seems to filter out all NULL bytes (for whatever reason), so you should prefer the first one (especially if you want to pass NULL bytes).

# Getting inputs from network

Here, you need to use another tool called `netcat` (the Swiss army knife of networking), often shortened into `nc`. Basically, if your vulnerable program is listening on `localhost:666` then the command line would be:

```
$> echo -ne "\xef\xbe\xad\xde" | nc -vv localhost 666 
```

Within `gdb`, the point will be to run (`r`) the program and to connect to it from another terminal.

# Keep the `stdin` open after injection

Most of the techniques for `stdin` will send the exploit string to the program which will end shortly after the termination of the input. The best way to keep it open afterward and get an active shell is to add a `cat` waiting for input on its `stdin`. It should look like this if you go though a file:

```
$> (cat ./mycommands.txt; cat) | ./program 
```

Or, like this if you want a shell command:

```
$> (echo -ne "\xef\xbe\xad\xde"; cat) | ./program 
```

Or, finally, if you are going through the network:

```
$> (echo -ne "\xef\xbe\xad\xde"; cat) | nc -vv localhost 666 
```

Note that I did not find how to do with `gdb` to keep the input open once the payload has been delivered.

* * *

# Update about Python3

You may have noticed that Python changed its way of handling raw bytes when going through the function `print()`. As an example you can have this kind of behavior (here is a comparison of the Python2 and Python3 behavior):

```
#> python2.7 -c 'print("\xde\xad\xbe\xef")' | xxd
00000000: dead beef 0a                                ....
#> python3.9 -c 'print("\xde\xad\xbe\xef")' | xxd
00000000: c39e c2ad c2be c3af 0a                   ......... 
```

As you noticed, in Python3, the output of `print()` is expressed in UTF-8 characters which change a lot the result of the command. This is not really convenient when you try to send a sequence of selected bytes to the program you attack.

So, if you are stuck with a system that offers nothing but a Python3 interpreter here is the command line to use:

```
python3.9 -c 'import sys; sys.stdout.buffer.write(b"\xde\xad\xbe\xef")' | xxd
00000000: dead beef                                .... 
```

You may notice that the command in Python3 is quite more complex than the one in Python2 which somehow ruin a bit the simplicity of the original command. If someone has a shorter and simpler way to achieve this, please drop a comment to this article, I am really looking forward it!

An alternate way of doing it can use the `echo` command like that:

```
#> echo -ne "\xde\xad\xbe\xef" | xxd
00000000: dead beef                                .... 
```

But, you cannot use the usual Python syntax such as `"\x90" * 12 + "\xde\xad\xbe\xef"` which can be annoying if you want to look for the proper padding.