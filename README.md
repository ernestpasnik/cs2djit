# CS2DJIT

32-bit LuaJIT injection for CS2D Dedicated Server on 64-bit Linux systems.

Fork of [cs2djit](https://github.com/MikuAuahDark/cs2djit) by [Miku AuahDark](https://github.com/MikuAuahDark).

## About This Fork

This fork focuses on providing **automated builds** and simplified deployment for CS2D server administrators. The original low-level code was maintained and adapted with AI assistance, as I don't have extensive low-level programming experience. If you encounter issues with the core injection mechanism, please refer to the [original repository](https://github.com/MikuAuahDark/cs2djit).

## What is this?

CS2DJIT enables the CS2D Dedicated Server to use LuaJIT instead of standard Lua, providing:
- **Faster Lua execution** (up to 10x performance improvement)
- **JIT compilation** for hot code paths
- **Better memory efficiency**

This is particularly useful for servers running complex Lua scripts or handling many players.

## Quick Start

### 1. Download Pre-built Binary
```bash
wget https://github.com/ernestpasnik/cs2djit/releases/latest/download/libcs2djit.so
```

### 2. Run CS2D with LuaJIT
```bash
LD_PRELOAD=./libcs2djit.so ./cs2d_dedicated
```

You should see LuaJIT version information in the server console on startup.

### 3. Systemd Service (Optional)

For production servers, use systemd:
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
RestartSec=10

[Install]
WantedBy=multi-user.target
```

Enable and start:
```bash
sudo systemctl daemon-reload
sudo systemctl enable cs2d
sudo systemctl start cs2d
```

## Build from Source

### Prerequisites

Install 32-bit development tools on your 64-bit Linux system:
```bash
sudo apt update
sudo apt install -y gcc-multilib libc6-dev-i386 git make
```

### Build Steps
```bash
# Clone with LuaJIT submodule
git clone --recurse-submodules https://github.com/ernestpasnik/cs2djit.git
cd cs2djit

# Build LuaJIT as static 32-bit library
cd luajit
make BUILDMODE=static CC="gcc" CFLAGS="-m32 -fPIC -O2" -j$(nproc)
cd ..

# Build CS2DJIT shared library
gcc -m32 -shared -fPIC -O2 -s \
  -I./luajit/src \
  src/cs2djitbase.c src/cs2djitmem.c src/cs2djitlinux.c \
  ./luajit/src/libluajit.a \
  -o libcs2djit.so \
  -ldl -lpthread
```

The resulting `libcs2djit.so` can be used with `LD_PRELOAD`.

## Automated Releases

This repository uses GitHub Actions for automated builds. To create a new release:
```bash
git commit -m "Update version [release]"
git push
```

Any commit containing `[release]` will trigger:
1. Automated build of `libcs2djit.so`
2. GitHub release creation with the binary attached

## Troubleshooting

**Server doesn't start or crashes:**
- Ensure you're using 32-bit `cs2d_dedicated` binary
- Check that `libcs2djit.so` is in the correct path
- Verify 32-bit libraries are installed: `ldd libcs2djit.so`

**No performance improvement:**
- LuaJIT only speeds up CPU-intensive Lua code
- Check server console for LuaJIT initialization messages
- Use `jit.status()` in your Lua scripts to verify JIT is active

**Build errors:**
- Make sure you cloned with `--recurse-submodules`
- If submodules are missing: `git submodule update --init --recursive`

## Credits

- **Original CS2DJIT:** [Miku AuahDark](https://github.com/MikuAuahDark/cs2djit)
- **LuaJIT:** [Mike Pall](https://luajit.org/)
- **This fork:** Automated builds and CI/CD improvements (built with Claude AI assistance)

## License

See [LICENSE.md](LICENSE.md)

## Contributing

Bug reports and suggestions are welcome! Please note that fixes to low-level injection code should ideally be contributed to the [upstream repository](https://github.com/MikuAuahDark/cs2djit) first.