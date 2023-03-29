# RISC-V环境搭建

## 1. riscv-gnu-toolchain

This is the RISC-V C and C++ cross-compiler. It supports two build modes: a generic ELF/Newlib toolchain and a more sophisticated Linux-ELF/glibc toolchain.

### Getting the sources

This repository uses submodules, but submodules will fetch automatically on demand, so `--recursive` or `git submodule update --init --recursive` is not needed.

```shell
git clone https://github.com/riscv/riscv-gnu-toolchain
```

**Warning: git clone takes around 6.65 GB of disk and download size**

### Prerequisites

Several standard packages are needed to build the toolchain.

On Ubuntu, executing the following command should suffice:

```shell
sudo apt-get install autoconf automake autotools-dev curl python3 libmpc-dev libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo gperf libtool patchutils bc zlib1g-dev libexpat-dev ninja-build
```

### Installation (riscv64-unknown-elf-gcc)

To build the **Newlib cross-compiler**, pick an install path (that is writeable). If you choose, say, `/opt/riscv`, then add `/opt/riscv/bin` to your `PATH`. Then, simply run the following command:

```shell
# 要先进入riscv-gnu-toolchain，建立一个build目录
cd riscv-gnu-toolchain
mkdir build && cd build
../configure --prefix=/opt/riscv64
sudo make -jN	# N取决于电脑CPU核心数量，用于加快编译速度 使用nproc命令查看
```

编译完成后，在/opt/riscv64/bin目录下，有riscv64-unknown-elf-gcc的所有工具

add `/opt/riscv64` to your `PATH`

```shell
vim ~/.bashrc
# 在最后添加
export PATH=$PATH:"/opt/riscv64/bin"
# 退出保存
source ~/.bashrc
```

## 2. qemu

QEMU is a generic and open source machine & userspace emulator and virtualizer.

### Prerequisites

#### Required additional packages

For Ubuntu LTS (and maybe other Debian based distributions), all required additional packages can be installed like this:

```shell
sudo apt-get install git libglib2.0-dev libfdt-dev libpixman-1-dev zlib1g-dev ninja-build
```

#### Recommended additional packages

For Ubuntu (and maybe other Debian based distributions), most of the recommended additional packages for maximum code coverage can be installed like this:

```shell
sudo apt-get install git-email
sudo apt-get install libaio-dev libbluetooth-dev libcapstone-dev libbrlapi-dev libbz2-dev
sudo apt-get install libcap-ng-dev libcurl4-gnutls-dev libgtk-3-dev
sudo apt-get install libibverbs-dev libjpeg8-dev libncurses5-dev libnuma-dev
sudo apt-get install librbd-dev librdmacm-dev
sudo apt-get install libsasl2-dev libsdl2-dev libseccomp-dev libsnappy-dev libssh-dev
sudo apt-get install libvde-dev libvdeplug-dev libvte-2.91-dev libxen-dev liblzo2-dev
sudo apt-get install valgrind xfslibs-dev 
```

Newer versions of Debian / Ubuntu might also try these additional packages:

```shell
sudo apt-get install libnfs-dev libiscsi-dev
```

### Getting the source code

If you want the latest code, follow the development of the code, work with several versions or maybe even contribute to the code, you will need a local copy of the QEMU code repository which is managed using git.

Get the code like this:

```shell
git clone git://git.qemu-project.org/qemu.git
```

The required version for the contest is 7.0.0, I downloaded the 7.0.0 version source code from the official website.

### Simple build and test

QEMU supports builds in this directory (not recommended) or in an extra directory (out-of-tree builds, recommended). There can be any number of out-of-tree builds, so if you plan to make cross builds, debug and release builds, out-of-tree builds are what you need.

```shell
# Switch to the QEMU root directory.
cd qemu
# Prepare a native debug build.
mkdir -p bin/debug/native
cd bin/debug/native
# Configure QEMU and start the build.
../../../configure --enable-debug
make
# Return to the QEMU root directory.
cd ../../..
```

**Now let's start a simple test:**

```shell
bin/debug/native/x86_64-softmmu/qemu-system-x86_64 -L pc-bios
```

This test runs the QEMU system emulation which boots a PC BIOS.

### Other building

QEMU is multi-platform software intended to be buildable on all modern Linux platforms, OS-X, Win32 (via the Mingw64 toolchain) and a variety of other UNIX targets. The simple steps to build QEMU are:

```shell
mkdir build
cd build
../configure
make
```
**配置环境变量**

```shell
vim ~/.bashrc
# 在最后添加
export PATH=/home/yuanzhen/os/qemu-7.0.0/build:$PATH
# 保存，关闭
source ~/.bashrc
```

## 3 spike RISCV模拟器

### 3.1 Prerequisites

```bash
sudo apt install git build-essential device-tree-compiler
```

### 3.2 Getting the source code && Building

```bash
cd riscv-isa-sim
./configure --prefix=$RISCV
make -jN	# N取决于电脑CPU核心数量，用于加快编译速度 使用nproc命令查看
sudo make install
```





