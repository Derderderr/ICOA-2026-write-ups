## Soultion

We recieve a png, using zsteg:
```
zsteg image.png

-->

meta application/vnd.excalidraw+json.. file: JSON data
    00000000: 7b 22 76 65 72 73 69 6f  6e 22 3a 22 31 22 2c 22  |{"version":"1","|
    00000010: 65 6e 63 6f 64 69 6e 67  22 3a 22 62 73 74 72 69  |encoding":"bstri|
    00000020: 6e 67 22 2c 22 63 6f 6d  70 72 65 73 73 65 64 22  |ng","compressed"|
    00000030: 3a 74 72 75 65 2c 22 65  6e 63 6f 64 65 64 22 3a  |:true,"encoded":|
    00000040: 22 78 9c ec bd d9 92 24  c7 91 25 fa 8e af c0 f0  |"x.....$..%.....|
    00000050: be 5c 22 62 dc 76 b3 79  23 5c 74 2e e0 d2 64 73  |.\"b.v.y#\t...ds|
    00000060: 5c 75 30 30 30 31 97 2b  23 94 5c 22 50 5c 75 30  |\u0001.+#.\"P\u0|
    00000070: 30 30 30 8a 2c a0 c0 aa  42 93 e0 48 ff fb 5c 75  |000.,...B..H..\u|
    00000080: 30 30 31 63 b5 c8 cc 70  53 cf 54 35 75 cf 24 78  |001c...pS.T5u.$x|
    00000090: af a0 d0 5c 75 30 30 30  32 a2 2b 32 4e fa 62 a6  |...\u0002.+2N.b.|
    000000a0: a6 cb d1 a3 ff e7 9d 77  df fd ce db af bf 7c fe  |.......w......|.|
    000000b0: 9d ff f5 ee 77 9e ff e3  a3 67 2f 5f 7c fc fa d9  |....w....g/_|...|
    000000c0: df bf f3 5c 75 30 30 31  65 fd fd 7f 3d 7f fd e6  |...\u001e...=...|
    000000d0: c5 ab 2f f0 91 ef ff ff  9b 57 5f bd fe a8 ff e4  |../......W_.....|
    000000e0: 67 6f df 7e f9 e6 7f fd  cf ff 79 fd c6 f9 a3 57  |go.~......y....W|
    000000f0: 9f 5f be f5 fc e5 f3 cf  9f 7f f1 f6 5c 72 7e ee  |._..........\r~.|
```
It indicates that the file has been compressed and encoded, and also tells the program, using excalidraw to open the image gives the answer:
![ans](https://github.com/Derderderr/ICOA-2026-write-ups/blob/main/images/Nothing-to-See-Here_1.png)


answer:
```
pecan{th3_sw0rd_0f_k1ng_4rthur}
```
