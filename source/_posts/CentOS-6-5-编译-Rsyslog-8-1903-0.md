title: CentOS 6.5 编译 Rsyslog 8.1903.0
date: 2019-04-03 16:43:16
tags:
---


源码下载参考我的另外一片博文：[CentOS 7.3 编译 Rsyslog 8.1903.0](http://zhang-jc.github.io/2019/04/03/CentOS-7-3-%E7%BC%96%E8%AF%91-Rsyslog-8-1903-0/)。

本篇博文从创建构建环境开始填坑/(ㄒoㄒ)/~~。通过上一篇博文可以解决的问题此处不再赘述。

执行配置时出现以下错误：

    # ./configure --enable-omhttp
    configure: error: cannot find install-sh or install.sh in "." "./.." "./../.."

<!--more-->

先执行 autogen.sh 出现以下错误：

    # sh autogen.sh
    checking for LIBESTR... configure: error: Package requirements (libestr >= 0.1.9) were not met:
    No package 'libestr' found

检查没有安装 libestr 库，所以重新安装：

    # yum install libestr libestr-devel

再次执行 autogen.sh 问题依旧o(╯□╰)o。需要编译安装 libestr。

    # wget https://libestr.adiscon.com/files/download/libestr-0.1.9.tar.gz
    # tar xzvf libestr-0.1.9.tar.gz
    # ./configure CC="gcc -m64" --prefix=/usr --libdir=/usr/lib64
    # make
    # make install

安装完成后重新执行 autogen.sh 问题解决。

问题逐一解决后执行自动配置成功：

    # autoreconf -fvi

执行配置：

    # ./configure --enable-omhttp

配置过程中出现以下错误信息：

    make[3]: Entering directory `/data/kafka/rsyslog-8.1903.0/grammar'
      LEX    lexer.c
    make[3]: *** [lexer.c] 错误 1
    make[3]: Leaving directory `/data/kafka/rsyslog-8.1903.0/grammar'
    make[2]: *** [all] 错误 2
    make[2]: Leaving directory `/data/kafka/rsyslog-8.1903.0/grammar'
    make[1]: *** [all-recursive] 错误 1
    make[1]: Leaving directory `/data/kafka/rsyslog-8.1903.0'
    make: *** [all] 错误 2

安装 flex：

    yum install flex

安装后执行 autogen.sh，再次执行 configure，然后执行 make 成功。进入 omhttp 目录执行 make 出现以下错误：

    # cd contrib/omhttp
    # make
      CC     omhttp_la-omhttp.lo
    omhttp.c: 在函数‘curlPostSetup’中:
    omhttp.c:1554: 错误：‘CURLOPT_TCP_KEEPALIVE’未声明(在此函数内第一次使用)
    omhttp.c:1554: 错误：(即使在一个函数内多次出现，每个未声明的标识符在其
    omhttp.c:1554: 错误：所在的函数内也只报告一次。)
    omhttp.c:1554: 警告：在‘_curl_opt’的声明中，类型默认为‘int’
    omhttp.c:1558: 错误：‘CURLOPT_TCP_KEEPIDLE’未声明(在此函数内第一次使用)
    omhttp.c:1558: 警告：在‘_curl_opt’的声明中，类型默认为‘int’
    omhttp.c:1562: 错误：‘CURLOPT_TCP_KEEPINTVL’未声明(在此函数内第一次使用)
    omhttp.c:1562: 警告：在‘_curl_opt’的声明中，类型默认为‘int’
    make: *** [omhttp_la-omhttp.lo] 错误 1

经过 Google 后发现 CURLOPT_TCP_KEEPALIVE 是在 libcurl 7.25.0 之后才定义的。使用以下命令检查系统当前 libcurl 版本：

    # rpm -q libcurl
    
  libcurl-7.19.7-52.el6.x86_64

所以需要升级 curl 版本，参考：https://www.jianshu.com/p/14f5f145453e。升级完成后创建以下软连接：

    # ln -s /usr/local/curl/include/curl/ /usr/include/curl

再次执行编译 omhttp，出现以下错误：

    make[2]: Entering directory `/data/kafka/rsyslog-8.1903.0/contrib/omhttp'
      CC     omhttp_la-omhttp.lo
    omhttp.c: 在函数‘checkConn’中:
    omhttp.c:470: 警告：此函数中的‘healthUrl’在使用前可能未初始化
      CCLD   omhttp.la
    /usr/bin/ld: cannot find -lcurl
    collect2: ld returned 1 exit status
    make[2]: *** [omhttp.la] 错误 1
    make[2]: Leaving directory `/data/kafka/rsyslog-8.1903.0/contrib/omhttp'
    make[1]: *** [all-recursive] 错误 1
    make[1]: Leaving directory `/data/kafka/rsyslog-8.1903.0'
    make: *** [all] 错误 2

创建以下软连接：

    cd /lib64
    # ln -s /usr/local/curl/lib/libcurl.so.4.5.0 libcurl.so
    # ln -s /usr/local/curl/lib/libcurl.so.4.5.0 libcurl.so.4

再次编译 omhttp 成功！！终于大功告成O(∩_∩)O哈哈~