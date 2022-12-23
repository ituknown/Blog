MacOS 自带的 gcc/g++ 版本与 Xcode 版本有关，通常默认的 gcc/g++ 版本都比较低。难道为了一个 gcc/g++ 要升级一下 Xcode？感觉有点划不来啊~

所以，对于 MacOS 用户来说，建议你使用 HomeBrew（有关 HomeBrew 的安装和使用不做说明，可以自行了解）！

有了 HomeBrew 我们就可以很方便的查看当前 gcc 的最新稳定版本：

```bash
$ brew info gcc

==> gcc: stable 12.2.0 (bottled), HEAD
GNU compiler collection
https://gcc.gnu.org/
Not installed
From: https://github.com/Homebrew/homebrew-core/blob/HEAD/Formula/gcc.rb
License: GPL-3.0-or-later with GCC-exception-3.1
==> Dependencies
Required: gmp ✔, isl ✔, libmpc ✔, mpfr ✔, zstd ✔
==> Options
--HEAD
	Install HEAD version
==> Analytics
install: 77,732 (30 days), 300,563 (90 days), 1,443,989 (365 days)
install-on-request: 38,513 (30 days), 148,834 (90 days), 682,628 (365 days)
build-error: 257 (30 days)
```

譬如，当前最新的稳定版本是 `12.2.0`。如果你想安装最新的稳定版本可以直接使用下面的安装命令：

```bash
$ brew install gcc
```

但是呢，如果你想要安装指定的 gcc 版本就不能使用这个命令了，可以先使用 `search` 查看下所有稳定版本信息：

```bash
$ brew search gcc

==> Formulae
aarch64-elf-gcc         gcc@11                  gcc@6                   gcc@9                   x86_64-elf-gcc          scc
gcc                     gcc@4.9                 gcc@7                   i686-elf-gcc            ghc                     tcc
gcc@10                  gcc@5                   gcc@8                   libgccjit               grc                     ncc

==> Casks
gcc-aarch64-embedded                gcc-arm-embedded                    gcs                                 icc
```

现在，所有的稳定版本就一目了然了~

那现在我想要安装 gcc-11 这个版本就直接执行下面的命令即可：

```bash
$ brew install gcc@11
```

安装完成后如果运行 `gcc -v` 你可能会发现 gcc 版本并没有发生变化，正确的打开方式是在后面追加你的安装版本。譬如 gcc@11 这个版本：

```bash
$ gcc-11 -v
$ g++-11 -v
```

如果每次使用 gcc/g++ 命令都要在后面加上版本号就会感觉很不舒服，所以建议你使用 `alias` 命令写到配置文件中：

```bash
alias gcc='gcc-11'
alias g++='g++-11'
alias cc='gcc-11'
alias c++='g++-11'
```

之后刷新下系统命令就可以了~

示例：

```bash
$ gcc -v

Using built-in specs.
COLLECT_GCC=gcc-11
COLLECT_LTO_WRAPPER=/usr/local/Cellar/gcc@11/11.3.0/bin/../libexec/gcc/x86_64-apple-darwin21/11/lto-wrapper
Target: x86_64-apple-darwin21
Configured with: ../configure --prefix=/usr/local/opt/gcc@11 --libdir=/usr/local/opt/gcc@11/lib/gcc/11 --disable-nls --enable-checking=release --with-gcc-major-version-only --enable-languages=c,c++,objc,obj-c++,fortran,d --program-suffix=-11 --with-gmp=/usr/local/opt/gmp --with-mpfr=/usr/local/opt/mpfr --with-mpc=/usr/local/opt/libmpc --with-isl=/usr/local/opt/isl --with-zstd=/usr/local/opt/zstd --with-pkgversion='Homebrew GCC 11.3.0' --with-bugurl=https://github.com/Homebrew/homebrew-core/issues --enable-libphobos --build=x86_64-apple-darwin21 --with-system-zlib --with-sysroot=/Library/Developer/CommandLineTools/SDKs/MacOSX12.sdk
Thread model: posix
Supported LTO compression algorithms: zlib zstd
gcc version 11.3.0 (Homebrew GCC 11.3.0)
```