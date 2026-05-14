---
name: ctf-crypto
description: 为CTF挑战提供密码学攻击技术。当解决涉及密码学原语、算法或协议的挑战时使用，包括经典密码学、现代对称密码学、非对称密码学、哈希函数、数字签名、密钥交换协议、侧信道攻击、格式攻击、填充攻击、哈希长度扩展攻击、碰撞攻击、生日攻击、模运算、数论、椭圆曲线密码学、格密码学等。
license: MIT
compatibility: 需要基于文件系统的代理（如Claude Code），支持bash、Python 3和互联网访问以安装工具。
allowed-tools: Bash Read Write Edit Glob Grep Task WebFetch WebSearch
metadata:
  user-invocable: "false"
---

# CTF密码学

密码学CTF挑战的快速参考。每种技术在这里都有一行描述；有关完整详细信息，请参见支持文件。

## 先决条件

**Python包（所有平台）：**
```bash
pip install pycryptodome sympy gmpy2 cryptography
```

**Linux（apt）：**
```bash
apt install openssl libssl-dev
```

**macOS（Homebrew）：**
```bash
brew install openssl
```

## 其他资源

- [classical.md](classical.md) - 经典密码学：凯撒密码、维吉尼亚密码、栅栏密码、培根密码、波利比奥斯棋盘、Atbash、ROT13、ROT47、A1Z26、摩尔斯电码、栅栏密码、Scytale、置换密码、替代密码、频率分析、字母频率统计、破解经典密码
- [symmetric.md](symmetric.md) - 对称密码学：AES、DES、3DES、RC4、ChaCha20、Salsa20、Blowfish、Twofish、IDEA、Serpent、CAST-128、SKIPJACK、流密码、分组密码、ECB、CBC、CFB、OFB、CTR、GCM、CCM、加密模式、密钥扩展、轮函数、S-box、P-box、Feistel网络
- [asymmetric.md](asymmetric.md) - 非对称密码学：RSA、ECC、DSA、ECDSA、EdDSA、Diffie-Hellman、Rabin、ElGamal、密钥生成、签名方案、加密方案、密钥交换、数字签名、证书、PKI、CA、OCSP、CRL
- [hash.md](hash.md) - 哈希函数：MD5、SHA-1、SHA-2、SHA-3、BLAKE2、MD4、RIPEMD-160、WHIRLPOOL、碰撞攻击、生日攻击、长度扩展攻击、抗原像性、抗第二原像性、HMAC、KMAC、CMAC、PRF、PRNG、熵估计
- [attacks.md](attacks.md) - 密码攻击：侧信道攻击、时序攻击、功率分析、电磁分析、故障注入攻击、差分密码分析、线性密码分析、代数密码分析、统计密码分析、中间相遇攻击、彩虹表攻击、字典攻击、暴力破解攻击、选择明文攻击、选择密文攻击、自适应选择密文攻击、已知明文攻击、唯密文攻击、格式攻击、填充攻击、Oracle攻击、LSB Oracle、高位Oracle
- [number-theory.md](number-theory.md) - 数论：素数检测、因数分解、离散对数、模运算、中国剩余定理、欧拉定理、费马小定理、威尔逊定理、二次剩余、勒让德符号、雅可比符号、素性测试、Pollard Rho、Baby-step Giant-step、指数计算、模逆元、原根、阶、群论、有限域、椭圆曲线、格基约化、LLL算法、CVP、SVP
- [post-quantum.md](post-quantum.md) - 后量子密码学：格基密码学、学习同态加密、基于哈希的密码学、多变量密码学、代码基密码学、NTRU、LWE、RLWE、BLISS、CRYSTALS-Kyber、CRYSTALS-Dilithium、SPHINCS+、FALCON、Rainbow、GeMSS、BIKE、HQC、SIDH、SIKE

---

## 何时转向

- 如果挑战涉及二进制漏洞利用，切换到`/ctf-pwn`。
- 如果涉及二进制逆向工程，切换到`/ctf-reverse`。
- 如果涉及Web应用程序漏洞利用，切换到`/ctf-web`。

## 快速启动命令

```bash
# 加密/解密
openssl enc -aes-256-cbc -in input.txt -out output.enc
openssl enc -aes-256-cbc -d -in output.enc -out decrypted.txt

# 哈希计算
md5sum file.txt
sha256sum file.txt
openssl dgst -sha256 file.txt

# 生成密钥对
openssl genrsa -out private.pem 2048
openssl rsa -in private.pem -pubout -out public.pem

# RSA加密/解密
openssl rsautl -encrypt -in plain.txt -out cipher.bin -inkey public.pem -pubin
openssl rsautl -decrypt -in cipher.bin -out plain.txt -inkey private.pem

# 签名/验证
openssl dgst -sha256 -sign private.pem -out signature.bin file.txt
openssl dgst -sha256 -verify public.pem -signature signature.bin file.txt

# 素数检测
openssl prime -generate -bits 2048
```

## 密码分析工作流程

1. **识别密码系统**
   - 分析密文特征
   - 识别加密算法
   - 确定密钥长度
   - 分析模式和填充

2. **收集信息**
   - 明文/密文对
   - 加密参数
   - 系统行为
   - 侧信道信息

3. **选择攻击方法**
   - 暴力破解
   - 字典攻击
   - 数学攻击
   - 侧信道攻击

4. **执行攻击**
   - 实现攻击代码
   - 优化攻击性能
   - 验证结果

5. **验证和解密**
   - 验证密钥正确性
   - 解密获取flag

## 经典密码破解

```python
# 凯撒密码解密
def caesar_decrypt(ciphertext, shift):
    result = ""
    for char in ciphertext:
        if char.isalpha():
            ascii_offset = ord('A') if char.isupper() else ord('a')
            result += chr((ord(char) - ascii_offset - shift) % 26 + ascii_offset)
        else:
            result += char
    return result

# 频率分析破解
def frequency_analysis(ciphertext):
    freq = {}
    for char in ciphertext.upper():
        if char.isalpha():
            freq[char] = freq.get(char, 0) + 1
    return sorted(freq.items(), key=lambda x: x[1], reverse=True)
```

## 对称密码攻击

```python
from Crypto.Cipher import AES

# CBC模式攻击示例
def cbc_bit_flip_attack(ciphertext, block_size=16):
    ciphertext = bytearray(ciphertext)
    # 翻转第n位
    ciphertext[0] ^= 0x01
    return bytes(ciphertext)

# ECB模式攻击示例
def ecb_byte_at_a_time(cipher, block_size=16):
    plaintext = b''
    for i in range(1, block_size + 1):
        prefix = b'A' * (block_size - i)
        for byte in range(256):
            test = prefix + plaintext + bytes([byte])
            ciphertext = cipher.encrypt(test)
            if ciphertext[:block_size] == target[:block_size]:
                plaintext += bytes([byte])
                break
    return plaintext
```

## RSA攻击

```python
import gmpy2

# 小指数攻击
def rsa_small_e_attack(ciphertext, e, n):
    return gmpy2.iroot(ciphertext, e)[0]

# 共模攻击
def rsa_common_modulus(c1, e1, c2, e2, n):
    gcd, x, y = extended_gcd(e1, e2)
    m = (pow(c1, x, n) * pow(c2, y, n)) % n
    return m

# 扩展欧几里得算法
def extended_gcd(a, b):
    if a == 0:
        return b, 0, 1
    gcd, x1, y1 = extended_gcd(b % a, a)
    x = y1 - (b // a) * x1
    y = x1
    return gcd, x, y
```

## 哈希攻击

```python
import hashlib

# 生日攻击
def birthday_attack(hash_func, target_hash):
    seen = {}
    while True:
        random_data = os.urandom(16)
        h = hash_func(random_data).hexdigest()
        if h in seen:
            return seen[h], random_data
        seen[h] = random_data

# 长度扩展攻击
def length_extension_attack(hash_func, known_hash, known_data, append_data):
    # 实现取决于具体哈希函数
    pass
```

## 数论工具

```python
from sympy import factorint, nextprime, isprime

# 因数分解
factors = factorint(n)

# 素数检测
if isprime(n):
    print(f"{n} 是素数")

# 求模逆
def mod_inverse(a, m):
    return pow(a, -1, m)

# 中国剩余定理
def crt(remainders, moduli):
    from sympy.ntheory.modular import crt
    return crt(moduli, remainders)[0]
```

## 常见漏洞模式

| 漏洞类型 | 描述 | 攻击方法 |
|----------|------|----------|
| 弱密钥 | 使用不安全的密钥生成 | 暴力破解、字典攻击 |
| 弱随机数 | 使用可预测的随机数生成器 | 预测密钥、非ce随机性 |
| 小指数 | RSA使用小e值 | 直接开方 |
| 共模攻击 | 多个用户共享模数 | 扩展欧几里得算法 |
| 填充漏洞 | PKCS#7填充实现错误 | Padding Oracle攻击 |
| 侧信道泄露 | 时序/功率泄露密钥信息 | 时序分析、差分功率分析 |
| 哈希长度扩展 | 允许扩展消息而不重新计算 | 长度扩展攻击 |
| 碰撞攻击 | 找到两个不同消息具有相同哈希 | 生日攻击、预计算表 |

## 深入笔记

使用相关的支持文件获取详细技术：

- [classical.md](classical.md) - 经典密码学
- [symmetric.md](symmetric.md) - 对称密码学
- [asymmetric.md](asymmetric.md) - 非对称密码学
- [hash.md](hash.md) - 哈希函数
- [attacks.md](attacks.md) - 密码攻击
- [number-theory.md](number-theory.md) - 数论
- [post-quantum.md](post-quantum.md) - 后量子密码学

## 工具资源

- **密码库**：PyCryptodome、cryptography、mbed TLS、OpenSSL
- **数论工具**：SymPy、GMP、gmpy2
- **密码分析工具**：Hashcat、John the Ripper、Aircrack-ng
- **在线工具**：CyberChef、Cryptii、Base64Decode
- **研究资源**：NIST、IETF RFC、ePrint Archive、Crypto Stack Exchange