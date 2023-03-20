# OS

## 一、环境搭建

### 1. riscv-gnu-toolchain

This is the RISC-V C and C++ cross-compiler. It supports two build modes: a generic ELF/Newlib toolchain and a more sophisticated Linux-ELF/glibc toolchain.

#### Getting the sources

This repository uses submodules, but submodules will fetch automatically on demand, so `--recursive` or `git submodule update --init --recursive` is not needed.

```shell
git clone https://github.com/riscv/riscv-gnu-toolchain
```

**Warning: git clone takes around 6.65 GB of disk and download size**

#### Prerequisites

Several standard packages are needed to build the toolchain.

On Ubuntu, executing the following command should suffice:

```shell
sudo apt-get install autoconf automake autotools-dev curl python3 libmpc-dev libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo gperf libtool patchutils bc zlib1g-dev libexpat-dev ninja-build
```

#### Installation (riscv64-unknown-elf-gcc)

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

### 2. qemu

