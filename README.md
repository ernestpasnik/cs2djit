# CS2DJIT

32-bit LuaJIT injection for CS2D Dedicated Server on 64-bit Linux systems.

Fork of [cs2djit](https://github.com/MikuAuahDark/cs2djit) by [Miku AuahDark](https://github.com/MikuAuahDark).

## What is this?

This repository provides automated builds of 32-bit LuaJIT and CS2DJIT library that can be injected into 32-bit `cs2d_dedicated` server running on 64-bit Linux via `LD_PRELOAD`.

## Quick Start

Download the latest release:

```bash
wget https://github.com/ernestpasnik/cs2djit/releases/latest/download/libcs2djit.so
```

Run CS2D Dedicated Server with LuaJIT:

```bash
LD_PRELOAD=./libcs2djit.so ./cs2d_dedicated
```

### Systemd Service

Add `LD_PRELOAD` to your systemd service file:

```ini
[Unit]
Description=CS2D Dedicated Server
After=network.target

[Service]
Type=simple
User=cs2d
WorkingDirectory=/path/to/cs2d
Environment="LD_PRELOAD=/path/to/libcs2djit.so"
ExecStart=/path/to/cs2d_dedicated
Restart=always

[Install]
WantedBy=multi-user.target
```

Reload and restart:

```bash
sudo systemctl daemon-reload
sudo systemctl restart cs2d
```

## Build from Source

### Prerequisites

```bash
sudo apt update
sudo apt install -y gcc-multilib libc6-dev-i386
```

### Build

```bash
git clone --recurse-submodules https://github.com/ernestpasnik/cs2djit.git
cd cs2djit

cd luajit
make clean
make BUILDMODE=static CC="gcc -m32" -j$(nproc)
cd ..

gcc -m32 -shared -fPIC -O2 -s -Wall -Wextra \
  -I./luajit/src \
  src/cs2djitbase.c src/cs2djitmem.c src/cs2djitlinux.c \
  ./luajit/src/libluajit.a \
  -o libcs2djit.so \
  -ldl -lpthread
```

## CI/CD

Every push builds `libcs2djit.so` automatically. To create a release, include `[release]` in commit message:

```bash
git commit -m "Update build [release]"
git push
```

## License

See [LICENSE.md](LICENSE.md)