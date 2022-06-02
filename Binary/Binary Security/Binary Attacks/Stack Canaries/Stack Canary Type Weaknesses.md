## Canary Type Weaknesses:
### Null Canary
- The null canary would be the simplest for the compiler programmer to implement. It places 4 NULL bytes just before the SFP and RP. As this is a predictable value, an attacker may still be able to bypass the canary. 
### Terminator Canary
- The terminator canary introduces two more hex values that attempt to terminate string operations, 0x0a and 0xff. These values are again predictable, and can be bypassed with relative ease under the right conditions.
### Random Canary
- A random canary will offer better protection. It usually consists of a NULL byte followed by 3 random bytes. The NULL byte would attempt to terminate string operations, while the 3 random bytes will make the canary less predictable to the attacker.
### Random XOR Canary
- The random XOR canary will be like the random canary, except it will be XOR’ed against a non-static value in the program (usually the Base Pointer EBP). As operating systems nowadays run with Address Space Layout Randomization (ASLR) activated, EBP will not be static across runs of the program. This adds an extra layer of randomization to the cookie, making it hard to predict this value.
### 64-bit Canary
### Custom Canary