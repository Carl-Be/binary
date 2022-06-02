# Hardening ELF binaries using Relocation Read-Only (RELRO)
https://www.redhat.com/en/blog/hardening-elf-binaries-using-relocation-read-only-relro
- RELRO can be turned on when compiling a program by using the following options:
`gcc -g -O0 -Wl,-z,relro,-z,now -o <binary_name> <source_code> `

- It’s also possible to compile with partial RELRO, which can be achieved by using the "-z,relro" option and not the "-z,now" on the gcc command line.
	- In partial RELRO, the non-PLT part of the GOT section (.got from readelf output) is read only but .got.plt is still writeable. Whereas in complete RELRO, the entire GOT (.got and .got.plt both) is marked as read-only.
	- Both partial and full RELRO reorder the ELF internal data sections to protect them from being overwritten in the event of a buffer-overflow, but only full RELRO mitigates the above mentioned popular technique of overwriting the GOT entry to get control of program execution.
	-  Partial RELRO is not really effective in preventing GOT overwrite attacks.
-  In conclusion, RELRO is a generic mitigation technique to harden the data sections of an ELF binary/process