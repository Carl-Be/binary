**Over filling a buffer on a stack based overflow format**
PAYLOAD =NOPs &lt;\\x90&gt;(n bytes) + SHELL CODE (n bytes) + A &lt;\\x41&gt; (n byes) + RETURN ADDRESS (n bytes).

### Correct way to input unreadable ASCII chars

### (printf “\\x04\\x10...\\x02”) | ./vulnerable_binary

sudo gvm-setup
**If the program is expecting command line arguments use**
./victim $(printf “foo”)
To find the offset to the saved return address (IP reg) with the CLI use
./victim $(printf “%0512x” 0)
Error: Segmentation fault == Start of IP reg offset address