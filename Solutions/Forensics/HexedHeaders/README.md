## Solution

We recieve a jpg, trying to open it gives and error, by the question title, get the image header:
```
xxd -l 128 logo.jpg

-->

xxd -l 64 logo.jpg
00000000: ffd8 ffe0 0d0a 1a0a 0010 4a46 4946 0001  ..........JFIF..
00000010: 0101 0078 0078 0000 ffdb 0043 0003 0202  ...x.x.....C....
00000020: 0302 0203 0303 0304 0303 0405 0805 0504  ................
00000030: 0405 0a07 0706 080c 0a0c 0c0b 0a0b 0b0d  ................
```

So it's indeed a jpg header(ffd8 ffe0 0010)  with extra characters (0d0a 1a0a), removing it in txt then converting it back to jpg:
```
xxd logo.jpg > hex.txt
in hex.txt, delete 0d0a 1a0a, then save and exit

xxd -r hex.txt fixed.jpg
```

Opning the image gives the answer:
![fixed_image](https://github.com/Derderderr/ICOA-2026-write-ups/blob/main/images/HexedHeaders_1.jpg)
answer:
```
pecan{h3x_and_hea43r2}
```
