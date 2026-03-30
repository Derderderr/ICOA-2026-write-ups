## Solution

For RSA, use the brute force on attacks first:

```
import math
import requests
from fractions import Fraction

def egcd(a, b):
    if a == 0:
        return b, 0, 1
    g, y, x = egcd(b % a, a)
    return g, x - (b // a) * y, y

def modinv(a, m):
    g, x, y = egcd(a, m)
    if g != 1:
        return None
    return x % m

def long_to_bytes(n):
    return n.to_bytes((n.bit_length() + 7) // 8, 'big')


# Wiener's Attack

def continued_fraction(n, d):
    while d:
        q = n // d
        yield q
        n, d = d, n - d * q

def convergents(cf):
    n0, d0 = 1, 0
    n1, d1 = cf[0], 1
    yield (n1, d1)
    for q in cf[1:]:
        n2 = q * n1 + n0
        d2 = q * d1 + d0
        yield (n2, d2)
        n0, d0 = n1, d1
        n1, d1 = n2, d2

def is_perfect_square(n):
    s = int(math.isqrt(n))
    return s * s == n

def wiener_attack(e, n):
    cf = list(continued_fraction(e, n))
    for k, d in convergents(cf):
        if k == 0:
            continue
        if (e * d - 1) % k != 0:
            continue

        phi = (e * d - 1) // k
        b = n - phi + 1
        discr = b * b - 4 * n

        if discr >= 0 and is_perfect_square(discr):
            return d
    return None


# Trial Factorization

def trial_factor(n, limit=10**6):
    for i in range(2, limit):
        if n % i == 0:
            return i, n // i
    return None, None


# FactorDB

def factordb(n):
    url = f"http://factordb.com/api?query={n}"
    r = requests.get(url).json()

    if 'factors' not in r:
        return None

    factors = []
    for f, count in r['factors']:
        factors.extend([int(f)] * count)

    if len(factors) == 2:
        return factors[0], factors[1]

    return None


# Compute d from p, q

def compute_private_key(p, q, e):
    phi = (p - 1) * (q - 1)
    d = modinv(e, phi)
    return d

# Decrypt

def decrypt(c, d, n):
    m = pow(c, d, n)
    try:
        return long_to_bytes(m)
    except:
        return m

# Mian #

def rsa_attack(n, e, c):
    print("[*] Trying Wiener's attack")
    d = wiener_attack(e, n)
    if d:
        print("Wiener: ")
        return decrypt(c, d, n)

    print("[*] Trial factorization")
    p, q = trial_factor(n)
    if p:
        print(f"[+] Found factors: p={p}, q={q}")
        d = compute_private_key(p, q, e)
        return decrypt(c, d, n)

    print("[*] FactorDB:")
    res = factordb(n)
    if res:
        p, q = res
        print(f"[+] FactorDB found: p={p}, q={q}")
        d = compute_private_key(p, q, e)
        return decrypt(c, d, n)

    return None



if __name__ == "__main__":
    n = int(input("n: "))
    e = int(input("e: "))
    c = int(input("c: "))

    result = rsa_attack(n, e, c)
    print("\n[RESULT]")
    print(result)

```

and the answer printed is:
```
PECAN{l4rg3r_pr1m35}
```
