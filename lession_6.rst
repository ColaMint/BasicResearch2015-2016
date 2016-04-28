李铭：netstat + flask源码剖析 + 《High Performance Networking in Chrome》翻译
===============================================================================

netstat
--------
netstat 是用来打印网络连接数、路由表、接口统计、非活跃连接和多播成员的工具

options
~~~~~~~~

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
~~~~~~~~~~~

用法：netstat [address_family_options] [--tcp|-t] [--udp|-u] [--raw|-w] [--listening|-l] [--all|-a] [--numeric|-n] [--numeric-hosts] [--numeric-ports] [--numeric-users] [--symbolic|-N] [--extend|-e[--extend|-e]] [--timers|-o] [--program|-p] [--verbose|-v] [--continuous|-c]

举例：显示所有tcp套接字的信息

.. code-block:: text 
    
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
~~~~~~~~~~~~

用法：netstat {--route|-r} [address_family_options] [--extend|-e[--extend|-e]] [--verbose|-v] [--numeric|-n] [--numeric-hosts] [--numeric-ports] [--numeric-users] [--continuous|-c]

举例：

.. code-block:: text 

    li@li: netstat -r
    内核 IP 路由表
    Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
    default         10.0.2.2        0.0.0.0         UG        0 0          0 enp0s3
    10.0.2.0        *               255.255.255.0   U         0 0          0 enp0s3
    link-local      *               255.255.0.0     U         0 0          0 enp0s3

网络接口信息
~~~~~~~~~~~~~

用法：netstat {--interfaces|-i} [--all|-a] [--extend|-e[--extend|-e]] [--verbose|-v] [--program|-p] [--numeric|-n] [--numeric-hosts] [--numeric-ports] [--numeric-users] [--continuous|-c]

举例：

.. code-block:: text 

  li@li: netstat -i
  Kernel Interface table
  Iface   MTU Met   RX-OK RX-ERR RX-DRP RX-OVR    TX-OK TX-ERR TX-DRP TX-OVR Flg
  enp0s3     1500 0     13358      0      0 0          4257      0      0      0 BMRU
  lo        65536 0     91899      0      0 0         91899      0      0      0 LRU

多播成员信息
~~~~~~~~~~~~~~

用法：netstat {--groups|-g} [--numeric|-n] [--numeric-hosts] [--numeric-ports] [--numeric-users] [--continuous|-c]

举例：
 
.. code-block:: text 

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
~~~~~~~~~~~~~~

用法：netstat {--masquerade|-M} [--extend|-e] [--numeric|-n] [--numeric-hosts] [--numeric-ports] [--numeric-users] [--continuous|-c]

统计信息
~~~~~~~~~~

用法：netstat {--statistics|-s} [--tcp|-t] [--udp|-u] [--raw|-w]

举例：

.. code-block:: text 

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


flask源码剖析
---------------

项目组织结构
~~~~~~~~~~~~~

+------------------+---------------------------------+
| 文件/文件夹      | 描述                            |
+------------------+---------------------------------+
| artwork/         | logo的svg文件以及logo的版权申明 |
+------------------+---------------------------------+
| docs/            | 文档（采用Sphinx）              |
+------------------+---------------------------------+
| examples/        | 几个示例项目的代码              |
+------------------+---------------------------------+
| flask/           | flask项目源码                   |
+------------------+---------------------------------+
| scripts/         | 一些辅助性的脚本                |
+------------------+---------------------------------+
| tests/           | flask项目测试代码               |
+------------------+---------------------------------+
| AUTHORS          | 项目作者列表                    |
+------------------+---------------------------------+
| CHANGES          | changelog                       |
+------------------+---------------------------------+
| CONTRIBUTING.rst | 介绍如何为flask项目作贡献       |
+------------------+---------------------------------+
| LICENSE          | 版本申明                        |
+------------------+---------------------------------+
| Makefile         | 用make封装常用命令              |
+------------------+---------------------------------+
| MANIFEST.in      | 发布清单(Distutils相关)         |
+------------------+---------------------------------+
| README           | README文件                      |
+------------------+---------------------------------+
| setup.cfg        | 配置文件(Distutils相关)         |
+------------------+---------------------------------+
| setup.py         | 项目发布、安装脚本              |
+------------------+---------------------------------+
| tox.ini          | tox标准化测试的配置文件         |
+------------------+---------------------------------+


一次请求的处理过程
~~~~~~~~~~~~~~~~~~~~


当我们发起一起请求时，处理过程将从flask.app.Flask.wsgi_app(self, environ, start_response)开始

首先用environ构造出一个flask.ctx.RequestContext(请求上下文)实例，并将其压入flask.globals._request_ctx_stack(请求上下文栈)中

但是在压入之前，如果flask.globals._app_ctx_stack（应用上下文栈）的栈顶为空或栈顶的应用上下文与当前请求上下文所属的应用不对应，那么会先压入当前应用请求上下文到栈中

在请求处理之前将请求上下文和应用上下文压入栈中是为了能够在请求处理过程获得当前应用(flask.globals.current_app)、当前请求(flask.globals.request)和全局对象g(flask.globals.g)

随后，代码进入flask.app.Flask.full_dispatch_request(self)，开始处理请求

如果这个请求是第一个请求，那么带有flask.app.Flask.before_first_request装饰器的函数被依次调用

然后，带有flask.app.before_request函数会被依次调用，如果其中某次函数调用返回了一个response，那么直接进入最后的reponse处理过程，否则将会调用与请求的URL匹配的处理函数，来得到一个response

紧接着，带有flask.app.Flask.after_request装饰器的函数被调用

此时得到的response并不是真正的flask.wrappers.Response实例，需要经过flask.app.Flask.make_response(self, rv)进行转化，并将结果输出给用户

最后，当前请求上下文从栈中弹出，如果该请求上下文入栈前还往应用上下文栈压入一个应用上下文，那么这个应用上下文也会弹出

在请求上下文弹出前，带有flask.app.Flask.after_request装饰器的函数被调用

在应用上下文弹出前，带有flask.app.Flask.teardown_appcontext装饰器的函数被调用

上下文栈
~~~~~~~~~

为了达到这种效果：对同一个对象操作能自动代理到当前线程/携程所对应的实际对象。例如同样对flask.request对象进行操作，在每个web请求中都会把操作代理到当前请求所对应的flask.wrappers.Request的实例，flask借助了werkzeug.local.LocalProxy, werkzeug.local.LocalStack 和 werkzeug.local.Local

Local 自身维护一个字典，Key是线程/携程ID，Value也是一个字典（真正存放数据的地方）。当我们要从Local中获取或设置一个Key的值时，它先根据线程/携程ID找到所属的数据字典，再对该字典进行操作。从而实现在不同线程/携程对Local进行数据操作实际操作的是不同的字典。

LocalStack 是被falsk用来存放上下文对象的栈，其内部维护一个Local对象，所以对于每个线程/携程来说，对LocalStack的操作实际上操作的是不同的栈实体。

LocalProxy的构造函数中需要传入一个函数，之后每当对LocalProxy对象进行操作时，它会先调用这个函数来获得所代理的对象，然后再把操作施加在被代理对象身上。flask 利用 falsk.globals._lookup_req_object函数来帮助flask.globals.request找到被代理的对象（即flask.globals._request_ctx_stack的栈顶元素）。

其他栈的道理相同。

既然利用Local+LocalProxy就能实现把对同一个代理对象的操作代理施加在被代理对象身上，为什么还要利用LocalStack呢？毕竟在实际web环境中，一个线程/携程本身只需要处理一个请求，那么栈的高度最大只能是1，看似多此一举吗？

实际上flask这么设计是为了方便对多个应用同时进行测试：

.. code-block:: py

   from flask import Flask, request
   app1 = Flask("app1")
   app2 = Flask("app2")

   @app1.route("/index1")
   def index1():
       return "app1"

   @app2.route("/index2")
   def index2():
       return "app2"

   with app1.test_request_context(path='/index1'):      # 压入请求上下文1
       print request.url                                # 访问请求上下文1
       with app2.test_request_context(path='/index2'):  # 压入请求上下文2 
           print request.url                            # 访问请求上下文2
       print request.url                                # 弹出请求上下文2，访问请求上下文1
                                                        # 弹出请求上下文1

运行结果：

.. code-block:: text

    http://localhost/index1
    http://localhost/index2
    http://localhost/index1

从结果可以看出，正是在一个线程中往同一个请求上下文栈压入和弹出不同的两个请求上下文，request才能代理到正确的请求对象

《High Performance Networking in Chrome》翻译
----------------------------------------------

`High Performance Networking in Chrome.pdf <_static/High_Performance_Networking_in_Chrome.pdf>`_

参考资料
----------

.. [1] man page - netstat
.. [2] flask source - https://github.com/mitsuhiko/flask
.. [3] 《High Performance Networking in Chrome》- http://aosabook.org/en/posa/high-performance-networking-in-chrome.html
