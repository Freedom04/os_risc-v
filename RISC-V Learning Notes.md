# RISC-V Learning Notes

## 1 riscv64-unknown-elf-gcc

### 1.1 预处理，编译，汇编，链接过程

riscv64-unknown-elf-gcc编译过程通常包括四个步骤：预处理、编译、汇编和链接。下面是这四个步骤的详细说明。

1. 预处理

预处理是一个将源代码转换为预处理代码的过程，主要是用来处理代码中的宏定义、条件编译和头文件引用等。在预处理过程中，可以使用以下命令：

```asm
riscv64-unknown-elf-gcc -E input.c -o output.i
```

其中，-E选项表示只进行预处理，input.c为输入文件，output.i为输出文件。

2. 编译

编译是将预处理代码转换为汇编代码的过程。在编译过程中，会进行语法检查、类型检查、优化等操作。在编译过程中，可以使用以下命令：

```asm
riscv64-unknown-elf-gcc -S input.i -o output.s
```

其中，-S选项表示只进行编译，input.i为预处理输出文件，output.s为汇编输出文件。

3. 汇编

汇编是将汇编代码转换为可重定位目标代码的过程。在汇编过程中，会将汇编代码翻译为机器指令，并生成符号表和重定位信息。在汇编过程中，可以使用以下命令：

```asm
riscv64-unknown-elf-gcc -c input.s -o output.o
```

其中，-c选项表示只进行汇编，input.s为汇编输出文件，output.o为目标文件。

4. 链接

链接是将目标代码和库代码链接成可执行文件或共享库的过程。在链接过程中，会进行符号解析、重定位等操作。在链接过程中，可以使用以下命令：

```asm
riscv64-unknown-elf-gcc input1.o input2.o -o output
```

其中，input1.o和input2.o为目标文件，output为可执行文件。

以上是riscv64-unknown-elf-gcc编译过程的详细说明。实际编译过程中，通常会使用Makefile等工具来自动化编译过程，提高开发效率。

### 1.2 各种参数

riscv64-unknown-elf-gcc是用于编译RISC-V架构的交叉编译器，它提供了许多参数来指定编译器的行为。下面是一些常用的参数：

1. -c：只编译不链接，生成目标文件。
2. -o：指定输出文件名。
3. -O0/-O1/-O2/-O3：控制代码优化的级别，分别为不优化、低优化、中优化和高优化。
4. -g：生成调试信息。
5. -Wall/-Wextra：开启额外的警告信息。
6. -Werror：将所有警告视为错误。
7. -std=：指定使用的C/C++标准版本。
8. -march=：指定生成的目标代码针对的处理器架构版本。
9. -mtune=：指定编译器的优化目标，以充分利用针对特定CPU架构的优化。
10. -static：生成静态链接的可执行文件。
11. -shared：生成共享库。
12. -fPIC：生成位置无关代码，用于生成共享库。
13. -I：指定头文件所在的目录。
14. -L：指定库文件所在的目录。
15. -l：指定需要链接的库文件名。

### 1.3 链接脚本

riscv64-unknown-elf-gcc链接脚本是用来告诉链接器如何将目标文件链接成可执行文件或共享库的配置文件。链接脚本的语法是基于脚本语言LD Script。

以下是一个简单的riscv64-unknown-elf-gcc链接脚本示例：

```
ENTRY(_start)

SECTIONS {
    . = 0x00000000; /* 代码段起始地址 */
    .text : {
        *(.text) /* 匹配所有.text节 */
    }

    . = 0x80000000; /* 数据段起始地址 */
    .data : {
        *(.data) /* 匹配所有.data节 */
    }

    .bss : {
        *(.bss) /* 匹配所有.bss节 */
    }
}
```

在链接脚本中，有两个重要的部分：ENTRY和SECTIONS。

ENTRY定义程序的入口点，通常为主函数的地址。在示例中，入口点为_start。

SECTIONS定义了链接器如何组织代码和数据。在示例中，定义了三个部分：

1. .text：代码段，用于存放代码。
2. .data：数据段，用于存放初始化的全局变量。
3. .bss：BSS段，用于存放未初始化的全局变量。

在SECTIONS中，通过“.”号将地址设置为特定的值。在示例中，代码段的起始地址为0x00000000，数据段的起始地址为0x80000000。

在SECTIONS中，使用“*”号匹配所有节，然后将它们放入对应的段中。

以上是一个简单的riscv64-unknown-elf-gcc链接脚本示例。实际的链接脚本可能会更加复杂，具体的脚本编写需要结合具体的应用程序进行配置。

**example:**

```
/* 设置输出架构为 RISC-V */
OUTPUT_ARCH("riscv")

/* 设置入口函数为 _start */
ENTRY(_start)

/* 定义内存区域 */
MEMORY
{
    /* 定义一个名为 ram 的内存区域，权限为可写、可执行、不可访问、无缓存，起始地址为 0x80000000，长度为 128M */
    ram (wxa!ri) : ORIGIN = 0x80000000, LENGTH = 128M
}

/* 定义程序头部（program headers） */
PHDRS
{
    /* 定义一个名为 text 的程序头部，类型为 PT_LOAD，用于加载代码段 */
    text PT_LOAD;
    /* 定义一个名为 data 的程序头部，类型为 PT_LOAD，用于加载数据段 */
    data PT_LOAD;
    /* 定义一个名为 bss 的程序头部，类型为 PT_LOAD，用于加载 bss 段 */
    bss  PT_LOAD;
}

/* 定义各个程序段（program sections） */
SECTIONS
{
    /* 定义代码段 */
    .text : {
        /* 定义一个名为 _text_start 的符号（symbol），表示代码段的起始地址 */
        PROVIDE(_text_start = .);
        /* 将 .text.init 段和 .text 段合并到一起，表示程序的初始化代码和主程序代码 */
        *(.text.init) *(.text .text.*)
        /* 定义一个名为 _text_end 的符号，表示代码段的结束地址 */
        PROVIDE(_text_end = .);
    } > ram AT> ram :text /* 将代码段映射到 ram 区域中 */

    /* 定义只读数据段 */
    .rodata : {
        /* 定义一个名为 _rodata_start 的符号，表示只读数据段的起始地址 */
        PROVIDE(_rodata_start = .);
        /* 将 .rodata 段和 .rodata.* 段合并到一起，表示程序的只读数据 */
        *(.rodata .rodata.*)
        /* 定义一个名为 _rodata_end 的符号，表示只读数据段的结束地址 */
        PROVIDE(_rodata_end = .);
    } > ram AT> ram :text /* 将只读数据段映射到 ram 区域中 */

    /* 定义数据段 */
    .data : {
        /* 将 .sdata 段和 .sdata.* 段合并到一起，表示程序的初始化数据 */
        . = ALIGN(4096);
        PROVIDE(_data_start = .);
        *(.sdata .sdata.*) *(.data .data.*)
        PROVIDE(_data_end = .);
    } > ram AT> ram :data /* 将数据段映射到 ram 区域中 */

  .bss :{
    PROVIDE(_bss_start = .);  // 定义 _bss_start 符号，表示 .bss 段的起始位置
    *(.sbss .sbss.*) *(.bss .bss.*)  // 将 .sbss 和 .bss 段以及所有子段合并到 .bss 段
    PROVIDE(_bss_end = .);  // 定义 _bss_end 符号，表示 .bss 段的结束位置
  } >ram AT>ram :bss  // 将 .bss 段放置在 ram 内存区域的开头，按照未初始化数据段(bss)格式排列

  PROVIDE(_memory_start = ORIGIN(ram));  // 定义 _memory_start 符号，表示 RAM 的起始地址
  PROVIDE(_memory_end = ORIGIN(ram) + LENGTH(ram));  // 定义 _memory
}
```



## 2 qemu-system-riscv64

### 2.1 各种参数

`qemu-system-riscv64`是在QEMU中使用的用于模拟RISC-V 64位处理器的命令。以下是`qemu-system-riscv64`的常用参数及其详细说明：

- `-machine <machine>`：指定使用的虚拟机型号。常用的机型包括`virt`（默认值，通用型）、`sifive_u`（SiFive Unmatched开发板）和`sifive_e`（SiFive HiFive1开发板）等。
- `-smp <num>`：指定虚拟机的CPU核心数量。默认值为1。
- `-m <size>`：指定虚拟机的内存大小，单位为MB。默认值为128MB。
- `-kernel <file>`：指定内核文件的路径，用于启动操作系统。如果不指定该参数，则需要通过其他方式启动操作系统。
- `-append <options>`：指定内核启动参数，多个参数之间使用空格分隔。
- `-drive <options>`：指定磁盘镜像文件的路径和相关参数。常用的参数包括`format`（指定磁盘镜像格式）、`if`（指定磁盘接口类型）和`index`（指定设备编号）等。
- `-device <options>`：指定设备类型和相关参数。常用的设备类型包括网卡（`virtio-net-pci`）、串口（`virtio-serial`）和USB控制器（`xhci`）等。
- `-nographic`：以无图形界面模式启动虚拟机，只使用终端进行操作。
- `-monitor <dev>`：指定监控设备类型。常用的监控设备包括串口（`virtio-serial`）和TCP/IP端口（`telnet`）等。
- `-serial <dev>`：指定串口设备类型和相关参数。常用的设备类型包括串口（`stdio`）和文件（`file:<file>`）等。
- `-netdev <options>`：指定网络设备类型和相关参数。常用的网络设备类型包括桥接（`bridge`）和用户模式（`user`）等。
- `-device <dev>`：指定设备类型和相关参数。常用的设备类型包括网卡（`virtio-net-pci`）和USB控制器（`xhci`）等。
- `-redir <rule>`：指定端口转发规则，将虚拟机中的端口映射到主机上。常用的规则包括TCP端口转发（`tcp:<host_port>::<guest_port>`）和UDP端口转发（`udp:<host_port>::<guest_port>`）等。
- `-no-reboot`：在虚拟机关机后不重新启动。
- `-enable-kvm`：启用KVM硬件加速。
- `-bios <file>`：指定BIOS文件的路径。

## 3 efl文件

RISC-V是一个开放的指令集架构，与其他架构不同，RISC-V的架构不受任何专利或其他限制。RISC-V提供了许多标准化的文件格式，其中之一就是EFL（Executable and Linkable Format），它类似于其他系统中的ELF文件格式。下面是RISC-V64 EFL文件的各个组成部分：

1. ELF头部（ELF Header）：包含文件类型、目标架构、程序头部表偏移量、程序头部表项数量等基本信息，指定了文件的类型和结构。
2. 程序头部表（Program Header Table）：描述了文件中各个段的信息。每个段描述了一个区域，例如.text段是存放代码的区域，.data段是存放数据的区域等等。
3. 代码段（Text Segment）：这个段包含了可执行的指令，是程序的主要部分。
4. 数据段（Data Segment）：这个段包含了全局变量、静态变量等数据。
5. 符号表（Symbol Table）：存放了代码中定义的所有符号的信息，包括符号名称、类型、值等。
6. 字符串表（String Table）：包含了符号表中使用的所有字符串，例如符号名称、段名称等。
7. 重定位表（Relocation Table）：由编译器在编译时生成，存储了所有需要在程序加载时进行重定位的地址信息。

总的来说，RISC-V64 EFL文件包含了程序的所有代码、数据、符号和重定位信息等。操作系统在加载EFL文件时，将会根据ELF头部中的信息读取各个段，并进行重定位，最终生成可执行的程序。

## 4 RISCV 指令

### 4.1 slli

RISC-V汇编语言中的slli指令是一个逻辑左移指令，用于将一个寄存器中的值逻辑左移一定的位数，并将结果存储回同一个寄存器中。

slli指令的语法如下：

```asm
slli rd, rs1, shamt
```

其中，rd是目的寄存器，rs1是源寄存器，shamt是移位数量，必须是一个在0到31之间的整数。指令将rs1的值逻辑左移shamt位，并将结果存储回rd寄存器中。

例如，以下指令将寄存器x1的值逻辑左移3位，并将结果存储回寄存器x2中：

```asm
slli x2, x1, 3
```

如果寄存器x1包含二进制数01010101，则slli指令的结果为10101000，因为01010101逻辑左移3位后变为10101000。

### 4.2 la

RISC-V汇编语言中的la指令是一个加载地址指令，用于将某个标签的地址加载到指定寄存器中。

la指令的语法如下：

```asm
la rd, label
```

其中，rd是目的寄存器，label是要加载地址的标签名。指令将label的地址加载到rd寄存器中。

例如，以下指令将标签my_data的地址加载到寄存器x1中：

```asm
la x1, my_data
```

在汇编代码中定义一个标签，可以使用以下语法：

```asm
my_data:
    .word 0x12345678
```

标签my_data定义了一个32位的数据单元，并初始化为0x12345678。在运行la指令之前，编译器会为my_data生成一个地址，并将该地址嵌入到la指令中。

因此，执行la x1, my_data指令后，寄存器x1将包含my_data的地址。在访问my_data时，可以使用la指令加载其地址，并使用lw或sw指令读取或写入其中的数据。

### 4.3 wfi

RISC-V汇编语言中的wfi指令（wait for interrupt）是一个等待中断指令，用于让CPU进入低功耗状态，等待中断事件的发生。当CPU执行wfi指令时，它会停止执行，进入等待状态，直到收到中断信号时才会被唤醒并继续执行后续指令。

wfi指令的语法如下：

```asm
wfi
```

wfi指令执行时，CPU会进入低功耗模式，并等待中断信号的到来。在等待中断时，CPU不会执行任何指令，也不会消耗系统资源，以节省功耗。一旦中断信号到来，CPU会被唤醒，继续执行后续指令。

在操作系统内核的实现中，wfi指令通常用于让CPU进入低功耗状态，以降低系统功耗和热量，延长电池寿命。此外，wfi指令还可以用于实现空闲等待状态，让CPU等待其他处理器或外设的操作完成，以降低CPU的占用率，提高系统资源利用率。

### 4.4 .skip

`.skip`指令是RISC-V汇编语言中用于分配内存空间的伪指令（pseudo-instruction），其语法如下：

```asm
.skip size
```

它的作用是在当前位置分配指定大小的空间，它并不真正地生成任何指令，而是将指令转换为等效的汇编代码。

例如，`.skip 16`会分配16个字节的空间，并将程序计数器（PC）增加16。在汇编代码中，`.skip`指令会被转换为多个`.zero`指令，这些指令将分配的空间全部初始化为0。

`.skip`指令通常用于在程序中预留空间，用于存储数据或其他代码。它可以与`.word`指令一起使用，来为数据分配空间，例如：

```asm
my_data:
    .word 1, 2, 3, 4, 5
    .skip 16
```

在上面的示例中，`.word`指令分配了一个包含5个32位整数的数据区域，`.skip`指令分配了16字节的空间，用于存储其他数据。

需要注意的是，由于`.skip`指令只是一个伪指令，不是RISC-V指令集的一部分，因此不是所有的汇编器都支持它。一些汇编器可能会使用不同的伪指令来实现相同的功能。

**example:**

```asm
stacks:
	.skip	STACK_SIZE * MAXNUM_CPU # allocate space for all the harts stacks

	.end				# End of file
```

这段代码使用了RISC-V汇编语言中的`.skip`指令来分配内存空间，并为每个CPU核心分配了一个固定大小的栈空间。

`.skip`指令的语法如下：

```asm
.skip	size
```

它的作用是为指定的字节数分配内存空间，并返回一个指向该内存空间起始地址的符号。在这个代码片段中，`.skip`指令的参数为`STACK_SIZE * MAXNUM_CPU`，也就是为每个CPU核心分配了`STACK_SIZE`大小的栈空间，总共分配了`MAXNUM_CPU`个栈空间的内存。这里的`STACK_SIZE`是指栈的大小，`MAXNUM_CPU`是指CPU核心的数量。

在这个代码片段中，`.skip`指令返回的符号是`stacks`，这个符号表示分配的内存空间的起始地址。

此外，该代码片段还使用了`.end`指令，表示文件的结束。`.end`指令没有任何参数，它的作用是告诉编译器已经到达了文件的结尾。

综上所述，这段代码的作用是为每个CPU核心分配固定大小的栈空间，以便进行函数调用和局部变量存储。`stacks`符号表示分配的内存空间的起始地址。

### 4.5 .equ

`.equ`指令是RISC-V汇编语言中用于定义符号常量的伪指令（pseudo-instruction），其语法如下：

```asm
.equ symbol, expression
```

它的作用是将一个符号常量与一个表达式进行关联，使得在汇编代码中可以使用该符号常量来代替表达式。当汇编器在处理代码时遇到该符号常量，就会将其替换为与之关联的表达式。

例如，`.equ STACK_SIZE, 1024`会定义一个名为`STACK_SIZE`的符号常量，并将其关联到值为1024的表达式。在汇编代码中，可以使用`STACK_SIZE`符号常量来代替`1024`，例如：

```asm
la sp, stacks + STACK_SIZE
```

在上面的示例中，`STACK_SIZE`符号常量被用于计算`stacks`标号加上一个栈大小的偏移量，以获取栈的顶部地址。

需要注意的是，由于`.equ`指令只是一个伪指令，不是RISC-V指令集的一部分，因此不是所有的汇编器都支持它。一些汇编器可能会使用不同的伪指令来实现相同的功能。

### 4.6 start_kernel()

`start_kernel()` 是 Linux 内核启动过程中的一个非常重要的函数。在 RISC-V 架构下，`start_kernel()` 函数的定义在 `arch/riscv/kernel/head.S` 文件中。当开机时，CPU 会从一个预定义的地址开始执行代码，这个地址指向了操作系统内核的入口函数，而在 RISC-V 架构下，这个入口函数就是 `start_kernel()`。

`start_kernel()` 函数主要完成以下工作：

1. 初始化 CPU 和硬件设备，包括设置 CPU 时钟、初始化中断控制器、开启内存映射等。
2. 进行内存管理的初始化，包括建立物理内存映射表、初始化虚拟内存管理等。
3. 初始化进程和线程管理，包括创建内核线程、初始化进程调度等。
4. 初始化文件系统和网络子系统，包括加载文件系统、初始化网络协议栈等。
5. 执行启动用户空间的一系列操作，包括初始化 `init` 进程、执行用户空间初始化脚本等。

一旦 `start_kernel()` 函数执行完毕，操作系统内核就正式启动了。在 `start_kernel()` 函数执行之后，会进入到一个死循环中，等待处理中断和调度进程。

## 5 Make

### 5.1 Makefile

Makefile 是一种自动化编译程序的工具，用于简化构建和编译代码的过程。它定义了一个名为 `Makefile` 的文件，其中包含了一系列规则和指令，用于告诉 `make` 命令如何构建和编译代码。

以下是一个包含使用QEMU模拟器的RISC-V操作系统的Makefile示例：

```makefile
# RISC-V 64-bit toolchain
CROSS_COMPILE=riscv64-unknown-elf-
CC=$(CROSS_COMPILE)gcc
LD=$(CROSS_COMPILE)ld
OBJCOPY=$(CROSS_COMPILE)objcopy

# Compilation flags
CFLAGS=-march=rv64ima -mabi=lp64 -mcmodel=medany -O2 -Wall -nostartfiles -ffreestanding
LDFLAGS=-static -nostdlib -nostartfiles -lgcc

# Output files
TARGET=my_os.elf
IMAGE=my_os.img

# Source files
SRCS=start.S main.c

# Object files
OBJS=$(SRCS:.S=.o)
OBJS+=$(filter %.o, $(shell $(CC) $(CFLAGS) -MMD -MF /dev/null -S $(SRCS) -o /dev/null))

all: $(TARGET) $(IMAGE)

$(TARGET): $(OBJS)
	$(LD) $(LDFLAGS) -Tlinker.ld -o $@ $^

$(IMAGE): $(TARGET)
	$(OBJCOPY) -O binary $^ $@

%.o: %.S
	$(CC) $(CFLAGS) -c $< -o $@

%.o: %.c
	$(CC) $(CFLAGS) -c $< -o $@

clean:
	rm -f $(OBJS) $(OBJS:.o=.d) $(TARGET) $(IMAGE)

qemu: $(IMAGE)
	qemu-system-riscv64 -nographic -machine virt -bios none -device loader,file=$(IMAGE),addr=0x80200000
```

此Makefile包括以下内容：

- `CROSS_COMPILE`和`CC`变量指定使用的交叉编译器。
- `CFLAGS`和`LDFLAGS`变量分别指定编译和链接选项。
- `TARGET`和`IMAGE`变量分别指定生成的ELF可执行文件和二进制镜像文件的名称。
- `SRCS`变量指定源文件列表。
- `OBJS`变量根据源文件列表生成目标文件列表。
- `all`目标编译所有源文件并生成目标文件和输出文件。
- `$(TARGET)`目标使用链接器将所有目标文件链接为单个ELF可执行文件。
- `$(IMAGE)`目标将ELF文件转换为二进制文件。
- `%.o`规则编译所有的.S和.c文件。
- `clean`目标删除所有生成的文件。
- `qemu`目标使用QEMU模拟器运行生成的二进制文件。

请注意，此Makefile使用了一个自定义的链接器脚本（`linker.ld`），该脚本将ELF文件的入口点设置为`start`符号。您需要根据自己的需求修改链接器脚本。

**example**

```makefile
CROSS_COMPILE = riscv64-unknown-elf-  # RISC-V 交叉编译器前缀
CFLAGS = -nostdlib -fno-builtin -march=rv64ima -mabi=lp64 -g -Wall  # 编译选项

QEMU = qemu-system-riscv64  # RISC-V 虚拟机
QFLAGS = -nographic -smp 1 -machine virt -bios none  # QEMU 启动选项

GDB = ${CROSS_COMPILE}gdb  # GDB 调试器
CC = ${CROSS_COMPILE}gcc  # C 编译器
OBJCOPY = ${CROSS_COMPILE}objcopy  # 二进制格式转换工具
OBJDUMP = ${CROSS_COMPILE}objdump  # 二进制反汇编工具

SRCS_ASM = \
	start.S \  # 汇编文件

SRCS_C = \
	kernel.c \  # C 文件

OBJS = $(SRCS_ASM:.S=.o)  # 将汇编文件名转换为目标文件名
OBJS += $(SRCS_C:.c=.o)  # 将 C 文件名转换为目标文件名

.DEFAULT_GOAL := all  # 默认目标为 all
all: os.elf  # 编译操作系统 ELF 文件

# os.elf 依赖于 ${OBJS}
# ${CC} ${CFLAGS} -Ttext=0x80000000 -o os.elf $^ 将目标文件链接为 ELF 文件
# ${OBJCOPY} -O binary os.elf os.bin 将 ELF 文件转换为二进制文件
os.elf: ${OBJS}
	${CC} ${CFLAGS} -Ttext=0x80000000 -o os.elf $^
	${OBJCOPY} -O binary os.elf os.bin

# 编译 C 文件
%.o : %.c
	${CC} ${CFLAGS} -c -o $@ $<

# 编译汇编文件
%.o : %.S
	${CC} ${CFLAGS} -c -o $@ $<

# 运行操作系统
run: all
	@${QEMU} -M ? | grep virt >/dev/null || exit
	@echo "Press Ctrl-A and then X to exit QEMU"
	@echo "------------------------------------"
	@${QEMU} ${QFLAGS} -kernel os.elf

# 调试操作系统
debug: all
	@echo "Press Ctrl-C and then input 'quit' to exit GDB and QEMU"
	@echo "-------------------------------------------------------"
	@${QEMU} ${QFLAGS} -kernel os.elf -s -S &
	@${GDB} os.elf -q -x gdbinit

# 反汇编操作系统
code: all
	@${OBJDUMP} -S os.elf | less

# 清除所有生成文件
clean:
	rm -rf *.o *.bin *.elf
```

### 5.2 sudo make install

`sudo make install`是在Linux和其他类Unix操作系统中从源代码安装软件时常用的命令。

需要注意的是，在使用`sudo make install`之前未查看安装说明或源代码可能是有风险的，因为它可能会覆盖重要的系统文件或与现有软件创建冲突。在安装来自源代码的软件时，始终建议先查看说明并小心操作。

**安装在指定目录**

可以在使用`sudo make install`时指定安装目录。默认情况下，`make install`会将软件安装在`/usr/local`目录中。但是，可以使用`PREFIX`变量指定不同的安装目录。

以下是指定自定义安装目录时使用`sudo make install`的示例：

```bash
sudo make install PREFIX=/opt/mysoftware
```

在此示例中，软件将安装在`/opt/mysoftware`目录中，而不是默认的`/usr/local`目录中。

请注意，并非所有软件都支持`PREFIX`变量。在这种情况下，您可能需要查看软件的安装说明或咨询文档，以确定如何指定自定义安装目录。





