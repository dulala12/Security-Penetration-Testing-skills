---
name: solve-challenge
description: 通过执行首次分类、识别主要类别并将执行路由到正确的专业ctf-*技能来解决CTF挑战。当用户提供挑战包、远程服务、可疑文件或仅提供模糊的挑战描述且您必须确定从哪里开始时使用。当类别已经明确且可以直接调用专业技能时，请勿使用此技能；这是调度器和侦察入口点，而不是特定类别技术的最深层参考。
license: MIT
compatibility: 需要基于文件系统的代理（如Claude Code），支持bash、Python 3和互联网访问。编排其他ctf-*技能。
allowed-tools: Bash Read Write Edit Glob Grep Task WebFetch WebSearch Skill
metadata:
  user-invocable: "true"
  argument-hint: "[category] [challenge-file-or-url]"
---

# CTF 挑战求解器

您是一名熟练的CTF玩家。您的目标是解决挑战并找到flag。

## 环境设置

根据您的工作流程，有两种设置策略：

### 预安装（比赛前推荐）

使用中央安装入口点：

```bash
bash scripts/install_ctf_tools.sh all
```

当您只需要一个工具组时，运行更窄的模式：

```bash
bash scripts/install_ctf_tools.sh python
bash scripts/install_ctf_tools.sh apt
bash scripts/install_ctf_tools.sh brew
bash scripts/install_ctf_tools.sh gems
bash scripts/install_ctf_tools.sh go
bash scripts/install_ctf_tools.sh manual
```

完整的软件包列表现在位于 [scripts/install_ctf_tools.sh](../scripts/install_ctf_tools.sh)。

### 按需安装（挑战期间）

每个类别技能的 `SKILL.md` 都有一个**Prerequisites**部分，列出该类别所需的工具。按需安装。

## 工作流程

### 步骤0：CTFd平台检测

如果知道CTF平台URL，检查它是否运行CTFd并切换到API驱动的导航：

```bash
# 检测CTFd（查找/api/v1/和/themes/core/）
curl -s "$CTF_URL/api/v1/" | head -5
curl -s "$CTF_URL" | grep -oE '/themes/core/'
```

如果检测到CTFd，**请向用户询问他们的API令牌**（从CTFd设置>访问令牌生成）。默认情况下不提供令牌——用户必须首先在CTFd Web UI中创建一个。提供后，设置环境变量并通过API继续：

```bash
export CTF_URL="https://ctf.example.com"
export CTF_TOKEN="ctfd_..."  # 向用户询问此值
```

调用 `/ctf-misc` 并加载其 `ctfd-navigation.md` 获取完整的API参考和Python客户端类。

### 步骤1：侦察

1. **探索文件** -- 列出挑战目录，对所有文件运行 `file *`
2. **分类二进制文件** -- 对二进制文件使用 `strings`、`xxd | head`、`binwalk`、`checksec`
3. **获取链接** -- 如果挑战提到URL，首先获取它们以获取上下文
4. **连接** -- 尝试远程服务（`nc`）以了解它们期望什么
5. **读取提示** -- 挑战描述、文件名和注释通常包含线索

### 步骤2：分类

确定主要类别，然后调用匹配的技能。

**按文件类型：**
- `.pcap`, `.pcapng`, `.evtx`, `.raw`, `.dd`, `.E01` -> forensics（取证）
- `.elf`, `.exe`, `.so`, `.dll`, 无扩展名的二进制文件 -> reverse（逆向）或 pwn（漏洞利用）（检查是否提供了远程服务——如果是，可能是pwn）
- `.py`, `.sage`, `.txt` 包含数字 -> crypto（密码学）
- `.apk`, `.wasm`, `.pyc` -> reverse（逆向）
- Web URL或包含HTML/JS/PHP/模板的源代码 -> web（Web）
- 图像、音频、PDF，无明显内容 -> forensics（取证，隐写术）

**按挑战描述关键词：**
- "buffer overflow", "ROP", "shellcode", "libc", "heap" -> pwn
- "RSA", "AES", "cipher", "encrypt", "prime", "modulus", "lattice", "LWE", "GCM" -> crypto
- "XSS", "SQL", "injection", "cookie", "JWT", "SSRF" -> web
- "disk image", "memory dump", "packet capture", "registry", "power trace", "side-channel", "spectrogram", "audio tracks", "MKV" -> forensics
- "find", "locate", "identify", "who", "where" -> osint
- "obfuscated", "packed", "C2", "malware", "beacon" -> malware
- "jail", "sandbox", "escape", "encoding", "signal", "game", "Nim", "commitment", "Gray code" -> misc

**按服务行为：**
- 端口带有交互式提示，长输入导致崩溃 -> pwn
- HTTP服务 -> web
- netcat带有数学/密码谜题 -> crypto
- netcat带有受限shell或eval -> misc（jail）

### 步骤3：调用类别技能

一旦确定类别，**调用匹配的技能**以获取专业技术：

| 类别 | 调用方式 | 使用场景 |
|----------|--------|-------------|
| Web | `/ctf-web` | XSS, SQLi, SSTI, SSRF, JWT, 文件上传, prototype pollution |
| Pwn | `/ctf-pwn` | 缓冲区溢出, 格式化字符串, heap, ROP, 沙箱逃逸 |
| Crypto | `/ctf-crypto` | RSA, AES, ECC, PRNG, ZKP, 经典密码 |
| Reverse | `/ctf-reverse` | 二进制分析, 游戏客户端, VM, 混淆代码 |
| Forensics | `/ctf-forensics` | 磁盘镜像, 内存转储, 事件日志, stego, 网络捕获 |
| OSINT | `/ctf-osint` | 社交媒体, 地理定位, DNS, 公共记录 |
| Malware | `/ctf-malware` | 混淆脚本, C2流量, PE/.NET分析 |
| Misc | `/ctf-misc` | Jails, 编码, RF/SDR, 深奥语言, 约束求解 |

您也可以调用 `/ctf-<category>` 加载完整的技能说明和详细技术。

### 步骤4：卡住时转换方向

如果您的第一种方法不起作用：

1. **重新检查假设** -- 这真的是您认为的类别吗？一个"web"挑战可能需要密码学来伪造JWT。一个"forensics" PCAP可能包含要重放的pwn漏洞利用。
2. **尝试不同的类别技能** -- 许多挑战跨越多个类别。调用第二个技能来获取跨领域技术。
3. **查找您遗漏的内容** -- 隐藏文件、备用端口、响应头、源代码中的注释、图像中的元数据。
4. **简化** -- 如果漏洞利用太复杂，检查是否有更简单的路径（默认凭证、已知CVE、逻辑漏洞）。
5. **检查边缘情况** -- 边界错误、竞争条件、整数溢出、编码不匹配。

**常见的多类别模式：**
- Forensics + Crypto: PCAP/磁盘镜像中的加密数据，需要密码学解密
- Web + Reverse: Web挑战中的WASM或混淆JS
- Web + Crypto: JWT伪造，自定义MAC/签名方案
- Reverse + Pwn: 先逆向二进制文件，然后利用漏洞
- Forensics + OSINT: 从转储中恢复数据，然后通过公共来源追踪
- Misc + Crypto: jail逃逸需要在约束下构建密码学原语
- OSINT + Stego: 社交媒体帖子带有Unicode同形字隐写术（西里尔字母相似字符编码位）
- Web + Forensics: 付费墙绕过（curl揭示被CSS覆盖隐藏的内容）
- Misc + Crypto + Game Theory: 多阶段交互式挑战，包含AES解密→HMAC承诺→组合游戏求解（GF(256) Nim）
- Crypto + Geometry + Lattice: 多层挑战，从空间重建→子空间恢复→LWE求解→AES-GCM解密
- Forensics + Signal Processing: 功率迹/侧信道分析，需要测量数据的统计分析
- Forensics + Network + Encoding: PCAP中的基于时序的编码（包间隔编码二进制数据）

### 步骤5：生成Write-up

解决挑战后，调用 `/ctf-writeup` 生成标准化的提交风格writeup —— 简洁、可重现，并准备好供比赛组织者或队友验证。

## Flag格式

Flag因CTF而异。常见格式：
- `flag{...}`, `FLAG{...}`, `CTF{...}`, `TEAM{...}`
- 自定义前缀：查看挑战描述或CTF规则了解格式（例如，`ENO{...}`, `HTB{...}`, `picoCTF{...}`）
- 有时只是没有包装的明文字符串

**验证规则（重要）：**
- 如果找到多个类flag字符串，将它们视为候选并在最终确定前验证。
- 优先选择与预期工件/工作流程相关联的令牌（不是随机元数据噪声或明显的诱饵）。
- 进行语料库范围的唯一性检查，并在报告时包含源文件/路径。

```bash
# 在文件中搜索常见flag模式
grep -rniE '(flag|ctf|eno|htb|pico)\{' .
# 在二进制/内存输出中搜索
strings output.bin | grep -iE '\{.*\}'
```

## 快速参考

```bash
# 侦察
file *                                    # 识别文件类型
strings binary | grep -i flag             # 快速字符串搜索
xxd binary | head -20                     # 十六进制转储头部
binwalk -e firmware.bin                   # 提取嵌入文件
checksec --file=binary                    # 检查二进制保护

# 连接
nc host port                              # 连接到挑战
echo -e "answer1\nanswer2" | nc host port # 脚本化输入
curl -v http://host:port/                 # HTTP侦察

# Python漏洞利用模板
python3 -c "
from pwn import *
r = remote('host', port)
r.interactive()
"
```

## 挑战

$ARGUMENTS