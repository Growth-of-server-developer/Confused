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
`/usr/sbin`으로 이동하여 `httpd -v`로 버전을 확인한다. (Apache 2.2) 해당 버전 문서를 찾아봤을 때 경로는 이 서버에 존재하지 않았다.  
어찌하여 찾게된 경로... `/etc/httpd`

글의 제목과 별개로 서론이 길었는데 이 디렉토리에 `sites-available`과 `sites_enabled`이 존재한다.
