## Solution

we recieved a .bmc file, since it's not a standard postfix, we try identify the file first:

```
file s32.bmc

-->

s32.bmc: data


exiftool s32.bmc

-->

ExifTool Version Number         : 13.30
File Name                       : s32.bmc
Directory                       : .
File Size                       : 2.2 MB
File Modification Date/Time     : 2026:04:07 17:35:44+08:00
File Access Date/Time           : 2026:04:07 17:58:54+08:00
File Inode Change Date/Time     : 2026:04:07 17:54:34+08:00
File Permissions                : -rw-r--r--
Error                           : Unknown file type

```
so it's not a standard file, using xxd to analyse the hex headers:
```
xxd -l 64 s32.bmc

-->

00000000: 0da3 8cfb 64b7 63ca 4000 4000 0040 0000  ....d.c.@.@..@..
00000010: 1100 0000 0000 0000 0000 0000 0000 0000  ................
00000020: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000030: 0000 0000 0000 0000 0000 0000 0000 0000  ................
```

so it indicates a broken header, with a lot of padding 0s, try getting more info after padding:
```
xxd s32.bmc > hex.txt
```
opening the txt and we can see regions such as:
```
f0f0 f000 f0f0 f000 f0f0 f000 f0f0 f000
```
this suggests that group of 4 bytes might represent an image rgba pixel, so the file might be an image stored as raaw memory dump, we wanna reconstruct the image, assuming the width is 1024px as an arbitary choice first:
```
from PIL import Image

with open("s32.bmc", "rb") as f:
    data = f.read()

if len(data) % 4 != 0:
    data = data[0 : len(data) % 4] # multiple of 4

pixels = []
for i in range(0, len(data), 4):
    r, g, b, a = data[i : i+4]
    if r == 0 and g == 0 and b == 0 and a == 0: # might be useless data
        r = 255
        a = 255
    if a == 0:
        a = 255
    pixels.append((r, g, b, a))

width = 1024
height = len(pixels) // width
pixels = pixels[:width*height] # multiple of width

img = Image.new("RGBA", (width, height))
img.putdata(pixels)

img = img.resize((width * 2, height * 2), Image.NEAREST)
img.save("s32_screen.png")
```
opening the image and we can see a lot of red areas, which indicates the original data is 0,0,0,0:

![attempt_1](https://github.com/Derderderr/ICOA-2026-write-ups/blob/main/images/Threre-is-always-a-trace_1.png)

remove these data:
```
...
if r == 0 and g == 0 and b == 0 and a == 0: # might be useless data
        continue
...
```
but it's still quite messy:
![attempt_2](https://github.com/Derderderr/ICOA-2026-write-ups/blob/main/images/Threre-is-always-a-trace_2.png)

we can try with different width this time:
```
...
for width in range(16, 512, 32):
  height = len(pixels) // width
  pixels_s = pixels[:width*height] # multiple of width
  
  img = Image.new("RGBA", (width, height))
  img.putdata(pixels_s)
  
  img = img.resize((width * 2, height * 2), Image.NEAREST)
  img.save(f"s32_screen_{width}.png")
```
in the width of 64, we find some useful image, since it looks like the head of the flag upside down:
![attempt_4](https://github.com/Derderderr/ICOA-2026-write-ups/blob/main/images/Threre-is-always-a-trace_3.png)

using this script to flip the image then ressemble the parts we can get the flag:
```
from PIL import Image

img = Image.open("s32_screen_64.png")

img_hv = img.transpose(Image.FLIP_TOP_BOTTOM)
img_hv.save("s32_screen_64_hv.png")
```
![attempt_4](https://github.com/Derderderr/ICOA-2026-write-ups/blob/main/images/Threre-is-always-a-trace_4.png)


answer:
```
PECAN{RDP_C4n_r3ve4L_cOol_inFO!}
```
