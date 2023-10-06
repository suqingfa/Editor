# 下载源码编译

https://blog.jetbrains.com/clion/2020/03/openjdk-with-clion/

## clone & configure & compile

```shell
git clone https://github.com/openjdk/jdk.git
bash configure --disable-warnings-as-errors --with-debug-level=slowdebug
make compile-commands
make
make clean
make dist-clean
```

## open in clion

文件 -> 打开 -> build/linux-x86_64-server-slowdebug/compile_commands.json -> 作为项目打开
工具 -> 编译数据库 -> 更改项目根 -> jdk

## clion configurations and build tools

设置 -> 自定义构建目标 -> 添加(+)
运行/调试配置 -> 添加新配置 -> 自定义构建应用程序

# 项目结构

- src/hotspot
    - src/hotspot/cpu
    - src/hotspot/os
    - src/hotspot/os_cpu
    - src/hotspot/share
        - src/hotspot/share/adlc Arch Desc Language Compiler
        - src/hotspot/share/asm
        - src/hotspot/share/c1
        - src/hotspot/share/cds Class Data Share
        - src/hotspot/share/ci Compiler Interface
        - src/hotspot/share/classfile Class File
        - src/hotspot/share/code Compiled Code
        - src/hotspot/share/compiler Compiler
        - src/hotspot/share/gc Garbage Collection
        - src/hotspot/share/include Head File
        - src/hotspot/share/interpreter Interpreter
        - src/hotspot/share/jfr Java Flight Record
        - src/hotspot/share/jvmci JVM Compiler Interface
        - src/hotspot/share/libadt
        - src/hotspot/share/logging Logging
        - src/hotspot/share/memory Memory
        - src/hotspot/share/metaprogramming Meta Programming
        - src/hotspot/share/oops
        - src/hotspot/share/opto
        - src/hotspot/share/precompiled
        - src/hotspot/share/prims
        - src/hotspot/share/runtime Runtime
        - src/hotspot/share/services Services
        - src/hotspot/share/utilities Utilities
- src/java.base
    - src/java.base/aix
    - src/java.base/linux
    - src/java.base/macos
    - src/java.base/share
        - src/java.base/share/classes
        - src/java.base/share/conf
        - src/java.base/share/data
        - src/java.base/share/legal
        - src/java.base/share/lib
        - src/java.base/share/man
        - src/java.base/share/native
            - src/java.base/share/native/include
            - src/java.base/share/native/launcher
            - src/java.base/share/native/libfdlibm
            - src/java.base/share/native/libjava
            - src/java.base/share/native/libimage
            - src/java.base/share/native/libjli
            - src/java.base/share/native/libnet
            - src/java.base/share/native/libnio
            - src/java.base/share/native/libsyslookup
            - src/java.base/share/native/libverify
            - src/java.base/share/native/libzip
    - src/java.base/unix
    - src/java.base/windows