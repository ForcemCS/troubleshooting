## IO

四个核心指标

+ **使用率**，是指磁盘忙处理 I/O 请求的百分比。过高的使用率（比如超过 60%）通常意味着磁盘 I/O 存在性能瓶颈。

+ **IOPS**（Input/Output Per Second），是指每秒的 I/O 请求数。

+ **吞吐量/tps**，是指每秒的 I/O 请求大小。

+ **响应时间**，是指从发出 I/O 请求到收到响应的间隔时间。



`iostat -d -x 1` 的作用是每隔 1 秒输出一次关于磁盘设备的详细 I/O 性能统计信息

```
bob@node01:~$ sudo iostat  -d -x    
Linux 5.15.0-105-generic (node01)       09/25/2024      _x86_64_        (1 CPU)

Device            r/s     rkB/s   rrqm/s  %rrqm r_await rareq-sz     w/s     wkB/s   wrqm/s  %wrqm w_await wareq-sz     d/s     dkB/s   drqm/s  %drqm d_await dareq-sz     f/s f_await  aqu-sz  %util

vda              2.11    106.75     0.54  20.33    0.28    50.53    1.26    242.13     2.89  69.68    5.12   192.81    0.00      0.00     0.00   0.00    0.00     0.00    0.35    1.53    0.01   0.31

#数据读取量很大

```

我们重点关注几个输出字段的含义

1. **r/s**：每秒读取的请求数（Reads per second），表示每秒钟从该设备完成的读取操作数量。
2. **w/s**：每秒写入的请求数（Writes per second），表示每秒钟向该设备完成的写入操作数量。
3. **rkB/s**：每秒读取的字节数（Reads kilobytes per second），表示每秒钟从该设备读取的数据量（以千字节为单位）。
4. **wkB/s**：每秒写入的字节数（Writes kilobytes per second），表示每秒钟向该设备写入的数据量（以千字节为单位）



发现哪个磁盘设备有问题之后，现在我们可以使用**pidstat -d **来确定是哪个进程导致了大量的I/O操作（例如数据传输量很小，但传输次数很多）

1. **kB_rd/s (0.00)**：每秒读取的千字节数。
2. **kB_wr/s (11.88)**：每秒写入的千字节数

还可以使用**dstat --top-io**来找出高TPS的进程xx，然后使用

1. **pgrep xx**找出进程的ID
2. **lsof -p xx** 找出进程打开的文件
3. **df $file** 找出相应的磁盘设备

