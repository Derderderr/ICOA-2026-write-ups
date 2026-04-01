## Solution

We recieve a .jpg file, first use strings -n 8:
```
...
c21lbGxzbGlrZXRvbmd1ZQo=
```
There is a base64 encoded message, decode it gives "smellsliketongue", which is not the answer. Using steghide, it extracts scent.txt, including the answer

answer:
```
pecan{tastes_like_chicken}
```
