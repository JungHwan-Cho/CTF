# PASSCODE
```
Mommy told me to make a passcode based login system.
My initial C code was compiled without any error!
Well, there was some compiler warning, but who cares about that?

ssh passcode@pwnable.kr -p2222 (pw:guest)
```

* 에러 없이 컴파일되었다고 한다. 대신 Warning 이 떴다고 하는데 과연 뭐일까?

```c
passcode@pwnable:~$ cat ./passcode.c
#include <stdio.h>
#include <stdlib.h>
void login(){
        int passcode1;
        int passcode2;
        printf("enter passcode1 : ");
        scanf("%d", passcode1);
        fflush(stdin);
        // ha! mommy told me that 32bit is vulnerable to bruteforcing :)
        printf("enter passcode2 : ");
        scanf("%d", passcode2);
        printf("checking...\n");
        if(passcode1==338150 && passcode2==13371337){
                printf("Login OK!\n");
                system("/bin/cat flag");
        }
        else{
                printf("Login Failed!\n");
                exit(0);
        }
}
void welcome(){
        char name[100];
        printf("enter you name : ");
        scanf("%100s", name);
        printf("Welcome %s!\n", name);
}
int main(){
        printf("Toddler's Secure Login System 1.0 beta.\n");
        welcome();
        login();
        // something after login...
        printf("Now I can safely trust you that you have credential :)\n");
        return 0;
}
```
* scanf("%d", passcode1); 
  * 이런 &passcode1 을 넣어야 정상적으로 동작할텐데 실수를 한 모양이다.
  * scanf의 두 번째 인자는 주소를 넣어야 하고, passcode1의 값을 주소로 인식하여 접근한 뒤 그 곳에 값을 쓸 것이다.(???)
