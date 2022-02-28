#    		               Lab Week 02	实验报告

实验内容：

- 熟悉 Linux 下 x86 汇编语言编程环境

- 验证实验 Blum’s Book: Sample programs in Chapter 04, 05 (Moving Data)

---

> Note: This report is written mainly in English while partly in Chinese. 

[TOC]

# Preview

## bare basic knowledge

1.sections

- data section

  Declaring data elements that are declared with an initial value.

- bss section

  Declaring data elements that are instantiated with a zero (or null) value. 

- text section

  Text section is required in all assembly language programs.

```assembly
.section .data
.section .bss
.section .text
```

2.Definitions

1. starting point ```_start``` , 
2.  for external applications ```.globl```



3. 存储方式

   1).大端存储：大端模式，是指数据的高字节保存在内存的低地址中，而数据的低字节保存在内存的高地址中，这样的存储模式有点儿类似于把数据当作字符串顺序处理：地址由小向大增加，而数据从高位往低位放。

   2).小端存储：小端模式，是指数据的高字节保存在内存的高地址中，而数据的低字节保存在内存的低地址中，这种存储模式将地址的高低和数据位权有效地结合起来，高地址部分权值高，低地址部分权值低，和我们的逻辑方法一致。



  4.Building the excutable 

​	Using ```as``` to assemble assemble .s file to .o file.  ```ld``` is used to link the file to be  excutable.

```assembly
as -o cpuid.o cpuid.s
ld -o cpuid cpuid.o
```

![image-20220222164122532](https://raw.githubusercontent.com/Maxwell-Wong/PicGo_Picbed/main/Lab/202202281151514.png)

  5.Debugging the program

- adding breakpoint 

![image-20220222171828351](/Users/maxwell/Library/Application Support/typora-user-images/image-20220222171828351.png)

The programs must be assembled using the ``` -gstabs``` parameter, so the debugger can match instruction codes with source code lines. 

```assembly
as -gstabs -o cpuid.o cpuid.s
ld -o cpuid cpuid.o
```

- Show registers infomation ```info registers```

<img src="https://raw.githubusercontent.com/Maxwell-Wong/PicGo_Picbed/main/Lab/Labweek2/202202281158092.png" alt="image-20220222171813292" style="zoom:50%;" />



- Display the memory locations at the output label

> Usage![image-20220222183816463](https://raw.githubusercontent.com/Maxwell-Wong/PicGo_Picbed/main/Lab/Labweek2/202202281158710.png)

![image-20220222183742231](/Users/maxwell/Library/Application Support/typora-user-images/image-20220222183742231.png)

- Print

  > Display the value of a specific register or variable from theprogram

  ```
  print/d to display the value in decimal
  print/t to display the value in binary
  print/x to display the value in hexadecimal
  ```

- data section

1. data section

   ```.section .rodata```

>.rodata. Any data elements defined in this section can only be accessed in read-only mode (thus the ro prefix)

2. Directive

> The directive instructs the assembler to reserve a specified amount of memory for the data element to be referenced by the label.

![image-20220222184802485](https://raw.githubusercontent.com/Maxwell-Wong/PicGo_Picbed/main/Lab/Labweek2/202202281159459.png)

3. Defining static symbols

```.equ```

```
.equ factor, 3
.equ LINUX_SYS_CALL, 0x80
```

- The bss section

> declare raw segments of memory that are reserved for whatever purpose you need them for

![image-20220222192636480](https://raw.githubusercontent.com/Maxwell-Wong/PicGo_Picbed/main/Lab/Labweek2/202202281159133.png)

Format   ```.comm symbol, length```  (length is the number of bytes contained inthe memory area)

Local common memory areas cannot be accessed by functions outside of where they were declared (they can’t be used in .globl directives) 【声明变量，但不定义值】

```assembly
.section .bss
.lcomm buffer, 10000
```



# Experiment

## I. Chapter 4

1. ##### Checking the Vendor ID string that is produced by the CPUID instruction

Assembly Code: cpuid.s

```assembly
# cpuid.s Sample program to extract the processor Vendor ID
.section .data
output:
   .ascii "The processor Vendor ID is 'xxxxxxxxxxxx'\n" 
   # This code snippet sets aside 42 bytes of memory(1char=1byte) , places the defined string sequentially in the memory bytes, and assigns the label output to the first byte. 
.section .text
.globl _start
_start:
   movl $0, %eax
   cpuid
   movl $output, %edi 
   movl %ebx, 28(%edi) #load the value in the EBX register to the EDI register 4 bytes after 
   movl %edx, 32(%edi)
   movl %ecx, 36(%edi)
   movl $4, %eax  
   movl $1, %ebx
   movl $output, %ecx # moves the memory address of the output label to the EDI register
   movl $42, %edx
   int $0x80
   movl $1, %eax # %eax中的值1表示使用exit函数，而%ebx则是退出值的指定。
   movl $0, %ebx
   int $0x80  # 中断调用使用的是Linux的软中断功能。通过int $0x80来执行。
	
	#%eax指定系统调用号 %ebx指定要写入的文件描述符 %ecx指向要输出的字符串的开头 %edx指定要输出的字符串的个数
```

Result:

![image-20220223144749911](https://raw.githubusercontent.com/Maxwell-Wong/PicGo_Picbed/main/Lab/Labweek2/202202281159925.png)



2. ##### using  the common C functions to check the Vendor ID string 

Assembly Code: cpuid2.s

```assembly
#cpuid2.s View the CPUID Vendor ID string using C library calls
.code32
.section .data
output:
    .asciz "The processor Vendor ID is '%s'\n"
.section .bss
    .lcomm buffer, 12
.section .text
.globl _start
_start:
    movl $0, %eax
    cpuid
    movl $buffer, %edi
    movl %ebx, (%edi)
    movl %edx, 4(%edi)
    movl %ecx, 8(%edi)
    pushl $buffer
    pushl $output
    call printf
    addl $8, %esp
    pushl $0
    call exit
```

**Result:**

![image-20220223144657209](https://raw.githubusercontent.com/Maxwell-Wong/PicGo_Picbed/main/Lab/Labweek2/202202281159913.png)



**Some Problems:** 

> 1. The assembly program is 32-bit program, but my machine is 64-bit.

**Error** When entering: 

```
$ ld -o cpuid2 -lc cpuid2.o
$ ./cpuid2
bash: ./cpuid2: No such file or directory
```

> The problem is that the linker was able to resolve the C functions, but the functions themselves were not included in the final executable program 

Using

```
$ ld -dynamic-linker /lib/ld-linux.so.2 -o cpuid2 -lc cpuid2.o
$ ./cpuid2
```

Encountering: 

```
Accessing a corrupted shared library
```

![image-20220222214900383](https://raw.githubusercontent.com/Maxwell-Wong/PicGo_Picbed/main/Lab/Labweek2/202202281200762.png)



**Solution:**

Entering the following commands will compile the .s file to a 32-bit .o file, and connect to the dynamic-linker which is displayed in the command, then the excutable file will be excuted successfully.

```assembly
as --32 -o cmovtest.o cmovtest.s
ld -m elf_i386 -dynamic-linker /lib32/ld-linux.so.2 /lib32/libc.so.6 -o cmovtest cmovtest.o
```



## II. Chapter 5

##### 1.Moving data values from memory to a register

Assembly Code: movetest1.s

```assembly
.section .data
   value:
      .int 1
.section .text
.globl _start
   _start:
      nop
      movl value, %ecx
      movl $1, %eax
      movl $0, %ebx
      int $0x80
```

**Result:**

![image-20220222200552103](https://raw.githubusercontent.com/Maxwell-Wong/PicGo_Picbed/main/Lab/Labweek2/202202281200763.png)

![image-20220222200608936](https://raw.githubusercontent.com/Maxwell-Wong/PicGo_Picbed/main/Lab/Labweek2/202202281200506.png)



##### 2.Moving data values from a register to memory

Assembly Code: movetest2.s

```assembly
.section .data
   value:
      .int 1
.section .text
.globl _start
   _start:
      nop
      movl $100, %eax
      movl %eax, value
      movl $1, %eax
      movl $0, %ebx
      int $0x80
      
```

**Result:**

![image-20220222205714719](https://raw.githubusercontent.com/Maxwell-Wong/PicGo_Picbed/main/Lab/Labweek2/202202281200412.png)





##### 3.Using indexed memory locations

Assembly Code: movetest3.s

```assembly
# movtest3.s ñ Another example of using indexed memory locations
.section .data
output:
   .asciz "The value is %d\n"
values:
   .int 10, 15, 20, 25, 30, 35, 40, 45, 50, 55, 60
.section .text
.globl _start
_start:
   nop
   movl $0, %edi
loop:
   movl values(, %edi, 4), %eax
   pushl %eax
   pushl $output
   call printf
   addl $8, %esp
   inc %edi
   cmpl $11, %edi
   jne loop
   movl $0, %ebx
   movl $1, %eax
   int $0x80
```

**Result:**

![image-20220223145703893](https://raw.githubusercontent.com/Maxwell-Wong/PicGo_Picbed/main/Lab/Labweek2/202202281200005.png)



##### 4.Using indirect addressing with registers

Assembly Code: movetest4.s

```assembly
.section .data
values:
.int 10, 15, 20, 25, 30, 35, 40, 45, 50, 55, 60
.section .text
.globl _start
_start:
nop
movl values, %eax
movl $values, %edi
movl $100, 4(%edi)
movl $1, %edi
movl values(, %edi, 4), %ebx
movl $1, %eax
int $0x80
```

> indexed memory mode:
>
> base_address(offset_address, index, size)
>
> 如：movl $1, %edi， movl values(, %edi, 4), %ebx 将values中的1号位的值（4bytes）放入寄存器ebx中

**Result:**

![image-20220223101530286](https://raw.githubusercontent.com/Maxwell-Wong/PicGo_Picbed/main/Lab/Labweek2/202202281200240.png)

Finally, ```movl $100, 4(%edi)``` command changes value 15 to 100 in values (100 value has replaced 15)

![image-20220223115758597](/Users/maxwell/Library/Application Support/typora-user-images/image-20220223115758597.png)

Command```echo $?```check the exit code in the shell. This is done using the special environment variable ``` $?:```

---



##### 5.Conditional Move Instructions

-  Using CMOV instructions

``` cmp %ebx, %ecx ```subtracts the first operand from the second and sets the EFLAGS registers appropriately

CMP（比较）指令执行从目的操作数中减去源操作数的隐含减法操作，并且不修改任何操作数：

```cmova %ecx, %ebx``` is then used to replace the value in EBX with the value in ECX if the value is larger than what was originally in the EBX register.

Assembly Code: cmovtest.s

```assembly
# cmovtest.s - An example of the CMOV instructions
.section .data
# .code32
output:
   .asciz "The largest value is %d\n"
values:
   .int 105, 235, 61, 315, 134, 221, 53, 145, 117, 5
.section .text
.globl _start
_start:
   nop
   movl values, %ebx
   movl $1, %edi
loop:
   movl values(, %edi, 4), %eax  # base_address(offset_address, index, size)
   cmp %ebx, %eax # 从第二个操作数中减去第一个操作数 105<235  CF = 0
   cmova %eax, %ebx  # CF = 0 当(eax) > (ebx) 将ebx中的值替换为eax中的值
   inc %edi
   cmp $10, %edi
   jne loop
   pushl %ebx
   pushl $output
   call printf
   addl $8, %esp
   pushl $0
   call exit
```

**Result:**

<img src="https://raw.githubusercontent.com/Maxwell-Wong/PicGo_Picbed/main/Lab/Labweek2/202202281200795.png" alt="image-20220223134342879" style="zoom:50%;" />

As expected, the larger value (235) was moved into the EBX register.

![image-20220223134525079](https://raw.githubusercontent.com/Maxwell-Wong/PicGo_Picbed/main/Lab/Labweek2/202202281200082.png)

At the end of loop, the largest value is put in ```$ebx```



-  using CMPXCHG instructions

Assembly Code: cmpxchgtest.s

```assembly
# cmpxchgtest.s - An example of the cmpxchg instruction
.section .data
data:
   .int 10
.section .text
.globl _start
_start:
   nop
   movl $10, %eax
   movl $5, %ebx
   cmpxchg %ebx, data
   movl $1, %eax
   int $0x80
```

**Result:**

<img src="https://raw.githubusercontent.com/Maxwell-Wong/PicGo_Picbed/main/Lab/Labweek2/202202281201915.png" alt="image-20220223135757352" style="zoom:50%;" />





-  using XCHG instructions

Assembly Code: bubble.s

```assembly
# bubble.s - An example of the XCHG instruction
.section .data
values:
   .int 105, 235, 61, 315, 134, 221, 53, 145, 117, 5
.section .text
.globl _start
_start:
   movl $values, %esi
   movl $9, %ecx
   movl $9, %ebx
loop:
   movl (%esi), %eax
   cmp %eax, 4(%esi)
   jge skip
   xchg %eax, 4(%esi) # 寄存器间接寻址
   movl %eax, (%esi)
skip:
   add $4, %esi
   dec %ebx
   jnz loop
   dec %ecx
   jz end
movl $values, %esi
   movl %ecx, %ebx
   jmp loop
end:
   movl $1, %eax
   movl $0, %ebx
   int $0x80
```

**Result:**

<img src="https://raw.githubusercontent.com/Maxwell-Wong/PicGo_Picbed/main/Lab/Labweek2/202202281201316.png" alt="image-20220223140138294" style="zoom:50%;" />



---

6. ##### The bss section

watching the size of the executable program as data elements are declared

- Assembly Code: sizetest1.s

```assembly
# sizetest1.s  A sample program to view the executable size
.section .text
.globl _start
_start:
   movl $1, %eax
   movl $0, %ebx
   int $0x80
```

![image-20220223140507644](https://raw.githubusercontent.com/Maxwell-Wong/PicGo_Picbed/main/Lab/Labweek2/202202281201344.png)

The total size of the executable program file is 664 bytes.



- Assembly Code: sizetest2.s

```assembly
# sizetest2.s - A sample program to view the executable size
.section .bss
   .lcomm buffer, 10000
.section .text
.globl _start
_start:
   movl $1, %eax
   movl $0, %ebx
   int $0x80
```

![image-20220223140617740](https://raw.githubusercontent.com/Maxwell-Wong/PicGo_Picbed/main/Lab/Labweek2/202202281201256.png)



- Assembly Code: sizetest3.s

```assembly
# sizetest3.s - A sample program to view the executable size
.section .data
buffer:
   .fill 10000
.section .text
.globl _start
_start:
   movl $1, %eax
   movl $0, %ebx
   int $0x80
```

![image-20220223140822532](https://raw.githubusercontent.com/Maxwell-Wong/PicGo_Picbed/main/Lab/Labweek2/202202281201311.png)



---

7. ##### Using BSWAP instruction

Assembly Code: swaptest.s

```assembly
# swaptest.s An example of using the BSWAP instruction
.section .text
.globl _start
_start:
   nop
   movl $0x12345678, %ebx
   bswap %ebx
   movl $1, %eax
   int $0x80
```

This instruction will transfer bits 0 through 7 are swapped with bits 24 through 31, while bits 8 through 15 are swapped with bits 16 through 23 etc.



**Result:**

<img src="https://raw.githubusercontent.com/Maxwell-Wong/PicGo_Picbed/main/Lab/Labweek2/202202281201920.png" alt="image-20220223152448834" style="zoom:50%;" />
