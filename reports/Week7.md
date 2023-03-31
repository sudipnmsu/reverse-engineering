# Reverse Engineering Ransomware

## General Approach:

We see that all the ransom programs are structurally almost similar to each other. What each program does:

- Take the encrypted `important.docx.pay_up` file as input.
- Ask for the key.
- If the key is correct, it will decrypt the file successfully.

There is another file `secret.txt.pay_up` which we probably will be able to decrypt if we can pass the file as a argument to the `decrypt` function. Instead of doing that, let’s see the `decrypt` function of each of the program and try to understand what they do for encryption.

In that class, considering the basic functionality of the program, we renamed the obfuscated functions to standard C library function in the order they probably would have executed. We did not strictly use the standard signature all the time, but in the end it was sufficient to figure out main decryption logic and write the decryption script.

For the next two programs, we strictly follow the functions signature which give us a more cleaner code for each of the program (screenshot below). 

## ransomware1:

### Screenshot from Ghidra

![ransom1 ghidra ss](images/ransom_1.png)

### Decryption program for ransomware1:

**Steps:**

- Take file as input.
- Read 1 byte at a time.
- For each byte, perform a bitwise `OR` operation with 0x34.
- Concatenate the results and finally print/write the output.

```
infile_path = "YOUR_LOCAL_PATH/ransomware1/secret.txt.pay_up"
output = ""

with open(infile_path, "rb") as f:
    while (byte := f.read(1)):
        byte = ord(byte) ^ 0x34
        output += chr(byte)

print(output)
f = open("output-secret-1.txt", "w")
f.write(output)
f.close()

```

Output: [file location](ransom-decrypted-file/output-secret-1.txt)

```
Dear Student,

You have decrypted the message. Good job!

"Many of the engineers I interviewed worked on reverse-engineering technology. Itâs a hallmark of Area 51."
 ~ ANNIE JACOBSEN

Go NMSU RE!

```

## ransomware2:

### Screenshot from Ghidra

![ransom2 ghidra ss](images/ransom_2.png)

### Decryption program for ransomware2:

**Steps:**

- Take file as input.
- Read 4 bytes at a time.
- Create another loop which execute 4 times (1 execution for each of the 4-byte chunk) in each iteration.
- Design the inner loop such a way that it performs a bitwise `OR` operation with `1` `3` `3` `7` and the consecutive bytes from the 4-byte chunk.
- Concatenate the results and finally print/write the output.

```
infile_path = "YOUR_LOCAL_PATH/ransomware2/secret.txt.pay_up"
output = ""
number_1337 = "1337"
chunk_size = 4

with open(infile_path, "rb") as f:
    while (byte := f.read(chunk_size)):
        loop_end = len(byte)
        temp_output = ""
        for i in range(0, loop_end):
            result = byte[i] ^ ord(number_1337[i])
            temp_output += chr(result)
        output += temp_output
            
    
print(output)
f = open("output-secret-2.txt", "w")
f.write(output)
f.close()

```

Output: [file location](ransom-decrypted-file/output-secret-2.txt)

```
Dear Student,

You have decrypted the message. Good job!

"Basically, if reverse engineering is banned, then a lot of the open source community is doomed to fail."
 ~ Jon Lech Johansen

Go NMSU RE!

```

## ransomware3:

### Screenshot from Ghidra

![ransom3 ghidra ss](images/ransom_3.png)

### Decryption program for ransomware3:


**Steps:**

This program is almost similar to the previous one, the differences are:

- Chunk size is 7 this time.
- Inner loops performs `OR` operation with `R3V3R53` string.


```
infile_path = "YOUR_LOCAL_PATH/ransomware3/secret.txt.pay_up"
output = ""
key_R3V3R53 = "R3V3R53"
chunk_size = 7

with open(infile_path, "rb") as f:
    while (byte := f.read(chunk_size)):
        loop_end = len(byte)
        temp_output = ""
        for i in range(0, loop_end):
            result = byte[i] ^ ord(key_R3V3R53[i])
            temp_output += chr(result)
        output += temp_output
            
    
print(output)
f = open("output-secret-3.txt", "w")
f.write(output)
f.close()

```

Output: [file location](ransom-decrypted-file/output-secret-3.txt)

```
Dear Student,

You have decrypted the message. Good job!

"A good engineer thinks in reverse and asks himself about the stylistic consequences of the components and systems he proposes."
 ~ Helmut Jahn

Go NMSU RE!

```
