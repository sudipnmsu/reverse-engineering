#Week 4: Assembly Code & Ghidra 

##Q/A Section:

1. What is the difference between machine code and assembly?
**Answer:** Machine code consists of instructions and data in binary numbers and is displayed in hexadecimal form. Assembly language is somewhat in between machine code and high level language, uses numbers, symbols and abbreviations instead of binary 0s and 1s, and highest level representation that can be constructed reliably from machine code. 

2. If the ESP register is pointing to memory address 0x001270A4 and I execute a `push eax` instruction, what address will ESP now be pointing to?
**Answer:** The ESP register will decrease by four and will be pointing at 0x001270A0.

3. What is a stack frame?
**Answer:** A stack frame defines how the stack is organized. A function typically needs local memory for arguments, local variable, temporary variable (if any) and return address - this region of memory altogether is called a stack frame.

4. What would you find in a data section?
**Answer:** The data section contains static and global values which are assigned during the initialization of a program.

5. What is the heap used for?
**Answer:** Heap manages dynamic memory allocation. It creates space for new variables and clears spaces that are no longer needed. 

6. What is in the code section of a program's virtual memory space?
**Answer:** The code section contains the instructions that are going to be executed. It controls how the tasks will be executed and what the tasks are.

7. What does the `inc` instruction do, and how many operands does it take?
**Answer:** `inc` instruction takes one operand and increments it by 1.

8. If I perform a `div` instruction, where would I find the remainder of the binary division (modulo)?
**Answer:** The remainder of the binary division would be found in EDX register.

9. How does `jz` decide whether to jump or not?
**Answer:** `jz` performs the jump operation if ZF (zero flag) is 1.

10. How does `jne` decide whether to jump or not?
**Answer:** `jne` performs the jump operation if ZF is 0, that is the `src` operand is not equal to `dst` operand.

11. What does a `mov` instruction do?
**Answer:** `mov` instruction moves data from a `src` to `dst`.

12. What does the `TF` flag do and why is it useful for debugging?
**Answer:** This flag is used for debugging. Since it makes the compiler execute one instruction at a time, the debugging becomes easier if this flag is on.

13. Why would an attacker want to control the EIP register inside a program they want to take control of?
**Answer:** EIP is the instruction pointer which points to the next instruction to be executed. As the name suggests, if one takes control over this register, he/she can control what is going to be executed by the CPU. 

14. What is the AL register and how does it relate to EAX?
**Answer:** AL register represents the lowest 8 bits of EAX register.

15. What is the result of the instruction `xor eax, eax` and where is it stored?
**Answer:** This instruction clears EAX register and sets the result 0 to EAX.


##Grad Only:

I) What does the `leave` instruction do in terms of registers to leave a stack frame?
**Answer:**

II) What `pop` instruction is `retn` equivalent to?
**Answer:**

III) What is a stack overflow?
**Answer:**

IV) What is a segmentation fault (a.k.a. a segfault)?
**Answer:**

V) What are the ESI and EDI registers for?
**Answer:**

Crack-Me Solution:



