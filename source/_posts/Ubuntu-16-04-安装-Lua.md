title: Ubuntu 16.04 安装 Lua
tags:
  - Ubuntu
  - Lua
categories:
  - 语言
  - Lua
date: 2016-07-23 22:30:14
---


在 Linux 系统上使用以下命令编译安装 Lua：

    curl -R -O http://www.lua.org/ftp/lua-5.3.3.tar.gz
    tar zxf lua-5.3.3.tar.gz
    cd lua-5.3.3
    make linux test

<!-- more -->

#### 安装 make

编译过程如果提示以下信息则需要先安装 make：

    # make linux test
    The program 'make' can be found in the following packages:
     * make
     * make-guile
    Try: apt install <selected package>

安装 make

    # apt-get install make

#### 安装 gcc

编译过程提示以下信息则需要安装 gcc：

    # make linux test
    cd src && make linux
    make[1]: Entering directory '/root/lua/lua-5.3.3/src'
    make all SYSCFLAGS="-DLUA_USE_LINUX" SYSLIBS="-Wl,-E -ldl -lreadline"
    make[2]: Entering directory '/root/lua/lua-5.3.3/src'
    gcc -std=gnu99 -O2 -Wall -Wextra -DLUA_COMPAT_5_2 -DLUA_USE_LINUX    -c -o lapi.o lapi.c
    make[2]: gcc: Command not found
    <builtin>: recipe for target 'lapi.o' failed
    make[2]: *** [lapi.o] Error 127
    make[2]: Leaving directory '/root/lua/lua-5.3.3/src'
    Makefile:110: recipe for target 'linux' failed
    make[1]: *** [linux] Error 2
    make[1]: Leaving directory '/root/lua/lua-5.3.3/src'
    Makefile:55: recipe for target 'linux' failed
    make: *** [linux] Error 2

安装 gcc：

    # apt-get install gcc

安装 gcc 过程提示以下信息：

    E: Failed to fetch http://cn.archive.ubuntu.com/ubuntu/pool/main/b/binutils/binutils_2.26.1-1ubuntu1~16.04_amd64.deb  404  Not Found [IP: 115.28.122.210 80]
    E: Unable to fetch some archives, maybe run apt-get update or try with --fix-missing?

先更新安装源：

    # apt-get update

再次安装 gcc 成功。

#### 安装 libreadline-dev

编译过程提示以下信息则需要安装 libreadline-dev：

    lua.c:80:31: fatal error: readline/readline.h: No such file or directory
    compilation terminated.

安装 libreadline-dev：

    # apt-get install libreadline-dev

再次编译成功。

#### 检查安装

用以下方法检查安装，如果安装成功会有版本信息提示：

    # src/lua -v
    Lua 5.3.3  Copyright (C) 1994-2016 Lua.org, PUC-Rio

#### 编译安装

    # make linux install