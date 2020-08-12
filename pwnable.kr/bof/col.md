# COL
```
Daddy told me about cool MD5 hash collision today.
I wanna do something like that too!

ssh col@pwnable.kr -p2222 (pw:guest)
```

## MD5 Hash 
### MD5
The MD5 message-digest algorithm is a widely used hash function producing a 128-bit hash value. Although MD5 was initially designed to be used as a cryptographic hash function, it has been found to suffer from extensive vulnerabilities. **It can still be used as a checksum to verify data integrity, but only against unintentional corruption.** It remains suitable for other non-cryptographic purposes, for example for determining the partition for a particular key in a partitioned database.[3]
### MD5 Hash Collision
* https://en.wikipedia.org/wiki/MD5#Collision_vulnerabilities  
* 


## Write-Up
```c
#include <stdio.h>
#include <string.h>
unsigned long hashcode = 0x21DD09EC;
unsigned long check_password(const char* p){
        int* ip = (int*)p;    // ?? 
        int i;
        int res=0;
        for(i=0; i<5; i++){
                res += ip[i];
        }
        return res;
}
int main(int argc, char* argv[]){
        if(argc<2){
                printf("usage : %s [passcode]\n", argv[0]);
                return 0;
        }
        if(strlen(argv[1]) != 20){
                printf("passcode length should be 20 bytes\n");
                return 0;
        }
        if(hashcode == check_password( argv[1] )){  // argv[1] is char * 
                system("/bin/cat flag");
                return 0;
        }
        else
                printf("wrong passcode.\n");
        return 0;
}
```
* hashcode == check_password( argv[1] ) 를 만족시킬려면?..
  * argv[1] 는 20 bytes 문자열 .. '\x01\x02\x ... \x14'
  * ip 는 int * 이므로 '\x04\x03\x02\x01' '\x14\x13\x12\x11' ... 처럼 읽는다.
  * 0x21DD09EC == 568134124 이고 20바이트를 4칸으로 나누면 5개 나오니까 5로 나누면 142033531 나머지는 4이다.
```  
>>> hex(v/5)
'0x6c5cec8'
>>> hex(v/5 + 4)
'0x6c5cecc'
```
* 결론적으로 이렇게 입력을 하면 될 것이다. (리틀 엔디언 고려!)
* '\x6c5cec8' * 4 + '\x6c5cecc' -> '\xc8cec506' * 4 + '\xcccec506'

```bash
col@pwnable:~$ ./col `python -c "print('\xc8\xce\xc5\x06' * 4 + '\xcc\xce\xc5\x06')"`
daddy! I just managed to create a hash collision :)
```
