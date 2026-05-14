---
name: ctf-reverse
description: 为CTF挑战提供逆向工程技术。当主要任务是理解编译、混淆、打包或虚拟化目标在漏洞利用或解决之前的工作原理时使用，包括二进制文件、APK、WASM、固件、自定义VM、字节码、游戏客户端、恶意软件样加载器以及反调试或反分析逻辑。当漏洞已经理解且剩余任务是漏洞利用时，不要使用它；改用pwn。不要将其用于纯web工作流、日志或磁盘取证，或独立的密码问题，除非逆向实现是真正的障碍。
license: MIT
compatibility: 需要基于文件系统的代理（如Claude Code），支持bash、Python 3和互联网访问以安装工具。
allowed-tools: Bash Read Write Edit Glob Grep Task WebFetch WebSearch
metadata:
  user-invocable: "false"
---

# CTF逆向工程

RE挑战的快速参考。有关详细技术，请参见支持文件。

## 先决条件

**Python包（所有平台）：**
```bash
pip install frida-tools angr qiling uncompyle6 capstone lief z3-solver
# 对于Python 3.9+字节码：从源代码构建pycdc
git clone https://github.com/zrax/pycdc && cd pycdc && cmake . && make
```

**Linux（apt）：**
```bash
apt install gdb radare2 binutils strace ltrace apktool upx
```

**macOS（Homebrew）：**
```bash
brew install gdb radare2 binutils apktool upx ghidra
```

**radare2插件：**
```bash
r2pm -ci r2ghidra   # radare2的本地Ghidra反编译器
```

**手动安装：**
- pwndbg — Linux: [GitHub](https://github.com/pwndbg/pwndbg), macOS: `brew install pwndbg/tap/pwndbg-gdb`

## 其他资源

- [tools.md](tools.md) - 静态分析工具（GDB、Ghidra、radare2、IDA、Binary Ninja、dogbolt.org、带Capstone的RISC-V、Unicorn仿真、Python字节码、WASM、Android APK、.NET、打包二进制文件）
- [tools-dynamic.md](tools-dynamic.md) - 动态分析工具：Frida（挂钩、反调试绕过、内存扫描、Android/iOS）、angr符号执行（路径探索、约束、CFG）、lldb（macOS/LLVM调试器）、x64dbg（Windows）
- [tools-emulation.md](tools-emulation.md) - 仿真框架和侧信道工具：Qiling（跨平台操作系统级仿真）、Triton（DSE）、Intel Pin指令计数+遗传算法侧信道、仅操作码跟踪重建、LD_PRELOAD时间冻结和memcmp侧信道用于逐字节暴力破解
- [tools-advanced.md](tools-advanced.md) - 高级工具（第1部分）：VMProtect/Themida分析、二进制差异（BinDiff、Diaphora）、反混淆框架（D-810、GOOMBA、Miasm）、Qiling框架、Triton DSE、Manticore、Rizin/Cutter、RetDec、自定义VM字节码提升到LLVM IR
- [tools-advanced-2.md](tools-advanced-2.md) - 高级工具（第2部分）：高级GDB（Python脚本、暴力破解、条件断点、监视点、使用rr的逆向调试、pwndbg/GEF）、高级Ghidra脚本、修补（Binary Ninja API、LIEF）、GDB约束提取+ILP求解器（BackdoorCTF 2017）、GDB位置编码输入零标志监控（EKOPARTY 2017）、LD_PRELOAD仅执行二进制转储（BackdoorCTF 2017）、PEDA current_inst逐位标志抓取器（CONFidence CTF 2019 Teaser）
- [anti-analysis.md](anti-analysis.md) - 反分析分类法：Linux反调试（ptrace、/proc、时序、信号、直接系统调用）、Windows反调试（PEB、NtQueryInformationProcess、堆标志、TLS回调、硬件/软件断点检测、基于异常、线程隐藏）、反VM/沙箱（CPUID、MAC、时序、工件、资源）、反DBI（Frida检测/绕过）、代码完整性/自哈希、反反汇编（不透明谓词、垃圾字节）、MBA识别/简化、综合绕过策略
- [anti-analysis-ctf.md](anti-analysis-ctf.md) - CTF writeup技术：用于执行模式切换的SIGILL处理程序（Hack.lu 2015）、通过strace计数实现SIGFPE信号处理程序侧信道（PlaidCTF 2017）、使用Keystone和Unicorn进行指令跟踪反转（MeePwn 2017）、通过栈帧操作实现无调用函数链接（THC 2018）、通过`process_vm_writev`实现父进程修补子进程二进制转储（Google CTF Quals 2018）
- [patterns.md](patterns.md) - 基础二进制模式：自定义VM、反调试、纳米锁、自修改代码、XOR密码、混合模式加载器、LLVM混淆、S-box/密钥流、SECCOMP/BPF、异常处理程序、内存转储、逐字节变换、x86-64陷阱、自定义混淆反转、基于位置的变换、十六进制编码字符串比较、基于信号的二进制探索
- [patterns-runtime.md](patterns-runtime.md) - 运行时修补和oracle技术：恶意软件反分析绕过、多阶段shellcode加载器、时序侧信道攻击、带诱饵+信号处理程序MBA的多线程反调试（ApoorvCTF 2026）、INT3补丁+coredump暴力破解oracle（Pwn2Win 2016）、信号处理程序链+LD_PRELOAD oracle（Nuit du Hack 2016）、printf格式化字符串VM反编译到Z3（SECCON 2017）、四叉树递归图像格式解析器（Google CTF Quals 2018）
- [patterns-ctf.md](patterns-ctf.md) - 比赛特定模式（第1部分）：隐藏模拟器操作码、LD_PRELOAD密钥提取、SPN静态提取、图像XOR平滑度、逐字节密码、数学收敛位图、Windows PE XOR位图OCR、两阶段RC4+VM加载器、GBA ROM中间相遇、Sprague-Grundy博弈论、内核模块迷宫求解、多线程VM通道、通过字符串差异检测后门共享库、带RC4平面二进制文件的自定义binfmt内核模块、哈希解析导入/无导入勒索软件、ELF节头损坏用于反分析
- [patterns-ctf-2.md](patterns-ctf-2.md) - 比赛特定模式（第2部分）：多层自解密暴力破解、嵌入式ZIP+XOR许可证、栈字符串反混淆、前缀哈希暴力破解、用于整数验证的CVP/LLL格、决策树函数混淆、GF(2^8)高斯消元、ROP链混淆分析（ROPfuscation）
- [patterns-ctf-3.md](patterns-ctf-3.md) - 比赛特定模式（第3部分）：Z3单行Python电路、滑动窗口popcount、通过ioctl实现键盘LED摩尔斯电码、C++析构函数隐藏验证、系统调用副作用内存损坏、MFC对话框事件处理程序、VM顺序密钥链暴力破解、Burrows-Wheeler变换反转、OpenType字体连字漏洞利用、带自修改代码的GLSL着色器VM、作为密码状态的指令计数器、通过objdump实现批量破解自动化、fork+pipe+死分支反分析、通过sigmoid层反转实现TensorFlow DNN反转、通过内核JIT到x64汇编实现BPF过滤器分析
- [languages.md](languages.md) - 语言特定：Python字节码&操作码重映射、Python版本特定字节码、Pyarmor静态解压、DOS存根、Unity IL2CPP、HarmonyOS HAP/ABC、Brainfuck/esolangs（+ BF逐字符静态分析、BF侧信道读取计数oracle、BF比较习语检测）、UEFI、转译为C、代码覆盖侧信道、OPAL函数式逆向、非双射替换、FRACTRAN程序反转
- [languages-platforms.md](languages-platforms.md) - 平台/框架特定：Roblox场所文件分析、Godot游戏资源提取、Rust serde_json模式恢复、Android JNI RegisterNatives混淆、通过/proc/self/maps实现Android DEX运行时字节码修补、通过新项目实现Android原生.so加载绕过、Frida Firebase云函数绕过、Verilog/硬件RE、前缀-by-前缀哈希反转、Ruby/Perl多语言约束满足、Electron ASAR提取+原生二进制分析、Node.js npm运行时内省
- [languages-compiled.md](languages-compiled.md) - Go二进制逆向（GoReSym、goroutines、内存布局、通道操作、embed.FS、用于C2枚举的Go二进制UUID修补）、Rust二进制逆向（反混淆、Option/Result、Vec、panic字符串）、Swift二进制逆向（反混淆、协议见证表）、Kotlin/JVM（协程状态机）、Haskell GHC CMM中间语言用于递归结构分析、C++（vtable重建、RTTI、STL模式）
- [platforms.md](platforms.md) - 平台特定RE：macOS/iOS（Mach-O、代码签名、Objective-C运行时、Swift、dyld、越狱绕过）、嵌入式/IoT固件（binwalk、UART/JTAG/SPI提取、ARM/MIPS、RTOS）、内核驱动（Linux .ko、eBPF、Windows .sys）、游戏引擎（Unreal Engine、Unity、反作弊、Lua）、汽车CAN总线
- [platforms-hardware.md](platforms-hardware.md) - 硬件和高级架构RE：HD44780 LCD控制器GPIO重建、RISC-V高级（自定义扩展、特权模式、调试）、ARM64/AArch64逆向和漏洞利用（调用约定、ROP gadgets、qemu-aarch64-static仿真）
- [field-notes.md](field-notes.md) - 快速参考笔记：二进制类型、反调试绕过、特殊模式、CTF案例笔记

---

## 何时转向

- 如果您已经理解二进制文件并且现在需要堆、ROP或内核漏洞利用，切换到`/ctf-pwn`。
- 如果挑战实际上是关于恢复删除的文件、PCAP数据或磁盘工件，切换到`/ctf-forensics`。
- 如果目标是web应用程序并且您只是逆向一个小的客户端辅助脚本，切换到`/ctf-web`。
- 如果二进制文件实现机器学习模型并且挑战是关于模型攻击或对抗性输入，切换到`/ctf-ai-ml`。
- 如果逆向二进制文件的核心逻辑是密码算法或数学问题，切换到`/ctf-crypto`。
- 如果二进制文件是具有C2、打包或规避行为的真实恶意软件样本，切换到`/ctf-malware`。
- 如果挑战是玩具VM、编码谜题或pyjail而不是真实二进制文件，切换到`/ctf-misc`。

## 问题解决工作流程

1. **从字符串提取开始** - 许多简单挑战有明文flag
2. **尝试ltrace/strace** - 动态分析通常无需逆向即可揭示flag
3. **尝试Frida挂钩** - 挂钩strcmp/memcmp以捕获期望值而无需逆向
4. **尝试angr** - 符号执行自动解决许多flag检查器
5. **尝试Qiling** - 仿真外来架构二进制文件或绕过沉重的反调试而无工件
6. **在修改执行前映射控制流**
7. **通过脚本自动化手动过程**（r2pipe、Frida、angr、Python）
8. **通过比较反编译器输出来验证假设**（dogbolt.org用于并排比较）

## 快速胜利（首先尝试！）

```bash
# 明文flag提取
strings binary | grep -E "flag\{|CTF\{|pico"
strings binary | grep -iE "flag|secret|password"
rabin2 -z binary | grep -i "flag"

# 动态分析 - 通常直接捕获flag
ltrace ./binary
strace -f -s 500 ./binary

# 十六进制转储搜索
xxd binary | grep -i flag

# 使用测试输入运行
./binary AAAA
echo "test" | ./binary
```

## 初始分析

```bash
file binary           # 类型、架构
checksec --file=binary # 安全功能（用于pwn）
chmod +x binary       # 使其可执行
```

## 内存转储策略

**关键见解：** 让程序计算答案，然后转储它。在最终比较处断点（`b *main+OFFSET`），输入任何正确长度的输入，然后`x/s $rsi`转储计算的flag。

## 诱饵flag检测

**模式：** 在真实检查之前有多个假目标。寻找具有不同成功消息的多个连续比较目标。在FINAL比较处设置断点，而不是更早的比较。

## GDB PIE调试

PIE二进制文件随机化基地址。使用相对断点：
```bash
gdb ./binary
start                    # 强制PIE基址解析
b *main+0xca            # 相对于main
run
```

## 比较方向（关键！）

两种模式：(1) `transform(flag) == stored_target` — 反转变换。(2) `transform(stored_target) == flag` — flag就是变换后的数据，只需对存储的目标应用变换。

## 常见加密模式

- 单字节XOR - 尝试所有256个值
- 已知明文XOR（`flag{`, `CTF{`）
- 硬编码密钥的RC4
- 自定义置换+XOR
- 带位置索引的XOR（`^ i`或`^ (i & 0xff)`）与重复密钥分层

## 快速工具参考

```bash
# Radare2
r2 -d ./binary     # 调试模式
aaa                # 分析
afl                # 列出函数
pdf @ main         # 反汇编main

# Ghidra（无头）
analyzeHeadless project/ tmp -import binary -postScript script.py

# IDA
ida64 binary       # 在IDA64中打开
```

## 深入笔记

在第一轮分类后，当您知道目标类型时，使用[field-notes.md](field-notes.md)。

- 目标格式：Python字节码、WASM、Android、Flutter、.NET、UPX、Tauri
- 技术笔记：反调试绕过、VM分析、x86-64陷阱、迭代求解器、Unicorn、时序侧信道
- 平台笔记：Godot、Roblox、macOS/iOS、嵌入式固件、内核驱动、游戏引擎、Swift、Kotlin、Go、Rust、D
- 案例笔记：现代CTF特定逆转变换模式和旧经典挑战模式