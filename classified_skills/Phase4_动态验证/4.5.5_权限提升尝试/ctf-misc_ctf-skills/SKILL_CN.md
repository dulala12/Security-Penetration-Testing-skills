---
name: ctf-misc
description: 为CTF挑战提供杂项技术。当挑战不属于其他类别（web、pwn、reverse、crypto、forensics、osint、malware、ai-ml）时使用，包括Python jail逃逸、编码/解码、隐写术、数据雕刻、数据压缩、游戏挑战、编程难题、网络协议分析、音频处理、视频分析、自定义文件格式、Steganography、QR码、brainfuck、shell jail逃逸和其他非标准挑战类型。
license: MIT
compatibility: 需要基于文件系统的代理（如Claude Code），支持bash、Python 3和互联网访问。
allowed-tools: Bash Read Write Edit Glob Grep Task WebFetch WebSearch
metadata:
  user-invocable: "false"
---

# CTF Misc

杂项CTF挑战的快速参考。每种技术在这里都有一行描述；有关完整详细信息，请参见支持文件。

## 先决条件

**Python包（所有平台）：**
```bash
pip install z3-solver scipy numpy pillow qrcode pyzbar soundfile
```

**Linux（apt）：**
```bash
apt install steghide exiftool imagemagick gzip bzip2 xz-utils tar unrar
```

**macOS（Homebrew）：**
```bash
brew install steghide exiftool imagemagick
```

## 其他资源

- [steganography.md](steganography.md) - 图像隐写术（LSB、EXIF、像素分析、颜色通道、F5、OutGuess）、音频隐写术（频谱分析、声谱图、MP3隐写）、文件雕刻、压缩数据、密码保护存档、ZIP伪加密、XOR分析、编码（Base64、Base32、Base58、Base91、Hex、URL编码）、字符串分析、二进制可视化、盲水印
- [steganography-advanced.md](steganography-advanced.md) - 高级隐写术：PNG IDAT位平面分析、GIF帧差异、颜色通道分离、LSB替换与匹配、JPEG DCT系数隐写、PNG压缩流修改、WebP隐写、自定义位编码方案、JPEG量化表操纵、通过Alpha通道隐写、基于频率的隐写、BPG/JXL格式隐写、基于机器学习的隐写分析、视觉隐写（图像中的隐藏文本/图案）、3D模型隐写（GLB/OBJ）
- [encoding-challenges.md](encoding-challenges.md) - 编码挑战：ASCII艺术、二进制、八进制、十六进制、摩尔斯电码、培根密码、波利比奥斯棋盘、凯撒密码、Vigenère、栅栏密码、Rail Fence、Scytale、A1Z26、Atbash、ROT13、ROT47、UUencode、XXencode、Quoted-printable、Base85、ASCII85、Z85、Base62、Base64url、URL安全变体、Unicode编码、UTF-7/UTF-8/UTF-16/UTF-32、ISO-8859编码、HTML实体、Unicode转义序列、Morse到二进制转换、Baudot代码、Teletype编码
- [data-formats.md](data-formats.md) - 数据格式：自定义二进制格式、文件头、magic bytes、压缩算法、存档格式（ZIP、RAR、7z、tar）、容器格式、序列化格式、JSON/YAML/TOML解析、二进制协议分析、网络字节序转换、位打包/解包、endianness处理、文件魔术字节识别、自定义序列化格式、ProtoBuf/Thrift/FlatBuffers、Excel文件分析（XLS/XLSX）、PDF结构解析、XML/HTML解析、CSV/TSV处理
- [pyjails.md](pyjails.md) - Python jail逃逸技术：属性访问绕过（`__getattr__`、`__dict__`、`__class__`、`__bases__`）、对象链、环境变量、导入绕过、AST注入、eval限制、禁用内置函数、沙箱逃逸、pickle利用、代码对象创建、字节码操作、sys.modules操作、subprocess模块利用、标准库模块滥用、字符串拼接绕过、lambda表达式、三元运算符、列表推导式、生成器表达式、装饰器滥用、上下文管理器、异常处理、内存视图、类型转换
- [pyjails-advanced.md](pyjails-advanced.md) - 高级Python jail逃逸：sys._getframe()本地变量访问、ast模块代码生成、code对象直接构造、字节码操作、pickle协议攻击、marshal模块利用、zipimport攻击、sys.setrecursionlimit绕过、gc模块滥用、ctypes内存操作、multiprocessing模块、socket模块、urllib模块、base64模块、hashlib模块、itertools模块、functools模块、operator模块、collections模块、re模块、json模块、os模块、pathlib模块、shutil模块、tempfile模块、threading模块、concurrent.futures模块
- [shell-jails.md](shell-jails.md) - Shell jail逃逸：受限shell、rbash、rbash绕过、PATH操纵、命令分隔符、环境变量、LD_PRELOAD、进程注入、符号链接攻击、文件描述符继承、trap命令、debug模式、编辑器逃逸（vi、ed、nano）、more/less分页器逃逸、man命令、python/ruby/perl解释器、awk/gawk、sed、find命令、xargs、curl/wget、nc/netcat、socat、python子进程、ruby子进程、perl子进程、lua解释器、node.js解释器、php解释器
- [shell-jails-advanced.md](shell-jails-advanced.md) - 高级shell jail逃逸：ptrace注入、LD_LIBRARY_PATH攻击、setuid程序滥用、capabilities攻击、namespace逃逸、cgroup攻击、seccomp绕过、apparmor绕过、selinux绕过、docker逃逸、kvm逃逸、虚拟化逃逸、容器逃逸、chroot逃逸、pivot_root攻击、mount命名空间、IPC命名空间、UTS命名空间、PID命名空间、网络命名空间、user命名空间、CGroups v2攻击、systemd攻击、dbus攻击、polkit攻击、sudoers配置错误、su命令漏洞、SSH配置漏洞、crontab攻击、at命令攻击、systemd timer攻击、anacron攻击
- [programming-puzzles.md](programming-puzzles.md) - 编程难题：数学问题、算法挑战、代码高尔夫、逆向工程代码、调试难题、逻辑谜题、数独变体、密码谜题、序列预测、模式识别、数学定理应用、图论问题、动态规划、贪心算法、回溯算法、分支定界、NP完全问题近似解、计算几何、字符串算法、排序算法、搜索算法、图遍历、最短路径、最小生成树、网络流、匹配算法、线性代数、矩阵运算、概率统计、组合数学、数论、群论、密码学数学、信息论、复杂度理论、算法分析、时间/空间复杂度优化
- [game-challenges.md](game-challenges.md) - 游戏挑战：修改游戏内存、逆向游戏逻辑、破解高分、绕过反作弊、游戏引擎漏洞、Unity/Unreal引擎分析、脚本注入、内存修改、指针扫描、代码注入、DLL注入、函数挂钩、游戏存档修改、加密存档破解、网络协议分析、数据包修改、服务器模拟、客户端模拟、游戏模组开发、地图编辑器利用、控制台命令、作弊引擎、调试器利用、模拟器漏洞、ROM hacking、NES/SNES游戏修改、Game Boy游戏修改、PSX游戏修改、N64游戏修改
- [audio-video.md](audio-video.md) - 音频/视频分析：声波隐写、频谱分析、声谱图、音频编码、音频压缩、视频编码、视频压缩、帧提取、视频隐写、音频隐写、FFmpeg命令、音频格式转换、视频格式转换、音频降噪、音频增强、视频稳定、视频修复、音频水印、视频水印、音频指纹、视频指纹、音频识别、视频识别、语音识别、语音合成、音频隐写检测、视频隐写检测
- [network-protocols.md](network-protocols.md) - 网络协议分析：TCP/IP、UDP、HTTP、HTTPS、DNS、FTP、SMTP、POP3、IMAP、SSH、Telnet、SNMP、DHCP、ARP、ICMP、IPv6、WebSocket、MQTT、CoAP、AMQP、XMPP、IRC、BitTorrent、DNS隧道、HTTP隧道、SSH隧道、VPN协议、TLS/SSL、证书分析、协议逆向、数据包分析、流量分析、网络取证、数据包雕刻、协议模糊测试、漏洞扫描、网络映射、端口扫描、服务识别、指纹识别
- [misc-techniques.md](misc-techniques.md) - 杂项技术：QR码解码、二维码分析、条形码解码、数据矩阵解码、PDF417解码、Aztec码解码、MaxiCode解码、MicroQR解码、自定义二维码、图像识别、OCR、光学字符识别、手写识别、验证码破解、机器学习模型攻击、AI生成内容检测、深度学习模型逆向、神经网络分析、模型提取攻击、模型盗窃、对抗性攻击、模型水印、模型加密、区块链分析、智能合约分析、DeFi漏洞、NFT分析、加密货币取证、比特币分析、以太坊分析、Solana分析、跨链桥分析、零知识证明分析、密码学协议分析、形式化验证、安全审计、渗透测试、漏洞研究、安全研究、漏洞开发、安全工具开发、安全自动化、威胁情报、安全监控、入侵检测、防御规避、红队操作、蓝队操作、紫队操作

---

## 何时转向

- 如果挑战涉及磁盘取证、日志分析或数据包分析，切换到`/ctf-forensics`。
- 如果涉及Web应用程序漏洞，切换到`/ctf-web`。
- 如果涉及二进制漏洞利用，切换到`/ctf-pwn`。
- 如果涉及密码学攻击，切换到`/ctf-crypto`。
- 如果涉及恶意软件分析，切换到`/ctf-malware`。
- 如果涉及AI/ML攻击，切换到`/ctf-ai-ml`。
- 如果涉及开源情报收集，切换到`/ctf-osint`。
- 如果涉及二进制逆向工程，切换到`/ctf-reverse`。

## 快速启动命令

```bash
# 文件分析
file unknown.bin
xxd unknown.bin | head -20
strings unknown.bin | grep -i flag

# 图像分析
exiftool image.png
steghide extract -sf image.jpg
zsteg image.png

# 压缩文件
unzip file.zip -d extract/
unrar x file.rar
7z x file.7z

# 音频分析
sox input.wav output.raw
audacity input.mp3  # 可视化声谱图

# QR码
zbarimg qr.png
zbarcam  # 实时扫描
```

## 常见编码检测

| 模式 | 编码类型 | 解码命令 |
|------|----------|----------|
| `ZHVtbXk=` | Base64 | `echo "..." | base64 -d` |
| `MFRGGZDF` | Base32 | `echo "..." | base32 -d` |
| `73x6E` | Hex | `echo "..." | xxd -r -p` |
| `.- ---.` | Morse | 手动解码或脚本 |
| `01001000` | Binary | `echo "..." | tr '01' '\x00\x01'` |

## Python Jail逃逸模式

**基本链：** `__class__` -> `__bases__` -> `__subclasses__()` -> 找到危险类（`os`、`subprocess`等）

```python
# 常见逃逸链
().__class__.__bases__[0].__subclasses__()
# 查找包含 'os' 或 'subprocess' 的类
```

**绕过禁用的字符：** 使用字符串拼接、编码、eval、exec等技巧。

## Shell Jail逃逸

```bash
# 检查限制
echo $SHELL
echo $PATH
which python3

# 尝试编辑器逃逸
vi
# :!bash

# 尝试命令分隔符
ls; bash
ls|bash
ls$(bash)
ls`bash`

# 使用python
python3 -c 'import os; os.system("/bin/bash")'
```

## 隐写术检查清单

- [ ] 检查文件头（magic bytes）
- [ ] 使用`strings`搜索隐藏文本
- [ ] 检查EXIF元数据
- [ ] 使用`zsteg`分析图像
- [ ] 尝试`steghide`提取
- [ ] 检查LSB位
- [ ] 分析声谱图（音频文件）
- [ ] 检查文件末尾的隐藏数据
- [ ] 尝试XOR解密
- [ ] 检查压缩数据

## 数据雕刻

```bash
# 从二进制文件中提取文件
foremost -i image.dd -o output/
binwalk -e firmware.bin
```

## 自定义二进制格式

```python
import struct

with open('file.bin', 'rb') as f:
    magic = f.read(4)
    version = struct.unpack('<I', f.read(4))[0]
    # 继续解析...
```

## 深入笔记

使用相关的支持文件获取详细技术：

- [steganography.md](steganography.md) - 基础隐写术技术
- [steganography-advanced.md](steganography-advanced.md) - 高级隐写术技术
- [encoding-challenges.md](encoding-challenges.md) - 编码挑战
- [data-formats.md](data-formats.md) - 数据格式解析
- [pyjails.md](pyjails.md) - Python jail基础
- [pyjails-advanced.md](pyjails-advanced.md) - 高级Python jail逃逸
- [shell-jails.md](shell-jails.md) - Shell jail基础
- [shell-jails-advanced.md](shell-jails-advanced.md) - 高级shell jail逃逸
- [programming-puzzles.md](programming-puzzles.md) - 编程难题
- [game-challenges.md](game-challenges.md) - 游戏挑战
- [audio-video.md](audio-video.md) - 音频/视频分析
- [network-protocols.md](network-protocols.md) - 网络协议
- [misc-techniques.md](misc-techniques.md) - 杂项技术