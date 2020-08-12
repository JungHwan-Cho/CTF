# 01. ShellCode


```asm
pwn@chown-1~/exploit/shell$cat ./ASM32.asm
section .data
        msg db "Hello, world!", 0x0a, 0x0d
section .text
        global _start
_start:
        ; SYSCALL: write(1, msg, 14)
        mov eax, 4
        mov ebx, 1
        mov ecx, msg
        mov edx, 14
        int 0x80
        ; SYSCALL: exit(0)
        mov eax, 1
        mov ebx, 0
        int 0x80
```


```bash
pwn@chown-1~/exploit/shell$nasm -f elf ASM32.asm             
-bash: nasm: command not found                               
pwn@chown-1~/exploit/shell$sudo apt-get install nasm  

pwn@chown-1~/exploit/shell$nasm -f elf ASM32.asm             
pwn@chown-1~/exploit/shell$ld -m elf_i386 -o hello ASM32.o   
pwn@chown-1~/exploit/shell$./hello                           
Hello, world!        
```

## Change to Shellcode format
### Code 
* 앞에서 개발한 프로그램은 혼자서 동작 x, 링킹 과정도 필요하므로 Shellcode가 아니다.
* 다음을 통해서 앞의 코드를 독자적으로 동작하게 변경할 수 있다.
  1. 텍스트, 데이터 세그먼트를 사용하지 않는다.
  2. 함수의 호출은 call 명령어를 사용하여 "helloworld" 함수를 호출한다.
  3. write 시스템 함수에 의해 출력 될 문자열은 해당 명령어 뒤에 작성한다.
