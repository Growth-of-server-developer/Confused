# sites-available VS sites_enabled
사내에서 SSL 인증서 갱신하려고 SSL 인증서가 설치된 폴더를 보니 여러 개가 존재했다. 
맨 처음에 인증서 만료일을 확인하여 이를 식별하려고 시도했다.
```bash
openssl x509 -enddate -noout -in 인증서파일명(.pm)
```
결과는?... **모두 동일했다.** 이 방법으로 갱신해야 할 인증서 파일을 찾으려 한 것은 아니지만 시작부터 막힌 느낌이었다.  
이제 SSL 파일 경로가 작성되어 있을 apache 설정 파일을 찾기 시작했다. (왜 이 서버는 너무 다른 것이냐고...)  
이 서버는 apache를 직접 설치하지 않고 내장되어 있었다. 일단 apache 버전마다 기본 경로가 다를 수 있기에 버전을 확인하였다.
1. httpd 명령어가 유효하도록 위치 확인
```sh
ps -ef | grep httpd
```
경로를 보니 `/usr/sbin/httpd` 로 확인됐다.  
`/usr/sbin`으로 이동하여 `httpd -v`로 버전을 확인한다. (Apache 2.2)   

- 문서에 나와있는 설정파일 경로...`/usr/local/apache2/conf/httpd.conf`
![image](https://user-images.githubusercontent.com/46062199/90579197-3973f700-e200-11ea-8a9e-3f2c992db111.png)
- httpd -d 옵션에서 볼 수 있는 default 경로 `/usr/local/apache2`
![image](https://user-images.githubusercontent.com/46062199/90579206-47297c80-e200-11ea-98fc-63578236d590.png)
하지만 이 경로는 서버에 존재하지 않았다. 어찌하여 찾게된 경로... CentOS apache 기본 경로라고 한다. (OS는 Red Hat인데...CentOS가 Red hat 제휴로 개발했으니 비슷하겠구나)   
`/etc/httpd/conf/httpd.conf`를 vi 편집기로 들여다보니 짧고 간결하게 적혀있는 한 줄이 보였다.
```bash
Include sites-enabled/*.conf
```

글의 제목과 별개로 서론이 길었는데 이 한 줄 덕분?에 `sites-available`과 `sites_enabled`를 정리하고자 글을 작성하게 되었다.
이런 이유보다도 두 개의 디렉토리는 NGINX를 사용할 때 보고 직접 설정까지 했는데도 말로 설명을 못 해서 정리하게 되었다.

### sites-available
- 가상 서버 환경들에 대한 설정 파일들이 있는 디렉토리
### sites-enabled
- sites-available에 있는 가상 서버 파일들 중에서 실행시키고 싶은 파일들만 symbolic link로 연결한 디렉토리

여기서 중요한 것은 [이곳을](https://stackoverflow.com/questions/21812360/what-is-the-difference-between-sites-enabled-and-sites-available-directory) 참고하면 알 수 있듯이 `sites-available` 디렉토리 내에서만 파일들을 생성하여 설정해야 한다.
그렇지 않고 `sites-enabled` 내에서 파일을 편집하게 되면 편집기가 out of memory 되거나 어떤 이유든간에 (종료와 관련된 시그널인) SIGHUP 또는 SIGTERM를 받아서 문제가 생긴다고 한다.  

위의 설명을 토대로 직관적으로 이해해보자.  
**sites available** : *사용 가능한 사이트들*로 사이트를 실제로 사용하고 있는지는 모른다.  
이를 설정하는게 **sites enabled** : *활성화된 사이트들* 이고, symbolic link를 사용해 이를 가능하게 한 것이다.
  
이렇게 이해하고 있다면 더이상 헷갈리지 않을 것이다.
