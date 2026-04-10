## Solution 

we recieve a png, this is Cistercian Number System (I found out by reverse image search the image on Google, which matches similar images that corresponds to this 
crypto challenge):

![Cistercian_Number_System](https://github.com/Derderderr/ICOA-2026-write-ups/blob/main/images/That's-Not-My-Unicode_1.png)

By corresponding the symbols, we can get the sequence to be:
```
99 71 86 106 89 87 53 55 86 87 52 120 89 122
66 107 77 49 56 48 109 82 102 81 122 69 49 100 68 78 121
89 122 70 104 98 105 66 102 82 110 86 117 102 81 61 61
```
the last 2 '61' correspond to '=' in ascii, which gives away the next layer is base64 encoded, so:
```
99 71 86 106 89 87 53 55 86 87 52 102 89
66 107 77 49 56 48 109 82 102 81 122 69 49 100 68 78 121
89 122 70 104 98 105 66 102 82 110 86 117 102 81 61 61

-->

cGVjYW57VW4fYBkM180mRfQzE1dDNyYzFhbiBfRnVufQ==

-->


```
