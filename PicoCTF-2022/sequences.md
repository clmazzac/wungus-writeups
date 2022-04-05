```python
import math
import hashlib
import sys
from tqdm import tqdm
import functools

ITERS = int(2e7)
VERIF_KEY = "96cc5f3b460732b442814fd33cf8537c"
ENCRYPTED_FLAG = bytes.fromhex("42cbbce1487b443de1acf4834baed794f4bbd0dfe08b5f3b248ef7c32b")

# This will overflow the stack, it will need to be significantly optimized in order to get the answer :)
@functools.cache
def m_func(i):
    if i == 0: return 1
    if i == 1: return 2
    if i == 2: return 3
    if i == 3: return 4

    return 55692*m_func(i-4) - 9549*m_func(i-3) + 301*m_func(i-2) + 21*m_func(i-1)


# Decrypt the flag
def decrypt_flag(sol):
    sol = sol % (10**10000)
    sol = str(sol)
    sol_md5 = hashlib.md5(sol.encode()).hexdigest()

    if sol_md5 != VERIF_KEY:
        print("Incorrect solution")
        sys.exit(1)

    key = hashlib.sha256(sol.encode()).digest()
    flag = bytearray([char ^ key[i] for i, char in enumerate(ENCRYPTED_FLAG)]).decode()

    print(flag)

if __name__ == "__main__":
    sol = m_func(ITERS)
    decrypt_flag(sol)
```

as we can there there is a monstrous recursive function `m_func()` that makes this program difficult to execute
simply put in this wolfram alpha `solve f(0) = 1, f(1) = 2, f(2) = 3, f(3) = 4, f(x) = 55692*f(x-4) - 9549*f(x-3) + 301*f(x-2) + 21*f(x-1)`
and it will make it closed form for you
https://www.wolframalpha.com/input?i2d=true&i=solve+f%5C%2840%290%5C%2841%29+%3D+1%5C%2844%29+f%5C%2840%291%5C%2841%29+%3D+2%5C%2844%29+f%5C%2840%292%5C%2841%29+%3D+3%5C%2844%29+f%5C%2840%293%5C%2841%29+%3D+4%5C%2844%29+f%5C%2840%29x%5C%2841%29+%3D+55692*f%5C%2840%29x-4%5C%2841%29+-+9549*f%5C%2840%29x-3%5C%2841%29+%2B+301*f%5C%2840%29x-2%5C%2841%29+%2B+21*f%5C%2840%29x-1%5C%2841%29

`f(x) = (1612 (-21)^x + 30685 2^(2 x + 5) 3^x - 1082829 13^x + 8349 17^(x + 1))/42636` this is what Wolfram Alpha comes out with.

substitute it into the python file and run
```python
import math
import hashlib
import sys
from tqdm import tqdm
import functools

ITERS = int(2e7)
VERIF_KEY = "96cc5f3b460732b442814fd33cf8537c"
ENCRYPTED_FLAG = bytes.fromhex("42cbbce1487b443de1acf4834baed794f4bbd0dfe08b5f3b248ef7c32b")

# This will overflow the stack, it will need to be significantly optimized in order to get the answer :)
@functools.cache
def m_func(i):
    if i == 0: return 1
    if i == 1: return 2
    if i == 2: return 3
    if i == 3: return 4
    #return 55692*m_func(i-4) - 9549*m_func(i-3) + 301*m_func(i-2) + 21*m_func(i-1)
    return (1612*(-21)**i + 30685 * 2**(2*i+5) * 3**i - 1082829*13**i + 8349*17**(i+1))//42636


# Decrypt the flag
def decrypt_flag(sol):
    sol = sol % (10**10000)
    sol = str(sol)
    sol_md5 = hashlib.md5(sol.encode()).hexdigest()

    if sol_md5 != VERIF_KEY:
        print("Incorrect solution")
        sys.exit(1)

    key = hashlib.sha256(sol.encode()).digest()
    flag = bytearray([char ^ key[i] for i, char in enumerate(ENCRYPTED_FLAG)]).decode()

    print(flag)

if __name__ == "__main__":
    sol = m_func(ITERS)
    decrypt_flag(sol)
```
it can take a few seconds, but 
this is the output
```picoCTF{b1g_numb3rs_4ebc92cc}```



(sorry if you came here to learn how the math works)
