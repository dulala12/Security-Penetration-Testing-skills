---
name: ctf-pwn
description: 为CTF挑战提供二进制漏洞利用技术。当您已经有一个易受攻击的本机目标或服务，需要将内存损坏或低级原语转换为代码执行或权限提升时使用，例如缓冲区溢出、格式化字符串、堆漏洞、ROP、ret2libc、shellcode、内核漏洞利用、seccomp绕过、沙箱逃逸或Windows/Linux漏洞利用链。当主要障碍是理解二进制文件的作用时，不要使用它；首先使用逆向工程。不要将其用于纯web漏洞、磁盘或数据包取证，或独立的密码/数学挑战。
license: MIT
compatibility: 需要基于文件系统的代理（如Claude Code），支持bash、Python 3和互联网访问以安装工具。
allowed-tools: Bash Read Write Edit Glob Grep Task WebFetch WebSearch
metadata:
  user-invocable: "false"
---

# CTF二进制漏洞利用（Pwn）

二进制漏洞利用（pwn）CTF挑战的快速参考。每种技术在这里都有一行描述；有关完整详细信息，请参见支持文件。

## 先决条件

**Python包（所有平台）：**
```bash
pip install pwntools ropper ROPgadget
```

**Linux（apt）：**
```bash
apt install gdb binutils strace ltrace qemu-system-x86
```

**macOS（Homebrew）：**
```bash
brew install gdb binutils qemu
```

**Ruby gems（所有平台）：**
```bash
gem install one_gadget seccomp-tools
```

**手动安装：**
- pwndbg — Linux: [GitHub](https://github.com/pwndbg/pwndbg), macOS: `brew install pwndbg/tap/pwndbg-gdb`
- checksec — 包含在pwntools中

## 其他资源

- [overflow-basics.md](overflow-basics.md) - 栈/全局缓冲区溢出、ret2win、canary绕过、forking服务器上的canary逐字节暴力破解、struct指针覆盖、有符号整数绕过、隐藏gadgets、基于步长的OOB读取泄露、通过未检查memcpy长度的解析器栈溢出与被调用者保存寄存器恢复
- [rop-and-shellcode.md](rop-and-shellcode.md) - 核心ROP链（ret2libc、syscall ROP、rdx控制、shell交互）、ret2csu、坏字符XOR绕过、奇特的x86 gadgets（BEXTR/XLAT/STOSB/PEXT）、通过xchg rax,esp实现栈pivot、用于坏字符绕过的sprintf() gadget链、作为RDX清零gadget的canary XOR结尾、通过read()返回值实现stub_execveat系统调用作为execve替代
- [rop-advanced.md](rop-advanced.md) - 高级ROP技术：通过leave;ret实现双栈pivot到BSS、带UTF-8约束的SROP（Sigreturn-Oriented Programming）、seccomp绕过、用于seccomp绕过的RETF架构切换（x64→x32）、带输入反转的shellcode、.fini_array劫持、ret2vdso、pwntools模板、用于seccomp绕过的x32 ABI系统调用别名、基于时间的盲shellcode数据泄露
- [format-string.md](format-string.md) - 格式化字符串漏洞利用（泄露、GOT覆盖、盲pwn、过滤器绕过、canary泄露、__free_hook、.rela.plt修补、保存的EBP覆盖用于.bss pivot、argv[0]覆盖用于栈溢出信息泄露、用于多阶段漏洞利用的.fini_array循环、带连续%p的__printf_chk绕过、单调用泄露+GOT覆盖、通过输入变换实现ROT13编码的格式化字符串漏洞利用）
- [advanced.md](advanced.md) - Seccomp高级技术、UAF、JIT、深奥的GOT、通过基转换实现堆重叠、树数据结构栈分配不足、ret2dlresolve、内核漏洞利用（基础）
- [heap-techniques.md](heap-techniques.md) - House of Apple 2（+ setcontext SUID变体）、House of Einherjar、House of Orange/Spirit/Lore/Force、堆整理、自定义分配器（nginx、talloc）、经典unlink、musl libc堆（元指针+atexit劫持）、tcache stashing unlink攻击、不安全的unlink + top chunk合并
- [heap-techniques-2.md](heap-techniques-2.md) - CTF-writeup堆变体：UAF vtable指针编码shell参数、未初始化chunk残留指针泄露、tcache strcpy空字节溢出+向后合并、相邻结构fn指针溢出用于libc泄露+GOT覆盖、隐藏菜单tcache poisoning、tcache double-free + 伪造_IO_FILE vtable stdout劫持、tcache-to-fastbin提升跨bin攻击、6位索引OOB + written_bytes累加器、IS_MMAPED位翻转用于calloc'd chunk上的unsorted bin泄露、通过仅LSB堆指针覆盖实现文件名正则约束的fastbin、自定义分配器不安全unlink到GOT
- [heap-fsop.md](heap-fsop.md) - FILE结构（_IO_FILE）漏洞利用：用于PIE + Full RELRO的fastbin stdout vtable两阶段劫持、_IO_buf_base空字节stdin劫持、glibc 2.24+ _IO_FILE vtable验证绕过、stdin _IO_buf_end上的unsorted-bin攻击、通过mp_结构实现unsorted-bin损坏、realloc(ptr, 0)作为free() UAF、单字节引用计数器环绕UAF
- [advanced-exploits.md](advanced-exploits.md) - 高级漏洞利用技术（第1部分）：VM有符号比较、BF JIT shellcode、类型混淆、off-by-one索引损坏、DNS溢出、ASAN影子内存、带编码约束的格式化字符串、自定义canary保护、有符号整数绕过、canary感知部分溢出、CSV注入、MD5原像gadgets、VM GC UAF slab重用、路径遍历清理器绕过、FSOP + 通过openat/mmap/write实现seccomp绕过
- [advanced-exploits-2.md](advanced-exploits-2.md) - 高级漏洞利用技术（第2部分）：通过自修改实现字节码验证器绕过、带SQE注入的io_uring UAF、整数截断int32->int16、GC空引用级联损坏、通过多fgets stdout FILE覆盖实现无泄露libc、有符号/无符号字符下溢堆溢出、XOR密钥流暴力破解写入原语、tcache指针解密堆泄露、通过伪造chunk大小实现unsorted bin提升、FSOP stdout TLS泄露、通过`__call_tls_dtors`实现TLS析构函数劫持、自定义影子栈指针溢出绕过、有符号整数溢出负OOB堆写入、XSS到二进制pwn桥
- [advanced-exploits-4.md](advanced-exploits-4.md) - 高级漏洞利用技术（第4部分）：Windows SEH覆盖 + pushad VirtualAlloc ROP、IAT相对解析、分离进程shell稳定性、SeDebugPrivilege SYSTEM提升、ARM缓冲区溢出带Thumb shellcode、Forth解释器系统字漏洞利用、用于多遍tcache poisoning的GF(2)高斯消元、单比特翻转漏洞利用原语（mprotect + 迭代代码修补）、通过静止生命形态实现生命游戏shellcode进化、通过菜单驱动strdup/free顺序实现UAF、通过system()作为有效调用目标实现Windows CFG绕过、作为函数指针索引OOB的神经网络输出、通过计数器溢出实现shellcode唯一字节限制绕过
- [advanced-exploits-3.md](advanced-exploits-3.md) - 高级漏洞利用技术（第3部分）：栈变量重叠/进位损坏OOB、通过8位循环计数器实现1字节溢出、游戏AI算术平均值OOB读取、任意读写GOT覆盖到shell、通过__environ + memcpy溢出实现栈泄露、通过uint16跳转截断实现JIT沙箱逃逸、带多问题ROP的DNS压缩指针栈溢出、通过程序头操作实现ELF代码签名绕过、游戏关卡格式有符号/无符号坐标不匹配、通过缺少O_CLOEXEC实现文件描述符继承、元数据解析中的符号扩展整数下溢、用只读原语构建ROP链、带持久寄存器的4字节shellcode带时序侧信道、作为任意读取的CRC oracle、UTF-8大小写转换缓冲区溢出
- [advanced-exploits-5.md](advanced-exploits-5.md) - 高级漏洞利用技术（第5部分）：数据解释漏洞利用 — Chip-8模拟器OOB内存用于ret2libc、双精度浮点快速排序canary重新定位、bloom filter abs(INT_MIN)负索引OOB写入
- [sandbox-escape.md](sandbox-escape.md) - 自定义VM漏洞利用、FUSE/CUSE设备、busybox/受限shell、shell技巧、process_vm_readv沙箱绕过、命名管道文件大小绕过、CPU模拟器打印操作码Python eval注入（参考ctf-misc/pyjails.md获取Python jail技术）
- [kernel.md](kernel.md) - Linux内核漏洞利用基础：环境设置、QEMU调试、堆喷射结构（tty_struct、poll_list、user_key_payload、seq_operations）、内核栈溢出、canary泄露、权限提升（ret2usr、内核ROP）、modprobe_path覆盖、core_pattern覆盖、kmalloc大小不匹配堆溢出 + struct file f_op损坏
- [kernel-techniques.md](kernel-techniques.md) - 内核漏洞利用技术：tty_struct kROP（伪造vtable + 栈pivot）、通过ioctl寄存器控制实现AAW、userfaultfd竞争稳定化、SLUB分配器内部（freelist硬化/混淆）、通过内核panic实现泄露、MADV_DONTNEED竞争窗口扩展（DiceCTF 2026）、跨缓存CPU分离攻击（DiceCTF 2026）、PTE重叠文件写入（DiceCTF 2026）、通过失败文件打开实现addr_limit绕过用于内核内存读/写
- [kernel-bypass.md](kernel-bypass.md) - 内核保护绕过：KASLR/FGKASLR绕过（__ksymtab）、KPTI绕过（swapgs trampoline、信号处理程序、通过ROP实现modprobe_path/core_pattern）、SMEP/SMAP绕过、GDB内核模块调试、initramfs/virtio-9p工作流、漏洞利用模板、漏洞利用交付
- [field-notes.md](field-notes.md) - 详细的pwn笔记：堆漏洞利用快速参考、额外的漏洞利用笔记、有用命令

---

## 何时转向

- 如果您还不理解二进制文件的作用，在尝试漏洞利用之前切换到`/ctf-reverse`。
- 如果服务实际上是受限shell、编码谜题或沙箱语言挑战，切换到`/ctf-misc`。
- 如果漏洞利用路径更多依赖于web端点、会话漏洞或上传原语而不是内存损坏，切换到`/ctf-web`。
- 如果漏洞在漏洞利用前需要破解密码学原语，切换到`/ctf-crypto`。

## 快速启动命令

```bash
# 二进制分析
checksec --file=binary
file binary
readelf -h binary

# 查找gadgets
ROPgadget --binary binary | grep "pop rdi"
ropper -f binary --search "pop rdi"
one_gadget /lib/x86_64-linux-gnu/libc.so.6

# 调试
gdb -q binary -ex 'start' -ex 'checksec'

# 偏移查找模式
python3 -c "from pwn import *; print(cyclic(200))"
python3 -c "from pwn import *; print(cyclic_find(0x61616168))"

# libc识别
./libc-database/find puts <leaked_addr_last_3_nibbles>
```

## 源代码危险信号

- 线程/`pthread` -> 竞争条件
- `usleep()`/`sleep()` -> 时序窗口
- 多线程中的全局变量 -> TOCTOU

## 竞争条件漏洞利用

```bash
bash -c '{ echo "cmd1"; echo "cmd2"; sleep 1; } | nc host port'
```

## 常见漏洞

- 缓冲区溢出：`gets()`、`scanf("%s")`、`strcpy()`
- 格式化字符串：`printf(user_input)`
- 整数溢出、UAF、竞争条件

## 保护机制对漏洞利用策略的影响

| 保护机制 | 状态 | 影响 |
|-----------|--------|-------------|
| PIE | 禁用 | 所有地址（GOT、PLT、函数）都是固定的 - 直接覆盖有效 |
| RELRO | 部分 | GOT可写 - GOT覆盖攻击可能 |
| RELRO | 完全 | GOT只读 - 需要替代目标（钩子、vtables、返回地址） |
| NX | 启用 | 无法在栈/堆上执行shellcode - 使用ROP或ret2win |
| Canary | 存在 | 检测到栈溢出 - 需要泄露或避免栈溢出（使用堆） |

**快速决策树：**
- 部分RELRO + 无PIE -> GOT覆盖（最简单，使用固定地址）
- 完全RELRO -> 目标`__free_hook`、`__malloc_hook`（glibc < 2.34）或返回地址
- 存在栈canary -> 优先选择基于堆的攻击或先泄露canary

## 栈缓冲区溢出

1. 查找偏移：`cyclic 200`然后`cyclic -l <value>`
2. 检查保护：`checksec --file=binary`
3. 无PIE + 无canary = 直接ROP
4. 通过格式化字符串或部分覆盖泄露canary
5. 在forking服务器上逐字节暴力破解canary（最多7*256次尝试）

**带魔术值的ret2win：** 溢出 -> `ret`（对齐） -> `pop rdi; ret` -> 魔术值 -> win()。**栈对齐：** `movaps`中的SIGSEGV = 添加额外的`ret` gadget。**偏移：** 缓冲区在`rbp - N`，返回地址在`rbp + 8`，总计 = N + 8。**输入过滤：** 确保payload避免`memmem()`禁止字符串。**Gadgets：** `ROPgadget --binary binary | grep "pop rdi"`，或pwntools `ROP()`用于CMP立即数中的隐藏gadgets。有关完整漏洞利用代码，请参见[overflow-basics.md](overflow-basics.md)。

## 解析器栈溢出（未检查的memcpy）

**模式：** 自定义文件解析器（PCAP、图像、归档）分配固定栈缓冲区，但输入记录可能超过它。`memcpy`在长度验证之前复制，溢出保存的寄存器和返回地址。必须恢复被调用者保存的寄存器：`rbx`到可读内存（BSS），循环计数器到退出值，然后`ret` gadget + win函数。有关完整漏洞利用，请参见[overflow-basics.md](overflow-basics.md#parser-stack-overflow-via-unchecked-memcpy-length-metactf-flash-2026)。

## Struct指针覆盖（堆菜单挑战）

**模式：** 菜单创建/修改/删除结构体，包含数据缓冲区+指针。将名称溢出到指针字段中，使用GOT地址，然后通过修改写入win地址。有关完整漏洞利用和GOT目标选择表，请参见[overflow-basics.md](overflow-basics.md)。

## 有符号整数绕过

**模式：** `scanf("%d")`无符号检查；负数*价格=负数总计，绕过余额检查。参见[overflow-basics.md](overflow-basics.md)。

## Canary感知部分溢出

**模式：** 溢出缓冲区和canary之间的`valid`标志。使用`./`作为无操作路径填充以获得精确长度。有关完整漏洞利用链，请参见[overflow-basics.md](overflow-basics.md)和[advanced.md](advanced.md)。

## 全局缓冲区溢出（CSV注入）

**模式：** 相邻全局变量；通过额外CSV分隔符溢出改变文件名指针。有关完整漏洞利用，请参见[overflow-basics.md](overflow-basics.md)和[advanced.md](advanced.md)。

## ROP链构建

通过`puts@PLT(puts@GOT)`泄露libc，返回到漏洞，阶段2使用`system("/bin/sh")`。有关完整的两阶段ret2libc模式、泄露解析和返回目标选择，请参见[rop-and-shellcode.md](rop-and-shellcode.md)。

**DynELF libc发现：** `pwntools.DynELF(leak_func, pointer_in_libc)`在不知道libc版本的情况下远程解析libc符号。参见[rop-and-shellcode.md](rop-and-shellcode.md#dynelf-automated-libc-discovery-rc3-ctf-2016)。

**小缓冲区中的受限shellcode：** 当缓冲区太小时，使用`read()` shellcode stub（< 20字节）来拉取完整的阶段2 shellcode。参见[rop-and-shellcode.md](rop-and-shellcode.md#constrained-shellcode-in-small-buffers-tum-ctf-2016)。

**原始syscall ROP：** 当`system()`/`execve()`崩溃时（CET/IBT），使用libc中的`pop rax; ret` + `syscall; ret`。参见[rop-and-shellcode.md](rop-and-shellcode.md)。

**ret2csu：** `__libc_csu_init` gadgets控制`rdx`、`rsi`、`edi`并调用任何GOT函数 — 无需libc gadgets的通用3参数调用。参见[rop-and-shellcode.md](rop-and-shellcode.md#ret2csu--__libc_csu_init-gadgets-crypto-cat)。

**坏字符XOR绕过：** 在写入`.data`之前用密钥XOR payload数据，然后用ROP gadgets就地XOR回来。避免空字节、换行符和其他过滤字符。参见[rop-and-shellcode.md](rop-and-shellcode.md#bad-character-bypass-via-xor-encoding-in-rop-crypto-cat)。

**奇特gadgets（BEXTR/XLAT/STOSB/PEXT）：** 当标准`mov`写入gadgets不可用时，链接晦涩的x86指令用于逐字节内存写入。参见[rop-and-shellcode.md](rop-and-shellcode.md#exotic-x86-gadgets--bextrxlatstosbpext-crypto-cat)。

**栈pivot（xchg rax,esp）：** 当溢出对于完整ROP链太小时，将栈指针交换到攻击者控制的堆/缓冲区。需要`pop rax; ret`先加载pivot地址。参见[rop-and-shellcode.md](rop-and-shellcode.md#stack-pivot-via-xchg-raxesp-crypto-cat)。

**rdx控制：** `puts()`之后，rdx被破坏为1。使用libc中的`pop rdx; pop rbx; ret`，或重新进入二进制文件的read设置 + 栈pivot。参见[rop-and-shellcode.md](rop-and-shellcode.md)。

**作为rdx清零gadget的Canary XOR结尾：** 当不存在`pop rdx; ret`时，跳转到canary检查结尾`xor rdx, fs:28h` — 当canary完整时它会清零RDX。参见[rop-and-shellcode.md](rop-and-shellcode.md#stack-canary-xor-epilogue-as-rdx-zeroing-gadget-volgactf-2017)。

**作为execve替代的stub_execveat：** 当不存在`pop rax; ret`时，使用`stub_execveat`（系统调用322/0x142）代替`execve` — 发送正好0x142字节，使`read()`返回值设置rax。参见[rop-and-shellcode.md](rop-and-shellcode.md#stub_execveat-syscall-as-execve-alternative-asis-ctf-2018)。

**Shell交互：** `execve`之后，`sleep(1)`然后`sendline(b'cat /flag*')`。参见[rop-and-shellcode.md](rop-and-shellcode.md)。

## 通过输入变换的格式化字符串

**ROT13编码的格式化字符串：** 当输入在到达`printf`之前经过ROT13/Caesar变换时，用逆变换预编码格式化字符串payload，使其完整到达。参见[format-string.md](format-string.md#format-string-exploit-through-rot13-encoding-sunshinectf-2018)。

## 内核漏洞利用

**通过失败文件打开实现addr_limit绕过：** 当内核模块设置`addr_limit = KERNEL_DS`但在错误路径上未能恢复它时，强制错误（例如，使目标文件成为目录）以从用户空间`read()`/`write()`保留内核内存访问。参见[kernel-techniques.md](kernel-techniques.md#kernel-addr_limit-bypass-via-failed-file-open-midnight-sun-ctf-2018)。

## 沙箱和模拟器逃逸

**CPU模拟器eval注入：** 当模拟器的打印操作码使用`eval('"' + buf + '"')`处理转义序列时，通过ADD操作码在模拟器内存中构建`"+__import__("os").system("cmd")#`以转义字符串并执行Python。参见[sandbox-escape.md](sandbox-escape.md#cpu-emulator-print-opcode-python-eval-injection-midnight-sun-ctf-2018)。

## 高级漏洞利用原语

**神经网络函数指针OOB：** 当二进制文件使用NN输出作为函数指针数组的索引而不进行边界检查时，重新训练权重/偏差以产生OOB索引，从偏差数组中读取目标地址。参见[advanced-exploits-4.md](advanced-exploits-4.md#neural-network-output-as-function-pointer-index-oob-swampctf-2018)。

**通过计数器溢出实现shellcode唯一字节限制绕过：** 当shellcode限制为N个唯一字节时，喷射栈以破坏`seen[256]`计数器，然后重新执行main（跳过`memset`），使溢出的计数器在第二次运行时允许任意字节。参见[advanced-exploits-4.md](advanced-exploits-4.md#shellcode-unique-byte-limit-bypass-via-counter-overflow-blaze-ctf-2018)。

## 深入笔记

一旦确认挑战确实是漏洞利用重的，使用[field-notes.md](field-notes.md)。

- 堆和分配器笔记：House of Apple、tcache、不安全的unlink、talloc、UAF、FSOP
- 高级漏洞利用笔记：seccomp绕过、ret2vdso、io_uring、整数截断、ASAN、时序oracles
- 沙箱和混合笔记：pyjail交叉、busybox逃逸、自定义VM、shell技巧、路径清理器
- 内核和Windows笔记：内核手册、SEH、CFG绕过、权限提升
- 历史案例笔记：旧但仍可重用的CTF漏洞利用模式