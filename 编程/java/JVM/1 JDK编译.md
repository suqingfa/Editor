https://blog.jetbrains.com/clion/2020/03/openjdk-with-clion/

# clone
```shell
git clone https://github.com/openjdk/jdk.git
```

# configure
```shell
bash configure --disable-warnings-as-errors --with-debug-level=slowdebug
```

# compile-commands
```shell
make compile-commands
```

# open in clion
CLion File -> Open -> build/linux-x86_64-server-slowdebug/compile_commands.json
Tools -> Compilation Database -> Change Project Root -> jdk

# configurations and build tools
