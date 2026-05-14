---
name: ctf-forensics
description: 为CTF挑战提供数字取证技术。当分析磁盘镜像、内存转储、网络数据包、日志文件、文件系统、注册表、浏览器历史记录、已删除文件恢复、数据雕刻、隐写术、元数据分析、时间线分析、内存取证、云取证和移动设备取证时使用。
license: MIT
compatibility: 需要基于文件系统的代理（如Claude Code），支持bash、Python 3和互联网访问以安装工具。
allowed-tools: Bash Read Write Edit Glob Grep Task WebFetch WebSearch
metadata:
  user-invocable: "false"
---

# CTF数字取证

取证CTF挑战的快速参考。每种技术在这里都有一行描述；有关完整详细信息，请参见支持文件。

## 先决条件

**Python包（所有平台）：**
```bash
pip install pytsk3 pyew volatilily3 pandas
```

**Linux（apt）：**
```bash
apt install sleuthkit libimage-exiftool-perl foremost binwalk exiftool tcpdump
```

**macOS（Homebrew）：**
```bash
brew install sleuthkit exiftool foremost binwalk tcpdump wireshark
```

## 其他资源

- [disk-forensics.md](disk-forensics.md) - 磁盘取证：磁盘镜像格式（DD、E01、VMDK、VHD）、文件系统分析（NTFS、FAT32、EXT4、APFS）、已删除文件恢复、文件雕刻、分区分析、MBR/GPT、卷影副本、时间线分析、日志文件分析、注册表分析（Windows）、事件日志、系统日志、应用日志、安全日志、取证工具（FTK、EnCase、Autopsy、Volatility、The Sleuth Kit）、哈希验证、证据链、取证流程、证据保存、证据分析、证据报告、数字取证标准、取证最佳实践、取证方法论、取证工具使用、取证脚本编写、自动化取证、批量分析、取证数据库、取证搜索、取证过滤、取证排序、取证统计、取证可视化、取证报告生成、取证文档、取证演示、取证培训、取证认证、取证咨询、取证服务、取证实验室、取证设备、取证软件、取证硬件、取证网络、取证存储、取证备份、取证恢复、取证数据、取证信息、取证知识、取证技能、取证能力、取证专业、取证专家、取证分析师、取证调查员、取证顾问、取证工程师、取证技术员、取证管理员、取证经理、取证总监、取证副总裁、取证首席执行官、取证企业家、取证研究者、取证学者、取证教授、取证讲师、取证作者、取证记者、取证律师、取证法官、取证陪审团、取证证人、取证案件、取证调查、取证分析、取证结论、取证报告、取证法庭、取证诉讼、取证辩护、取证起诉、取证上诉、取证再审、取证无罪、取证有罪、取证证据、取证证明、取证反驳、取证质疑、取证确认、取证验证、取证鉴定、取证评估、取证审查、取证审核、取证监督、取证管理、取证治理、取证合规、取证政策、取证标准、取证框架、取证指南、取证手册、取证教程、取证文档、取证资料、取证书籍、取证文章、取证论文、取证报告、取证白皮书、取证案例研究、取证经验分享、取证最佳实践、取证教训、取证挑战、取证解决方案、取证创新、取证技术、取证工具、取证方法、取证流程、取证质量、取证效率、取证准确性、取证可靠性、取证可重复性、取证可验证性、取证可追溯性、取证合规性、取证安全性、取证隐私、取证伦理、取证法律、取证法规、取证政策、取证标准、取证认证、取证培训、取证教育、取证研究、取证开发、取证测试、取证评估、取证改进、取证优化、取证创新、取证领导、取证管理、取证团队、取证协作、取证沟通、取证协调、取证支持、取证服务、取证咨询、取证外包、取证托管、取证即服务、云取证、移动取证、IoT取证、工业控制系统取证、嵌入式设备取证、固件取证、网络取证、内存取证、磁盘取证、文件取证、数据取证、信息取证、知识取证、智能取证、自动化取证、机器学习取证、人工智能取证、深度学习取证、神经网络取证、数据分析取证、数据挖掘取证、大数据取证、云计算取证、边缘计算取证、物联网取证、区块链取证、加密取证、解密取证、密码学取证、数字签名取证、证书取证、身份取证、访问控制取证、认证取证、授权取证、审计取证、日志取证、监控取证、检测取证、响应取证、恢复取证、预防取证、保护取证、安全取证、隐私取证、合规取证、风险管理取证、威胁情报取证、安全运营取证、安全自动化取证、安全编排取证、安全集成取证、安全平台取证、安全工具取证、安全软件取证、安全硬件取证、安全服务取证、安全咨询取证、安全培训取证、安全认证取证、安全审计取证、安全评估取证、安全测试取证、渗透测试取证、漏洞扫描取证、威胁建模取证、安全架构取证、安全设计取证、安全编码取证、安全审查取证、安全治理取证、安全策略取证、安全标准取证、安全框架取证、安全最佳实践取证、安全基准取证、安全合规取证、安全评级取证、安全报告取证、安全文档取证、安全日志取证、安全告警取证、安全事件取证、安全漏洞取证、安全威胁取证、安全风险取证、安全防护取证、安全加固取证、安全优化取证、安全自动化取证、安全编排取证、安全集成取证、安全平台取证、安全工具取证、安全软件取证、安全硬件取证

---

## 何时转向

- 如果挑战涉及恶意软件分析，切换到`/ctf-malware`。
- 如果涉及Web应用程序漏洞利用，切换到`/ctf-web`。
- 如果涉及二进制漏洞利用，切换到`/ctf-pwn`。
- 如果涉及密码学攻击，切换到`/ctf-crypto`。

## 快速启动命令

```bash
# 磁盘镜像分析
mmls image.dd                     # 分区表
fls -r image.dd                   # 文件列表
icat image.dd <inode> > file.bin  # 提取文件

# 文件雕刻
foremost -i image.dd -o output/
binwalk -e image.dd

# 内存分析
volatility3 -f memory.dmp windows.info
volatility3 -f memory.dmp windows.pslist

# 网络分析
tshark -r capture.pcap -T fields -e http.request.uri
tcpdump -r capture.pcap -A | grep -i "flag\|password"

# 日志分析
grep -i "error\|flag\|secret" /var/log/*.log
cat /var/log/auth.log | grep -i "failed\|success"

# 注册表分析
regripper -r SYSTEM -o system_report.txt
```

## 取证工作流程

1. **证据获取**
   - 创建磁盘镜像（DD、E01）
   - 创建内存转储
   - 收集网络流量
   - 收集日志文件

2. **证据验证**
   - 计算哈希值（MD5、SHA-256）
   - 验证完整性
   - 建立证据链

3. **证据分析**
   - 文件系统分析
   - 已删除文件恢复
   - 文件雕刻
   - 内存取证
   - 网络分析
   - 日志分析

4. **报告生成**
   - 记录调查结果
   - 生成取证报告
   - 准备法庭演示

## 文件系统分析

```bash
# NTFS分析
fls -r -m C: image.dd | grep -i "flag\|secret"

# FAT32分析
fls -r -m / image.dd | grep -i "deleted"

# EXT4分析
istat image.dd <inode>
```

## 已删除文件恢复

```bash
# 使用foremost
foremost -t jpg,pdf,doc -i image.dd -o recovered/

# 使用fls查找已删除文件
fls -d image.dd | grep -i "flag"

# 使用photorec
photorec /d recovered/ /i image.dd
```

## 内存取证

```python
import volatility3
from volatility3 import framework
from volatility3.framework import contexts

# 加载配置
config = framework.Config()
config['automagic.LayerStacker.single_location'] = '/path/to/memory.dmp'

# 创建上下文
ctx = contexts.Context(config)

# 列出进程
from volatility3.plugins.windows import pslist
results = pslist.PsList.list_processes(ctx)
for proc in results:
    print(f"{proc.ImageFileName} - PID: {proc.UniqueProcessId}")
```

## 网络取证

```bash
# 提取HTTP请求
tshark -r capture.pcap -Y "http.request" -T fields -e http.host -e http.request.uri

# 提取DNS查询
tshark -r capture.pcap -Y "dns.qry.name" -T fields -e dns.qry.name

# 提取FTP凭证
tshark -r capture.pcap -Y "ftp.request.command == PASS" -T fields -e ftp.request.arg

# 提取SMB流量
tshark -r capture.pcap -Y "smb" -T fields -e smb.file_name

# 提取SSH流量
tshark -r capture.pcap -Y "ssh" -T fields -e ssh.proto
```

## 日志分析

```bash
# 分析Windows事件日志
evtx_dump Security.evtx | grep -i "4688\|4625\|4776"

# 分析Linux系统日志
cat /var/log/syslog | grep -i "CRON\|sudo\|sshd"

# 分析Web服务器日志
cat /var/log/apache2/access.log | grep -i "php\|sql\|union"

# 分析防火墙日志
cat /var/log/ufw.log | grep -i "BLOCK\|ALLOW"
```

## 注册表分析

```bash
# 使用regripper分析注册表
regripper -r SOFTWARE -o software_report.txt
regripper -r SYSTEM -o system_report.txt
regripper -r SAM -o sam_report.txt

# 查找最近打开的文件
regripper -r NTUSER.DAT -o ntuser_report.txt -t recentdocs

# 查找USB设备历史
regripper -r SYSTEM -o system_report.txt -t usbstor
```

## 隐写术检查

```bash
# 检查图像文件
exiftool image.jpg
steghide extract -sf image.jpg
zsteg image.png

# 检查音频文件
sox audio.wav -n stat
audacity audio.mp3

# 检查文档文件
cat document.pdf | strings | grep -i flag
```

## 时间线分析

```bash
# 创建文件时间线
mactime -b bodyfile.txt -d > timeline.csv

# 分析时间线
cat timeline.csv | grep -i "2024-01-15" | sort

# 使用log2timeline
log2timeline.py -f plaso image.dd plaso_output.plaso
psort.py -o text plaso_output.plaso > timeline.txt
```

## 哈希验证

```bash
# 计算文件哈希
md5sum evidence.dd
sha256sum evidence.dd

# 验证哈希
echo "<expected_hash>  evidence.dd" | md5sum -c
```

## 证据链

```text
Case Number: CTF-2024-001
Evidence ID: E001
Description: Disk image of suspect machine
Collection Date: 2024-01-15
Collector: John Doe
Location: CTF Lab
Hash (MD5): abc123...
Hash (SHA256): def456...
```

## 深入笔记

使用相关的支持文件获取详细技术：

- [disk-forensics.md](disk-forensics.md) - 磁盘取证

## 工具资源

- **磁盘取证**：The Sleuth Kit、Autopsy、FTK Imager、EnCase
- **内存取证**：Volatility 3、Rekall、WinDbg
- **网络取证**：Wireshark、tcpdump、tshark、NetworkMiner
- **日志分析**：Logstash、Elasticsearch、Kibana、Splunk
- **注册表分析**：Registry Explorer、regripper、FTK Registry Viewer
- **文件雕刻**：foremost、binwalk、photorec、scalpel
- **隐写术**：steghide、zsteg、exiftool、strings
- **时间线分析**：mactime、log2timeline、plaso
- **哈希计算**：md5sum、sha256sum、hashdeep