# CS2DJIT

Fork of [cs2djit](https://github.com/MikuAuahDark/cs2djit) by [Miku AuahDark](https://github.com/MikuAuahDark).

## Quick Start

### Download Pre-built Binary

Download the latest `libcs2djit.so` from [Releases](../../releases/latest).

### Build from Source

#### Prerequisites

```bash
sudo apt-get update
sudo apt-get install -y git build-essential gcc-multilib g++-multilib libc6-dev-i386
```

#### Clone and Build

```bash
git clone --recurse-submodules https://github.com/ernestpasnik/cs2djit.git
cd cs2djit

cd luajit
make clean
make BUILDMODE=static CC="gcc -m32" -j$(nproc)
cd ..

gcc -m32 -shared -fPIC \
  -O2 -Wall -Wextra \
  -I./luajit/src \
  src/cs2djitbase.c src/cs2djitmem.c src/cs2djitlinux.c \
  ./luajit/src/libluajit.a \
  -o libcs2djit.so \
  -ldl -lpthread
```

## Usage

```bash
LD_PRELOAD=./libcs2djit.so cs2d
```

## CI/CD

Every push to `main`/`master` automatically builds and uploads `libcs2djit.so` as an artifact. To create a release, include `[release]` in your commit message:

```bash
git commit -m "Update build [release]"
git push
```