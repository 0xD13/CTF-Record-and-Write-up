# sugar free candies

## Solution

題目有給加密的 code，再寫一份解碼的就可以了

### Encrypt code

```python
from Crypto.Util.number import bytes_to_long

FLAG = open("flag.txt", "rb").read()

step = len(FLAG) // 3
candies = [bytes_to_long(FLAG[i:i+step]) for i in range(0, len(FLAG), step)]

cnd1, cnd2, cnd3 = candies

with open('output.txt', 'w') as f:
    f.write(f'v1 = {cnd1**3 + cnd3**2 + cnd2}\n')
    f.write(f'v2 = {cnd2**3 + cnd1**2 + cnd3}\n')
    f.write(f'v3 = {cnd3**3 + cnd2**2 + cnd1}\n')
    f.write(f'v4 = {cnd1 + cnd2 + cnd3}\n')
```

### Decrypt code

```python
from sympy import symbols, Eq, solve

# 讀取 output.txt 中的數據
with open('output.txt', 'r') as f:
    lines = f.readlines()

# 解析出 v1, v2, v3, v4 的值
v1 = int(lines[0].split('=')[1].strip())
v2 = int(lines[1].split('=')[1].strip())
v3 = int(lines[2].split('=')[1].strip())
v4 = int(lines[3].split('=')[1].strip())

# 使用 SymPy 來定義未知數
cnd1, cnd2, cnd3 = symbols('cnd1 cnd2 cnd3')

# 創建方程
equation1 = Eq(cnd1**3 + cnd3**2 + cnd2, v1)
equation2 = Eq(cnd2**3 + cnd1**2 + cnd3, v2)
equation3 = Eq(cnd3**3 + cnd2**2 + cnd1, v3)
equation4 = Eq(cnd1 + cnd2 + cnd3, v4)

# 解方程
solution = solve([equation1, equation2, equation3, equation4], (cnd1, cnd2, cnd3))

# 解析出解的值
if solution:
    cnd1_val, cnd2_val, cnd3_val = solution[0]
    print(f"cnd1 = {cnd1_val}, cnd2 = {cnd2_val}, cnd3 = {cnd3_val}")

    # 根據解重新組合成 FLAG（長整數轉換為字節）
    from Crypto.Util.number import long_to_bytes
    
    flag_part1 = long_to_bytes(cnd1_val)
    flag_part2 = long_to_bytes(cnd2_val)
    flag_part3 = long_to_bytes(cnd3_val)

    # 合併各部分以還原 FLAG
    flag = flag_part1 + flag_part2 + flag_part3
    print(f"FLAG = {flag.decode('utf-8', errors='ignore')}")
else:
    print("無法找到解")
```
