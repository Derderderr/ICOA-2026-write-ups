## Solution

We recieve two files, a .py that contains the flag generator and its output .txt, the generator looks like this:
```
from hashlib import sha256
import random

FLAG = "[REDACTED]"

def gen_flags(flag):
    flag_bytes = flag.encode()
    seed = len(flag_bytes) ** 3
    random.seed(seed)
    
    flags = []
    for i in range(seed):
        result = sha256(flag_bytes + bytes(i)).digest()
        result = bytearray(result)
        result[random.randint(0, seed) % len(result)] = flag_bytes[random.randint(0, seed) % len(flag_bytes)]
        flags.append(result.hex())
    return flags


with open("flags.txt", "w") as file:
    file.writelines(', '.join([("pecan{" + f + "}") for f in gen_flags(FLAG)]))
```

So, we know that the seed is the length flag bytes to the third power, and it would generate that much of the flags, in each generation, it would randomly replace a byte
in the random result from the sha256 to a byte in the flag, then append its hex to the list

To reverse this, first slice out the pecan{...} to leave only the hex, then turn it into bytes:
```
b = bytes.fromhex(flags[i][6:-1])
```
The bytes consists of both printable and non-printable characters, which could be difficult to identify the exact letter at a given position, change it into plaintext:
```
s = ''.join(chr(byte) if 32 <= byte <= 126 else '?' for byte in b)
```
Now, to simulate the switch in the generator, also uses the switch function then extract the random numbers being generated:
```
switch1_list = [-1] * len_result
        switch2_list = [t for t in range(0, len_flag_bytes)]
        switch1_list[random.randint(0, seed) % len_result] = switch2_list[random.randint(0, seed) % len_flag_bytes]

        for j, e in enumerate(switch1_list):
            if e != -1:
                switch1 = j
                switch2 = e
                break
```
From this, we can get one character from the flag, and we can get the answer by repeatly doing this through the entire txt file:
```
import random

with open("flags.txt", "r") as file:
    data = file.read()
    flags = data.split(', ')
    seed = len(flags)
    len_flag_bytes = round(seed ** (1/3))
    answer = '?' * len_flag_bytes

    len_result = 32 #sha256 always have length of 256 bits -> 32 bytes

    print("seed: ", seed)
    random.seed(seed)
    for i in range(round(seed / 100)):
        b = bytes.fromhex(flags[i][6:-1])
        s = ''.join(chr(byte) if 32 <= byte <= 126 else '?' for byte in b)

        switch1_list = [-1] * len_result
        switch2_list = [t for t in range(0, len_flag_bytes)]
        switch1_list[random.randint(0, seed) % len_result] = switch2_list[random.randint(0, seed) % len_flag_bytes]

        for j, e in enumerate(switch1_list):
            if e != -1:
                switch1 = j
                switch2 = e
                break
            
        answer = answer[:switch2] + s[switch1] + answer[switch2+1:] #subsitute the answer character into ?
    print(answer)
```

answer:
```
pecan{r3d_h3rr1ng_h4sh3s}
```
