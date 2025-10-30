# 1. Custom encryption:

Can you get sense of this code file and write the function that will decode the given encrypted file content.

Given encrypted file:
```
a = 94
b = 29
cipher is: [260307, 491691, 491691, 2487378, 2516301, 0, 1966764, 1879995, 1995687, 1214766, 0, 2400609, 607383, 144615, 1966764, 0, 636306, 2487378, 28923, 1793226, 694152, 780921, 173538, 173538, 491691, 173538, 751998, 1475073, 925536, 1417227, 751998, 202461, 347076, 491691]
```

Given python source code:
```
from random import randint
import sys


def generator(g, x, p):
    return pow(g, x) % p


def encrypt(plaintext, key):
    cipher = []
    for char in plaintext:
        cipher.append(((ord(char) * key*311)))
    return cipher


def is_prime(p):
    v = 0
    for i in range(2, p + 1):
        if p % i == 0:
            v = v + 1
    if v > 1:
        return False
    else:
        return True


def dynamic_xor_encrypt(plaintext, text_key):
    cipher_text = ""
    key_length = len(text_key)
    for i, char in enumerate(plaintext[::-1]):
        key_char = text_key[i % key_length]
        encrypted_char = chr(ord(char) ^ ord(key_char))
        cipher_text += encrypted_char
    return cipher_text


def test(plain_text, text_key):
    p = 97
    g = 31
    if not is_prime(p) and not is_prime(g):
        print("Enter prime numbers")
        return
    a = 94
    b = 29
    print(f"a = {a}")
    print(f"b = {b}")
    u = generator(g, a, p)
    v = generator(g, b, p)
    key = generator(v, a, p)
    b_key = generator(u, b, p)
    shared_key = None
    if key == b_key:
        shared_key = key
    else:
        print("Invalid key")
        return
    semi_cipher = dynamic_xor_encrypt(plain_text, text_key)
    cipher = encrypt(semi_cipher, shared_key)
    print(f'cipher is: {cipher}')


if __name__ == "__main__":
    message = sys.argv[1]
    test(message, "trudeau")
```

## Solution:

- Include as many steps as you can with your thought process
- You **must** include images such as screenshots wherever relevant.

Python code to decrypt:
```
from random import randint

def generator(g, x, p):
    return pow(g, x) % p

def is_prime(p):
    v = 0
    for i in range(2, p + 1):
        if p % i == 0:
            v = v + 1
    return v <= 1

def dynamic_xor_encrypt(plaintext, text_key):
    cipher_text = ""
    key_length = len(text_key)
    for i, char in enumerate(plaintext[::-1]):
        key_char = text_key[i % key_length]
        encrypted_char = chr(ord(char) ^ ord(key_char))
        cipher_text += encrypted_char
    return cipher_text

def dynamic_xor_decrypt(cipher_text, text_key):
    # XOR again with the same key to decrypt, then reverse
    decrypted_reversed = ""
    key_length = len(text_key)
    for i, char in enumerate(cipher_text):
        key_char = text_key[i % key_length]
        decrypted_reversed += chr(ord(char) ^ ord(key_char))
    return decrypted_reversed[::-1]

def decrypt(cipher, text_key):
    p = 97
    g = 31
    a = 94
    b = 29

    u = generator(g, a, p)
    v = generator(g, b, p)
    key = generator(v, a, p)
    b_key = generator(u, b, p)

    if key != b_key:
        print("Invalid key exchange.")
        return None

    shared_key = key
    factor = shared_key * 311

    # Step 1: Undo multiplication
    semi_cipher = ''.join([chr(int(c // factor)) for c in cipher])

    # Step 2: Undo XOR encryption
    plaintext = dynamic_xor_decrypt(semi_cipher, text_key)

    return plaintext



if __name__ == "__main__":
    
    cipher = [260307, 491691, 491691, 2487378, 2516301, 0, 1966764, 1879995, 1995687, 1214766, 0, 2400609, 607383, 144615, 1966764, 0, 636306, 2487378, 28923, 1793226, 694152, 780921, 173538, 173538, 491691, 173538, 751998, 1475073, 925536, 1417227, 751998, 202461, 347076, 491691]  # <-- replace with your cipher list
    text_key = "trudeau"
    plaintext = decrypt(cipher, text_key)
    print(f"Flag: {plaintext}")
```

## Flag:

```
picoCTF{custom_d2cr0pt6d_751a22dc}
```

## Concepts learnt:

- I learnt that we can make our own custom encryptions if we want to. I also learnt how to figure out what algorithm they used to encrypt the message in python, and how we can write our own code to decode it. 



## Resources:

- https://w3schools.com/python/
- https://www.youtube.com/playlist?list=PLBlnK6fEyqRhBsP45jUdcqBivf25hyVkU
- https://www.geeksforgeeks.org/python/python-functions/
- https://www.programiz.com/python-programming/function

***

# 2. Mini RSA: 

What happens if you have a small exponent? There is a twist though, we padded the plaintext so that (M ** e) is just barely larger than N. Let's decrypt this: ciphertext

The cipher text:
```
N: 1615765684321463054078226051959887884233678317734892901740763321135213636796075462401950274602405095138589898087428337758445013281488966866073355710771864671726991918706558071231266976427184673800225254531695928541272546385146495736420261815693810544589811104967829354461491178200126099661909654163542661541699404839644035177445092988952614918424317082380174383819025585076206641993479326576180793544321194357018916215113009742654408597083724508169216182008449693917227497813165444372201517541788989925461711067825681947947471001390843774746442699739386923285801022685451221261010798837646928092277556198145662924691803032880040492762442561497760689933601781401617086600593482127465655390841361154025890679757514060456103104199255917164678161972735858939464790960448345988941481499050248673128656508055285037090026439683847266536283160142071643015434813473463469733112182328678706702116054036618277506997666534567846763938692335069955755244438415377933440029498378955355877502743215305768814857864433151287
e: 3

ciphertext (c): 1220012318588871886132524757898884422174534558055593713309088304910273991073554732659977133980685370899257850121970812405700793710546674062154237544840177616746805668666317481140872605653768484867292138139949076102907399831998827567645230986345455915692863094364797526497302082734955903755050638155202890599808147276605782889813772992918898400408026067642464141885067379614918437023839625205930332182990301333585691581437573708925507991608699550931884734959475780164693178925308303420298715231388421829397209435815583140323329070684583974607064056215836529244330562254568162453025117819569708767522400676415959028292550922595255396203239357606521218664984826377129270592358124859832816717406984802489441913267065210674087743685058164539822623810831754845900660230416950321563523723959232766094292905543274107712867486590646161628402198049221567774173578088527367084843924843266361134842269034459560612360763383547251378793641304151038512392821572406034926965112582374825926358165795831789031647600129008730
```

## Solution:

- Include as many steps as you can with your thought process
- You **must** include images such as screenshots wherever relevant.

Python code: 
```
# RSA low exponent (e = 3) cube root attack

N = 1615765684321463054078226051959887884233678317734892901740763321135213636796075462401950274602405095138589898087428337758445013281488966866073355710771864671726991918706558071231266976427184673800225254531695928541272546385146495736420261815693810544589811104967829354461491178200126099661909654163542661541699404839644035177445092988952614918424317082380174383819025585076206641993479326576180793544321194357018916215113009742654408597083724508169216182008449693917227497813165444372201517541788989925461711067825681947947471001390843774746442699739386923285801022685451221261010798837646928092277556198145662924691803032880040492762442561497760689933601781401617086600593482127465655390841361154025890679757514060456103104199255917164678161972735858939464790960448345988941481499050248673128656508055285037090026439683847266536283160142071643015434813473463469733112182328678706702116054036618277506997666534567846763938692335069955755244438415377933440029498378955355877502743215305768814857864433151287
e = 3
c = 1220012318588871886132524757898884422174534558055593713309088304910273991073554732659977133980685370899257850121970812405700793710546674062154237544840177616746805668666317481140872605653768484867292138139949076102907399831998827567645230986345455915692863094364797526497302082734955903755050638155202890599808147276605782889813772992918898400408026067642464141885067379614918437023839625205930332182990301333585691581437573708925507991608699550931884734959475780164693178925308303420298715231388421829397209435815583140323329070684583974607064056215836529244330562254568162453025117819569708767522400676415959028292550922595255396203239357606521218664984826377129270592358124859832816717406984802489441913267065210674087743685058164539822623810831754845900660230416950321563523723959232766094292905543274107712867486590646161628402198049221567774173578088527367084843924843266361134842269034459560612360763383547251378793641304151038512392821572406034926965112582374825926358165795831789031647600129008730


#  integer nth root finder 
def integer_nth_root(x, n):
    """Find the integer component of the real n-th root of x (floor)."""
    if x == 0:
        return 0
    if x < 0 and n % 2 == 0:
        raise ValueError("even root of negative number")

    low, high = 0, x
    while low < high:
        mid = (low + high) // 2
        mid_n = pow(mid, n)
        if mid_n < x:
            low = mid + 1
        elif mid_n > x:
            high = mid
        else:
            return mid
    return low - 1


# integer to bytes
def long_to_bytes(n):
    """Convert an integer into a byte string (big endian)."""
    length = (n.bit_length() + 7) // 8
    return n.to_bytes(length, 'big')


# main search 
for i in range(10000):
    m = integer_nth_root(i * N + c, e)
    flag = long_to_bytes(m)
    if b"pico" in flag:
        print(flag)
        break
```

## Flag:

```
picoCTF{e_sh0u1d_b3_lArg3r_85d643d5}'
```

## Concepts learnt:

- Include the new topics you've come across and explain them in brief
- 

## Notes:

- Include any alternate tangents you went on while solving the challenge, including mistakes & other solutions you found.

## Resources:

- https://w3schools.com/python/
- https://www.geeksforgeeks.org/python/python-functions/
- https://www.programiz.com/python-programming/function
- https://amsghimire.medium.com/low-exponent-attack-511bf5d227fc
- https://www.youtube.com/watch?v=2xHb9Fxa22w
