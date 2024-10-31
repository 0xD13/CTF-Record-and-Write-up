# sekur julius

## encrypt code

```python
from random import choices

def julius_encrypt(msg, shift):
    ct = ''
    for p in msg:
        if p == ' ':
            ct += '0'
        elif not ord('A') <= ord(p) <= ord('Z'):
            ct += p
        else:
            o = ord(p) - 65
            ct += chr(65 + (o + shift) % 26)
    return ct

def encrypt(msg, key):
    for shift in key:
        msg = julius_encrypt(msg, shift)
    return msg

msg = open('secret.txt').read().upper()
secure_key = os.urandom(1337)

with open('output.txt', 'w') as f:
    f.write(encrypt(msg, secure_key))
```

## Solution

題目有給加密程式，看得出來只是字母置換，就是[替換式加密法](https://zh.wikipedia.org/zh-tw/ROT13)，因為題目只限定全大寫而已，代表轉換的可能性只有26種，使用轉換工具 [rot13.com](https://rot13.com/) 把每種可能都試試看就可以找到答案了