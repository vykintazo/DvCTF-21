# Stego
## Read
We are present with an image file

![](flag.png)

If you happen to play around with stereograms/magic eye pictures in the past, you would probably notice this is one of those.
You can try looking at it and you might be able to see letters poping out. However they're very difficult to read because of the background.
I used [this](https://magiceye.ecksdee.co.uk/) website to solve it.
```
dvCTF{th4t5_4_l0t_0f_n0153}
```
## Pidieff
This challenge gives you a PDF file. That file contains two pages of random boring text. However, as my PDF viewer Okular notified me, it contained embedded (as allowed per PDF standard) file as well. That file was `flag.txt` and it contained a flag.
```
dvCTF{look_at_the_files}
```

## Da Vinci vs Pacioli
Here we are presented with a PGN file with a chess game and locked ZIP file.
It took me a while to get the password from that PGN. After looking at the game itself, the only really obvious thing is that the game and it moves do not make sense a all. So I googled "chess steganography" and found [this site](https://incoherency.co.uk/chess-steg/). I've got ZIP password there.

Now, the ZIP contained two files, `keys.kdbx` and `nothing_to_see_here`. The first one can be opened with password manager KeePass, but it requires a master key to decrypt.
Looking at `nothing_to_see_here` in hex editor, it's easy to notice that it seems to contain *a lot* of blocks of just bytes between `0x00` and `0xff`. However by looking at the very end of the file, those blocks are seemingly shifted, suggesting that somewhere in the file there's some other bytes, which mess up the neat order of other blocks.

I just manually carefully scrolled up and down until I found the bytes. They were all printable, but didn't fit as the password for KeePass. After noticing that all characters are 0-9 or a-f I assumed it's actually hex and converted it to ASCII and found what was clearly a password.

In the KeePass I found multiple accounts and their password. Challenge asked password for a flag, so I tried a few and one (from `DigiTeam` account) worked:
```
dvCTF{MQNFZ0VPGDsfeQCvudeX}
```