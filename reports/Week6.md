# Binary Reverse Engineering with Crackmes

## ezcrackme1:

For this crackme, the password can be easily extracted using `uftrace`. 
Steps:
- Open the program using this command `uftrace -a ./easy_crackme_1`.
- Enter a random string and hit enter.
- Unless the password was entered correctly by chance, the output would show the text “Your password <password> was incorrect. Time for some more RE….” along with the source code where the methods and arguments are clearly visible.
- An `strcmp` function is present in the source code and it is comparing the user input with the `picklecucumber1337` string - which is the correct password.

## ezcrackme2:

This crackme can also be solved following the same way as the previous crackme. In this case, the password is `artificialtree`.

## ezcrackme3:

To solve this crackme, if we use the same approach as we have done for the previous two, we can see notable C/C++ library functions `strcat` and `strcmp`. The `strcat` concatenates two strings and `strcmp` compares two strings to see if they are matched or not. The `strcmp` function has been used three times with the user input, with these combinations - `strawberry`, `kiwi`, and  `strawberrykiwi`. We may guess one of these three strings would be the correct password but let's open the program using `ghidra` to understand to take a deeper look.

We get the following information with `ghidra` in the main function:
- `getline` function takes user input.
- `strcat` has been used twice and the string `strawberrykiwi` is stored in a variable.
- The first comparison result with `kiwi` and user input is stored in a variable. 
- The second comparison with `strawberrykiwi` and user input stored in a variable. This value gets manipulated later by storing the result of `strawberry` and user input in the same variable. Before manipulating the value, another boolean value is used for tracking the comparison result of `strawberrykiwi` and user input (if boolean is `False`, the user input and `strawberrykiwi` are equal)
- The next part shows an `if` statement with multiple `or`s, where each will be false only if the user input is `strawberrykiwi`. Hence we prove our initial assumption and find out the correct password.


## Control Flow 1:

All the control flow problems start from `main` and have to land on the sink `win` function to crack the password. We can start inspecting the code from `main` and keep going through `rock`, `paper`, `scissors`, `lizard`, `spock` and finally reach the function `win`. As we start inspecting the code, we can write the rules along the way:

- `main`: password is more than 16 characters.
- `rock`: `input[3]` is ‘2’.
- `paper`: `input[7]` is `%` by guessing `input[7] = 37` so that `input[7] - 37` equals 0 which leads us to bypass the `uVar1 & 1 = 0` if statement.
- `scissors`: The if statement in the middle of the code shows that `input[0]` has to be `A` (65) to go to the next function.
- `lizard`: `switch` case value needs to be `6` which is the value of `input[1]`
- `spock`: another `switch` statement to set the value of `input[15]` to `*` and reach the `win` function.

### Keygen code:

```
import string
import random

def password_generator(size=16, chars=string.ascii_uppercase + string.ascii_lowercase + string.digits):
	return ''.join(random.choice(chars) for _ in range(size))

password_list = []

# generate 10 password
for i in range(0, 10):
	password = password_generator()
	ls_password = list(password)
	ls_password[0] = 'A'
	ls_password[1] = '6'
	ls_password[3] = '2'
	ls_password[7] = '%'
	ls_password[15] = '*'
	password = ''.join(ls_password)
	password_list.append(password)

print("List of some valid passwords [min 16 chars]: ")
for j in range(0, 10):
	print(password_list[j] + " [+ anything else you want]")


```

## Control Flow 2:

### Keygen code:

```
import string
import random

def password_generator(size=16, chars=string.ascii_uppercase + string.ascii_lowercase + string.digits):
	return ''.join(random.choice(chars) for _ in range(size))

password_list = []

# generate 10 password
for i in range(0, 10):
	password = password_generator()
	ls_password = list(password)
	ls_password[6] = 'Y'
	ls_password[8] = '#'
	ls_password[10] = 'A'
	ls_password[11] = '*'
	ls_password[13] = '6'
	password = ''.join(ls_password)
	password_list.append(password)

print("List of some valid passwords [min 16 chars]: ")
for j in range(0, 10):
	print(password_list[j] + " [+ anything else you want]")


```

## Control Flow 3:

### Keygen code:

```
import string
import random

# # # # # # # # # # # # # # # # # # 
# Some working passwords from a run:
# # # # # # # # # # # # # # # # # # 

# password: Co4msplm60FfFTbX
# password: 2ytlUupp5r8N8jAL
# password: TrUF8uCCajrvrfPV
# password: Bxo1zr75cDDSDd5t
# password: ckFIBAsrH1YKY7fr
# password: fsFKYPnlK2OcOygW

# # # # # # # 
# Draft rules:
# # # # # # # 

# rule 1: password[1] + password[3] - password[5] == password[6]
# rule 2: password[6] ^ password[7] < 3
# rule 3: password[10] == password[12]
# rule 4: password[8] ^ password[7] > 4 
# rule 5: password[8] != password[9]
# rule 6: password[12] ^ password[8] ^ password[9] == password[10] > 3

def get_single_char(size=1, chars=string.ascii_uppercase + string.ascii_lowercase + string.digits):
    return ''.join(random.choice(chars) for _ in range(size))

def valid_password(password):
    if((ord(password[1]) + ord(password[3]) - ord(password[5]) == ord(password[6])) and 
    (ord(password[6]) ^ ord(password[7]) < 3) and
    (ord(password[10]) == ord(password[12])) and
    (ord(password[8]) ^ ord(password[7]) > 4) and
    (ord(password[8]) != ord(password[9])) and
    (ord(password[12]) ^ ord(password[8]) ^ ord(password[9]) == ord(password[10]) > 3)):
        return True
    else:
        return False

def get_char_at_8(char_at_7):
    print('char_at_7: ' + char_at_7)
    for i in range(0, 100):
        char_at_8 = str(get_single_char())
        if (ord(char_at_8) ^ ord(char_at_7) > 4):
            return char_at_8
    return None

def get_char_at_9(char_at_10, char_at_8):
    print('char_at_10: ' + char_at_10)
    print('char_at_8: ' + char_at_8)
    for i in range(0, 100):
        char_at_9 = str(get_single_char())
        if (ord(char_at_10) ^ ord(char_at_8) ^ ord(char_at_9) != (ord(char_at_10) < 3) and (ord(char_at_8) != ord(char_at_9))):
            return char_at_9
    return None

def get_char_at_6(char_at_7):
    print('char_at_7: ' + char_at_7)
    for i in range(0, 100):
        char_at_6 = str(get_single_char())
        if (ord(char_at_6) ^ ord(char_at_7) < 3):
            return char_at_6
    return None

def get_char_at_1_3_5(char_at_6):
    print('char_at_6: ' + char_at_6)
    for i in range(0, 100):
        char_at_1 = get_single_char()
        char_at_3 = get_single_char()
        char_at_5 = get_single_char()
        if (ord(char_at_1) + ord(char_at_3) - ord(char_at_5) == ord(char_at_6)):
            return char_at_1, char_at_3, char_at_5
    return None

for i in range(0, 100):
    try:
        char_at_10 = str(get_single_char())
        char_at_12 = char_at_10
        char_at_7 = str(get_single_char())
        char_at_8 = get_char_at_8(char_at_7)
        char_at_9 = get_char_at_9(char_at_10, char_at_8)
        print('char_at_9: ' + str(char_at_9))
        char_at_6 = get_char_at_6(char_at_7)
        char_at_1, char_at_3, char_at_5 = get_char_at_1_3_5(char_at_6)
        char_at_0 = str(get_single_char())
        char_at_2 = str(get_single_char())
        char_at_4 = str(get_single_char())
        char_at_11 = str(get_single_char())
        char_at_13 = str(get_single_char())
        char_at_14 = str(get_single_char())
        char_at_15 = str(get_single_char())
        password = char_at_0 + char_at_1 + char_at_2 + char_at_3 + char_at_4 + char_at_5 + char_at_6 + char_at_7 + char_at_8 + char_at_9 + char_at_10 + char_at_11 + char_at_12 + char_at_13 + char_at_14 + char_at_15 
        print('password: ' + password)
    except:
        print("An error occured! Trying again...")


```


