# CS2DJIT

Fork of [cs2djit](https://github.com/MikuAuahDark/cs2djit) by [Miku AuahDark](https://github.com/MikuAuahDark).

## Prerequisites

Install required packages for building 32-bit LuaJIT:

```bash
sudo apt-get update
sudo apt-get install -y git build-essential gcc-multilib g++-multilib
```

## Install

```bash
git clone --recurse-submodules https://github.com/ernestpasnik/cs2djit.git
cd cs2djit
```

## Build

```bash
# Build 32-bit LuaJIT
cd luajit
make clean
make BUILDMODE=static CC="gcc -m32"
cd ..

# Build CS2DJIT
gcc -m32 -shared -fPIC \
  -I./luajit/src \
  src/cs2djitbase.c src/cs2djitmem.c src/cs2djitlinux.c \
  ./luajit/src/libluajit.a \
  -o libcs2djit.so
```
