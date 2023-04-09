# Week 5: Binary Reverse Engineering Challenges

## Crackme1:

The content of this crackme can be divided into three significant steps:

### Step 1:

For each character of the string, the characters are set to lowercase (ASCII value) if their positions are even, and set to uppercase of their positions are odd. We generated the output by concatenating the ASCII values of the username.

![crackme1_ss1](images/crackme1_ss1.png)

### Step 2:

From line 88, we assume that the serial is 8 characters long regardless of the username size. We find the starting point of the serial number by solving the equation `(iVar2 + -8) * 2`.

![crackme1_ss2](images/crackme1_ss2.png)

### Step 3:

We clip 8 characters from the ASCII concatenated string starting from the result of the previous step. As the username is limited to 8-12 characters, we choose:

- position 0-7 is the username is 8-character long
- position 2-9 is the username is 9-character long
- position 4-11 is the username is 10-character long
- position 6-13 is the username is 11-character long
- position 8-15 is the username is 12-character long

### Keygen Code:

```
username = 'aaaaaaaaaaa'
if len(username) < 8 or len(username) > 12:
    print('username must be between 8 and 12!')
else:
    result = ''
    for i in range(0, len(username)):
        if i & 1:
            result += str(ord(username[i].upper()))
        else:
            result += str(ord(username[i].lower()))
    
    #print("result: " + result)
    usernamelength = (len(username) - 8) * 2
    serial = result[usernamelength:usernamelength+8]
    
    print("serial number: " + serial)

```








