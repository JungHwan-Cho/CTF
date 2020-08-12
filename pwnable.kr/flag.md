# flag
```
Papa brought me a packed present! let's open it.

Download : http://pwnable.kr/bin/flag

This is reversing task. all you need is binary
```
* packed present? => pack 된 파일 문제일 것 같다. (압축 ???)
* 압축 : https://upx.github.io/

```bash
pwn@chown-1~/fun/CTF/pwnable.kr/flag$objdump -d ./flag
./flag:     file format elf64-x86-64
pwn@chown-1~/fun/CTF/pwnable.kr/flag$strings ./Flag | grep upx
pwn@chown-1~/fun/CTF/pwnable.kr/flag$strings ./Flag | grep UPX
UPX...? sounds like a delivery service :)
pwn@chown-1~/fun/CTF/pwnable.kr/flag$upx -d ./Flag
                       Ultimate Packer for eXecutables
                          Copyright (C) 1996 - 2017
UPX 3.94        Markus Oberhumer, Laszlo Molnar & John Reiser   May 12th 2017
        File size         Ratio      Format      Name
   --------------------   ------   -----------   -----------
    883745 <-    335288   37.94%   linux/amd64   Flag
Unpacked 1 file.
```

```gdb
pwndbg> disassemble main
Dump of assembler code for function main:
   0x0000000000401164 <+0>:     push   rbp
   0x0000000000401165 <+1>:     mov    rbp,rsp
   0x0000000000401168 <+4>:     sub    rsp,0x10
   0x000000000040116c <+8>:     mov    edi,0x496658           
   0x0000000000401171 <+13>:    call   0x402080 <puts>
   0x0000000000401176 <+18>:    mov    edi,0x64
   0x000000000040117b <+23>:    call   0x4099d0 <malloc>
   0x0000000000401180 <+28>:    mov    QWORD PTR [rbp-0x8],rax
   0x0000000000401184 <+32>:    mov    rdx,QWORD PTR [rip+0x2c0ee5]        # 0x6c2070 <flag>
   0x000000000040118b <+39>:    mov    rax,QWORD PTR [rbp-0x8]
   0x000000000040118f <+43>:    mov    rsi,rdx
   0x0000000000401192 <+46>:    mov    rdi,rax
   0x0000000000401195 <+49>:    call   0x400320
   0x000000000040119a <+54>:    mov    eax,0x0
   0x000000000040119f <+59>:    leave  
   0x00000000004011a0 <+60>:    ret    
End of assembler dump.

# <+32> 부분에 break point를 걸고 r 시켜보면
*RDX  0x496628 ◂— push   rbp /* 'UPX...? sounds like a delivery service :)' */
```
*UPX...? sounds like a delivery service :)
