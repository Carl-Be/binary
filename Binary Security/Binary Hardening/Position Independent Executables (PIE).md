# Hardening binaries using Position Independent Executables (PIE)
- In order to understand how PIE works we must first understand [Address Space Layout Randomization (ASLR)](../../Binary%20Security/Binary%20Hardening/Address%20Space%20Layout%20Randomization%20%28ASLR%29.md)
	- PIE is, after all, an application of ASLR on the executable. 
- The fundamental idea behind PIE is that the location of everything, including the executable itself, is randomised each time the program is run.
- A PIE binary and all of its dependencies are loaded into random locations within virtual memory each time the application is executed. This makes Return Oriented Programming (ROP) attacks much more difficult to execute reliably.
- PIE makes knowing (or guessing) how the application has been mapped into memory much harder for an attacker, since the address of the executable and every Dynamic Shared Object (DSO) are randomised on each execution.
- CVE-2012-0056 is a vulnerability that abuses binaries without PIE.
- PIE should always be considered as a critical defence mechanism for protecting setuid applications and network attached applications from potential attacks.
- PIE utilises relative addresses.
- PIE application randomises not only the address of the executable, but also every Dependent Shared Object (DSO)

## Example of why PIE is needed 
Compilation using the default compiler settings will produce the same output on each execution:
```
$ gcc demo.c
$ ./a.out
0x4004c4
$ ./a.out
0x4004c4  	   <-- Same
```
However, recompiling the same program as a Position Independent Executable (PIE) produces different results on each execution:
```
$ gcc -pie -fpie demo.c
$ ./a.out
0x7f57fc4746fc
$ ./a.out
0x7faffd70c6fc   <-- Different
```

## Example of how PIE complies files diffrently
The Executable and Linkable Format (ELF) produced by PIE is recognised as a shared library.
```
$gcc normal.c
$file a.out | sed -e 's/,.*$//'
a.out: ELF 64-bit LSB executable

$gcc -pie -fpie pie.c
$ file a.out | sed -e 's/,.*$//'
a.out: ELF 64-bit LSB shared object

```



## ## How To Aattack (binaries without PIE enabled):

[Return to libc Attack](../../Binary%20Security/Binary%20Attacks/Return%20to%20libc%20Attack.md)

## Sources:
https://www.redhat.com/en/blog/position-independent-executables-pie

<ins>Security Research Paper For PIE done by RedHat Linux distro:</ins>
- [pie.odt.zip](../../_resources/pie.odt.zip)