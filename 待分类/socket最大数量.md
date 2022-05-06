# socket最大数量
## 问题：
1. socket.accept() 返回的 socket 和 listen 的 socket，二者有啥区别？
> socket 是由五元组来唯一标示(协议/server ip/server port/client ip/client port)；
> 二者区别，主要就是 client ip 和 client port 不同；

2. accept() 产生的 socket 会占用新的端口吗？
> 不会；accept() 得到的 socket 只是 tcp/ip 的实现，对外提供编程接口，数据传递仍然依赖的是 listen 的那个 socket；
> unix 系统上，程序执行 I/O 的实质都是读写文件描述符；
> 所以 listen 的 socket 才会占用文件描述符，所以它的最大个数和系统最大文件描述符个数有关；
> 而 accept() 的 socket 只是对协议封装实现，不占用端口，更不占用文件描述符；

3. 误解：单个服务器程序可承受最大连接数“理论”上是“65535”
> 65535 其实是端口的最大个数(因为端口号在协议中占用16位)；而每个 listen 的 socket 都需要监听端口，所以 listen 的 socket 最大个数是 65535，但 accept 的 socket 和这个 65535 并无关系，而是和句柄个数以及内存大小等有关  
> ps：
> - 查看系统文件描述符占用量：`cat /proc/sys/fs/file-nr`
> - 查看系统最大文件句柄数: `cat /proc/sys/fs/file-max`

4. 可承载最大连接数(accept 的 socket 数目)和哪些值有关呢？
- window 系统下：
  - 最大TCP连接数： TcpNumConnections = 0x00fffffe (Default = 16,777,214)  
  >以上注册表信息配置单机的最大允许的TCP连接数，默认为 16M。这个数值看似很大，这个并不是限制最大连接数的唯一条件，还有其他条件会限制到TCP 连接的最大连接数。

  - 最大动态端口数：MaxUserPort = 5000 (Default = 5000, Max = 65534)
  > TCP客户端和服务器连接时，客户端必须分配一个动态端口，默认情况下这个动态端口的分配范围为 1024-5000 ，也就是说默认情况下，客户端最多可以同时发起3977 个Socket 连接。我们可以修改如下注册表来调整这个动态端口的范围

  - 最大TCB 数量：MaxFreeTcbs = 2000 (Default = RAM dependent, but usual Pro = 1000, Srv=2000)
  > 每次 TCP 连接都会分配一个 TCP 控制块(TCP control block or TCB)，这个控制块用于缓存TCP连接的一些参数，每个TCB需要分配 0.5 KB的pagepool 和 0.5KB 的Non-pagepool，也就说，每个TCP连接会占用 1KB 的系统内存。
  > 非Server版本，MaxFreeTcbs 的默认值为1000 （64M 以上物理内存）
  > Server 版本，这个的默认值为 2000。

  - 最大TCB Hash table 数量：MaxHashTableSize = 512 (Default = 512, Range = 64-65536)
  > TCB 是通过Hash table 来管理的，下面注册表设置决定了这个Hash table 的大小
  >这个值指明分配 pagepool 内存的数量，也就是说，如果MaxFreeTcbs = 1000 , 则 pagepool 的内存数量为 500KB 
  > 那么 MaxHashTableSize 应大于 500 才行。这个数量越大，则Hash table 的冗余度就越高，每次分配和查找 TCP  连接用时就越少。这个值必须是2的幂，且最大为65536.

5. 典型配置有哪些？
   - MaxUserPort = 65534 (Decimal)
   - MaxHashTableSize = 65536 (Decimal)
   - MaxFreeTcbs = 16000 (Decimal) 
   > 这里我们可以看到 MaxHashTableSize 被配置为比MaxFreeTcbs 大4倍，这样可以大大增加TCP建立的速度。

6. 单个连接消耗内存的地方？
   - socket buffer(read 和 write 各有一个)
   > 默认值是87K/16K, min是4K/4K, max是8M/16M, 实际使用默认值最低也要保留8K,8K  
   > 查看方式：第一个表示最小值，第二个表示默认值，第三个表示最大值  
   > `cat /proc/sys/net/ipv4/tcp_wmem`  
   > `cat /proc/sys/net/ipv4/tcp_rmem`

### 排查问题常用命令：
- `dmesg or dmesg -T`
    > 查看内核的错误信息，比如tcp链接太多，句柄不够用，内存不足导致某些进程被kill掉
- `iostat -d 1 10 -x`
    > 动态查看 io 使用情况
- `vmstat -n 1 10`
    > 动态查看机器使用情况(mem/swap/io/sys/cpu)