# Week 4: Assembly Code Review

## Q/A Section:

### 1. What is the difference between machine code and assembly?

**Answer:** Machine code consists of instructions and data in binary numbers and is displayed in hexadecimal form. Assembly language is somewhat in between machine code and high level language, uses numbers, symbols and abbreviations instead of binary 0s and 1s, and highest level representation that can be constructed reliably from machine code. 

### 2. If the ESP register is pointing to memory address 0x001270A4 and I execute a `push eax` instruction, what address will ESP now be pointing to?

**Answer:** The ESP register will decrease by four and will be pointing at 0x001270A0.

### 3. What is a stack frame?

**Answer:** A stack frame defines how the stack is organized. A function typically needs local memory for arguments, local variable, temporary variable (if any) and return address - this region of memory altogether is called a stack frame.

### 4. What would you find in a data section?

**Answer:** The data section contains static and global values which are assigned during the initialization of a program.

### 5. What is the heap used for?

**Answer:** Heap manages dynamic memory allocation. It creates space for new variables and clears spaces that are no longer needed. 

### 6. What is in the code section of a program's virtual memory space?

**Answer:** The code section contains the instructions that are going to be executed. It controls how the tasks will be executed and what the tasks are.

### 7. What does the `inc` instruction do, and how many operands does it take?

**Answer:** `inc` instruction takes one operand and increments it by 1.

### 8. If I perform a `div` instruction, where would I find the remainder of the binary division (modulo)?

**Answer:** The remainder of the binary division would be found in EDX register.

### 9. How does `jz` decide whether to jump or not?

**Answer:** `jz` performs the jump operation if ZF (zero flag) is 1.

### 10. How does `jne` decide whether to jump or not?

**Answer:** `jne` performs the jump operation if ZF is 0, that is the `src` operand is not equal to `dst` operand.

### 11. What does a `mov` instruction do?

**Answer:** `mov` instruction moves data from a `src` to `dst`.

### 12. What does the `TF` flag do and why is it useful for debugging?

**Answer:** This flag is used for debugging. Since it makes the compiler execute one instruction at a time, the debugging becomes easier if this flag is on.

### 13. Why would an attacker want to control the EIP register inside a program they want to take control of?

**Answer:** EIP is the instruction pointer which points to the next instruction to be executed. As the name suggests, if one takes control over this register, he/she can control what is going to be executed by the CPU. 

### 14. What is the AL register and how does it relate to EAX?

**Answer:** AL register represents the lowest 8 bits of EAX register.

### 15. What is the result of the instruction `xor eax, eax` and where is it stored?

**Answer:** This instruction clears EAX register and sets the result 0 to EAX.


## Grad Only:

### I) What does the `leave` instruction do in terms of registers to leave a stack frame?

**Answer:** It moves EBP to ESP and pops EBP.

### II) What `pop` instruction is `retn` equivalent to?

**Answer:** `pop [return_address]` is equivalent to `ret`.

### III) What is a stack overflow?

**Answer:** It is an overflow error which happens when the program attempts to use more memory in the call stack pointer than it’s been allocated. 

### IV) What is a segmentation fault (a.k.a. a segfault)?

**Answer:** When a program tries to access any memory that is not allowed to access, that is called segmentation fault.

### V) What are the ESI and EDI registers for?

**Answer:** They are general purpose registers which often are used as pointers for source address (ESI) and destination address (EDI) while copying a block of data.

## Crack-Me Solution:

We start by opening the `keyg3nme` file in Ghidra and look for the `main` function under the `Symbol Tree` section. Double click on the `main` and we will see some decompiled C code. The main function has a few variables at the beginning, then a `printf` statement, followed by a `scanf`, a `validate_key` function and two condition logic blocks.

We see the `validate_key` function in the `Symbol Tree` and inside it checks if `param_1 % 0x4c7 == 0` and returns a boolean value. The decimal value of `0x4c7` is `1223` which is a prime number. This means that if `param_1` is either ‘1223’ or ‘multiple of 1223’ or ‘0’, the function will return true. The functionality of `CONCAT71` is not clear, it takes the `validate_key` function’s return value as 1 parameter and another undefined 7-byte variable and checks if the output is 1 after typecasting it to integer.

We check our finding by running the binary `./keyg3nme`. As mentioned above, any input that is ‘1223’ or ‘multiple of 1223’ or ‘0’, the output shows `Good job mate…`, and for any other input, the output is ‘nope.’. 





