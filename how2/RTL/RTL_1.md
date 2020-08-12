# RTL(Return to Libc) - x86
* RLT 이란 Return address 영역에 **공유 라이브러리** 함수의 주소로 변경해,  
  해당 함수를 호출하는 방식입니다.
  * 해당 기법을 이용해 NX bit(DEP)를 우회 할 수 있습니다.

## Calling Convention
