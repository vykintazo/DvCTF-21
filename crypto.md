# Crypto
## Decode
We are given this to decode:
```
59'9#5$9[=S-B7S-N8W)Y<#<Q,&Y]
```
By going to CyberChef and trying Magic recipe on the string I managed to find string which contained (but also contained other gibberish) the flag:
```
dvCTF{w3b_3ncryp710n}
```

## Substution
We are given this ciphertext:
```
Rm xibkgltizksb, z hfyhgrgfgrlm xrksvi rh z nvgslw lu vmxibkgrmt rm dsrxs fmrgh lu kozrmgvcg ziv ivkozxvw drgs xrksvigvcg, zxxliwrmt gl z urcvw hbhgvn; gsv "fmrgh" nzb yv hrmtov ovggvih (gsv nlhg xlnnlm), kzrih lu ovggvih, girkovgh lu ovggvih, nrcgfivh lu gsv zylev, zmw hl uligs. Gsv ivxvrevi wvxrksvih gsv gvcg yb kviulinrmt gsv rmevihv hfyhgrgfgrlm.

Hfyhgrgfgrlm xrksvih xzm yv xlnkzivw drgs gizmhklhrgrlm xrksvih. Rm z gizmhklhrgrlm xrksvi, gsv fmrgh lu gsv kozrmgvcg ziv ivziizmtvw rm z wruuvivmg zmw fhfzoob jfrgv xlnkovc liwvi, yfg gsv fmrgh gsvnhvoevh ziv ovug fmxszmtvw. Yb xlmgizhg, rm z hfyhgrgfgrlm xrksvi, gsv fmrgh lu gsv kozrmgvcg ziv ivgzrmvw rm gsv hznv hvjfvmxv rm gsv xrksvigvcg, yfg gsv fmrgh gsvnhvoevh ziv zogvivw.

Gsviv ziv z mfnyvi lu wruuvivmg gbkvh lu hfyhgrgfgrlm xrksvi. Ru gsv xrksvi lkvizgvh lm hrmtov ovggvih, rg rh gvinvw z hrnkov hfyhgrgfgrlm xrksvi; z xrksvi gszg lkvizgvh lm ozitvi tilfkh lu ovggvih rh gvinvw klobtizksrx. Z nlmlzokszyvgrx xrksvi fhvh urcvw hfyhgrgfgrlm levi gsv vmgriv nvhhztv, dsvivzh z klobzokszyvgrx xrksvi fhvh z mfnyvi lu hfyhgrgfgrlmh zg wruuvivmg klhrgrlmh rm gsv nvhhztv, dsviv z fmrg uiln gsv kozrmgvcg rh nzkkvw gl lmv lu hvevizo klhhryrorgrvh rm gsv xrksvigvcg zmw erxv evihz.
weXGU{xi1kg3w_x1ks3i}
```
Combined with the challenge name, we can deduce that this was encrypted using substitution cipher. [dCode.fr]([dCode.fr](https://www.dcode.fr/monoalphabetic-substitution)) has a nice tool which can automatically decipher those via frequency analysis. 

And indeed it manages to decipher the text which at the end includes the flag:
```
dvCTF{cr1pt3d_c1ph3r}
```
The substitution alphabet used was querty keyboard layout in order.

## Bootless RSA
To start with, we are given RSA public key parameters $N, e$ and ciphertext $ct$:
```
N: 148818474926605063920889194160313225216327492347368329952620222220173505969004341728021623813340175402441807560635794342531823708335067243413446678485411066531733814714571491348985375389581214154895499404668547123130986872208497176485731000235899479072455273651103419116166704826517589143262273754343465721499,
e: 3, 
ct: 4207289555943423943347752283361812551010483368240079114775648492647342981294466041851391508960558500182259304840957212211627194015260673748342757900843998300352612100260598133752360374373
```
Right of the bat we can see that $e$ is very low. That's not a weakness generally, if everything else is fine. But there's another irregularity: $ct$ is much shorter than $N$ and that at least means that the message was unpadded. It also makes it possible that message was so short that it didn't even reach $N$ when exponentiated. In RSA we have
$$
ct = M^e \mod{N}
$$
if $M^e < N$ then we can simply ignore the modulus and recover the message by taking e-th root of the ciphertext:
$$
M = \sqrt[e]{ct}
$$
$e = 3$ in our case so the operation is trivial and I did it in Wolfram Alpha in this case. We get
```
M = 161436153337866397698230350131849911745245662937350542382627197
```
This can be converted to bytes in Python.
```python
161436153337866397698230350131849911745245662937350542382627197.to_bytes(a.bit_length(), byteorder='big')
```
And we get a flag:
```
dvCTF{RS4_m0dul0_inf1nity}
```

## Z340 but not 340
We are presented with a cipher very similar to those of the infamous Zodiac Killer ciphers. Actually the challenge name relates to the specific Zodiac's cipher message, Z340, which was only recently solved. We are given this ciphertext:
```
<EV>UpjM8^WGR@;pC
O4+|;:M+7#6@^c5pc
+P8bM+-T6zcp|%T2c
RCb>UN8zR35dz<#+A
4<.MY&MbB5F<B7-6+
6Oj&O@S2pNXHFD%4c
.Sj9Kp<Up*ObVYqL+
(HLM4pU/6R@tXbqL9
tH^-49V3UVM2^7fBO
```
All Zodiac's ciphers are just homophonic substitution ciphers with some twists, we can try putting this text into decoder based on actuall decrypted messages [here](https://www.dcode.fr/zodiac-killer-cipher). But we get gibberish, even when selecting Z340 option. After watching a [Youtube video](https://youtu.be/-1oQLPRE21o) about how the Z340 was cracked, it's clear that we need to transpose the message in "knight move" diagonal patter, like this:

![](cipher-explorer.png)

I wrote Python script for this. After reading everything in the new order, we reshape our grid to the same dimensions as before and we have this:

```
<+M8BXVqOE|+z5HYL
tV;-RFFq9H>:T3<DL
(^UM65B%+H-p+zd74
.L4j7cz-cSM9M#p<6
6j4V86|#+O9p3^@%+
4jKUUW^TA<&p/VGc2
R.O<6MR5cCM@UR2@p
+bYSp@^;cP>&2*t7p
O8UMpOXfC4bNbNbbB
```
And we get this after pluging it into dcode.fr:

```
IHOPEROUAREHAVING
LOTSOFFUNINTRYING
TOSODVETHISCHALLE
NGEPLEASEDONOTCID
DPEOPDETHANCYOUTH
EPASSWORDISCUOTEM
ONAIDOOVEYOUSOMUC
HENDCUOTEINSMALLC
APSOCARBYEEEEEEEE
```
which certainly looks like actual message. However it's full of mistakes:

```
I HOPE ROU ARE HAVING
LOTS OF FUN IN TRYING
TO SODVE THIS CHALLENGE PLEASE DONOT
CIDD PEOPDE THANC YOU THE PASSWORD IS
CUOTE MONA I DOOVE YOU SO MUCH END CUOTE IN SMALL CAPS OCAR
BYEEEEEEEE
```
We can go through and see that certain wrong letters always correspond to other, correct ones:
```
R-Y D-L C-K C-Q
```
after substituting those where appropriate:
```
I HOPE YOU ARE HAVING
LOTS OF FUN IN TRYING
TO SOLVE THIS CHALLENGE PLEASE DO NOT
KILL PEOPLE THANK YOU THE PASSWORD IS
QUOTE MONA I LOOVE YOU SO MUCH END QUOTE IN SMALL CAPS OKAY
BYEEEEEEEE
```
Challenge contained a notice that we need to wrap password into `dvCTF{}` to get the flag, so here it is:
```
dvCTF{monailooveyousomuch}
```