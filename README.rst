
PgBouncer
=========

PostgreSQL的轻量级连接池。

主页
    https://pgbouncer.github.io

源代码，bug追踪
    https://github.com/pgbouncer/pgbouncer

构建
---------

PgBouncer取决于几件事情获得编译：

* `GNU Make`_ 3.81+
* libevent_ 2.0
* (可选的) 用于TLS支持的 OpenSSL_ 1.0.1。
* (可选的) `c-ares`_ 作为libevent的evdns的替换。

.. _GNU Make: https://www.gnu.org/software/make/
.. _libevent: http://libevent.org/
.. _OpenSSL: https://www.openssl.org/
.. _`c-ares`: http://c-ares.haxx.se/

安装完依赖以后，只需要运行 ::

    $ ./configure --prefix=/usr/local --with-libevent=libevent-prefix
    $ make
    $ make install

如果你是从git构建的，或者是为了Windows构建的，请查看下面分别的构建说明。

DNS查找支持
------------------

从PgBouncer 1.4开始，它在连接时而不是配置加载时执行主机名查找。
这需要适当的异步DNS实现。以下列表显示支持的后端和他们的查看顺序：

+----------------------------+----------+-----------+------------+----------------+---------------------------------------+
| 后端                       | 并行     | EDNS0 (1) | /etc/hosts | SOA 查找 (2)   | 注意                                  |
+============================+==========+===========+============+================+=======================================+
| c-ares                     | yes      | yes       | yes        | yes            | ipv6+CNAME buggy in <=1.10            |
+----------------------------+----------+-----------+------------+----------------+---------------------------------------+
| udns                       | yes      | yes       | no         | yes            | 仅ipv4                                |
+----------------------------+----------+-----------+------------+----------------+---------------------------------------+
| evdns, libevent 2.x        | yes      | no        | yes        | no             | 不检查 /etc/hosts 更新                |
+----------------------------+----------+-----------+------------+----------------+---------------------------------------+
| getaddrinfo_a, glibc 2.9+  | yes      | yes (3)   | yes        | no             | N/A 在非Linux上                       |
+----------------------------+----------+-----------+------------+----------------+---------------------------------------+
| getaddrinfo, libc          | no       | yes (3)   | yes        | no             | N/A 在win32上，需要pthreads           |
+----------------------------+----------+-----------+------------+----------------+---------------------------------------+
| evdns, libevent 1.x        | yes      | no        | no         | no             | buggy                                 |
+----------------------------+----------+-----------+------------+----------------+---------------------------------------+

1. EDNS0需要在一个主机名后面有超过8个地址。
2. 需要进行SOA查找以重新检查区域串行更改的主机名
3. 要启用EDNS0，添加 `options edns0` 到/etc/resolv.conf

`./configure` 还有标志 `--enable-evdns` 和 `--disable-evdns`，
它们关闭自动探测并强制使用 `evdns` 或 `getaddrinfo_a()`。

从GIT构建
-----------------

从GIT构建PgBouncer要求您获取libusual子模块并生成头文件和配置文件，然后才能运行configure ::

	$ git clone https://github.com/pgbouncer/pgbouncer.git
	$ cd pgbouncer
	$ git submodule init
	$ git submodule update
	$ ./autogen.sh
	$ ./configure ...
	$ make
	$ make install

需要额外的包：autoconf、automake、libtool、
autoconf-archive、python-docutils和pkg-config。

为WIN32构建
------------------

目前只有env测试的版本是MINGW32 / MSYS。
Cygwin和Visual $ AnyYTHING未经测试。 DNS主机名查询需要Libevent 2.x。

然后执行::

	$ ./configure ...
	$ make

如果从Unix进行交叉编译::

	$ ./configure --host=i586-mingw32msvc ...

在WIN32上运行
----------------

从命令行运行是正常的，除了-d（守护进程），-R（重新启动）
和-u（切换用户）开关将不工作。

要作为Windows服务运行pgbouncer，你需要配置 `service_name`
参数为服务设置名字。然后：

	$ pgbouncer -regservice config.ini

要卸载服务：

	$ pgbouncer -unregservice config.ini

要使用Windows事件日志，在配置文件中设置"syslog = 1"。
但是之前你需要注册pgbevent.dll::

	$ regsvr32 pgbevent.dll

要卸载它，执行::
    
        $ regsvr32 /u pgbevent.dll

