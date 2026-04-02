## Solution

We recieve a bitmap, the prompt suggest that the flag is in the Thrid LSB and in byte order, first we try extract the r, g, b, value at TLSB and concatenate to form
binary numbers that can possibily turn into ascii strings:
```
from PIL import Image

img = Image.open("TLSB.bmp").convert("RGB")
pixels = list(img.get_flattened_data())

bits = []
for pixel in pixels:
    for idx in [0, 1, 2]:
        bits.append(str((pixel[idx] >> 2) & 1)) 

chars = []
for i in range(0, len(bits), 8):
    byte = bits[i:i+8]
    byte_val = int("".join(byte), 2)
    chars.append(chr(byte_val))
result = "".join(chars)
print(result)

-->

Ø½
  RIs   È]ÌZs2P\Lae
                   RÕû#P]
=Ø :ÙôHÈ1IH?XÑ!<         À»ÝÑá  QÝ*M\]Éxz1)Á<HT3
```

The result does not look nice, so there might be a bitshift or different channels, try all possibilities:
```
from PIL import Image
from itertools import permutations

img = Image.open("TLSB.bmp").convert("RGB")
pixels = list(img.get_flattened_data())

orders = permutations([0, 1, 2])
bits = []
for pixel in pixels:
    for order in orders:
        for idx in order:
            bits.append(str((pixel[idx] >> 2) & 1)) 

    chars = []
    for bitshift in range(8):
        for i in range(bitshift, len(bits), 8):
            byte = bits[i:i+8]
            byte_val = int("".join(byte), 2)
            chars.append(chr(byte_val))
        result = "".join(chars)
        print("order: ", order, "bitshift: ", bitshift, "result: ", result)

-->

...
order:  (2, 1, 0) bitshift:  0 result:  nfQ=='F81dDN0X2IxdjFjNG5xZ24xZDV0X1M0X0wzNDVfbjB7VGg0dGVjYW5is: `c Flag ). The fun :ou hadHope y
...
```

Most result look scrambled, which is normal, but at order (2, 1, 0), bitshift 0, we can see some words and the Flag, but the order seems to be off.
Since most bitmap data is stored bottom to top, we can also read it in the same order, first determine the size of the bitmap:
```
file TLSB.bmp
-->
TLSB.bmp: PC bitmap, Windows 3.x format, 16 x 16 x 24, resolution 16 x 16 px/m, cbSize 822, bits offset 54
```
So there are 16 lines in total, and each line contains 16 * 3 = 48 bits = 6 binaries = 6 characters, instead of using for loop, we use a reverse for loop
to iterate from bottom to top:
```
from PIL import Image
from itertools import permutations

img = Image.open("TLSB.bmp").convert("RGB")
pixels = list(img.get_flattened_data())

order = [2, 1, 0] # B, G, R

# h = 16
# w = 16

bits = []
for h in reversed(range(16)):
    for w in range(16):
        for idx in order:
            bits.append(str((pixels[h * 16 + w][idx] >> 2) & 1))


bitshift = 0
chars = []
for i in range(bitshift, len(bits), 8):
    byte = bits[i:i+8]
    byte_val = int("".join(byte), 2)
    chars.append(chr(byte_val))
result = "".join(chars)
print("order: ", order, "bitshift: ", bitshift, "result: ", result)

-->

order:  [2, 1, 0] bitshift:  0 result:  Hope you had fun :). The Flag is: `cGVjYW57VGg0dDVfbjB0X0wzNDV0X1MxZ24xZjFjNG50X2IxdF81dDNnfQ=='
```
and decode the base64:
```
echo cGVjYW57VGg0dDVfbjB0X0wzNDV0X1MxZ24xZjFjNG50X2IxdF81dDNnfQ== | base64 -d

-->

pecan{Th4t5_n0t_L345t_S1gn1f1c4nt_b1t_5t3g}
```

answer:
```
pecan{Th4t5_n0t_L345t_S1gn1f1c4nt_b1t_5t3g}
```
