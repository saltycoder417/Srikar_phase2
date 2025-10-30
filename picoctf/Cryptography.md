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

- They gave a python script where they first generated a key, then encrypted the cipher first with the xor function and then with the encrypt function.
- So we had to make a code to first decrypt the encrypted function and then the xor function.
- They also gave us the values of a and b. so we can remove the random generator that they gave in the source code.
- With the below python code, we can decode the cipher and get the flag. 

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

- From the challenge name and also the files they gave, we can understand that they used RSA algorithm to encrypt the code.
- From one of the hint they gave, which was what will happend if the value of e is small, I searched up about that and found that if the value of exponent, which is e in this case is small, then it is vulnerable to attacks especially the cube root attack.
- We first have to find the value of M to keep it in the for range by brute force, I first kept 10000 but it didnt work. Then I kept 5000 and it worked.
- I We have to convert the integer to bytes and then to string. Then whenever we find pico, we have to print that part as the flag will start with that.

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

- I learnt about RSA encryption, which is a asymmetric encryption where the encryption can be done with the public key, but decryption can only be done with the private key. There are mathematical formulas for encryption and decryption.
- I also learnt how RSA is vulnerable when we use weak keys. Like in this challenge, they value of the exponent was small, which made the encryption weak and we could do a cube root attack. 

## Notes:

- I made mistakes while making the python code as the formulas used in RSA and the numbers are pretty big, so we have to be careful.

## Resources:

- https://w3schools.com/python/
- https://www.geeksforgeeks.org/python/python-functions/
- https://www.programiz.com/python-programming/function
- https://amsghimire.medium.com/low-exponent-attack-511bf5d227fc
- https://www.youtube.com/watch?v=2xHb9Fxa22w

***

# 3. RSA Oracle:

Can you abuse the oracle?

An attacker was able to intercept communications between a bank and a fintech company. They managed to get the message (ciphertext) and the password that was used to encrypt the message.

After some intensive reconassainance they found out that the bank has an oracle that was used to encrypt the password and can be found here nc titan.picoctf.net 59904. Decrypt the password and use it to decrypt the message. The oracle can decrypt anything except the password.

Hints:

- Crytography Threat models: chosen plaintext attack.
- OpenSSL can be used to decrypt the message. e.g openssl enc -aes-256-cbc -d ...
- The key to getting the flag is by sending a custom message to the server by taking advantage of the RSA encryption algorithm.
- Minimum requirements for a useful cryptosystem is CPA security.


## Solution:

- Since they mentioned chosen plaintext attack in the hints, I first looked up about it, and i learnt that we can use the oracle by sending our own messages to encrypt and decrypt and try using the RSA algorithm to find out the password. Basically we send multiple arguments to encrypt or decrypt.
- I also read somehwere that RSA has homomorphic property, which is basically you can multiply two ciphertexts together to get a new ciphertext that, when decrypted, is the product of the original plaintexts. However this is only for multiplication, not addition or division. So using this property, we can make a code to calculate and find the password, since calculating such large numbers manually will be impossible.
- After we get the password, they gave in the hint that we can use OpenSSL to decrypt the message. So i used the OpenSSL command on the message file with the password we decrypted and got the flag. 

Python code to get password:
```
from subprocess import run, PIPE  
  
c = 1765037049764047724348114634473658734830490852066061345686916365658618194981097216750929421734812911680434647401939068526285652985802740837961814227312100
  
  
print(f"c = {c}\n")  
 
# Enter whatever u want, just keep the same thing in the oracle. 
m1 = input("Enter message (m1): ")  
m1_bytes = bytes(m1, "utf-8")  
m1_int = ord(m1_bytes) 
  
print(f"Have the oracle encrypt this message (m1): {m1}\n")  
c1 = int(input("Enter ciphertext from oracle (c1 = E(m1)): "))  
print("\n")  
 
# Exploit the homomorphic property of RSA
c2 = c * c1  
print(f"Have the oracle decrypt this message (c2 = c * c1): {c2}\n")  
  
m2 = int(input("Enter decrypted ciphertext as HEX (m2 = D(c2): "), 16)  
print("\n")  
 
# Exploit the homomorphic property of RSA some more
m_int = m2 // m1_int  
m = m_int.to_bytes(len(str(m_int)), "big").decode("utf-8").lstrip("\x00")
print(f"Password (m = m2 / m1): {m}\n")  
  
print("-" * 50) 
```
Code terminal: 
```
c = 1765037049764047724348114634473658734830490852066061345686916365658618194981097216750929421734812911680434647401939068526285652985802740837961814227312100

Enter message (m1): a
Have the oracle encrypt this message (m1): a

Enter ciphertext from oracle (c1 = E(m1)): 1894792376935242028465556366618011019548511575881945413668351305441716829547731248120542989065588556431978903597240454296152579184569578379625520200356186


Have the oracle decrypt this message (c2 = c * c1): 3344378746901187057733146330417154614967897092097381232820769771567582019249609372563765215104944867176199885929270022282129502059626996351942254590865021586025981355523819624411257309507460128244944233368442952118580893027261393807751298523497094518989597199047515930901371746729140121027277016789387650600

Enter decrypted ciphertext as HEX (m2 = D(c2): 154d4ab6f999


Password (m = m2 / m1): 881d9
```
Ubuntu terminal for exploiting Oracle and getting flag:
```
saltycoder@LAPTOP-LP81GQQE:~$ nc titan.picoctf.net 55043
*****************************************
****************THE ORACLE***************
*****************************************
what should we do for you?
E --> encrypt D --> decrypt.
E
enter text to encrypt (encoded length must be less than keysize): a
a

encoded cleartext as Hex m: 61

ciphertext (m ^ e mod n) 1894792376935242028465556366618011019548511575881945413668351305441716829547731248120542989065588556431978903597240454296152579184569578379625520200356186

what should we do for you?
E --> encrypt D --> decrypt.
D
Enter text to decrypt: 3344378746901187057733146330417154614967897092097381232820769771567582019249609372563765215104944867176199885929270022282129502059626996351942254590865021586025981355523819624411257309507460128244944233368442952118580893027261393807751298523497094518989597199047515930901371746729140121027277016789387650600
decrypted ciphertext as hex (c ^ d mod n): 154d4ab6f999
decrypted ciphertext: MJ¶ù

what should we do for you?
E --> encrypt D --> decrypt.
^C
//We got the password by here. 
saltycoder@LAPTOP-LP81GQQE:/mnt/d/temp$ openssl enc -aes-256-cbc -d -in secret.enc -k 881d9
*** WARNING : deprecated key derivation used.
Using -iter or -pbkdf2 would be better.
picoCTF{su((3ss_(r@ck1ng_r3@_881d93b6}
```

## Flag:

```
picoCTF{su((3ss_(r@ck1ng_r3@_881d93b6}
```

## Concepts learnt:

- I learnt about the chosen plaintext attack, which is basically where an attacker can select specific plaintexts and obtain the corresponding ciphertexts, which they then use to analyze and break the encryption scheme.
- I also learnt that RSA has homomorphic property for multiplication, which means we can multiply two ciphertexts together to get a new ciphertext that, when decrypted, is the product of the original plaintexts.


## Resources:

- https://en.wikipedia.org/wiki/Chosen-plaintext_attack
- https://taylorandfrancis.com/knowledge/Engineering_and_technology/Computer_science/Chosen-plaintext_attack/
- https://crypto.stackexchange.com/questions/3555/homomorphic-cryptosystems-in-rsa
- https://dualitytech.com/blog/what-is-homomorphic-encryption/
- https://www.youtube.com/watch?v=r8psTgL4K4M
