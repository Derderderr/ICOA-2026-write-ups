## Solution

We recieve:
```
ZnJpcmEgbyBmY25wciBmcmlyYSBhdmFyIGZjbnByIGZ2ayBzIGZjbnByIGZyaXJhIHN2aXIgZmNucHIgZ2piIG1yZWIgZmNucHIgZnZrIGZyaXJhIGZjbnByIGZ2ayBzIGZjbnByIGZyaXJhIHNiaGUgZmNucHIgZ2piIG1yZWIgZmNucHIgZnJpcmEgc2JoZSBmY25wciBmdmsgcnZ0dWcgZmNucHIgZnZrIGF2YXIgZmNucHIgZnJpcmEgZ3VlcnIgZmNucHIgZnJpcmEgcQ==
```

Notice the == at the end, decode base64:
```
echo "ZnJpcmEgbyBmY25wciBmcmlyYSBhdmFyIGZjbnByIGZ2ayBzIGZjbnByIGZyaXJhIHN2aXIgZmNucHIgZ2piIG1yZWIgZmNucHIgZnZrIGZyaXJhIGZjbnByIGZ2ayBzIGZjbnByIGZyaXJhIHNiaGUgZmNucHIgZ2piIG1yZWIgZmNucHIgZnJpcmEgc2JoZSBmY25wciBmdmsgcnZ0dWcgZmNucHIgZnZrIGF2YXIgZmNucHIgZnJpcmEgZ3VlcnIgZmNucHIgZnJpcmEgcQ==" | base64 -d

-->

frira o fcnpr frira avar fcnpr fvk s fcnpr frira svir fcnpr gjb mreb fcnpr fvk frira fcnpr fvk s fcnpr frira sbhe fcnpr gjb mreb fcnpr frira sbhe fcnpr fvk rvtug fcnpr fvk avar fcnpr frira guerr fcnpr frira q
```

Notice the repeating words and short phrases, try using ROT13:
```
echo "frira o fcnpr frira avar fcnpr fvk s fcnpr frira svir fcnpr gjb mreb fcnpr fvk frira fcnpr fvk s fcnpr frira sbhe fcnpr gjb mreb fcnpr frira sbhe fcnpr fvk rvtug fcnpr fvk avar fcnpr frira guerr fcnpr frira q" | tr 'A-Za-z' 'N-ZA-Mn-za-m'

-->

seven b space seven nine space six f space seven five space two zero space six seven space six f space seven four space two zero space seven four space six eight space six nine space seven three space seven d
```

Either manually typing out, or using a script:
```
dict = {
    "zero": 0,
    "one": 1,
    "two": 2,
    "three": 3,
    "four": 4,
    "five": 5,
    "six": 6,
    "seven": 7,
    "eight": 8,
    "nine": 9,
    "space": ' ',
}

def words_to_letter(words):
    result = ''
    for word in words:
        if word in dict:
            result += str(dict[word])
        elif word.islower():
            result += word
        else:
            result += '?'
    return result

if __name__ == "__main__":
    words = input("words:").split()
    letter = words_to_letter(words)
    print(letter)

-->

7b 79 6f 75 20 67 6f 74 20 74 68 69 73 7d
```

decode the hex string:
```
echo "7b796f7520676f7420746869737d" | xxd -r -p

-->

{you got this}
```
answer:
```
pecan{you got this}
```
