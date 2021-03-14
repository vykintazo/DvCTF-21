# Reversing
## Rocca Pia
We need to decompile a binary, which basically takes input and does some manipulations on it and finally checks it against another string. You are supposed to give it a flag and then it would match the latter string after manipulation.

The program code works like this:
1. Iterate through a given string byte by byte
2. if current index is even (`i & 1 == 0`), XOR it with `0x13`, else xor it with `0x37`
3. Check if resulting string matches `"wAPcULZh\x7f\x06x\x04LDd\x06~Z\"YtJ"`

So it's just XORing each char based on their index, so to solve it we can use this script:
```python
t = "wAPcULZh\x7f\x06x\x04LDd\x06~Z\"YtJ"

for i, c in enumerate(t):
    if i & 1 == 0:
        n = ord(c) ^ 0x13
    else:
        n = ord(c) ^ 0x37
    print(chr(n), end="")
print("")
```

```
dvCTF{I_l1k3_sw1mm1ng}
```

## Crackme
This binary takes your input (again, the flag) and:
1. Hashes it with MD5
2. Reverses the bytes
3. Checks against the string `"d2862c3379cbf547d317b3b1771a4fb6"`

We can simply take the string it's checking against and reverse it to get original MD5:
```
b64f1a77b1b317d347f5cb79332c86d2
```
I plugged it in [crackstation.net](https://crackstation.net/) and it found the hashed message in a second:
```
741852963
```
So the flag was:
```
dvCTF{741852963}
```

## Cryptex

This time a bit different reversing challenge. We are give Android app APK file, which when istalled asks for the password and does something with it (similarly as before).
I had some problems decompiling the APK into Java, because I can't read Smali, but I managed to do it by using [JADX](https://github.com/skylot/jadx) tool. The only relevant file is `LoginActivity.class`. It does this:
1. Takes user input and hashes it with SHA-256
2. Uses predifined key to encrypt the hash with AES ECB
3. Checks resulting ciphertext against predifined value for equality.

So we know the ciphertext and the key, so we can easily decrypt the hash. But that's only SHA-256 hash. This time I had to check multiple websites for hash cracking (brutoforcing is not an option for SHA-256 generally).

One [website](https://md5hashing.net/hash/sha256/386f72455fcdf95a9a16b3a4b7f9620ca8539b3888e9d6c885b95b4dfd21094c) managed to find the solution:

```
JohnCena
```
So the flag is
```
sdvCTF{JohnCena}
```