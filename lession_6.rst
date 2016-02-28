李铭：ifstat
=============

netstat
--------
netstat 是用来打印网络连接数、路由表、接口统计、非活跃连接和多播成员的工具

options
---------

+------------------------+--------------------------------------------------------------------------------------------+
| 选项                   | 说明                                                                                       |
+------------------------+--------------------------------------------------------------------------------------------+
| --verbose , -v         | 开启啰嗦模式                                                                               |
+------------------------+--------------------------------------------------------------------------------------------+
| --wide , -W            | 输出结果不要截断IP地址                                                                     |
+------------------------+--------------------------------------------------------------------------------------------+
| --numeric , -n         | 以数字的方式显示主机、端口、用户                                                           |
+------------------------+--------------------------------------------------------------------------------------------+
| --numeric-hosts        | 以数字的方式显示主机                                                                       |
+------------------------+--------------------------------------------------------------------------------------------+
| --numeric-ports        | 以数字的方式显示端口                                                                       |
+------------------------+--------------------------------------------------------------------------------------------+
| --numeric-users        | 以用户ID的方式显示用户                                                                     |
+------------------------+--------------------------------------------------------------------------------------------+
| --protocol=family , -A | 指定显示的底层协议，多个协议用逗号(',')隔开，可用的值有inet,                               |
|                        | unix, ipx, ax25, netrom 和 ddp。                                                           |
|                        |                                                                                            |
|                        | 这和使用--inet, --unix (-x), --ipx, --ax25, --netrom 和 --ddp 选项有相同的效果。           |
|                        |                                                                                            |
|                        | inet协议族包括 raw，tcp，udp 套接字                                                        |
+------------------------+--------------------------------------------------------------------------------------------+
| -c, --continuous       | 每秒打印一次信息                                                                           |
+------------------------+--------------------------------------------------------------------------------------------+
| -e, --extend           | 显示额外的信息，-ee 将显示最多的信息                                                       |
+------------------------+--------------------------------------------------------------------------------------------+
| -o, --timers           | 显示网络定时器的信息                                                                       |
+------------------------+--------------------------------------------------------------------------------------------+
| -p, --program          | 显示 socket 所属进程 ID                                                                    |
+------------------------+--------------------------------------------------------------------------------------------+
| -l, --listening        | 只显示正在监听的socket                                                                     |
+------------------------+--------------------------------------------------------------------------------------------+
| -a, --all              | 显示监听状态和非监听状态的socket。和 --interfaces 选项一起使用，将会显示那些没有开启的接口 |
+------------------------+--------------------------------------------------------------------------------------------+
| -F                     | 显示从FIB得出的路由信息(默认效果)                                                          |
+------------------------+--------------------------------------------------------------------------------------------+
| -C                     | 显示从缓存得出的路由信息                                                                   |
+------------------------+--------------------------------------------------------------------------------------------+

套接字信息
-----------------

用法：netstat [address_family_options] [--tcp|-t] [--udp|-u] [--raw|-w] [--listening|-l] [--all|-a] [--numeric|-n] [--numeric-hosts] [--numeric-ports] [--numeric-users] [--symbolic|-N] [--extend|-e[--extend|-e]] [--timers|-o] [--program|-p] [--verbose|-v] [--continuous|-c]

举例：显示所有tcp套接字的信息

.. code-block:: 
    
    li@li: netstat -at
    激活Internet连接 (服务器和已建立连接的)
    Proto Recv-Q Send-Q Local Address           Foreign Address         State      
    tcp        0      0 *:5676                  *:*                     LISTEN     
    tcp        0      0 *:http                  *:*                     LISTEN     
    tcp        0      0 li:domain               *:*                     LISTEN     
    tcp        0      0 *:ssh                   *:*                     LISTEN     
    tcp        0      0 localhost:ipp           *:*                     LISTEN     
    tcp        0      0 localhost:49058         *:*                     LISTEN     
    tcp        0      0 li:5676                 localhost:60030         TIME_WAIT  
    tcp        1      0 localhost:40788         localhost:49058         CLOSE_WAIT 
    tcp        1      0 localhost:40640         localhost:49058         CLOSE_WAIT 
    tcp        0      0 li:5676                 localhost:60106         TIME_WAIT  
    tcp        0      0 li:5676                 localhost:60070         TIME_WAIT  
    tcp        0      0 localhost:53960         localhost:5676          TIME_WAIT  
    tcp        0      0 li:5676                 localhost:60014         TIME_WAIT  
    tcp        0      0 li:5676                 localhost:60068         TIME_WAIT  
    tcp        0      0 localhost:53990         localhost:5676          TIME_WAIT  
    tcp        0      0 localhost:5676          localhost:53992         TIME_WAIT  
    tcp        0      0 localhost:5676          localhost:54026         TIME_WAIT  
    tcp        0      0 li:5676                 localhost:60054         TIME_WAIT
    ...

路由表信息
-----------

用法：netstat {--route|-r} [address_family_options] [--extend|-e[--extend|-e]] [--verbose|-v] [--numeric|-n] [--numeric-hosts] [--numeric-ports] [--numeric-users] [--continuous|-c]

举例：

.. code-block:: 

    li@li: netstat -r
    内核 IP 路由表
    Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
    default         10.0.2.2        0.0.0.0         UG        0 0          0 enp0s3
    10.0.2.0        *               255.255.255.0   U         0 0          0 enp0s3
    link-local      *               255.255.0.0     U         0 0          0 enp0s3

网络接口信息
-------------

用法：netstat {--interfaces|-i} [--all|-a] [--extend|-e[--extend|-e]] [--verbose|-v] [--program|-p] [--numeric|-n] [--numeric-hosts] [--numeric-ports] [--numeric-users] [--continuous|-c]

举例：

.. code-block:: 

  li@li: netstat -i
  Kernel Interface table
  Iface   MTU Met   RX-OK RX-ERR RX-DRP RX-OVR    TX-OK TX-ERR TX-DRP TX-OVR Flg
  enp0s3     1500 0     13358      0      0 0          4257      0      0      0 BMRU
  lo        65536 0     91899      0      0 0         91899      0      0      0 LRU

多播成员信息
-------------

用法：netstat {--groups|-g} [--numeric|-n] [--numeric-hosts] [--numeric-ports] [--numeric-users] [--continuous|-c]

举例：
 
.. code-block:: 

    li@li：netstat -g
    IPv6/IPv4 Group Memberships
    Interface       RefCnt Group
    --------------- ------ ---------------------
    lo              1      all-systems.mcast.net
    enp0s3          1      224.0.0.251
    enp0s3          1      all-systems.mcast.net
    lo              1      ip6-allnodes
    lo              1      ff01::1
    enp0s3          1      ff02::fb
    enp0s3          1      ff02::1:ff4e:ebeb
    enp0s3          1      ip6-allnodes
    enp0s3          1      ff01::1

无效连接信息
-------------

用法：netstat {--masquerade|-M} [--extend|-e] [--numeric|-n] [--numeric-hosts] [--numeric-ports] [--numeric-users] [--continuous|-c]

统计信息
---------

用法：netstat {--statistics|-s} [--tcp|-t] [--udp|-u] [--raw|-w]

举例：

.. code-block:: 

    li@li: netstat -s
    Ip:
        113480 total packets received
        0 forwarded
        0 incoming packets discarded
        113478 incoming packets delivered
        113481 requests sent out
        40 outgoing packets dropped
    Icmp:
        82 ICMP messages received
        0 input ICMP message failed.
        ICMP接收历史
            destination unreachable: 80
            timeout in transit: 2
        105 ICMP messages sent
        0 ICMP messages failed
        ICMP发出历史
            destination unreachable: 105
    IcmpMsg:
            InType3: 80
            InType11: 2
            OutType3: 105
    Tcp:
        4374 active connections openings
        4296 passive connection openings
        5 failed connection attempts
        0 connection resets received
        5 connections established
        112909 segments received
        112786 segments send out
        79 segments retransmited
        0 bad segments received.
        38 resets sent
    Udp:
        409 packets received
        105 packets to unknown port received.
        0 packet receive errors
        562 packets sent
        IgnoredMulti: 10
    UdpLite:
    TcpExt:
        4155 TCP sockets finished time wait in fast timer
        308 delayed acks sent
        398 packets directly queued to recvmsg prequeue.
        960 bytes directly in process context from backlog
        175980 bytes directly received in process context from prequeue
        33984 packet headers predicted
        397 packets header predicted and directly queued to user
        15360 acknowledgments not containing data payload received
        35253 predicted acknowledgments
        4 congestion windows recovered without slow start after partial ack
        16 other TCP timeouts
        TCPRcvCoalesce: 20329
        TCPAutoCorking: 7
        TCPSynRetrans: 79
        TCPOrigDataSent: 51217
        TCPKeepAlive: 315
    IpExt:
        InNoRoutes: 2
        InMcastPkts: 32
        OutMcastPkts: 34
        InBcastPkts: 6
        OutBcastPkts: 6
        InOctets: 38022169
        OutOctets: 22741372
        InMcastOctets: 3975
        OutMcastOctets: 4055
        InBcastOctets: 284
        OutBcastOctets: 284
        InNoECTPkts: 122673 
