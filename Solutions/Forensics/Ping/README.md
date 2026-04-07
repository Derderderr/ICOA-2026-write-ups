## Solution

We recieve a pcapng file, opening it and we can see that the protocol used is ICMP echo request and echo reply, after examination, the payload seems to be messy, 
but each payload length is repeated twice, once in request and once in reply, extract the length using tshark, using icmp.type = 8 to filter out only the request:
```
tshark -r capture.pcapng -Y "icmp.type == 8" -T fields -e frame.len

-->

154
143
141
139
152
165
142
...
```

look at the first few numbers, notice that the change in number is -11, -2, -2, -2, +13, which might correspond to 'pecan' (112 101 99 97 110),
so try shifting and convert to ASCII:

```
tshark -r capture.pcapng -Y "icmp.type == 8" -T fields -e frame.len | awk '{printf "%c", $1-42}'

-->

pecan{d4t4_3xf1ltr4t10n_thr0gh_p1ng_93485}

```

answer:
```
pecan{d4t4_3xf1ltr4t10n_thr0gh_p1ng_93485}
```
