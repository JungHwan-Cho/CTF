# [*] OverTheWire - narnia2 
[+] Category :

```c

#include <stdio.h>
#include <string.h>
#include <stdlib.h>
int main(int argc, char * argv[]){
    char buf[128];
    if(argc == 1){
        printf("Usage: %s argument\n", argv[0]);
        exit(1);
    }
    strcpy(buf,argv[1]);
    printf("%s", buf);
    return 0;
}
```

## [*] HOW 2 SOLVE
* 내 how2 문서를 읽어 보자.
```gdb
narnia2@narnia:/narnia$ gdb -q ./narnia2
Reading symbols from ./narnia2...(no debugging symbols found)...done.
(gdb) disas main
Dump of assembler code for function main:
   0x0804844b <+0>:     push   %ebp
   0x0804844c <+1>:     mov    %esp,%ebp
   0x0804844e <+3>:     add    $0xffffff80,%esp
   0x08048451 <+6>:     cmpl   $0x1,0x8(%ebp)           # if (argc == 1) {
   0x08048455 <+10>:    jne    0x8048471 <main+38>      #   // ...
   0x08048457 <+12>:    mov    0xc(%ebp),%eax
   0x0804845a <+15>:    mov    (%eax),%eax
   0x0804845c <+17>:    push   %eax
   0x0804845d <+18>:    push   $0x8048520
   0x08048462 <+23>:    call   0x8048300 <printf@plt>
   0x08048467 <+28>:    add    $0x8,%esp
   0x0804846a <+31>:    push   $0x1
   0x0804846c <+33>:    call   0x8048320 <exit@plt>     # } 
   0x08048471 <+38>:    mov    0xc(%ebp),%eax
   0x08048474 <+41>:    add    $0x4,%eax
   0x08048477 <+44>:    mov    (%eax),%eax              # 
   0x08048479 <+46>:    push   %eax
   0x0804847a <+47>:    lea    -0x80(%ebp),%eax         # buf 포인터의 포인터
   0x0804847d <+50>:    push   %eax
   0x0804847e <+51>:    call   0x8048310 <strcpy@plt>
   0x08048483 <+56>:    add    $0x8,%esp
   0x08048486 <+59>:    lea    -0x80(%ebp),%eax
   0x08048489 <+62>:    push   %eax
   0x0804848a <+63>:    push   $0x8048534
   0x0804848f <+68>:    call   0x8048300 <printf@plt>
   0x08048494 <+73>:    add    $0x8,%esp
   0x08048497 <+76>:    mov    $0x0,%eax
   0x0804849c <+81>:    leave  
   0x0804849d <+82>:    ret   
   
(gdb) b *(main+51)
Breakpoint 1 at 0x804847e
(gdb) b * main
Breakpoint 2 at 0x804844b

(gdb) r
Starting program: /narnia/narnia2 
(gdb) i r esp
esp            0xffffd6dc       0xffffd6dc
