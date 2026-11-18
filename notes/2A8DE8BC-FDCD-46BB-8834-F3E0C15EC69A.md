---
id: 2A8DE8BC-FDCD-46BB-8834-F3E0C15EC69A
title: "Chapter-3"
kind: book
notebook: 87A8FD83-C607-4C8B-9B83-33A218D08AA9
section: 36CFE85A-2DF2-49FD-85C4-7772EDB9B88E
created: 2026-11-16T08:58:08.198Z
pdf: "Attachments/B6160C69-A2DE-4852-929D-4818E979D8A5.pdf"
---

<!-- block B6753D92-BCB6-4D4E-BEC1-CA0A88F83717 area=main x=64 y=131 w=720 -->
# Machine-Level code

in machine code for x86-64, parts of the processor state are visible that normally are hidden from the C programmer:

- PC(or %rip in x86-64)
- integer register file
- condition registers
- a set of vector registers

OS translates virtual addresses into the physical addresses of values in the actual processor memory

<!-- block 50E996BD-81FB-4070-9B4A-14E8D14F5C01 area=main x=64 y=605 w=720 -->
# Code examples

```bash
工作目录　/Users/jade

lijiahui@lijiahui-249:~/ics15_213$ gdb
GNU gdb (Ubuntu 12.1-0ubuntu1~22.04.2) 12.1
Copyright (C) 2022 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<https://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.
--Type <RET> for more, q to quit, c to continue without paging--

For help, type "help".
Type "apropos word" to search for commands related to "word".
(gdb) file mstore.o
Reading symbols from mstore.o...
(No debugging symbols found in mstore.o)
(gdb) x/14xb multstore
0x0 <multstore>:        0xf3    0x0f    0x1e    0xfa    0x53      0x48    0x89    0xd3
0x8 <multstore+8>:      0xe8    0x00    0x00    0x00    0x00      0x48
(gdb) q
```

<!-- block 4B1FBE7B-C086-4CC4-BCAE-C36414B5E003 area=main x=64 y=1787 w=720 -->
# disassembler

```
工作目录　/Users/jade

lijiahui@lijiahui-249:~/ics15_213$ objdump -d mstore.o

mstore.o:     file format elf64-x86-64


Disassembly of section .text:

0000000000000000 <multstore>:
   0:   f3 0f 1e fa             endbr64 
   4:   53                      push   %rbx
   5:   48 89 d3                mov    %rdx,%rbx
   8:   e8 00 00 00 00          call   d <multstore+0xd>
   d:   48 89 03                mov    %rax,(%rbx)
  10:   5b                      pop    %rbx
  11:   c3                      ret    
```

<!-- block B27F2BF2-F21C-4E68-A967-26DA2E53CC64 area=main x=64 y=2472 w=720 -->
# x86-64 ISA features

- instrurctions can range in length from 1 to 15 bytes
- <mark>The instruction format is designed in such a way that from a given starting </mark> <mark>position, there is a unique decoding of the bytes into machine instructions.</mark><mark>For example, only the instruction pushq %rbx can start with byte value 53</mark>

<!-- block 783D97D7-F65A-4CB3-9208-8655D8883706 area=main x=64 y=2747 w=720 -->
# Linker

`linux> gcc -Og -o prog main.c mstore.c`

<!-- block 371F1A60-CB4F-45A9-A877-4B9DF466A4AF area=main x=64 y=2875 w=720 -->
# Notes on formatting

full content of the file mstore.s:

```
工作目录　/Users/jade

lijiahui@lijiahui-249:~/ics15_213$ cat mstore.s
        .file   "mstore.c"
        .text
        .globl  multstore
        .type   multstore, @function
multstore:
.LFB0:
        .cfi_startproc
        endbr64
        pushq   %rbx
        .cfi_def_cfa_offset 16
        .cfi_offset 3, -16
        movq    %rdx, %rbx
        call    mult2@PLT
        movq    %rax, (%rbx)
        popq    %rbx
        .cfi_def_cfa_offset 8
        ret
        .cfi_endproc
.LFE0:
        .size   multstore, .-multstore
        .ident  "GCC: (Ubuntu 11.4.0-1ubuntu1~22.04.3) 11.4.0"
        .section        .note.GNU-stack,"",@progbits
        .section        .note.gnu.property,"a"
        .align 8
        .long   1f - 0f
        .long   4f - 1f
        .long   5
0:
        .string "GNU"
1:
        .align 8
        .long   0xc0000002
        .long   3f - 2f
2:
        .long   0x3
3:
        .align 8
4:
```

<u><b>All of the lines beginning with ‘.’ are directives to guide the assembler and linker</b></u>

<!-- block B6F88FE4-3882-49BC-8FA1-B92DF711BD93 area=main x=64 y=3968 w=720 -->
# Simplified

<img src="Attachments/0AC09816-1DAD-480D-8429-32AA4885CC0D.png" alt="image.png" width="536">

<!-- block 52BD1DE3-F086-47FF-9108-DE6FE3618569 area=main x=64 y=4302 w=720 style=concept -->
word: 16-bit

double words: 32-bit

quad words: 64-bit

<!-- block A4F117D7-A150-4C65-8114-A2D2A10338D0 area=main x=64 y=4466 w=720 -->
**inline assembly:** feature of gcc, where brief sections of assembly code can be incorporated into a C program using the asm directive

<!-- block 79081CE7-82B7-42A1-9EF5-7A3DBDE8884B area=main x=64 y=4614 w=720 -->
# Size of C data types in x86-64

<img src="Attachments/CB004E00-785A-4817-A573-A163B92695FA.png" alt="image.png" width="348">

<!-- block 4F156011-EBF6-40BD-8E59-DC887B0CF8BA area=main x=64 y=4850 w=720 -->
# Accessing information

x86-64 CPU contains 16 GPRS to storing 64-bit values (integer data and pointers)

different registers serve different roles in the typical programs

<mark>%r</mark>

## operate data

- 8-bit
- 16-bit
- 32-bit
- 64-bit

when these instructions have registers as  
destinations:

- Those that generate 1- or 2-byte quantities leave the remaining bytes unchanged
- Those that generate 4-byte quantities set the upper 4 bytes of the register to zero

## GPRS fucntion

- %rsp: indicate the end position in the run-time stack
- 15 gprs are more flexible

<!-- block 04104EC8-3365-402B-AE88-BFCF3D22E056 area=main x=64 y=5602 w=720 -->
# Operand Specifiers

x86-64 operand forms: operands can denote immediate values, register values or values from memory

<img src="Attachments/FC467E51-9BEE-4CCF-A12C-7312226D7710.png" alt="image.png" width="348">

<!-- block 99D20B38-AE4B-4B65-B02E-06F0915CF87E area=main x=64 y=5982 w=720 -->
## examples

- Imm: $-577,$0x1F, different instructions allow different ranges of imm. values
- reg: 8-,4-,2-,1- bytes
- mem: Imm(rb,ri,s), s can be 1,2,4,8, rb,ri must be 64-bit regs

<!-- block A678E206-C1E4-45B2-B635-242491F691D8 area=main x=64 y=6260 w=720 -->
# Data movement instructions

## `MOV` class

`movb/movw/movl/movq: MOV S,D`

<u>x86-64 imposes the restriction that a move instruction cannot have both operands refer to memory locations</u>

`movabsq`

> **摘录**
> 
> The regular movq instruction can only have immediate source operands that can be represented as 32-bit two’s-complement numbers. This value is then sign extended to produce the 64-bit value for the destination. The movabsq in- struction can have an arbitrary 64-bit immediate value as its source operand and can only have a register as a destination.
> 
> — 第 219 页

## Two classes of data movement instructions

when smaller source value to a larger destination, the destination must be a register

- zero-extending
- sign-extending

<img src="Attachments/93B26BA4-FA11-4655-9231-86CA37422D7E.png" alt="image.png" width="318"><img src="Attachments/DA676C13-F2A9-4D68-9A4D-0BE5660787A2.png" alt="image.png" width="318">

**`cltq`is a operands-less instructions to implement 32-to-64 bit extension**

## Pushing and Popping Stack Data

with x86-64, the program stack is stored in some region of memory  

the stack grows downward such that the top element of the stack has the lowest address of all stack elements

example:

`pushq %rbp` equals to `subq $8, %rsp` with `movq %rbp, (%rsp)`

- less instruction length: 1-byte
- > **摘录**
  > 
  > Since the stack is contained in the same memory as the program code and other forms of program data, programs can access arbitrary positions within the stack using the standard memory addressing methods. For example, assuming the topmost element of the stack is a quad word, the instruction movq 8(%rsp),%rdx will copy the second quad word from the stack to register %rdx
  > 
  > — 第 227 页

<!-- block E53A3414-E103-481C-A763-4371294DAA2D area=main x=64 y=8204 w=720 -->
# Arithmetic and Logical Operations

The operations are divided into four groups: **load effective address, unary, binary, and shifts**

**<img src="Attachments/21CAD76B-6F9C-48F4-9914-1C3C7E119D1E.png" alt="image.png" width="299">**

<p><br></p>

## Load effective address

- the destination operand must be a register
- it does not reference memory at all
- just load the effective address

for example:

`leaq 7(%rdx,%rdx,4), %rax` compute the address and save it in reg. %rax

> **摘录**
> 
> Compilers often find clever uses of leaq that have nothing to do with effective address computations.
> 
> — 第 228 页

for example:

`long t = x + 4 * y + 12 * z;`

use `leaq`

`// x in %rdi, y in %rsi, z in % rdx`

`leaq (%rdi,%rsi,4), %rax`

`leaq (%rdz,%rdx,2), %rdx`

`leaq (%rax,%rdx,4), %rax`

## Unary and Binary Operations

- unary operations with single operand serving as both source and destination , the operand can be either a register or a memory location
- binary operations' second operand serving as both a source and a destination.
  
  > **摘录**
  > 
  > the source operand is given first and the destination second
  > 
  > — 第 230 页

for example:

`subq %rax, %rdx` decrements register `%rdx` by the value in `%rax`.

The first operand can be either an immediate value, a register, or a memory location. The second can be either a register or a memory location.

<mark>the two operands cannot both be memory locations</mark>

## Shift operations

the shift amount is given first and the value to shift is given second.

- shift amount: imm. or single byte reg.
- **reg: `%cl` for w-bit long data, only low-order m bits of register `%cl` are valid (2^m = w)**
- the destination operand of a shift operation can be either a register or a memory locaiton
- SAL,SHL: filling from the right with zero
- SAR,SHR: arithmatic shift and logical shift

## Special Arithmetic Operations

generate the full 128-bit product of two 64-bit numbers, as well as integer division

<img src="Attachments/62A209F2-8D0D-4F36-B905-8F70746652A0.png" alt="image.png" width="299">

- quad word x quad word -> oct word
- one operand must in `%rax`
- oct word is stored in `%rdx : %rax`

> **摘录**
> 
> little-endian machine, the high-order bytes are stored at higher addresses, as indicated by the address specification 8(%rdi).
> 
> — 第 235 页

<!-- block EDF4EACE-A30E-4E1B-8537-3B154392EC4B area=main x=64 y=11085 w=720 -->
# Control
