## 如何调试段错误（segmentation fault）

### 使用GBD

1. **编译程序时加上调试信息：**

   ```
   gcc -g -o myprogram myprogram.c
   ```
   
2. **运行GDB：**

   ```
   gdb ./myprogram
   ```
   
3. **设置运行参数（如果有）：**

   ```
   (gdb) set args [your_program_arguments]
   ```
   
4. **运行程序：**

   ```
   (gdb) run
   ```
   
5. **当段错误发生时，GDB会停止程序，并显示出错位置：**

   ```
   Program received signal SIGSEGV, Segmentation fault.
   0x0804841a in main () at myprogram.c:10
   ```

6. **查看调用栈：**

   ```
   (gdb) backtrace
   ```
   
7. **查看具体代码：**

   ```
   (gdb) list
   ```
   
8. **查看变量值：**

   ```
   (gdb) print variable_name
   ```

9. **打断点**

   ```
   在特定行设置断点：
   (gdb) b filename:linenumber
   
   在特定函数设置断点：
   (gdb) b function_name
   
   在特定地址设置断点：
   (gdb) b *address
   
   查看所有断点
   (gdb) info b
   
   删除断点
   (gdb) delete breakpoint_number
   ```

### 检查代码常见错误

1. **检查指针的使用：** 确保所有指针在使用前都已初始化，并指向有效的内存区域。
2. **检查数组越界：** 确保数组的访问索引在合法范围内。
3. **检查动态内存分配：** 确保动态分配的内存在释放前没有被重复释放。

###  添加调试输出

在关键代码处添加打印语句，可以帮助你确定程序执行到哪一步发生了错误。例如：

```
printf("Debug: reached point A\n");
```

## 什么是 core dump ? 以及如何使用gdb对 core dumped 进行调试

Core Dump 是指在程序崩溃时，操作系统将程序的当前内存状态（包括寄存器、堆栈、已分配的堆内存等）写入到一个文件中，这个文件通常被称为 core dump 文件。Core dump 文件可以用于调试，帮助开发人员了解程序崩溃时的状态，从而找出导致崩溃的原因。

1. **使用 GDB 调试 core dump 文件：**

```
gdb ./myprogram core.1234
```

这里，`./myprogram` 是可执行文件，`core.1234` 是 core dump 文件。

2. **在 GDB 中查看崩溃信息：**

```
(gdb) bt
```

`bt`（backtrace）命令会显示崩溃时的调用栈，帮助确定程序崩溃的位置和调用路径。

3. **查看崩溃时的代码：**

```
(gdb) list
```

`list` 命令会显示崩溃点附近的源码。

4. **查看变量的值：**

```
(gdb) print variable_name
```

`print` 命令可以查看崩溃时特定变量的值。

5. **查看内存和寄存器状态：**

- 查看寄存器状态：

```
(gdb) info registers
```

## gdb查看栈帧

1. **`backtrace` (或 `bt`) 命令**

`backtrace` 或简写 `bt` 命令用于显示当前线程的调用栈（栈帧）：

```
(gdb) backtrace
```

或

```
(gdb) bt
```

这将显示一个调用栈的列表，其中每一行代表一个栈帧，包括函数名、参数和源码位置。

### 查看特定栈帧

1. **选择特定栈帧**

通过 `frame` 命令或简写 `f` 命令，可以选择特定的栈帧进行详细查看。栈帧编号在 `backtrace` 输出的左侧显示，例如 `#0` 表示最顶层的栈帧：

```
(gdb) frame 0
```

或

```
(gdb) f 0
```

选择栈帧后，可以查看该栈帧的详细信息，包括局部变量、参数等。

## gdb如何调试多线程

**info threads** 显示当前可调试的所有线程，每个线程会有一个GDB为其分配的ID，后面操作线程的时候会用到这个ID。 前面有*的是当前调试的线程。

**thread ID** 切换当前调试的线程为指定ID的线程。

**break thread_test.c:123 thread all** 在所有线程中相应的行上设置断点

**thread apply ID1 ID2 command** 让一个或者多个线程执行GDB命令command。 

**thread apply all command** 让所有被调试线程执行GDB命令command。

**set scheduler-locking off|on|step** 估计是实际使用过多线程调试的人都可以发现，在使用step或者continue命令调试当前被调试线程的时候，其他线程也是同时执行的，怎么只让被调试程序执行呢？通过这个命令就可以实现这个需求。off 不锁定任何线程，也就是所有线程都执行，这是默认值。 on 只有当前被调试程序会执行。 step **在单步执行时锁定**。GDB 仅在执行单步调试操作（如 `step` 或 `next`）时锁定当前线程，允许其他线程在非单步操作期间继续运行。

## 如何查看网络状态

### 1. ifconfig

`ifconfig`

`ifconfig` 是一个传统的命令，用于查看和配置网络接口：

```
ifconfig
```

### 2. netstat和ss

`netstat` 用于显示网络连接、路由表、接口统计等信息：

```
netstat -an
```

- **-a**: 显示所有连接和监听端口。
- **-t**: 显示TCP协议的连接。
- **-u**: 显示UDP协议的连接。
- **-l**: 显示正在监听的端口。
- **-n**: 数字形式显示地址和端口号。
- **-p**: 显示连接的程序名和PID（需要超级用户权限）。
- **-r**: 显示路由表。
- **-i**: 显示网络接口信息。
- **-s**: 显示各协议的统计信息。
- **-e**: 显示扩展的接口信息（包含更多统计信息）。

`ss` 是 `netstat` 的替代品，速度更快，信息更详细：

```
ss -tuln
```

- `-t`：显示TCP套接字
- `-u`：显示UDP套接字
- `-l`：显示监听套接字
- `-n`：不解析服务名称

## 查看还有多少可用内存命令(free)，可用的包含buff/cache的大小吗？

在Linux系统中，可以使用 `free` 命令来查看系统的内存使用情况。`free` 命令显示的信息包括总内存、已用内存、空闲内存以及缓冲区/缓存的使用情况。

以下是 `free` 命令的输出示例及解释：

```
free -h
```

输出示：

```
              total        used        free      shared  buff/cache   available
Mem:            16G         8G         2G        1.5G         6G         6G
Swap:          2.0G        0.0G       2.0G
```

字段解释：

- **total**: 总内存。
- **used**: 这列显示了已使用的内存量，包括了系统和应用程序使用的内存以及用于缓冲区和缓存的内存。`used (in detail) = used (application) + buff/cache`
- **free**: 这列显示了未被分配给任何用途的内存，即纯空闲的内存。
- **shared**: 共享内存。
- **buff/cache**: 这列显示了系统中用于缓冲区和缓存的内存总量。缓冲区（buffer）和缓存（cache）用于加速文件读写操作和数据存取。
- **available**: 这列显示了当前可用的内存量，供新启动的应用程序使用。它考虑到了`buff/cache`中可以被释放和重新分配的内存。`available = free + buff/cache (which can be reclaimed) - shared`

## Linux网络抓包用什么命令来实现？

在Linux系统中，网络抓包通常使用`tcpdump`和`Wireshark`这两个工具。下面介绍这两个工具的基本用法：

### 1. `tcpdump`

`tcpdump` 是一个强大的命令行网络抓包工具，可以捕获和分析通过网络接口的数据包。

#### 安装

在大多数Linux发行版上，你可以使用包管理器来安装`tcpdump`：

```
sudo apt-get install tcpdump   # 在Debian/Ubuntu上
```

#### 基本用法

- **抓取所有接口的数据包**：

  ```
  sudo tcpdump
  ```
  
- **抓取指定接口的数据包**：

  ```
  sudo tcpdump -i eth0
  ```
  
- **保存抓包结果到文件**：

  ```
  sudo tcpdump -i eth0 -w capture.pcap
  ```
  
- **读取抓包文件**：

  ```
  sudo tcpdump -r capture.pcap
  ```
  
- **过滤特定的数据包（例如，只抓取来自特定IP地址的数据包）**：

  ```
  sudo tcpdump -i eth0 src 192.168.1.1
  ```
  
- **抓取特定端口的数据包**：

  ```
  sudo tcpdump -i eth0 port 80
  ```

#### 常用选项

- `-i <interface>`：指定要监听的网络接口。
- `-w <file>`：将抓包结果写入文件。
- `-r <file>`：读取抓包文件并解析。
- `-c <count>`：指定抓取的数据包数量。
- `-n`：不解析主机名和服务名，直接显示IP地址和端口号。

### 2. Wireshark

`Wireshark` 是一个功能强大的图形化网络协议分析工具，提供更丰富的分析和可视化功能。

#### 安装

在大多数Linux发行版上，你可以使用包管理器来安装`Wireshark`：

```
sudo apt-get install wireshark   # 在Debian/Ubuntu上
```

安装过程中，系统会提示是否允许非root用户捕获数据包，建议选择“是”。

#### 使用

- **启动Wireshark**：

  ```
  wireshark
  ```
  
  或者在应用菜单中找到Wireshark并启动。

- **选择网络接口**：

  启动Wireshark后，会显示可用的网络接口列表，选择你要抓包的接口。

- **开始捕获**：

  点击“Start”按钮开始捕获数据包。

- **应用过滤器**：

  在捕获过程中，可以在顶部的过滤器栏中输入过滤表达式，例如：

  - `ip.addr == 192.168.1.1`：过滤指定IP地址的数据包。
  - `tcp.port == 80`：过滤特定端口的数据包。
  - `http`：过滤HTTP协议的数据包。

- **保存抓包结果**：

  在菜单中选择“File”->“Save As...”将抓包结果保存为`.pcap`文件。

- **读取抓包文件**：

  在菜单中选择“File”->“Open”打开已有的抓包文件。

### 3. 示例

假设你想抓取网络接口`eth0`上所有到端口80（HTTP）的数据包并保存到文件`http_traffic.pcap`：

使用`tcpdump`：

```
sudo tcpdump -i eth0 port 80 -w http_traffic.pcap
```

使用`Wireshark`：

1. 启动Wireshark。
2. 选择`eth0`接口。
3. 在过滤器栏中输入`tcp.port == 80`。
4. 点击“Start”按钮开始捕获。
5. 捕获一段时间后，点击“Stop”按钮停止捕获。
6. 保存捕获结果为`http_traffic.pcap`文件。

通过以上方法，你可以方便地在Linux系统上进行网络抓包分析。

## awk命令

`awk`是一种强大的文本处理工具，常用于模式匹配和数据操作。它可以逐行读取输入，并根据特定的模式进行处理，常用于日志文件分析和格式化数据输出。

```bash
awk 'pattern { action }' file
pattern：指定一个模式，awk 仅对匹配此模式的行执行动作。
action：指定一个动作，awk 对每一行执行的操作。

打印文件的所有行
awk '{ print }' filename

$0: 代表整行。
$1, $2, ... : 代表每行中的第一个、第二个字段，依此类推。
awk '{ print $1 }' filename

打印包含特定字符串的行
awk '/pattern/ { print }' filename

/var/log/syslog 文件中筛选出包含 "ERROR" 字符串的行，并打印这些行的内容。
awk '/ERROR/ {print $0}' /var/log/syslog
{print $0}：这是一个动作（action），表示对匹配到的每一行执行打印操作。$0 表示当前行的全部内容。
```

## 平时怎么查看日志？

> 查看日志时，我经常使用以下命令：
>
> - `cat`: 直接查看整个日志文件。
> - `less`: 分页查看长日志文件，支持向上向下滚动，翻页。less不必读取整个文件加载速度更快
> - more：more不支持上下滚动，只支持翻页，
> - `tail`: 实时（-f）查看日志文件的最新内容: `tail -f /var/log/syslog`
> - `grep`: 结合`grep`筛选出包含特定关键字的日志条目: `grep "ERROR" /var/log/syslog`
> - `awk`和`sed`: 用于更复杂的日志处理和格式化: `awk '/ERROR/ {print $0}' /var/log/syslog`

## top命令

`top`命令非常常用，用于实时显示系统的运行情况，包括CPU和内存的使用率、每个进程的资源消耗等信息。

运行`top`命令之后，可以看到各个进程的资源使用情况，可以按`q`退出，按`h`获取帮助，按`k`终止进程，按`M`排序内存使用等。

## du和df命令

`du`（disk usage）命令用于显示文件和目录的磁盘使用情况。

`df`（disk free）命令用于显示文件系统的磁盘空闲空间。

## 怎么查看文件被谁占用了，用什么命令lsof

**查看某个特定文件被哪个进程占用**：

```sh
lsof /path/to/your/file
```

## 怎么看进程占用了什么端口

netstat

lsof -i：端口号  // lsof -i显示网络连接

## 查看进程号PID打开了哪些文件

`lsof -p <PID>`

## netstat详解

**常见的 netstat 命令选项：**

- **-a:** 显示所有连接，包括监听的端口
- **-t:** 只显示 TCP 连接
- **-u:** 只显示 UDP 连接
- **-n:** 以数字形式显示 IP 地址和端口号
- **-p:** 显示进程信息

以`netstat -napt`为例子

```shell
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 127.0.0.1:6010          0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:5939          0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:3000          0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:8388          0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:37387         0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:35921         0.0.0.0:*               LISTEN      47947/code-fee1edb8 
tcp        0      0 127.0.0.1:35051         0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:44331         0.0.0.0:*               LISTEN      -                   
tcp        0     65 127.0.0.1:35921         127.0.0.1:41778         ESTABLISHED 47947/code-fee1edb8 
```

**Proto:**

- **协议**：表示该连接使用的网络协议，常见的包括 TCP（传输控制协议）和 UDP（用户数据报协议）。TCP 是一种面向连接的协议，提供可靠的数据传输；UDP 是一种无连接的协议，数据传输的可靠性较低。

**Recv-Q:**

- **接收队列**：表示本地系统接收但尚未处理的数据包数量。通常情况下，这个值应该保持较小，如果过大可能表示网络拥塞或应用程序处理能力不足。

**Send-Q:**

- **发送队列**：表示本地系统已经准备发送但尚未发送出去的数据包数量。同样，这个值也应该保持较小。

**Local Address:**

- **本地地址**：表示本地系统参与该连接的 IP 地址和端口号。IP 地址是计算机在网络中的唯一标识，端口号则用于区分不同的应用程序。

**Foreign Address:**

- **远端地址**：表示远端系统参与该连接的 IP 地址和端口号。

**State:**

- 状态

  ：表示该连接当前所处的状态。TCP 连接的状态比较复杂，常见的包括：

  - **ESTABLISHED**：已建立连接
  - **SYN_SENT**：发送 SYN 报文，等待对方确认
  - **SYN_RECV**：收到 SYN 报文，等待发送确认
  - **FIN_WAIT1**、**FIN_WAIT2**、**TIME_WAIT**、**CLOSE_WAIT** 等：表示连接关闭过程中的各种状态
  - UDP 连接的状态相对简单，通常只有 **ESTABLISHED**。

**PID/Program name:**

- **进程 ID/程序名**：表示发起或接受该连接的进程的 ID 和名称。通过这个信息，可以定位到哪个应用程序正在使用该网络连接。

## find命令

```bash
find [搜索路径] [搜索条件] [操作]

# 按照名字 name名字，iname忽略大小写
find -name

#按照类型查找 f 表示文件 d表示目录
find -type f 

# 按照大小
find /path/to/search -size [+|-]N[c|k|M|G]

# 查找以.doc结尾的文件
find -type t -name "*.doc"
```



## 如何查看僵死进程？

僵死进程在`ps`命令的输出中会显示为`Z`状态。可以用以下命令列出所有僵死进程：ps aux | grep 'Z'

或者更加详细的方式： ps -ef | grep defunct

找到父进程进行处理一般是解决办法之一，终止或重启父进程可能会清理掉这些僵死进程。



**查看系统资源**

1. **top命令**：这是一个实时显示系统中各个进程的资源占用状况的监视器。它可以展示CPU使用率、内存使用率、进程状态等信息。通过top命令，用户可以快速识别哪些进程占用了过多的系统资源。
2. **ps命令**：用于显示当前系统中活动进程的信息。通过执行“ps -ef”或“ps aux”，用户可以查看所有进程的详细信息，包括进程ID、CPU和内存使用率等。此外，结合grep命令，可以方便地查找特定进程。
3. **free命令**：用于显示系统的内存使用情况。执行“free -m”可以以MB为单位显示内存使用情况，包括总内存、已用内存、空闲内存等信息。
4. **df命令**：用于显示磁盘分区上的可用和已使用的磁盘空间。执行“df -h”可以以人类可读的格式（如GB、MB）显示磁盘空间使用情况。
5. **sar命令**：这是一个系统活动报告工具，可以用于收集、报告和保存系统活动信息。通过sar命令，用户可以查看CPU使用率、内存使用率、I/O等历史数据。
6. **vmstat命令**：用于显示关于系统虚拟内存、进程、CPU活动等的统计信息。通过vmstat命令，用户可以实时监控系统的资源使用情况。

**查看日志**

1. **tail命令**：常用于查看实时变化的日志文件。例如，“tail -f /var/log/syslog”可以实时查看系统日志的最新内容。此外，“tail -n [行数] 文件名”可用于查看文件的最后几行。
2. **cat命令**：用于显示文件内容。结合grep命令，可以搜索关键字附近的日志内容。例如，“cat /var/log/syslog | grep '关键字'”可以搜索包含特定关键字的日志行。
3. **less命令**：这是一个强大的文本查看器，允许用户向前和向后翻页查看文件内容。对于大型日志文件，less命令提供了方便的导航和搜索功能。
4. **head命令**：与tail命令相反，head命令用于查看文件的开头部分。例如，“head -n [行数] 文件名”可以查看文件的前几行。
5. **more命令**：以全屏幕的方式按页显示文件内容，适合查看长文件。

**查看网络状态**

1. **ifconfig**：此命令用于显示和配置网络接口的详细信息，包括IP地址、MAC地址、网络掩码等。例如，ifconfig 可以列出所有活动网卡的信息。
2. **ip**：ip 命令是一个多功能的网络工具，用于显示或操作路由、网络设备、策略路由和隧道。例如，ip addr 可以显示网络接口的详细信息，类似于 ifconfig；ip route 可以显示路由表信息。
3. **netstat**： 此命令用于显示网络连接、路由表、接口统计等网络相关信息。例如，netstat -an 可以显示所有活动的网络连接和监听的端口。
4. **ss**：ss 命令是一个比 netstat 更强大的工具，用于查看系统的socket统计信息。它可以提供更详细的信息，并且比 netstat 更快。例如，ss -tuln 可以列出所有TCP和UDP的监听端口。
5. **ping**：此命令用于测试网络的连通性，通过发送ICMP回显请求数据包到目标主机，并等待接收回显回复数据包来判断网络是否连通。例如，ping [www.baidu.com](https://gw-c.nowcoder.com/api/sparta/jump/link?link=https%3A%2F%2Fjuejin.cn%2Feditor%2Fdrafts%2F7363195315052445748) 可以测试到Baidu的网络连通性。

## strace命令

`strace` 是 Linux 中非常有用的调试工具，主要用于跟踪系统调用（system calls）和信号（signals）。

`strace` 的输出信息包括：

- 每个系统调用的名称
- 系统调用的参数
- 系统调用的返回值
- 时间信息（可选）
- 信号的捕获和处理

```bash
可以追踪程序并追踪系统调用
strace ./my_program
追踪进程
strace -p <PID>

 -tt 在每行输出的前面，显示毫秒级别的时间
 -T 显示每次系统调用所花费的时间
 -v 对于某些相关调用，把完整的环境变量，文件stat结构等打出来。
 -f 跟踪目标进程，以及目标进程创建的所有子进程
 -e 控制要跟踪的事件和跟踪行为,比如指定要跟踪的系统调用名称
 -o 把strace的输出单独写到指定的文件
 -s 当系统调用的某个参数是字符串时，最多输出指定长度的内容，默认是32个字节
 -p 指定要跟踪的进程pid, 要同时跟踪多个pid, 重复多次-p选项即可。
```

场景

**调试程序异常（如崩溃、死锁等）**：当程序崩溃或卡住时，`strace` 可以帮助你跟踪最后执行的系统调用，分析导致问题的根源。

**分析程序性能问题**：`strace` 可以帮助你发现系统调用的瓶颈。例如，如果一个程序频繁进行 `read` 或 `write` 调用，你可以用 `-T` 选项查看每个调用的耗时，找到潜在的性能问题。

**分析文件系统操作**：通过 `strace`，你可以查看程序对文件的所有操作，比如打开哪些文件，读取或写入了哪些数据。

**网络问题排查**：当你怀疑程序的网络通信出现问题时，使用 `strace` 跟踪网络系统调用，可以帮助定位问题。

**查看程序依赖的库文件**：通过跟踪文件系统调用，你可以看到程序动态加载的库文件以及它们的位置。

## git

### git pull和git fetch的区别？

fetch：抓取指令就是将仓库里的更新都抓取到本地，不会进行合并

如果不指定远端名称和分支名，则抓取所有分支。 

pull：拉取指令就是将远端仓库的修改拉到本地并**自动进行合并**，等同于fetch+merge 

如果不指定远端名称和分支名，则抓取所有并更新当前分支。

### git rebase和merge的区别

git merge：`git merge` 会通过创建一个新的“合并提交（merge commit）”将两个分支的历史合并在一起。

git rebase：`git rebase` 会将一个分支的所有提交“重新应用”到另一个分支的最新提交之后，使提交历史呈现为一条线性的形式。

# Linux内核

## 内核镜像格式有几种？分别有什么区别？

1. linux内核经过编译后也会生成一个**elf格式的可执行程序**，叫**vmlinux**或**vmlinuz**，这个就是原始的未经任何处理加工的原版内核elf文件；嵌入式系统部署时烧录的一般不是这个 vmlinuz/vmlinux，而是要用**objcopy**工具去制作成烧录镜像格式，经过制作加工成烧录镜像的文件就叫**Image**（制作把78M大的精简成了7.5M，因 此这个制作烧录镜像主要目的就是缩减大小，节省磁盘）。 

2. 原则上Image就可以直接被烧录到Flash上进行启动执行，但是实际上并不是 这么简单。实际上linux的作者们觉得Image还是太大了所以对Image进行了压缩，并且在image压缩后的文件的前端附加了一部分解压缩代码。构成了一个压缩格式的镜像就叫zImage。（因为当年Image大小刚好比一张软盘（软盘有2种，1.2M的和1.44MB两种）大，为了节省1张软盘的钱于是乎设计了这种压缩Image成zImage的技术）。 

3. uboot为了启动linux内核，还发明了一种内核格式叫uImage。uImage是由zImage加工得到的， uboot中有一个工具，可以将zImage加工生成uImage。注意：uImage不关linux内核的事，linux 内核只管生成zImage即可，然后uboot中的mkimage工具再去由zImage加工生成uImage来给 uboot启动。这个加工过程其实就是在zImage前面加上64字节的uImage的头信息即可。 

4. 原则上uboot启动时应该给他uImage格式的内核镜像，但是实际上uboot中也可以支持zImage， 是否支持就看x210_sd.h中是否定义了LINUX_ZIMAGE_MAGIC这个宏。所以大家可以看出：有些 uboot是支持zImage启动的，有些则不支持。但是所有的uboot肯定都支持uImage启动。 

   通过上面的介绍我们了解了内核镜像的各种格式，如果通过uboot启动内核，Linux必须为uImage 格式。

## 为什么需要区分内核和用户空间？

在 CPU 的所有指令中，有些指令是非常危险的，如果错用，将导致系统崩溃，比如**清内存、设置时钟**等。如果允许所有的程序都可以使用这些指令，那么系统崩溃的概率将大大增加。

所以，CPU 将指令分为特权指令和非特权指令，对于那些危险的指令，只允许操作系统及其相关模块使用，普通应用程序只能使用那些不会造成灾难的指令。比如 Intel 的 CPU 将特权等级分为 4 个级别： Ring0~Ring3。 

其实 Linux 系统只使用了 Ring0 和 Ring3 两个运行级别(Windows 系统也是一样的)。当进程运行在 Ring3 级别时被称为运行在用户态，而运行在 Ring0 级别时被称为运行在内核态。

## 用户空间与内核通信方式有哪些？

在Linux系统中，用户空间和内核空间需要进行通信，以便用户空间的应用程序能够使用内核提供的服务和资源。以下是用户空间与内核通信的主要方式：

### 1. 系统调用（System Call）

系统调用是用户空间与内核空间通信的最基本方式。系统调用提供了用户程序与内核交互的接口，通过系统调用，用户程序可以请求内核执行特定的操作，如文件操作、内存管理、进程控制等。

#### 示例：

```c
#include <unistd.h>
#include <sys/syscall.h>

int main() {
    syscall(SYS_write, 1, "Hello, World!\n", 14);
    return 0;
}
```

在这个例子中，`syscall`函数用于直接进行系统调用，向标准输出写入数据。

### 2. `/proc` 文件系统和 `/sys` 文件系统

`/proc` 和 `/sys` 文件系统是虚拟文件系统，提供了访问内核数据结构和配置内核参数的接口。用户可以通过读写这些文件来获取系统信息或配置系统行为。

#### 示例：

```bash
cat /proc/cpuinfo
echo 1 > /proc/sys/net/ipv4/ip_forward
```

### 3. `ioctl`（Input/Output Control）

`ioctl`是一种设备控制接口，通过它可以向**设备发送控制命令或获取设备状态**。`ioctl`函数允许用户空间程序对设备进行更细粒度的控制。

#### 示例：

```c
#include <sys/ioctl.h>
#include <fcntl.h>
#include <unistd.h>

int main() {
    int fd = open("/dev/some_device", O_RDWR);
    if (fd == -1) {
        // handle error
        return -1;
    }
    int result = ioctl(fd, SOME_IOCTL_COMMAND, NULL);
    close(fd);
    return 0;
}
```

### 4. Netlink 套接字

Netlink 套接字是一种特殊的套接字，用于用户空间进程与内核之间的双向通信。它广泛应用于网络子系统，但也可以用于其他子系统。

#### 示例：

```c
#include <linux/netlink.h>
#include <sys/socket.h>

int main() {
    int sock_fd = socket(AF_NETLINK, SOCK_RAW, NETLINK_ROUTE);
    if (sock_fd == -1) {
        // handle error
        return -1;
    }
    // 设置netlink地址和消息等
    // ...
    close(sock_fd);
    return 0;
}
```

### 5. 文件操作（读/写）

用户空间程序可以通过标准文件操作（如`open`、`read`、`write`）与**字符设备或块设备**进行通信。设备驱动程序在内核中实现，对应设备节点通常位于`/dev`目录下。

#### 示例：

```c
#include <fcntl.h>
#include <unistd.h>

int main() {
    int fd = open("/dev/some_device", O_RDWR);
    if (fd == -1) {
        // handle error
        return -1;
    }
    char buffer[128];
    ssize_t bytes_read = read(fd, buffer, sizeof(buffer));
    // handle read data
    close(fd);
    return 0;
}
```

### 6. mmap（内存映射）

`mmap`允许用户空间程序将文件或设备的内容映射到进程的地址空间中，从而直接访问物理内存中的数据。这对于共享内存和高效的数据传输非常有用。

#### 示例：

```c
c复制代码#include <sys/mman.h>
#include <fcntl.h>
#include <unistd.h>

int main() {
    int fd = open("/dev/some_device", O_RDWR);
    if (fd == -1) {
        // handle error
        return -1;
    }
    void *map = mmap(NULL, length, PROT_READ | PROT_WRITE, MAP_SHARED, fd, 0);
    if (map == MAP_FAILED) {
        // handle error
        close(fd);
        return -1;
    }
    // 直接访问映射内存
    munmap(map, length);
    close(fd);
    return 0;
}
```

### 7. 信号

内核空间出现一些异常时候会发送信号给进程，如SIGSEGV、SIGILL、SIGPIPE等。

### 总结

用户空间与内核通信的方式多种多样，包括系统调用、虚拟文件系统（/proc 和 /sys）、ioctl、Netlink 套接字、标准文件操作、mmap、信号等。选择合适的通信方式取决于具体的应用场景和需求。理解这些通信机制有助于开发高效、可靠的用户空间与内核交互程序。

## 内核链表为什么具有通用性

内核中由于要管理大量的设备，但是各种设备各不相同，必须将他们统一起来管理，于是内核设计者就 想到了使用通用链表来处理，通用链表看似神秘，实际上就是**双向循环链表**，这个链表的每个节点都是 只有指针域，没有任何数据域。

![image-20240710135321259](C:/Users/10503/AppData/Roaming/Typora/typora-user-images/image-20240710135321259.png)

使用通用链表的好处是：

1. 通用链表中每个节点中没有数据域，也就是说无论数据结构有多复杂在链表中只有前后级指针。 
2. 如果一个数据结构（即是描述设备的设备结构体）想要用通用链表管理，只需要在结构体中包含节点的字段即可。
3. 双向链表可以从任意一个节点的前后遍历整个链表，遍历非常方便。 
4. 使用循环链表使得可以不断地循环遍历管理节点，像进程的调度：操作系统会把就绪的进程放在一 个管理进程的就绪队列的通用链表中管理起来，循环不断地，为他们分配时间片，获得cpu进行周 而复始的进程调度。

## 系统调用的作用

系统调用（System Call）是操作系统内核提供的一组接口，用于用户空间的应用程序与内核空间进行**通信和交互**。系统调用是用户程序请求操作系统服务的唯一途径，操作系统通过系统调用为用户程序提供各种资源管理和服务功能。

### 1. 进程管理

系统调用用于创建、控制和终止进程，提供多任务处理和进程间通信的支持。

#### 示例：

- **`fork()`**：创建一个子进程。
- **`exec()`**：在当前进程中执行一个新程序。
- **`wait()`**：等待子进程结束。
- **`exit()`**：终止进程。

```
c复制代码#include <unistd.h>
#include <sys/wait.h>

int main() {
    pid_t pid = fork();
    if (pid == 0) {
        // 子进程
        execlp("/bin/ls", "ls", NULL);
    } else {
        // 父进程
        wait(NULL);
    }
    return 0;
}
```

### 2. 文件操作

系统调用提供文件和目录的创建、删除、读写、关闭等操作，使得程序可以访问和管理文件系统。

#### 示例：

- **`open()`**：打开一个文件。
- **`read()`**：从文件读取数据。
- **`write()`**：向文件写入数据。
- **`close()`**：关闭文件。

```
c复制代码#include <fcntl.h>
#include <unistd.h>

int main() {
    int fd = open("example.txt", O_RDONLY);
    if (fd == -1) {
        // handle error
        return -1;
    }
    char buffer[128];
    ssize_t bytes_read = read(fd, buffer, sizeof(buffer));
    // 处理读取的数据
    close(fd);
    return 0;
}
```

### 3. 设备管理

系统调用用于访问和控制硬件设备，包括终端、磁盘、网络接口等。

#### 示例：

- **`ioctl()`**：控制设备。
- **`mmap()`**：映射设备内存到用户空间。

```
c复制代码#include <fcntl.h>
#include <unistd.h>
#include <sys/mman.h>

int main() {
    int fd = open("/dev/some_device", O_RDWR);
    if (fd == -1) {
        // handle error
        return -1;
    }
    void *map = mmap(NULL, length, PROT_READ | PROT_WRITE, MAP_SHARED, fd, 0);
    if (map == MAP_FAILED) {
        // handle error
        close(fd);
        return -1;
    }
    // 访问映射的内存
    munmap(map, length);
    close(fd);
    return 0;
}
```

### 4. 网络通信

系统调用提供创建和管理网络连接的功能，使得程序能够通过网络进行数据传输。

#### 示例：

- **`socket()`**：创建一个套接字。
- **`connect()`**：连接到远程主机。
- **`send()`**：发送数据。
- **`recv()`**：接收数据。

```
c复制代码#include <sys/socket.h>
#include <arpa/inet.h>
#include <unistd.h>

int main() {
    int sockfd = socket(AF_INET, SOCK_STREAM, 0);
    if (sockfd == -1) {
        // handle error
        return -1;
    }
    struct sockaddr_in server_addr;
    server_addr.sin_family = AF_INET;
    server_addr.sin_port = htons(8080);
    server_addr.sin_addr.s_addr = inet_addr("127.0.0.1");

    if (connect(sockfd, (struct sockaddr *)&server_addr, sizeof(server_addr)) == -1) {
        // handle error
        close(sockfd);
        return -1;
    }
    const char *msg = "Hello, server!";
    send(sockfd, msg, strlen(msg), 0);
    char buffer[128];
    recv(sockfd, buffer, sizeof(buffer), 0);
    // 处理接收到的数据
    close(sockfd);
    return 0;
}
```

### 5. 内存管理

系统调用提供内存分配和管理功能，使得程序可以动态分配和释放内存。

#### 示例：

- **`brk()`**：调整数据段的终止位置。
- **`mmap()`**：映射文件或设备到内存。

```
c复制代码#include <sys/mman.h>
#include <unistd.h>

int main() {
    void *mem = mmap(NULL, 4096, PROT_READ | PROT_WRITE, MAP_PRIVATE | MAP_ANONYMOUS, -1, 0);
    if (mem == MAP_FAILED) {
        // handle error
        return -1;
    }
    // 使用分配的内存
    munmap(mem, 4096);
    return 0;
}
```

### 6. 进程间通信（IPC）

系统调用提供进程间通信的机制，使得多个进程可以协同工作和交换数据。

#### 示例：

- **`pipe()`**：创建管道。
- **`shmget()`**、**`shmat()`**：共享内存。
- **`semget()`**、**`semop()`**：信号量。
- **`msgget()`**、**`msgsnd()`**、**`msgrcv()`**：消息队列。

```
c复制代码#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/shm.h>
#include <unistd.h>

int main() {
    key_t key = ftok("shmfile", 65);
    int shmid = shmget(key, 1024, 0666 | IPC_CREAT);
    char *str = (char *)shmat(shmid, (void *)0, 0);
    // 使用共享内存
    shmdt(str);
    shmctl(shmid, IPC_RMID, NULL);
    return 0;
}
```

### 7. 时间管理

系统调用提供获取和设置系统时间、定时器和睡眠功能。

#### 示例：

- **`time()`**：获取当前时间。
- **`gettimeofday()`**：获取当前时间的详细信息。
- **`sleep()`**：使进程休眠。

```
c复制代码#include <unistd.h>
#include <sys/time.h>
#include <iostream>

int main() {
    struct timeval tv;
    gettimeofday(&tv, NULL);
    std::cout << "Current time: " << tv.tv_sec << std::endl;
    sleep(2); // 休眠2秒
    std::cout << "Wake up after 2 seconds" << std::endl;
    return 0;
}
```

### 8. 安全和权限管理

系统调用提供设置文件和进程的权限、用户和组管理的功能。

#### 示例：

- **`chmod()`**：更改文件权限。
- **`chown()`**：更改文件所有者。
- **`setuid()`**、**`setgid()`**：设置用户和组ID。

```
c复制代码#include <unistd.h>

int main() {
    if (setuid(1000) == -1) {
        // handle error
        return -1;
    }
    return 0;
}
```

## 什么是bootloader？

Linux系统要启动就必须需要一个bootloader启动程序，也就是说芯片上电之后先运行一段bootloader程序。这段bootloader程序会初始化时钟，看门狗，中断，SDRAM等外设，然后将linux内核从flash（NAND，NOR，FLASH，SD，MMC）拷贝到SDRAM，最后启动linux内核。总的来说，Bootloader就是一小段程序，他在系统上电开始执行，初始化硬件设备，准备好软件环境，最后调用OS内核

## uboot启动过程做了哪些事

**第一阶段**

**时钟初始化**，关闭看门狗，关中断，启动ICACHE，关闭DCACHE和TLB，关闭MMU（ICACHE为指令缓存，可以不关闭，指令直接操作的硬件，实际的物理地址。但是DCACHE就必须要关闭，此时MMU没有使能，虚拟地址映射不成功，sdram无法访问，DCACHE无数据），**初始化SDRAM**，初始化NAND FLASH，复制 Bootloader的第二阶段代码到SDRAM空间中（重定位）

**第二阶段**

串口初始化(至少一个，用于调试)，检测系统内存映射（确定板上使用了多少内存，地址空间是什么），将内核映像和根文件系统映象从FLASH上读到SDRAM，**为内核设置启动参数**
（**Bootloader将参数放在某个约定的地方之后，再启动内核，内核启动后从这个地方获得参数**。），调用内核

## uboot和内核如何完成参数传递的？

将内核映像和根文件系统映象从FLASH上读到SDRAM，为内核设置启动参数，调用内核

**具体是如何跳转呢**？做法很简单，**直接修改PC寄存器的值为Linux内核所在的地址**，这样CPU就会从Linux内核所在的地址去取指令，从而执行内核代码。

将内核存放在适当的位置后，直接跳到它的入口点即可调用内核。调用内核之前，下列条件要满足：
  （1）CPU寄存器的设置
  R0=0（规定）。
  R1=机器类型ID；对于ARM结构的CPU，其机器类型ID可以参见 linux/arch/arm tools/ mach-types
  R2=启动参数标记列表在RAM中起始基地址（下面会详细介绍如何传递参数）。
  （2）CPU工作模式
  必须禁止中断（IRQ和FIQ，uboot启动是一个完整的过程，没有必要也不能被打断）
  CPU必须为SVC（Supervisor Mode 管理模式，超级用户模式）模式（为什么呢？主要是像异常模式、用户模式都不合适。具体深入的原因自己可以查下资料）。
  （3） Cache和MMU的设置
  MMU必须关闭。
  指令 Cache可以打开也可以关闭。
  数据 Cache必须关闭。

其中上面第一步CPU寄存器的设置中，就是通过R0,R1,R2三个参数给内核传递参数的。

## 为什么要给内核传递参数呢？

  在此之前，uboot已经完成了硬件的初始化，可以说已经”适应了“这块开发板。然而，内核并不是对于所有的开发板都能完美适配的（如果适配了，可想而知这个内核有多庞大，又或者有新技术发明了，可以完美的适配各种开发板），此时对于开发板的环境一无所知。所以，要想启动Linux内核，uboot必须要给内核传递一些必要的信息来告诉内核当前所处的环境。

## 如何给内核传递参数？

  因此，uboot就把机器ID通过R1传递给内核，Linux内核运行的时候首先就从R1中读取机器ID来判断是否支持当前机器。这个机器ID实际上就是开发板CPU的ID，每个厂家生产出一款CPU的时候都会给它指定一个唯一的ID，大家可以到uboot源码的arch\arm\include\asm\mach-type.h文件中去查看。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200520214753230.png)

R2存放的是块内存的基地址，这块内存中存放的是uboot给Linux内核的其他参数。这些参数有**内存的起始地址、内存大小、Linux内核启动后挂载文件系统的方式等信息**。很明显，参数有多个，不同的参数有不同的内容，为了让Linux内核能精确的解析出这些参数，双方在传递参数的时候要求参数在存放的时猴需要按照双方规定的格式存放。

  除了约定好参数存放的地址外，还要规定参数的结构。Linux2.4.x以后的内核都期望以标记列表（tagged_list）的形式来传递启动参数。标记，就是一种数据结构；标记列表，就是挨着存放的多个标记。标记列表以标记ATAG_CORE开始，以标记ATAG_NONE结束。

  标记的数据结构为tag，它由一个tag_header结构和一个联合（union）组成。tag_header结构表示标记的类型及长度，比如是表示内存还是表示命令行参数等。对于不同类型的标记使用不同的联合（union），比如表示内存时使用tag_ mem32，表示命令行时使用 tag_cmdline。具体代码见arch\arm\include\asm\setup.h。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200520214935996.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE2OTMzNjAx,size_16,color_FFFFFF,t_70#pic_center)

从上面可以看出，struct_tag结构体由structtag_header+联合体union构成，结构体struct tag_header用来描述每个tag的头部信息，如tag的类型，tag大小。联合体union用来描述每个传递给Linux内核的参数信息

## 为什么uboot要关闭caches

caches是cpu内部的一个2级缓存，作用是将常用的数据和指令放在cpu内部，caches是通过CP15管理的，刚上电的时候，cpu还不能管理caches，上电的时候icache可关也可开，但dcache必须关闭，否则导致刚开始的代码里面，去取数据的时候从cache取，而cache中的数据可能不是最新的，导致数据不一致的问题。

> CP15是ARM架构中的协处理器（Coprocessor），用于管理系统控制寄存器和内存管理单元（MMU）。在ARM处理器中，CP15负责控制系统的关键功能，包括缓存（Cache）、虚拟内存、存储保护等。通过CP15，操作系统和启动代码可以配置和管理处理器的各种硬件特性。

## bootloader第一个阶段为什么要足够的小？

Bootloader的第一个阶段需要足够小，主要是由于以下几个原因：

**1. 内存限制**

- **有限的RAM初始化**：在第一阶段启动时，SDRAM通常还未初始化，因此可用的RAM空间也非常有限。第一阶段代码必须足够小，才能在这些有限的内存空间中执行。

**2. 硬件初始化顺序**

- **逐步引导**：由于系统上电后的状态非常初始，第一阶段代码需要在有限的资源和环境中完成基本的硬件初始化，为第二阶段Bootloader的执行创造条件。

3. **快速启动**

- **减少启动时间**：第一阶段代码体积小，功能精简，可以快速完成初始化并将控制权移交给第二阶段Bootloader。这样可以有效减少系统的整体启动时间。

4. **固化在芯片上的限制**

- **片上存储器限制**：许多嵌入式系统将第一阶段Bootloader固化在芯片的ROM中。ROM的容量往往有限，因此必须确保第一阶段代码足够小以适应这些空间限制。

## 什么是根文件系统

根文件系统首先是一种文件系统，该文件系统不仅具有普通文件系统的存储数据文件的功能，但是相对 于普通的文件系统，它的特殊之处在于，它是内核启动时所挂载（mount）的第一个文件系统，**内核代码的映像文件**保存在根文件系统中，系统引导启动程序会在根文件系统挂载之后从中把一些初始化脚本 （如rcS,inittab）和服务加载到内存中去运行，里面包含了 Linux系统能够运行所必需的应用程序、库 等，比如可以给用户提供操作 Linux的控制界面的shell程序、动态链接的程序运行时需要的glibc库等。 

## 根文件系统为什么这么重要

根文件系统之所以在前面加一个”根“，说明它是加载其它文件系统的”根“，那么如果没有这个根，其它的文件系统也就没有办法进行加载的。根文件系统包含系统启动时所必须的目录和关键性的文件，以及使其他文件系统得以挂载（mount）所必要的文件。例如： **init进程的应用程序**必须运行在根文件系统上。 根文件系统提供了**根目录“/”**。 linux挂载分区时所依赖的信息存放于根文件系统/etc/fstab这个文件中。 **shell命令程序**必须运行在根文件系统上，譬如ls、cd等命令。

总之：一套linux体系，只有内核本身是不能工作的，必须要rootfs（上的etc目录下的配置文件、/bin /sbin等目录下的shell命令，还有/lib目录下的库文件等）相配合才能工作。



# 嵌入式

## I2C、SPI、UART、SDIO、USB的异同（串/并、速度、全/半双工、总线拓扑等）

### I2C集成电路间通信（Inter-Integrated Circuit）

- **串/并行**: 串行通信
- **速度**: 通常100kbps（标准模式）、400kbps（快速模式），还有1Mbps（快速模式+）和3.4Mbps（高速度模式）
- **全/半双工**: 半双工
- **总线拓扑**: 多主从结构，可以挂接多个设备
- **特点**: 使用两根线（SDA数据线和SCL时钟线），通过地址识别设备，简单可靠，适用于低速应用

### SPI串行外围设备接口（Serial Peripheral Interface）

- **串/并行**: 串行通信
- **速度**: 通常在几Mbps到几十Mbps范围内，具体取决于硬件
- **全/半双工**: 全双工
- **总线拓扑**: 主从结构，通常一个主设备控制一个或多个从设备
- **特点**: 使用四根线（MOSI、MISO、SCK、SS），通信速度快，无需地址识别，但需要更多引脚

### UART通用异步收发传输器（Universal Asynchronous Receiver/Transmitter）

- **串/并行**: 串行通信
- **速度**: 通常在几百bps到几Mbps范围内
- **全/半双工**: 全双工
- **总线拓扑**: 点对点通信
- **特点**: 使用两根线（TX发送线和RX接收线），无需时钟同步，简单可靠，适用于点对点的低速数据传输

### SDIO安全数字输入输出（Secure Digital Input Output）

- **串/并行**: 串行通信
- **速度**: 通常在几Mbps到几十Mbps范围内，具体取决于SD卡的速度等级
- **全/半双工**: 半双工
- **总线拓扑**: 点对点通信
- **特点**: 主要用于SD卡和主机之间的数据传输，支持标准SD协议

### USB通用串行总线（Universal Serial Bus）

- **串/并行**: 串行通信
- **速度**: 支持多种速度等级，如低速（1.5Mbps）、全速（12Mbps）、高速（480Mbps）、超高速（5Gbps）、超高速+（10Gbps）
- **全/半双工**: 低速和全速模式下是半双工，高速和更高模式下是全双工
- **总线拓扑**: 星形拓扑，可通过集线器扩展，支持热插拔
- **特点**: 广泛应用于计算机和外设之间的数据传输，支持供电，复杂但功能强大

### 总结表格

| 特性      | I2C                      | SPI                                | UART                           | SDIO             | USB                            |
| --------- | ------------------------ | ---------------------------------- | ------------------------------ | ---------------- | ------------------------------ |
| 串/并行   | 串行                     | 串行                               | 串行                           | 串行             | 串行                           |
| 速度      | 100kbps-3.4Mbps          | 几Mbps-几十Mbps                    | 几百bps-几Mbps                 | 几Mbps-几十Mbps  | 1.5Mbps-10Gbps                 |
| 全/半双工 | 半双工                   | 全双工                             | 全双工                         | 半双工           | 低速/全速:半双工, 高速+:全双工 |
| 总线拓扑  | 多主从                   | 主从                               | 点对点                         | 点对点           | 星形                           |
| 线数      | 2                        | 4                                  | 2                              | 6                | 4                              |
| 特点      | 简单可靠，适用于低速应用 | 通信快，无需地址识别，但需更多引脚 | 简单可靠，适用于点对点低速传输 | 用于SD卡数据传输 | 功能强大，支持供电和热插拔     |
